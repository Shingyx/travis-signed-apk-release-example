# travis-signed-apk-release-example

[![Build Status](https://travis-ci.org/Shingyx/signed-apk-release-test.svg?branch=master)](https://travis-ci.org/Shingyx/signed-apk-release-test)


## Description

This is a sample project which demonstrates automatically creating signed APKs as GitHub releases through Travis CI using an encrypted tarball containing keystore related files. This means that Travis CI can use your keystores to sign APKs without having to commit plaintext passwords and a raw keystore file to the repository.

To build the signed APK of this project, it requires the files `keystore.properties` and `test-keystore.jks` at the project's root directory. `keystore.properties` contains details about the `test-keystore.jks` keystore and is used by the app's `build.gradle` file to sign the release APK.

Note that `keystore.properties` and `test-keystore.jks` are not committed to the repository. This is because they are zipped inside a tarball which is encrypted using Travis's public key assigned to this repository. The encrypted tarball is represented by the file `archive.tar.gz.enc`.

For details on how to encrypt files for only Travis CI to access, see https://docs.travis-ci.com/user/encrypting-files/

For details on deploying GitHub releases using Travis CI, see https://docs.travis-ci.com/user/deployment/releases/


## Building the project locally

The way I built the project locally is as follows:

1. Make a `keystore.properties` file with the following content:
```
storePassword=store-pass
keyPassword=key-pass
keyAlias=alias
storeFile=test-keystore.jks
``` 

2. Create a `test-keystore.jks` keystore by running the following command:
```
keytool -genkey -keystore test-keystore.jks -keyalg RSA -keysize 2048 -validity 10000 -alias alias -storepass store-pass -keypass key-pass -dname "CN=a" -noprompt
# keytool is inside "[...]/Android/Android Studio/jre/bin" and "$JAVA_HOME/bin"
# there are many variations of this command that also generate a valid test-keystore.jks
```

3. Run `./gradlew build`

Try experimenting with different passwords, aliases, and keystore filenames to learn how to apply this technique to other projects.

Take care when applying this to a public repository - even though your keystore information is encrypted, it is still publicly available. You may want to strip down the keystore file to only contain a single alias which is only used for the one specific project.
