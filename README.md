<div align="center">

<img width="100%" alt="header" src="https://capsule-render.vercel.app/api?type=waving&height=210&text=ANF%20Miniapp%20Bot&fontAlign=50&fontAlignY=36&fontSize=56&desc=Daily%20Check-In%20%7C%20Auto%20Reactor%20Claim%20%7C%20Reactor%20PRO%20%7C%20Referral%20Claim%20%7C%20Multi-Account&descAlign=50&descAlignY=58"/>

<img alt="typing" src="https://readme-typing-svg.demolab.com?font=Inter&size=18&duration=3000&pause=650&center=true&vCenter=true&width=900&lines=Auto+Daily+Check-In+%7C+Streak+Tracking;Auto+Reactor+Claim+%7C+Hold+Tier+Based+Mining+Rate;Auto+Reactor+PRO+Claim+%7C+Hybrid+Mining+Rewards;Auto+Referral+Claim+%7C+Collect+Pending+Referral+Rewards;Proxy+Support+%7C+One+Proxy+Per+Account;Multi-Account+%7C+Sequential+Processing+Per+Cycle"/>

<p>
  <img alt="python" src="https://img.shields.io/badge/Python-3.10+-3776AB?logo=python&logoColor=white"/>
  <img alt="platform" src="https://img.shields.io/badge/Platform-ANF%20Miniapp-111111"/>
  <img alt="multi-account" src="https://img.shields.io/badge/Multi--Account-Supported-111111"/>
  <img alt="proxy" src="https://img.shields.io/badge/Proxy-Supported-111111"/>
  <img alt="author" src="https://img.shields.io/badge/by-Yuurisandesu-111111"/>
</p>

<p>
  <b>ANF Miniapp Bot</b> is a full automation bot for the ANF Telegram Miniapp.<br/>
  It handles the complete daily cycle: authenticating each account, completing the daily check-in to maintain the streak, claiming Reactor mining rewards based on the account hold tier and boost multiplier, claiming Reactor PRO rewards from the hybrid mining system, and collecting any pending referral rewards, all running across multiple accounts with proxy support and a live countdown between cycles.<br/>
  Built and distributed by <b>Yuurisandesu</b>.
</p>

</div>

---

## Table of Contents

- [Requirements](#requirements)
- [Installation](#installation)
- [Configuration](#configuration)
- [Running the Bot](#running-the-bot)
- [Features](#features)
- [File Structure](#file-structure)
- [Disclaimer](#disclaimer)

---

## Requirements

- Python `3.10+`
- Git

---

## Installation

**Clone the repository:**

```bash
git clone https://github.com/Yuurisan-N1/ANF-Miniapp.git
cd ANF-Miniapp
```

**Install dependencies:**

```bash
pip install requests colorama
```

**Download the bot binary using the downloader:**

```bash
python bot.py
```

The downloader will show a numbered menu. Enter the number that matches your platform and the binary will be downloaded automatically with a live progress bar showing MB downloaded. For Linux binaries, file permissions are set to executable automatically after the download completes.

**Or download manually from the releases page:**

| Platform | Filename | Download |
|---|---|---|
| Linux ARM64 | `anf-linux-arm64` | [Download](https://github.com/Yuurisan-N1/ANF-Miniapp/releases/download/v1.0.0/anf-linux-arm64) |
| Linux AMD64 | `anf-linux-amd64` | [Download](https://github.com/Yuurisan-N1/ANF-Miniapp/releases/download/v1.0.0/anf-linux-amd64) |
| Windows AMD64 | `ANF.exe` | [Download](https://github.com/Yuurisan-N1/ANF-Miniapp/releases/download/v1.0.0/ANF.exe) |

If you download the Linux binary manually, you need to set it as executable before running:

```bash
chmod +x anf-linux-arm64
# or
chmod +x anf-linux-amd64
```

---

## Configuration

### 1. Accounts (data.txt)

Fill `data.txt` with Telegram WebApp `initData` for each account, one per line:

```
user=%7B%22id%22...&hash=abc123
user=%7B%22id%22...&hash=def456
```

> `initData` can be obtained from the browser DevTools when opening ANF on Telegram Web.

### 2. Proxy (proxy.txt) - Optional

Fill `proxy.txt` with proxies, one per line. Proxies are assigned to accounts by index (first proxy to first account, second proxy to second account, and so on). If the number of proxies is fewer than the number of accounts, proxies wrap around cyclically. If `proxy.txt` is missing or empty, the bot runs without a proxy.

```
http://user:pass@ip:port
http://user:pass@ip:port
```

Supported formats: `http://user:pass@host:port` or `host:port:user:pass`

### 3. Bot Settings (config.json)

`sleep_seconds` controls how many seconds the bot waits between cycles. If `config.json` is missing, the bot falls back to a default of `3600` seconds.

```json
{
  "settings": {
    "sleep_seconds": 3600
  }
}
```

---

## Running the Bot

**Linux ARM64:**

```bash
./anf-linux-arm64
```

**Linux AMD64:**

```bash
./anf-linux-amd64
```

**Windows:**

```bash
ANF.exe
```

Press `Ctrl+C` at any time to stop the bot cleanly.

---

## Features

### Auto Login
The bot authenticates each account by syncing the `initData` against the protocol endpoint with encrypted client integrity headers. The username, total balance, current hold tier level, Reactor PRO level, and referral count are resolved from the sync response and logged before any actions are taken. Accounts that are banned or not yet registered on the protocol are detected early and skipped without stopping the rest of the cycle.

### Auto Daily Check-In
The bot sends a daily check-in request for each account and logs the current streak count and updated balance on success. If the daily reward was already collected today, the result is logged and the account moves on without stopping the rest.

### Auto Reactor Claim
The bot calculates the current mining rate for each account based on the hold tier derived from the total balance and any active boost multipliers. If enough ANF has accrued since the last claim, a claim request is sent and the reward amount and tier level are logged on success. If the accrued amount has not yet reached one whole ANF, the account is logged and skipped without stopping the rest.

### Auto Reactor PRO Claim
The bot calculates the Reactor PRO accrual from the hybrid mining system based on the account Reactor PRO level. If enough has accrued since the last claim, a claim request is sent and the reward and level are logged on success. If not enough has accrued yet, the account is logged and skipped without stopping the rest.

### Auto Referral Claim
The bot checks both the direct referral reward and the mining referral reward balances. If the combined pending amount reaches at least one whole ANF, a claim request is sent and the collected amount is logged on success. If there are no pending referral rewards, this step is logged and skipped.

### Proxy Support
Each account can be assigned its own proxy via `proxy.txt`. If a proxy is configured for the current account, it is shown in masked form before processing begins. Proxy assignment uses a round-robin fallback if there are fewer proxies than accounts. Both `http://user:pass@host:port` and `host:port:user:pass` formats are supported.

### Multi Account
All accounts in `data.txt` are processed sequentially within every cycle. Account index, username, balance, hold tier, Reactor PRO level, and referral count are logged at the start of each account. Final balance and total mined in the ledger are logged after all actions complete. A blank line separates each account output in the terminal for readability.

### Auto Countdown
After all accounts complete a cycle, the bot displays a live `HH:MM:SS` countdown in the terminal until the next cycle starts, then re-shows the banner before beginning again.

---

## File Structure

```text
ANF-Miniapp/
├── bot.py          # Downloader, select and fetch binary for your platform
├── config.json     # Sleep duration between cycles
├── data.txt        # Account initData, one per line
├── proxy.txt       # Proxies, one per line (optional)
├── LICENSE         # License file
└── utils/
    ├── banner.py   # Banner display on startup
    └── __init__.py
```

---

## Disclaimer

This tool is built for educational and technical exploration purposes. Use it wisely and at your own responsibility.

---

<div align="center">
<img width="100%" alt="footer" src="https://capsule-render.vercel.app/api?type=waving&height=120&section=footer"/>
</div>