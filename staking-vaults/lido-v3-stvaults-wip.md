# Lido v3 stVaults (WIP)

## Installing Dependencies

Remove the incorrect `yarn` package

```
sudo apt remove -y nodejs
```

Install the correct `yarn` package.

```
nvm install 20
nvm use 20
nvm alias default 20
node --version
```

Enable Corepack under `Node 20` and activate `yarn`.

```
corepack enable
corepack prepare yarn@stable --activate
hash -r
which yarn
yarn --version
```

**Expected:** `which yarn` should point to a Corepack-managed path and `yarn --version` should return a normal Yarn version (e.g., `1.22.x` or `3.x/4.x`), not error.

Reload your shell startup file.

```
source ~/.bashrc
```

Install other dependencies for running stVaults.

```
cd ~/lido-staking-vault-cli
yarn install
```

## Configure Environment Variables

Create a `.env` file under the `lido-staking-vault-cli` folder.

```
nano .env
```

Use this template and fill in your actual environment variables.

```
# Network Configuration (Required)
CHAIN_ID=560048
CL_URL=https://your-consensus-layer-endpoint
EL_URL=https://your-execution-layer-endpoint

# Contract Deployment Configuration (Required)
DEPLOYED=deployed-hoodi-vaults.json

# Wallet Configuration (Choose one method)
# use private key
PRIVATE_KEY=0x1234567890abcdef...

# OR use encrypted account file
ACCOUNT_FILE=wallets/account.json
ACCOUNT_FILE_PASSWORD=your_secure_password

# OR use WalletConnect
# WALLET_CONNECT_PROJECT_ID is NOT a secret. It is a public identifier
# of the application that uses WalletConnect.
WALLET_CONNECT_PROJECT_ID=ee928c025792b10a6daa97d85328c433
```

