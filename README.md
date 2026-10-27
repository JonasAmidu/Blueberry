# Blueberry

A private Bitcoin wallet for your terminal.

Early software. Still under active development.

Blueberry is a light Bitcoin wallet that runs in the terminal. It uses Neutrino-style compact filters to sync without downloading the full chain. This fork requires a local Tor SOCKS proxy for synchronization and broadcasting, with encrypted BIP-324 peer connections. You can create or restore a wallet, see balance and history, receive, and send.

New wallet secrets are encrypted before they are saved. Read [SECURITY.md](SECURITY.md) for the security boundaries and validation results. This is experimental software; these changes do not establish suitability for holding real Bitcoin or guarantee anonymity.

<img width="3750" height="2022" alt="image" src="https://github.com/user-attachments/assets/3375738f-3b95-4b42-8795-3df4fefca94a" />


## TODO

* [x] Require encryption before saving a new wallet secret
* [ ] camera QR scan
* [ ] Payment Codes (BIP47 + SP)

## Features

- **Neutrino light sync** — headers + compact filters; fetch full blocks only when they match your wallet
- **Tor required** — synchronization and broadcasting through local Tor; no direct fallback or local peer DNS
- **Protected storage** — required password encryption and owner-only wallet directory/database permissions
- **Native SegWit (BIP84)**
- **Create or restore** — new 12-word seed, or import a seed / account `zpub` / compressed `WIF` / address
- **Watch-only ready** — `zpub` wallets can build unsigned PSBTs (including air-gapped anmated QR flow)
- **Receive**
- **Send** — set amount or send max; hot wallets sign in-app; watch-only export for external signing
- **Live sync dashboard** — peers, chain tip, filters, matching, blocks, balance, and transactions in one TUI
- **Desktop cli** but has UI

## Build and run

Use Bun 1.3.14 on Linux, WSL, or macOS. Native Windows is not supported by the private-storage permission checks; use WSL and its Linux filesystem.

```sh
bun install --frozen-lockfile --ignore-scripts
bun run typecheck
bun test tests/unit tests/security
bun run build
```

Start your local Tor service before launching `dist/blueberry`. The default SOCKS endpoint is `127.0.0.1:9050`; use `--tor-socks-port=9150` if your Tor service uses that port. Keep the wallet's working directory private and outside the source checkout. Blueberry creates `blueberry.data` in its working directory.

Choose **Create new wallet**, record the seed offline, and set a strong password of at least 12 characters. Existing wallets prompt for their password. BIP39 seeds with an additional passphrase are not supported; the disk-encryption password is a separate feature. Press `q` outside a text field to quit.

## Receive and send

With Tor running, let synchronization finish. Use left/right arrows on the dashboard to select **Receive** or **Send**, then press Enter.

**Receive:** display the address/QR and verify it before sharing it. Only Bitcoin on the Bitcoin main network is supported. The address can be reused by the UI until an incoming payment is recognized; separate unpaid requests are not assigned reserved addresses.

**Send:** use up/down arrows and Space to select the coins to spend, then Enter. Enter the recipient address, amount in BTC (or `max`), and a payment label; up/down moves between fields. Continue to the fee rate in sat/vB, then inspect the recipient, amount, fee, and change in the preview. Enter on the final signed preview broadcasts the transaction. Esc closes the dialog. Watch-only wallets produce an unsigned PSBT for an external signer.

“Transaction submitted” does not establish acceptance or confirmation. Verify confirmation in synced transaction history and with the recipient. Avoid creating another payment from the same coins while the first is pending. Back up your seed offline; never put seeds, passwords, wallet databases, or transaction screenshots into issues or commits.
