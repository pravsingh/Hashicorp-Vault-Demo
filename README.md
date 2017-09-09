# Hashicorp-Vault-Demo
Hashicorp's Vault - Encryption As A Service Demo

# Create Container

Run the below command to launch Vault server in development mode:

```bash
docker run --name=pravsingh-vault --cap-add=IPC_LOCK -e 'VAULT_DEV_ROOT_TOKEN_ID=pravsingh-vault-token' -e 'VAULT_DEV_LISTEN_ADDRESS=127.0.0.1:8200' -p 8200:8200 vault
```

In above terminal, the server will spit out the VAULT_ADDR and "Unseal Key" and "Root Token".
Note them down as they might be useful for advanced features.
However for us, we have fixed the token to 'pravsingh-vault-token' which server will use to initialize, the same will be used in following steps. In case you don't provide VAULT_DEV_ROOT_TOKEN_ID variable, the Vault generates random UUID as Root Token for us.

# Setting up the Client
We can invoke Vault commands from our machine/laptop or from inside the above docker container.
We will leverage container itself to test out Vault encryption APIs and hence lets launch interactive terminal (-it option) connecting to the container.

```bash
docker exec -it pravsingh-vault sh
```

The above will start a command prompt. Execute following on the prompt:

```bash
export VAULT_ADDR='http://127.0.0.1:8200'
echo "pravsingh-vault-token" > ~/.vault-token 
```

# enable Transit backend for Encryption service

```bash
vault mount transit
```

# setup named encryption key

```bash
vault write -f transit/keys/foo
# verify if things are as expected
vault read transit/keys/foo
```

# perform encryption operation

## encrypt

```bash
echo -n "the quick brown fox" | base64 | vault write transit/encrypt/foo plaintext=-
```

note down the ciphertext from above command's output

## decrypt

pass the ciphertext (noted above) to below

```bash
vault write transit/decrypt/foo ciphertext=vault:v1:VGMchNf4bZZWmzhwDYhQsIqqtIpUzzAxQJ7j0+K/uKAlmyT7xRqYM0s2CyoQn7k=
```

note down the plaintext from above output. Note that its Base64 bit encoded.
Decode and check the actual plaintext:

```bash
echo "dGhlIHF1aWNrIGJyb3duIGZveA==" | base64 -d
```

The output from above command must be "the quick brown fox".
If so the demo is successful!


congratulations!
