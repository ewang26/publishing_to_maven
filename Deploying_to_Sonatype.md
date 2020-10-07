# Deploying and Releasing Components

In order to deploy your components to OSSRH and release them to the Central Repository, you should use the Nexus Staging Maven Plugin.

## 1. Nexus Staging Maven Plugin for Deployment and Release
Add the following plugin to your Maven pom.xml:
```

<plugin>
  <groupId>org.sonatype.plugins</groupId>
  <artifactId>nexus-staging-maven-plugin</artifactId>
  <version>1.6.7</version>
  <extensions>true</extensions>
  <configuration>
     <serverId>ossrh</serverId>
     <nexusUrl>https://oss.sonatype.org/</nexusUrl>
     <autoReleaseAfterClose>true</autoReleaseAfterClose>
  </configuration>
</plugin>

```

You will also need to create a 'settings.xml' file under your home directory '~\.m2':
```
<settings xmlns="http://maven.apache.org/settings/1.0.0">
    <servers>
        <server>
            <id>ossrh</id>
			<!-- update the below with real username and password -->
            <username>username</username>
            <password>password</password>
        </server>
    </servers>
    <profiles>
        <profile>
            <id>ossrh</id>
            <properties>
                <gpg.executable>gpg</gpg.executable>
				<!-- update here -->
                <gpg.passphrase>passphrase</gpg.passphrase>
            </properties>
        </profile>
    </profiles>
    <activeProfiles>
        <activeProfile>ossrh</activeProfile>
    </activeProfiles>
</settings>
```


If your version is a release version, you can just run a deployment to OSSRH and the Central Repository using:
```
mvn clean deploy
```
## 2. Performing a Release Deployment
You must edit your 'version' in all of your POM files to use release versions, which means that they cannot end in '-SNAPSHOT'.

Changing the versions for your project can be performed using the Maven versions plugin.
```
mvn versions:set -DnewVersion=1.2.3
```
After updating the version, you can perform the deployment using the 'release' profile with:
```
mvn clean deploy -P release
```

## 3. Performing a Release Development with the Maven Release Plugin (optional)
You must edit your 'pom.xml' file to include the following plugin since it is necessary when pushing to the Sonatype repository:
```
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
```

Finally, with all these steps completed and the Sourcecode Management (SCM) connection correctlty configured, you can perform a release deployment ot OSSRH using the following command:
```
mvn release:clean release:prepare
```
and then immediately entering the command:
```
mvn release:perform
```
Due to the Nexus Staging Maven Plugin, this command will deploy to OSSRH and the Maven Central Repository.
