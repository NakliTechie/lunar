# Lunar

A menstrual cycle tracker that runs only on your device.

**No account. No server. No analytics. No company database holds your cycle data — because there is no company database. If someone asks us for your data, there is nothing to give them.**

## Status

**Pre-launch.** The code is functionally complete (Phase 1–5 of the internal spec) and end-to-end walkthrough-tested. Before a recommended public launch, several review gates need human attention:

- First-run copy reviewed by at least three people who actually use period trackers, including at least one trans or non-binary user and at least one person in a restrictive legal context.
- Security-literate review of the Web Crypto implementation.
- Cross-browser verification on mobile Safari, mobile Chrome, desktop Firefox, desktop Chrome, and desktop Safari.

The code works. The reviews matter. Do not rely on this as a primary cycle tracker until those reviews are complete.

## What this app does

It stores the days you log, predicts your next period from your own past cycles, and shows you patterns in your symptoms and moods. Calendar view, stats, import from Apple Health / Flo / Clue, encrypted backups, printable clinician summary.

## What this app does not do

It does not send your data anywhere. No account. No server. No analytics. No company database holds your cycle data, because there is no company database. If someone asks us for your data, there is nothing to give them.

## How to run

Open `index.html` in a modern browser. That's it. No build step, no dependencies, no backend.

For local development with hot reload, serve it as a static file:

```bash
python3 -m http.server 8080
# open http://localhost:8080
```

Web Crypto requires a secure origin, so `localhost` and any HTTPS deploy work. `file://` and non-localhost HTTP will not work (Web Crypto is unavailable there).

## Encryption

- **Key derivation:** PBKDF2-SHA256, 250,000 iterations, 16-byte random salt stored alongside the ciphertext.
- **Cipher:** AES-GCM, 256-bit, fresh 12-byte random IV per write.
- **What is encrypted:** the entire serialised data blob. IndexedDB stores only `{salt, iv, ciphertext, version, hasPassphrase}`. Nothing in plaintext, including settings.
- **Session handling:** the derived key lives in memory only. Auto-lock after 5 minutes while the tab is hidden (if a passphrase is set).
- **Passphrase reset is impossible.** If you forget it, the data is gone. Export an encrypted backup before you forget, and keep the backup somewhere safe.

## Privacy features

- **Quick-hide gesture.** Press Escape or long-press anywhere for half a second. The screen blurs instantly. If you have a passphrase, tapping the overlay locks the vault and requires re-entry.
- **Decoy vault.** Set a second passphrase that unlocks a separate, hidden vault. From the outside, the two vaults are indistinguishable. Useful in environments where you might be coerced to unlock the app. Documented as plausible deniability, not perfect secrecy — see the in-app help for limitations.
- **Editable tab title and favicon.** Shown in the browser tab. Pick something neutral if you share the device.
- **Delete all.** One button in Settings. Type DELETE to confirm. Clears every day, cycle, and setting from the device.

## Portability

- **Encrypted backup.** Downloads a `.lunar` file (salt + IV + ciphertext). Restored with the same passphrase. Exports the currently-active vault only.
- **Plaintext JSON.** Behind a confirmation screen. Readable data — treat the file like a diary.
- **Imports.** Apple Health (`export.xml`), Flo (JSON export), Clue (CSV export). All parsed on your device. Nothing is uploaded.
- **Printable clinician summary.** Renders a one-page cycle history locally and opens your browser's print dialog. Save as PDF from there.

## Predictions

Two models, pick in Settings:

- **Moving average** (default). Mean of the last 6 full cycle lengths, window widened by observed variance, minimum ±2 days.
- **Bayesian** (opt-in). Normal-normal conjugate update with a clinical prior (μ₀=28, σ₀=4). Pulls toward the population when your history is short, converges to your own pattern as you log more.

Every prediction view says "Estimate based on your past cycles. Not medical advice." Lunar is not a medical device.

## Network activity

Zero after first load. Open the devtools Network tab. Use the app. Zero cross-origin requests. The only network-capable element in the file is an inline `data:` favicon URL, which never touches the wire.

Why isn't there a service worker enforcing this at runtime? Because a blob-URL service worker (the only option from within a single HTML file) cannot control the hosting page's scope. The single-file constraint and a runtime-enforced same-origin policy are incompatible. The guarantee is source-level and verifiable: grep the file for `fetch(`, `XMLHttpRequest`, `WebSocket`, `sendBeacon`, `EventSource`. All absent. A deployment to a real origin could add a companion `/sw.js` to enforce it at runtime.

## Language

English only at the moment. The i18n scaffolding is in place — a `LOCALES.en` dictionary with ~180 keys, dotted-key lookup with interpolation, RTL-ready. Adding a language is a single dictionary block and one line in the locale registry. More languages planned.

## Palette

Coloured with **`japan-04 · 桜 SAKURA`** — Hanami picnic, cherry blossom on the breeze. Soft pink-tinged cream body, bengara-red flow shades, Edo-navy ovulation marker. Cycles, blossoms, breath.

Palette pulled from [**Rangrez**](https://github.com/NakliTechie/rangrez), the global colour-palette library that backs all NakliTechie projects.

## Part of the NakliTechie series

A series of self-contained, single-file web apps that run entirely in the browser. No server, no API keys, no data leaving the device.

- [**BabelLocal**](https://github.com/NakliTechie/BabelLocal) — Offline translation, 200 languages
- [**StripLocal**](https://github.com/NakliTechie/StripLocal) — EXIF metadata stripper
- [**VoiceVault**](https://github.com/NakliTechie/VoiceVault) — Audio transcription, Whisper, offline
- [**SnipLocal**](https://github.com/NakliTechie/SnipLocal) — Background remover, passport mode
- [**LocalMind**](https://github.com/NakliTechie/LocalMind) — Private AI research agent
- [**VaultMind**](https://github.com/NakliTechie/VaultMind) — Obsidian vault explorer
- [**NakliPoster**](https://github.com/NakliTechie/NakliPoster) — Local-first API client
- [**KanZen**](https://github.com/NakliTechie/KanZen) — Kanban, local-first Trello alternative
- [**Nemawashi**](https://github.com/NakliTechie/Nemawashi) — Multi-axis group consensus
- [**BOFH**](https://github.com/NakliTechie/BOFH) — Browser-native dev toolkit
- [**Bahi**](https://github.com/NakliTechie/bahi) — Browser-native accounting for Indian SMBs

---

**Built by [Chirag Patnaik](https://github.com/NakliTechie)**
