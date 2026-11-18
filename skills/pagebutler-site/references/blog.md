# The blog

Never scaffold a site generator for a blog: on Pagebutler, the blog is a collection. `pb init --blog` adds one beside the starter site: a `posts` collection and an `authors` collection in `pb.config.json`, the blog's index page, and three templates Pagebutler renders once per post, tag and author.

## Until collections render (v1)
Collections render from v1. Until then:
- `/blog/` is served exactly as written, sample post included. The templates are kept with each version, and nothing is made from them: no post pages, lists, archives or feed.
- `pb check` gives a PB-T901 warning on each template, list and collection. They need no change: keep the markup.
- The header doesn't link to `/blog/`. Leave it so until posts render.
- Don't write posts as pages under `/blog/`: those addresses belong to the collection. A page the person needs now goes elsewhere, written with `pb new page`.
- Tell the person at every hand-over that the blog shows a sample post until Pagebutler starts building blogs in a later release, before you promise it works.

## The files
They are shown for a site titled "My site", in English. The `collections` block of `pb.config.json`:

<!--pb:generated blog-collections-->
```json
{
  "collections": {
    "posts": {
      "path": "/blog/:slug/",
      "template": "/blog/_post.html",
      "fields": {
        "title": "text",
        "excerpt": "text",
        "cover": "image",
        "tags": "tags",
        "author": "ref:authors",
        "body": "rich"
      },
      "sort": "-date",
      "feed": "/blog/rss.xml",
      "archives": {
        "tags": "/blog/tag/_tag.html",
        "author": "/blog/author/_author.html"
      },
      "seo": {
        "type": "BlogPosting"
      }
    },
    "authors": {
      "fields": {
        "name": "text",
        "bio": "rich",
        "avatar": "image"
      }
    }
  }
}
```
<!--/pb:generated-->

The index, `blog/_post.html` (the post template), `blog/tag/_tag.html` and `blog/author/_author.html` (the archives):

<!--pb:generated blog-templates-->
`blog/index.html`:
```html
<!doctype html>
<html lang="en">
<head data-pb-include="head">
  <title>Blog - My site</title>
  <meta name="description" content="Posts from My site.">
</head>
<body>
  <header class="site-header" data-pb-include="site-header"></header>
  <main>
    <section class="hero" data-pb-scope="hero">
      <h1 data-pb-field="title">Blog</h1>
    </section>
    <ul class="posts" data-pb-collection="posts" data-pb-sort="-date">
      <li data-pb-item>
        <a data-pb-field="url" href="/blog/sample-post/"><span data-pb-field="title">Sample post</span></a>
        <time data-pb-field="date" data-pb-format="long" datetime="2026-01-01">1 January 2026</time>
        <p data-pb-field="excerpt">A sentence about the post.</p>
      </li>
      <li data-pb-empty>No posts yet.</li>
    </ul>
  </main>
  <footer class="site-footer" data-pb-include="site-footer"></footer>
</body>
</html>
```

`blog/_post.html`:
```html
<!doctype html>
<html lang="en">
<head data-pb-include="head">
  <title data-pb-field="seo.title">Sample post</title>
  <meta name="description" data-pb-field="seo.description" content="A sentence about the post.">
</head>
<body>
  <header class="site-header" data-pb-include="site-header"></header>
  <main>
    <article>
      <h1 data-pb-field="title">Sample post</h1>
      <p class="byline">
        <time data-pb-field="date" data-pb-format="long" datetime="2026-01-01">1 January 2026</time>
        by <span data-pb-field="author.name">Sample author</span>
      </p>
      <div data-pb-field="body"><p>The post's text.</p></div>
      <ul class="tags" data-pb-field="tags">
        <li data-pb-item><a data-pb-field="url" href="/blog/tag/sample/"><span data-pb-field="name">Sample</span></a></li>
      </ul>
    </article>
  </main>
  <footer class="site-footer" data-pb-include="site-footer"></footer>
</body>
</html>
```

`blog/tag/_tag.html`:
```html
<!doctype html>
<html lang="en">
<head data-pb-include="head">
  <title data-pb-field="seo.title">Posts tagged Sample</title>
  <meta name="description" data-pb-field="seo.description" content="Posts tagged Sample.">
</head>
<body>
  <header class="site-header" data-pb-include="site-header"></header>
  <main>
    <h1>Posts tagged <span data-pb-field="tag.name">Sample</span></h1>
    <ul class="posts" data-pb-collection="posts" data-pb-per-page="10">
      <li data-pb-item>
        <a data-pb-field="url" href="/blog/sample-post/"><span data-pb-field="title">Sample post</span></a>
      </li>
    </ul>
    <nav class="pagination" data-pb-pagination aria-label="Pages">
      <a data-pb-field="prev" href="/blog/tag/sample/">Newer posts</a>
      <a data-pb-field="next" href="/blog/tag/sample/page/2/">Older posts</a>
    </nav>
  </main>
  <footer class="site-footer" data-pb-include="site-footer"></footer>
</body>
</html>
```

`blog/author/_author.html`:
```html
<!doctype html>
<html lang="en">
<head data-pb-include="head">
  <title data-pb-field="seo.title">Posts by Sample author</title>
  <meta name="description" data-pb-field="seo.description" content="Posts by Sample author.">
</head>
<body>
  <header class="site-header" data-pb-include="site-header"></header>
  <main>
    <h1 data-pb-field="author.name">Sample author</h1>
    <div data-pb-field="author.bio"><p>A sentence about the author.</p></div>
    <ul class="posts" data-pb-collection="posts" data-pb-per-page="10">
      <li data-pb-item>
        <a data-pb-field="url" href="/blog/sample-post/"><span data-pb-field="title">Sample post</span></a>
      </li>
    </ul>
    <nav class="pagination" data-pb-pagination aria-label="Pages">
      <a data-pb-field="prev" href="/blog/author/sample-author/">Newer posts</a>
      <a data-pb-field="next" href="/blog/author/sample-author/page/2/">Older posts</a>
    </nav>
  </main>
  <footer class="site-footer" data-pb-include="site-footer"></footer>
</body>
</html>
```
<!--/pb:generated-->

## Rules for the templates
- A `_`-prefixed `.html` file is a template: never served itself, and an error unless `pb.config.json` names it.
- How names bind inside templates, lists and archives from v1: `references/names.md`.
