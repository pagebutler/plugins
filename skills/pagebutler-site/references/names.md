# Names and field paths

Every field has a path, worked out from where its name sits. The path is the field's identity: what a person edits is kept under it. Renaming a field, or moving it into another scope, makes a new field, and a person's value on the old one loses its place. So choose names once, and read this before you name anything.

## The order
<!--pb:generated resolution-order-->
1. Inside `data-pb-ignore` in the same file, it is not a field.
2. In a collection context (v1), a name the collection binds takes that binding: declared fields, built-ins, `seo.*` and one-step references. Collection binding never crosses an include fence.
3. A dotted name is absolute.
4. Otherwise, the scopes and list items above build the path; the nearest dotted scope restarts it as absolute.
5. What remains is prefixed with `partials.<name>.` inside a partial, else with `pages.<page id>.`.
<!--/pb:generated-->

## Every place a name can sit
Rows marked "renders from v1" are read in v0 as written, with no paths.

<!--pb:generated resolution-table-->
- **A page, outside any scope**: `data-pb-field="title"` gives `pages.<page>.<name>`. An undotted field is page-local: each page has its own value.
  - `index.html`: `<h1 data-pb-field="title">Ship faster</h1>` gives `pages.index.title`
- **Inside an undotted scope**: `data-pb-scope="hero"` around `data-pb-field="title"` gives `pages.<page>.<scope>.<name>`. An undotted scope is page-relative: the same scope on two pages gives two separate values.
  - `about/index.html`: `<section data-pb-scope="hero"><h1 data-pb-field="title">Small on purpose.</h1></section>` gives `pages.about.hero.title`
- **Inside a dotted scope**: `data-pb-scope="site.contact"` around `data-pb-field="phone"` gives `<scope>.<name>`. A dotted scope is absolute: its fields are one site-wide value on every page that carries it.
  - `contact/index.html`: `<p data-pb-scope="site.contact"><span data-pb-field="phone">+44 20 7946 0000</span></p>` gives `site.contact.phone`
- **A `site.*` or other dotted field name**: `data-pb-field="site.phone"` gives `<name>`. A dotted name is absolute, inside any scope and in any partial: one site-wide value.
  - `about/index.html`: `<section data-pb-scope="hero"><a data-pb-field="site.phone" href="tel:+442079460000">+44 20 7946 0000</a></section>` gives `site.phone`
- **Nested scopes**: `data-pb-scope="hero"` around `data-pb-scope="stats"` gives `pages.<page>.<scope>.<scope>.<name>`. Scopes join their names; the nearest dotted scope restarts the path as absolute.
  - `work/case/index.html`: `<section data-pb-scope="hero"><div data-pb-scope="stats"><b data-pb-field="figure">12</b></div></section>` gives `pages.work/case.hero.stats.figure`
- **Inside a partial**: `data-pb-field="legal"` in `_partials/site-footer.html` gives `partials.<partial>.<scopes>.<name>`. A field in a partial is one value on every page that includes it. The include is a fence: scopes, lock, ignore and collection binding above it never reach inside, so a partial inside a list item is PB-T307.
  - `_partials/site-footer.html`: `<p data-pb-field="legal">(c) 2026 Example Studio.</p>` gives `partials.site-footer.legal`
- **Inside a list item**: `data-pb-item="ana"` in a `data-pb-type="list"` field gives `<list path>[<key>].<name>`. Every item has a key; the fields inside are relative to the item, and a field on the item element itself is `<list>[<key>].<name>`. Within one element, `data-pb-item` applies before `data-pb-field` of a list and before `data-pb-scope`, whatever their order in the source. An element between a list and its item is PB-T303.
  - `about/index.html`: `<ul data-pb-field="team" data-pb-type="list"><li data-pb-item="ana"><p data-pb-field="bio">Designs the type.</p></li></ul>` gives `pages.about.team[ana].bio`
- **Inside another field**: `data-pb-field="lede"` inside `data-pb-field="intro"` gives None when the outer field binds content: PB-T211. Inside a link that holds elements: `pages.<page>.<name>`. A text, rich, number or date field's value is all of its content, so a field inside it is PB-T211. A link whose content is elements binds only `href`, so a field inside it is its own value; a list holds items, and an image holds nothing.
  - `index.html`: `<div data-pb-field="intro"><p data-pb-field="lede">Small on purpose.</p></div>` gives no path (PB-T211)
- **Inside `data-pb-ignore`**: `data-pb-ignore` around a `data-pb-field` gives None: not a field. Inside `data-pb-ignore` in the same file, `data-pb-field` makes no field.
  - `index.html`: `<div data-pb-ignore><p data-pb-field="caption">Drawn live</p></div>` gives no path (PB-T105)
- **Head elements on a normal page**: `<title data-pb-field="seo.title">` gives None: PB-T205. No field lives in a normal page's `<head>`, in the page or in a head partial. Write `<title>` and `<meta name="description">` as plain tags; the SEO panel reads them from v1.
  - `about/index.html`: `<title data-pb-field="seo.title">About</title>` gives no path (PB-T205)
- **An entry template** (renders from v1): `data-pb-field="title"` in `blog/_post.html` gives Bound: `entries.<collection>/<slug>.<name>`. Otherwise: `pages.<folder>/_<name>.<name>`.
- **A tag or author archive template** (renders from v1): `data-pb-field="tag.name"` in `blog/tag/_tag.html` gives Subject: `entries.<collection>/<slug>.<name>`. SEO: `pages.<archive page>.seo.*`. Otherwise: `pages.<folder>/_<name>.<name>`.
- **A collection list item** (renders from v1): `data-pb-field="title"` inside `data-pb-collection="posts"` gives `entries.<collection>/<slug>.<name>`, per rendered item.
- **Inside a tags field** (renders from v1): `data-pb-field="name"` inside `data-pb-field="tags"` gives `name`: `entries.tags/<tag>.name` when tags is a collection, else none. `url`: none.
- **Inside `data-pb-pagination`** (renders from v1): `data-pb-field="prev"`, `"next"`, `"page"` or `"pages"` gives None: bindings PageButler fills. Other names: `pages.<page>.<name>`.
- **A hand-authored entry page** (renders from v1): `data-pb-entry="posts/notes-on-glass"` on `<html>` gives Declared fields: `entries.<collection>/<slug>.<name>`. Otherwise: `pages.<page>.<name>`.
<!--/pb:generated-->

## Page ids
<!--pb:generated page-id-rule-->
1. Take the file's path relative to the site root, in Unicode NFC with `/` separators.
2. Drop the `.html` extension.
3. Drop a final `index` segment. If nothing is left, the id is `index`.
4. In each segment, percent-encode every UTF-8 byte outside `A-Z a-z 0-9 - _ ~` as `%` plus two upper-case hex digits, so `.` is `%2E` and `%` is `%25`. Case is kept.
5. Two files with one id are an error: `about.html` beside `about/index.html`, and a page in a folder named `index`, are the site contract's PB-09, and any other pair is PB-T208. The id never depends on `routing.trailingSlash`.

| File | Page id |
|---|---|
| `index.html` | `index` |
| `about.html` | `about` |
| `about/index.html` | `about` |
| `work/case/index.html` | `work/case` |
| `blog/tag/index.html` | `blog/tag` |
| `404.html` | `404` |
| `blog/_post.html` | `blog/_post` |
| `blog/tag/_tag.html` | `blog/tag/_tag` |
| `docs/v1.2/index.html` | `docs/v1%2E2` |
<!--/pb:generated-->

## Writing names
<!--pb:generated path-rules-->
- `pages`, `partials` and `entries` are roots. `seo` and `code` are platform-owned at the global root and as the first step after a page id: they parse, but authoring them is refused outside collection contexts (PB-T206).
- `data-pb-field` and `data-pb-scope` values are names separated by dots, with no brackets, `/` or `#`. `data-pb-item` is a key. `data-pb-include` is a partial name. `data-pb-entry` is `collection/slug`.
- A name or key: `name, key    = %x61-7A 0*63( ALPHA / DIGIT / "-" / "_" )   ; lower-case letter first`
<!--/pb:generated-->

## Pages with no fields yet
With `pb`, on pages that have no `data-pb-field` yet, such as a site made elsewhere, let `pb annotate` name the fields before you write any by hand: a field written first stops the group around it from being suggested, so the fields near it lose that group's name.
1. Run `pb annotate`. It lists each field it would mark, with its path, and changes nothing. On a large site, read one page at a time with `--page <file>`. Where it says blocks would be a list in an element of their own, such as a changelog's entries beside its heading, wrap them in one, such as a `<div>`, keeping how the page looks, and run it again before you accept.
2. Run `pb annotate --accept`, which adds those attributes and nothing else, or `pb annotate --accept --only <path>,<path>` for some of them. A list is accepted whole; quote a path holding `[`.
3. Before the first deploy that carries them, rename any `data-pb-field`, `data-pb-scope` or `data-pb-item` value that reads badly, such as `acme-is-a-design-studio` from a lede's first words, which reads better as `lede`.
4. Run `pb check`, then `pb deploy`: a preview.
