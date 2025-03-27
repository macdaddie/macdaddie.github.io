# Useful OpenSSL Tricks

**By Van Emery**

## Introduction

OpenSSL deserves a lot of credit. It is an extremely useful, valuable Open Source project. When people talk about how successful Apache is, rock-solid crypto toolkits like OpenSSL and OpenSSH should also be mentioned. Here are a few (of the many) functions that I have found useful, along with examples of how to use them:

- Base64 Encoding and Decoding
- Symmetric Encryption and Decryption of Files
- Cryptographic Hashing of Files
- S_CLIENT SSL/TLS Test Utility

These examples assume that you are using a Unix-like OS, with OpenSSL 0.9.6b or higher.

## Base64 Encode/Decode

Base64 encoding is a standard method for converting 8-bit binary information into a limited subset of ASCII characters for safe transport through e-mail systems, and other systems that are not 8-bit safe. With OpenSSL, it is very easy to encode and decode Base64 data:

$ `openssl enc -base64 -in myfile -out myfile.b64`

$ `openssl enc -d -base64 -in myfile.b64 -out myfile.decrypt`

## Symmetric Encryption/Decryption of Files

As you can imagine, being able to encrypt and decrypt files with strong ciphers is a useful function. With OpenSSL, you can even use the commands in shell scripts. Here are some command line examples using the Blowfish, Triple DES, and CAST5 ciphers:

$ `openssl enc -e -a -salt -bf -in tomcat.jpg -out tomcat.blowfish`

enter bf-cbc encryption password:

Verifying password - enter bf-cbc encryption password:

$ `openssl enc -d -a -bf -in tomcat.blowfish -out tomcat-decrypt.jpg`

enter bf-cbc decryption password:

$ `openssl enc -e -a -salt -des3 -in tomcat.jpg -out tomcat.des3`

enter des-ede3-cbc encryption password:

Verifying password - enter des-ede3-cbc encryption password:

$ `openssl enc -d -a -des3 -in tomcat.des3 -out tomcat-des3.jpg`

enter des-ede3-cbc decryption password:

$ `openssl enc -e -a -salt -cast5-cbc -in tomcat.jpg -out tomcat.cast5`

enter cast5-cbc encryption password:

Verifying password - enter cast5-cbc encryption password:

$ `openssl enc -d -a -cast5-cbc -in tomcat.cast5 -out tomcat-cast5.jpg`

enter cast5-cbc decryption password:

 If the file will not be transported as an e-mail attachment, you can forego the -a argument, which base64 encodes and decodes the ciphertext. Sometimes this is referred to as "ASCII armor". The non-base64 encoded files should be smaller. Here is an example using the CAST5-CBC algorithm:

$ `openssl enc -e -salt -cast5-cbc -in tomcat.jpg -out tomcat.nob64`

enter cast5-cbc encryption password:

Verifying password - enter cast5-cbc encryption password:

$ `openssl enc -d -cast5-cbc -in tomcat.nob64 -out tomcat-nob64.jpg`

enter cast5-cbc decryption password:

## Cryptographic Hashing Functions

What if you want to check to see that a file has not been tampered with? One simple way to do this is a cryptographic *hashing* function. This will give you a fixed-length string (called a message digest) given an input file of any length. SHA-1 and RIPE-MD160 are considered current; MD-5 is considered outdated.

$ `openssl dgst -sha1 -c tomcat.jpg`

SHA1(tomcat.jpg)= 92:b1:9b:96:ef:45:c3:89:b4:2e:e6:96:5b:43:bf:02:66:4a:47:8f

$ `openssl dgst -ripemd160 -c tomcat.jpg`

RIPEMD160(tomcat.jpg)= 68:f2:05:a9:9d:52:f1:cc:04:ed:d7:1e:42:80:0a:b8:c0:e6:cc:6d

$ openssl dgst -md5 -c tomcat.jpg

MD5(tomcat.jpg)= e7:13:d6:a7:cc:16:e3:da:0a:f7:ab:5a:fa:e3:3b:34

You can see that the md5sum utility that is shipped with most GNU/Linux distributions returns the same value as the openssl md5 message digest:

$ `md5sum tomcat.jpg`

e713d6a7cc16e3da0af7ab5afae33b34 tomcat.jpg

The OpenSSL dgst (message digest/hashing) command also has numerous options for signing digests, verifying signatures, etc.

## S_CLIENT SSL/TLS Test Utility

OpenSSL has a great test utility available, called s_client. This lets you test servers that use SSL/TLS with a powerful command line utility. The following is an example of using s_client to view information about a secure web server:

$ `openssl s_client -connect www.redhat.com:443`

CONNECTED(00000003)

depth=0 /C=US/ST=North Carolina/L=Durham/O=Red Hat, Inc./OU=Web Operations/CN=www.redhat.com

verify error:num=20:unable to get local issuer certificate

verify return:1

depth=0 /C=US/ST=North Carolina/L=Durham/O=Red Hat, Inc./OU=Web Operations/CN=www.redhat.com

verify error:num=27:certificate not trusted

verify return:1

depth=0 /C=US/ST=North Carolina/L=Durham/O=Red Hat, Inc./OU=Web Operations/CN=www.redhat.com

verify error:num=21:unable to verify the first certificate

verify return:1

\---

### Certificate chain

 0 s:/C=US/ST=North Carolina/L=Durham/O=Red Hat, Inc./OU=Web Operations/CN=www.redhat.com

  i:/C=US/O=RSA Data Security, Inc./OU=Secure Server Certification Authority

\---

### Server certificate

```hex
-----BEGIN CERTIFICATE-----

MIID3TCCA0qgAwIBAgIQC4A9mzg//B7clolOw0V4WzANBgkqhkiG9w0BAQQFADBf

MQswCQYDVQQGEwJVUzEgMB4GA1UEChMXUlNBIERhdGEgU2VjdXJpdHksIEluYy4x

LjAsBgNVBAsTJVNlY3VyZSBTZXJ2ZXIgQ2VydGlmaWNhdGlvbiBBdXRob3JpdHkw

HhcNMDExMTE0MDAwMDAwWhcNMDMxMjA1MjM1OTU5WjCBgTELMAkGA1UEBhMCVVMx

FzAVBgNVBAgTDk5vcnRoIENhcm9saW5hMQ8wDQYDVQQHFAZEdXJoYW0xFjAUBgNV

BAoUDVJlZCBIYXQsIEluYy4xFzAVBgNVBAsUDldlYiBPcGVyYXRpb25zMRcwFQYD

VQQDFA53d3cucmVkaGF0LmNvbTCBnzANBgkqhkiG9w0BAQEFAAOBjQAwgYkCgYEA

4MFi5Xg1rYKETCZ4inSeLJwK4/g/WcOI8JUpH7aK/Hm/e8Lz0uwagzEg/EQnACGl

o6HZsAwlNwV/H4LDXhf4I7NIfgLHmrp6qY1e3SX5qfAAPbxFl4ghiGzNdlTR2Pkn

XQhj/0eW8Pt7NdmQ6LDaMHxb2WchBQYVTYC/cK2zU+8CAwEAAaOCAXkwggF1MAkG

A1UdEwQCMAAwCwYDVR0PBAQDAgWgMDwGA1UdHwQ1MDMwMaAvoC2GK2h0dHA6Ly9j

cmwudmVyaXNpZ24uY29tL1JTQVNlY3VyZVNlcnZlci5jcmwwgawGA1UdIASBpDCB

oTCBngYLYIZIAYb4RQEHAQEwgY4wKAYIKwYBBQUHAgEWHGh0dHBzOi8vd3d3LnZl

cmlzaWduLmNvbS9DUFMwYgYIKwYBBQUHAgIwVjAVFg5WZXJpU2lnbiwgSW5jLjAD

AgEBGj1WZXJpU2lnbidzIENQUyBpbmNvcnAuIGJ5IHJlZmVyZW5jZSBsaWFiLiBs

dGQuIChjKTk3IFZlcmlTaWduMB0GA1UdJQQWMBQGCCsGAQUFBwMBBggrBgEFBQcD

AjAZBgpghkgBhvhFAQYPBAsWCTg3ODA1MTU1NjA0BggrBgEFBQcBAQQoMCYwJAYI

KwYBBQUHMAGGGGh0dHA6Ly9vY3NwLnZlcmlzaWduLmNvbTANBgkqhkiG9w0BAQQF

AAN+AEBUhe0gnMw8OWcnKA5XnoglC3V9v//UIZh7lVJCaMA/K2tFAiRlmkGPsim7

H8rHpZhtTOUBqZl6PuA/VJD2wCECJ+uUYx0zUh1dKwoJKWgcaBQOQ6GsCgxsOB2a

i6wMUcAlqHZULjF1mDkM4bu0gNmLXpIMIsw9UotTvz/O

-----END CERTIFICATE-----
```

subject=/C=US/ST=North Carolina/L=Durham/O=Red Hat, Inc./OU=Web Operations/CN=www.redhat.com

issuer=/C=US/O=RSA Data Security, Inc./OU=Secure Server Certification Authority

\---

No client certificate CA names sent

\---

SSL handshake has read 1549 bytes and written 314 bytes

\---

New, TLSv1/SSLv3, Cipher is EDH-RSA-DES-CBC3-SHA

Server public key is 1024 bit

SSL-Session:

  Protocol : TLSv1

  Cipher  : EDH-RSA-DES-CBC3-SHA

  Session-ID: 97D3E2DF903F5757AF8BED807F5FD9665F43300F139BDFCD1701974D97E5C5CA

  Session-ID-ctx:

  Master-Key: 4B2295AEDCE520F4615769135FB65EBD6E2345C88FCE4EB7450B71B17FD1A2B4460D751DC3DF05C311DA54B02A7B04D1

  Key-Arg  : None

  Start Time: 1063899107

  Timeout  : 300 (sec)

  Verify return code: 21 (unable to verify the first certificate)

\---

Once you have connected, you can manually type in any commands you want, such as "GET /" and "HEAD / HTTP/1.0" for secure web servers. There are also options like -no_tls1 and -no_ssl2 that let you specify which version of SSL/TLS that you want to connect with.

 The -showcerts and -debug options are also worth a look.

Most web servers that run SSL (https) run on SSL version 3 or TLS version 1. There are still some outdated servers running SSL version 2.

To check if your web server is running SSL v2.0, run this on your Unix/Linux console:

openssl s_client -connect localhost:443 -ssl2

If it shows an error like SSL2_WRITE:ssl handshake failure:s2_pkt.c, it means SSL v2.0 has been disabled.

EXAMPLE: Lets test if slashdot.org is running SSL v2.0 or v3.0.

Check for SSL version 2

$ openssl s_client -connect slashdot.org:443 -ssl2

CONNECTED(00000004)

11358:error:1407F0E5:SSL routines:SSL2_WRITE:ssl handshake failure:s2_pkt.c:428:

|      | [![en-000000.png](blob:file:///71a8aae3-ebcd-4164-9dd7-9665e8cc59b0)](http://www.google.com/url?ct=abg&q=https://www.google.com/adsense/support/bin/request.py%3Fcontact%3Dabg_afc%26url%3Dhttp://aruljohn.com/info/sslversion/%26gl%3DGB%26hl%3Den%26client%3Dca-pub-4499772829528217&usg=AFQjCNHW-AIThZy4JnE45Tik15X4L0jgag) |      | [► Secure SSL](<http://googleads.g.doubleclick.net/pagead/ads?client=ca-pub-4499772829528217&output=html&h=15&slotname=4331892416&w=468&lmt=1369502168&flash=11.8.800&url=http://aruljohn.com/info/sslversion/&dt=1374744092218&bpp=17&shv=r20130718&cbv=r20130206&saldr=sa&prev_slotnames=4824922504&correlator=1374744092203&frm=20&adk=4066576775&ga_vid=1851460068.1374744092&ga_sid=1374744092&ga_hid=2038637376&ga_fc=0&u_tz=60&u_his=5&u_java=1&u_h=1080&u_w=1920&u_ah=1054&u_aw=1920&u_cd=24&u_nplug=10&u_nmime=67&dff=arial&dfs=14&adx=142&ady=602&biw=1265&bih=690&oid=3&ref=http://www.google.co.uk/url?sa=t&rct=j&q=openssl> ssl verify version&source=web&cd=1&ved=0CC0QFjAA&url=http%3A%2F%2Faruljohn.com%2Finfo%2Fsslversion%2F&ei=GO7wUd6oM5Cl0wWx-oHwDw&usg=AFQjCNE8Aojq9c3waLxpm_QJLawC-cwCmQ&sig2=Cr4jdRKL8U_9HhsTpUI1Ag&fu=0&ifi=2&dtd=32&xpc=ncQTdQXVue&p=<http://aruljohn.com&rl_rc=true&adsense_enabled=true&ad_type=text_image&oe=utf8&hei>)   [► SSL Key](<http://googleads.g.doubleclick.net/pagead/ads?client=ca-pub-4499772829528217&output=html&h=15&slotname=4331892416&w=468&lmt=1369502168&flash=11.8.800&url=http://aruljohn.com/info/sslversion/&dt=1374744092218&bpp=17&shv=r20130718&cbv=r20130206&saldr=sa&prev_slotnames=4824922504&correlator=1374744092203&frm=20&adk=4066576775&ga_vid=1851460068.1374744092&ga_sid=1374744092&ga_hid=2038637376&ga_fc=0&u_tz=60&u_his=5&u_java=1&u_h=1080&u_w=1920&u_ah=1054&u_aw=1920&u_cd=24&u_nplug=10&u_nmime=67&dff=arial&dfs=14&adx=142&ady=602&biw=1265&bih=690&oid=3&ref=http://www.google.co.uk/url?sa=t&rct=j&q=openssl> ssl verify version&source=web&cd=1&ved=0CC0QFjAA&url=http%3A%2F%2Faruljohn.com%2Finfo%2Fsslversion%2F&ei=GO7wUd6oM5Cl0wWx-oHwDw&usg=AFQjCNE8Aojq9c3waLxpm_QJLawC-cwCmQ&sig2=Cr4jdRKL8U_9HhsTpUI1Ag&fu=0&ifi=2&dtd=32&xpc=ncQTdQXVue&p=<http://aruljohn.com&rl_rc=true&adsense_enabled=true&ad_type=text_image&oe=utf8&hei>)   [► Domain SSL](<http://googleads.g.doubleclick.net/pagead/ads?client=ca-pub-4499772829528217&output=html&h=15&slotname=4331892416&w=468&lmt=1369502168&flash=11.8.800&url=http://aruljohn.com/info/sslversion/&dt=1374744092218&bpp=17&shv=r20130718&cbv=r20130206&saldr=sa&prev_slotnames=4824922504&correlator=1374744092203&frm=20&adk=4066576775&ga_vid=1851460068.1374744092&ga_sid=1374744092&ga_hid=2038637376&ga_fc=0&u_tz=60&u_his=5&u_java=1&u_h=1080&u_w=1920&u_ah=1054&u_aw=1920&u_cd=24&u_nplug=10&u_nmime=67&dff=arial&dfs=14&adx=142&ady=602&biw=1265&bih=690&oid=3&ref=http://www.google.co.uk/url?sa=t&rct=j&q=openssl> ssl verify version&source=web&cd=1&ved=0CC0QFjAA&url=http%3A%2F%2Faruljohn.com%2Finfo%2Fsslversion%2F&ei=GO7wUd6oM5Cl0wWx-oHwDw&usg=AFQjCNE8Aojq9c3waLxpm_QJLawC-cwCmQ&sig2=Cr4jdRKL8U_9HhsTpUI1Ag&fu=0&ifi=2&dtd=32&xpc=ncQTdQXVue&p=<http://aruljohn.com&rl_rc=true&adsense_enabled=true&ad_type=text_image&oe=utf8&hei>)   [► APACHE2 SSL](<http://googleads.g.doubleclick.net/pagead/ads?client=ca-pub-4499772829528217&output=html&h=15&slotname=4331892416&w=468&lmt=1369502168&flash=11.8.800&url=http://aruljohn.com/info/sslversion/&dt=1374744092218&bpp=17&shv=r20130718&cbv=r20130206&saldr=sa&prev_slotnames=4824922504&correlator=1374744092203&frm=20&adk=4066576775&ga_vid=1851460068.1374744092&ga_sid=1374744092&ga_hid=2038637376&ga_fc=0&u_tz=60&u_his=5&u_java=1&u_h=1080&u_w=1920&u_ah=1054&u_aw=1920&u_cd=24&u_nplug=10&u_nmime=67&dff=arial&dfs=14&adx=142&ady=602&biw=1265&bih=690&oid=3&ref=http://www.google.co.uk/url?sa=t&rct=j&q=openssl> ssl verify version&source=web&cd=1&ved=0CC0QFjAA&url=http%3A%2F%2Faruljohn.com%2Finfo%2Fsslversion%2F&ei=GO7wUd6oM5Cl0wWx-oHwDw&usg=AFQjCNE8Aojq9c3waLxpm_QJLawC-cwCmQ&sig2=Cr4jdRKL8U_9HhsTpUI1Ag&fu=0&ifi=2&dtd=32&xpc=ncQTdQXVue&p=<http://aruljohn.com&rl_rc=true&adsense_enabled=true&ad_type=text_image&oe=utf8&hei>) |
| ---- | ------------------------------------------------------------ | ---- | ------------------------------------------------------------ |
|      |                                                              |      |                                                              |

Check for SSL version 3

$ `openssl s_client -connect slashdot.org:443 -ssl2`

CONNECTED(00000004)

depth=0 /C=US/O=slashdot.org/OU=3246035501/OU=See www.geotrust.com/resources/cps (c)08/OU=

  Domain Control Validated - QuickSSL(R)/CN=slashdot.org

verify error:num=20:unable to get local issuer certificate

verify return:1

........

........

........

New, TLSv1/SSLv3, Cipher is RC4-MD5

Server public key is 1024 bit

Compression: NONE

Expansion: NONE

SSL-Session:

  Protocol : SSLv3

  Cipher  : RC4-MD5

Enable version SSLv3 and disable SSLv2

Edit Apache's ssl.conf and include these lines at minimum:

SSLProtocol -all +SSLv3

SSLCipherSuite SSLv3:+HIGH:+MEDIUM

Restart Apache and you're good to go.
