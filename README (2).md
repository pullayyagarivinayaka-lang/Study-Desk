# Study Desk

A single-page, all-in-one study dashboard for students. Search any topic and get a sourced summary, a related image, a link to video lessons, and a personal notebook — all on one page, with no sign-up, no backend, and no API keys.

**Live demo:** deploy with GitHub Pages (steps below) — it's one static file.

---

## What it actually does (and how, honestly)

Everything in this app calls a **free, public, no-key API** directly from the browser, so it keeps working for anyone who forks the repo — nobody has to add a secret key for it to function.

| Feature | How it works | Why it's trustworthy |
|---|---|---|
| Topic summary | Fetches `https://en.wikipedia.org/api/rest_v1/page/summary/{topic}` | Real encyclopedia text, licensed CC BY-SA — attributed in the footer |
| "Proof of answer" | Every result card shows a **call number**, the article's **real last-edited date**, and a **direct link to the full source article** | You can click through and verify the claim yourself instead of trusting a black box |
| Didn't find it? | Falls back to Wikipedia's `opensearch` endpoint and shows clickable "did you mean" suggestions | No dead ends — a near-miss search still gets you somewhere useful |
| Related image | Uses the topic's own Wikipedia thumbnail image | The image is *actually about the topic*, not a generic stock photo |
| Video lessons | A "Watch on YouTube" button opens a real YouTube search for `"{topic} explained"` in a new tab | No API key is required for a *search link* (unlike the YouTube Data API, which needs one) — this is the honest, always-working version |
| Full forms & acronyms | The same search box resolves acronyms — searching "CPU" or "DNA" lands you on Wikipedia's article for the full term | Wikipedia already indexes acronyms as redirects to their full-form article, so no separate lookup service is needed |
| Reference Desk (textbooks, workbooks, worksheets) | Every result card ends with a row of links that open a live search for that exact topic on **Khan Academy**, **OpenStax**, **CK-12**, **LibreTexts**, and **NCERT** | All five are free, openly licensed education providers. Khan Academy uses its real public search URL (`khanacademy.org/search?page_search_query=`); the other four use a Google `site:` search scoped to that domain, since they don't expose a stable public query API — either way, the link always leads to real content on a legitimate source, never a pirated PDF |
| Notes | A ruled notebook per topic, saved with `localStorage` | Notes persist across visits, on your device only — nothing is sent anywhere |
| Saved cards | A bookmark list, also `localStorage` | Quick way back to topics you're revising |
| Subject catalog | A sidebar of ready-made topics across Math, Physics, Chemistry, Biology, History, CS, Economics, English | Gives you somewhere to start instead of a blank search box |
| Live search suggestions | As you type (2+ characters), a dropdown of matching Wikipedia titles appears, navigable with ↑ / ↓ / Enter / Esc | Faster than typing a full title and pressing search |
| Recently viewed | The last 6 topics you looked up appear as chips above the search bar | One click back to something you were just reading |
| Reading lamp toggle | A light/dark theme switch in the header, remembered across visits | Comfortable for late-night study sessions |
| Toast notifications | Small confirmations slide in when you save/remove notes or cards | Clear feedback without interrupting your reading |
| Loading skeleton | A shimmering placeholder card while a summary is being fetched | The page never feels frozen or broken while waiting on the network |
| Keyboard shortcut | Press `/` anywhere on the page to jump into the search box | Faster navigation for keyboard users |

### About the video links
There is no free, key-less API that returns embeddable YouTube search results — the YouTube Data API requires an API key and a quota. Rather than fake it with hardcoded video IDs (which would go stale or be wrong), Study Desk gives you a **real, live YouTube search link** for the exact topic. It always works, and you always get current results.

If you *do* want embedded video search inside the page, you can add your own YouTube Data API v3 key and extend `runSearch()` in `index.html` — see the comment block at the top of the `<script>` section for where to plug it in.

### About the Reference Desk (textbooks / workbooks / worksheets)
Actual textbook and worksheet PDFs are copyrighted, and most "free PDF download" sites for them are unlicensed copies. Rather than link to those, or fabricate files that don't exist, Study Desk's Reference Desk points to five **real, free, openly licensed** education providers and runs a live search for your exact topic on each:

- **Khan Academy** — lessons and practice exercises, via its real public search URL.
- **OpenStax** — peer-reviewed, Creative-Commons-licensed college textbooks (Rice University).
- **CK-12** — free K-12 textbooks ("FlexBooks") and practice worksheets.
- **LibreTexts** — an open-textbook library funded by the US Department of Education.
- **NCERT** — India's official, government-published school textbooks, free to the public.

Four of these don't expose a stable public search API, so their links use a Google `site:` search scoped to that one domain — this is a completely standard, transparent technique (it's exactly what typing `site:openstax.org photosynthesis` into Google does), not a workaround or a guess. Every link lands on the real site, never a pirated copy.

---

## Project structure

```
study-desk/
├── index.html   ← the entire app (HTML + CSS + JS, no build step)
└── README.md    ← this file
```

One file, no dependencies to install, no `npm build`. It only reaches out to two Wikipedia endpoints and Google Fonts at runtime.

---

## Run it locally

Just open the file:

```bash
# clone your repo, then:
open index.html        # macOS
start index.html       # Windows
xdg-open index.html    # Linux
```

Or serve it (recommended, some browsers restrict `fetch` on `file://` URLs):

```bash
python3 -m http.server 8000
# then visit http://localhost:8000
```

---

## Deploy on GitHub Pages

1. Create a new GitHub repository (or use an existing one).
2. Add `index.html` and `README.md` to the repo root.
3. Commit and push:
   ```bash
   git init
   git add index.html README.md
   git commit -m "Add Study Desk dashboard"
   git branch -M main
   git remote add origin https://github.com/<your-username>/<your-repo>.git
   git push -u origin main
   ```
4. In your repo: **Settings → Pages → Build and deployment → Source → Deploy from a branch**, choose `main` and `/ (root)`, then **Save**.
5. Your site goes live at:
   ```
   https://<your-username>.github.io/<your-repo>/
   ```
   (Pages usually takes 1–2 minutes to publish after the first push.)

---

## Customizing

- **Add or change subjects/topics:** edit the `SUBJECTS` array near the top of the `<script>` block in `index.html`.
- **Change the look:** all styling is in the `<style>` block — colors are defined once as CSS custom properties at the top (`--paper`, `--ink`, `--teal`, `--mustard`, etc.), so retheming is a matter of changing a handful of values.
- **Swap the source language/wiki:** change `en.wikipedia.org` to another language's subdomain (e.g. `es.wikipedia.org`) in the two `fetch` URLs inside `fetchSummary` and `fetchSuggestions`.

---

## Known limitations (stated plainly, not buried)

- Wikipedia doesn't have a thumbnail for every article — when it doesn't, the card says so instead of showing a broken image.
- Notes and saved cards are stored in **your browser's local storage**, per device/browser — they don't sync across devices and will be lost if you clear site data.
- Video results are a search link, not embedded players (see explanation above).
- This is a static front-end only; there's no login, no server, and no shared/multi-user data — it's a personal study tool, not a classroom platform.
- The Reference Desk sends you to a *search results page* on each partner site, not directly to a specific file — the exact textbook, workbook, or worksheet you need may take one more click to find, because Study Desk doesn't fabricate direct file links to content it hasn't verified exists.

---

## License

Use, fork, and modify this project freely for personal or educational purposes. Wikipedia text is reused under **CC BY-SA 4.0**, with attribution and a source link shown on every result card, as its license requires.
