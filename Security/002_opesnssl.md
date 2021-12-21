# openssl

参考[Mac OSX 安装新版OpenSSL问题](https://www.jianshu.com/p/32f068922baf):

> Mac OSX一般自带的有OpenSSL。但因为上述OpenSSL“心脏病失血”事件，Mac OSX 自 10.11 El Capitan 起，将原有的 OpenSSL 替换为 LibreSSL 。

![008](https://github.com/winfredzen/JavaEE-Basic/blob/master/Security/images/008.png)



## 使用Openssl生成根证书CA以及签发子证书

使用Openssl生成根证书CA以及签发子证书网络上有很多文章，可以作为参考，大致有如下的几个步骤：

1.生成根证书及自签名证书

2.生成服务端证书，使用根证书签发

3.生成客户证书，使用根证书签发











