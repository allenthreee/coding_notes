# GDB 

- 从官网学习(其他都是二手或者三手资料)
- 系统性的学习才让人成长，碎片化的学习只会让人焦虑



## 1. GDB 是什么

GDB, the GNU Project debugger, allows you to see what is going on `inside' another program while it executes -- or what another program was doing at the moment it crashed.
GDB调试器允许逆在程序运行的时候检查程序里面发生了什么

GDB can do four main kinds of things (plus other things in support of these) to help you catch bugs in the act:

- Start your program, specifying anything that might affect its behavior.
  开始并设置参数
- Make your program stop on specified conditions.
  特殊的时候让程序停止（断点）
- Examine what has happened, when your program has stopped.
  检查当程序停止时发生了什么事情
- Change things in your program, so you can experiment with correcting the effects of one bug and go on to learn about another.
  改变程序中的东西，找bug？

Those programs might be executing on the same machine as GDB (native), on another machine (remote), or on a simulator. GDB can run on most popular UNIX and Microsoft Windows variants, as well as on Mac OS X.

## 2. 解决什么问题

debug的问题

## 3. quickstar



## 4. 了解它的底层是怎么实现的