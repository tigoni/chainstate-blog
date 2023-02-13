---

title: "Cardano Address Derivation"
date: 2023-02-12 23:39:00 -0000

---

One of the essential components of blockchains is the use of addresses. With addresses, we are able to transfer value from one party to another. This value could be in many forms including cryptocurrency, NFTs, or any specific metadata. This is a brief guide on how addresses are generally created on the Cardano blockchain but it's worth noting that the process of address creation is largely similar across all blockchains.

When you create a wallet, the typical process starts with a random number being generated and from it, a seed phrase of mnemonic is generated. The randomness of the number is extremely huge considering that it is a number whose upper bound is 2<sup>256</sup> bits. To provide some context to how large it is, we need to understand that the number in decimal is 10<sup>77</sup>. The estimated total number of atoms in the known universe is 10<sup>80</sup>. 

With the number generated, a checksum value is calculated to ensure correctness. The number is then combined with the checksum and the binary representation is broken down into 11-bit segments and converted to decimal format. The decimal numbers are used to look up the corresponding word in a list known as the [BIP-39 word list]([https://github.com/bitcoin/bips/blob/master/bip-0039/english.txt](https://github.com/bitcoin/bips/blob/master/bip-0039/english.txt)).  

The next step is getting the root key from the 12, 15, or 24-word seed phrase. The hex representation of the random number from the previous step (with the checksum stripped off), is mixed with a user-specified password. This password is optional and by default, some wallets use an empty string. 

The password and the random hex string are used as inputs to a hashing function that uses an algorithm called `PBKDF2WithHMacCSHA512` (PB Key Derivation Format 2 With HMac SHA-512). The function runs the inputs through a specified number of iterations and outputs 96 bytes of data. This is the root key that will be used to derive child keys.

Next, a derivation function is run on the root key to get all child keys. Key derivation provides for keys to be set to the required format for use and for this purpose, paths are used. Cardano uses the derivation paths to describe the structure of the keys, such that they can be accurately recreated in the future. 

The format of the paths used is:

`root/   H(PURPOSE)/   H/(COIN_TYPE)   /H(ACCOUNT)   /S(CHANGE)   /S(INDEX)` 

With the example below, the coin type is set to 1815 which means the key is meant to hold Ada and the account, which is typically 0, the change path specifies the internal, external, or stake key. The index of the key count goes up as more keys are generated. 

`root / 1852' /1815' /0' /0 /0` 

After the child private keys are derived, the public keys are created by applying a one-way function to the private key. This means that a public key can be derived from a private key but not the other way around.  

Finally, to get the address, the public key is hashed with the functions; `SHA-3` and `blake2S`. The resulting hash is an address that can be used to receive funds and assets.