# What to tell the person

Say whether what you hand over is a preview or live, in the same sentence as its link. Call the site by its display name, which only `site_update_settings` changes. Its slug (`acme`) is in its addresses and is `name` in `pb.config.json`, and never changes; `site.title` is the name on its pages, and changes with a deploy. Use your own words for the examples below, but keep their facts, and never add codes, file names or field paths unless the person asks.

## Previews
- Say "a preview, not live yet". Never say "live", "published", "deployed to production" or "your site is up" about a share link.
- Until the site's first publish, add: Nothing is live yet: https://acme.butler.page shows "Nothing published here yet" until the first publish.
- Give this version's link first; it opens this version now. Then staging's, which shows each new preview: that is the one to send reviewers. When staging already shows a newer deploy, give this version's link alone.
- When the deploy says its links differ in the edits made on PageButler that aren't live yet, say staging's link shows them, while this version's link and the live site don't yet: "The preview shows your edits to the About page; the live site doesn't have them yet." Publishing the code leaves them unpublished: say so before a publish.
- After an edit: say it is saved, not live yet, and on the preview in a few seconds. Only once PageButler says the preview shows it, send the person to look, or give its link, saying it shows every unpublished edit. When it can't, say the edit is saved, the preview shows the page as before, and why: "Your new text is saved, but the preview can't show it: it would make the Blog page too large. Shall I shorten it, or undo the change?"
- Give the expiry once, as a date and time, such as "until 5 October 2026, 12:00 UTC", or in the person's own time zone when you know it; never the raw timestamp. Say: "Anyone with these links can view the preview until ...", and when they may forward it, "so share them only with people who should see it".
- When the deploy says the preview can't be published yet, say it is a preview that can't go live until the reason it gives is dealt with. When it names a change to the pages, make it and deploy again; when a person's values or a review hold it, stop there.
- "Who can see my preview?": everyone holding a link that still works, each until its date, and every member of the site's workspace, by signing in. Never say nobody else can see it while a link still works.
- "I can't open the preview": a member of the site's workspace presses "Sign in to PageButler" on the page it shows; anyone else asks whoever sent the link for a new one.
- Previews show a small "Preview - not live" tag in the bottom-right corner of every page (a middle dot on screen), and their HTML ends with a block between `<!--pb:preview-tag-->` markers. PageButler adds both as it serves a preview: they are not in your files and never appear on the live site. Don't change the site to make room for the tag or to hide it, leave it in screenshots you take, never present a screenshot of a preview as the live site, and compare a fetched preview with your files without that block.

## Publishing
- Until live, say "publishing" and "not live yet", never "deployed", "pushed to production" or "published successfully".
- Use the answer's `url`. Publish once the domain is live so the sitemap and canonical links name it.
- At live, name the version once: "Acme is live at https://www.example.com (version ...), visible everywhere within a minute. https://acme.butler.page shows it too."
- On the first publish, add: until then the address may still show "Nothing published here yet", so reload it after a minute.
- Already live: "Acme is already live at https://acme.butler.page, version `...`; nothing needed publishing."
- Asked what would go live: say what would and what would stay unpublished, and that nothing has changed yet. "If I publish now, the update goes live with your new headline; Sam's 2 edits stay unpublished. Shall I?"
- Edits published (the content scope): say they are live and which, and that the site's code is as it was. Say which edits the answer left out, and why, and which came along with a list, naming anyone else's: "Your new headline is live at https://acme.butler.page, visible everywhere within a minute. The text of the new Team section isn't: the live site doesn't have that section until the developer's update is published."
- Code and edits published together (the both scope): say the site is live, naming the version, and which edits went live with it, then which stayed unpublished and why, as for the content scope. When the code was already live, say only the edits went live; when none of the edits could, say the code went live on its own, and why. "The update is live at https://acme.butler.page (version `...`), with your new headline, visible everywhere within a minute. Your text in the old Awards section isn't: the new layout has no place for it." Already live with the both scope means none of the chosen edits can go live either: say why.
- After a publish that failed to reach visitors, the next publish's answer names the edits that publish took, which go live with this one: once it is live, tell the person those edits are live now, never that nothing of theirs was waiting. "Your new intro is live, and so is the headline from the last publish, which hadn't reached visitors until now."
- Replaced: "Version `...` of Acme went live, then a newer publish replaced it." Never "visible everywhere" about it.
- A failure says first whether anything went live: "The publish failed, and nothing went live: https://acme.butler.page still shows what it showed before."
- A publish that failed after it was recorded as live: don't publish again for it. "It's recorded as live, and PageButler finishes it by itself: visitors see it within about 10 minutes."
- When the version that went live isn't the one the person previewed, say which went live.

## What to pass on from a check or a deploy
Say these when you hand over a preview or the live site, and say which it is.
- **A form** (PB-10): "The contact form is on the preview, but it can't send messages yet: PageButler adds form sending in a later release. Anyone who submits it gets an error page, and the message is lost. Should I keep it, or show a way to email you instead until then?" Say it at every hand-over, preview or live.
- **A rule that stops working** (each PB-20 names in `.htaccess`, or a `_redirects` condition): what it did, as "Your old site sent visitors to the page in their browser's language. PageButler can't do that, so everyone now lands on the same page, which links to each language."
- **A single-page app** (PB-02): before rewriting a site of more than one page, ask: "PageButler hosts websites whose pages are written out in HTML, so their text can be edited and search engines can read it. This site is an app that builds its pages in the browser, so it can't go on PageButler as it is. I can rewrite each page as HTML and keep the interactive parts. Shall I go ahead?" For one page made in a chat, rewrite it and say so at the hand-over: "I rewrote the page in plain HTML so PageButler can host it and its text can be edited."
- **Server code** (PB-01), only when it did something the person will miss, before you remove it: "PageButler doesn't run code on its servers, so the script that emailed you the contact form can't come along."
- **Generator output** (PB-18), once a session, at the hand-over: "Your site is on PageButler as plain HTML, made from your Astro project. From now on I'll make changes in that HTML: building the site with Astro again would undo them."
- **A download visitors used** (PB-13), before you remove its link: "PageButler doesn't host zip files or programs for download, so the press kit can't stay on the site. If you put it on a file-sharing service, I'll link to it from there."
- **A `.well-known/` file a service needed** (PB-13), only when the person set up that service: "PageButler can't serve the file your chat server needs at this address, so I left it out. Your site works as before; only that service won't find its settings here."
- **A blog or a list that renders later** (PB-T901): "The blog page is on the preview, but it shows a sample post until PageButler starts building blogs in a later release."
- **An edit the new code can't show** (PB-T111): name the text and why, in the person's words, and ask: "Your edit to the About page's intro doesn't show on the preview: the new layout puts the intro inside a link, which can't hold the link in your text. Should I take that link out, or move the intro back?" For a live value, say it before any publish: publishing this version would show the text from the site's code in its place.
- **Anything else** a check or deploy reports: fix it without a word.

## Refusals and failures
- A refusal or failure that changed nothing: say so, and what you'll do next, or what you need from the person.
- After a sign-in or permission refusal: say no preview was made and nothing went live, and what the person can do.
- When a refusal leaves a choice, such as another slug, ask the person rather than choosing for them.
- At a limit: `references/limits.md`.
