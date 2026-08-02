# Room 404 — TryHackMe

| | |
|---|---|
| **Room** | Room 404 (`hh-room404-804573bf`) |
| **Category** | Web |
| **Difficulty** | Very Easy |
| **Points** | 30 |
| **Tags** | Web, Directory Enumeration |
| **Vulnerability class** | CWE-527 — Exposure of Version-Control Repository to an Unauthorized Control Sphere |
| **Objective** | Dump the exposed source code, find the flag |

---

## Briefing

A hotel guest platform ("Byte Lotus") with a hidden, unlisted "room," port 8080 open, hinting at a staging build shipped with more than intended.

---

## Methodology

### 1. Manual recon

Loaded `http://<target>:8080` and inspected the DOM via browser DevTools. Clean HTML/CSS, no JS files visible in the rendered DOM.

Checked the Network tab on page refresh, only the base HTML and favicon loaded, no XHR/fetch calls. Confirmed a genuinely static-feeling front end rather than a red herring.

### 2. Full page source

Viewed the raw page source (`Ctrl+U`) rather than relying on the DOM inspector alone. Surfaced two details missed in the rendered view:

- `<a href="/booking">Reserve a stay</a>` — an internal path reference
- Footer text: `"guest experience platform · build staging"`

The word **staging** was the key signal.

### 3. Checking for an exposed `.git` directory

Staging builds commonly leak version control artifacts into the web root:

```
http://<target>:8080/.git/
```

Returned an **open directory listing**, `COMMIT_EDITMSG`, `HEAD`, `config`, `objects/`, `refs/`, all directly browsable. Confirmed the vulnerability class immediately.

### 4. First dump attempt — `wget --mirror` (failed)

```bash
wget --mirror --no-parent -P ~/thm/room404-git http://<target>:8080/.git/
```

Partially failed. `--mirror` implies `-N` (timestamping), which caused critical files (`HEAD`, `config`, `index`, and loose object blobs) to return `304 NOT MODIFIED` and get skipped rather than downloaded.

**Why:** `wget` isn't built to understand the git object graph, it just mirrors a directory listing as HTML. Directory-index pages can collide with real filenames of the same name (e.g. `.git/index` vs. wget's own generated `.git/index.html`), and timestamp-based skipping compounds the problem against a listing that looks "already fetched" from a prior test request.

**Takeaway:** `wget --mirror` is not a reliable tool for dumping an exposed `.git` directory, despite being the obvious first instinct. It fails silently (304s, not hard errors), which makes it worse than an outright crash.

### 5. Correct tool — `git-dumper`

```bash
pip install git-dumper --break-system-packages
git-dumper http://<target>:8080/.git/ ~/thm/room404-git
```

`git-dumper` parses `HEAD`/`refs` to determine exactly which objects are needed, fetches them individually, sanitizes `.git/config`, and runs `git checkout` to produce a clean working tree.

**Result:** succeeded cleanly. Recovered `app.js`, `index.html`, `README.md`.

### 6. Reading recovered files

Read every recovered file rather than assuming the flag would be in the most "technical" one:

```bash
cat app.js        # front-end stub, no secrets
cat index.html     # matches the live site, no surprises
cat README.md      # flag was here
```

---

## Flag

```
THM{byt3_l0tus_n3v3r_f0rg3ts}
```

Found in `README.md`, explicitly labeled `Staging flag (remove before launch)`, never removed before the "launch." That's the room's joke, and also a very real-world mistake category, staging artifacts shipping to production.

---

## Tools used

- Browser DevTools (Inspector, Network tab, View Source)
- `wget --mirror` (attempted, failed, documented rather than omitted)
- `git-dumper` (succeeded)
- Standard `git` / `cat` for reading the recovered repository

---

## Key lessons

- Exposed `.git` directories are a real, common misconfiguration class, not just a CTF trope. This room is effectively a clean simulation of a real incident category.
- Manual recon before automation paid off. The footer text clue ("build staging") led directly to the right guess, skipping a blind directory brute-force entirely, worth noting since the room's own tag ("Directory Enumeration") suggests brute-forcing is the "intended" path.
- `wget --mirror` is not a reliable `.git`-dumping tool. Worth a specific callout since it's the obvious built-in choice, and it fails in a non-obvious way.
- Secrets and flags aren't always in the most technical-looking file. Check READMEs and comments, not just code.

---

*Part of the [home-lab](https://github.com/infinitedomain314/home-lab) series — TryHackMe writeups.*
