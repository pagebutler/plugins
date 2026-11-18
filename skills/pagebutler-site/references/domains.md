# A site's own domain

A site always answers at `https://<slug>.butler.page`. In v0 Pagebutler approves each domain itself; no command or tool approves one. With a shell, run the commands below in the site's folder, or give `--site <slug>`; with tools, give `site`.

## The order
1. Agree the exact host with the person. `www.example.com` and `example.com` are added on their own; the bare domain needs CNAME flattening or an ALIAS record at its DNS host.
2. Run `pb domains add <domain>`, or call `domain_add` with `domain` the host alone, no `https://` or path. Tell the person: "I've asked Pagebutler for {domain}. It approves each custom domain itself first; once it has, I'll give you the DNS records to set. Your live site stays at https://{site}.butler.page meanwhile." Then end your turn. Never wait in a loop: approval can take hours.
3. When the person comes back, run `pb domains status <domain>`, or call `domain_get`. Follow its `next_step`, and use its latest answer before saying anything about the domain. `pb domains list` and `domains_list` show the site's domains, its address first.
4. At `pending_dns`, say: "Pagebutler approved {domain}. Set these records at your DNS host, and I'll tell you when it's live:" Give the records as below. The TXT records change nothing for visitors and can be set now. If the domain shows another site today, lower its traffic record's TTL to 5 minutes and wait for `verified` before changing its CNAME. Otherwise its CNAME can be set now.
5. At `verified`, say: "{domain} has its certificate. Once it points at cdn.butler.page it shows your live site; I can check https://{domain} for you." Verified means Pagebutler holds the certificate, not that DNS points here. Say it is live only once its CNAME points at `cdn.butler.page`; check `https://<domain>` if you can.
6. Once the domain is live, publish again when the person asks, with `pb publish` or `site_publish`, so the live pages' sitemap and canonical links name the address. Publishing rules: `publishing.md`.

## Giving the records
Ask where the domain's DNS is managed: usually where it was bought. Give one numbered line per record, in the answer's order: ownership TXT, certificate TXT, then the traffic CNAME. Name the type and copy the name and value exactly, each in a code span. Never shorten or guess a value. Give the latest TXT value: it can change while it waits.

The answer writes names in full. Some DNS hosts append the domain themselves: there, type only the part before it, such as `_acme-challenge.www` for `_acme-challenge.www.example.com`. For a bare domain, many DNS hosts write its own name as `@`. Don't guess the DNS zone from the last two labels.

## Each status
- `pending_approval`: nothing is served on the domain yet; end your turn until the person returns.
- `pending_dns`: follow the records and next step; while there are no records, Pagebutler is preparing them.
- `verified`: the certificate is ready. Never call the domain live before it is verified and pointing here.
- `refused` or `withdrawn`: say "Pagebutler didn't approve {domain}" or "withdrew its approval of {domain}", then ", so it won't serve your site. Your live site stays at https://{site}.butler.page; hello@pagebutler.ai can tell you why."
- `needs_attention` or `expired`: relay `summary` and `next_step` in your own words.

`pb domains status` exits 0 when verified, 75 while approval or DNS waits (also for a status it doesn't know), and 1 when the domain cannot serve. `--json` keeps that exit and prints the API answer.

## The address and removing
The site's `url` names its current address: its chosen verified domain, otherwise `www` within a verified pair, otherwise its earliest verified domain, otherwise its `butler.page` address. The other verified domains send visitors there with a 301; `butler.page` keeps serving.

Only on the person's ask, choose a domain that reaches this site with `pb domains address <domain>` or `domain_update` with `address: true`. A domain still pointing elsewhere would send the other domains' visitors there. Before verification the choice waits. Once live, publish again when asked; until then the live pages keep their previous address. The address command asks nothing; choosing the previous domain again undoes it. `--dry` (tools: `dry_run`) checks without changing anything.

Remove only when the person asks, with `pb domains remove <domain>` (type the domain again at its prompt), or give `--confirm <domain>`. For `domain_remove`, set `confirm` to the domain exactly as `domain_get` names it. `--json` never prompts. `--dry` checks without removing. Pagebutler stops serving the removed domain within about a minute; then its DNS records can be deleted. If it was the address, the next takes over after removal completes: publish again when asked so the live pages name it. Adding it again waits for approval again.
