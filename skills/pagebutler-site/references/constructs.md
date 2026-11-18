# Every attribute and file convention in v0

These are all of Pagebutler's attributes and file conventions that render in v0: where each goes, its value, the path it gives, and what editors can do with it. Any other `data-pb-*` attribute is an error that names what to write instead, and some names are kept for Pagebutler: both are in `references/substitutes.md`.

<!--pb:generated construct-cards-v0-->
### `data-pb-field`
Marks the element whose content an editor may change.
- Write: `data-pb-field="<name>"`
- Put it on: An element in a page's `<body>` or in a body partial that the element types allow; never inside `<head>`
- Value: A name, such as `title`, or a dotted name, such as `site.phone`: a lower-case letter, then letters, digits, `-` or `_`, at most 64 characters per name
- Path: Undotted: `pages.<page>.<scopes>.<name>`, or `partials.<partial>.<scopes>.<name>` in a partial. Dotted: the name itself, one site-wide value
- Editors: Change what the type binds: text in place, rich text, an image and its alt text, a link's text and address, a date, a number, or a list's items
```html
<!-- about/index.html -->
<h1 data-pb-field="title">Small on purpose.</h1>  <!-- pages.about.title -->
<img data-pb-field="portrait" src="/img/ana.jpg" alt="Ana at work" width="640" height="480">  <!-- an image field -->
<a data-pb-field="site.phone" href="tel:+442079460000">+44 20 7946 0000</a>  <!-- site.phone, one value site-wide -->
```

### `data-pb-scope`
Groups the fields inside an element under one name.
- Write: `data-pb-scope="<name>"`
- Put it on: Any element in a page's `<body>` or in a partial
- Value: A name, such as `hero`. A dotted name (`site.contact`, `shared.hero`) is site-wide
- Path: Undotted: page-local, `pages.<page>.<scope>.<field>`. Dotted: the name itself, one value on every page that carries it. In a partial: `partials.<partial>.<scope>.<field>`
- Editors: Nothing on the scope itself; they change the fields inside it
```html
<!-- about/index.html -->
<section data-pb-scope="hero">
  <h1 data-pb-field="title">Small on purpose.</h1>   <!-- pages.about.hero.title -->
</section>

<!-- any page -->
<p data-pb-scope="site.contact">
  <span data-pb-field="phone">+44 20 7946 0000</span>  <!-- site.contact.phone, one value site-wide -->
</p>
```

### `data-pb-type`
Sets a field's type when the element's own type is not the one wanted.
- Write: `data-pb-type="text|rich|image|link|date|number|list"`
- Put it on: An element that carries `data-pb-field`
- Value: `text`, `rich`, `image`, `link`, `date`, `number` or `list`; the element types table says which fit which element
- Path: None of its own: the field keeps its path
- Editors: Use the editor the type brings: in place for text, a toolbar for rich text, the image panel, a link panel, a date picker, a number input, or add, remove and reorder for a list
```html
<div data-pb-field="intro" data-pb-type="text">One plain line in a div.</div>
<ul data-pb-field="faq" data-pb-type="list">
  <li data-pb-item="pricing"><h3 data-pb-field="q">How much does it cost?</h3></li>
</ul>
```

### `data-pb-item`
Marks one item of a list field, with the key that names it.
- Write: `data-pb-item="<key>"` in a list field; `data-pb-item` with no value in a collection list or a tags field (v1)
- Put it on: Each element child of a `data-pb-type="list"` container; the item template of a collection list or tags field
- Value: A key: a lower-case letter, then letters, digits, `-` or `_`, at most 64 characters, unique in its list. No value in collection lists and tags fields
- Path: `<list path>[<key>]`, and the fields inside are relative to it: `pages.about.team[ana].bio`
- Editors: Add, remove, duplicate and reorder items, and change each item's fields; the item's structure and styling stay as written
```html
<!-- about/index.html -->
<ul data-pb-field="team" data-pb-type="list">
  <li data-pb-item="ana">
    <h3 data-pb-field="name">Ana Lima</h3>             <!-- pages.about.team[ana].name -->
    <p data-pb-field="bio">Designs the type systems.</p> <!-- pages.about.team[ana].bio -->
  </li>
  <li data-pb-item="max">
    <h3 data-pb-field="name">Max Weber</h3>
    <p data-pb-field="bio">Writes the code.</p>
  </li>
</ul>
```

### `data-pb-lock`
Makes the fields inside an element read-only for editors.
- Write: `data-pb-lock`
- Put it on: A field, or any element around fields, in a page's `<body>` or in a partial; not on an element with `data-pb-include`
- Value: None: it works by being present
- Path: None of its own: the fields keep their paths and stay in the merge
- Editors: Nothing: locked fields show a padlock. Owners and admins, and agents acting for them, can still change them through the API, and anyone in the site's code
```html
<p data-pb-field="site.legal" data-pb-lock>&copy; 2026 Example Studio. All rights reserved.</p>
```

### `data-pb-ignore`
Takes an element and everything inside it out of Pagebutler's view.
- Write: `data-pb-ignore`
- Put it on: Any element in a page's `<body>` or in a partial; not on an element with `data-pb-include`
- Value: None: it works by being present
- Path: None: nothing inside it is a field, and no hooks are written there
- Editors: Nothing inside it
```html
<div id="stage" data-pb-ignore><canvas width="800" height="600"></canvas></div>
```

### `data-pb-include`
Fills an element with a partial, a fragment shared by many pages.
- Write: `data-pb-include="<name>"` for `_partials/<name>.html`
- Put it on: Any element that can hold children, `<head>` included; not `<html>` or `<body>`
- Value: A partial name: lower-case letters, digits, `-` and `_`, with `/` for subfolders (`nav/main`); never a file path
- Path: Fields inside the partial are `partials.<name>.<scopes>.<field>`, one value on every page that includes it; dotted names stay absolute
- Editors: Change the fields inside the partial, which changes every page that includes it. Which partial a page uses is code
```html
<!-- about/index.html -->
<head data-pb-include="head">
  <title>About</title>
  <meta name="description" content="Who we are.">
</head>
<body>
  <header class="dark" data-pb-include="site-header"></header>
  <main>...</main>
  <footer data-pb-include="site-footer"></footer>
</body>
```

### `_partials/`
Holds the partials: HTML fragments included with `data-pb-include`.
- Write: `_partials/<name>.html`; subfolders are part of the name, so `_partials/nav/main.html` is `nav/main`
- Put it on: The site root; never served
- Value: A fragment with no `<html>`, `<head>` or `<body>` tags. A head partial holds head elements only
- Path: Its fields are `partials.<name>.*`
- Editors: Nothing on the file; they change the fields inside it
```html
<!-- _partials/site-footer.html -->
<p><span data-pb-field="site.name">Example Studio</span>
  <a data-pb-field="site.phone" href="tel:+442079460000">+44 20 7946 0000</a></p>
<p data-pb-field="legal" data-pb-lock>&copy; 2026 Example Studio.</p>  <!-- partials.site-footer.legal -->
```

### `.html` pages
A complete HTML document at its served path; its page id names its page-local fields.
- Write: `index.html`, `about.html` or `about/index.html`, `work/case/index.html`, `404.html`
- Put it on: Anywhere in the site except under `_`-prefixed paths
- Value: A complete document with `<html lang>`, `<head>` and `<body>`
- Path: `pages.<page id>.*`, with the id from the file path: `index`, `about`, `work/case`, `404`
- Editors: Change the page's fields; the page itself is code
```html
<!-- work/case/index.html: page id work/case -->
<!doctype html>
<html lang="en-GB">
<head data-pb-include="head"><title>A case study</title></head>
<body>
  <header data-pb-include="site-header"></header>
  <h1 data-pb-field="title">A studio site in a week</h1>  <!-- pages.work/case.title -->
  <footer data-pb-include="site-footer"></footer>
</body>
</html>
```
<!--/pb:generated-->

## From v1
These render from v1. In v0, `pb check` warns (PB-T901) and the page is served as written, so use them only where the starter blog does:

<!--pb:generated construct-list-v1-->
`data-pb-collection`, `data-pb-sort`, `data-pb-limit`, `data-pb-offset`, `data-pb-tag`, `data-pb-author`, `data-pb-featured`, `data-pb-related`, `data-pb-per-page`, `data-pb-pagination`, `data-pb-empty`, `data-pb-keep`, `data-pb-format`, `data-pb-fit`, `data-pb-entry`
<!--/pb:generated-->

## What Pagebutler writes
Pagebutler writes these into the pages it serves. Never write them yourself, and never copy them from a fetched page into your files:

<!--pb:generated construct-generated-v0-->
- `data-pb-path`: The merge key of a field, written on every field node Pagebutler bakes.
- `aria-current`: Marks a partial's link to the page it is on, written by Pagebutler.
- `pb:include`: The comments Pagebutler writes around an expanded partial.
- `pb:` regions: The marked regions where Pagebutler writes SEO tags, custom code and its script from v1, written empty until then.
<!--/pb:generated-->
