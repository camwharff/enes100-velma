# VELMA: ENES100 Lab Management Platform

**An internal web platform for running the ENES100 engineering labs at the University of Maryland.** VELMA gives lab staff one place to reach the tools they use every shift: tool tracking, School Store checkout, the safety log, mission documentation, and the Milestone 7 mission randomizer.

> **Archived for my portfolio.** This repository is a frozen snapshot of the platform as of December 2025, when I handed it off. The original repository is [umdenes100/enes100-velma](https://github.com/umdenes100/enes100-velma).

**Timeline:** Started January 2025 · Launched April 2025 · Maintained through December 2025

**Role:** Proposed, designed, and developed the platform; mentored my successor

**Stack:** SvelteKit · TypeScript · Svelte 4 · Vite · GitHub Actions · GitHub Pages

**Used by:** 50+ ENES100 lab staff

---

## Overview

ENES100 is UMD's first-year engineering design course, where student teams build autonomous over-terrain vehicles (OTVs). Running its labs means tracking hundreds of tools, checking out parts from the course's School Store, logging safety incidents, and running mission demonstrations, all while helping students. Before VELMA, there was no digital tool tracking or safety logging. Both existed only as manual processes, and milestone demonstration results were recorded on paper.

I proposed VELMA as a single hub for lab operations and built the platform alongside its first new tool, Tool Check. The Safety Log followed as a mentored project. Staff open one app on a lab tablet or laptop and switch between tools from a persistent menu.

## Platform

| App | Route | What it does |
|---|---|---|
| **Tool Check** | `/tools` | Track whether each tool is present or missing at every lab table, with timestamped history in Firebase. I was the sole developer ([repo](https://github.com/camwharff/enes100-toolcheck)). |
| **School Store** | `/store` | Check out reusable OTV parts to student teams against a per-team budget. |
| **Safety Log** | `/safety` | The lab safety log. Built by my successor as a mentored project. |
| **Documentation** | `/docs` | Tabbed internal reference for mission specifications (six OTV missions) and project details (arena, milestones, awards, final product), with accessibility improvements (heading structure, alt text) to the embedded documents. |
| **Milestone 7** | `/milestone` | Randomizes mission conditions for student demonstrations. Proctors enter results in an embedded form that feeds an automated results spreadsheet. |
| **Website** | `/website` | The public [course website](https://github.com/camwharff/enes100-website), embedded so staff can reach it without leaving the platform. |

## My contributions

- **Proposed and built the platform:** the SvelteKit app, the home screen of app tiles, the collapsible navigation menu, the shared styling, and the GitHub Pages deployment.
- **Tool Check:** designed and built the tool tracking app that VELMA embeds. It tracks 730 tools across 10 lab tables in Firebase Realtime Database, and every check is logged with a timestamp.
- **Milestone 7 mission randomizer:** for each OTV mission (fire, water, seed, data, materials), the tool generates random demonstration conditions, such as which flames are lit and which arena topography to use. Where it helps, it shows a mission diagram. I published it for the Spring 2025 demonstrations.
- **Milestone 7 results pipeline:** demonstration results (about 200 runs per semester) used to be recorded on paper, and a course administrator typed every run into a spreadsheet by hand. I proposed moving the process to a Google Form and built the pipeline. Now project proctors record each demonstration run in a Google Form embedded on the same page as the randomizer, so they never leave VELMA during a demo. Submissions flow automatically into a Google Sheet I set up. The sheet organizes and ranks runs by team, section, and score. This removed manual data entry, and instructors get up-to-date standings as soon as each run is submitted.
- **Documentation section:** a nested tab interface that organizes mission specifications and project details for lab staff in one place. I also improved the embedded documents' accessibility by adding a proper heading structure and alt text for images, which makes them navigable with screen readers.
- **School Store integration:** embedded the School Store checkout app.
- **Graphics:** designed all of the platform's graphics in Adobe Illustrator, including the app icons, the menu logo, and the mission diagrams.
- **Mentorship and handoff:** trained my successor, Maggie Crooks, to take over the platform through two hands-on projects:
  - **Hydrogen mission:** a small, contained change that adds a new mission to the Milestone 7 randomizer. I used it to walk her through the GitHub workflow end to end: cloning, committing, pushing, and seeing the change deploy automatically through GitHub Actions.
  - **Safety Log:** a full new app, which I used to teach Svelte development and Firebase. Once it was ready, I integrated it into VELMA.

## Architecture

VELMA is a statically generated SvelteKit shell. Each operational tool is its **own independently deployed app**, embedded in the shell with an iframe:

```
VELMA (SvelteKit shell: navigation, layout, docs, Milestone 7)
├── /tools   → enes100-toolcheck   (vanilla JS + Firebase)
├── /store   → enes100-schoolstore
├── /safety  → enes100-safetylog
└── /website → enes100.umd.edu     (SvelteKit)
```

**Why this design:**
- **Independent development.** Teaching fellows could build and deploy their own tool in its own repository and stack without touching the platform. The Safety Log was built this way and plugged in by adding one route.
- **Fault isolation.** If one tool breaks or is being redeployed, the rest of the platform keeps working.
- **No backend to maintain.** The shell is static and hosted for free on GitHub Pages. Each tool that needs data owns its own data layer, such as Firebase for Tool Check.

The tradeoff is that the shell and the embedded apps don't share state or sign-in. That was acceptable for an internal staff tool where each app manages its own data.

## Project structure

```
src/
├── lib/
│   ├── Index.svelte        # Collapsible navigation menu
│   ├── IndexWeb.svelte     # Navigation variant for the full-height website view
│   └── global.css          # Shared styles
└── routes/
    ├── +page.svelte        # Home screen (app tiles)
    ├── +layout.svelte      # Shell layout: menu + active app
    ├── tools/              # Tool Check (embedded)
    ├── store/              # School Store (embedded)
    ├── safety/             # Safety Log (embedded)
    ├── website/            # Course website (embedded)
    ├── docs/               # Tabbed mission and project documentation
    └── milestone/          # Milestone 7 mission randomizer + proctor results form
static/img/                 # App icons and mission diagrams
```

## Running locally

Requires Node.js.

```bash
git clone https://github.com/camwharff/enes100-velma.git
cd enes100-velma
npm install
npm run dev
```

> **Note:** the embedded apps point to the live ENES100 deployments. Tool Check and the School Store write to production data, so look around without submitting changes.

## Conference presentation

VELMA and the lab's other management tools were presented at **ISAM 2025** (International Symposium on Academic Makerspaces) in the poster *"Strategies for Managing a High-Throughput Academic Makerspace."* I was the lead author, and my co-author was Joshua Cocker, a Keystone Program instructor and the ENES100 lab manager. I wrote and designed the poster, including the isometric lab illustration, in Adobe Illustrator, and we presented it together at the conference. The poster covers how the ENES100 labs support 480–800 students each week. It shows how the physical organization (shadow-boarded tool chests, the School Store, digital signage) works together with software tools like VELMA on the lab tablets, Tool Check, and the course website.
![ISAM 2025 poster: Strategies for Managing a High-Throughput Academic Makerspace](/isam-2025-poster.jpg)

## Credits

Built for the UMD Keystone Engineering Program's ENES100 course. **Maggie Crooks** built the Safety Log and added the hydrogen mission to the Milestone 7 randomizer as part of her onboarding, and she took over maintenance of the platform.
