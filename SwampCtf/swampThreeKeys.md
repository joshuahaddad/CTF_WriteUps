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
*Let E~K~(X) = AES encryption of X using key K,
B~i~ = the ith block, 
 C~i~ = the ith resultant ciphertext*
 
E~K~(IV ⊕ B~0~) = C~0~
E~K~(C~i-1~ ⊕ B~i~) = C~i~

Decryption follows a similar pattern:
*Let D~K~(X) = AES decryption of X using key K,
B~i~ = the ith plaintext block, 
C~i~ = the ith ciphertext*
 
P~i~ = D~K~(C~i~) ⊕ C~i-1~
P~0~ = C~0~ ⊕ IV

#### _Key=IV Attack_
Inspection of the main script for encryption and decryption reveals the vulnerability in the code: `encrypt_message(key, key)`
Examination of the encryption code also reveals the block length to be 16 bytes:
`%16`
This code uses the key as the IV for encryption.  Along with the ability to generate ciphertext and decrypt cipher text an attack can be mounted as follows:
1) Create a three block (48 byte) plaintext, note C~0~ , C~1~ , and C~2~
2) Modify the ciphertext such that C~0~ = C~2~ to force D~K~(C~2~) = D~K~(C~0~)
3) Decrypt modified plaintext to obtain P~0~, P~2~, P~2~ for the modified block
4) Compute D~K~(C~0~) using P~2~ = D~K~(C~2~) ⊕ C~1~ ⇒  P~2~ ⊕  C~1~ = D~K~(C~2~)
5) Compute IV = P~1~ ⊕ D~K~(C~0~)
6) IV = Key, which contains the flag

**Using plaintext**: 
616161616161616161616161616161616161616161616161616161616161616161616161616161616161616161616161

**Encrypt with key 1**: 
6b1a585f0788db5721bf270cf46f445308970de331a0a2f06fa9ba7a792d04df564337ed2f604008dc6acb88b23787fac23cd794b80b7979e1a32700274ff328

**Separate blocks**:
Load into python string and substring blocks of 16 `block1 = cipher[0:32]`
C~0~ = 6b1a585f0788db5721bf270cf46f4453
C~1~ = 08970de331a0a2f06fa9ba7a792d04df
C~2~ = 564337ed2f604008dc6acb88b23787fa
C~3~ = c23cd794b80b7979e1a32700274ff328 (This is a result of the newline character)

**Create Ciphertext C~0~ + C~1~ + C~0~ + C~3~ , noting C~1~**:
6b1a585f0788db5721bf270cf46f445308970de331a0a2f06fa9ba7a792d04df6b1a585f0788db5721bf270cf46f4453c23cd794b80b7979e1a32700274ff328
C~1~ = 08970de331a0a2f06fa9ba7a792d04df

**Decrypt Ciphertext**:
61616161616161616161616161616161616161616161616161616161616161610f9a0de52bb6f3e651bfb32f6c13098a2d497fa238f88b4fedc5fc945648d3b9
P~0~ = 61616161616161616161616161616161
P~2~ = 0f9a0de52bb6f3e651bfb32f6c13098a

**Compute P~2~ ⊕ C~1~ = D~K~(C~0~)**
D~K~(C~0~) = 70d00061a1651163e160955153e0d55

**Compute D~K~(C~0~) ⊕ P~0~ = IV = Key**
IV = 666c61677b7730775f776834745f6c34
Converting back to ASCII: *flag{w0w_wh4t_l4*

**Decrypting on other keys and following same procedure**:
Since the above ciphertext is a valid encrypted string, we can use the same C~0~ + C~1~ + C~0~ + C~3~ for each key.

Key 2: 
P~2~ = 90cceb203589b904830c91b4793ce0e2 P~0~ = e222b9a8375061ab81d048a65f66d709
*Key = zy_k3yz_much_w34*

Key 3:  
P~2~ = 6a14b23d37551a4a3eecb3440267a4a8 P~0~ = 09dcdcac7f85cc8a0e233d0f1739810a
*Key = k_crypt0_f41ls!}*

Flag: flag{w0w_wh4t_l4zy_k3yz_much_w34k_crypt0_f41ls!}

