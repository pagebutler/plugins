# PB-T codes: the template layer

`pb check` and every deploy report a finding as its place (`file:line:column`), its severity, its code and a message, then a `hint:` line that names the fix. Follow the hint. An error stops a deploy; a warning doesn't, but read each one before you hand a preview over; info needs no change. Codes marked (v1) come only from constructs that render from v1. The site contract's PB codes: `references/site-rules.md`.

<!--pb:generated template-codes-->
| Code | Name | Severity | What it means |
|---|---|---|---|
| PB-T101 | `unknown-attribute` | error | not a Pagebutler attribute |
| PB-T102 | `unknown-value` | error | value not allowed |
| PB-T103 | `element-cannot-bind` | error | element cannot carry this field |
| PB-T104 | `attribute-out-of-context` | error | attribute in the wrong place |
| PB-T105 | `lock-ignore-reach` | warning | lock or ignore reaches differently than it reads |
| PB-T106 | `lossy-default` | warning | what an edit drops |
| PB-T107 | `default-not-held` | info | a default its type can't hold |
| PB-T108 | `field-size` | error | content over 262,144 characters |
| PB-T109 | `content-size` | error | a site's fields over one version's 25 MiB |
| PB-T110 | `page-fields` | error | a page's fields over 10,000, items added to its lists counted |
| PB-T111 | `value-not-shown` | warning | an edited value this version can't show |
| PB-T112 | `unplaced-values` | info | edited values with no field in this version |
| PB-T113 | `item-copies` | error | fields copied by added items over 100,000 across a site |
| PB-T114 | `ids-not-added` | warning | page sent from a chat deployed without field ids |
| PB-T115 | `reattached-fields` | warning | fields re-attached to text whose words changed |
| PB-T116 | `image-without-size` | info | an image field with no size in its code |
| PB-T117 | `no-fields` | info | no page has a field yet |
| PB-T118 | `image-not-uploaded` | warning | an image address the site's library doesn't have |
| PB-T201 | `reserved-root` | error | starts with `pages`, `partials` or `entries` |
| PB-T202 | `malformed-name` | error | breaks the name rules |
| PB-T203 | `dotted-on-one-page` | warning | dotted, on one page only |
| PB-T204 | `differing-defaults` | error | one path, different defaults |
| PB-T205 | `field-in-head` | error | a field inside `<head>` |
| PB-T206 | `reserved-name` | error | `seo`, `code` or an undotted `site` as a first name |
| PB-T207 | `path-shape-conflict` | error | one path, two shapes |
| PB-T208 | `page-id-collision` | error | two files, one page id |
| PB-T209 | `similar-paths` | warning | two paths that read as one |
| PB-T210 | `repeated-page-value` | info | same text on many pages |
| PB-T211 | `field-in-field` | error | inside a field that binds content |
| PB-T301 | `item-without-key` | error | list item without a key |
| PB-T302 | `duplicate-key` | error | two items with one key |
| PB-T303 | `list-shape` | error | list without items, or a child that is not an item |
| PB-T304 | `list-in-item` | error | list inside a list item |
| PB-T305 | `dotted-name-in-item` | error | dotted name inside a list item |
| PB-T306 | `item-key-in-collection` | error | keyed item in a collection list or tags field (v1) |
| PB-T307 | `partial-in-item` | error | partial inside a list item |
| PB-T401 | `partial-missing` | error | partial does not exist |
| PB-T402 | `partial-cycle` | error | partials include each other |
| PB-T403 | `partial-limit` | error | partials nested more than 5 deep, or a page over a limit once they are in |
| PB-T404 | `include-host-attribute` | error | vocabulary attribute on a partial's host |
| PB-T405 | `partial-shape` | error | document tags in a partial, body content in a head partial, or a partial cut off inside a tag |
| PB-T406 | `include-encoding` | error | page or partial not UTF-8 |
| PB-T407 | `template-syntax` | warning | another tool's template syntax in a partial |
| PB-T408 | `include-host-children` | warning | an include's host holds markup its partial replaces |
| PB-T409 | `head-partial-repeats` | warning | a head partial repeats what the page's head holds |
| PB-T501 | `field-in-template` | warning | inside `<template>` |
| PB-T502 | `field-in-script` | warning | in `<script>` text |
| PB-T503 | `field-in-js-file` | warning | in a JavaScript file |
| PB-T601 | `generated-in-source` | error | written by Pagebutler, found in source |
| PB-T602 | `page-attribute-reserved` | error | reserved for localisation |
| PB-T603 | `unknown-marker` | error | `pb:` marker Pagebutler does not know |
| PB-T604 | `region-placement` | error | a page Pagebutler can't add its regions to |
| PB-T605 | `keep-pairing` | warning | a `pb:keep` marker without its pair |
| PB-T701 | `collection-undeclared` | error | collection not in `pb.config.json` |
| PB-T702 | `collection-item-count` | error | not exactly one item template |
| PB-T703 | `name-not-declared` | error | matches nothing the collection declares (v1) |
| PB-T704 | `builtin-shadowed` | error | manifest field repeats a built-in |
| PB-T705 | `paginated-list-count` | error | more than one paginated list |
| PB-T706 | `entry-page-mismatch` | error | hand-authored entry page that doesn't fit its collection |
| PB-T707 | `entry-slug-taken` | error | entry page and stored entry share a slug (v1) |
| PB-T708 | `pagination-binding` | error | pagination link on the wrong element (v1) |
| PB-T709 | `tags-container-name` | error | name other than `name` or `url` in a tags field (v1) |
| PB-T710 | `template-head-unbound` | error | plain title or description in a template head (v1) |
| PB-T711 | `nested-collection` | error | collection list inside an item |
| PB-T712 | `multi-hop-reference` | error | reference walks more than one step (v1) |
| PB-T801 | `lang-invalid` | warning | page language is not a valid language tag |
| PB-T802 | `lang-ambiguous` | error | page language matches several locales |
| PB-T803 | `lang-unmatched` | info | page language is not a site locale |
| PB-T804 | `manifest-mismatch` | warning | differs from the manifest |
| PB-T805 | `language-unset` | warning | no page language and no site language |
| PB-T901 | `renders-later` | warning | renders from v1 |
<!--/pb:generated-->
