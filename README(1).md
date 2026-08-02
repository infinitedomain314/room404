# home-lab

A home cybersecurity lab built from a laptop that was headed for a skip and a Raspberry Pi. Documenting the journey from complete beginner to (hopefully) something more, one exercise at a time.

Full narrative writeups live on the blog. This repo holds the technical detail, commands, payloads, and raw findings, for anyone who wants the "how" without the story around it.

**Blog:** [infinitedomain.hashnode.dev](https://infinitedomain.hashnode.dev)
**Instagram:** [@infinitedomain314](https://instagram.com/infinitedomain314)

---

## Lab setup

| Machine | Role | Details |
|---|---|---|
| Lenovo 100e | Attacker / daily driver | Arch Linux, KDE, KVM/libvirt hypervisor |
| Raspberry Pi 4 (`kalipi`) | Target | Headless Raspberry Pi OS, DVWA |
| Dell laptop | Windows lab | AD domain lab (`lab.local`), DC01/CLIENT01 |

Full setup writeup: [Setting Up a Home Lab Without Breaking the Bank](https://infinitedomain.hashnode.dev/setting-up-a-home-lab-without-breaking-the-bank-pi-dumpster-laptop)

---

## Writeups

| Writeup | Vulnerability / topic | Blog post |
|---|---|---|
| [`dvwa-sql-injection.md`](./writeups/dvwa-sql-injection.md) | SQL Injection (DVWA, Low) | [My First SQL Injection](https://infinitedomain.hashnode.dev) |
| [`bash-scripting-detour.md`](./writeups/bash-scripting-detour.md) | Bash scripting, `$PATH`, exit codes | [Teaching Myself Bash by Accident](https://infinitedomain.hashnode.dev) |
| `dvwa-brute-force.md` | Brute force (DVWA, Low) | *coming soon* |
| `dvwa-command-injection.md` | Command Injection (DVWA, Low) | *in progress* |

More writeups get added as the lab work progresses. Each one follows the same format: environment details, step-by-step payloads and results, root cause, and remediation.

---

## TryHackMe rooms

| Room | Category / difficulty | Blog post |
|---|---|---|
| [`thm-room404.md`](./tryhackme/thm-room404.md) | Web, Very Easy | [The Room That Wasn't on the Floor Plan](https://infinitedomain.hashnode.dev) |

Free-tier rooms, filtered Red Team + Easy, worked through outside of DVWA as a second practice track. Same writeup format as the vulnerability writeups above.

---

## Scripts

| Script | Purpose |
|---|---|
| [`battery.sh`](./scripts/battery.sh) | Reads live battery percentage and charging status via `/sys` |
| [`check_apache`](./scripts/check_apache) | Checks whether Apache is running on the Pi, starts it if not |

---

## Structure

```
home-lab/
├── writeups/     # DVWA vulnerability writeups
├── tryhackme/    # TryHackMe room writeups
├── scripts/      # small utility scripts from the lab
└── notes/        # general notes, WIP material
```

---

## Why this exists

No expensive gear, no paid course, just a laptop nobody wanted, a Raspberry Pi, and free software. This repo is the technical record of that process, mistakes included.
