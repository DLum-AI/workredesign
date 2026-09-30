# WorkRedesign.sg

A free, static web tool that walks Singapore hirers through five stages:
**Awareness → Education → Execution → Grant Mapping → Talent Match** —
turning "we have a staffing headache" into "here's a fractional/job-share
role, here's what funds it, here's who could fill it."

No backend, no build step, no database. Pure HTML/CSS/vanilla JS, using
the browser's `localStorage` to carry a hirer's answers between pages.
This matches the MVP scope: fast to publish, free to run, nothing to
maintain beyond the content in `js/data.js`.

> **Admin portal and talent library added.** This site now also has an
> optional backend (Supabase) and admin/CMS layer for managing content, the
> talent profile library, and account approvals — see
> **[README-ADMIN.md](./README-ADMIN.md)** for that setup. The public site
> below still works standalone without it.

## Folder structure

```
workredesign-sg/
├── index.html          Stage 1 — "Is this job ready for redesign?" quiz
├── task-map.html        Stage 2/3 — task deconstruction + classification tool
├── grants.html           Stage 4 — grant info + application-readiness checklists
├── match.html            Stage 5 — talent match request form
├── about.html            Framework explainer / credibility page
├── css/
│   └── styles.css        All shared styling (one file, CSS variables at the top)
├── js/
│   ├── data.js            ← EDIT THIS FIRST: org name, contact email, grant text, quiz questions
│   ├── layout.js          Renders the shared header/nav/footer on every page
│   ├── quiz.js            Stage 1 logic
│   ├── taskmap.js         Stage 2/3 logic (task classification + recommendation engine)
│   ├── grants.js          Stage 4 checklist logic
│   └── match.js           Stage 5 form handling (mailto fallback, or POST to a form endpoint)
└── README.md              This file
```

## Before you publish: things to customise

Everything content-specific lives in **`js/data.js`** — you shouldn't need
to touch the HTML files for day-to-day updates:

- `SITE.orgName` — your social enterprise's real name
- `SITE.contactEmail` — the inbox that should receive match requests
- `SITE.matchFormEndpoint` — leave blank to use the built-in `mailto:` fallback
  on the Stage 5 form, or set it to a [Formspree](https://formspree.io) or
  [Netlify Forms](https://www.netlify.com/platform/core/forms/) endpoint to
  collect submissions properly instead of relying on the hirer's email client
- `GRANTS` — grant names, funding figures, and checklist items. **Verify these
  against the official pages before publishing** — scheme terms are updated
  periodically:
  - [Career Conversion Programmes](https://www.ourworkforce.gov.sg/programmes-directory/programme/career-conversion-programmes)
  - [Workforce Development Grant (Job Redesign+)](https://www.ourworkforce.gov.sg/programmes-directory/programme/workforce-development-grant-job-redesign-plus)
  - [Mid-Career Pathways Programme](https://www.ourworkforce.gov.sg/programmes-directory/programme/mid-career-pathways-programme)
- `QUIZ_QUESTIONS` — the Stage 1 awareness quiz questions/scoring

## Publishing on GitHub Pages

1. Create a new repository on GitHub (e.g. `workredesign-sg`) and push this
   folder's contents to its `main` branch:

   ```bash
   cd workredesign-sg
   git init
   git add .
   git commit -m "Initial WorkRedesign.sg portal"
   git branch -M main
   git remote add origin https://github.com/<your-username>/<your-repo>.git
   git push -u origin main
   ```

2. On GitHub, go to **Settings → Pages**.
3. Under **Build and deployment → Source**, choose **Deploy from a branch**.
4. Under **Branch**, choose `main` and folder `/ (root)`, then **Save**.
5. GitHub will publish the site at `https://<your-username>.github.io/<your-repo>/`
   within a minute or two — refresh the Pages settings page for the link.

No GitHub Actions workflow, Jekyll config, or build step is required — this
is a plain static site and GitHub Pages serves it as-is.

### Using a custom domain (optional)

If you want this at your own domain (e.g. `workredesign.sg`):
1. Add a `CNAME` file to the repo root containing just your domain name.
2. In your DNS provider, point the domain at GitHub Pages per
   [GitHub's custom domain guide](https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site).
3. Re-enable HTTPS in **Settings → Pages** once DNS has propagated.

## What's intentionally NOT included (per the MVP scope)

- **No login/accounts** — the tool is fully usable without signing up.
- **No payment processing** — nothing on this site charges anyone.
- **No live talent directory / fuzzy search** — Stage 5 is a request form
  routed to your team for manual matching, matching the "Stage A: don't build
  search yet" recommendation for early-stage pool sizes. See the phased
  scaling plan (Postgres + `pg_trgm` fuzzy search, then `pgvector` semantic
  matching) for when to add this as the PMET pool and match-request volume grow.
- **No CorpPass or government API integration** — none is publicly offered
  for this use case; the grants pages link out to the official application
  channels rather than trying to replicate them.

## Data privacy note

This MVP collects no PMET personal data at all — Stage 5 only captures
hirer-side contact details, sent by the hirer's own email client (or your
chosen form endpoint) directly to your team. If you extend this into a
self-serve talent directory later, build in explicit PMET consent tracking
and a gated-reveal flow before publishing any PMET-identifying information,
per Singapore's Personal Data Protection Act (PDPA).

## Local preview before publishing

Because the site uses `fetch`-free, inline JS data (no JSON fetching), you
can preview it by simply opening `index.html` in a browser — no local server
required. If you later add features that do require a server (e.g. testing
a form endpoint), any simple static server works:

```bash
python3 -m http.server 8000
# then visit http://localhost:8000
```
