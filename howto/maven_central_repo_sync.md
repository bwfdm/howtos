## How to make a release and sync it with the maven central repository

Main guide:
* https://central.sonatype.org/pages/ossrh-guide.html

Prepare pom.xml
* https://central.sonatype.org/pages/apache-maven.html
* see also [PGP signatures and maven](PGP_signatures_maven.md)

How to make release (the full manual, not all commands are used in our projects):
* https://central.sonatype.org/pages/apache-maven.html

## Commands to make release

````
mvn clean deploy
mvn nexus-staging:release
````

## Maven repositories

* Deploy snapshot artifacts into repository -> https://oss.sonatype.org/content/repositories/snapshots
* Deploy release artifacts into the staging repository -> https://oss.sonatype.org/service/local/staging/deploy/maven2
* Download snapshot and release artifacts from group -> https://oss.sonatype.org/content/groups/public
* Download snapshot, release and staged artifacts from staging group -> https://oss.sonatype.org/content/groups/staging

Find "bwFDM" on maven central repository
* https://mvnrepository.com/search?q=bwfdm


## Example: config of "pom.xml" for "DSpace Connector" (all needed plugins)

````
<project xmlns="http://maven.apache.org/POM/4.0.0"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
   
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.github.bwfdm</groupId>
    <artifactId>dspace-connector</artifactId>
    <version>0.1.0</version>
    <packaging>jar</packaging>

    <name>DSpace Connector</name>
    <description>
        Library to publish files and/or metadata to DSpace-based publication repositories.
    </description>
    <url>https://github.com/bwfdm/dspace-connector/</url>

    <licenses>
        <license>
            <name>MIT License</name>
            <url>http://www.opensource.org/licenses/mit-license.php</url>
            <distribution>repo</distribution>
        </license>
    </licenses>

    <scm>
        <connection>scm:git:https://github.com/bwfdm/dspace-connector.git</connection>
        <developerConnection>scm:git:git@github.com:bwfdm/dspace-connector.git</developerConnection>
        <url>https://github.com/bwfdm/dspace-connector/</url>
    </scm>

    <developers>
        <developer>
            <name>Name Surname of 1. developer</name>
            <url>https://github.com/1-developer-login</url>
            <organization>University_Name, Institute_Name</organization>
            <organizationUrl>University_or_Institute_URL</organizationUrl>
        </developer>
        <!-- 
          You can remove this part, if there is only 1 developer 
        -->  
        <developer>
            <name>Name Surname of 2. developer</name>
            <url>https://github.com/2-developer-login</url>
            <organization>University_Name, Institute_Name</organization>
            <organizationUrl>University_or_Institute_URL</organizationUrl>
        </developer>
    </developers>

    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <java.version>1.7</java.version>
        <maven.compiler.source>${java.version}</maven.compiler.source>
        <maven.compiler.target>${java.version}</maven.compiler.target>
    </properties>
    
    <!-- Prepare pom for Maven Central Repo
         more here: https://central.sonatype.org/pages/apache-maven.html
     -->
     
    <distributionManagement>
        <snapshotRepository>
            <id>ossrh</id>
            <url>https://oss.sonatype.org/content/repositories/snapshots</url>
        </snapshotRepository>
        <repository>
            <id>ossrh</id>
            <url>https://oss.sonatype.org/service/local/staging/deploy/maven2/</url>
        </repository>
    </distributionManagement>


    <build>
        <plugins>
            <!-- Sonatype plugin -->
            <plugin>
                <groupId>org.sonatype.plugins</groupId>
                <artifactId>nexus-staging-maven-plugin</artifactId>
                <version>1.6.7</version>
                <extensions>true</extensions>
                <configuration>
                    <serverId>ossrh</serverId>
                    <nexusUrl>https://oss.sonatype.org/</nexusUrl>
                    <!-- Disable autorelease -->
                    <autoReleaseAfterClose>false</autoReleaseAfterClose>
                </configuration>
            </plugin>
            
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-source-plugin</artifactId>
                <version>2.2.1</version>
                <executions>
                    <execution>
                        <id>attach-sources</id>
                        <goals>
                            <goal>jar-no-fork</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
            
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-javadoc-plugin</artifactId>
                <version>2.9.1</version>
                <executions>
                    <execution>
                        <id>attach-javadocs</id>
                        <goals>
                            <goal>jar</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
            
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

            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-release-plugin</artifactId>
                <version>2.5.3</version>
                <configuration>
                    <autoVersionSubmodules>true</autoVersionSubmodules>
                    <useReleaseProfile>false</useReleaseProfile>
                    <releaseProfiles>release</releaseProfiles>
                    <goals>deploy</goals>
                </configuration>
            </plugin>

            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.0</version>
                <configuration>
                    <source>${java.version}</source>
                    <target>${java.version}</target>
                </configuration>
            </plugin>
            
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-jar-plugin</artifactId>
                <version>2.4</version>
                <configuration>
                    <archive>
                        <manifest>
                            <addDefaultImplementationEntries>
                                true
                            </addDefaultImplementationEntries>
                            <addDefaultSpecificationEntries>
                                true
                            </addDefaultSpecificationEntries>
                        </manifest>
                    </archive>
                </configuration>
            </plugin>
                    
        </plugins>
    </build>
    
    <dependencies>
         
         <!-- Put all dependencies here -->
         
    </dependencies>  

</project>       
````
