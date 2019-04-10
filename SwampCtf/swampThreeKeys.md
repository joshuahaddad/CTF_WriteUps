SwampCTF - "We Three Keys"
======
##### CTF Name: SwampCTF 2019
##### Challenge Name: We Three Keys
##### Challenge Category: Crypto
##### Difficulty: Easy
##### Points: 144
##### Challenge Description: 
>In this modern world of cyber, keys are the new kings of the world. In fact, we will let you use the 3 best keys that I could find, make sure to see what they offer to you!
>
>nc chal1.swampctf.com 1441
>
>-=Created by noopnoop =-

### Solution
#### _Intro and Given Code_
This was an interesting crypto challenge which gave the source code to a faulty AES-CBC block cipher encryption service.  The full script is shown [here](add Link).
#### _AES-CBC Theory_
![Encrypt](AES_CBC_Encrypt)
![Decrypt](AES_CBC_Decrypt)

In the encryption, the plaintext is divided into multiple blocks.  The encryption is then as follows:
*Let E<sup>K</sup>(X) = AES encryption of X using key K,
B</sup>i</sup> = the ith block, 
 C<sup>i</sup> = the ith resultant ciphertext*
 
E<sup>K</sup>(IV ⊕ B</sup>0</sup>) = C<sup>0<//sup>
E<sup>K</sup>(C<sup>i-1<//sup> ⊕ B</sup>i</sup>) = C<sup>i</sup>

Decryption follows a similar pattern:
*Let D</sup>K</sup>(X) = AES decryption of X using key K,
B</sup>i</sup> = the ith plaintext block, 
C<sup>i</sup> = the ith ciphertext*
 
P</sup>i</sup> = D</sup>K</sup>(C<sup>i</sup>) ⊕ C<sup>i-1<//sup>
P</sup>0</sup> = C<sup>0<//sup> ⊕ IV

#### _Key=IV Attack_
Inspection of the main script for encryption and decryption reveals the vulnerability in the code: `encrypt_message(key, key)`
Examination of the encryption code also reveals the block length to be 16 bytes:
`%16`
This code uses the key as the IV for encryption.  Along with the ability to generate ciphertext and decrypt cipher text an attack can be mounted as follows:
1) Create a three block (48 byte) plaintext, note C<sup>0</sup> , C<sup>1<//sup> , and C<sup>2</sup>
2) Modify the ciphertext such that C<sup>0</sup> = C<sup>2</sup> to force D</sup>K</sup>(C<sup>2</sup>) = D</sup>K</sup>(C<sup>0</sup>)
3) Decrypt modified plaintext to obtain P</sup>0</sup>, P</sup>2</sup>, P</sup>2</sup> for the modified block
4) Compute D</sup>K</sup>(C<sup>0</sup>) using P</sup>2</sup> = D</sup>K</sup>(C<sup>2</sup>) ⊕ C<sup>1</sup> ⇒  P</sup>2</sup> ⊕  C<sup>1</sup> = D</sup>K</sup>(C<sup>2</sup>)
5) Compute IV = P</sup>1</sup> ⊕ D</sup>K</sup>(C<sup>0</sup>)
6) IV = Key, which contains the flag

**Using plaintext**: 
616161616161616161616161616161616161616161616161616161616161616161616161616161616161616161616161

**Encrypt with key 1**: 
6b1a585f0788db5721bf270cf46f445308970de331a0a2f06fa9ba7a792d04df564337ed2f604008dc6acb88b23787fac23cd794b80b7979e1a32700274ff328

**Separate blocks**:
Load into python string and substring blocks of 16 `block1 = cipher[0:32]`
C<sup>0</sup> = 6b1a585f0788db5721bf270cf46f4453
C<sup>1</sup> = 08970de331a0a2f06fa9ba7a792d04df
C<sup>2</sup> = 564337ed2f604008dc6acb88b23787fa
C<sup>3</sup> = c23cd794b80b7979e1a32700274ff328 (This is a result of the newline character)

**Create Ciphertext C<sup>0</sup> + C<sup>1</sup> + C<sup>0</sup> + C<sup>3</sup> , noting C<sup>1</sup>**:
6b1a585f0788db5721bf270cf46f445308970de331a0a2f06fa9ba7a792d04df6b1a585f0788db5721bf270cf46f4453c23cd794b80b7979e1a32700274ff328
C<sup>1</sup> = 08970de331a0a2f06fa9ba7a792d04df

**Decrypt Ciphertext**:
61616161616161616161616161616161616161616161616161616161616161610f9a0de52bb6f3e651bfb32f6c13098a2d497fa238f88b4fedc5fc945648d3b9
P</sup>0</sup> = 61616161616161616161616161616161
P</sup>2</sup> = 0f9a0de52bb6f3e651bfb32f6c13098a

**Compute P</sup>2</sup> ⊕ C<sup>1</sup> = D</sup>K</sup>(C<sup>0</sup>)**
D</sup>K</sup>(C<sup>0</sup>) = 70d00061a1651163e160955153e0d55

**Compute D</sup>K</sup>(C<sup>0</sup>) ⊕ P</sup>0</sup> = IV = Key**
IV = 666c61677b7730775f776834745f6c34
Converting back to ASCII: *flag{w0w_wh4t_l4*

**Decrypting on other keys and following same procedure**:
Since the above ciphertext is a valid encrypted string, we can use the same C<sup>0</sup> + C<sup>1</sup> + C<sup>0</sup> + C<sup>3</sup> for each key.

Key 2: 
P</sup>2</sup> = 90cceb203589b904830c91b4793ce0e2 P</sup>0</sup> = e222b9a8375061ab81d048a65f66d709
*Key = zy_k3yz_much_w34*

Key 3:  
P</sup>2</sup> = 6a14b23d37551a4a3eecb3440267a4a8 P</sup>0</sup> = 09dcdcac7f85cc8a0e233d0f1739810a
*Key = k_crypt0_f41ls!}*

Flag: flag{w0w_wh4t_l4zy_k3yz_much_w34k_crypt0_f41ls!}

