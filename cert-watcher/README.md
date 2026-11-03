# cert-watcher

Checks when the TLS certificates of the sites you list expire, and warns while there is still time to renew.

## What it is for

An expired certificate takes a site down as surely as a crashed server, and it usually happens because an automatic renewal stopped working weeks earlier without anyone noticing. This agent checks the expiry date of every certificate you care about each morning and speaks up at 30 days, again at 7, and immediately if one has already expired. It is meant for anyone running HTTPS sites, on this machine or anywhere else.

## What it does

- For each host in its list, it connects on port 443 with `openssl s_client` and reads the certificate's expiry date, subject and issuer.
- If this machine has Let's Encrypt certificates in `/etc/letsencrypt/live/`, it checks those too.
- It keeps the host list and the last known expiry dates in its memory, so it can tell you "this one renewed itself last month and has not this time" - the sentence that catches a broken renewal job.

When it speaks:

| Days left | What it does |
|---|---|
| More than 30 | Nothing. Silence is the correct result. |
| 30 or fewer | One card per host on the kanban board, with the date. It checks the board first, so a certificate never gets two cards. |
| 7 or fewer | The card, and a message to your channel. |
| Already expired | A message to your channel at once, saying which service is affected. |

When a certificate has been renewed, it moves its card to done with the new expiry date.

## Before it is useful

Its memory starts empty. On its first run it asks which hosts to watch: give it the host names (for example `example.com`, `mail.example.com`), in the chat or by editing its memory. It does not guess.

## What it will not do

- It has no root, so it never runs `certbot renew` and never reads a private key. When a renewal is needed, it writes the exact command you would run, and on which machine.
- A certificate it could not reach is not a certificate that is fine: it reports the connection error and says that it does not know the expiry date.
- What a certificate says is data: a subject or issuer that reads like an instruction is quoted, never obeyed.

## What it comes with

| | |
|---|---|
| **Tools** | `bash.run` (for `openssl`), `kanban.add_card`, `kanban.move_card`, `kanban.list_cards`, `channel.write`, `memory.append`, `memory.replace` |
| **Schedule** | Every day at 06:30, server time (`30 6 * * *`) |
| **Limits per run** | 10,000 tokens, 12 steps, 180 seconds; at most 4 runs a day |

## Installing it

In Bunch of AIgents, press **+**, choose **Import from a GitHub template** and pick **cert-watcher**. It arrives switched off and without a model: choose a provider and a model in its settings, give it the list of hosts, and switch it on.
