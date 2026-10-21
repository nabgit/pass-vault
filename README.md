# Pass Vault

A single-file password encryptor that runs entirely in your browser. Enter a master passphrase, then encrypt or decrypt text. There is no server, no dependencies, and no network access.

**Live page:** https://nabgit.github.io/pass-vault/

## How to use

You can run it two ways:

- **Online:** open the live page above. This is convenient on any device, but it needs an internet connection and loads whatever code is in this repo at that moment (see Security notes).
- **Offline:** download [`index.html`](index.html), save it on your device, and open the saved file in any modern browser. This copy never changes unless you replace it.

Then:

1. **Encrypt:** enter your master passphrase in both passphrase fields, paste the password into **Text**, and click **Encrypt**. Store the resulting `v1.…` string wherever you like.
2. **Decrypt:** enter your master passphrase, paste a `v1.…` string into **Text**, and click **Decrypt**.

Both versions use the same format, so a string encrypted in one decrypts in the other.

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
- **The live page is only as trustworthy as this repo.** Anyone who can push to it could change the page to capture passphrases. The repo owner should use two-factor sign-in on GitHub and keep write access limited. If you're not the owner, prefer the offline copy, or fork the repo and host your own.
- **Browser extensions can read the page.** Extensions with access to all sites can see what you type. Consider using a browser profile with no extensions.
- **The clipboard is not private.** Copied passwords can persist in clipboard history (Windows Win+V, phone keyboards, cloud clipboard sync).
- This is a small personal tool, not an audited product. For everyday password management, a mature open-source manager such as KeePassXC or Bitwarden adds backups, sync, and autofill.

## License

MIT. See [LICENSE](LICENSE).
