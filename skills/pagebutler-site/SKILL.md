---
name: pagebutler-site
description: "Build, edit, check, deploy, preview and publish a website on PageButler, which hosts sites on butler.page. Use it when a person wants a website made, changed, put online or taken live there, when a folder holds pb.config.json, or when the pb command or PageButler's tools, such as pagebutler_guide, are available. PageButler sites are plain HTML, CSS and JavaScript that PageButler renders, never a site generator or framework. Not for app code or documentation sites."
license: Apache-2.0
compatibility: Needs the pb command in a shell, or PageButler's MCP server (https://api.pagebutler.ai/mcp) connected.
metadata:
  vocabulary: "0"
---

# PageButler

PageButler hosts websites on butler.page and renders them itself. A site is plain HTML pages, CSS, JavaScript and files you write, with `data-pb-*` attributes marking shared parts and the text people may edit. Never scaffold a site generator, framework or single-page app (no `npm create astro`, Vite, Next, Hugo or Eleventy), or keep one as a second source of the pages: `pb check` and every deploy report its output and refuse an app's empty shell. Write the site like a Ghost theme, without Handlebars. A tool may compile CSS or JavaScript that pages load (`npx @tailwindcss/cli`, `npx esbuild --bundle`); no tool may write an `.html` file. It suits landing pages, portfolios and company sites, not documentation sites or app code.

## Before you start
- With PageButler's tools connected, call `pagebutler_guide` first: it returns these rules.
- `pb`, here and in its output, means the full path of this skill's `scripts/pb` if that file exists, else `pb` on the PATH. With a shell and `pb`, run it in the site's folder; prefer it to the tools. With only the tools, as in a chat app, follow `references/chat.md`.
- With a shell but no `pb`, run `npx --yes pagebutler@latest setup`, which installs `pb` and this skill from npm (in Codex, with escalated permissions, which asks the person), and do what it prints. Never install anything else to get `pb`.
- Run `pb whoami`. If it isn't signed in, run plain `pb login`, give the person the lines it prints for them exactly as printed, and end your turn; once they've allowed it, run `pb login --check`, with nothing after it. Never put a token in a command, a file or a chat, and never ask the person to paste a sign-in link, code or email. More: `references/signing-in.md`.

## Start a site, or bring one
In the site's folder, the first command is `pb init --name <slug> --title "<title>"`, both in the person's words, since a name made from the folder's may be taken. For a site that already exists, a generator's included, follow `references/existing-site.md`, not the steps below. Add `--blog` for a blog. Then:
1. Replace the home page's lede and description first: the starter text says the site is new.
2. Add pages with `pb new page <path> --title "<title>" --description "<one sentence>"`, then add the `<a>` its result names to the `<nav>` in `_partials/site-header.html`. Copy the starter files (`references/scaffold.md`); never write your own skeleton.
3. Restyle freely, but keep every `data-pb-field` and `data-pb-scope`, so a person's edits keep their places.
4. Keep `404.html`, in the site's language.
5. A blog's posts and lists render from v1: tell the person before you promise a blog (`references/blog.md`).

## Name things before you write them
A page is a complete HTML document, named by its file (`about/index.html` is `about`). A partial replaces what its `data-pb-include` element held (`<head>` keeps its own) and takes no parameters. Text a person may edit gets `data-pb-field`; `data-pb-scope` groups fields; a list field holds keyed `data-pb-item` children. Read this table before you name a field (`references/names.md`: examples, `pb annotate`):

<!--pb:generated resolution-table-v0-->
| Where the name is | You write | Path |
|---|---|---|
| A page, outside any scope | `data-pb-field="title"` | `pages.<page>.<name>` |
| Inside an undotted scope | `data-pb-scope="hero"` around `data-pb-field="title"` | `pages.<page>.<scope>.<name>` |
| Inside a dotted scope | `data-pb-scope="site.contact"` around `data-pb-field="phone"` | `<scope>.<name>` |
| A `site.*` or other dotted field name | `data-pb-field="site.phone"` | `<name>` |
| Nested scopes | `data-pb-scope="hero"` around `data-pb-scope="stats"` | `pages.<page>.<scope>.<scope>.<name>` |
| Inside a partial | `data-pb-field="legal"` in `_partials/site-footer.html` | `partials.<partial>.<scopes>.<name>` |
| Inside a list item | `data-pb-item="ana"` in a `data-pb-type="list"` field | `<list path>[<key>].<name>` |
| Inside another field | `data-pb-field="lede"` inside `data-pb-field="intro"` | None when the outer field binds content: PB-T211. Inside a link that holds elements: `pages.<page>.<name>` |
| Inside `data-pb-ignore` | `data-pb-ignore` around a `data-pb-field` | None: not a field |
| Head elements on a normal page | `<title data-pb-field="seo.title">` | None: PB-T205 |
<!--/pb:generated-->

- Never invent a `data-pb-*` attribute. There are no conditionals, loops, layouts or computed values: write the HTML as it should read. An unknown attribute is an error saying what to write instead (`references/substitutes.md`); every real one is in `references/constructs.md`, and redirects, headers and file types in `references/serving.md`.
- Each page has its own `<title>` and `<meta name="description">` as plain tags in `<head data-pb-include="head">`: never in `_partials/head.html`, and never with `data-pb-field`.
- Link within the site by root-relative paths (`/about/`), never to a preview address.
- Don't add `robots.txt` or `sitemap.xml` unless the person asks for their own rules: PageButler writes both.

## Check, deploy, look, publish
1. Put any compile step in `pb.config.json`'s `build.command`, without `--watch`: `pb deploy` runs it.
2. Run `pb check` before you write a second page and before every deploy. Exit 1 (`ok: false` under `--json`): fix every error and check again. Never delete a person's file for a hint: add the `.pbignore` line it names. An answer with a `code` means it couldn't check: follow the hint. `details.unexpanded` or `details.skipped`: fix the errors, then check again for the rest. Codes: `references/template-codes.md`, `references/site-rules.md`.
3. `pb deploy` lands a new version on staging: a preview. Relay its links and preview sentence as printed, and say it is a preview, not live yet. Before them it prints its merge report: `references/merge-report.md`. If it fails, follow its `hint:` and tell the person its last line. Running it again is always safe. Before it runs: `references/publishing.md`.
4. After the first `pb deploy` that succeeds in a conversation, before anything else, open staging's link (`Open staging, ...`) in the person's browser (how: `references/publishing.md`) and ask them to keep the tab: it shows each new preview.
5. Publish only when the person asks for the site to go live; liking the preview is not asking. `pb publish` takes the preview's code live, with the content already live. Relay its lines and stop: its "Live at" line is the whole report, and "is already live at" is a success too. Edits, `--dry`, rolling back: `references/publishing.md`; the site's own domain: `references/domains.md`.

## Previews
- Whenever you hand over a link or report a change, say "preview", or "live" once a publish says so.
- Hand share links over exactly as given, never in a code block: this version's first, then staging's, for reviewers. Give the expiry once, as a date: until then, either opens every preview, later ones too, so never post them anywhere public, such as commits, issues or logs.
- PageButler tags each preview it serves "Preview - not live", never your files or the live site: don't change the site for it.

## What the person hears
- Call a site by its display name.
- Until the first publish, say so, for example: Nothing is live yet: https://acme.butler.page shows "Nothing published here yet" until the first publish.
- A form PB-10 names doesn't send yet: at each hand-over, say so and ask whether to keep it or show an email link.
- No codes, file names or field paths unless asked. More: `references/telling-the-person.md`, `references/limits.md`.

## House rules
- The last upload is the code: edit the site's files, never a generator's source.
- A value a person edited on PageButler, published or not, is kept when you deploy. Replace it only when they named that field, and say so.
- Never publish a person's unpublished edit unless they named it.
- Never roll a site back unasked; prefer a new deploy that fixes it.
- In a site's folder, run `pb status` first; if it says to pull, run `pb pull` before you edit, then tell the person: `references/pulling.md`.
- Put a photo on a page from its file or link; base64 only without `pb`: `references/images.md`.
- After a conflict (409), read again, re-apply only your change, retry once; after a second, stop and tell the person.
- When a deploy's preview can't be published yet, do what it says; for held values, only what their `held:` lines allow; else tell the person and stop.
- Never put a person's email in a file, commit, log or report.

## When something fails
- A failed command or call changed nothing unless it says what did. Take the next step its hint names; never decide by an error's code alone.
- If a repeat the hint asks for fails too, stop and tell the person nothing changed.
- After fixing a refusal's cause, send a new request, with a new `idempotency_key` if you used one.
- A dry run is not a preview: only a deploy makes one.
- `pb help --json` lists every command, flag and exit code (75: not yet; 77: sign in); `--json` gives any result as data.

## More
Each `references/<name>.md` is also `pagebutler_guide` with `topic` `<name>`. Fields and tool deploys: `references/tools.md`; photos: `assets_import`; comments: `references/comments.md`.

Written for template vocabulary 0, the `version` in `pb.config.json`.
