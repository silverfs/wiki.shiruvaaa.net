


# An Introduction to Cryptography

In this research document, I will discuss some cryptographic principles and practices to understand encryption. I will be demonstrating some examples by making use of the [Cryptography library](https://cryptography.io) in Python.

Firstly, I would like to discuss the most standard encryption algorithms to this date: AES, RSA, ECC, and SHA (for hashing). I will include the inner workings and provide examples with best practices.



## AES 

[AES](https://www.geeksforgeeks.org/advanced-encryption-standard-aes/) stands for *Advanced Encryption Standard* and is a symmetric encryption algorithm, meaning the same key is used for both encryption and decryption. It is a secure and pretty fast encryption algorithm, if implemented correctly. 

**How it works**

AES is a block cipher, which is an algorithm that operates on fixed-length groups of bits, called blocks. The key-size of AES can be 128/192/256 bits, but it encrypts data using 128 bit-sized blocks, which is equivalent to 16 bytes. This means that it can use these 3 lengths of bits as an input, and outputs encrypted cipher text with a bit-length of 16 bytes (128 bits). 

Why exactly is encrypting using a bit length of 256 better in terms of security, but slower than a 128-bit length? Let's break that down.

The security of AES encryption depends on the size of the key space, which is determined by the bit length of the key. As I mentioned before, the key-size of AES can be 128, 192, or 256 bits long. A 128-bit key provides 2¹²⁸ possible combinations, while a 256-bit key provides 2²⁵⁶ possible combinations, which, in theory, makes it stronger to e.g. brute-force attacks. 
The reason why it takes longer to encrypt/de-crypt a 256-bit length key, is because a longer key length needs more complex mathematical operations, so it takes longer to compute a larger key in comparison to a shorter one. 

### Testing

#### Key Generation

I will elaborate the theory by providing an example written in Python with the Encryption module. We will operate with `hazmat`, the hazardous materials of the cryptography library. In its most basic form, cryptography uses [Fernet](https://cryptography.io/en/latest/fernet/), but to use different kinds of encryption methods, one needs to dive into the hazmat section, as implementations made with these functions are more prone to being implemented wrongly. With that out of the way, let's get started.

```python
from cryptography.hazmat.backends import default_backend
from cryptography.hazmat.primitives import hashes
from cryptography.hazmat.primitives.kdf.pbkdf2 import PBKDF2HMAC
import os


ef generate_aes_key(password):
	salt = os.urandom(16) # Generate random salt
	print("Salt:", salt
	kdf = PBKDF2HMAC(
		algorithm=hashes.SHA256(),
		length=32, # 256 bits (here 32 bytes) for AES-256
		salt=salt,
		iterations=100000, 
		backend=default_backend()
	)
	key = kdf.derive(password.encode())
	return key


# Example
password = "secure_password"
aes_key = generate_aes_key(password)
print("AES Key:", aes_key)
```

Firstly we create a randomly generated salt using 16 bytes as the length. The salt is used as an additional input to increase the security of key derivation to ensure that the derived key is unique, even if the same password is used multiple times. 

Next, we use PBKDF2HMAC (Password-Based Key Derivation Function 2 Hash Message Authentication Code), which is a password-based [Key derivation function](https://en.wikipedia.org/wiki/Key_derivation_function) in combination with the integrity and authenticity assurance of HMAC. PBKDF2HMAC is then used in combination with the SHA-256 hash algorithm to derive a secure AES key from a password.
AES-256 requires a 256-bit key, so a 32-byte key is suitable (16 bytes is equivalent to a 128-bit sized key). The key derivation function is responsible for producing a key of the desired size. 
Finally, `iterations=100000` means that the underlying hash function (SHA-256 in this case) will be applied 100000 times to derive the final key. Multiple iterations slow down key derivation, adding computational expense for attackers attempting brute-force or dictionary attacks, and thus more protected against brute-force or dictionary attacks.


In the example part, PBKDF2 is used with SHA-256 as the hash function to derive a secure AES key from a password.

```bash
> pipenv run python aes.py

Salt: b'\x02\xbc\xb7x\x1c**4O\xf4\xad\xf3\x1c|\xb1\xfa'

pbkdf2hmac key:  b'\x1aTEoNa\x030M\xa3\xf1s,$\x9a\\M\xa8\xbf\xa08J\x9a\xe3\xc5\x00\x19CC\xe4\x0f/'

password:  secure_password

AES Key: b'\x1aTEoNa\x030M\xa3\xf1s,$\x9a\\M\xa8\xbf\xa08J\x9a\xe3\xc5\x00\x19CC\xe4\x0f/'
```



But in this part, we only created a key from the password. We have yet to encrypt something using our generated key. 


#### Message Encryption Using a generated key


In the first part, the `generate_aes_key(password)` is used to derive an AES key from the password as an input. The resulting key is then used in the encryption part that comes next. 

```python
def aes_encrypt(key, plaintext):
	cipher = Cipher(algorithms.AES(key), modes.ECB(), backend=default_backend())
	encryptor = cipher.encryptor()
	ciphertext = encryptor.update(plaintext) + encryptor.finalize()
	return ciphertext
```

In the function above, we'll try to encrypt a message, in this case a plain-text string. In the first line of the function, `cipher` starts with specifying the the AES algorithm through the `Cipher` class with the `key` as an input. This key is in the return line of the key generation function in the first part of our experiment. 

**AES encryption modes**

Next, it specifies in which mode of operation AES should run. In this case, it's 'ECB'. 
As I mentioned before, AES operates on fixed-size blocks of 128 bits of data  (16 bytes). ECB stands for Electronic CodeBook. 

From [_Wikipedia_](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#Electronic_codebook_(ECB)):
> "The simplest (and not to be used anymore) of the encryption modes is the **electronic codebook** (ECB) mode (named after conventional physical [codebooks](https://en.wikipedia.org/wiki/Codebook "Codebook")). The message is divided into blocks, and each block is encrypted separately."


!!!  caution
	Although it is generally not recommended to use this mode of encryption due to its lack of variability and it not being suitable for encrypting data in general any longer, I will use it as a simplified approach to showcase how cipher blocks work. <br>
	The ECB method lacks diffusion (hiding the plaintext statistics by spreading it over a larger area of ciphertext). Because ECB encrypts identical plaintext blocks of data into identical ciphertext blocks as mentioned earlier, it does not hide patterns well, making it not recommended for use in cryptographic protocols.




Writing it down is one thing, but to truly understand, you'll have to experience it for yourself. 

---

So, we'll take a detour for a moment and convert our Python encryption block above to something that shows ECB blocks step by step:

```python
from cryptography.hazmat.primitives.ciphers import Cipher, algorithms, modes
from cryptography.hazmat.backends import default_backend
from cryptography.hazmat.primitives import padding

def aes_encrypt_ecb_debug(key, plaintext):
    cipher = Cipher(algorithms.AES(key), modes.ECB(), backend=default_backend())
    encryptor = cipher.encryptor()

    # Padding the plaintext
    padder = padding.PKCS7(algorithms.AES.block_size).padder()
    padded_plaintext = padder.update(plaintext) + padder.finalize()

    ciphertext_blocks = []

    # Encrypt each block independently
    for i in range(0, len(padded_plaintext), algorithms.AES.block_size):
        block = padded_plaintext[i:i + algorithms.AES.block_size]
        encrypted_block = encryptor.update(block)
        ciphertext_blocks.append(encrypted_block)

        # Print intermediate results
        print(f"Block {i // algorithms.AES.block_size + 1} plaintext: {block.hex()}")
        print(f"Block {i // algorithms.AES.block_size + 1} ciphertext: {encrypted_block.hex()}")
        print()

    return b''.join(ciphertext_blocks)

# Example usage
key = b'Sixteen byte key'  # 128-bit key for AES-128
plaintext = b'Hello. This is plaintext for my ECB AES walkthrough! Lets make our string longer so we can have more blocks. Those sentences were not enough, so let us add one more.'

encrypted_data = aes_encrypt_ecb_debug(key, plaintext)

# Print the final result
print("Final Encrypted Data:", encrypted_data.hex())

```

in the example above, we start with the same start of the function by providing the Cipher algorithm and the encryption mode for AES.


**The use of padding**

Next, we add some padding. Padding, specifically in the context of of encryption, is a process of adding extra bits to a piece of data to make it fit a specific block size. But why should we need it?  

AES in ECB mode operates on fixed-size blocks of data. Let's say we take in 128-bit size blocks for AES, but the provided plaintext cannot divide itself evenly over 128-bit sized blocks, it needs extra padding to make up for the length.

So, If the length of the plaintext is a multiple of the block size, it means that the length can be divided evenly into complete blocks without any remainder. If it's not a multiple, there will be a remainder when dividing by the block size and padding might be needed. 

Let's say the block size is 16 bytes (128 bits). A plaintext length of either, 16, 32, 48, and so on, would be a multiple of the block size. If the plaintext is not a multiple, additional padding ensures that the last block is filled to make it a multiple. In our example above, we use the padding standard scheme PKCS#7. 



!!! info
	The word "plaintext" consists of 9 characters. Typically - assuming ASCII or UTF-8 encoding - each character typically requires 1 byte. 
	In this case, the word "plaintext" would be 9 bytes long. <br>
	In the context of AES with a block siz of 128 bits (16 bytes) is a length of 9 bytes not a multiple of 16. We need additional padding to make up for it. <br>
	PKCS#7 adds bytes equal to the number of bytes needed for padding. For "plaintext", it would look like this: <br>
	`"plaintext\x07\x07\x07\x07\x07\x07\x07"` <br>
	`\x07` represents the number of padding bytes (7 bytes) to make the total length a multiple of 16 bytes. 
	<br>


Padding is not always needed. In cases where padding introduces issues or it needs to be implemented and used in a more simpler way, methods like Ciphertext Stealing (CTS) can be used. CTS is _"a method of using a block cipher mode of operation that allows for processing of messages that are not evenly divisible into blocks without resulting in any expansion of the ciphertext, at the cost of slightly increased complexity"_, which means that it can encrypt a plaintext block without padding the message to a multiple of the block size. So, instead of adding extra padding to fill up the block, this method "steals" some ciphertext from the previous block to complete the last one. 


---

Now that we know that padding is needed, we add it to the script as in our example above.

After that, a for-loop is introduced which iterates over blocks of the padded plaintext using the block size of AES with ECB, which in this case is 16 bytes. 


`block = padded_plaintext[i:i + algorithms.AES.block_size]` extracts a block of data from the padded plaintext. It starts with `i` and includes the next AES block size bytes. 
Next, the current block is encrypted using AES. This, of course, happens block per block. After encryption of the current block, it appends the encrypted block to a list called `ciphertext_blocks`. 

After that, it tries to print the hexadecimal representation of the current plaintext block, and the ciphertext (encrypted) block below it.
It repeats this process until no further blocks are found. 


After the loop ends, `return b''.join(ciphertext_blocks)` is used to concatenate (or simply join them together) into the final encrypted data, which is printed as well. 

At the end, we add the sixteen byte key and a plaintext worthy of having 2 blocks. The output looks somewhat like this:

```bash
> pipenv run python aes_ecb.py
> 
Block 1 plaintext: 48656c6c6f2e205468697320697320706c61696e7465787420666f72206d7920454342204145532077616c6b7468726f75676821204c657473206d616b65206f757220737472696e67206c6f6e67657220736f2077652063616e2068617665206d6f726520626c6f636b732e2054686f73652073656e74656e63657320776572
Block 1 ciphertext: 734326e08f5d4c2927597d492740efb622fe74e49044ce0228cc5353196578067269781e02250a10afbb3807897f1867daba7b3273f30b620bc5df4a5537975cc66c8d47304b3043b553500d6fbc7fe4e9f1d24e4ca73660d3c8a18a4e34c0616b7a3edbfebb0bee6bbe8a7f219c0c7793c625471d92238bec618f659b482e53

Block 2 plaintext: 65206e6f7420656e6f7567682c20736f206c657420757320616464206f6e65206d6f72652e0b0b0b0b0b0b0b0b0b0b0b
Block 2 ciphertext: 79b906b7c49bf211867ee1322ed4c5f19dc302a8ce376db53f10f2335d63cd5f3ab99134759ed851844a9e6a4244d14b

Final Encrypted Data: 734326e08f5d4c2927597d492740efb622fe74e49044ce0228cc5353196578067269781e02250a10afbb3807897f1867daba7b3273f30b620bc5df4a5537975cc66c8d47304b3043b553500d6fbc7fe4e9f1d24e4ca73660d3c8a18a4e34c0616b7a3edbfebb0bee6bbe8a7f219c0c7793c625471d92238bec618f659b482e5379b906b7c49bf211867ee1322ed4c5f19dc302a8ce376db53f10f2335d63cd5f3ab99134759ed851844a9e6a4244d14b

```


Interestingly, Block 2 has a lot of `0b`s in its plaintext. `0b` is hexadecimal, and represents the number 11. this means that 11 bytes of padding were added. The padding in this plaintext is identified as the last 11 bytes, represented as `0b0b0b0b0b0b0b0b0b0b0b`. 
In the encrypted data below it, nothing is left of the appended padding on the plaintext. 

The final encrypted data is shown in a hexadecimal format, and will always be the same. ECB mode does not provide semantic security, and the same plaintext blocks will always produce the same ciphertext blocks, hence the lack of variability explained earlier.



---

### Modes of Operation


Now is a good time to talk about operation modes. As discussed prior in the AES segment, ECB is one such mode, but not secure in the slightest. Taken from the same Wikipedia page: _"The purpose of cipher modes is to mask patterns which exist in encrypted data."_
If ECB is not capable of masking in a secure way, than what modes are? 


CBC (cipher block chaining) is one of the most widely used mode of operation. However, it still has its drawbacks. 2 of them are as follows: the use of sequential encryption and that messages must be padded to a multiple of the block size, just like ECB. 
Like ECB, CBC makes use of sequential encryption, which refers to encrypting data in a linear manner; typically, one block of data at a time. As seen in the examples above, data is typically divided into fixed-sized blocks, and the encryption of one block is dependent on the encryption of the previous block. However, even if ECB and CBC both use sequential encryption, they are fundamentally different. In ECB mode, each block is encrypted with the exact same key, whereas with CBC, each block is [XORed](https://www.pcmag.com/encyclopedia/term/xor) with the ciphertext of the previous block before encryption. This is called a chaining mechanism and should provide better diffusion than not using chaining at all. 

Obviously, the opposite variant is parallel encryption, which encrypts multiple blocks of data simultaneously.
The most obvious bonus point is its speed of encryption. However, it might be more suitable for multi-thread architectures as it needs to exploit parallel processing power. 

Parallel encryption is not necessarily more secure than sequential encryption, if implemented correctly. Both have its advantages and concerns. Parallel encryption for example, may have faster processing times, but might introduce new vulnerabilities because it is less tested than sequential encryption in e.g. CBC mode.

There exist many different modes and variants, with each their own advantages, use-cases, and drawbacks. 


---

## RSA

RSA stands for "Rivest-Shamir-Adleman", which are the 3 last names of the founders of this method of encryption. RSA is a cryptographic system that involves a public and a private key. The public key is used for encryption, and the private key is used for decryption. 

**How it works**


RSA makes use of a private key and a public key. When presenting 2 parties - call them Alice and Bob - each of them has their own private and public keys. The private key is obviously private to its own party, but the public key can be shared to anyone. Unless implemented badly, messages encrypted using the public key can only be decrypted with the private key.


RSA encryption is not typically used to directly encrypt large messages. Encrypting something using a public/private key pair is usually done to confirm the authenticity of the content. You cannot encrypt a lot of information with the RSA encryption technique, but you can encrypt a large message content separately using AES.

So, encrypting something using a public/private key pair like RSA, is commonly employed for ensuring the authenticity and confidentiality of the content. However, due to the computational cost associated with asymmetric encryption algorithms like RSA, they are not as efficient for encrypting large amounts of data directly.












## Conclusion



## Afterword

Honestly speaking, this topic is incredibly difficult to understand, and I am sure I missed a lot of key parts on how certain mechanics work. However, diving too deep into this rabbit hole is not the main purpose of this assignment, and will only worsen my understanding of this topic at this point in time. With that being said, I think this is a very interesting subject, but it requires a significant amount of time in order to research this, as there is a lot to discuss. I hope I provided some entry-level explanations on cryptography in this document.


---
## Sources

- NIST's Cryptographic Hash functions, encryption algorithms, and keys - https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-57pt1r5.pdf (page 40) 
- The concept of AES and its use of operation modes - https://crypto.stackexchange.com/questions/57645/is-using-the-same-iv-in-aes-similar-to-not-using-an-iv-in-the-first-place/57648#57648
- Information about ECB - https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#Electronic_codebook_(ECB)
- Information about CBC - https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#Cipher_block_chaining_(CBC)
- AES/ECB mode with padding and its security- https://stackoverflow.com/questions/10531932/aes-ecb-mode-requires-padding-how-come-we-have-this-aes-ecb-nopadding-then
- Using padding in encryption modes and its usage recommendations - https://crypto.stackexchange.com/questions/71365/why-does-a-padding-block-exist-for-ecb-and-cbc-modes
- Information about CTS - https://en.wikipedia.org/wiki/Ciphertext_stealing
- Information about RSA - https://en.wikipedia.org/wiki/RSA_(cryptosystem)
- More information about RSA - https://www.youtube.com/watch?v=ZPXVSJnDA_A



Key size selection rsa - https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-57pt1r5.pdf (page 65)