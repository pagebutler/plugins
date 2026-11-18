# Pagebutler for Claude Code

Build plain HTML websites with Claude Code and host them on butler.page. Every change is a preview first, and goes live only when you ask.

Pagebutler is invite-only for now. To ask for an account, write to [hello@pagebutler.ai](mailto:hello@pagebutler.ai).

## Install

You need a Pagebutler account and the `pb` command, which step 3 installs.

1. In Claude Code, add the marketplace:

   ```text
   /plugin marketplace add pagebutler/plugins
   ```

2. Install the plugin, and choose "Install for you":

   ```text
   /plugin install pagebutler@pagebutler
   ```

3. In a terminal, install `pb`:

   ```sh
   npm install -g pagebutler
   ```

4. Restart Claude Code. Type `/mcp`, choose `plugin:pagebutler:pagebutler`, then choose "Authenticate" and sign in.

To try it, ask Claude to make a one-page site for Acme and show you a preview.

<details>
<summary>What the plugin adds</summary>

- The `pagebutler-site` skill, which tells Claude how to build, check, preview and publish a site.
- Pagebutler's tools, through the server at `https://api.pagebutler.ai/mcp`.

`pb` isn't part of the plugin. It needs Node.js 22.22.2 or a later 22, 24.15.0 or a later 24, or 26 and later.

</details>

<details>
<summary>Didn't work?</summary>

- **No sign-in email:** your address may not be invited yet. Write to [hello@pagebutler.ai](mailto:hello@pagebutler.ai).
- **npm says `pb` already exists:** another program uses that name. Remove it, then run step 3 again.
- **`/mcp` doesn't list Pagebutler:** type `/plugin` and open the Errors tab.

</details>

## Update

Claude Code doesn't update this plugin by itself. In a terminal:

```sh
claude plugin marketplace update pagebutler
claude plugin update pagebutler@pagebutler
npm install -g pagebutler
```

Then restart Claude Code.

## Remove

In a terminal:

```sh
claude plugin marketplace remove pagebutler
npm uninstall -g pagebutler
```

Your sites and your account stay as they are. To disconnect Claude Code from your account, go to [Apps and keys](https://auth.pagebutler.ai/connections).

## Report a problem

[Open an issue](https://github.com/pagebutler/plugins/issues). Issues are public, so leave out anything private.

For a security problem, follow [SECURITY.md](SECURITY.md) instead.

## Contributing

This repository is generated from Pagebutler's source with each release, so pull requests aren't accepted here. To suggest a change, open an issue.

## Licence

Copyright Luminous Design LTD. Licensed under [Apache-2.0](LICENSE).
