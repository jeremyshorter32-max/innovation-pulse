# Innovation Pulse

A weekly, social-feed style innovation briefing for Aldar's executive management. Each Monday it shows what moved worldwide, and in the GCC, across ten subject areas, with a two-line summary and a "why it matters for Aldar" line for each story.

It is a copy of the Robotics Market Pulse page (`intel.html` in [Robotics-App](https://github.com/jeremyshorter32-max/Robotics-App)), widened from robotics to innovation in general. The robotics vendor tracking is left out.

## Business areas (verticals)
A row of tabs across the top lets each part of the business open its own weekly pulse: **All Aldar** (the company-wide briefing, shown first), then **Development**, **Communities**, **Hospitality**, **Education**, **Investments** and **Infrastructure**, following the business structure.

Each vertical gets its own tailored report every week:
- **its own news searches**, global and GCC (`queries`, `gcc_queries` under `verticals` in `pulse/config.json`);
- **its own story picks**, from stories those searches found or that name the vertical's `keywords`, plus a big general shift when it clearly affects that business;
- **its own takeaways, trend radar and "Why it matters for [vertical]" lines**, written by Claude from the vertical's `focus` line (what that business does);
- **its own research tabs**: Big moves, Startups to watch and a category map scored for that vertical.

The page remembers the last vertical each reader chose. Links open straight on one: add `?v=` with `development`, `communities`, `hospitality`, `education`, `investments` or `infrastructure` (for example `https://innopulse.netlify.app/?v=education`). The **Copy brief** button copies the vertical's brief with its link.

Editions published before the verticals were added only have the All Aldar view; their vertical tabs say the report starts with the next edition.

To change a vertical, edit its `focus`, `queries`, `gcc_queries` or `keywords` in `pulse/config.json`. To add one, add an entry there with a new `id` and `label`; it appears as a tab after the next run. Per-vertical research is on by default (`research.per_vertical`); with it off, verticals still get their own stories, takeaways and trends, but the research tabs show only on All Aldar. `vertical_max_searches` and `vertical_max_fetches` set how much web research each vertical gets, and `parallel` how many reports are written at once.

## Subject areas
| Id | Category |
|---|---|
| `proptech` | PropTech & smart buildings |
| `ai` | AI & software |
| `sustainability` | Sustainability & energy |
| `construction` | Construction tech |
| `mobility` | Mobility & smart cities |
| `robotics` | Robotics & automation |
| `retail` | Retail & customer experience |
| `fintech` | Real estate finance & fintech |
| `frontier` | Frontier tech (quantum, space, biotech, materials, fusion) |
| `work_edu` | Future of work & education |

## Where things live
| Path | What it is |
|---|---|
| `index.html` | The page (works on desktop and phone), with the business-area tabs across the top. |
| `data/pulse-feed.json`, `data/pulse-feed.js` | The editions. Written by the weekly job; don't edit by hand. |
| `pulse/innovation_pulse.py` | The weekly job: collects news, picks stories, writes the edition. |
| `pulse/research.py` | Claude's weekly web research (Big moves, Startups to watch, Category map). |
| `pulse/config.json` | Settings: categories, colours, news searches, keywords, feeds, story limits, model. |
| `.github/workflows/innovation-pulse.yml` | Runs the weekly job on GitHub. |
| `netlify.toml` | Netlify settings (no build step). |

## How it updates
Every Monday at 10:00 Abu Dhabi time a GitHub Action runs `pulse/innovation_pulse.py`. A full run, with all seven reports and their research, takes about 30 to 45 minutes. It collects the last 7 days of news from Google News searches (several per category, plus GCC-focused searches) and trade-press feeds, merges stories covered by several outlets, picks the most important ones and commits the new edition to `data/`. Netlify then redeploys. If the run fails or GitHub delays it, it retries every hour through Tuesday afternoon; once the week's edition exists, the retries do nothing. The last 12 weeks stay available in the edition picker.

The briefing is kept broad: at most 4 stories per category (`max_per_category`), up to 30 stories in all (`max_posts`), and at least 8 GCC stories when there are that many worth reading (`gcc_min_posts`).

**Trend memory.** Each edition records how much news each category had and compares it with the previous four weeks. The **Trend radar** panel shows running themes (new, rising, steady or cooling) and each category's news volume against its recent average.

## Turn on AI summaries (recommended)
Add a repository secret named `ANTHROPIC_API_KEY` (GitHub: Settings → Secrets and variables → Actions → New repository secret). Claude then chooses the stories and writes the summaries, takeaways and "why it matters" lines. Without a key, the job still runs but uses a simple rule to pick stories and shows the original headlines.

**Research tabs (needs the API key).** Claude also searches the web itself each week, and the page gains two tabs and a chart:
- **Big moves:** the week's most talked-about innovation announcements, each checked (available today, independently confirmed, in commercial use, proven at scale, fits the GCC), with a verdict and a line for leadership.
- **Startups to watch:** young companies in our categories, scored 0–4 on funding, named customers, scale, partners and route to the GCC. 14+ with named customers: worth exploring; 8–13: watch; under 8: ignore.
- **Category map** (in the Trend radar): market readiness against relevance to Aldar for each category.

Only links Claude actually opened during its research are kept, so the page never shows a made-up source. Web search must be enabled for the organisation in the Claude Console; the number of searches is under `research` in `pulse/config.json`. If research fails, the edition is published without it.

## Change the categories or sources
Edit `pulse/config.json`. Each category has a label, colours, `queries` (news searches) and `keywords` (used to sort stories). To add a category, also add a short name and an icon for it in `SHORT` and `ICONS` near the top of the script in `index.html` (it falls back to a default icon if you don't).

## Run it by hand
In GitHub: Actions → Innovation Pulse → Run workflow (tick "force" to rebuild this week). Locally: `pip install -r pulse/requirements.txt`, then `python pulse/innovation_pulse.py` (`--dry-run` prints without saving, `--force` rebuilds this week).

## Deploy on Netlify
1. In Netlify: **Add new site → Import an existing project → GitHub**.
2. Pick this repository, branch `main`.
3. Leave the build command empty and the publish directory as `.`, then deploy.

Every push to `main` redeploys automatically.

## Run locally
`python3 -m http.server` in this folder, then open http://localhost:8000.

## Phone app (home screen)
The site installs like an app. On iPhone, open it in Safari, tap **Share → Add to Home Screen**. It is named "Innovation Pulse" and opens full screen.
