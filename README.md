# Kormo AI — AI-Powered Job Portal & Skill Matching

A capstone project that turns a resume into ranked job matches and a personalised skill-gap plan.
Everything runs in the browser: no backend and no API keys, and the resume never leaves the device.

## Features

- **Resume parsing**: PDF (via pdf.js) or TXT upload, or paste text. Extracts name, email, headline and years of experience.
- **Skill extraction**: a 72-skill taxonomy with aliases (e.g. `reactjs`, `k8s`, `sklearn`), using longest-match-first, boundary-aware matching.
- **Hybrid match score**, which is fully explainable:
  | Component | Weight |
  |---|---|
  | Required skills coverage | 55% |
  | Nice-to-have skills coverage | 20% |
  | TF-IDF cosine similarity (resume ↔ job text) | 15% |
  | Experience fit | 10% |
- **Skill-gap plan**: every missing skill links to a free official course with an estimated number of hours.
- **Candidate dashboard**: KPI tiles, a radar chart (your skills vs. market demand), top missing skills and saved jobs.
- **Recruiter view**: define a role and instantly rank a candidate pool with the same engine.
- **Light & dark themes**: a sun/moon toggle in the navbar. The first visit follows the system setting, the choice is remembered, and an inline script in `index.html` applies it before first paint (no flash).
- **Jobs board**: search, filters (type, level, location), sorting by match, date or salary, and bookmarks (stored in localStorage).

## Getting started

Requires **Node.js 18+**.

```bash
npm install
npm run dev
```

Open http://localhost:5173. To try it quickly, go to **Analyze Resume** and click **Use sample resume**.

Production build:

```bash
npm run build
npm run preview
```

## Project structure

```
kormo-ai/
├── public/                 favicon
├── src/
│   ├── assets/             logo + background pattern (SVG)
│   ├── components/
│   │   ├── layout/         Navbar, Footer, Layout, PageTransition, ScrollToTop
│   │   ├── ui/             Button, Panel, MatchRing, SkillChip, SkillPicker, Modal, ProgressBar, Reveal…
│   │   ├── home/           HeroSection, HeroVisual, HowItWorks, FeatureCard, StatsBand, CtaSection
│   │   ├── jobs/           JobCard, FilterBar
│   │   ├── analysis/       ResumeUploader, AnalysisProgress, MatchBreakdown, SkillGapPanel
│   │   └── dashboard/      DashboardCard, RadarChart (pure SVG), BarList
│   ├── context/            ProfileContext (profile + saved jobs), ThemeContext (light/dark)
│   ├── data/               skills taxonomy, jobs, candidates, courses, sample resume
│   ├── hooks/              useMatches, useLocalStorage, useCountUp, useDebounce, useDocumentTitle
│   ├── pages/              Home, Jobs, JobDetails, Analyze, Dashboard, Recruiter, NotFound
│   ├── styles/             variables.css (light + dark design tokens), globals.css, forms.css
│   ├── utils/              skillExtractor, tfidf, matcher, skillGap, fileParser, format
│   ├── App.jsx             routes + animated page transitions
│   └── main.jsx            entry point
├── index.html
├── package.json
└── vite.config.js
```

Each component has its own `*.module.css` file next to it (CSS Modules).

## The AI engine (`src/utils`)

1. `skillExtractor.js` normalises the text, then matches every alias with a regex like `(?<![a-z0-9+#])alias(?![a-z0-9+#])`, trying longer aliases first. Consumed text is blanked out, so "react native" is never also counted as "react".
2. `tfidf.js` handles tokenisation, stop-word removal, IDF over the job corpus, sub-linear TF weighting and cosine similarity.
3. `matcher.js` combines skill coverage, semantic similarity and experience into the weighted score, and ranks jobs for a candidate or candidates for a job.
4. `skillGap.js` builds the learning plan, the market-wide missing-skill ranking and the per-category coverage used by the radar chart.

### Ideas to extend it for the capstone

- Replace TF-IDF with sentence embeddings (e.g. Sentence-BERT through a small FastAPI service) and compare the accuracy of the two.
- Scrape real listings (for example from Bdjobs) into `data/jobs.js` or a database.
- Add authentication and a real backend (Node/Express + MongoDB) for recruiters to post jobs.
- Evaluate the ranking with precision@k against a hand-labelled set of resume–job pairs.

## Tech stack & credits

- [React 18](https://react.dev) + [Vite 5](https://vitejs.dev)
- [React Router 6](https://reactrouter.com)
- [Framer Motion](https://motion.dev) for page transitions, scroll reveals, parallax and micro-interactions
- [Lucide](https://lucide.dev) icons (ISC license)
- [pdf.js](https://mozilla.github.io/pdf.js/) for PDF text extraction (Apache-2.0)
- Fonts: [Plus Jakarta Sans](https://fonts.google.com/specimen/Plus+Jakarta+Sans) and [Inter](https://fonts.google.com/specimen/Inter) via Google Fonts (OFL)

All companies and candidates in `src/data` are fictional demo data.
