---
title: alias命令
tags:  [Linux]
categories: [Linux]
excerpt: alias命令可以帮助我们给命令设置别名，在日常使用Linux系统的过程中，可以提供很大的便利。
---

众所周知，Linux下的解压命令多又难记，时不时还有用错的情况。在邓迅邓巨神的指点下，知晓了 ``alias`` 这个神奇的命令，使用起来确实方便了许多，遂记下！

# 查看当前已设置的别名

```bash
alex@PC-20200316LEME:~$ alias
alias alert='notify-send --urgency=low -i "$([ $? = 0 ] && echo terminal || echo error)" "$(history|tail -n1|sed -e '\''s/^\s*[0-9]\+\s*//;s/[;&|]\s*alert$//'\'')"'
alias egrep='egrep --color=auto'
alias fgrep='fgrep --color=auto'
alias grep='grep --color=auto'
alias l='ls -CF'
alias la='ls -A'
alias lh='ls -lh'
alias ll='ls -alF'
alias ls='ls --color=auto'
alias tarbz2='tar -jxvf'
```

直接输入 ``alias`` 即可查看已经设置的别名，已经设置好的别名我们可以直接使用。例如，我输入命令 ``ll`` ，就相当于输入 ``ls -alF`` 。

# 为命令设置别名

```bash
alex@PC-20200316LEME:~$ alias vi='vim'
alex@PC-20200316LEME:~$ alias
alias alert='notify-send --urgency=low -i "$([ $? = 0 ] && echo terminal || echo error)" "$(history|tail -n1|sed -e '\''s/^\s*[0-9]\+\s*//;s/[;&|]\s*alert$//'\'')"'
alias egrep='egrep --color=auto'
alias fgrep='fgrep --color=auto'
alias grep='grep --color=auto'
alias l='ls -CF'
alias la='ls -A'
alias lh='ls -lh'
alias ll='ls -alF'
alias ls='ls --color=auto'
alias tarbz2='tar -jxvf'
alias vi='vim'
```

这个例子我们给 ``vim`` 设置了一个别名 ``vi`` ，注意设置时等号左右不要有空格。此时我们输入 ``vi`` 命令就相当于输入 ``vim`` 命令。

# 删除设置的别名

```bash
alex@PC-20200316LEME:~$ unalias vi
alex@PC-20200316LEME:~$ alias
alias alert='notify-send --urgency=low -i "$([ $? = 0 ] && echo terminal || echo error)" "$(history|tail -n1|sed -e '\''s/^\s*[0-9]\+\s*//;s/[;&|]\s*alert$//'\'')"'
alias egrep='egrep --color=auto'
alias fgrep='fgrep --color=auto'
alias grep='grep --color=auto'
alias l='ls -CF'
alias la='ls -A'
alias lh='ls -lh'
alias ll='ls -alF'
alias ls='ls --color=auto'
alias tarbz2='tar -jxvf'
```

使用 ``unalias`` 就可以删除我们已经设置好的别名。

# 让别名永久生效（当然，别删除它）

按照上面的方法生成的别名，在我们重启电脑后就会失效。怎么样才能让他一直生效呢？当然是把它加入到会自动运行的脚本中！ ``~/.bashrc`` 就是一个不错的选择。

打开 ``~/.bashrc`` ：

```bash
vim ~/.bashrc
```

可以看到一些已经写入在这个脚本中的 ``alias`` 命令：

```bash
···
# some more ls aliases
alias ll='ls -alF'
alias la='ls -A'
alias lh='ls -lh'
alias l='ls -CF'
#下面这行是我自己加入的alia命令，用来解压.tar.bz2的压缩包
alias tarbz2='tar -jxvf'
···
```

现在在这个脚本中加入自己想用的别名吧！之后每次只要你是用这个账户登录的Linux，就可以使用这些设置的别名。面对那些复杂的压缩解压命令等等，碰到一个就加入进来吧！

