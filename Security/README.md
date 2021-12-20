# Keytool

> Manages a keystore (database) of cryptographic keys, X.509 certificate chains, and trusted certificates.
>
> 管理加密密钥、X.509 证书链和可信证书的密钥库（数据库）

> The *Java Keytool* is a command line tool which can generate public key / private key pairs and store them in a [Java KeyStore](http://tutorials.jenkov.com/java-cryptography/keystore.html). The Keytool executable is distributed with the Java SDK (or JRE), so if you have an SDK installed you will also have the Keytool executable.
>
> Java Keytool 是一个命令行工具，可以生成公钥/私钥对并将它们存储在 Java KeyStore 中。 Keytool 可执行文件随 Java SDK（或 JRE）一起分发，因此如果您安装了 SDK，您还将拥有 Keytool 可执行文件。

在Mac的终端中输入keytool，有如下的显示：

![001](https://github.com/winfredzen/JavaEE-Basic/blob/master/Security/images/001.png)



## 生成私钥和证书

 `keytool -genkey -help`帮助命令

![002](https://github.com/winfredzen/JavaEE-Basic/blob/master/Security/images/002.png)

### 生成证书

**默认生成jks格式的证书**

```shell
keytool -genkeypair -alias serverkey -keystore server.keystore -keyalg RSA  -keysize 2048
```

![003](https://github.com/winfredzen/JavaEE-Basic/blob/master/Security/images/003.png)

+ `-genkeypair`  -  生成一对密钥
+ `-keyalg RSA `- 使用 RSA 算法
+ `-alias serverkey` -  别名，这个比较重要，主要用于管理密钥
+ `-keystore server.keystore` - keystore 的文件名
+ `-keysize 2048` - 密钥长度, 本例为 2048



其它：

+ `-storepass xxxx` - keystore 的密码
+ `-validity 360` - 设置有效期，默认为三个月



若不想输入参数，可提供参数：

```shell
# keysize 密钥长度(DSA算法对应的默认算法是sha1withDSA，不支持2048长度，此时需指定RSA)
$ keytool -genkeypair -alias serverkey -keypass 123456 -storepass 123456 \
-dname "C=CN,ST=JS,L=SZ,O=buubiu,OU=dev,CN=bu" \
-keyalg RSA -keysize 2048 -validity 365 -keystore server.keystore
```



### 查看keystore详情

`keytool -list -help`列出秘钥库中条目

![004](https://github.com/winfredzen/JavaEE-Basic/blob/master/Security/images/004.png)

如下的例子，`keytool -list -keystore server.keystore`

![005](https://github.com/winfredzen/JavaEE-Basic/blob/master/Security/images/005.png)

> 由于生成时没有设置密码，直接按Enter键即可





再看如下的例子，参考自：

+ [Java Keytool工具简介](https://blog.csdn.net/liumiaocn/article/details/61921014)

```shell
keytool -genkey -alias kstore -keypass init123 -keyalg RSA -keysize 2048 -validity 30 -keystore /tmp/kstore.keystore -storepass init234
```

+ `-keypass` - 指定别名条目的密码(私钥的密码)



```shell
keytool -list  -v -keystore /tmp/kstore.keystore -storepass init234
```

![006](https://github.com/winfredzen/JavaEE-Basic/blob/master/Security/images/006.png)



**证书导出**

```shell
keytool -export -alias kstore -keystore kstore.keystore -file kstore.crt -rfc -storepass init234
```

生成了`kstore.crt`文件，使用mac下的钥匙串打开，显示如下的内容：

![007](https://github.com/winfredzen/JavaEE-Basic/blob/master/Security/images/007.png)

































