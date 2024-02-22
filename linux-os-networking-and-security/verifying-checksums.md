# Verifying checksums

## Concept

As a best practice, we should always verify the checksum of all downloaded binary and zipped files. This ensures that the downloaded files are indeed the ones we intended to download - i.e. have not been corrupted or tampered with since it was originally created.

Network and device level security are powerless against this attack vector because you are basically inviting these tampered files into your system if you do not perform checksum verification before running these files.

## How to verify checksums

Each downloadable file comes with it's own checksum (see below).

<figure><img src="../.gitbook/assets/image (107).png" alt=""><figcaption></figcaption></figure>

Verify its checksum after downloading the file.

```sh
echo "<checksum> <file_name>" | <checksum_method> --check
```

**Breakdown:**

* \<checksum> refers to the long hexadecimal string in the screenshot above
* \<checksum\_method> refers to the the hashing algorithm used (in lower case) - e.g. MD5, SHA256, SHA512&#x20;

**Example:**

```sh
echo "444bf523e0db9c23243b365e717b5b15 nethermind-1.20.1-9f39c0c7-linux-x64.zip" | md5sum --check
```

_**Expected output:** Verify output of the checksum verification_

```
nethermind-1.20.1-9f39c0c7-linux-x64.zip: OK
```
