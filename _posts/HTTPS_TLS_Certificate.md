---
title: 数字签名, 数字证书, 加密, SSL, HTTPS
date: 2017-03-15
categories: web
tags: [TLS, HTTPS]
---

# 加解密
- 对称加密算法: DES, 3DES, AES(标准)
- 非对称加密: RSA, DSA, 分公钥和私钥.
# 摘要算法(Digest)
- MD5
- SHA1, SHA2(常用)[参考](https://en.wikipedia.org/wiki/Secure_Hash_Algorithm)

# 数字签名
数字签名用于验证发送方的身份, 并保证数据的完整性.
1. 发送方对要传输的数据计算摘要
2. 对摘要用私钥加密, 该密文称为签名
3. 接收方收到数据,用同样的摘要算法计算出摘要1, 并用发送方的公钥解密签名得到摘要2, 如果摘要1 == 摘要2, 说明数据确实来自发送方,并且传输过程中数据没有被篡改.
> 接收方需要有发送方的公钥, 公钥是通过数字证书获得的.
[图文参考](http://www.ruanyifeng.com/blog/2011/08/what_is_a_digital_signature.html)

# 数字证书
数字证书由证书颁发机构(CA, certificate authority)颁发,可以用于确认证书持有者的身份和分发公钥.
数字证书中包含的内容:

- Subject Name: 证书持有人的信息,国家,地点,域名等
- Issuer Name: 证书颁发机构信息
- Public Key: 证书持有人的公钥
- Signature algorithm : 采用的摘要算法
- Valid before/after: 证书有效时间
- FingerPrints: 证书的签名, 由颁发机构用自己的私钥加密, 用颁发机构的公钥可以解密签名获得证书的摘要, 如果这个摘要与用户计算出的摘要一致,那么这个证书就是可以信任的.
    
# 根证书(root certificate):
证书颁发机构给自己颁发的证书, 内含该机构的公钥, 是信任链的起点. 用户可以使用公钥去验证该机构颁发的其他证书. 根证书通常由操作系统预装.


# SSL
SSL: Secure Sockets Layer, 是介于HTTP和TCP之间的一个可选层. 是一套由非对称加密, 对称加密, 摘要算法构成的加密传输协议. client像server所要公钥并验证, 通信双方协商一个对称加密密钥, 然后双方采用对称加密算法进行加密通信.


### 握手过程:
握手就是互相验证,并协商密钥的过程, 如图.
![握手过程](https://i.imgsafe.org/8e54710257.png)

**交互流程图**:

![握手过程](https://i.imgsafe.org/8e58a55806.png)
[*图片来源*](https://www.ibm.com/support/knowledgecenter/SSFKSJ_7.1.0/com.ibm.mq.doc/sy10660_.htm)

**WireShark抓包结果**:

![握手过程](https://i.imgsafe.org/8e5bd5fe66.png)

- client发送
    - **ClientHello**,数据结构:

            struct {
                ProtocolVersion client_version;// client支持的协议版本,SSL2.0, SSL3.0, SSL3.1 ie TLS1.0,TLS2.0等
                Random random;//32 bytes(4 bytes timestamp + 28 random bytes),用于生成对称密钥
                SessionID session_id;//the client may decide to reuse previously established session.
                CipherSuite cipher_suites<2..2^16-1>;//client可用的加密组合, 如TLS_RSA_WITH_3DES_EDE_CBC_SHA, TLS_RSA_WITH_DES_CBC_SHA
                CompressionMethod compression_methods<1..2^8-1>;//client支持的压缩算法, deflate, gzip等
                Extension extensions<0..2^16-1>;
            } ClientHello;

- server收到请求, 发送
    - **ServerHello**,数据结构:

            struct {
               ProtocolVersion server_version;//
               Random random;//与ClientHello.random一样,但两者必须不同
               SessionID session_id;
               CipherSuite cipher_suite;//Server从ClientHello中选择出的一个cipher suite
               CompressionMethod compression_method;//Server从ClientHello中选择出的一个压缩算法
            } ServerHello;
    - **Server Certificate**, 即server的数字证书,其中包含了server的公钥, client将用这个公钥验证server, 并用于下一步中加密premaster secret.
    - **Server Key Exchange**,可选的,client可用来加密下一步中的Client Key Exchange,只有当证书中不包含公钥的时候才需要.如Diffie Hellman密钥交换算法.通常使用RSA公钥算法的时候不需要提供.[key-exchange-参考1](https://en.wikipedia.org/wiki/Key_exchange#Public_key_infrastructure),[key-exchange-参考2](https://technet.microsoft.com/en-us/library/cc962035.aspx)
    - **Client Certificate Request**,可选的,通常的站点不需要验证用户的身份,如google;但银行站点需要验证,如U盾中就包含了client证书,通信过程需要客户与银行互相验证.
    - **Server Hello Done**,抓包结果中并没有这个

- client获得server证书之后, 验证证书的合法性. 如果证书受信任，浏览器栏里面会显示一个小锁头，否则会给出证书不受信的提示。如果证书受信任，或者是用户接受了不受信的证书，client会再生成一串随机数(用于生成对称密钥, **第三个随机数称为pre-master key**)，计算摘要,并一起用证书中提供的公钥加密,发送给server:
    - **Client Certificate**, 可选的,只有收到了server的Client Certificate Request消息后才发送
    - **Certificate Verify**. 在client发送Client Certificate之后发送,发送了一段数据和签名,用于server验证client是否拥有正确的私钥.
    - **Client Key Exchange**, client利用已有的随机数生成一个premaster secret, 用server的公钥加密后发送给server.双方都在本地计算master key,而不是通过网络传输.
    - **Change Cipher Spec**.通知server,Client Finish之后的所有消息都使用刚刚协商好的密钥加密.
    - **Client Finished**. client计算整个会话的摘要, 然后用协商好的密钥加密之后发送给server,这是第一条用协商密钥加密的消息.

- server接收client发来的数据之后,用自己的私钥将信息解密,取出随机数,并验证摘要是否一致。server利用前面得到的**三个随机数**生成对称密钥(协商密钥).然后向client发送通知:

    - **Change Cipher Spec**. 通知client, server将用协商的密钥加密之后的消息.
    - **Server Finished**. server计算整个会话的摘要,用协商密钥加密之后发给client,如果client能解密,并且摘要通过验证,那么握手过程就建立好了.

    
> 握手过程中如果有任何错误，都会断开连接.

参考:
> https://technet.microsoft.com/en-us/library/cc785811(v=ws.10).aspx
> http://www.cisco.com/c/en/us/support/docs/security-vpn/secure-socket-layer-ssl/116181-technote-product-00.html
> http://tools.ietf.org/html/rfc2246#page-31
> http://www.ruanyifeng.com/blog/2014/02/ssl_tls.html
> http://www.infoq.com/cn/articles/HTTPS-Connection-Jeff-Moser
> ssl握手过程抓包工具wireshark

# TLS (Transport Layer Security)
TLS来源于SSL, 从SSL3.1开始改名为TLS1.0, 所以经常会说SSL/TLS. 
> TLS与SSL的区别与联系:
http://security.stackexchange.com/questions/5126/whats-the-difference-between-ssl-tls-and-https
https://luxsci.com/blog/ssl-versus-tls-whats-the-difference.html

# HTTPS
HTPPS = TCP + SSL/TLS. 
