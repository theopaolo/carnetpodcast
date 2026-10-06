# Product

<!-- impeccable:product-schema 1 -->

## Platform

web

## Users

Two readers, in two situations:

- Learners after class. They come back alone, days or weeks later, to reread a chapter, find a command, or redo an exercise. Search, the table of contents and prev/next links serve them.
- The teacher projecting a page in front of the group. Text size buttons (A− / A+), fullscreen diagrams and the step-by-step animated diagrams serve this use.

Learners are people learning to make a podcast. Each course states its own audience in its `index.md`.

## Product Purpose

A static site that publishes the author's own podcast-making courses, written in Markdown. It lets a learner reread a course without the teacher, and lets the teacher present from the same pages in class. Success means a learner can understand a chapter on their own and find what they need again later.

## Positioning

The site is called Carnet de podcast. It holds the author's personal teaching materials: courses, reference material and teaching support. The theme is copied from Carnet de dev (`Ada/cours-ada`).

## Operating Context

- Content lives in `src/cours/<course>/`. The course home page is `index.md` and chapters are the other `.md` files, ordered by `order` in frontmatter. `hidden: true` keeps a teacher-only page out of the build.
- Diagrams are written in Mermaid inside the Markdown. A flowchart fenced as ```` ```mermaid play ```` can be played connection by connection. Use it only when the order of the arrows carries meaning. The custom `animated` blocks are step-by-step sequence diagrams with a context bar.
- Deployed as a static site on Coolify using the repository’s `Dockerfile` (Nginx, port 80).

## Capabilities and Constraints

- Static Astro site. There is no server, no accounts and no stored learner data.
- No analytics or tracking. The only third-party requests allowed are fonts and Mermaid.
- French only. All interface copy stays in French and no translation is planned.
- Reader features: full-text search, a "Reprendre la lecture" link on the home page to the last page and section read (kept in `localStorage`), per-page table of contents, a sidebar that lists only the current course (or the fiches) with a link back to all courses, light and dark themes, adjustable text size, a Focus mode that hides the sidebar and table of contents (for reading alone or projecting), copy buttons on code blocks, three ribbons (bookmarks) placed from the table of contents and hanging in the margin of the marked heading, highlights with notes found again after a chapter is edited (a note whose passage is gone stays in the Pochette as détachée), a Pochette page that lists ribbons and notes and exports or imports them as Markdown, with a switch to hide them before projecting (reached from a pocket card on the home page and at the foot of the sidebar), section headings that link to their section and copy its address, fullscreen diagrams, flowchart playback, and a text version under each animated sequence diagram. Shared links show a preview built from the page's first paragraph, and a missing page offers search and a link back to its course.
- The site name is Carnet.

## Brand Commitments

- The site name is Carnet, shown in the top bar, the page titles and the home page.
- The courses address learners in French. Each course keeps its own form of address ("tu" or "vous").

## Evidence on Hand

- One course, Faire un podcast, with its home page only.
- There is no logo or brand asset in the repo yet. Future work must not invent one.

## Product Principles

1. Reading comes first. Every page has to work for a learner alone, without the teacher's spoken explanations.
2. The same page serves self-study and projection. Features for one use must not break the other.
3. Markdown stays the source. Authors write plain `.md`, and the site handles presentation.
4. Nothing about the learner leaves their browser. Preferences stay in `localStorage` and nothing is tracked.

## Accessibility & Inclusion

- Readers can change text size, and the layout keeps working at every size. The root font size stays at 100% so the browser setting is respected.
- Diagrams carry an accessible title and description (`accTitle` / `accDescr`), and animated diagrams have a text version.
- Animated diagrams stop animating under `prefers-reduced-motion`.
