# Receive distributed validator keyshares

## GPG Key Exchange

This Public key cryptography process is used to transfer the distributed validator keyshares from Ether.Fi to participating node operators securely.

### Overview of steps

1. Receive EtherFi GPG public key from the programme coordinator
2. Generate your own GPG public-private key pair and the public key fingerprint
3. Export your GPG public key and send it to the programme coordinator via Telegram or Discord
4. Export your GPG <mark style="color:red;">**fingerprint**</mark> and send it to the programme coordinator via <mark style="color:red;">**email**</mark>
5. Receive EtherFi GPG fingerprint from the programme coordinator via email
6. Import EtherFi GPG public key into your GPG keyring and verify the fingerprint with the one you received via email
7. Decrypt the encrypted keyshare files after you receive them from EtherFi

### Receive EtherFi public key

You will receive this `.asc` file from EtherFi's programme coordinator via Telegram or Discord after you have been onboarded onto a testnet or mainnet cohort.

### Generate your own GPG key pair&#x20;

```
gpg --full-generate-key
```

* Choose the key type (e.g., RSA and RSA).
* Set the key size (e.g., 4096 bits for high security).
* Define the expiration date (or set it to never expire).
* Provide your name and email address when prompted.
* Set a secure passphrase to protect the private key.

### Export your GPG public key

```
gpg --armor --export <your-email@example.com> > my-public-key.asc
```

* This creates an ASCII-armored file called `my-public-key.asc`.
* Copy the contents of this file and send it to the EtherFi coordinator in the Telegram or Discord chat.

### Export your GPG **fingerprint**

```
gpg --fingerprint <your-email@example.com>
```

* You’ll see the fingerprint printed as a series of hex digits. e.g., `B365 BB56 85E2 2A50 540A 12C6 E42C FC8D 577E 83F5`
* Copy this fingerprint and email it to the EtherFi programme coordinator

### Receive EtherFi GPG fingerprint

You will then receive the EtherFi GPG fingerprint as an email response.

### Import EtherFi GPG public key & verify fingerprint

* Import EtherFi GPG public key into your GPG keyring

```
gpg --import <etherfi-public-key.asc>
```

* Verify the fingerprint using the email sent to you

```
gpg --fingerprint <etherfi-email@example.com>
```

### Decrypt encrypted keyshare files

After receiving the encrypted keyshare files from EtherFi, move them into the same machine that you generated your GPG key pairs in and decrypt them.

```
gpg --decrypt <encrypted-file.gpg> > decrypted-file.zip
```
