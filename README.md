# Network-Restricted Decryption

A Java-based encryption system where decryption is bound to a specific network environment. Messages are encrypted with AES, the AES key is protected with RSA, and a Wi-Fi fingerprint of the network is captured and tied to the payload — so the message can only be decrypted from the intended network context.

## How It Works

1. **AES Encryption** – The plaintext message is encrypted using a randomly generated AES key (`AESUtil`).
2. **RSA Key Wrapping** – The AES key itself is encrypted with the recipient's RSA public key (`RSAUtil`), so only the holder of the matching private key can recover it.
3. **Wi-Fi Fingerprinting** – The surrounding Wi-Fi environment is scanned and fingerprinted (`WifiScanner`), binding the payload to a specific network context.
4. **Payload Assembly** – The ciphertext, encrypted AES key, and Wi-Fi fingerprint are bundled into a single JSON payload (`PayloadUtil`).
5. **Decryption** – On the receiving side, the current network's Wi-Fi fingerprint is checked against the one embedded in the payload before the RSA private key is used to unwrap the AES key and decrypt the message (`Decrypt` / `Decryptor`).

## Project Structure

| File | Purpose |
|---|---|
| `EncryptCLI.java` | Command-line entry point for encrypting a message and producing a payload |
| `EncryptMessage.java` | Core encryption logic / message handling |
| `AESUtil.java` | AES key generation, encryption, and decryption helpers |
| `RSAUtil.java` | RSA key pair generation, AES key wrapping/unwrapping |
| `KDFEncryptDemo.java` | Demo of key-derivation-function–based encryption |
| `WifiScanner.java` | Captures the local Wi-Fi fingerprint used to restrict decryption |
| `PayloadUtil.java` | Builds and parses the JSON payload bundling ciphertext, key, and fingerprint |
| `Decrypt.java` / `Decryptor.java` | Decrypts a payload after validating the network fingerprint |
| `Main.java` | Stub entry point — use `EncryptCLI` instead |

## Requirements

- Java JDK 8 or higher
- A terminal with network/Wi-Fi access for fingerprinting to work correctly

## Setup & Usage

Clone the repository:

```bash
git clone https://github.com/rohit87887/network_security-.git
cd network_security-
```

Compile the source files:

```bash
javac *.java
```

### Encrypt a message

```bash
java com.example.networkrestricted.EncryptCLI -m "your secret message" -o encrypted_payload.json
```

Options:
- `-m`, `--message` — the plaintext message to encrypt (if omitted, you'll be prompted interactively)
- `-o`, `--output` — output file for the encrypted payload (default: `encrypted_payload.json`)
- `-h`, `--help` — show usage help

On first run, an RSA key pair (`recipient_public.der`, `recipient_private.der`) is generated automatically if one doesn't already exist. A Wi-Fi fingerprint is also captured and saved to `wifi_fingerprint.json`.

### Decrypt a message

```bash
java com.example.networkrestricted.Decryptor encrypted_payload.json
```

Decryption will only succeed if the current network's Wi-Fi fingerprint matches the one recorded at encryption time.

## Security Notes

- This project was built for learning/demonstration purposes around applied cryptography and network-context-based access control — it has **not** been audited for production use.
- Private keys (`recipient_private.der`) and generated fingerprint/payload files should never be committed to version control (see `.gitignore`).
- Wi-Fi fingerprinting is environment-dependent and may behave differently across OSes and network adapters.

## License

This project is licensed under the MIT License — see the [LICENSE](LICENSE) file for details.
