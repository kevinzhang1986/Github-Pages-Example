---
layout:     post
title:      Python开源项目AutoJump代码分析
category: 技术总结
description: 代码分析
tags: python
---

# Python开源项目AutoJump代码分析

## 一、简介
AutoJump是一个基于Python的开源项目，主要用于简化目录的定位。 原本需要“cd 一长串的路径”到达指定的路径。这个项目会把历史记录放到自定义的数据库中，然后通过索引"j 文件夹"就可以到达指定的路径。这个项目的源码，涵盖了大部分的Python语法知识，值得学习（但是软件架构有点冗余和复杂，不是很好理解）。

## 二、数据结构
**ArgumentParser类**

![](images/2017-12-12-AutoJump_analysis/ArgumentParser.jpg)
![](images/2017-12-12-AutoJump_analysis/ExtendsArgumentParser.jpg)

该类继承于_AttributeHolder和_ActionsContainer类<br>


**_AttributeHolder类**

![](images/2017-12-12-AutoJump_analysis/AttributeHolder.jpg)
![](images/2017-12-12-AutoJump_analysis/ExtendedAttributeHolder.jpg)

**_ActionsContainer类**

![](images/2017-12-12-AutoJump_analysis/ActionsContainer.jpg)
![](images/2017-12-12-AutoJump_analysis/ExtendedActionsContainer.jpg)

## 三、流程

自定义安装流程（install.py）:<br>
![1](images/2017-12-12-AutoJump_analysis/1.jpeg)

使用：<br>
![2](images/2017-12-12-AutoJump_analysis/2.jpeg)

**源码解析**
```
#!/usr/bin/env python
# -*- coding: utf-8 -*-
from __future__ import print_function

import os
import platform
import shutil
import sys

sys.path.append('bin')
from autojump_argparse import ArgumentParser

SUPPORTED_SHELLS = ('bash', 'zsh', 'fish', 'tcsh')


def cp(src, dest, dryrun=False):
    # (2.3) 使用shutil拷贝文件
    print("copying file: %s -> %s" % (src, dest))
    if not dryrun:
        shutil.copy(src, dest)


def get_shell():
    return os.path.basename(os.getenv('SHELL', ''))


def mkdir(path, dryrun=False):
    # (2.2) 用os.path.exsits()判断是否存在文件，如果不存在，用os.makekdirs创建
    print("creating directory:", path)
    if not dryrun and not os.path.exists(path):
        os.makedirs(path)


def modify_autojump_sh(etc_dir, share_dir, dryrun=False):
    """Append custom installation path to autojump.sh"""
    custom_install = "\
        \n# check custom install \
        \nif [ -s %s/autojump.${shell} ]; then \
        \n    source %s/autojump.${shell} \
        \nfi\n" % (share_dir, share_dir)

    with open(os.path.join(etc_dir, 'autojump.sh'), 'a') as f:
        f.write(custom_install)


def modify_autojump_lua(clink_dir, bin_dir, dryrun=False):
    """Prepend custom AUTOJUMP_BIN_DIR definition to autojump.lua"""
    custom_install = "local AUTOJUMP_BIN_DIR = \"%s\"\n" % bin_dir.replace(
        "\\",
        "\\\\")
    clink_file = os.path.join(clink_dir, 'autojump.lua')
    with open(clink_file, 'r') as f:
        original = f.read()
    with open(clink_file, 'w') as f:
        f.write(custom_install + original)


def parse_arguments():  # noqa
    # (1.1) 通过os.getenv获取环境变量，设置默认用户目的路径，如果是Linux，则用os.path.expanduser
    if platform.system() == 'Windows':
        default_user_destdir = os.path.join(
            os.getenv('LOCALAPPDATA', ''),
            'autojump')
    else:
        default_user_destdir = os.path.join(
            os.path.expanduser("~"),
            '.autojump')
    default_user_prefix = ''
    default_user_zshshare = 'functions'
    default_system_destdir = '/'
    default_system_prefix = '/usr/local'
    default_system_zshshare = '/usr/share/zsh/site-functions'
    default_clink_dir = os.path.join(os.getenv('LOCALAPPDATA', ''), 'clink')
    
    # (1.2) 创建ArgumentParser实例
    parser = ArgumentParser(
        description='Installs autojump globally for root users, otherwise \
            installs in current user\'s home directory.')
    # (1.3.1)  添加-n参数       
    parser.add_argument(
        '-n', '--dryrun', action="store_true", default=False,
        help='simulate installation')
    # (1.3.2)  添加-f参数    
    parser.add_argument(
        '-f', '--force', action="store_true", default=False,
        help='skip root user, shell type, Python version checks')
    # (1.3.3)  添加-d参数
    parser.add_argument(
        '-d', '--destdir', metavar='DIR', default=default_user_destdir,
        help='set destination to DIR')
    parser.add_argument(
        '-p', '--prefix', metavar='DIR', default=default_user_prefix,
        help='set prefix to DIR')
    parser.add_argument(
        '-z', '--zshshare', metavar='DIR', default=default_user_zshshare,
        help='set zsh share destination to DIR')
    parser.add_argument(
        '-c', '--clinkdir', metavar='DIR', default=default_clink_dir,
        help='set clink directory location to DIR (Windows only)')
    parser.add_argument(
        '-s', '--system', action="store_true", default=False,
        help='install system wide for all users')

    # (1.4)  解析参数
    args = parser.parse_args()

    if not args.force:
        if sys.version_info[0] == 2 and sys.version_info[1] < 6:
            print("Python v2.6+ or v3.0+ required.", file=sys.stderr)
            sys.exit(1)
        if args.system:
            if platform.system() == 'Windows':
                print("System-wide installation is not supported on Windows.",
                      file=sys.stderr)
                sys.exit(1)
            elif os.geteuid() != 0:
                print("Please rerun as root for system-wide installation.",
                      file=sys.stderr)
                sys.exit(1)

        if platform.system() != 'Windows' \
                and get_shell() not in SUPPORTED_SHELLS:
            print("Unsupported shell: %s" % os.getenv('SHELL'),
                  file=sys.stderr)
            sys.exit(1)

    # (1.5)  如果目的目录和默认目录不相等，则设置custom_install为True
    if args.destdir != default_user_destdir \
            or args.prefix != default_user_prefix \
            or args.zshshare != default_user_zshshare:
        args.custom_install = True
    else:
        args.custom_install = False

    if args.system:
        if args.custom_install:
            print("Custom paths incompatible with --system option.",
                  file=sys.stderr)
            sys.exit(1)

        args.destdir = default_system_destdir
        args.prefix = default_system_prefix
        args.zshshare = default_system_zshshare

    # (1.6) 返回args
    return args


def show_post_installation_message(etc_dir, share_dir, bin_dir):
    if platform.system() == 'Windows':
        print("\nPlease manually add %s to your user path" % bin_dir)
    else:
        if get_shell() == 'fish':
            aj_shell = '%s/autojump.fish' % share_dir
            source_msg = "if test -f %s; . %s; end" % (aj_shell, aj_shell)
            rcfile = '~/.config/fish/config.fish'
        else:
            aj_shell = '%s/autojump.sh' % etc_dir
            source_msg = "[[ -s %s ]] && source %s" % (aj_shell, aj_shell)

            if platform.system() == 'Darwin' and get_shell() == 'bash':
                rcfile = '~/.profile'
            else:
                rcfile = '~/.%src' % get_shell()

        print("\nPlease manually add the following line(s) to %s:" % rcfile)
        print('\n\t' + source_msg)
        if get_shell() == 'zsh':
            print("\n\tautoload -U compinit && compinit -u")

    print("\nPlease restart terminal(s) before running autojump.\n")

def main(args):
    # (2) parser参数的过程见相关源文件的解析。解析出来的dryrun为false
    if args.dryrun:
        print("Installing autojump to %s (DRYRUN)..." % args.destdir)
    else:
        print("Installing autojump to %s ..." % args.destdir)

    # (2.1) 把目录和文件名连接起来得到目标目录
    bin_dir = os.path.join(args.destdir, args.prefix, 'bin')
    etc_dir = os.path.join(args.destdir, 'etc', 'profile.d')
    doc_dir = os.path.join(args.destdir, args.prefix, 'share', 'man', 'man1')
    share_dir = os.path.join(args.destdir, args.prefix, 'share', 'autojump')
    zshshare_dir = os.path.join(args.destdir, args.zshshare)

    # (2.2) 创建目录
    mkdir(bin_dir, args.dryrun)
    mkdir(doc_dir, args.dryrun)
    mkdir(etc_dir, args.dryrun)
    mkdir(share_dir, args.dryrun)
    
    # (2.3) 把脚本从源拷贝到目的路径下
    cp('./bin/autojump', bin_dir, args.dryrun)
    cp('./bin/autojump_argparse.py', bin_dir, args.dryrun)
    cp('./bin/autojump_data.py', bin_dir, args.dryrun)
    cp('./bin/autojump_utils.py', bin_dir, args.dryrun)
    cp('./bin/icon.png', share_dir, args.dryrun)
    cp('./docs/autojump.1', doc_dir, args.dryrun)
    
    # (2.4) 判断是什么操作系统，并拷贝相应的脚本
    if platform.system() == 'Windows':
        cp('./bin/autojump.lua', args.clinkdir, args.dryrun)
        cp('./bin/autojump.bat', bin_dir, args.dryrun)
        cp('./bin/j.bat', bin_dir, args.dryrun)
        cp('./bin/jc.bat', bin_dir, args.dryrun)
        cp('./bin/jo.bat', bin_dir, args.dryrun)
        cp('./bin/jco.bat', bin_dir, args.dryrun)
         
        # (2.5) custom_install为 False，暂不分析lua脚本 
        if args.custom_install:
            modify_autojump_lua(args.clinkdir, bin_dir, args.dryrun)
    else:
        mkdir(etc_dir, args.dryrun)
        mkdir(share_dir, args.dryrun)
        mkdir(zshshare_dir, args.dryrun)

        cp('./bin/autojump.sh', etc_dir, args.dryrun)
        cp('./bin/autojump.bash', share_dir, args.dryrun)
        cp('./bin/autojump.fish', share_dir, args.dryrun)
        cp('./bin/autojump.zsh', share_dir, args.dryrun)
        cp('./bin/_j', zshshare_dir, args.dryrun)

        if args.custom_install:
            modify_autojump_sh(etc_dir, share_dir, args.dryrun)

    show_post_installation_message(etc_dir, share_dir, bin_dir)

# （1）首先调用parse_arguments()解析参数，并将返回值作为入参给main
if __name__ == "__main__":
    sys.exit(main(parse_arguments()))

```

autojump_argparse.py源码分析

```

# Author: Steven J. Bethard <steven.bethard@gmail.com>.
# -*- coding: utf-8 -*-
# flake8: noqa
"""Command-line parsing library

This module is an optparse-inspired command-line parsing library that:

    - handles both optional and positional arguments
    - produces highly informative usage messages
    - supports parsers that dispatch to sub-parsers

The following is a simple usage example that sums integers from the
command-line and writes the result to a file::

    parser = argparse.ArgumentParser(
        description='sum the integers at the command line')
    parser.add_argument(
        'integers', metavar='int', nargs='+', type=int,
        help='an integer to be summed')
    parser.add_argument(
        '--log', default=sys.stdout, type=argparse.FileType('w'),
        help='the file where the sum should be written')
    args = parser.parse_args()
    args.log.write('%s' % sum(args.integers))
    args.log.close()

The module contains the following public classes:

    - ArgumentParser -- The main entry point for command-line parsing. As the
        example above shows, the add_argument() method is used to populate
        the parser with actions for optional and positional arguments. Then
        the parse_args() method is invoked to convert the args at the
        command-line into an object with attributes.

    - ArgumentError -- The exception raised by ArgumentParser objects when
        there are errors with the parser's actions. Errors raised while
        parsing the command-line are caught by ArgumentParser and emitted
        as command-line messages.

    - FileType -- A factory for defining types of files to be created. As the
        example above shows, instances of FileType are typically passed as
        the type= argument of add_argument() calls.

    - Action -- The base class for parser actions. Typically actions are
        selected by passing strings like 'store_true' or 'append_const' to
        the action= argument of add_argument(). However, for greater
        customization of ArgumentParser actions, subclasses of Action may
        be defined and passed as the action= argument.

    - HelpFormatter, RawDescriptionHelpFormatter, RawTextHelpFormatter,
        ArgumentDefaultsHelpFormatter -- Formatter classes which
        may be passed as the formatter_class= argument to the
        ArgumentParser constructor. HelpFormatter is the default,
        RawDescriptionHelpFormatter and RawTextHelpFormatter tell the parser
        not to change the formatting for help text, and
        ArgumentDefaultsHelpFormatter adds information about argument defaults
        to the help.

All other classes in this module are considered implementation details.
(Also note that HelpFormatter and RawDescriptionHelpFormatter are only
considered public as object names -- the API of the formatter objects is
still considered an implementation detail.)
"""

```
