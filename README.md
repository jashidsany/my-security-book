# my-security-book
Offensive security methodology &amp; reference book

**Live at:** [book.jashidsany.com](https://book.jashidsany.com)

## How It Works

- Content is written in Markdown files in `src/`
- `src/SUMMARY.md` defines the sidebar navigation
- Push to `main` → Amplify auto-builds with mdBook → live in ~2 minutes
- Dark theme with full-text search, code copy buttons, keyboard navigation

## Local Preview

```bash
# Install mdBook (one-time)
brew install mdbook        # macOS
cargo install mdbook       # any OS with Rust

# Preview
mdbook serve --open
# Opens at http://localhost:3000
```

## Updating Content

### Add a technique to an existing page

Edit any `.md` file in `src/` and add:

```markdown
### Name of Technique

\```bash
command --to-run $IP
\```

> When and why to use this

**Why:** What this achieves

<details>
<summary>Example Output</summary>

\```
example output here
\```
</details>

---
```

Commit and push. Amplify auto-deploys.

### Add a new page

1. Create a `.md` file in the appropriate directory
2. Add content
3. Add to `src/SUMMARY.md`
4. Commit and push

### Add a new section

1. Create directory + `index.md`
2. Add to `src/SUMMARY.md`
3. Commit and push

## Project Structure

```bash
oscp-book/
  amplify.yml            # Amplify build config
  book.toml              # mdBook configuration
  theme/
    custom.css           # Dark terminal theme
  src/
    SUMMARY.md           # Table of contents → sidebar
    README.md            # Landing page
    recon/               # Recon & Scanning
    ports/               # Port Enumeration (16 pages)
    web-enum/            # Web Enumeration
    web-attacks/         # Web Attacks (10 pages)
    quick-ref/           # Wordlists, transfers, hashes
    linux-pe/            # Linux PrivEsc (9 pages)
    windows-pe/          # Windows PrivEsc (8 pages)
    ad/                  # Active Directory (7 pages)
    pivoting/            # Pivoting & Tunneling (6 pages)
    speed/               # Speed Hacks
    decisions/           # Decision Trees
    rabbit-holes/        # Rabbit Hole Warnings
    creds/               # Credential Tracking
```

## Companion Tools

- **[jashidsany.com](https://jashidsany.com)** — Blog & portfolio
