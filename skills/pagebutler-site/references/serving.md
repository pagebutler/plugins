# Serving: redirects, headers, caching, robots.txt, the sitemap, minifying and file types

## Redirects and headers
- Write `_redirects` and `_headers` at the site's root, in Cloudflare's format, never Netlify's. Netlify's forms (`301!`, a `404` rule, conditions on a visitor's country, language, cookie or role) are refused, naming the line. Tell the person when a rule by language or country goes (`references/telling-the-person.md`).
- The limits: 2,000 plain redirects, then 100 with `*` or `:name`; 100 header rules; 2,000 characters a line; 1 MiB a file; at most one `*` or `:name` in each part of a path. A line Cloudflare would skip stops the deploy, naming the file and the line.
- Redirects apply before files. A rule that shows one page at every address, such as `/* /index.html 200`, is a single-page app's: the deploy is refused. Write each page as HTML instead.
- `_headers` can't set or remove `Set-Cookie`, `Clear-Site-Data`, or the headers that carry the response itself, such as `Content-Length`, `Content-Encoding` and `Transfer-Encoding`. Nothing may use `/_pb/`, which is Pagebutler's on every site.
- Every site gets safe default headers: no guessing a file's type, only the site's address sent as the referrer, the camera, microphone and location blocked, and framing by the site itself only. `_headers` changes or removes any of them, and a site's own `Content-Security-Policy` replaces Pagebutler's whole.
- Only for a page other sites should frame: under its path in `_headers` (`/` for `/?embed`: rules never read the query), add `! Content-Security-Policy` for any site, or a policy with `frame-ancestors 'self' https://<host>` for named ones. With a policy of the site's own, add the hosts, or `*`, to its `frame-ancestors` instead. Previews never allow it: check it live.

## Page addresses
- Use the answer's `url`; `site.url` only marks an own host for links and rules.
- `routing.trailingSlash` in `pb.config.json` defaults to `auto`: `about.html` is `/about`, and `docs/index.html` is `/docs/`. `always` makes both end in `/`; `never` makes neither end in `/`. The home page stays `/`, and assets keep their addresses.
- Live and preview HTTPS follow that policy. Live HTTP GET/HEAD reaches the canonical HTTPS address in one 301, query kept. A same-host rule to a page uses its canonical target and the rule's status; a 200 rule serves at the requested address.
- Use root-relative references, such as `/assets/site.css`, when pinning a policy: `assets/site.css` resolves from the page's address, which the policy can move. Page ids stay the same. The sitemap and current-link fill follow the policy.

## Caching
- Pages are never kept by browsers without checking. A file whose name carries a build fingerprint is kept for a year: `app.3f9a2b1c.css`, Vite's `/assets/name-XXXXXXXX.js`, Astro's `/_astro/`.
- Give compiled files fingerprinted names, so a change reaches visitors at once. For other files that never change, list their paths in `pb.config.json` under `headers.immutablePatterns`, each with at most one `*` and never matching a page.
- `_headers` can give any file its own `Cache-Control`.

## robots.txt and the sitemap
- When the site has no `robots.txt`, Pagebutler writes one that allows every crawler and names the sitemap, the site's own or Pagebutler's.
- Pagebutler writes `sitemap.xml` when the site has no sitemap at its root (`sitemap*.xml`) and either Pagebutler wrote `robots.txt` or the site's own has a `Sitemap:` line on the site's address, another active domain, or its `butler.page` host.
- Pagebutler's sitemap lists each page at its live address, leaving out the not-found page and pages that say `noindex`, redirect, or have a canonical link to another page or an external domain. With no page left, it writes none.
- Don't add either file to a site that has neither: Pagebutler's sitemap follows the pages at every deploy. Write `robots.txt` only for rules the person asks for, such as keeping crawlers out of a folder, and give it the `Sitemap:` line above so Pagebutler's sitemap stays. Keep the files a site already has.
- Previews always answer `/robots.txt` with a file that keeps crawlers out. The site's own, or Pagebutler's, shows on the live address once published.
- When you change a page that is live, keep its `<link rel="canonical">`, and don't make every page say `noindex`, in a meta tag or in `_headers`: otherwise the preview can't be published, and nothing overrides that.

## Minifying and compressing
- Pagebutler minifies the pages and stylesheets it serves, previews and live alike, without changing how they look, and stores a Brotli and a gzip copy of each file that compresses. Write readable HTML and CSS; don't minify them yourself.
- In pages, each run of spaces and line breaks becomes one space or one line break, and comments are removed, except `pb:` markers and conditional comments. To keep a comment, such as a copyright notice, start it with `<!--!`. Tags, attributes and scripts never change. Left as written: `<pre>`, `<textarea>`, SVG text, the spacing of a page whose CSS sets `white-space: pre` or `pre-wrap`, and a page a framework such as React or Svelte hydrates.
- Stylesheets and `<style>` blocks are minified, except a build's own files (a fingerprinted name or a `sourceMappingURL` comment), a file a page loads with `integrity`, and `<style>` on a page whose `Content-Security-Policy` allows styles by hash. A stylesheet or block over 1 MiB is also kept as written; if it comes from a library, use the library's minified build instead. Scripts, JSON and images are served as written. CSS that a style query compares (`@container style(...)` or `if(style(...))`) is kept as written.
- So a served page, preview or live, differs from your file in spacing and comments, carries Pagebutler's `pb:` regions and attributes such as `data-pb-path` (`references/constructs.md`), and shows the text people edited on Pagebutler: the live text on the live site and this version's link, and edits not live yet on staging's. Compare text, not bytes, and never copy a served page back into the site.
- To keep part of a page exactly as written, wrap it in `<!-- pb:keep -->` and `<!-- /pb:keep -->`. To serve every page as written, set `"minify": { "html": false }` in `pb.config.json`; for stylesheets, `"css": false` in the same object. Turn either off only when the person asks, when a script reads the page's comments or spacing, or, for `css`, when a script compares a custom property's text.

## Rendering on this computer
- `pb build` writes the site into `.pb/build/` as Pagebutler renders it, after running `build.command`: partials in, fields filled with the values in the files. Read a page there to see what its partials and fields become. It needs no sign-in and uploads nothing.
- It doesn't write robots.txt or a sitemap, minify, or show text people changed on Pagebutler, and copies an uploaded image only from `.pb/assets/`, where `pb pull` fetches the ones the site uses. Its pages link from the site's root, so opened as files they show without their styles.
- Its folder is not a preview: never give the person its path, copy its pages back into the site, or deploy it. Only `pb deploy` makes a preview.

## File types
- Every served file needs a type Pagebutler knows. Archives (`.zip`), programs (`.exe`) and compressed copies (`.gz`, `.br`) are refused; keep the uncompressed files, since Pagebutler compresses what it serves. Documents for download, such as `.pdf`, `.docx` and `.epub`, are served.
- A data file a script loads with an extension Pagebutler doesn't serve: rename it to `.bin`, or `.txt` or `.json` for text, and change the script to match.
- A file without an extension is refused unless its name is a known one, such as `LICENSE`, `README` or `CNAME`, or a known file under `.well-known/`.
- Nothing whose name starts with `.` is served, except `.well-known/` at the site's root. `.env` files and `.git` folders refuse the upload. An `.htaccess` gets a warning: Pagebutler doesn't run Apache, so its rules stop applying.
- `_partials/`, `_`-prefixed `.html` templates and the control files (`_redirects`, `_headers`, `pb.config.json`) are never served. Other `_` folders are ordinary assets.
- Pages are UTF-8. Server code (`.php`, `functions/`, `_worker.js`) is refused: Pagebutler runs no code on its servers.
- Every page reads correctly with JavaScript off; scripts add to it.

## Animation and WASM
- Self-host the Rive runtime's WASM instead of loading it from a CDN: pin its version, give the file a hashed name, and preload the `.riv` file the page plays.
- Never set COOP or COEP by default: they break cross-origin iframes, fonts and scripts from CDNs, and Rive, GSAP, Three.js and Lottie don't need them. When a page does, set them for that path alone in `_headers`.
- When you set a `Content-Security-Policy`, allow `'wasm-unsafe-eval'` and `worker-src blob:`, and keep `'self'` in `style-src`: previews link their tag's stylesheet from `/_pb/`, and a policy that blocks it leaves the tag unstyled at the foot of each preview page. The live site gets nothing from Pagebutler.
- `.wasm` is served as `application/wasm`. `.riv`, glTF (`.gltf`, `.glb`), `.ktx2`, `.hdr` and shader sources (`.glsl`, `.vert`, `.frag`) are served with their types.
- Keep text that a script splits or animates, such as with SplitText, written in full in the HTML: that is the text people edit.
