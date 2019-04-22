# Install Vault CLI

You first may need to `sudo yum install unzip` if you don't have unzip installed.

# Linux
```
curl -o /tmp/vault_1.1.1_linux_amd64.zip https://releases.hashicorp.com/vault/1.1.1/vault_1.1.1_linux_amd64.zip
unzip /tmp/vault_1.1.1_linux_amd64.zip -d /tmp
sudo cp /tmp/vault /usr/bin/
```

# Mac
```
curl -o /tmp/vault_1.1.1_darwin_amd64.zip https://releases.hashicorp.com/vault/1.1.1/vault_1.1.1_darwin_amd64.zip
unzip /tmp/vault_1.1.1_darwin_amd64.zip -d /tmp
sudo cp /tmp/vault /usr/bin/
```

# Install Vault
```
oc new-project vault
oc adm policy add-scc-to-user anyuid -z default
oc create configmap vault-config --from-file=vault-config=./vault/vault-config.json
oc create -f ./vault/vault.yaml
oc create route reencrypt vault --port=8200 --service=vault
```
# Initialize Vault
```
export VAULT_ADDR=https://`oc get route | grep -m1 vault | awk '{print $2}'`
vault operator init -tls-skip-verify -key-shares=1 -key-threshold=1
```
Save the generated key and token. 

# Unseal Vault.
 
You have to repeat this step every time you start vault. 

Don't try to automate this step, this is manual by design. 

You can make the initial seal stronger by increasing the number of keys. 

We will assume that the KEYS environment variable contains the key necessary to unseal the vault and that ROOT_TOKEN contains the root token.

For example:

`export KEYS=tjgv5s7M4CtMeUz92dU9jV3EudPawgNz6euEnciZoFs=`


`export ROOT_TOKEN=1487cceb-f05d-63be-3e24-d08e429c760c`



```
vault operator unseal -tls-skip-verify $KEYS
```
