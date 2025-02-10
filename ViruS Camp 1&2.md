# ViruS Camp 1 - Digital Forensics Challenge Writeup

## Challenge Description:
Alice was just hired as a junior developer and she is absolutely obsessed with light themes. While customizing her work laptop, she suddenly found out that their top secret flag was encrypted. Can you figure out how this happened and uncover a few flags in the process?

## Initial Analysis:
The challenge provided a file named `dimp.ad1`, which is a forensic image file that can be analyzed using Autopsy or FTK Imager. Upon mounting and browsing through the contents, I identified that the system was a VirtualBox environment. Carefully analyzing the file structure, I found a `.vscode` folder, which typically stores extensions and user configurations.

Interestingly, I got the idea of checking `.vscode` because my brain was intrigued by the title of the challenge, which had 'ViruS Camp' with 'VSC' capitalized—hinting at VS Code.

### Key Findings:
- **Distraction Elements**: A `flag.enc` file was found on the desktop, likely a decoy.
- **Suspicious Folder**: Inside `.vscode`, a folder named `undefined-publisher-activate` contained a `out/extension.js` file.
- **Malicious JavaScript Code**: The `extension.js` file was a VS Code extension, and it contained a suspiciously obfuscated script.

### Reverse Engineering the Code:
Examining `extension.js`, I found a suspicious block of Base64-encoded text at the end:
```
VGhlIDFzdCBmbGFnIGlzOiBCSVRTQ1RGe0gwd19jNG5fdlNfYzBkM19sM3RfeTB1X3B1Ymwxc2hfbTRsMWNpb3VzX2V4NzNuc2kwbnNfU09fZWFzaWx5Pz9fNWE3YjMzNmN9
```
This looked like a Base64-encoded string. Decoding it gave the first flag:
```
BITSCTF{H0w_c4n_vS_c0d3_l3t_y0u_publ1sh_m4l1cious_ex7ensi0ns_SO_easily??_5a7b336c}
```

---

# ViruS Camp 2 - Digital Forensics Challenge Writeup

## Challenge Description:
Can you now get the contents of the flag as well?

## Investigation:
In `extension.js`, there was a large obfuscated string embedded within the script, seemingly used for encryption or execution. By analyzing the PowerShell execution section, I extracted a long, encoded string that was reversed and Base64-encoded.

### Decryption Process:
Using Python, I reversed and Base64-decoded the string:
```python
import base64

payload = "K0QZjJ3bG1CIlxWaGRXdw5WakASblRXStUmdv1WZSpQDK0QKoU2cvx2Qu0WYlJHdTRXdvRi..."
reversed_payload = payload[::-1]
decoded_ps = base64.b64decode(reversed_payload).decode("utf-8", errors="replace")
print("Decoded PowerShell code:")
print(decoded_ps)
```
Output:
```
$password = "MyS3cr3tP4ssw0rd"
$salt = [Byte[]](0x01,0x02,0x03,0x04,0x05,0x06,0x07,0x08)
$iterations = 10000
$keySize = 32   
$ivSize = 16

$deriveBytes = New-Object System.Security.Cryptography.Rfc2898DeriveBytes($password, $salt, $iterations)
$key = $deriveBytes.GetBytes($keySize)
$iv = $deriveBytes.GetBytes($ivSize)

$inputFile = "C:\Users\vboxuser\Desktop\flag.png"
$outputFile = "C:\Users\vboxuser\Desktop\flag.enc"

$aes = [System.Security.Cryptography.Aes]::Create()
$aes.Key = $key
$aes.IV = $iv
$aes.Mode = [System.Security.Cryptography.CipherMode]::CBC
$aes.Padding = [System.Security.Cryptography.PaddingMode]::PKCS7
```
This script revealed the AES encryption settings and the encryption key derivation parameters.

### Recovering the Flag:
Since the password `MyS3cr3tP4ssw0rd` and encryption settings were exposed, I could use Python to decrypt `flag.enc` and recover the flag.
```python
from Crypto.Cipher import AES
from Crypto.Protocol.KDF import PBKDF2
import base64

password = b"MyS3cr3tP4ssw0rd"
salt = b"\x01\x02\x03\x04\x05\x06\x07\x08"
key_size = 32
iv_size = 16
iterations = 10000

derived_key = PBKDF2(password, salt, dkLen=key_size + iv_size, count=iterations)
key, iv = derived_key[:key_size], derived_key[key_size:]

with open("flag.enc", "rb") as f:
    ciphertext = f.read()

cipher = AES.new(key, AES.MODE_CBC, iv)
plaintext = cipher.decrypt(ciphertext)
plaintext = plaintext.rstrip(b"\x00")

with open("flag.png", "wb") as f:
    f.write(plaintext)
```
After running this script, I successfully extracted the decrypted `flag.png`, which contained the final flag.

## Conclusion:
Both challenges involved forensic analysis of a compromised system, focusing on reverse engineering JavaScript, Base64 decoding, and AES decryption. The key takeaways:
- **Understand how malicious VS Code extensions can execute scripts**
- **Analyze encoded and obfuscated scripts carefully**
- **Extract encryption parameters and decrypt data when possible**

These challenges reinforced practical skills in digital forensics and cryptography.
