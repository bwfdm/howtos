GnuPG (GPG) - programm to make a certificate to sign your data (and communications - mails, messages...)
* https://www.gnupg.org/

Installation of GnuPG (GPG) and maven configuration:
* https://central.sonatype.org/pages/working-with-pgp-signatures.html

Maven password encryption:
* https://maven.apache.org/guides/mini/guide-encryption.html

## Configuration

1. GPG config file "~/.gnupg/gpg.conf"
* no changes are needed here! But we can set a default gpg key (read comments inside the file there!)

2. Create a master password for maven, it will be used for your user account on the computer: 
* see https://maven.apache.org/guides/mini/guide-encryption.html
* `mvn --encrypt-master-password <some_extra_password>`

the output will be like this:

`{jSMOWnoPFgsHVpMvz5VrIt5kRbzGpI8u+9EF1iFQyJQ=}`

put this password to file in your home folder: `~/.m2/settings-security.xml`

````
    <settingsSecurity>
      <master>{jSMOWnoPFgsHVpMvz5VrIt5kRbzGpI8u+9EF1iFQyJQ=}</master>
    </settingsSecurity>
````

3. Import a private key (public key will be added automatically)

`gpg --allow-secret-key-import --import your_private.key`

4. Get id of the private key. Get a list of private keys and copy the needed key id, e.g. "ABCD012":

`gpg --list-secret-keys`

part of the output will be like this:

`sec     4096R/ABCD012`

where `ABCD012` is a needed key id.

5. Configure maven general settings. 
* file for Windows -> `C:\Program Files\apache-maven-3.3.9\conf\settings.xml`
* for Linux -> `/usr/java/apache-maven-3.3.9/conf/settings.xml`
* encrypt your passwords for Sonatype and for the private key, in both cases use this command: 
  
  `mvn --encrypt-password <your_password_for_sonatype_or_private-key>`
 
 the output will be like this: `{COQLCE6DU6GtcS5P=}`, copy it together with "{}".

* Setup authentication servers:
  
  * Sonatype: your login for Sonatype and encrypted password
  * your private key: key id and encrypted password
         
````
    <server>
      <id>ossrh</id>
      <username>your_login_on_sonatype</username>
      <password>{encrypted_password_for_sonatype}</password>
    </server>
    <server>
      <id>yor_GPG_key_eg_ABCD012</id>
      <passphrase>{encrypted_password_for_pgp_key}</passphrase>
    </server>
````
  
  Info: as encrypted password only the text inside "{}" will be used, that's why you can also add some comment message for the password field, e.g. 
  
  ````
  <passphrase>Encrypted password with comment, password = {COQLCE6DU6GtcS5P=}</passphrase>`
  ````

* Setup profile for GPG signing:

````
    <profile>
      <activation>
        <activeByDefault>true</activeByDefault>
      </activation>
      <properties>
        <gpg.keyname>yor_GPG_key_eg_ABCD012</gpg.keyname>
      </properties>
    </profile>
````


6. Configure gpg plugin in your project
* file -> pom.xml
```` 
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-gpg-plugin</artifactId>
        <version>1.6</version>
        <executions>
            <execution>
                <id>sign-artifacts</id>
                <phase>verify</phase>
                <goals>
                    <goal>sign</goal>
                </goals>
                <configuration>
                    <keyname>${gpg.keyname}</keyname>
                    <passphraseServerId>${gpg.keyname}</passphraseServerId>
                </configuration>
            </execution>
        </executions>
      </plugin>
````
