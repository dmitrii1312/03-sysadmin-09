## 1 Установите Bitwarden плагин для браузера. Зарегестрируйтесь и сохраните несколько паролей.

![image](https://user-images.githubusercontent.com/93075740/146953240-aba0db84-cf8f-442e-81a3-0fc8c6f5db38.png)

![image](https://user-images.githubusercontent.com/93075740/146955690-1886558c-7363-45c0-a183-b1e0ed56eeab.png)

## 2 Установите Google authenticator на мобильный телефон. Настройте вход в Bitwarden акаунт через Google authenticator OTP.

![image](https://user-images.githubusercontent.com/93075740/146956022-560184ee-44ad-4356-8274-e2faa14c12b3.png)

## 3 Установите apache2, сгенерируйте самоподписанный сертификат, настройте тестовый сайт для работы по HTTPS.

	apt install apache2

```
# openssl req -x509 -newkey rsa:4096 -keyout key.pem -out cert.pem -sha256 -days 3650
Generating a RSA private key
.......++++
..............++++
writing new private key to 'key.pem'
Enter PEM pass phrase:
Verifying - Enter PEM pass phrase:
-----
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) [AU]:RU
State or Province Name (full name) [Some-State]:Saint-Petersburg
Locality Name (eg, city) []:Saint-Petersburg
Organization Name (eg, company) [Internet Widgits Pty Ltd]:Netology
Organizational Unit Name (eg, section) []:Student
Common Name (e.g. server FQDN or YOUR name) []:test.test.ru
Email Address []:
```

```
# openssl rsa -in key.pem -out key2.pem
Enter pass phrase for key.pem:
writing RSA key
```

	# a2enmod ssl
	
	# cp cert.pem /etc/ssl/certs/test.pem
	# cp key2.pem /etc/ssl/private/test.key
	# vi /etc/apache2/sites-available/default-ssl.conf

```
SSLCertificateFile      /etc/ssl/certs/test.pem
SSLCertificateKeyFile /etc/ssl/private/test.key
```

	# a2ensite default-ssl
	# systemctl restart apache2
	
## 4 Проверьте на TLS уязвимости произвольный сайт в интернете (кроме сайтов МВД, ФСБ, МинОбр, НацБанк, РосКосмос, РосАтом, РосНАНО и любых госкомпаний, объектов КИИ, ВПК ... и тому подобное).

```
./testssl.sh -U --sneaky https://mail.ru

###########################################################
    testssl.sh       3.1dev from https://testssl.sh/dev/
    (35ddd91 2021-12-21 10:54:58 -- )

      This program is free software. Distribution and
             modification under GPLv2 permitted.
      USAGE w/o ANY WARRANTY. USE IT AT YOUR OWN RISK!

       Please file bugs @ https://testssl.sh/bugs/

###########################################################

 Using "OpenSSL 1.0.2-chacha (1.0.2k-dev)" [~183 ciphers]
 on ubuntu-20:./bin/openssl.Linux.x86_64
 (built: "Jan 18 17:12:17 2019", platform: "linux-x86_64")


Testing all IPv4 addresses (port 443): 217.69.139.200 94.100.180.201 217.69.139.202 94.100.180.200
--------------------------------------------------------------------------------
 Start 2021-12-21 16:26:53        -->> 217.69.139.200:443 (mail.ru) <<--

 Further IP addresses:   94.100.180.200 217.69.139.202 94.100.180.201 2a00:1148:db00:0:b0b0::1
 rDNS (217.69.139.200):  mail.ru.
 Service detected:       HTTP


 Testing vulnerabilities

 Heartbleed (CVE-2014-0160)                not vulnerable (OK), timed out
 CCS (CVE-2014-0224)                       not vulnerable (OK)
 Ticketbleed (CVE-2016-9244), experiment.  not vulnerable (OK)
 ROBOT                                     not vulnerable (OK)
 Secure Renegotiation (RFC 5746)           supported (OK)
 Secure Client-Initiated Renegotiation     not vulnerable (OK)
 CRIME, TLS (CVE-2012-4929)                not vulnerable (OK)
 BREACH (CVE-2013-3587)                    potentially NOT ok, "gzip" HTTP compression detected. - only supplied "/" tested
                                           Can be ignored for static pages or if no secrets in the page
 POODLE, SSL (CVE-2014-3566)               not vulnerable (OK)
 TLS_FALLBACK_SCSV (RFC 7507)              Downgrade attack prevention supported (OK)
 SWEET32 (CVE-2016-2183, CVE-2016-6329)    VULNERABLE, uses 64 bit block ciphers
 FREAK (CVE-2015-0204)                     not vulnerable (OK)
 DROWN (CVE-2016-0800, CVE-2016-0703)      not vulnerable on this host and port (OK)
                                           make sure you don't use this certificate elsewhere with SSLv2 enabled services
                                           https://censys.io/ipv4?q=73CE7337E1FE4D5E6CBAB304B5E401B21C006CCEC612092AD83209BBABEED18B could help you to find out
 LOGJAM (CVE-2015-4000), experimental      not vulnerable (OK): no DH EXPORT ciphers, no common prime detected
 BEAST (CVE-2011-3389)                     TLS1: ECDHE-RSA-AES256-SHA ECDHE-RSA-AES128-SHA ECDHE-RSA-DES-CBC3-SHA
                                                 DHE-RSA-AES256-SHA DHE-RSA-AES128-SHA EDH-RSA-DES-CBC3-SHA AES256-SHA
                                                 AES128-SHA DES-CBC3-SHA
                                           VULNERABLE -- but also supports higher protocols  TLSv1.1 TLSv1.2 (likely mitigated)
 LUCKY13 (CVE-2013-0169), experimental     potentially VULNERABLE, uses cipher block chaining (CBC) ciphers with TLS. Check patches
 Winshock (CVE-2014-6321), experimental    not vulnerable (OK) - CAMELLIA or ECDHE_RSA GCM ciphers found
 RC4 (CVE-2013-2566, CVE-2015-2808)        no RC4 ciphers detected (OK)


 Done 2021-12-21 16:27:37 [  45s] -->> 217.69.139.200:443 (mail.ru) <<--
 ```
 
 ## 5 Установите на Ubuntu ssh сервер, сгенерируйте новый приватный ключ. Скопируйте свой публичный ключ на другой сервер. Подключитесь к серверу по SSH-ключу.
 
```
 # apt install openssh-server
 # ssh-keygen
 # ssh-copy-id 192.168.22.12
 # ssh 192.168.22.12 -v
debug1: Authentication succeeded (publickey).
Authenticated to 192.168.22.12 ([192.168.22.12]:22).
```
