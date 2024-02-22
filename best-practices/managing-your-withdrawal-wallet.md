# Managing your withdrawal wallet

1. Generate your validator keys on an air-gapped machine
2. Do not store your mnemonic word lists of both your validator keys and withdrawal wallets on any online machines (e.g. validator node, external signer)
3. Do not generate your validator keys without setting your withdrawal address. Additional, set your withdrawal address to a multi-sig wallet - e.g. [Safe wallet](https://safe.global/)
4. Build the validator key generation tool from source as detailed in this guide. Otherwise, make sure you verify the checksum of the downloaded file if you want to use the binaries directly
5. Triple check the address of the ETH beacon deposit contract before approving the transaction
