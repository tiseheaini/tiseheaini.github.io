---
layout: post
title: "在iterm irb中显示中文"
tags: []
---

```bash
tiseheaini.github.io git:(master) ✗ irb
irb(main):001:0> s = '\U+FFE4\U+FFB8\U+FFAD\U+FFE6'
=> ""
irb(main):002:0> 

```
昨天突然出现这样的问题，让我苦恼了很久，开始的时候以为是 iTerm 除了问题，后来才知道是因为少装了一个库 ==> readline (在linux 中没出现这个问题)

使用 brew 来安装 readline

```bash
brew install readline
```
系统会提示你一些命令，如

```bash
LDFLAGS: -L/usr/local/Cellar/readline/6.2.4/lib
CPPFLAGS: -L/usr/local/Cellar/readline/6.2.4/include
```

这些环境变量运行一下。

```bash
LDFLAGS="-L/usr/local/Cellar/readline/6.2.4/lib"
CPPFLAGS="-I/usr/local/Cellar/readline/6.2.4/include"
```

卸载掉之前的 ruby 版本

```bash
rbenv uninstall 2.0.0-p451
##重新安装
rbenv install 2.0.0-p451
```

这种方式应该可以通过，如果在编译的过程中出现问题

```bash
compiling ossl_pkcs12.c
compiling ossl_pkcs5.c
compiling ossl_pkcs7.c
compiling ossl_pkey.c
compiling ossl_pkey_dh.c
compiling ossl_pkey_dsa.c
compiling ossl_pkey_ec.c
compiling ossl_pkey_rsa.c
compiling ossl_rand.c
compiling ossl_ssl.c
compiling ossl_ssl_session.c
linking shared-object date_core.bundle
compiling ossl_x509.c
compiling ossl_x509attr.c
ossl_ssl.c:1114:13: warning: using the result of an assignment as a condition without parentheses [-Wparentheses]
            if (rc = SSL_shutdown(ssl))
                ~~~^~~~~~~~~~~~~~~~~~~
ossl_ssl.c:1114:13: note: place parentheses around the assignment to silence this warning
            if (rc = SSL_shutdown(ssl))
                   ^
                (                     )
ossl_ssl.c:1114:13: note: use '==' to turn this assignment into an equality comparison
            if (rc = SSL_shutdown(ssl))
                   ^
                   ==
compiling ossl_x509cert.c
compiling ossl_x509crl.c
compiling ossl_x509ext.c
linking shared-object nkf.bundle
compiling ossl_x509name.c
compiling ossl_x509req.c
compiling ossl_x509revoked.c
1 warning generated.
compiling ossl_x509store.c
installing default openssl libraries
linking shared-object openssl.bundle
linking shared-object ripper.bundle
make: *** [build-ext] Error 2
```

这个说明 readline 没有装好，很不幸我就遇到了这个问题，然后我找了另一个方法去解决
[连接地址](https://github.com/sstephenson/rbenv/issues/579#issuecomment-41122071)

```bash
brew uninstall readline
brew install https://raw.githubusercontent.com/Homebrew/homebrew/0181c8a1633353affefabe257c170edbd6d7c008/Library/Formula/readline.rb
brew pin readline
```

卸载 ruby 重新安装，到了这里才算是完结了，我在 Mac 运行 irb 也可以正常的打中文了。