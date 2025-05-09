# Depositing 32 ETH into your validator

## Warnings

* Follow the instructions of this section closely. We will be using Metamask to connect to the Ethereum Launchpad website to complete the deposit process
* **DO NOT TRANSFER ETH DIRECTLY INTO THE BEACON DEPOSIT CONTRACT ADDRESS**
* Spare no effort in checking (and double checking) all details before making the transaction - _e.g. public keys of validator signing keys and the deposit data file matches, verify smart contract address of the deposit contract from multiple sources_
* Make sure both your execution and consensus layer clients are fully synced and running without errors before you make the deposit

## Whitelist your wallet with ETHStaker&#x20;

1. First, go to [https://holesky-faucet.pk910.de/](https://holesky-faucet.pk910.de/) to try mining the full 32 testnet ETH.&#x20;
   * If successful, skip to the next section
   * If not, just mine a small amount and continue on the following steps.
2. Join the discord server here - [https://discord.gg/ethstaker](https://discord.gg/ethstaker)
3. Join the #cheap-holesky-validator channel
4. Type “/cheap-holesky-deposit `<your ETH address>` ” in the text box and press enter
5.  Click on the link generated (ie. the [**Signer.is**](http://signer.is) text shown below)&#x20;

    <figure><img src="../.gitbook/assets/image (144).png" alt=""><figcaption></figcaption></figure>
6. Connect your Metamask wallet and sign the message
7. Copy the URL and paste it in the Enter Signature box.

## Reviewing mandatory disclaimers

Go to [https://holesky.launchpad.ethstaker.cc/](https://holesky.launchpad.ethstaker.cc/) on your browser and click on **"Become a validator"**

<figure><img src="../.gitbook/assets/image (41).png" alt=""><figcaption></figcaption></figure>

Click through and read all disclaimers carefully.

<figure><img src="../.gitbook/assets/image (42).png" alt=""><figcaption></figcaption></figure>

Continue clicking through until you reach the **"Upload deposit data"** section. Don't worry about the "Choose client" and "Generate keys" sections as you would already have dealt with those if you followed this guide in order.&#x20;

<figure><img src="../.gitbook/assets/image (43).png" alt=""><figcaption></figcaption></figure>

## Making the deposit

Upload your `deposit_data-<timestamp>.json` file you generated during the earlier section of the guide into the box above and click through until you see the following page.

<figure><img src="../.gitbook/assets/image (45).png" alt=""><figcaption></figcaption></figure>

Here you will need to check all disclaimer boxes before you can proceed. _**But before that,** make sure you are not being phished by_ _clicking on **"Learn here how to do it safely"** as highlighted above._

You will be brought to a page where you can reveal the address of the Ethereum Beacon Deposit Contract. Check that the address displayed in your wallet is the same before you execute the transaction approval.&#x20;

Additionally, you can also check the address against the following sources:

1. [Etherscan](https://etherscan.io/address/0x00000000219ab540356cBB839Cbe05303d7705Fa)
2. [Consensys](https://consensys.net/blog/news/eth2-phase-0-deposit-contract-address/)
3. [Ethereum Foundation](https://ethereum.org/en/staking/deposit-contract/)

{% hint style="info" %}
The checks you perform above will point you to the deposit contract address for Mainnet. The deposit contract address on Holesky is:

**`0x4242424242424242424242424242424242424242`**
{% endhint %}

Once you are ready, click on **"Send Deposit"** and follow the instructions on Metamask or your other wallets.

<figure><img src="../.gitbook/assets/image (46).png" alt=""><figcaption></figcaption></figure>

Once your transaction goes through, you will be able to click on the external links highlighted in yellow to track the activation progress and performance of your validator. Bookmark these links so that you can come back to them easily.

<figure><img src="../.gitbook/assets/image (47).png" alt=""><figcaption></figcaption></figure>

Don't panic if it doesn't show up initially! Give it some time to update itself :)&#x20;

<figure><img src="../.gitbook/assets/image (48).png" alt=""><figcaption></figcaption></figure>

**Congratulations! You are now a proud contributor to the decentralisation of the Ethereum network** :vulcan:

