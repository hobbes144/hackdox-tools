# HackDox Tools

Four small, working security tools, written in Python with a terminal UI. Each one does a real job on real data, and each one also has a "game mode" that generates practice scenarios. They were built as the toolkit behind **HackDox**, a cybersecurity-themed puzzle game where you play the clerk at the gate of a data dock and decide who gets in.

| Tool | What it does |
|------|--------------|
| [`ghostscan`](ghostscan/) | OSINT reconnaissance: checks a username across 25+ platforms; with an email it adds a breach lookup (Have I Been Pwned) and a Gravatar profile. |
| [`logwatch`](logwatch/) | Log analysis and threat detection for auth logs, web logs and Windows event logs: brute force, credential stuffing, impossible travel, after-hours access, privilege escalation, web scanners. Includes a synthetic attack-log generator. |
| [`hashcrack`](hashcrack/) | Dictionary and rule-based password-hash cracker (MD5, SHA-1, SHA-256, bcrypt) with hash-type auto-detection and a mutation engine. |
| [`stegotool`](stegotool/) | LSB image steganography: hide and reveal messages, plus statistical detection (chi-square, RS analysis, autocorrelation) that finds hidden data without extracting it. |

> ## Authorized and educational use only
> These tools do real reconnaissance and real password cracking. Use them only on systems, accounts, data and files that you own or have **explicit written permission** to test. Do not use them to access, profile or harass anyone without consent. You are responsible for following the law and the terms of service of any site or API a tool contacts. The software is provided as is, without warranty, and the authors accept no liability for misuse. See `LICENSE`.

## Requirements

- Python 3.12 or newer
- Each tool has its own `requirements.txt` and its own virtual environment, so you can use one without installing the others.

## Setup (any tool)

Replace `<tool>` with `ghostscan`, `logwatch`, `hashcrack` or `stegotool`.

```bash
cd <tool>
python -m venv .venv
source .venv/bin/activate        # macOS / Linux
# .venv\Scripts\activate         # Windows (cmd)
# .venv\Scripts\Activate.ps1     # Windows (PowerShell)
pip install -r requirements.txt
```

## ghostscan

```bash
python ghostscan.py scan torvalds            # a username
python ghostscan.py scan user@example.com    # an email: adds breach + Gravatar checks
```

Optional API keys go in `ghostscan/config.py` (the key slots ship **empty**; never commit real keys):

- `HIBP_API_KEY`: enables breach lookup (free key at haveibeenpwned.com/API/Key)
- `GITHUB_TOKEN`: raises the GitHub rate limit from 60 to 5000 requests/hour

JSON reports are saved to `ghostscan/output/`. Scans make live network requests.

## logwatch

```bash
python logwatch.py analyze sample_logs/brute_force_easy.log   # format is auto-detected
python logwatch.py scenarios                                  # list attack scenarios
python logwatch.py forge-log brute_force --difficulty hard    # generate a synthetic log
python logwatch.py forge-log web_scan --format web --difficulty medium
```

`sample_logs/` contains pre-generated scenarios (brute force, credential stuffing, impossible travel, insider threat, web scan, slow burn). Thresholds live in `logwatch/config.py`. Impossible-travel checks use the free ip-api.com geolocation service.

## hashcrack

```bash
python hashcrack.py identify 5f4dcc3b5aa765d61d8327deb882cf99          # detect the hash type
python hashcrack.py crack-hash 5f4dcc3b5aa765d61d8327deb882cf99        # auto-detects type
python hashcrack.py crack-hash <sha256hash> --type sha256
python hashcrack.py crack-hash <hash> --no-rules                       # faster, fewer candidates
python hashcrack.py crack-file hashes.txt
python hashcrack.py hash-password dragon --algo md5                    # build a test case
python hashcrack.py generate --difficulty hard --algo sha256           # practice challenge
python hashcrack.py generate --reveal                                  # show the answer
```

bcrypt mode deliberately tries only 200 candidates to show why bcrypt is slow. In PowerShell, wrap bcrypt hashes in **single quotes** (`'$2b$12$...'`) so `$` isn't expanded. The bundled wordlist (`wordlists/common.txt`) is a short list of common passwords.

## stegotool

```bash
python stegotool.py hide cover.png "secret payload" --difficulty easy
python stegotool.py hide cover.png "classified" --difficulty hard --key mypassword
python stegotool.py reveal suspect.png                   # auto-detects difficulty
python stegotool.py scan suspect.png --verbose           # statistical detection only

python stegotool.py scenarios                            # practice scenarios
python stegotool.py generate --scenario data_exfil --difficulty easy
python stegotool.py verify challenges/<image>.png        # scan + reveal, then you give a verdict
```

Difficulty changes how many colour channels carry the payload and how it is encoded (plain text, base64, or XOR + base64). The XOR layer is intentionally weak: it is there to show why XOR is not encryption.

## Why a game?

Every tool has a `forge` / `generate` mode that produces scenarios with a hidden answer, which is what lets the same code power a game. The game itself is a separate, closed-source project and is not part of this repository.

## License

See [`LICENSE`](LICENSE).
