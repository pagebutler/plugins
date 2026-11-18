# After a deploy: the merge report

Every deploy merges the new code with what people typed on Pagebutler, and its merge report says what that did to the preview: the lines `pb deploy` prints from `merge report:` on, or `merge_report` in `deploy_status`'s answer. Its hint, the first line, counts the whole report and says what to do. Tell the person what it says in their words, without field paths unless they ask, and say it is about the preview.

## Reading `pb deploy`
`pb deploy` waits for its preview to land: seconds for a small site, minutes for one of thousands of files. If it outlasts your command's time limit, wait for it to finish and read its output; never start a second `pb deploy` of the same files meanwhile. If it stopped, run it again: it uploads nothing, and waits for the same landing.

`pb deploy` prints the report's lines after any findings and before the links, each starting with a label:
- `note:` what the merge did, such as whose wording it kept: tell the person. An `override:` line under it takes the new code's wording for all of that field, even where both sides' edits were merged: run it only when the person asked to change that field, and say so.
- `held:` values a person typed that the new code has no place for, which keep the preview from being published. Do only what its line allows with the `command:` lines under it: when it isn't about a value you moved, tell the person and stop.
- `draft:` unpublished edits on fields the new code changed, moved or removed, which hold nothing up: see "Edits not live yet" below.
- `candidates:` fields like a held value or an unpublished edit, a likeness only. Map a held value onto one only if you moved it there; an edit can't be mapped.
- `refused:` a resolution the landing left out, and why.

Never add `--strategy`: the default keeps every value a person typed. `ours` takes the new code's wording on every field both sides changed except those you pulled, and `ask` is for a person at a terminal: a developer choosing field by field can run `pb deploy --continue --strategy=ask` there. The values' own words print only with `--show-values`.

Exit 0 means a preview, whether or not it can be published yet: read the sentence after the links. When it can't be, say so and why, in the person's words: "The preview is ready, but it can't go live yet: the availability line you edited has no place in the new page." Never add `--strict`: it is for CI, where such a preview should fail the job, and a failed command's output reaches you cut.

Prefer these lines to `--json`. When you need a key they don't show, write the document to a file, `pb deploy --json > deploy.json`, and read only the keys you need, such as `status`, `halts` or `merge_report.held`: a large report is longer than what you are shown of a command.

When `pb deploy` warns that another deploy was committed after the version these files came from, tell the person who made it and when, that this preview replaced it on staging, and that its changes are in this preview only if this folder had them: "Ana deployed to staging after the version I started from. This preview replaces hers there, and has her changes only if my files did; hers is still at its own link." Add `--require-base <version>` only when the person asks that a deploy never replace someone else's.

When a `note:` says it kept the live code's value because your files hold an older one, they may be behind the live site: bring them up to date (`git pull` if the site is in git, or `pb pull` if the hint says so) and deploy again. Its `override:` line brings the older value back: run it only if the person wants that.

When you can say what a deploy changes, add `--message "<one sentence>"`: it is kept with the version.

## Placing held values
Each `command:` line under a `held:` line is a `pb resolve` command. It records one decision, and nothing changes on the preview or the live site until `pb deploy --continue` lands the version again.
- Run a `command:` line only when its `held:` line's condition is true of your own change. For a page, folder or list you moved, that is the one `--map` it prints, never a resolution for each field, with any exact map or drop printed beside it. Run each as printed, single quotes and all.
- `--drop-orphans` removes the person's value: run it only when they asked to remove that value, and never without a path.
- An `override:` line, `--take ours`, replaces the person's wording with the new code's: run it only for a field the person named. Never add `--scope all` unless they asked for the new code's wording everywhere.
- Then run `pb deploy --continue` once, and read what it prints: `note:` lines for what moved, `held:` lines still to place, and `refused:` lines for resolutions it left out, which are no longer recorded. Run again only what a line still allows; one refused because the field was published again waits for the person.
- Without `pb`, send the `resolution` of each command these rules allow in one `deploy_resolve` call, with the report's `version` as `id` and its `theirs_rev`; then call `deploy_continue` once with that `theirs_rev`, and read its new report in `deploy_status`. Where `mcp` is null, as on a read-only connection, give the developer the `pb` commands.
- If `pb resolve` says the live content changed, run `pb deploy --continue` first, then the lines of the report it prints.
- To give a field words the person chose, neither side's, run `pb resolve '<field>' --take value --locale <key> --value-file <file>`, the file holding the value as `page_update_fields` takes it (text in double quotes), only for a field the person named.
- A change to the files after a held deploy makes a new version, with no resolutions. To send it the decisions you made from the earlier report, write them to a file, `{"theirs_rev": <that report's>, "resolutions": [<each one's resolution, as the JSON gives it>]}`, and run `pb deploy --resolutions <file>`. Take `theirs_rev` from that report, `merge_report.theirs_rev` in `pb deploy --json`, never from elsewhere.
- When no line allows a value, and always in an unattended run, leave it held: tell the person the preview can't go live yet, which of their edits have no place, and where the report thinks each might go (its `candidates:`), in their words. Then stop: never retry blindly.
- Languages: a value with no language of its own, such as a number or a name, fits any language. After swapping two pages' languages, map each page's values to the page now in their language, with a plain exact map. Use maps of every page into a new language, which a report may offer, only when every held value's words are in that language.

## Edits not live yet
A deploy keeps every unpublished edit made on Pagebutler, and moves it with its field where the new code renamed that field. The report lists the edits the new code touched: the `draft:` lines, `draft_conflicts` in the JSON.
- Tell the person after you hand over the preview, saying whose edit it is when it isn't theirs. Never call it a conflict, and never say it holds the deploy or a publish: it doesn't.
- The new code changed the field too: "This update also changes the home page title, which you edited. Your version is kept, and the preview shows it. Do you want to keep yours, or use the developer's?" Someone else's: "This update also changes the home page title, which Ana edited and hasn't published. Her version is kept, and the preview shows it. You may want to check with her."
- Offer both, and do only what the person picks, for that field: `pb fields set '<path>' --take edit`, to keep the person's version, or `--take code`, to use the developer's; without `pb`, that `take` on `page_update_fields`. For several, list them and ask once: the answer covers each one listed. Then say "Your version is kept." or "The preview now shows the developer's title; the live site is unchanged." Keep or replace someone else's edit only when the person names that edit, and say whose it was. If a take is refused because the field changed, tell the person before they pick again.
- `page_get` marks these edits with `code_changed`, with the code's words then and now, while the choice is open. Trust it over the report, which lists each waiting edit as the deploy found it.
- Moved with its field: "Ana's edit to the devices text moved with it to its new place: please check it on the preview."
- The preview can't show it, since the new code removed its field, made it another kind of field, or shows it in another language: "Ana's edit to the awards title is kept, but the new layout has no place for it, so the preview doesn't show it." For one made another kind of field, offer to use the developer's version (`--take code`, or `take` set to `code`) or to write the edit again in the new kind. When a warning (PB-T111) names the same edit, say it once.
- Change or publish such an edit only when the person asks for that edit. Publishing it takes the edit's words live, not the new code's, but one that moved with its field goes live only with the developer's update: "Your edit to the devices text needs the developer's update to go live first. Publishing everything takes both live."
- Before you publish one the new layout has no place for, say: "If I publish your awards title now, it goes live, but then the developer's update can't go live until the new layout has a place for it or you agree to remove it."
