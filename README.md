## TLS 1.3 Demo Server App

### Requirement
Java: 17
### Setup TLS 1.3
1. Create keystore by executing this command:
```shell
keytool -genkeypair -alias demoTls1.3 -keyalg RSA -keysize 4096 \
  -validity 3650 -dname "CN=localhost" -keypass changeit -keystore keystore.p12 \
  -storeType PKCS12 -storepass changeit
```
2. Add these line on `application.properties`:
```yaml
# SSL protocol to use
server.ssl.protocol=TLS
# keystore format
server.ssl.key-store-type=PKCS12
# keystore location
server.ssl.key-store=keystore.p12
# keystore password
server.ssl.key-store-password=changeit
# Enabled SSL protocols
server.ssl.enabled-protocols=TLSv1.3
```
### Run The Server
1. Create CA Cert from keystore
```shell
openssl pkcs12 -in keystore.p12 -out demoTls1point3.crt -nokeys
### Then input "changeit" as the password
```
2. Run the SpringBoot App (`./gradlew bootRun`)
3. You'll see this line (`Tomcat started on port(s): 8080 (https)`) that confirm it runs on TLS mode
```shell
2022-11-29T11:25:29.663+07:00  INFO 63998 --- [           main] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat started on port(s): 8080 (https) with context path ''
```
4. Hit the api:
```shell
curl -v --cacert demoTls1point3.crt https://localhost:8080/actuator     
```
5. You'll see this output that confirm it is using TLS 1.3
```shell
* SSL connection using TLSv1.3 / AEAD-AES256-GCM-SHA384
* ALPN: server did not agree on a protocol. Uses default.
* Server certificate:
*  subject: CN=localhost
*  start date: Nov 29 04:33:05 2022 GMT
*  expire date: Nov 26 04:33:05 2032 GMT
*  common name: localhost (matched)
*  issuer: CN=localhost
*  SSL certificate verify ok.
```



### Sources:
* https://www.baeldung.com/spring-tls-setup
* https://www.ssl.com/how-to/export-certificates-private-key-from-pkcs12-file-with-openssl/