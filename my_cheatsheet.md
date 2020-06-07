# Misc

### GDB as Hex (Offset)-Calculator:
gdb --> print 0x1234 - 0x4321 --> <decimal result>

### Special Char in Terminal
press ctrl+shift+u --> enter unicode value --> enter

### Linux default filedescriptors
0 = stdin, 1=stdout, 2=stderr
Can be found at */proc/\<pid\>/fd/*

### Linux use script substitution in command
./some_program $(*put any command, e.g. perl, here*)

### xxd
xxd data.txt > data.hex --> creates hexdump of data.txt
xxd -r data.hex --> reverses the operation

### Remove Github Secrets
Use (bfg Repo Cleaner)[http://rtyley.github.io/bfg-repo-cleaner/] with command
bfg --delete-files *File*
and after that push repo (maybe need to use --force switch)

### Add 20 GB to Qemu Hard drive
qemu-img resize *File*.qcow2 +20G

# Reverse Engineering
(Most of this comes from the [Zero2Automate](https://courses.zero2auto.com) course; will add some things in later times)

## Identify Encryption
- Look for WinAPI functions, e.g. WinCrypt API (CryptCreateHash, CryptEncrypt/Decrypt); look for constant parameter *AlgID* => usually obfuscated
- Look for constants that correspond to encryption algorithms (Substitution boxes, magic numbers, values used in math ops) => automated by tools like KANAL
- Look for code flow; needs to have experience to determine the flow, hardest option

### RC4
- Used by: Dridex, IcedID
- No algo specific constants
- Flow of KSA, PRGA and XOR stage can be recognized (e.g. key size compared to 256 /0x100)
### AES
- Used by: PandaBanker, Trickbot
- Look for substitution box
- Relationship between key size and number of rounds
- Implementation specific magic number (from [repo](https://github.com/Yara-Rules/rules/blob/master/crypto/crypto_signatures.yar)):
{% highlight javascript linenos %}
rule RijnDael_AES
{	meta:
		author = "_pusher_"
		description = "RijnDael AES"
		date = "2016-06"
	strings:
		$c0 = { A5 63 63 C6 84 7C 7C F8 }
	condition:
		$c0
}
{% endhighlight %}

### 3DES
- Used by: Turla PowerShell Loader
- Often natively available and therefore used
-Usage in Powershell can be identified by looking for standard functions/API calls

### Serpent
- Used by ISFB
- Ranked 2nd after Rijndael in AES contest
- Substitution boxes, XOR stages => look at code flow

### Salsa20
- Used by: REvil, GandCrab v4.0
- closely related to ChaCha20
- uses a 8 byte nonce, bitwise addition 
### Mysti-1
- Used by: Turla Outlook Backdoor
- uses a Feistel network
- 128-bit key size that operates on 64-bit blocks
- recursive code flow

### RSA
- Used by: Phobos, ISFB, WannaCry
- key generation and encryption/decryption stages can be identified by code flow and relatiosnhip of key
- many different implementations
- look for hybrid encryption (combination of Symmetric & Asymmetric encryption)

### ECDH
- Used by: Petya
- Very similar to classic Diffie-Hellman
- Uses elliptic curve properties
- Generates Public/Private key pair (like RSA) and passes it to decrypt/encrypt functions

## Identify Compression
- Provides smaller size to payloads and some more obfuscation to code
- Look for API calls related to Compression, e.g. *RTLDecompressBuffer* and its *CompressionFormat* parameter
- Look for constants (magic numbers). These are either used for the math ops or as headers to data to provide additional data => Automated by tools like *signsrch*

### APLIB
- Used by: ISFB
- Uses magic number *AP32*
### ZLIB
- Used by: Obscene Trojan
- wraps the deflated data with a header and trailer to provide extra functionality (e.g. error checking)

### LZMA
- Used by: Angler Exploit Kit Payload
- related to LZ77
- used to hide Adobe Flash payload by Angler

### LZW
- Used by: Reaver
- Also used in Unix file compression and GIFs

## Identify Hashing
- look for *WinCryptAPI* functions, e.g. *CryptCreateHash* or *BCryptCreateHash*
- often uses hardcoded values to output correct data => KANAL & signsrch can be used to look for them
### MD5
- Used by: Dridex
- not cryptographically secure (anymore)
- can be used to creat unique values, e.g. in Dridex used to create BotIDs
- Hash length: 128-bit
### SHA-1
- Used by: FIN8
- used very often in legitimate and malicious software
- Hash length: 160-bit

### CRC
- Used by: Parallax RAT, Netwalker
- bit inversion
