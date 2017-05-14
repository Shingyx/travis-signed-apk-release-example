# travis-signed-apk-release-example

## Description

This is a sample project which demonstrates automatically creating signed APKs as GitHub releases through Travis CI using keystore files inside an encrypted tarball.

To build the signed APK of this project, it requires the files `keystore.properties` and `test-keystore.jks` at the project's root directory. `keystore.properties` contains details about the `test-keystore.jks` keystore and is used by the app's `build.gradle` file to sign the release APK.

Note that `keystore.properties` and `test-keystore.jks` are not committed to the repository. This is because they are zipped inside a tarball which is encrypted using Travis's public key assigned to this repository. The encrypted tarball is represented by the file `archive.tar.gz.enc`.

For details on how to encrypt files for only Travis CI to access, see https://docs.travis-ci.com/user/encrypting-files


## Building this project locally

Make a `keystore.properties` file with the following content:
```
storePassword=store-pass
keyPassword=key-pass
keyAlias=alias
storeFile=test-keystore.jks
``` 

Create a `test-keystore.jks` keystore by running the following command:
```
keytool -genkey -keystore test-keystore.jks -keyalg RSA -keysize 2048 -validity 10000 -alias alias -storepass store-pass -keypass key-pass -dname "CN=a" -noprompt
# keytool is inside "[...]/Android/Android Studio/jre/bin" and "$JAVA_HOME/bin"
# there are many variations of this command that also generate a valid test-keystore.jks
```

Run `./gradlew build`
