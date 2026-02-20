I have a Flutter Web application at `d:\flutter_projects\website` that acts as a 
dynamic content router. Here is the full specification:

---

## Project Overview

The app is a Flutter Web site deployed to a custom domain. It dynamically reads
a `mapping.md` file from the GitHub repo:

  https://github.com/vineelvanjari/website-content

and routes every URL accordingly — NO code changes are needed to add new routes,
just edit `mapping.md` on GitHub.

---

## mapping.md Format

The file lives at:
  https://raw.githubusercontent.com/vineelvanjari/website-content/main/mapping.md

Each line is:  <url-path> <value>

There are two types of values:

1. Plain URL → embed it full-screen in an iframe:
     / https://vineelvanjari-portfolio.onrender.com/
     /portfolio https://vineelvanjari-portfolio.onrender.com/

2. Obsidian wiki-link → fetch the .md file and render it as Markdown:
     /blogs/flutter/intro [[website/content/blogs/flutter/intro|intro]]
     /intro [[website/content/intro|intro]]

   The Obsidian path format is [[vault/path|alias]]. The vault prefix is
   `website/content/` which must be stripped to get the path relative to the
   GitHub repo root. For example:
     [[website/content/blogs/flutter/intro|intro]]
     → strip prefix → blogs/flutter/intro
     → append .md  → blogs/flutter/intro.md
     → fetch from  → https://raw.githubusercontent.com/vineelvanjari/
                       website-content/main/blogs/flutter/intro.md

---

## Routing Behavior

- The mapping.md is fetched FRESH on every single page navigation (no caching at startup).
  This means editing mapping.md on GitHub takes effect immediately for new visitors.

- `/` and `/portfolio` → full-screen iframe embedding the portfolio URL
  (no app bar, no chrome — just the iframe filling the viewport).

- All other mapped routes → show a Markdown reader page with:
  - An AppBar titled "Vineel Vanjari"
  - Centered content, max width 800px
  - The rendered Markdown content fetched from GitHub

- Unknown routes (not in mapping.md) → show `mapping.md` contents as Markdown
  so the user can see what routes exist.

---

## Technical Stack

- Flutter (Dart), targeting Flutter Web only
- go_router ^13.x for client-side routing
- http ^1.x for fetching mapping.md and .md content files
- flutter_markdown ^0.6.x for rendering Markdown
- package:web ^1.0.0 for iframe embedding (HtmlElementView.fromTagName)
- Conditional import pattern:
    import 'iframe_stub.dart' if (dart.library.html) 'iframe_web.dart';
  so the project compiles on all platforms (mobile, desktop, web)

- GoRouter uses a catch-all route `/:rest(.*)` to handle all paths

---

## File Structure

lib/
  main.dart        ← all routing logic, page widgets, mapping parser
  iframe_web.dart  ← web-only: HtmlElementView.fromTagName iframe
  iframe_stub.dart ← non-web stub (no-op)

---

## Content Repository Structure

GitHub repo: vineelvanjari/website-content

mapping.md         ← route definitions (the source of truth for routing)
blogs/
  flutter/
    intro.md       ← blog content files
intro.md           ← simple pages
404.md             ← optional 404 page

---

## Deployment Target

Will be hosted on a custom domain. Build command:
  flutter build web
Output folder: build/web/ — deploy to any static hosting (Cloudflare Pages,
Netlify, GitHub Pages, Firebase Hosting, etc.)