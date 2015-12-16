Encrypted Config Value
======================
This repository provides tooling for encrypting certain configuration parameter values in dropwizard apps. This defends against accidental leaks of sensitive information such as copy/pasting a config file - unlike jetty obsfucated passwords, one would also have to share the encryption key to actually reveal the sensitive information.


encrypted-config-value-bundle
-----------------------------
A dropwizard bundle which provides a way of using encrypted values in your dropwizard configs (via a variable substitutor) and utility commands.

The bundle sets the `ConfigurationSourceProvider` to one capable of parsing encrypted values specified as variables.

The bundle adds the following commands
 - `encrypt-config-value -v <value> [-k <keyfile>]` for encrypting values. In the case of non-symmetric algorithms (e.g. RSA) specify the private key.
 - `generate-random-key -a <algorithm> [-f <keyfile>]` for generating random keys with the specified algorithm. In the case of non-symmetric algorithms (e.g. RSA) the private key will have a .private extension.
 
Currently supported algorithms:
 - AES: (AES/GCM/PKCS5Padding) with random IV
 - RSA

###Example Usage
To use in your app, just add the bundle.

```java
public final class Main extends Application<MyApplicationConfig> {
    @Override
    public void initialize(Bootstrap<MyApplicationConfig> bootstrap) {
        ...
        bootstrap.addBundle(new EncryptedConfigValueBundle());
    }
    ...
}
```
 
then

```console
my-application$ ./bin/my-dropwizard-app generate-random-key -a AES
Wrote key to var/conf/encrypted-config-value.key
my-application$ ./bin/my-dropwizard-app encrypt-config-value -v topSecretPassword
enc:V92jePHsFbT0PxdJoer+oA== 
```

now use the encrypted value in your config file (as a variable):

```yaml
auth:
   username: my-user
   password: ${enc:INNv4cGkVF45MLWZhgVZdIsgQ4zKvbMoJ978Es3MIKgrtz5eeTuOCLM1vPbQm97ejz2EK6M=}
```

License
-------
This repository is made available under the [Apache 2.0 License](http://www.apache.org/licenses/LICENSE-2.0).

