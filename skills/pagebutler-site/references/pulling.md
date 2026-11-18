# Bringing the live site's content into the files with pb pull

People change a site's words and images on Pagebutler. Once they publish, the live site shows them, but the site's files still hold the old text. `pb pull` writes the live values into the files, each in its place, and nothing else in a file changes. It reads only what is live, never edits that aren't published, and changes nothing on the preview or the live site.

## When to run it
- Run `pb status` first in a site's folder, at the start of each conversation. Run `pb pull` before you edit when it says to: when it lists values changed on the live site since the last pull, or says no pull is recorded here. Also run it when the person says someone changed the site on Pagebutler since.
- When `pb deploy` says values changed on the live site since the last pull, nothing is lost: the deploy keeps them. Run `pb pull` before you next edit those files. When it also says it doesn't send what that pull recorded, as with `--archive`, wording a person removed since can come back on the preview: run `pb pull`, then `pb deploy` again.
- `pb` never pulls by itself, and nothing is lost without it: a deploy keeps every value people typed. A pull makes the files match the live site, so the next merge report has less to say.
- Run it before you edit, not after: a value your files changed is never overwritten, so a pull after your edit leaves it for you to ask about.
- `pb pull --dry` says what it would write and leave, writing nothing.

## What pb status says
`pb status` reads and changes nothing. Its sections, in order:
- What is live, or that nothing is live yet.
- The latest deploy: a preview or live, and what holds it back. When it is held, do what its lines say before anything else: nothing can go live until then.
- Another deploy made after the version this folder came from, with who and when. The next `pb deploy` from here replaces it on staging, with its changes only if this folder has them. Tell the person before you deploy, unless it was theirs and `pb status` says it may have been from this folder.
- Values changed on the live site since the last pull here: each with who changed it, or set it back to the code's text after a rollback or an undo, and when. People published these, and your files don't hold them yet: pull before editing those files. An edit that isn't live is never listed here.
- Edits still waiting to be published, by each person. Leave them alone: a deploy keeps them, and only a publish naming them takes them live, when the person asks.
- `Next:` names the one step to take first.
"No `pb pull` is recorded" stays after a pull that found nothing to pull, which records nothing: once `pb pull` says there is nothing to pull, carry on.

## With the tools, without pb
For files you hold where `pb` doesn't run, such as from an earlier `deploy_plan` or archive:
- `site_status` answers what `pb status` prints. Send `pulled_rev` and `pulled_since` as the `live_rev` and `since.version_id` of your last `content_pull`, and `base_version` as the version your files came from.
- `content_pull` lists the live values that differ from your files' version (`since`, the live version unless you name another). Send `next_cursor` as `cursor` until it is null, even after a page with no values, and read every page before you write. Refused with `live_content_changed`: start again from the first page.
- Write each `value` into every file its `files` lists, or into none, and only where they still hold `base.value`; where they hold other words, both changed it: ask the person which to keep. A `text` or `rich` value is Tiptap's JSON: write the HTML it describes, `bold` as `<strong>` and `italic` as `<em>`. A `figure` is `<figure>` with `attrs.class`, then `attrs.image` as the `<img>`, inside `attrs.link`'s `<a>` if any, then its content as a `<figcaption>` with `attrs.captionClass` unless empty. Leave `generated` and `machine_translated` values out, and write a partial's value only when each of its languages shows the same.
- Keep the answer's `live_rev` and `since.version_id` for the next `site_status` or `deploy_plan`.
- On `deploy_commit`, send `pulled_rev` and `pulled_since` as for `site_status`, and `pulled_fields` as `{<locale>: {<path>: rev}}`, each value's `rev`, for only the values you wrote into the files: the merge then reads your change to that wording as yours.
- A value marked `value_left_out` was too long for one answer, and the read goes on past it. With `value` and no `base.value`, nothing shows whether your files changed it: where they hold other words than `value`, keep them and ask the person which to keep. With no `value`, write nothing for it and never count it as pulled: run `pb pull` where it runs, or tell the person that wording can't be brought into the files from here, and that the live site still shows it.

## Reading its result
- `Pulled ...` names the version, who edited on the live site, and each field written, with its files. A `site.*` value goes into every file that holds it, or none.
- `Put back ...`: after a rollback, or an edit undone on Pagebutler, the live site shows the code's text again, and `pb pull` writes it back where it had pulled the edit.
- `Left ...` lines say what it didn't write, and why; each has a hint. Under `--json`, `left` holds each with its `reason`.
  - Your files and the live site both changed a value: ask the person which to keep. Never take either by yourself. `pb pull --show-values` prints both.
  - No place in your files: the folder may not hold the version the live site serves. Check before you deploy.
  - A partial whose languages show different live text: keep it as it is; each language keeps its own on Pagebutler.
  - A file left as it is (changed while it ran, a link, another user's file, or a write refused): follow the hint, then run `pb pull` again. What it wrote counts as done.
  - A `site.*` value written into some of its files only (`partly_written`, or `pb pull` stopped between two files): `pb check` and `pb pull` refuse the site until those files agree. Set those files back to the text `pb pull` replaced, or edit them to one text, then run `pb pull` again.
- An item added to a list repeats the first item's ids: say so if a link or script uses them.
- Exit 0: it pulled, even with values left. Exit 1: a file was left, the code's text to put back didn't match its checksum, or it couldn't run. Exit 75: the live site kept changing while it read, and nothing was written: run it again in a minute.
- `.pb/` holds what `pb pull` pulled and the version `pb deploy` last deployed from this folder. It keeps itself out of git: never commit it or edit it. Deleting it loses those records: `pb status` can't list what changed since the pull, and a deploy merges as if you hadn't pulled. It then keeps a person's wording wherever your files changed it, and wording a person removed since can come back from your files.

## Deploying what you pulled
- `pb deploy` sends what the last pull recorded, so the merge knows which wording came from the live site. Where your files changed that wording, the preview shows yours, and a `note:` line says whose it replaced. Where a person changed it on Pagebutler after the pull, theirs is kept, as when both sides change a field.
- Tell the person whose wording the preview replaced. Only if they ask for theirs back, run `pb resolve <field> --take theirs`, then `pb deploy --continue`.
- When the report's first line says the merge kept the live code's values where your files still hold wording you pulled, a person removed that wording after your pull: run `pb pull`, not `git pull`, before you edit those files again. It puts the code's text back.

## What the person hears
- From `pb status`, say only what concerns them: a preview that can't go live yet, someone else's newer deploy, and what you pulled. Mention edits waiting to be published only when they talk about publishing, by name: "Ana has 3 edits waiting."
- Say whose wording came in, by name, and that the site didn't change: "I've brought the live site's wording into the files: Sam's new site name and Ana's About page text. Nothing on the preview or the live site changes, since both already show it."
- No names to give: "The files now have the wording the live site shows."
- A value a rollback brought back (`rolled_back` under `--json`), or one put back: "The files have the footer text the live site shows again since Sam's rollback."
- Wording you pulled and changed: "The preview has the new About intro in place of Sam's wording. The live site keeps Sam's until it is published."
- Nothing to pull: say nothing, unless they asked.
- Both changed: "Sam changed the About page's intro on the live site to "We make calm software", and the files have other words there: "Small tools, made well". Which should the site keep?" Then do as they say, and deploy only once they agree.
- A value left out of `content_pull`'s answer: "Ana's About page article is too long to bring into the files from this chat. The live site still shows it, and the next preview will too." For one without its `base.value`, ask as when both changed, naming the part rather than quoting it: "Ana changed the About page's article on the live site, and the files have another version of it. Which should the site keep?"
- Never say the edits were published, deployed or moved: they were live already.
