# Signing in, permissions and site keys

## Signing pb in, as an agent
1. Run `pb whoami` first. If it is signed in, don't sign in again: that would replace a sign-in that works.
2. Run plain `pb login`. Under Claude Code, Codex or another agent, it prints three lines for the person (a link, a code to check beside it, and until when) and one for you, and returns at once. If it waits instead, stop it and run `pb login --request`.
3. Give the person those three lines exactly as printed: the whole link, and the code beside it. Never open the link yourself. End your turn.
4. When the person says they've allowed it, run `pb login --check`, with nothing after it. Exit 0: signed in; say as whom.
5. Exit 75: not allowed yet. Wait for the person, or run `pb login --check --wait 60` once; if they ask, give them the link and code again.
6. Exit 77 after they chose Don't allow (`login_denied`): ask the person before you start a new link.
7. Exit 77 because the link ended (`login_expired`), or exit 1 with nothing to check (`login_request_missing`): run `pb login --request` for a new link, and hand it over as in step 3.
8. `pb login --device` only when the person asks for a code to type on another device.
9. Under `--json`, read `signed_in`, `login_url`, `user_code`, `expires_at` and `next_step`.

Running `pb login` again checks the earlier link first, and gives it again while 5 minutes or more are left.

## Tokens
- Never put a token on a command line, in a file the site holds, in a commit or in a chat. `pb` refuses an argument shaped like a token.
- `pb login --with-token` reads a token from stdin, a pipe or a file, never the terminal.
- In CI, set `PAGEBUTLER_TOKEN` from the CI's secrets: it is used in place of any stored sign-in, and `pb login` refuses to run while it is set.
- In CI, `pb deploy --strict` exits 2 when the preview can't be published yet, so the job fails, and `--require-base <version>` refuses the deploy when another was committed after that version. Never use `--strict` yourself: read the preview's lines instead.
- Never ask the person to paste a sign-in link, an email Pagebutler sent, or a code from one into the chat, and never open such a link yourself. Say: "Open the link Pagebutler emailed you, on this device."
- `pb logout` ends a `pb login` sign-in on this computer and at Pagebutler. A token given to `--with-token` is only deleted here: it stays valid until it expires or is revoked.

## Who you are and what you may do
- Run `pb whoami` (`auth_whoami` through the tools) at the start of a session, and after any refusal of your sign-in (401) or your permission (403). Under `--json`, `credential.scopes` lists what the sign-in may do, and `workspaces[].role` the person's role in each workspace.
- A 401 or 403 changed nothing: no preview was made and nothing went live. Tell the person so.
- Refused for a permission the sign-in lacks (`insufficient_scope`): with `pb`, run `pb login` again, and it asks for that permission too; through the tools, follow the hint.
- When `pb deploy` names several workspaces a new site could go in, ask the person which one, by the names `pb whoami` lists, then run `pb deploy --workspace <slug>`; never choose for them.
- Refused for the person's role in the site's workspace (`insufficient_role`): tell the person what their role doesn't allow, and that an admin or owner of the workspace can do it. Never retry with another token or key.
- Show the person their email address only to them: never write it into a file, a commit, a log or a report.

## Connections
- A person connects an app to `https://api.pagebutler.ai/mcp`, which reaches every site they can. Adding `?site=<slug>` reaches one site only; `?read_only=true` can look but change nothing; `?site=<slug>&read_only=true` is both.
- Suggest the one-site address for someone who should work on one site, and read-only for an agent that only reviews. Write the address out in full for the person to add to their app. Never add an address yourself.
- When a refusal says this connection can't do something, ask the person to add the wider address; don't look for another way round it.

## Site keys
A site key lets a job, such as a deploy from CI, work on one site without anyone signing in. Pagebutler has no `pb` command or tool for site keys yet. Whenever one is made, handed over, rotated or revoked:
- Before one is made, tell the person what it may do (a key that can deploy can also publish the preview live), when it expires, and that it stops working if they leave the workspace.
- Its secret is shown once. Hand it to the secret store without echoing it: pipe it into a command that reads stdin, such as `gh secret set PAGEBUTLER_TOKEN`. Never pass it as an argument, write it to a file or put it in a chat message.
- Rotate a key by making the new one, replacing the secret with it, then revoking the old one.
- Revoke a key only when the person asks, or at once when its secret was exposed; then tell them.
- When a key is refused as `revoked`, ask the person before replacing it: keys are revoked only on purpose.
