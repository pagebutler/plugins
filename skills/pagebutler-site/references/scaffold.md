# The starter site's files

`pb init` writes these files, and `pb new page` writes pages like the last one. Copy them rather than writing your own skeleton, and keep every `data-pb-*` attribute when you change the markup or the look. Without `pb`, write them from here. They are shown for a site named `my-site`, titled "My site", in English: use the site's own slug, title and language. Pages in several languages: `references/existing-site.md`, step 4.

The manifest. `$schema` gives editors its autocomplete and checks, and `version` is the template vocabulary the site is written for. If VS Code says the `$schema` location is untrusted, keep the line and tell the person to trust pagebutler.ai once, with the warning's Configure Trusted Domains fix:

<!--pb:generated scaffold-config-->
`pb.config.json`:
```json
{
  "$schema": "https://pagebutler.ai/schema/pb.config.v0.json",
  "name": "my-site",
  "version": 0,
  "site": {
    "title": "My site",
    "language": "en"
  }
}
```
<!--/pb:generated-->

The home page. Replace its lede and description first: the starter text says the site is new. The `<title>` and description belong to the page, inside the head that includes `_partials/head.html`:

<!--pb:generated scaffold-index-->
`index.html`:
```html
<!doctype html>
<html lang="en">
<head data-pb-include="head">
  <title>My site</title>
  <meta name="description" content="Welcome to My site.">
</head>
<body>
  <header class="site-header" data-pb-include="site-header"></header>
  <main>
    <section class="hero" data-pb-scope="hero">
      <h1 data-pb-field="title">My site</h1>
      <p class="lede" data-pb-field="lede">This site is just getting started. Check back soon.</p>
    </section>
  </main>
  <footer class="site-footer" data-pb-include="site-footer"></footer>
</body>
</html>
```
<!--/pb:generated-->

The partials, in `_partials/`. Each fills the element whose `data-pb-include` names it, so write that element with its end tag, never as `<header ... />`. The head partial holds what every page shares, never a `<title>` or description. `site.name` is one value on every page: keep its text the same as `site.title` in `pb.config.json`.

<!--pb:generated scaffold-partials-->
`_partials/head.html`:
```html
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<meta name="color-scheme" content="light dark">
<link rel="stylesheet" href="/assets/site.css">
```

`_partials/site-header.html`:
```html
<nav aria-label="Main">
  <a class="brand" href="/"><span data-pb-field="site.name">My site</span></a>
</nav>
```

`_partials/site-footer.html`:
```html
<p>&copy; <span data-pb-field="site.name">My site</span></p>
```
<!--/pb:generated-->

The not-found page, which visitors see at any address the site doesn't have. Without it, visitors see a plain English page. Keep it in the site's language and look:

<!--pb:generated scaffold-404-->
`404.html`:
```html
<!doctype html>
<html lang="en">
<head data-pb-include="head">
  <title>Page not found</title>
</head>
<body>
  <header class="site-header" data-pb-include="site-header"></header>
  <main>
    <section class="hero" data-pb-scope="hero">
      <h1 data-pb-field="title">Page not found</h1>
      <p class="lede" data-pb-field="lede">There's no page at this address.</p>
      <p><a class="button" href="/">Go to the home page</a></p>
    </section>
  </main>
  <footer class="site-footer" data-pb-include="site-footer"></footer>
</body>
</html>
```
<!--/pb:generated-->

A page from `pb new page about --title "About" --description "Who we are."`. Its result names the link to add to the `<nav>` in `_partials/site-header.html`, here `<a href="/about/">About</a>`. Replace its description once the page has content:

<!--pb:generated new-page-->
`about/index.html`:
```html
<!doctype html>
<html lang="en">
<head data-pb-include="head">
  <title>About - My site</title>
  <meta name="description" content="Who we are.">
</head>
<body>
  <header class="site-header" data-pb-include="site-header"></header>
  <main>
    <section class="hero" data-pb-scope="hero">
      <h1 data-pb-field="title">About</h1>
    </section>
  </main>
  <footer class="site-footer" data-pb-include="site-footer"></footer>
</body>
</html>
```
<!--/pb:generated-->

`pb init` also writes:
- `assets/site.css`: the look, a floating glass header on a plain page that follows the reader's light or dark setting. Restyle it freely; keep the bottom-right corner clear, where previews show their tag.
- `.gitignore`, with `.pb/`, the `pb` command's cache.
- A Pagebutler section in `AGENTS.md`, between its two `pb:agents` markers, pointing agents at this skill. `pb setup` keeps the text between those markers current; write the site's own notes outside them, since a section changed inside them is never updated. `AGENTS.md` and `CLAUDE.md` are never served. A `CLAUDE.md` in the folder without `@AGENTS.md` hides that section from Claude Code, and so does a `CLAUDE.md` in any folder above the site, which `pb init` doesn't read: when there is one, tell the person.

`pb init` never overwrites a file. In a folder that already holds a site it writes no page, partial or stylesheet, only `pb.config.json` and the `.pb/` line (`references/existing-site.md`); in a folder with no page, when a file it would write is there, it writes nothing and names it. An existing `.gitignore` or `AGENTS.md` is no obstacle: it adds `.pb/` or its section to the end. For a site without a not-found page, `pb new page 404` writes one with the site's partials.
