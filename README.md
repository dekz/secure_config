# Secure Config
Secure data and configuration is a very important step when setting up your project. As always, when adding additional security,
you must make tradeoffs.

This tool provides the building blocks to store sensitive data securely, without making too many assumptions

### Scenario 1
Store encrypted localy in the repository

```sh
# Store (local dev)
cat .env.staging | ./secure_config encrypt > .env.staging.secure

git add .env.staging.secure
# ...

# Decrypt (build or runtime)
cat .env.staging.secure | ./secure_config decrypt > .env.staging
```

### Scenario 2
Store encrypted remotely in an s3 bucket


```sh
# Store (local dev)
cat .env.staging | ./secure_config encrypt | ENVIRONMENT=staging APP=my-app ./secure_config push-secure-config .env.staging

# Retrieve (build or run time)
ENVIRONMENT=staging APP=my-app ./secure_config fetch-secure-config .env.staging | ./secure_config decrypt > .env.staging
```

### Scenario 3
Store unencrypted in a secure config repository in internal git.

Use a combination of the above during build/runtime


## File format
* One line

```
SECURE_CONFIG_V1|IV|ENCRYPTED_KEY|DATA
```

## Examples

Fetching decrypting and exporting each line to ENVIRONMENT VARIABLES

```
data=$(./secure_config fetch-secure-config | ./secure_config decrypt)
export $(echo $data | xargs)
```


Downloading a piece of data (such as a key)

```
./secure_config fetch-secure-config file.key | ./secure_config decrypt > file.key
```
