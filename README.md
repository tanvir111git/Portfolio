# Md. Tanvir Hossain — Developer Portfolio

A single-page portfolio built with plain **HTML / CSS / JS** — no build step,
no npm install, no framework. Open it and it works. Education, Experience,
and Projects are all **data-driven**: edit a JSON file, the page updates
itself, newest entry automatically on top.

**Live signature features:**
- Animated canvas background (drifting connected nodes, reacts to mouse) + a soft glowing blob behind the hero terminal
- Custom cursor with hover states
- Typewriter terminal in the hero that "runs commands" on load, plus a rotating headline phrase
- Fully **dynamic Education & Experience timelines** — ongoing entries are detected from real dates, not a manual flag, and always sort to the top automatically
- Fully **dynamic, filterable Projects grid** — sorted newest-first from JSON, with filter buttons generated from whatever categories exist in the data
- Subtle 3D tilt-on-hover effect on project cards (skipped automatically on touch devices / reduced-motion)
- Scroll-reveal animations, animated stat counters, animated skill bars
- Fully working contact form UI (client-side validated, ready to wire to a backend)
- Fully responsive, keyboard-accessible, respects `prefers-reduced-motion`

---

## 1. File structure

```
portfolio/
├── index.html                  ← page shell — Education/Experience/Projects are empty containers filled in by JS
├── 404.html                     ← custom not-found page
├── manifest.json                 ← basic web app manifest
├── assets/
│   ├── css/style.css              ← all styling (design tokens at the top)
│   ├── data/
│   │   ├── education.json          ← ⭐ EDIT to add/update your education history
│   │   ├── experience.json          ← ⭐ EDIT to add/update work/training experience
│   │   └── projects.json             ← ⭐ EDIT to add/update projects
│   └── js/
│       ├── portfolio-data.js          ← offline fallback copy of the JSON above (keep in sync)
│       ├── render.js                  ← fetches the JSON, computes ordering, builds all the HTML
│       └── main.js                    ← everything else (nav, cursor, canvas bg, contact form, etc.)
└── README.md
```

## 2. How the dynamic sections work

You never hand-edit HTML for Education, Experience, or Projects — `render.js`
fetches the JSON and builds all of it on page load.

### Ordering — computed, not manual
Every entry has a `startDate` / `endDate` in `"YYYY-MM"` format (e.g.
`"2024-07"`). At page-load time, `render.js` compares each date against
**today's real date**:

- If an entry's `endDate` hasn't happened yet — or it has a `startDate` but
  no `endDate` at all — it's treated as **ongoing** and always sorts first.
  There's no `"current": true` switch to remember to flip off later; it's
  just true because the calendar says so right now. Once your `endDate`
  passes, the badge changes automatically on the next page load.
- Otherwise, entries sort by their most recent date, newest first.
- Older records without precise month/year dates (like an SSC batch year)
  can use a `"batch"` field as a sorting fallback instead — see the SSC entry
  in `education.json` for an example.

### Badges generated for you
- **Education:** an ongoing degree gets a pulsing *"Currently Studying"*
  badge; finished ones get *"Completed"*.
- **Experience:** shows the entry's `"type"` (Training, Internship, Work,
  Freelance, Volunteer) as a badge, with *"• Ongoing"* appended automatically
  if it's still in progress.
- **Projects:** the newest entry gets a *"Latest"* ribbon; filter buttons
  (All / Systems / Web / …) are generated from whatever `category` values
  exist in `projects.json` — add a new category and a new button just appears.

### Adding a new entry
Open the matching JSON file, copy one entry's `{ ... }` block, paste it, fill
in the details, give it a unique `"id"`. You never need to think about
*where* it goes in the file or the page — the dates decide the order.

**Example — you finish your B.Sc. and start an M.Sc. later:** add the new
M.Sc. entry to `education.json` with a `startDate` and no `endDate` yet (it
shows as ongoing immediately), and give your B.Sc. entry a real past
`endDate`. Nothing else to touch — it reorders itself.

### Important — keep the fallback in sync
`assets/js/portfolio-data.js` holds a duplicate copy of the same data as a
safety net for when the page is opened directly as a file (`file://`)
instead of through a local server or a live deployment — browsers block
`fetch()` of local files in that case. **Whenever you edit a JSON file,
make the same edit in `portfolio-data.js`.** On GitHub Pages / Netlify /
Vercel, or with VS Code's Live Server, the JSON files load normally and this
fallback is never used — but it's what keeps the site from breaking if
someone just double-clicks `index.html`.

---

## 3. Edit it locally in VS Code

1. Install **VS Code**: https://code.visualstudio.com
2. Install the **Live Server** extension (by Ritwick Dey) from the Extensions panel.
3. Open the `portfolio` folder in VS Code (`File → Open Folder`).
4. Right-click `index.html` → **Open with Live Server**.
5. Your browser opens the site at `http://127.0.0.1:5500` and **auto-refreshes**
   every time you save a file. This is your live local preview.

### What to personalize
| What | Where |
|---|---|
| Name, title, bio | `index.html` — hero section + About section |
| Education history | `assets/data/education.json` *(and mirror in `portfolio-data.js`)* |
| Experience | `assets/data/experience.json` *(and mirror in `portfolio-data.js`)* |
| Projects | `assets/data/projects.json` *(and mirror in `portfolio-data.js`)* |
| Skills / percentages | `index.html` — Skills section, edit `data-fill="XX"` (0–100) |
| Colors | `assets/css/style.css` — the `:root { ... }` block at the top (change `--accent`, `--warn`, `--bg`, etc.) |
| Social links / email | `index.html` — Contact section, `.contact__social` |
| Contact form backend | `assets/js/main.js` — see the `TODO` comment inside `initContactForm()` |

To make the contact form actually send you emails without a backend, the
easiest free option is **Formspree** (https://formspree.io) — sign up, get a
form endpoint, and paste it into the `fetch()` call already stubbed out in
`main.js`.

---

## 4. Deploy for free (pick one — all take under 5 minutes)

### Option A — GitHub Pages (recommended, simplest ongoing updates)
1. Create a new repo on GitHub, e.g. `portfolio`.
2. In VS Code's terminal (inside the `portfolio` folder):
   ```bash
   git init
   git add .
   git commit -m "Initial portfolio"
   git branch -M main
   git remote add origin https://github.com/YOUR_USERNAME/portfolio.git
   git push -u origin main
   ```
3. On GitHub: **Settings → Pages → Source → Deploy from branch → `main` / `root`** → Save.
4. Your site is live at `https://YOUR_USERNAME.github.io/portfolio/` in ~1 minute.
5. **To update later:** edit files in VS Code, then:
   ```bash
   git add .
   git commit -m "Updated education / projects"
   git push
   ```
   The live site updates automatically within about 30 seconds.

### Option B — Netlify
1. Go to https://app.netlify.com → **Add new site → Deploy manually** and drag
   the `portfolio` folder in, **or** connect your GitHub repo for auto-deploys
   on every push (same workflow as above).
2. Free instant HTTPS + a `yoursite.netlify.app` URL.

### Option C — Vercel
1. Go to https://vercel.com/new, import your GitHub repo.
2. Framework preset: **Other** (it's static, no build command needed).
3. Deploy — you get a `yoursite.vercel.app` URL, auto-redeploys on every push.

### Custom domain (.xyz / .com / .site / etc.)
All three hosts above support free custom domains:
- Buy a cheap domain (Namecheap, Porkbun, etc. — `.xyz` domains are often under $2/yr on promo).
- In GitHub Pages / Netlify / Vercel dashboard → **Domain settings** → add your domain.
- Add the DNS records they show you (usually a CNAME or A record) at your domain registrar.
- HTTPS is provisioned automatically and free on all three.

---

## 5. Using AI tools to keep improving it

Since everything is plain HTML/CSS/JS/JSON, any AI coding tool can edit it directly:

- **Claude Code** (in VS Code's terminal or as an extension): point it at this
  folder and ask for changes in plain English — "add an internship entry to
  experience.json," "make the accent color purple," "add a new project card."
- **GitHub Copilot**: great for inline autocomplete while you hand-edit.
- **ChatGPT / Grok / Claude.ai**: paste a snippet of `index.html`, `style.css`,
  `render.js`, or a JSON file and ask for a targeted change, then paste it back.

Because the CSS uses **design tokens** (the `:root` variables at the top of
`style.css`), the JS is split into small named functions, and content lives
in separate JSON files, any of these tools can make precise edits without
needing to understand the whole codebase.

---

## 6. Browser support & accessibility notes
- Works in all modern evergreen browsers (Chrome, Firefox, Safari, Edge).
- Respects `prefers-reduced-motion` — background animation, the hero blob,
  and the tilt-hover effect are all disabled for users who request it.
- All interactive elements have visible keyboard focus states.
- Semantic HTML (`<header>`, `<section>`, `<article>`, `<footer>`) throughout.
- Dynamic content is HTML-escaped before insertion, so JSON data can never
  break page markup.

Happy shipping.
