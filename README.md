# Morse Encryptor (AES-256-GCM + Base32 wire)

> ⚠️ **Morse code is outdated and offers no security** — it’s included here purely for fun and transport/teaching. The cryptography here is **AES-256-GCM with PBKDF2(SHA-256)**; Morse is just an optional wrapper for sending/receiving over dots/dashes.

Single-file, dependency-free web app that:
- **Encrypts** text with **AES-256-GCM** (key from **PBKDF2-SHA256**, configurable iterations),  
- **Encodes “cipherbytes”** to **Base32** (recommended) or **Base64**,  
- **Maps the wire text to/from Morse**, so you can send/receive by `·` and `−`.

No build. No network. All in-browser.

---

## ✨ Features
- **Text → AES-GCM → Base32/Base64 → Morse**; reverse pipeline for **Morse/wire → plaintext**.
- **Base32 by default** to avoid case loss over Morse; Base64 is supported but **case-sensitive**.
- **Robust Morse normalization**: accepts `·•∙` for dot, `–—−` for dash, `⁄∕` for `/`, collapses whitespace; letters separated by space, words by `/`.
- **Configurable PBKDF2 iterations** (default **300 000**, min **60 000**).
- **Clear status messages** (e.g., `Decrypt error: … (wrong password or altered Morse).`).
- **Single file** (`index.html`), no deps, works offline.

## 🧭 How it works
**Encrypt path**
1. **KDF**: PBKDF2-SHA256(pass, random 16-byte salt, iterations) → 256-bit key.
2. **Encrypt**: AES-256-GCM with random 12-byte IV.
3. **Header** + ciphertext is the “wire payload.”

**Header layout (bytes)**
"MCE1" | alg(0x01=AES-GCM) | iters(u32 BE) | salt(16) | iv(12) | cipher(+tag)


**Wire alphabets**
- **Base32 (recommended)** — case-agnostic, safe for Morse round-trip.
- **Base64** — case-sensitive; only use if you’re **not** sending as Morse.

## 🔐 Security notes (read me)
- **Morse ≠ security**. It’s a lossy channel wrapper and can corrupt case; use **Base32** to prevent authentication tag failures.
- **AES-GCM** provides confidentiality + integrity; random 96-bit IV each time.
- **PBKDF2 iterations**: raise on fast hardware; lower only if your device stalls. Strong passphrases still required.
- **Client-side only**: no telemetry, no server. This does not defend against compromised browsers, keyloggers, or shoulder-surfing.

## 🧑‍💻 Usage
1. Open `index.html` in a modern browser.
2. **Encrypt**
   - Enter **Plaintext** and **Password**, set **PBKDF2 iterations** (default 300 000).
   - Choose **Wire Encoding** (Base32 recommended).
   - Click **Encrypt → Wire & Morse**.
   - Copy **Wire text** or **Morse**.
3. **Decrypt**
   - Paste **Morse** (or wire text) and provide **Password**.  
   - Click **Decode & Decrypt**.  
   - If you get `OperationError`, it’s usually a **wrong password** or **altered/Morse-mangled wire**.

## 🧾 Morse alphabet & separators
- Alphabet includes `A–Z`, `0–9`, plus `+`, `/`, `=`; unknown tokens error out.
- **Letter separator**: space; **Word separator**: `/`.
- Input tolerance: `·•∙`→`.`, `–—−`→`-`, multiple `//` collapsed.

## 💡 Tips
- Sending over radio/audio? Prefer **Base32** → Morse.  
- Don’t edit the wire manually; keep `=` padding intact for Base32/Base64.
- Keep a record of your **iterations** if you change the default (the value is embedded in the header, but using wildly different fallbacks may confuse manual workflows).

## ♿ Accessibility
- Keyboard-operable controls; ARIA labels; live-status region for errors.

## 🛠️ Customize
- Change default KDF iterations (input box).
- Extend the Morse map by adding characters to the `MAP` (reverse map derives automatically).

## 📦 Deploy
- **GitHub Pages**: push this repo, then enable Pages (deploy from `main` / root).  
- Any static host works (Netlify, Vercel, Cloudflare Pages, local file).

## 🗺️ Roadmap
- WPM/ratio controls and audio playback for Morse.
- Clipboard and share helpers.
- Practice mode (drills, accuracy, stats).
- Haptics on mobile (where supported).
- Optional light theme.

## 🛡️ Privacy
All work happens locally in your browser. Only minimal preferences (if any) are stored client-side.

## 📄 License
MIT — see `LICENSE`.

---
