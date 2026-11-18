# A site that already exists

Use this when the person's site was made somewhere else: a folder of pages from another host, a site builder's export, or a site generator's project, such as Eleventy, Hugo, Jekyll or Astro. Pagebutler hosts the site's HTML as it is. The site keeps running where it is now until its domain points at Pagebutler.

## 1. Set it up with `pb init`
In the folder that holds the site, run `pb init --name <slug> --title "<title>"` with the person's own names: the domain without its ending, such as `acme-shop` for acme-shop.example, makes a good slug. Never write `pb.config.json` from scratch. Then do what its `Next:` line says.
- **A folder of pages:** it adds only `pb.config.json` and a `.pb/` line in `.gitignore`, and changes no page.
- **A generator's project** (`eleventy.config.js` or `.eleventy.js`, `hugo.toml`, `astro.config.mjs` and the like, or the generator in `package.json`): run it in the project's folder. It finds the folder the generator built, such as `_site/`, and sets that up instead, with the generator as `import.from` in its `pb.config.json`; leave `import` as it is, since it is how every check and deploy know to say where the pages came from. That folder is the site from now on: work in it, never in the project's templates, data or Markdown. Never run the generator again, or put it in `build.command`: a new build replaces the pages and everything changed in them, and `pb deploy` refuses a command that writes pages.
- **Not built yet:** build the project once, with the command its answer names, then run `pb init` again.
- **Pages of its own beside a generator's leftovers,** such as a `_site/` from `jekyll serve` or a `package.json` naming a generator that didn't build these pages: add the generator's folder (`_site/`) to `.pbignore`, then run `pb init` again, which then sets up this folder.
- **`pb.config.json` gone after a build** (most generators empty their folder): run `pb init` there again with the site's own `--name` and `--title`, never a name made from `dist` or `_site`.
- **An app**, whose `index.html` is an empty page a script fills in: `pb init` refuses it. Ask the person before you rewrite it as pages (`references/telling-the-person.md`).
- **`pb.config.json` already there:** it is a Pagebutler site. Go on to step 2.

## 2. Check it
Run `pb check`, and fix every error before you deploy. On a site made elsewhere, most come from files that aren't part of the site.

## 3. Leave out what isn't the site
Files beside the pages that visitors shouldn't get, such as notes, deploy scripts, tests and tools, go in `.pbignore`: `pb check` names each line to add, and `references/site-rules.md`, "Files that aren't the site", says how. Never delete, move or rename a person's file to keep it out: their current host may still use it. An `.htaccess` stops applying here: write the rules the site needs in `_redirects` and `_headers` (`references/serving.md`), then add `.htaccess` to `.pbignore`. `pb check` names each rule with no equivalent, with its line; when its warning says it wasn't read (`details.unread`), read its rules yourself. One that kept visitors out of files means they'd be public here: leave those files out too. Rules forcing HTTPS, `www` or a trailing slash need nothing, nor does `ErrorDocument 404 /404.html`; another not-found page is `routing.notFound` in `pb.config.json`. A page other sites show in a frame, such as one its guides tell people to embed in Notion, stays embeddable only with a rule in `_headers` (`references/serving.md`). A form the old host sent itself, such as Netlify's or one posting to a `.php` file, doesn't send here: `pb check` names it (PB-10 `server-form`). Leave it as it is, since the current host may still send it, and tell the person (below).

## 4. Shared parts and languages
- A header or footer repeated on every page works as it is. Make one a partial only when it is the same on every page.
- Pages in several languages: each page's `<html lang>` says its language, and an edit changes only the pages in its own. `pb init` sets only `site.language`, from the home page: before the first deploy, add the others to `pb.config.json` as the pages write them, such as `"locales": { "enabled": ["de", "fr", "zh-CN"] }`. `pb check` names one left out (PB-T803).
- A partial holds one text, which every page including it shows, in any language, until someone edits it in theirs. So a part whose words differ by language is one partial for each language, such as `site-footer-de`, or stays on each page: never one partial in one language.
- A partial fills the element that includes it, replacing everything inside it, and takes no parameters: two versions are two partials. `<head>` keeps its own elements, so leave out of each page's head what the head partial holds. A `_partials/` folder from another tool must be the same: plain HTML with no `{{ }}`, `{% %}` or `<% %>`, and each including element empty, or holding exactly what the partial holds. Fix what `pb check` names.
- A tool of the site's own that copies shared parts into each page isn't needed once they are partials: don't run it, or put it in `build.command`.

## 5. Make its words editable
Run `pb annotate`, then `pb annotate --accept`, before you write any `data-pb-field` yourself (`references/names.md`, "Pages with no fields yet"). Then run `pb check` again.

## 6. Deploy
Run `pb deploy`: a preview, as for any site. Publish only when the person asks; the domain comes after that (`references/domains.md`).

## What to tell the person
- At the first hand-over, with the preview's links: "This is a preview of your site on Pagebutler, not live yet. Your current site is unchanged, and keeps running until your domain points here."
- A site a generator built: once a session, in the words `references/telling-the-person.md` gives for generator output.
- When git leaves the built folder out (`git check-ignore -q _site` succeeds), ask: "The pages Eleventy built aren't saved in your git repository, which is set to leave them out. Now that they are the site, shall I keep them in git?"
- A form the old host sent itself (PB-10 `server-form`): the form line in `references/telling-the-person.md`. When its message names Netlify's form handling, Cloudflare Pages' Static Forms or a file's code, such as PHP code, rather than "server code", add: "Your current site still sends it until your domain points here."
- Files you left out: only what the person will miss, such as a redirect their old host made that the site doesn't have yet.
- Each `.htaccess` rule `pb check` names, by what it did for visitors: "Your old server counted store-link taps in its log. Pagebutler can't, so they aren't counted from now on." Nothing for one that only set up the old server, such as a PHP setting.
