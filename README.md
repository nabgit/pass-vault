# Pass Vault

A single-file, offline password encryptor. Open `pass-vault.html` in a browser, enter a master passphrase, and encrypt or decrypt text. Everything happens locally in your browser; there is no server, no dependencies, and no network access.

## How to use

1. Download `pass-vault.html` and save it on your device.
2. Open the saved file in any modern browser (Chrome, Firefox, Safari, Edge).
3. **Encrypt:** enter your master passphrase in both passphrase fields, paste the password into **Text**, and click **Encrypt**. Store the resulting `v1.…` string wherever you like.
4. **Decrypt:** enter your master passphrase, paste a `v1.…` string into **Text**, and click **Decrypt**.

There is no recovery. If you forget the master passphrase, nothing encrypted with it can be decrypted.

## How it works

| Part | Choice |
|---|---|
| Key derivation | PBKDF2-HMAC-SHA256, 600,000 iterations, random 16-byte salt per encryption |
| Cipher | AES-256-GCM (authenticated), random 12-byte IV per encryption |
| Implementation | Browser-native Web Crypto API (`crypto.subtle`), no third-party libraries |
| Passphrase handling | Unicode NFC normalization, so the same characters give the same key on any device |
| Network | Blocked by a Content Security Policy (`default-src 'none'`) |

Output format:

```
"v1." + base64( salt[16] | iv[12] | ciphertext + GCM tag[16] )
```

Because the salt and IV are random every time, encrypting the same password twice gives different output. That is expected.

## Security notes

- **Your master passphrase is the whole security model.** Use something long and random, such as 5–6 randomly chosen words. PBKDF2 slows down guessing but does not stop a determined attacker with GPUs from cracking a weak passphrase.
- **Keep your encrypted strings private.** This tool is safe to publish, but the `v1.…` strings you generate should not be committed to a public repository or posted publicly.
- **Use a saved local copy.** A hosted copy of this page (for example on GitHub Pages) is only as trustworthy as whoever controls the hosting account at that moment. Review the file once, save it, and use that copy.
- **Browser extensions can read the page.** Extensions with access to all sites can see what you type. Consider using a browser profile with no extensions.
- **The clipboard is not private.** Copied passwords can persist in clipboard history (Windows Win+V, phone keyboards, cloud clipboard sync).
- This is a small personal tool, not an audited product. For everyday password management, a mature open-source manager such as KeePassXC or Bitwarden adds backups, sync, and autofill.

## License

MIT. See [LICENSE](LICENSE).
