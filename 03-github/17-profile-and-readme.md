# GitHub Profile and README

**Difficulty:** 🟢 Beginner | **Time:** 35 minutes

Your GitHub profile is your developer homepage. It is often the first thing a recruiter, open source maintainer or potential collaborator looks at when they search for you. A well-built profile with a good README, well-chosen pinned repositories and consistent activity communicates more about your skills and professionalism than any CV bullet point. This file covers everything from the basic profile setup to building a standout profile README with dynamic stats, skill badges and animated components.

---

## Table of Contents

- [Your GitHub profile page](#your-github-profile-page)
- [Profile settings - what to fill in](#profile-settings---what-to-fill-in)
- [The profile README - how it works](#the-profile-readme---how-it-works)
- [What renders in a profile README](#what-renders-in-a-profile-readme)
- [Creating the special repository](#creating-the-special-repository)
- [No-code generators - start here](#no-code-generators---start-here)
- [Profile README components](#profile-readme-components)
- [GitHub Readme Stats](#github-readme-stats)
- [Streak stats](#streak-stats)
- [Skill icons](#skill-icons)
- [Shields.io badges](#shieldsio-badges)
- [Capsule Render headers and footers](#capsule-render-headers-and-footers)
- [Typing SVG animated text](#typing-svg-animated-text)
- [Activity graph](#activity-graph)
- [Profile trophies](#profile-trophies)
- [Contribution snake animation](#contribution-snake-animation)
- [Visitor counter](#visitor-counter)
- [Dark and light mode support](#dark-and-light-mode-support)
- [Pinned repositories](#pinned-repositories)
- [What recruiters actually look at](#what-recruiters-actually-look-at)
- [Complete template - beginner](#complete-template---beginner)
- [Complete template - intermediate](#complete-template---intermediate)
- [Organisation profile READMEs](#organisation-profile-readmes)
- [Performance and accessibility](#performance-and-accessibility)
- [Try It Yourself](#try-it-yourself)
- [Common Mistakes](#common-mistakes)
- [Summary](#summary)
- [Sources and Further Reading](#sources-and-further-reading)

---

## Your GitHub Profile Page

Your profile page at `github.com/YOUR_USERNAME` shows:

- Your profile photo, name, bio, location, website and social links
- Your contribution graph (green squares calendar)
- Your pinned repositories (up to 6)
- Your GitHub achievements
- Your organisations
- Your recent activity
- Your profile README (if you have set one up) - displayed above everything else

The profile README is the most powerful customisation option. It is a full Markdown file that renders at the very top of your profile, before the pinned repos. It is the first thing visitors see.

---

## Profile Settings - What to Fill In

Go to **Settings → Profile** to edit your profile.

**Photo:** A clear, recognisable image. A professional photo works well but a consistent avatar or illustration is also fine. Something is always better than the default Identicon.

**Name:** Your real name or professional name - the name you use on your CV and LinkedIn.

**Bio:** 160 characters maximum. Be specific. State your role, what you are working on or what you care about. Examples:

- `Electronic engineering & CS student | Building tools for developers | Python, C, JavaScript`
- `Software engineer at @COMPANY | Open source contributor | Interested in distributed systems`

**Pronouns:** Optional. Only visible to signed-in GitHub users.

**Company:** Your employer, university or organisation. Use `@ORG_NAME` to link to a GitHub organisation.

**Location:** Your city and country. Helps in networking and community context.

**Website:** Your most important link - portfolio, personal site or LinkedIn. Only one link is supported.

**Social accounts:** Link Twitter/X and LinkedIn directly.

> [!TIP]
> Keep your profile up to date. An old employer or outdated bio looks neglected. Review it every few months.

---

## The Profile README - How it Works

GitHub displays the `README.md` from a repository that has the **exact same name as your username** at the top of your profile.

**The four requirements that must all be true:**

1. The repository name matches your username **exactly** (case-sensitive)
2. The repository is **public**
3. It contains a `README.md` in the root directory
4. The file is non-empty

When you create this repository, GitHub shows a banner: *"You found a secret! ✨ YOUR_USERNAME/YOUR_USERNAME is a special repository."*

If any condition breaks - the repo is made private, the README is deleted, the repo is renamed - the profile README disappears silently.

---

## What Renders in a Profile README

GitHub's Markdown renderer supports most Markdown features plus a limited subset of HTML. Understanding what works and what does not prevents frustration.

**Works:**

- All standard Markdown: headings, bold, italic, lists, code blocks, tables, images, links
- HTML tags: `<img>`, `<a>`, `<div>`, `<p>`, `<details>`, `<summary>`, `<table>`, `<picture>`
- The deprecated `align` attribute on `<img>` and `<div>` (still the main way to centre content)
- GitHub alert syntax: `> [!NOTE]`, `> [!TIP]` etc.
- Emoji shortcodes: `:rocket:`, `:wave:`, `:star:`

**Does not work (stripped by GitHub's sanitiser):**

- CSS - no `<style>` tags, no `style=""` attributes
- JavaScript - no `<script>` tags
- `<iframe>` embeds
- Inline SVG
- Base64-encoded images

> [!NOTE]
> The workaround for custom CSS/animations is to embed HTML and CSS inside an SVG file using `<foreignObject>`, then reference that SVG as an image. This enables animations and even `prefers-color-scheme` dark/light switching.

---

## Creating the Special Repository

**Step 1.** Go to `github.com/new`

**Step 2.** Set the repository name to exactly your GitHub username. GitHub shows the special confirmation banner.

**Step 3.** Set visibility to **Public**.

**Step 4.** Check **Add a README file**.

**Step 5.** Click **Create repository**.

**Step 6.** Edit `README.md` - click the pencil icon to edit directly on GitHub, or clone it locally.

**Step 7.** Commit and push. Visit your profile to see the README displayed.

---

## No-Code Generators - Start Here

If you want a great profile README quickly without writing everything from scratch, use a generator first and customise from there.

### GPRM - GitHub Profile README Maker

**[gprm.itsvg.in](https://gprm.itsvg.in)** - the most comprehensive generator, trusted by 92,000+ users.

GPRM walks you through a seven-step wizard:

1. **Username** - enter your GitHub username to auto-populate data
2. **Introduction** - bio prompts (what you are working on, learning, looking to collaborate on, contact info, fun fact)
3. **GitHub stats** - toggle which stats to show, choose themes
4. **Social links** - pick from 30+ platforms and add your URLs
5. **Skills** - select from 300+ technology icons
6. **Extras** - trophies, visitor counter, donation links
7. **Generate** - copy the Markdown or download as `.md`

### Other generators

**[rahuldkjain.github.io/gh-profile-readme-generator](https://rahuldkjain.github.io/gh-profile-readme-generator)** - clean interface with live preview, supports all major widgets.

**[awesome-github-profile-readme](https://github.com/abhisheknaiidu/awesome-github-profile-readme)** - not a generator but a curated gallery of real profile READMEs organised by style (animated, simple, detailed, dynamic). Browse it for inspiration before building your own.

---

## Profile README Components

The following sections cover every major component with copy-paste Markdown code. Replace `YOUR_USERNAME` throughout with your actual GitHub username.

---

## GitHub Readme Stats

**Repository:** `github.com/anuraghazra/github-readme-stats` (78,000+ stars)

The most widely used profile README widget. Generates dynamic stat cards from the GitHub API.

> [!NOTE]
> The public instance at `github-readme-stats.vercel.app` can be slow or rate-limited due to heavy shared usage. For a reliable profile, either self-host on Vercel (free) or use GitHub Actions to generate static SVGs daily.

### Stats card

Shows total stars earned, total commits, PRs, issues and a calculated rank (S, A+, A, B, C):

```markdown
![YOUR_USERNAME's GitHub stats](https://github-readme-stats.vercel.app/api?username=YOUR_USERNAME&show_icons=true&theme=radical&include_all_commits=true)
```

### Top languages card

```markdown
![Top Languages](https://github-readme-stats.vercel.app/api/top-langs/?username=YOUR_USERNAME&layout=compact&langs_count=8&theme=radical)
```

**Layout options:** `normal`, `compact`, `donut`, `donut-vertical`, `pie`

### Side by side cards

```html
<div align="center">
  <img height=200 align="center" src="https://github-readme-stats.vercel.app/api?username=YOUR_USERNAME&show_icons=true&theme=radical&include_all_commits=true" />
  <img height=200 align="center" src="https://github-readme-stats.vercel.app/api/top-langs/?username=YOUR_USERNAME&layout=compact&langs_count=8&card_width=320&theme=radical" />
</div>
```

### Pinned repo card (beyond the 6-pin limit)

```markdown
[![Repo Card](https://github-readme-stats.vercel.app/api/pin/?username=YOUR_USERNAME&repo=YOUR_REPO_NAME&theme=radical)](https://github.com/YOUR_USERNAME/YOUR_REPO_NAME)
```

### Themes

Over 30 built-in themes: `default`, `dark`, `radical`, `merko`, `gruvbox`, `tokyonight`, `onedark`, `cobalt`, `synthwave`, `highcontrast`, `dracula`, `monokai`, `vue`, `nord`, `solarized-dark`, `material-palenight` and more.

### Common parameters

| Parameter | Description | Example |
|---|---|---|
| `theme` | Visual theme | `theme=radical` |
| `hide_border` | Remove card border | `hide_border=true` |
| `border_radius` | Corner rounding | `border_radius=10` |
| `bg_color` | Background (hex or gradient) | `bg_color=0d1117` |
| `title_color` | Title text colour | `title_color=ff6e96` |
| `hide` | Hide specific stats | `hide=contribs,issues` |
| `show` | Show extra stats | `show=reviews,prs_merged` |
| `locale` | Language (45+ supported) | `locale=en` |
| `cache_seconds` | Cache duration (min 21600) | `cache_seconds=86400` |

---

## Streak Stats

**Repository:** `github.com/DenverCoder1/github-readme-streak-stats`

Shows total contributions, current streak and longest streak.

```markdown
[![GitHub Streak](https://streak-stats.demolab.com/?user=YOUR_USERNAME&theme=radical)](https://git.io/streak-stats)
```

**Key parameters:** `theme`, `hide_border`, `border_radius`, `card_width`, `locale`, `short_numbers` (K/M abbreviations), `exclude_days` (e.g. `exclude_days=Sun,Sat`).

Over 50 themes available. Self-hosting on Vercel is recommended for reliability.

---

## Skill Icons

**Repository:** `github.com/tandpfun/skill-icons`

The cleanest way to display your tech stack - 200+ uniform, beautiful icons served from Cloudflare Workers.

```markdown
[![My Skills](https://skillicons.dev/icons?i=python,js,ts,react,nextjs,nodejs,docker,aws,git,vscode&perline=5)](https://skillicons.dev)
```

**Parameters:**
- `i` - comma-separated icon IDs (required)
- `theme` - `dark` or `light`
- `perline` - icons per row (1-50, default 15)

**Popular icon IDs:** `python`, `js`, `ts`, `react`, `nextjs`, `vue`, `angular`, `svelte`, `nodejs`, `express`, `fastapi`, `django`, `flask`, `java`, `spring`, `go`, `rust`, `cpp`, `cs`, `php`, `ruby`, `swift`, `kotlin`, `dart`, `flutter`, `postgres`, `mysql`, `mongodb`, `redis`, `sqlite`, `docker`, `kubernetes`, `aws`, `azure`, `gcp`, `vercel`, `netlify`, `git`, `github`, `gitlab`, `linux`, `bash`, `vscode`, `idea`, `vim`, `figma`, `tailwind`, `sass`, `webpack`, `vite`

---

## Shields.io Badges

**Website:** `shields.io` - serves 1.6+ billion badge images per month.

**Five badge styles:** `flat`, `flat-square`, `plastic`, `for-the-badge`, `social`

The `for-the-badge` style is most popular for profile READMEs. The generic pattern:

```
https://img.shields.io/badge/LABEL-HEX_COLOR?style=for-the-badge&logo=LOGO_NAME&logoColor=white
```

Logo names come from **Simple Icons** (`simpleicons.org`).

### Tech stack badges

```markdown
![Python](https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=white)
![TypeScript](https://img.shields.io/badge/TypeScript-3178C6?style=for-the-badge&logo=typescript&logoColor=white)
![React](https://img.shields.io/badge/React-20232A?style=for-the-badge&logo=react&logoColor=61DAFB)
![Node.js](https://img.shields.io/badge/Node.js-339933?style=for-the-badge&logo=node.js&logoColor=white)
![Docker](https://img.shields.io/badge/Docker-2496ED?style=for-the-badge&logo=docker&logoColor=white)
![AWS](https://img.shields.io/badge/AWS-232F3E?style=for-the-badge&logo=amazonwebservices&logoColor=white)
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-316192?style=for-the-badge&logo=postgresql&logoColor=white)
![Git](https://img.shields.io/badge/Git-F05032?style=for-the-badge&logo=git&logoColor=white)
```

### Social and contact badges

```markdown
[![LinkedIn](https://img.shields.io/badge/LinkedIn-0077B5?style=for-the-badge&logo=linkedin&logoColor=white)](https://linkedin.com/in/YOUR_USERNAME)
[![Twitter/X](https://img.shields.io/badge/Twitter-1DA1F2?style=for-the-badge&logo=twitter&logoColor=white)](https://twitter.com/YOUR_USERNAME)
[![Portfolio](https://img.shields.io/badge/Portfolio-FF5722?style=for-the-badge&logo=todoist&logoColor=white)](https://your-website.com)
[![Email](https://img.shields.io/badge/Email-D14836?style=for-the-badge&logo=gmail&logoColor=white)](mailto:your@email.com)
```

### Pre-made badge collections

- `github.com/Ileriayo/markdown-badges` - 200+ ready-made badges
- `github.com/alexandresanlim/Badges4-README.md-Profile` - organised by category

---

## Capsule Render Headers and Footers

**Repository:** `github.com/kyechan99/capsule-render`

Generates animated, colourful banners to use as headers and footers.

```markdown
![Header](https://capsule-render.vercel.app/api?type=waving&color=gradient&height=200&section=header&text=YOUR%20NAME&fontSize=80&animation=fadeIn&fontAlignY=35)
```

**Shape types:** `wave`, `egg`, `shark`, `slice`, `rect`, `soft`, `rounded`, `cylinder`, `waving`, `transparent`, `venom`

**Colour modes:** `gradient` (random gradient), `random` (random solid), `auto` (changes each time), a hex code, or a custom gradient: `color=0:EEFF00,100:a82da8`

**Animations:** `fadeIn`, `scaleUp`, `blink`, `blinking`, `twinkling`

**Matching footer:**

```markdown
![Footer](https://capsule-render.vercel.app/api?type=waving&color=gradient&height=120&section=footer)
```

---

## Typing SVG Animated Text

**Repository:** `github.com/DenverCoder1/readme-typing-svg` (8,500+ stars)

Creates an animated typing and deleting text effect - ideal for profile sub-headings.

```html
<p align="center">
  <a href="https://git.io/typing-svg">
    <img src="https://readme-typing-svg.demolab.com/?lines=Full+Stack+Developer;Open+Source+Contributor;Always+Learning+New+Things&font=Fira+Code&center=true&width=440&height=45&duration=4000&pause=1000" alt="Typing SVG" />
  </a>
</p>
```

**Key parameters:**
- `lines` - text lines separated by `;`, spaces as `+`
- `font` - font family (Google Fonts supported)
- `center` - centre the text
- `width` / `height` - card dimensions
- `duration` - typing speed in milliseconds
- `pause` - pause between lines in milliseconds
- `repeat` - loop (`true`) or play once (`false`)
- `color` - hex colour

---

## Activity Graph

**Repository:** `github.com/Ashutosh00710/github-readme-activity-graph`

Renders a line graph of your contribution activity over the last 31 days.

```markdown
[![Activity Graph](https://github-readme-activity-graph.vercel.app/graph?username=YOUR_USERNAME&theme=react-dark&area=true)](https://github.com/ashutosh00710/github-readme-activity-graph)
```

**Themes:** `react-dark`, `tokyo-night`, `dracula`, `github-dark`, `high-contrast`, `minimal` and more.

**Parameters:** `theme`, `bg_color`, `color`, `line`, `point`, `area` (boolean fill under line), `hide_border`, `custom_title`, `radius`

---

## Profile Trophies

**Repository:** `github.com/ryo-ma/github-profile-trophy`

Generates dynamic achievement trophies ranked from SSS down to C based on your GitHub stats.

```markdown
[![Trophies](https://github-profile-trophy.vercel.app/?username=YOUR_USERNAME&theme=onedark&column=-1&row=1&no-frame=true&no-bg=true)](https://github.com/ryo-ma/github-profile-trophy)
```

**Trophy types:** Stars, Commits, Followers, Issues, Pull Request, Repositories, Experience (account age), and secret achievements.

**Parameters:**
- `theme` - `flat`, `onedark`, `dracula`, `nord`, `algolia`, `tokyonight`, `juicyfresh`, `radical` and more
- `column` - number of columns (`-1` = auto)
- `row` - number of rows
- `no-frame` - remove card frames
- `no-bg` - transparent background
- `title` - show only specific trophies: `title=Stars,Followers`
- `rank` - filter by rank: `rank=S,AAA,AA`

---

## Contribution Snake Animation

**Repository:** `github.com/Platane/snk`

Generates an animated SVG of a snake eating your contribution graph squares. Requires a GitHub Actions workflow to generate and update the animation.

**Step 1.** Create `.github/workflows/snake.yml` in your profile repository:

```yaml
name: Generate Snake

on:
  schedule:
    - cron: "0 */12 * * *"    # regenerate every 12 hours
  workflow_dispatch:

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: Platane/snk@v3
        with:
          github_user_name: ${{ github.repository_owner }}
          outputs: |
            dist/github-snake.svg
            dist/github-snake-dark.svg?palette=github-dark

      - uses: crazy-max/ghaction-github-pages@v2.1.3
        with:
          target_branch: output
          build_dir: dist
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

**Step 2.** Run the workflow manually (Actions tab → Generate Snake → Run workflow).

**Step 3.** Add to your README with dark mode support:

```html
<picture>
  <source
    media="(prefers-color-scheme: dark)"
    srcset="https://github.com/YOUR_USERNAME/YOUR_USERNAME/blob/output/github-snake-dark.svg"
  />
  <source
    media="(prefers-color-scheme: light)"
    srcset="https://github.com/YOUR_USERNAME/YOUR_USERNAME/blob/output/github-snake.svg"
  />
  <img
    alt="Contribution snake animation"
    src="https://github.com/YOUR_USERNAME/YOUR_USERNAME/blob/output/github-snake.svg"
  />
</picture>
```

---

## Visitor Counter

The most popular visitor counter uses komarev's service:

```markdown
![Profile Views](https://komarev.com/ghpvc/?username=YOUR_USERNAME&color=blue&style=flat-square&label=PROFILE+VIEWS)
```

**Colour options:** `blue`, `green`, `red`, `orange`, `purple`, `brightgreen`, `grey`, or a hex code.

**Style options:** `flat`, `flat-square`, `plastic`, `for-the-badge`

> [!NOTE]
> Visitor counters track page loads through GitHub's image proxy (Camo CDN), not unique visitors. The count increments each time the profile page loads an image, including your own visits.

---

## Dark and Light Mode Support

GitHub adapts your profile to the viewer's system theme (dark or light). Use the `<picture>` element with `prefers-color-scheme` media queries to serve different images depending on the theme.

```html
<picture>
  <source
    srcset="https://github-readme-stats.vercel.app/api?username=YOUR_USERNAME&show_icons=true&theme=dark"
    media="(prefers-color-scheme: dark)"
  />
  <source
    srcset="https://github-readme-stats.vercel.app/api?username=YOUR_USERNAME&show_icons=true"
    media="(prefers-color-scheme: light), (prefers-color-scheme: no-preference)"
  />
  <img src="https://github-readme-stats.vercel.app/api?username=YOUR_USERNAME&show_icons=true" alt="GitHub Stats" />
</picture>
```

Apply this to every widget for a polished dark/light experience.

Alternatively, use `&theme=transparent` or `&bg_color=00000000` (transparent) on widgets - they will adapt to either theme automatically.

---

## Pinned Repositories

Pin up to **6 repositories** to your profile. These appear directly below your profile README and are the first thing most visitors explore after reading your introduction.

**To pin repositories:**

1. Go to `github.com/YOUR_USERNAME`
2. Click **Customize your pins**
3. Select up to 6 repositories (your own or ones you have contributed to)
4. Drag to reorder
5. Click **Save pins**

**What to pin:**

- Your best, most complete projects
- Projects with the most impact or interesting scope
- Projects that demonstrate the skills most relevant to your goals
- Open source contributions you are proud of

**Project README quality matters more than the profile README.** When a recruiter clicks a pinned project, the project's `README.md` is what they read. Include: what the project does, how to run it, screenshots or a live demo link, technologies used and your contribution.

---

## What Recruiters Actually Look At

Research from hiring managers and recruiters consistently shows this priority order:

**1. Pinned repositories** - the first impression. They look at what you have built, not how many commits you have made.

**2. Code quality in your projects** - readable code, meaningful commit messages, tests, CI with GitHub Actions.

**3. Project READMEs** - a well-documented project signals professional engineering habits.

**4. Open source contributions** - even one merged PR to a known project shows you can collaborate.

**5. Consistent activity** - a green contribution graph helps but quality beats frequency.

**What matters less than developers think:**

- The profile README itself (it creates an impression but does not replace substance)
- Contribution snake animations
- Trophy widgets
- Follower and star counts

> [!TIP]
> Spend more time making your pinned project READMEs excellent than perfecting your profile README. A project with a live demo link, clear setup instructions, architecture diagram and CI badge will impress a recruiter far more than an animated contribution graph.

---

## Complete Template - Beginner

This template takes about 15 minutes. Replace all `YOUR_` placeholders with your actual details.

```markdown
# Hi, I am YOUR_NAME 👋

## About Me

I am a [your role] passionate about [your interests]. Currently studying/working at [institution/company].

- 🔭 Currently working on: [project name or description]
- 🌱 Currently learning: [technology or skill]
- 💬 Ask me about: [topics you know well]
- 📫 How to reach me: your@email.com
- ⚡ Fun fact: [something interesting about you]

## Tech Stack

![Python](https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=white)
![JavaScript](https://img.shields.io/badge/JavaScript-F7DF1E?style=for-the-badge&logo=javascript&logoColor=black)
![React](https://img.shields.io/badge/React-20232A?style=for-the-badge&logo=react&logoColor=61DAFB)
![Git](https://img.shields.io/badge/Git-F05032?style=for-the-badge&logo=git&logoColor=white)

## GitHub Stats

![YOUR_USERNAME's GitHub stats](https://github-readme-stats.vercel.app/api?username=YOUR_USERNAME&show_icons=true&theme=radical&include_all_commits=true)

## Connect with Me

[![LinkedIn](https://img.shields.io/badge/LinkedIn-0077B5?style=for-the-badge&logo=linkedin&logoColor=white)](https://linkedin.com/in/YOUR_LINKEDIN)
[![Portfolio](https://img.shields.io/badge/Portfolio-FF5722?style=for-the-badge&logo=todoist&logoColor=white)](https://YOUR_WEBSITE.com)
```

---

## Complete Template - Intermediate

This template takes about 30-45 minutes. Customise each section. Preview each widget URL in your browser before adding it to confirm it loads correctly.

```markdown
![Header](https://capsule-render.vercel.app/api?type=waving&color=gradient&height=200&section=header&text=YOUR%20NAME&fontSize=80&animation=fadeIn&fontAlignY=35)

<p align="center">
  <a href="https://git.io/typing-svg">
    <img src="https://readme-typing-svg.demolab.com/?lines=Full+Stack+Developer;Open+Source+Enthusiast;Always+Learning&font=Fira+Code&center=true&width=440&height=45&duration=4000&pause=1000" alt="Typing SVG" />
  </a>
</p>

<p align="center">
  <img src="https://komarev.com/ghpvc/?username=YOUR_USERNAME&style=flat-square&color=blue" alt="Profile Views" />
</p>

---

## 🚀 About Me

Brief 2-3 sentences describing your background, what you build and what excites you.

- 🔭 Working on **[Project Name](https://github.com/YOUR_USERNAME/project)**
- 🌱 Learning **[Technology]**
- 👯 Looking to collaborate on **open source projects**
- 📫 Contact: **your@email.com**

---

## 🧰 Tech Stack

[![My Skills](https://skillicons.dev/icons?i=python,ts,react,nextjs,nodejs,docker,postgres,aws,git,vscode&perline=5)](https://skillicons.dev)

---

## 📊 GitHub Stats

<div align="center">
  <img height=200 align="center" src="https://github-readme-stats.vercel.app/api?username=YOUR_USERNAME&show_icons=true&theme=radical&include_all_commits=true" alt="GitHub Stats" />
  <img height=200 align="center" src="https://github-readme-stats.vercel.app/api/top-langs/?username=YOUR_USERNAME&layout=compact&langs_count=8&card_width=320&theme=radical" alt="Top Languages" />
</div>

<br/>

<div align="center">
  <a href="https://git.io/streak-stats">
    <img src="https://streak-stats.demolab.com/?user=YOUR_USERNAME&theme=radical" alt="GitHub Streak" />
  </a>
</div>

---

## 🏆 Trophies

[![Trophies](https://github-profile-trophy.vercel.app/?username=YOUR_USERNAME&theme=onedark&column=-1&row=1&no-frame=true&no-bg=true)](https://github.com/ryo-ma/github-profile-trophy)

---

## 🚀 Featured Projects

| Project | Description | Stack |
|---|---|---|
| [**Project 1**](https://github.com/YOUR_USERNAME/project1) | Brief description of what it does | React, Node.js, PostgreSQL |
| [**Project 2**](https://github.com/YOUR_USERNAME/project2) | Brief description of what it does | Python, FastAPI, Docker |
| [**Project 3**](https://github.com/YOUR_USERNAME/project3) | Brief description of what it does | TypeScript, AWS |

---

## 📫 Connect

[![LinkedIn](https://img.shields.io/badge/LinkedIn-0077B5?style=for-the-badge&logo=linkedin&logoColor=white)](https://linkedin.com/in/YOUR_LINKEDIN)
[![Twitter](https://img.shields.io/badge/Twitter-1DA1F2?style=for-the-badge&logo=twitter&logoColor=white)](https://twitter.com/YOUR_TWITTER)
[![Portfolio](https://img.shields.io/badge/Portfolio-FF5722?style=for-the-badge&logo=todoist&logoColor=white)](https://YOUR_WEBSITE.com)

![Footer](https://capsule-render.vercel.app/api?type=waving&color=gradient&height=120&section=footer)
```

---

## Organisation Profile READMEs

Organisations can also have a profile README visible on their organisation page at `github.com/ORG_NAME`.

**For public viewers:** Create a `.github` repository in the organisation. Add a `profile/README.md` file.

**For organisation members only:** Create a `.github-private` repository. Add a `profile/README.md` file. Only signed-in members see this.

The same Markdown rules apply. Organisation READMEs are commonly used for: introduction to the org's mission, links to key repositories, contribution guidelines, contact information and team links.

---

## Performance and Accessibility

### Performance

Every external widget is an API call that can slow your profile page load. Follow these rules:

- **Limit dynamic cards to 3-5 maximum.** Each one adds load time.
- **Self-host github-readme-stats** on Vercel (free) to avoid shared rate limits.
- **Use `&cache_seconds=86400`** on stats cards to reduce API calls.
- **Use GitHub Actions** to generate static SVGs stored in your repository - zero external dependencies, fastest possible load.
- Use `&theme=transparent` for widgets - they adapt to both themes without separate dark/light versions.

### Accessibility

- Add descriptive `alt` text to every image (`alt="YOUR_USERNAME's GitHub stats"` not `alt="image"`). Keep under 125 characters.
- Do not use emoji as the only way to convey information.
- Maintain heading hierarchy - do not skip from H1 to H3.
- Ensure text has sufficient contrast against backgrounds.
- GitHub now underlines links by default for accessibility.

---

## Try It Yourself

**Step 1.** Create your profile repository:

1. Go to `github.com/new`
2. Name it exactly your GitHub username
3. Set it to **Public**
4. Check **Add a README file**
5. Click **Create repository**

**Step 2.** Use GPRM to generate a starting point:

1. Go to [gprm.itsvg.in](https://gprm.itsvg.in)
2. Enter your GitHub username
3. Work through the 7 steps, filling in your details
4. Copy the generated Markdown

**Step 3.** Edit the README:

1. Go to your profile repository on GitHub
2. Click the pencil icon to edit `README.md`
3. Replace the contents with the GPRM output
4. Replace all placeholder URLs with your real social links
5. Click **Preview** to check how it looks

**Step 4.** Commit:

```bash
git commit -m "add: profile README"
```

Or commit directly on GitHub.

**Step 5.** Visit `github.com/YOUR_USERNAME` and see the result.

**Step 6.** Iterate. Add or remove widgets, change themes, update the bio. The best profile READMEs evolve over time.

---

## Common Mistakes

**Using the default generated template without customising it.**

The "Hi there 👋" auto-generated README is the first thing to change. Every detail - the bio, the tech stack icons, the social links - should reflect you specifically.

**Too many widgets.**

A profile with stats card + streak + top languages + trophy + activity graph + snake animation + visitor counter all visible at once is overwhelming and slow to load. Pick 3-4 that matter to you and let them breathe.

**Not updating social links.**

Many profiles have placeholder LinkedIn/Twitter links that go nowhere. Every link should be real and current.

**Pinning forked repositories with no changes.**

A forked repo pinned on your profile looks like you are padding your portfolio. Only pin projects where you made meaningful contributions.

**Ignoring project READMEs while perfecting the profile README.**

A dazzling profile README that links to projects with no documentation undoes all the work. The project README is what a recruiter actually reads.

**Broken widgets.**

The shared public instances of stats services can go down or return errors. Check your profile from a private window periodically to catch broken images. Use self-hosting or GitHub Actions for critical widgets.

---

## Summary

- The profile README lives in a **public repo named exactly your username**, with a `README.md` in the root
- GitHub supports Markdown plus limited HTML - no CSS, no JavaScript, no iframes
- Use **GPRM** (`gprm.itsvg.in`) to generate a quality starting point quickly
- Key components: **github-readme-stats** (stats cards), **streak-stats** (streak), **skill-icons** (tech stack), **shields.io** (badges), **capsule-render** (headers/footers), **typing-svg** (animated text)
- Use `<picture>` with `prefers-color-scheme` for dark/light mode widget support
- **Pinned repositories** are more important than the profile README itself for recruiters
- Limit dynamic widgets to 3-5 for performance; self-host or use GitHub Actions for reliability
- Add descriptive alt text to every image for accessibility
- The best profile READMEs balance visual appeal with substance - real projects, real skills, real contact info

---

## Sources and Further Reading

- [Managing your profile README - GitHub Docs](https://docs.github.com/en/account-and-profile/setting-up-and-managing-your-github-profile/customizing-your-profile/managing-your-profile-readme) - official guide
- [GPRM - GitHub Profile README Maker](https://gprm.itsvg.in) - best no-code generator
- [awesome-github-profile-readme](https://github.com/abhisheknaiidu/awesome-github-profile-readme) - curated gallery of real profiles for inspiration
- [github-readme-stats](https://github.com/anuraghazra/github-readme-stats) - stats cards (self-hosting recommended)
- [github-readme-streak-stats](https://github.com/DenverCoder1/github-readme-streak-stats) - streak card
- [skill-icons](https://github.com/tandpfun/skill-icons) - 200+ tech stack icons
- [capsule-render](https://github.com/kyechan99/capsule-render) - animated headers and footers
- [readme-typing-svg](https://github.com/DenverCoder1/readme-typing-svg) - animated typing effect
- [github-profile-trophy](https://github.com/ryo-ma/github-profile-trophy) - achievement trophies
- [snk](https://github.com/Platane/snk) - contribution snake animation
- [Ileriayo/markdown-badges](https://github.com/Ileriayo/markdown-badges) - 200+ pre-made Shields.io badges
- [5 tips for making your profile page accessible - GitHub Blog](https://github.blog/developer-skills/github/5-tips-for-making-your-github-profile-page-accessible/) - accessibility guide

---

<div align="center">

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

</div>
