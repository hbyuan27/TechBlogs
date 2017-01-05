# Install and configure SSL/TLS support on Tomcat
## Step 1: create keystore file
Create a keystore file to store the server's private key and self-signed certificate by executing the following command:

**Windows:**
````
"%JAVA_HOME%\bin\keytool" -genkey -alias tomcat -keyalg RSA
````
**Unix:**
```
$JAVA_HOME/bin/keytool -genkey -alias tomcat -keyalg RSA
```
**Note:**
- run the command, you will be asked to provide the keystore password and 'key password' for <tomcat> alias. by default, you can use 'changeit' which can be recogonized by tomcat.
- If you want to use your own password, you should keep the alias password the same as the keystore password, and put it in tomcat server.xml (keystorePass field)

## Step 2: edit tomcat configuration
uncomment below section - note: both my keystore password and tomcat alias password are 'tomcat8'
```
	<Connector
           protocol="org.apache.coyote.http11.Http11NioProtocol"
           port="8443" maxThreads="200"
           scheme="https" secure="true" SSLEnabled="true"
           keystoreFile="${user.home}/.keystore" keystorePass="tomcat8"
           clientAuth="false" sslProtocol="TLS"/>
```
