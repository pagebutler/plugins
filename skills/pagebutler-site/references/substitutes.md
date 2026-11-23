# What to write instead

PageButler has no conditionals, expressions, loops, layouts, include parameters or values computed when a page is viewed. Never invent a `data-pb-*` attribute for them: an unknown one is an error (PB-T101 or PB-T102) whose message is the matching row below. Write the HTML as it should read, and do what the row says. A mark at the end of a row, such as (From v1: `feed`.), covers the constructs that row suggests, not its advice to remove the attribute, which holds now.

<!--pb:generated substitutions-->
- **Conditionals** (`data-pb-if`, `data-pb-unless`, `data-pb-else`, `data-pb-show`): PageButler has no conditionals. Remove it and write the element as it should always appear. Only collection lists hide content: an empty list is removed with everything in its container. (From v1: `data-pb-collection`.)
- **Expressions, filters, helpers** (`data-pb-expr`, `data-pb-value`, `data-pb-helper`, `data-pb-compute`): PageButler has no expressions, filters or helpers. Write the final text in the HTML. To format a date, use a `data-pb-format` preset: `long`, `short`, `numeric`, `month-year` or `iso`. (From v1: `data-pb-format`.)
- **Include parameters** (`data-pb-prop`, `data-pb-props`, `data-pb-param`, `data-pb-with`): Partials take no parameters. Give the host element a class and style the variant in CSS, or make a second partial, such as `site-header-dark`.
- **Layouts** (`data-pb-layout`, `data-pb-extends`, `data-pb-slot`, `data-pb-block`): PageButler has no layouts. Copy the page skeleton into each page, or, where `pb` is installed, run `pb new page <path>` to write one with the site's partials.
- **Loops and data files** (`data-pb-each`, `data-pb-for`, `data-pb-foreach`, `data-pb-repeat`): PageButler has no loops or data files. Use a list field for items an editor adds and reorders: `data-pb-type="list"` on the container and a keyed `data-pb-item` on each child. Use a collection for posts. (From v1: `collections`.)
- **Queries** (`data-pb-where`, `data-pb-query`, `data-pb-filter`, `data-pb-search`): Collection lists have no queries beyond their modifiers. Use `data-pb-sort`, `data-pb-limit`, `data-pb-offset`, `data-pb-tag`, `data-pb-author`, `data-pb-featured` or `data-pb-related`. For search or live filtering, write a script over the collections JSON endpoint. (From v1: `data-pb-collection`, `data-pb-sort`, `data-pb-limit`, `data-pb-offset`, `data-pb-tag`, `data-pb-author`, `data-pb-featured`, `data-pb-related`, `collections`.)
- **Front matter, page variables** (`data-pb-var`, `data-pb-set`, `data-pb-let`, `data-pb-meta`): Pages have no front matter or variables. Write each value where it shows: the title in `<title>`, the description in `<meta name="description">`, editable text in the page with `data-pb-field`.
- **Markdown pages** (`data-pb-markdown`, `data-pb-md`): Pages are HTML, not Markdown. Write the page in HTML. Markdown is accepted only as the body of a collection entry. (From v1: `_content/`.)
- **Custom feed and sitemap templates** (`data-pb-feed`, `data-pb-rss`, `data-pb-sitemap`): Feeds and the sitemap have no templates. Remove it. Set `feed` on the collection in `pb.config.json`; PageButler writes `sitemap.xml` and `robots.txt` itself when the site has neither. (From v1: `feed`.)
- **Shortcodes** (`data-pb-shortcode`, `data-pb-widget`, `data-pb-embed`): PageButler has no shortcodes. Write the embed's HTML in place; scripts that enhance it are fine.
- **Attribute binding** (`data-pb-attr`, `data-pb-bind`, `data-pb-href`, `data-pb-src`): Fields bind only their element's own value, not other attributes. Put `data-pb-field` on the element that holds the value: text in an element, a URL on `<a>`, an image on `<img>`. For a background image, use `<img>` with `object-fit: cover`.
- **Client-side rendering** (`data-pb-fetch`, `data-pb-render`, `data-pb-client`, `data-pb-hydrate`): Editorial content is not rendered by scripts. Write the content into the HTML. Scripts may enhance it, but text an editor changes must be in the page as served.
- **Relative dates** (`data-pb-ago`, `data-pb-timeago`, `data-pb-relative`): Dates have no relative format: "3 days ago" would go stale between publishes. Use a preset: `data-pb-format="long"`, `short`, `numeric`, `month-year` or `iso`. (From v1: `data-pb-format`.)
- **Values computed at view time** (`data-pb-year`, `data-pb-now`, `data-pb-copyright`): PageButler computes no values when a page is viewed. Write the year as text in a locked site-wide field: `<span data-pb-field="site.year" data-pb-lock>2026</span>`.
- **Active navigation** (`data-pb-active`, `data-pb-current`): PageButler marks the current link itself. Remove it: links in a partial that point at the current page get `aria-current="page"`, which CSS can style. For a whole section, add a class to `<body>`.
- **Date archives** (`data-pb-archive`): PageButler has no date archives. Use tags: each tag gets its own archive page. (From v1: `archives`.)
- **Nested collections** (`data-pb-collection` inside a `data-pb-item`): Collections do not nest, and a reference goes one step (`author.name`). Link to the related page instead, or list the entries on a tag or author archive. (From v1: `collections`, `archives`.)
- **Multi-hop references** (a path such as `author.team.name`): Collections do not nest, and a reference goes one step (`author.name`). Link to the related page instead, or list the entries on a tag or author archive. (From v1: `collections`, `archives`.)
<!--/pb:generated-->

## Other names for real attributes
Written under another name, these are errors whose hint gives the real attribute:

<!--pb:generated synonyms-->
| You might write | Write instead |
|---|---|
| `data-pb-editable` | `data-pb-field="..."` |
| `data-pb-text` | `data-pb-field="..."` |
| `data-pb-content` | `data-pb-field="..."` |
| `data-pb-edit` | `data-pb-field="..."` |
| `data-pb-group` | `data-pb-scope="..."` |
| `data-pb-section` | `data-pb-scope="..."` |
| `data-pb-list` | `data-pb-field="..." data-pb-type="list"` |
| `data-pb-repeater` | `data-pb-field="..." data-pb-type="list"` |
| `data-pb-readonly` | `data-pb-lock` |
| `data-pb-skip` | `data-pb-ignore` |
| `data-pb-exclude` | `data-pb-ignore` |
| `data-pb-partial` | `data-pb-include="..."` |
| `data-pb-component` | `data-pb-include="..."` |
| `data-pb-import` | `data-pb-include="..."` |
<!--/pb:generated-->

## Names you can't use
These names are PageButler's where each line says. Written there, they are errors:

<!--pb:generated reserved-names-->
- `pages`, `partials`, `entries`: The first name of a dotted `data-pb-field` or `data-pb-scope` (PB-T201).
- `seo`, `code`: The first name of a site-wide path, and the first step after a page id, outside collection templates (PB-T206).
- `site`: The first step after a page id or a partial name: an undotted `data-pb-field` or `data-pb-scope` named `site` (PB-T206).
- `slug`, `url`, `status`, `date`, `updated`, `readingTime`, `featured`: Collection field names in `pb.config.json`; in collection contexts they bind to the entry (PB-T704).
- `seo`: Collection field names in `pb.config.json` (PB-T704).
- `prev`, `next`, `page`, `pages`: Inside `data-pb-pagination` (v1) (PB-T708).
- `name`, `url`: Inside a tags field (v1) (PB-T709).
- `data-pb-path`, `data-pb-tags`, `data-pb-paged`, `data-pb-status`, `data-pb-generated`, `data-pb-entry`: Source files (`data-pb-entry` is authored on `<html>` only; `data-pb-featured`, which the bake also writes on rendered items, is a collection-list setting and PB-T104 elsewhere) (PB-T601).
- `pb:include`, `pb:seo`, `pb:seo-markup`, `pb:code-head`, `pb:platform`, `pb:code-body`: Comments in source files (PB-T601).
- `pb:keep`, `pb:todo`, `<meta name="pb:<region>" content="off">`: Source files.
- `data-pb-page`: Anywhere (PB-T602).
- `/_pb/*`: File paths, redirect sources and header rules (PB-16).
<!--/pb:generated-->
