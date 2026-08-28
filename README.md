# Portfolio site — deployment notes

This is a static site (no build step needed). To publish it on your
existing `weston-nyabeze.github.io` repo:

1. Copy `index.html`, `style.css`, and the `files/` folder into the
   root of your `weston-nyabeze.github.io` repo (replace what's there,
   or merge if you already have other pages).
2. Commit and push:
   ```bash
   git add .
   git commit -m "New portfolio design"
   git push origin main
   ```
3. GitHub Pages will rebuild automatically, check
   `https://weston-nyabeze.github.io` in a minute or two.

## What to customize next
- Swap out the GitHub project links (currently pointing at your
  profile) for direct repo links once Khaziq / The Ledger are public,
  or link a live demo if you deploy one.
- Add a headshot: drop an image into `images/`, then add an `<img>`
  tag in the header section of `index.html`.
- The Board EPM and Career Development entries are marked "Client
  work" / "Employer project" instead of linked — swap in a writeup
  link (e.g. a LinkedIn article, like Cynthia's site did) if you want
  to publish more detail on either.
- If you'd rather hand this to Claude Code for further iteration
  (adding a case-study subpage, a blog, dark/light toggle, etc.),
  just point it at this repo — the file structure is plain HTML/CSS,
  no framework to explain.
