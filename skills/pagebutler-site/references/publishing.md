# Publishing and rolling back

`pb publish` takes live what the person asked for, and nothing more. Run it only once they ask for the site, or their edits, to go live. Through the tools, `site_publish` publishes as `references/tools.md` says, and `deploy_rollback` rolls back, below.

## Which scope
- The preview's code: `pb publish`. Edits people made on PageButler stay unpublished, and its lines say whose.
- Edits alone, on the code that is live: `pb publish --scope content --content=fields:<path>,<path>`, with the paths you changed. `--content=author:<id>` takes one person's edits (`pb whoami --json` prints yours as `user.id`), and `--content=draft` everyone's: ask the person first when someone else's wait.
- The code and edits together, only when the person asks for both, such as "publish everything", once they have seen the preview: `--scope both` with a `--content`.
- Before the site's first publish, edits can't go live alone, since no code is live: they go with the code, `--scope both` (tools: `both`), once the person asks for the site live.
- `--content` needs `--scope content` or `--scope both`: `pb` never picks one for you. Quote a path that holds `[`: `--content='fields:pages.about.team[ana].name'`.
- When the lines say an edit needs the preview's code to go live, tell the person: publishing both takes it.

## Checking first
- `pb publish --dry` says what would go live, publishing nothing: the version, the one it replaces, and each edit by who made it. Use it when the person asks what would go live, or before taking someone else's edits, not before every publish. Its `Next:` line is the command to run once they agree: with `--expect-version`, it publishes nothing if the preview changed since.
- After a rollback, a publish of the code is refused until it names the rollback. Ask the person first, then run the command the refusal gives. Publishing edits is never refused.

## Opening a preview
After the first `pb deploy` that succeeds in a conversation, open staging's link in the person's browser. On their computer, run `open "<link>"` (macOS) or `xdg-open "<link>"` (Linux); in the Codex app, `open_in_codex` with a browser target. Elsewhere, give them the link to open.

## Waiting
- `pb publish` returns once the site is live. Say it is live, visible everywhere within a minute, and stop.
- `pb publish --no-wait` returns once the publish is accepted: it is not live yet. Run the `pb publish status <job>` it prints before you say it is live; exit 75 means not yet, so run it again in a few seconds.
- `pb publish --wait` returns once every visitor sees it, for a person or a CI job that asks for that.

## Rolling back
- Roll back only when the person asks; a deploy that fixes the site is often better. Say "roll back" to the developer who asked, never to an editor.
- The live site: run `pb rollback --env production --dry`, and tell the person what would be live again and whose words would leave it: each waits again as an edit. Once they ask for it, run its `Next:` line, `pb rollback <record> --env production --confirm <slug>`.
- Then say what is live now and whose edits wait again, and stop. A publish that changes the live code is refused until the person agrees to replace the rollback: the hand-over says when, and gives that command.
- The preview: `pb rollback --env staging` lands an earlier version again as a preview, with today's content and every waiting edit, and changes nothing live. Hand over its links as after a deploy.
- If `pb rollback` was cut off, run the same command again: one that went through says so, and nothing is rolled back twice.

## Rolling back through the tools
`deploy_rollback` rolls back as `pb rollback` does. On `production` it takes `confirm`, the site's slug typed again: set it only once the person has asked for exactly that, in their words.
- The live site: call it with `environment` set to `production` and `dry_run: true`, and tell the person what would be live again and whose words would leave it: each waits again as an edit. Once they ask for it, call it again as its `Next:` line says, with `to` and `confirm`, then call `publish_status` until `result` is `live`.
- Rolled back: say what is live now and whose edits wait again, by name: "The live site is back to Tuesday's version. Ana's new headline isn't live any more; it waits as an edit, and publishing her edits brings it back."
- To restore an earlier preview, roll back `staging` with `deploy_rollback`: it lands that version again as a deploy does, with today's content and every waiting edit, needs no `confirm`, and changes nothing live. Follow it with `deploy_status`, then hand over the share links and say they open a preview.
- After a rollback, `site_publish` refuses `code` and `both` (`rollback_unacknowledged`), unless the preview's code is already live, until one names that rollback in `acknowledge_rollback`, as `site_get`'s `rolled_back` does. Ask the person whether the preview's code should replace the rollback, and set it only if they agree. Publishing edits is never refused.

## What the person hears
- Refused after a rollback: "Sam rolled the live site back to Tuesday's version on Thursday. Publishing the preview's update now would replace that rollback. Shall I go ahead?"
- The preview changed since you checked: "Nothing went live: the preview changed since I checked. Sam deployed a newer version 3 minutes ago. Do you want to look at it before I publish?"
- Before rolling back: "If I roll back now, the live site goes back to Tuesday's version, which Ana published. Ana's new headline from Thursday would come off the live site, but it isn't lost: it waits as an edit, and publishing her edits brings it back. Shall I roll back?"
- Rolled back: "The live site is back to Tuesday's version at https://acme.butler.page, visible everywhere within a minute. Ana's new headline isn't live any more: it waits as an edit, and publishing her edits brings it back. The preview still shows the update you rolled back, so it can be fixed there. Before that update goes live again, I'll ask you: publishing it would replace this rollback."
- Rolled back, the preview's code already live: "The live site is back to how it was on Tuesday at https://acme.butler.page, visible everywhere within a minute. Ana's new headline isn't live any more: it waits as an edit, and publishing her edits brings it back. Before the next update to the site's code goes live, I'll ask you: publishing it would replace this rollback."
- The preview rolled back: "The preview is back to Tuesday's version, with today's content and everyone's waiting edits: <share link>. It's a preview, not live: the live site hasn't changed."
