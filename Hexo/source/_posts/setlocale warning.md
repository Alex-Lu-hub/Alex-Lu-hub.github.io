---
title: setlocale warning
tags:  [Linux]
categories: [Linux]
date: 2020-10-27 16:00:00
updated: 2020-10-27 16:00:00
---

小系统登陆时出现多条如下warning:

```bash
-bash:warning:setlocale:LC_CTYPE:connot change locale (en_US.UTF-8):No such file or directory
```

<!-- more -->

# 解决方案

```bash
# 打开小系统里的i18n
vi /etc/sysconfig/i18n
# 添加下面的
LC_ALL=C
export LC_ALL
```

重新打包后问题解决。

# ``LC_ALL = C``

之前的报错说明我们本身的小系统里没有安装en_US的local，使用 ``LC_ALL = C`` 是为了去除所有本地化的设置，让命令能正确执行。

在Linux中通过 ``locale`` 来设置程序运行的不同语言环境， ``locale`` 由ANSI C提供支持。 ``locale`` 的命名规则为 ``<语言>_<地区>.<字符集编码>`` ，如 ``zh_CN.UTF-8`` ，zh代表中文，CN代表大陆地区，UTF-8表示字符集。在 ``locale`` 环境中，有一组变量，代表国际化环境中的不同设置：

*  ``LC_COLLATE``
定义该环境的排序和比较规则

*  ``LC_CTYPE``
用于字符分类和字符串处理，控制所有字符的处理方式，包括字符编码，字符是单字节还是多字节，如何打印等。是最重要的一个环境变量。

*  ``LC_MONETARY``
货币格式

*  ``LC_NUMERIC``
非货币的数字显示格式

*  ``LC_TIME``
时间和日期格式

*  ``LC_MESSAGES`` 
提示信息的语言。另外还有一个 ``LANGUAGE`` 参数，它与 ``LC_MESSAGES`` 相似，但如果该参数一旦设置，则 ``LC_MESSAGES`` 参数就会失效。 ``LANGUAGE`` 参数可同时设置多种语言信息，如 ``LANGUANE="zh_CN.GB18030:zh_CN.GB2312:zh_CN"`` 。

*  ``LANG``
 ``LC_*`` 的默认值，是最低级别的设置，如果 ``LC_*`` 没有设置，则使用该值。类似于 ``LC_ALL`` 。

*  ``LC_ALL`` 
它是一个宏，如果该值设置了，则该值会覆盖所有 ``LC_*`` 的设置值。注意， ``LANG`` 的值不受该宏影响。

"C"是系统默认的 ``locale`` ，"POSIX"是"C"的别名。所以当我们新安装完一个系统时，默认的 ``locale`` 就是 ``C`` 或 ``POSIX`` 。

