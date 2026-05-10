<div align="center">

<br/>

```
██╗   ██╗ █████╗ ██╗   ██╗██╗  ████████╗██████╗ ██╗      █████╗ ██╗   ██╗
██║   ██║██╔══██╗██║   ██║██║  ╚══██╔══╝██╔══██╗██║     ██╔══██╗╚██╗ ██╔╝
██║   ██║███████║██║   ██║██║     ██║   ██████╔╝██║     ███████║ ╚████╔╝ 
╚██╗ ██╔╝██╔══██║██║   ██║██║     ██║   ██╔═══╝ ██║     ██╔══██║  ╚██╔╝  
 ╚████╔╝ ██║  ██║╚██████╔╝███████╗██║   ██║     ███████╗██║  ██║   ██║   
  ╚═══╝  ╚═╝  ╚═╝ ╚═════╝ ╚══════╝╚═╝   ╚═╝     ╚══════╝╚═╝  ╚═╝   ╚═╝  
```

### **AES-256-GCM Client-Side Encrypted Video Player**

*Encrypt. Protect. Play — entirely in your browser.*

<br/>

[![Live Demo](https://img.shields.io/badge/🔐%20Live%20Demo-VaultPlay-gold?style=for-the-badge&color=c9a227)](https://fullstackdeveloperparvez.github.io/VideoEncrypter/)
[![License](https://img.shields.io/badge/License-MIT-green?style=for-the-badge)](LICENSE)
[![HTML5](https://img.shields.io/badge/HTML5-E34F26?style=for-the-badge&logo=html5&logoColor=white)](https://developer.mozilla.org/en-US/docs/Web/HTML)
[![Web Crypto API](https://img.shields.io/badge/Web%20Crypto%20API-AES--256--GCM-blue?style=for-the-badge)](https://developer.mozilla.org/en-US/docs/Web/API/Web_Crypto_API)
[![No Dependencies](https://img.shields.io/badge/Dependencies-Zero-brightgreen?style=for-the-badge)]()

<br/>

> 🔒 **No server. No upload. No backend. Your video never leaves your device.**

<br/>

</div>

---

## 📖 Table of Contents

- [Overview](#-overview)
- [Live Demo](#-live-demo)
- [Features](#-features)
- [How It Works](#-how-it-works)
- [Security Architecture](#-security-architecture)
- [The `.encvid` Container Format](#-the-encvid-container-format)
- [Usage Guide](#-usage-guide)
  - [Encrypting a Video](#step-1--encrypting-a-video)
  - [Playing an Encrypted Video](#step-2--playing-an-encrypted-video)
- [Keyboard Shortcuts](#-keyboard-shortcuts)
- [Player Controls Reference](#-player-controls-reference)
- [File Types](#-file-types)
- [Technology Stack](#-technology-stack)
- [Project Structure](#-project-structure)
- [Running Locally](#-running-locally)
- [Browser Compatibility](#-browser-compatibility)
- [Security Considerations](#-security-considerations)
- [Limitations](#-limitations)
- [Contributing](#-contributing)
- [License](#-license)

---

## 🔍 Overview

**VaultPlay** is a fully client-side, zero-dependency encrypted video player built with pure HTML, CSS, and JavaScript. It lets you encrypt any `.mp4` video file using **AES-256-GCM** — the gold standard in symmetric encryption — and play it back securely inside the browser without ever uploading your video to any server.

The encrypted video is stored as a custom `.encvid` container file. Decryption happens entirely **in-memory** at playback time, meaning the plaintext video data is never written to disk. When you close the tab, it's gone.

This is ideal for:
- 🎓 Protecting proprietary course or training content
- 🏢 Distributing confidential internal video recordings
- 🔐 Sharing sensitive video evidence or recordings
- 🎬 Personal archiving of private video content
- 🧪 Demonstrating client-side cryptography with the Web Crypto API

---

## 🌐 Live Demo

👉 **[https://fullstackdeveloperparvez.github.io/VideoEncrypter/](https://fullstackdeveloperparvez.github.io/VideoEncrypter/)**

No installation needed. Open the link, drop your `.mp4`, and encrypt it right in the browser.

---

## ✨ Features

### 🔐 Encryption
- **AES-256-GCM** encryption via the native [Web Crypto API](https://developer.mozilla.org/en-US/docs/Web/API/Web_Crypto_API) — no third-party crypto libraries
- **Auto key generation** — generates a cryptographically secure 256-bit random key if no passphrase is provided
- **Passphrase support** — derives a key from your passphrase using **PBKDF2** (200,000 iterations, SHA-256 hash, random 128-bit salt)
- **Dual export** — downloads both the `.encvid` encrypted container and a `.vaultkey` key file
- Real-time progress bar during encryption
- File size comparison before and after encryption

### ▶️ Video Player
- Full-featured HTML5 video player with a polished custom UI
- **Seek bar** with hover time tooltip and buffered-range indicator
- **Play / Pause** toggle
- **Stop** (resets playback to beginning)
- **Skip ±10 seconds** forward and backward
- **Volume slider** with mute toggle
- **Playback speed** cycling: `0.25×` → `0.5×` → `0.75×` → `1×` → `1.25×` → `1.5×` → `2×`
- **Picture-in-Picture (PiP)** mode
- **Fullscreen** mode
- **Keyboard shortcuts** for all major controls
- Time display showing elapsed and total duration
- Encrypted file metadata strip (codec, file size, duration, cipher info)

### 🎨 Design
- Dark, industrial-luxury aesthetic with gold accents
- Zero external dependencies — no frameworks, no libraries, no CDN calls for logic
- Drag-and-drop file zones for both encrypted files and key files
- Responsive layout suitable for desktop and laptop use

---

## ⚙️ How It Works

```
┌──────────────────────────────────────────────────────────────┐
│                       ENCRYPTION FLOW                        │
│                                                              │
│  .mp4 File  ──►  ArrayBuffer  ──►  AES-256-GCM Encrypt       │
│                                         │                    │
│                    ┌────────────────────▼──────────────┐     │
│                    │         .encvid Container          │     │
│                    │  MAGIC │ VER │ IV │ SALT? │ DATA  │     │
│                    └───────────────────────────────────┘     │
│                                         │                    │
│                                   Download File              │
└──────────────────────────────────────────────────────────────┘

┌──────────────────────────────────────────────────────────────┐
│                       PLAYBACK FLOW                          │
│                                                              │
│  .encvid File ──►  Parse Container  ──►  Extract IV + Data   │
│  .vaultkey    ──►  Import Key                                │
│                          │                                   │
│                   AES-256-GCM Decrypt                        │
│                          │                                   │
│                   Blob URL (in-memory)                       │
│                          │                                   │
│                   <video> Element  ──►  Play                 │
│                                                              │
│             ✅ Plaintext data NEVER touches disk             │
└──────────────────────────────────────────────────────────────┘
```

---

## 🛡️ Security Architecture

| Component | Details |
|---|---|
| **Cipher** | AES-256-GCM (Authenticated Encryption with Associated Data) |
| **Key size** | 256 bits |
| **IV / Nonce** | 96-bit (12 bytes) cryptographically random, generated fresh per encryption |
| **Authentication tag** | 128-bit GCM tag — detects any tampering with the ciphertext |
| **Key derivation (passphrase)** | PBKDF2 · SHA-256 · 200,000 iterations · 128-bit random salt |
| **Key source (auto)** | `crypto.subtle.generateKey` — OS-level CSPRNG |
| **Crypto engine** | Browser's native [Web Crypto API](https://developer.mozilla.org/en-US/docs/Web/API/SubtleCrypto) — no third-party library |
| **Data scope** | In-memory `Blob URL` only; decrypted data is never written to `localStorage`, `IndexedDB`, or disk |
| **Server exposure** | Zero — 100% client-side, no network requests made |

> **Why AES-256-GCM?**  
> GCM (Galois/Counter Mode) provides both **confidentiality** (encryption) and **integrity** (authentication). If an attacker modifies even a single byte of the ciphertext, decryption will fail with an authentication error — your video cannot be silently corrupted.

---

## 📦 The `.encvid` Container Format

VaultPlay uses a custom binary container format to bundle all decryption metadata alongside the ciphertext:

```
Offset    Size     Field
──────    ──────   ──────────────────────────────────────────
0         8 B      Magic bytes: ASCII "VAULTPLY"
8         1 B      Version: 0x01
9         12 B     IV (AES-GCM Nonce) — random per encryption
21        1 B      Salt flag: 0x00 = no salt, 0x01 = salt present
22        0 or     PBKDF2 Salt (only if salt flag = 0x01)
          16 B
22/38     variable AES-256-GCM ciphertext (includes 16-byte GCM auth tag)
```

This design means:
- You only need the **key** (or passphrase) to decrypt — the IV and salt are embedded
- The magic header allows quick validation before attempting decryption
- The format is forward-compatible via the version byte

---

## 🚀 Usage Guide

### Step 1 — Encrypting a Video

1. **Open the app** at [https://fullstackdeveloperparvez.github.io/VideoEncrypter/](https://fullstackdeveloperparvez.github.io/VideoEncrypter/)
2. Stay on the **① Encrypt Video** tab
3. **Drop your `.mp4` file** onto the upload zone, or click to browse
4. *(Optional)* Enter a custom **passphrase** in the key field, or leave it blank for auto key generation
5. Click **Encrypt & Export**
6. Wait for the progress bar to complete
7. **Download both files:**
   - `yourfile.encvid` — the encrypted video container
   - `yourfile.vaultkey` — your decryption key

> ⚠️ **Critical:** Keep your `.vaultkey` file safe. Without it, the video **cannot be recovered**. There is no password reset or key recovery mechanism.

---

### Step 2 — Playing an Encrypted Video

1. Switch to the **② Play Encrypted** tab
2. **Load the `.encvid` file** in the left drop zone
3. **Load the key** using either method:
   - Drop your `.vaultkey` file into the right drop zone, **or**
   - Paste the Base64 key string (or passphrase) manually into the text field
4. Click **Decrypt & Play**
5. The video decrypts in memory and begins playing in the built-in player

---

## ⌨️ Keyboard Shortcuts

| Key | Action |
|---|---|
| `Space` | Play / Pause |
| `→` | Skip forward 5 seconds |
| `←` | Skip back 5 seconds |
| `↑` | Increase volume |
| `↓` | Decrease volume |
| `M` | Toggle mute |
| `F` | Toggle fullscreen |

> Keyboard shortcuts are active when focus is not inside a text input field.

---

## 🎮 Player Controls Reference

| Control | Description |
|---|---|
| ▶ / ⏸ Play/Pause | Start or pause playback |
| ⏹ Stop | Pause and return to the beginning |
| ⏮ Skip –10s | Jump back 10 seconds |
| ⏭ Skip +10s | Jump forward 10 seconds |
| 🔊 Volume Slider | Drag to set volume from 0–100% |
| 🔇 Mute Toggle | Instantly silence or restore audio |
| Seek Bar | Click or drag to jump to any position; hover for time preview |
| `1×` Speed Button | Cycle playback rate: 0.25×, 0.5×, 0.75×, 1×, 1.25×, 1.5×, 2× |
| ⧉ Picture-in-Picture | Float the video in a mini player overlay |
| ⛶ Fullscreen | Expand to fullscreen; press `Esc` or `F` to exit |

---

## 📁 File Types

| Extension | Description |
|---|---|
| `.mp4` | Input source video file (H.264 recommended) |
| `.encvid` | VaultPlay encrypted video container (custom binary format) |
| `.vaultkey` | Plain-text file containing the Base64-encoded AES-256 key |

---

## 🛠️ Technology Stack

| Technology | Purpose |
|---|---|
| **HTML5** | Application structure and `<video>` element |
| **CSS3** | Custom-built player UI, animations, dark theme |
| **Vanilla JavaScript (ES2020+)** | All logic — encryption, decryption, player controls |
| **Web Crypto API** (`crypto.subtle`) | AES-256-GCM encryption/decryption, PBKDF2 key derivation |
| **Blob / URL.createObjectURL** | In-memory video playback without disk writes |
| **File API / FileReader** | Reading local files from drag-and-drop or file picker |
| **GitHub Pages** | Static site hosting |

**Zero runtime dependencies.** No npm. No bundler. No framework. No crypto library. One HTML file.

---

## 📂 Project Structure

```
VideoEncrypter/
│
├── index.html          # The entire application — HTML + CSS + JS in one file
├── README.md           # This file
└── LICENSE             # MIT License
```

The entire application is self-contained in a single `index.html` file, making it trivially deployable to any static host or usable offline by simply opening the file in a browser.

---

## 💻 Running Locally

No build step, no dependencies, no package manager required.

**Option 1 — Open directly:**
```bash
# Clone the repo
git clone https://github.com/fullstackdeveloperparvez/VideoEncrypter.git

# Open in your browser
open VideoEncrypter/index.html       # macOS
xdg-open VideoEncrypter/index.html  # Linux
start VideoEncrypter/index.html     # Windows
```

**Option 2 — Serve with a local server** *(recommended for full File API support in some browsers)*:
```bash
# Using Python 3
cd VideoEncrypter
python3 -m http.server 8080
# Then open http://localhost:8080

# Using Node.js (npx)
npx serve .
```

**Option 3 — VS Code Live Server:**  
Install the [Live Server](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer) extension and click **"Go Live"** from `index.html`.

---

## 🌐 Browser Compatibility

VaultPlay requires a modern browser with full Web Crypto API support.

| Browser | Support | Notes |
|---|---|---|
| Chrome 85+ | ✅ Full | Recommended |
| Edge 85+ | ✅ Full | Chromium-based |
| Firefox 75+ | ✅ Full | |
| Safari 15+ | ✅ Full | PiP may vary on iOS |
| Opera 71+ | ✅ Full | |
| IE 11 | ❌ None | Web Crypto API not supported |

> `crypto.subtle` is only available in **secure contexts** (HTTPS or `localhost`). The app will not function over plain HTTP on a remote host.

---

## 🔒 Security Considerations

**What VaultPlay protects against:**
- Unauthorized viewing of `.encvid` files without the key
- Passive network interception (nothing is transmitted)
- Accidental cloud sync exposure of plaintext video

**What VaultPlay does NOT protect against:**
- Screen recording while the video is playing
- An attacker who already has access to both the `.encvid` file **and** the `.vaultkey` file
- Memory forensics on a compromised machine during active playback
- Brute-force attacks on very weak passphrases (use strong passphrases)

**Best practices:**
- Store the `.encvid` file and `.vaultkey` file in **separate locations**
- Use a strong, random passphrase if using passphrase-based encryption
- Prefer auto-generated keys for maximum entropy
- Never share the `.vaultkey` file over the same channel as the `.encvid` file

---

## ⚠️ Limitations

- **File size:** Very large files (>2 GB) may hit browser memory limits depending on available RAM, as the entire file is loaded into memory for encryption/decryption
- **Format:** Only `.mp4` input is supported for encryption; the player requires H.264-encoded content that the browser's native video decoder can handle
- **No streaming decryption:** The entire file is decrypted before playback begins; there is no chunk-based streaming decryption
- **No DRM:** VaultPlay is not a DRM system — it is an encryption utility. A determined user with the key can trivially export the decrypted blob

---

## 🤝 Contributing

Contributions are welcome! Here are some ideas for enhancements:

- [ ] Chunk-based streaming decryption for large files
- [ ] Support for additional video formats (`.webm`, `.mov`)
- [ ] Subtitles / caption file support (`.vtt`)
- [ ] Bookmarks / chapter markers
- [ ] Thumbnail preview strip on seek bar hover
- [ ] Batch encryption of multiple files

**To contribute:**

```bash
# 1. Fork the repository on GitHub

# 2. Clone your fork
git clone https://github.com/YOUR_USERNAME/VideoEncrypter.git

# 3. Create a feature branch
git checkout -b feature/your-feature-name

# 4. Make your changes to index.html

# 5. Commit and push
git commit -m "feat: add your feature description"
git push origin feature/your-feature-name

# 6. Open a Pull Request on GitHub
```

Please ensure your changes maintain the zero-dependency philosophy and work without a build step.

---

## 📄 License

This project is licensed under the **MIT License** — see the [LICENSE](LICENSE) file for details.

```
MIT License · Copyright (c) 2025 Parvez
Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software...
```

---

<div align="center">

**Built with 🔐 and zero dependencies**

[![Live Demo](https://img.shields.io/badge/🔐%20Try%20VaultPlay%20Live-c9a227?style=for-the-badge)](https://fullstackdeveloperparvez.github.io/VideoEncrypter/)

*If you find this project useful, please consider giving it a ⭐ on GitHub!*

</div>
