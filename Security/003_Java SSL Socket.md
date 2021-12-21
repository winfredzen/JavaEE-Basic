# Java SSL Socket

**Keystore vs Truststore**

在使用SSL/TLS进行通讯的时候，需要使用到**keystore**和**truststore**

参考：

+ [Difference Between a Java Keystore and a Truststore](https://www.baeldung.com/java-keystore-truststore-difference)



> Usually, these are password-protected files that sit on the same file system as our running application. The default format used for these files is **JKS until Java 8**.
>
> 通常，这些受密码保护的文件与我们正在运行的应用程序位于同一文件系统中。 在 Java 8 之前，这些文件使用的默认格式是 JKS。
>
> Since Java 9, though, **the default keystore format is PKCS12**. The biggest difference between JKS and PKCS12 is that JKS is a format specific to Java, while PKCS12 is a standardized and language-neutral way of storing encrypted private keys and certificates.
>
> 但是，从 Java 9 开始，默认的密钥库格式是 PKCS12。 JKS 和 PKCS12 的最大区别在于 JKS 是 Java 特有的格式，而 PKCS12 是一种标准化的、语言中立的存储加密私钥和证书的方式。



> A Java keystore stores private key entries, certificates with public keys or just secret keys that we may use for various cryptographic purposes. It stores each by an alias for ease of lookup.
>
> Java 密钥库存储私钥条目、带有公钥的证书或我们可能用于各种加密目的的秘密密钥。为了便于查找，它通过别名存储每个。
>
> Generally speaking, keystores hold keys that our application owns that we can use to prove the integrity of a message and the authenticity of the sender, say by signing payloads.
>
> 一般来说，密钥库持有我们的应用程序拥有的密钥，我们可以使用这些密钥来证明消息的完整性和发送者的真实性，比如通过签署有效载荷。
>
> Usually, we'll use a keystore when we are a server and want to use HTTPS. During an SSL handshake, the server looks up the private key from the keystore and presents its corresponding public key and certificate to the client.
>
> 通常，当我们是服务器并且想要使用 HTTPS 时，我们会使用密钥库。在 SSL 握手期间，服务器从密钥库中查找私钥并将其相应的公钥和证书提供给客户端。
>
> Correspondingly, if the client also needs to authenticate itself – a situation called mutual authentication – then the client also has a keystore and also presents its public key and certificate.
>
> 相应地，如果客户端也需要对自己进行身份验证——这种情况称为相互身份验证——那么客户端也有一个密钥库，并提供其公钥和证书。
>
> There's no default keystore, so if we want to use an encrypted channel, we'll have to set javax.net.ssl.keyStore and javax.net.ssl.keyStorePassword. If our keystore format is different than the default, we could use javax.net.ssl.keyStoreType to customize it.
>
> 没有默认的密钥库，所以如果我们想使用加密通道，我们必须设置 `javax.net.ssl.keyStore` 和 `javax.net.ssl.keyStorePassword`。如果我们的密钥库格式与默认格式不同，我们可以使用 `javax.net.ssl.keyStoreType` 对其进行自定义。
>
> Of course, we can use these keys to service other needs as well. Private keys can sign or decrypt data, and public keys can verify or encrypt data. Secret keys can perform these functions as well. A keystore is a place that we can hold onto these keys.
>
> 当然，我们也可以使用这些密钥来满足其他需求。私钥可以对数据进行签名或解密，而公钥可以验证或加密数据。密钥也可以执行这些功能。密钥库是我们可以保存这些密钥的地方。
>
> We can also interact with the keystore programmatically.



> A truststore is the opposite – while a keystore typically holds onto certificates that identify us, a truststore holds onto certificates that identify others.
>
> 信任库则相反——密钥库通常持有可识别我们身份的证书，而信任库则持有可识别他人身份的证书。
>
> In Java, we use it to trust the third party we're about to communicate with.
>
> 在 Java 中，我们使用它来信任我们将要与之通信的第三方。
>
> Take our earlier example. If a client talks to a Java-based server over HTTPS, the server will look up the associated key from its keystore and present the public key and certificate to the client.
>
> 以我们之前的例子为例。 如果客户端通过 HTTPS 与基于 Java 的服务器通信，服务器将从其密钥库中查找关联的密钥，并将公钥和证书提供给客户端。
>
> We, the client, then look up the associated certificate in our truststore. If the certificate or Certificate Authorities presented by the external server is not in our truststore, we'll get an SSLHandshakeException and the connection won't be set up successfully.
>
> 我们，客户，然后在我们的信任库中查找相关的证书。 如果外部服务器提供的证书或证书颁发机构不在我们的信任库中，我们将收到 SSLHandshakeException 并且无法成功建立连接。
>
> Java has bundled a truststore called cacerts and it resides in the $JAVA_HOME/jre/lib/security directory.
>
> Java 捆绑了一个名为 cacerts 的信任库，它位于 `$JAVA_HOME/jre/lib/security` 目录中。
>
> It contains default, trusted Certificate Authorities:
>
> 它包含默认的、受信任的证书颁发机构



## SSL Socket

SSL Socket的例子，可参考:

+ [williamswhy](https://github.com/williamswhy)/**[SSLSocket](https://github.com/williamswhy/SSLSocket)**



这个例子，应该只是客服端验证服务端的证书，不是双向验证，但也很有参考价值

> ### Generate SSL
>
> Create a server keystore file:
>
> ```shell-script
> keytool -genkey -keystore sslserverkeys -keyalg RSA
> ```
>
> Export the key as a cert:
>
> ```shell-script
> keytool -export -keystore sslserverkeys -file cert.cer -keyalg RSA
> ```
>
> Add the cert to the trust store of the client:
>
> ```shell-script
> keytool -import -keystore sslclienttrust -file cert.cer -keyalg RSA
> ```



客户端是trustStore的代码

```java
if(System.getProperty("javax.net.ssl.trustStore") == null || System.getProperty("javax.net.ssl.trustStorePassword") == null) {
	System.setProperty("javax.net.ssl.trustStore", "sslclienttrust");
	System.setProperty("javax.net.ssl.trustStorePassword", "123456");
}
```

服务端是keyStore的代码

```java
if (System.getProperty("javax.net.ssl.keyStore") == null || System.getProperty("javax.net.ssl.keyStorePassword") == null) {
    // set keystore store location
    System.setProperty("javax.net.ssl.keyStore", "sslserverkeys");
    System.setProperty("javax.net.ssl.keyStorePassword", "123456");
}
```



如果想使用代码方式实现，参考：[SSL Socket connection](https://stackoverflow.com/questions/18787419/ssl-socket-connection)

```java
KeyStore ks = KeyStore.getInstance("JKS");
ks.load(new FileInputStream("keystoreFile"), "keystorePassword".toCharArray());

KeyManagerFactory kmf = KeyManagerFactory.getInstance("X509");
kmf.init(ks, "keystorePassword".toCharArray());

TrustManagerFactory tmf = TrustManagerFactory.getInstance("X509"); 
tmf.init(ks);

SSLContext sc = SSLContext.getInstance("TLS"); 
TrustManager[] trustManagers = tmf.getTrustManagers(); 
sc.init(kmf.getKeyManagers(), trustManagers, null); 

SSLServerSocketFactory ssf = sc.getServerSocketFactory(); 
SSLServerSocket s = (SSLServerSocket) ssf.createServerSocket(serverport);
SSLSocket c = (SSLSocket) s.accept();
```





参考[Java使用SSLSocket通信](https://my.oschina.net/itblog/blog/651608)

> 客户端
>
> ```java
> 	//客户端将要使用到client.keystore和ca-trust.keystore
> 	public void init() throws Exception {
> 		String host = "127.0.0.1";
> 		int port = 1234;
> 		String keystorePath = "/home/user/CA/certs/client.keystore";
> 		String trustKeystorePath = "/home/user/CA/certs/ca-trust.keystore";
> 		String keystorePassword = "abc123_";
> 		SSLContext context = SSLContext.getInstance("SSL");
> 		//客户端证书库
> 		KeyStore clientKeystore = KeyStore.getInstance("pkcs12");
> 		FileInputStream keystoreFis = new FileInputStream(keystorePath);
> 		clientKeystore.load(keystoreFis, keystorePassword.toCharArray());
> 		//信任证书库
> 		KeyStore trustKeystore = KeyStore.getInstance("jks");
> 		FileInputStream trustKeystoreFis = new FileInputStream(trustKeystorePath);
> 		trustKeystore.load(trustKeystoreFis, keystorePassword.toCharArray());
> 		
> 		//密钥库
> 		KeyManagerFactory kmf = KeyManagerFactory.getInstance("sunx509");
> 		kmf.init(clientKeystore, keystorePassword.toCharArray());
> 
> 		//信任库
> 		TrustManagerFactory tmf = TrustManagerFactory.getInstance("sunx509");
> 		tmf.init(trustKeystore);
> 		
> 		//初始化SSL上下文
> 		context.init(kmf.getKeyManagers(), tmf.getTrustManagers(), null);
> 		
> 		sslSocket = (SSLSocket)context.getSocketFactory().createSocket(host, port);
> 	}
> ```



























