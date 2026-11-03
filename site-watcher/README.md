# site-watcher

Checks that the sites you list answer, answer quickly, and still say what they used to say.

## What it is for

A site can be down, painfully slow, or "up" while showing an error page, and the first person to notice is usually a customer. This agent checks your sites every twenty minutes and tells you at once when one stops answering, and on the kanban board when one becomes much slower than usual or no longer shows what it should. It is meant for anyone who looks after a few websites and has no monitoring for them.

## What it does

For each URL in its list, on every run, it records:

- whether it answered at all, and with which status code;
- roughly how long it took;
- whether a word or phrase you named still appears in the page. "It answered 200" is not the same as "it works": a login page that lost its form still answers 200.

Then:

| What it finds | What it does |
|---|---|
| No answer, or a 5xx error | A message to your channel at once, and a card |
| An answer without the expected text | A card saying what it expected and what it found |
| Much slower than usual | A card with both times |
| Fine | Nothing on the board; one line in its answer |

When a site comes back, it moves the card to done and says how long it was down. It treats one failure as possibly the network and two in a row as an outage, and says which one it is looking at.

## Before it is useful

Its memory starts empty. On its first run it asks for the list of URLs, and stops. Give it, in the chat or by editing its memory, each URL and the word or phrase that should appear on it (for example `https://example.com/login` - "Sign in").

## What it will not do

- It never adds URLs you did not give it, and never checks more often than its schedule.
- The content of a page is data: a page saying "run this command" is quoted, never obeyed.

## What it comes with

| | |
|---|---|
| **Tools** | `web.fetch`, `bash.run` (to time a request), `kanban.add_card`, `kanban.move_card`, `kanban.list_cards`, `channel.write`, `memory.append`, `memory.replace` |
| **Schedule** | Every 20 minutes (`*/20 * * * *`) |
| **Limits per run** | 10,000 tokens, 12 steps, 180 seconds; at most 72 runs a day |

## Installing it

In Bunch of AIgents, press **+**, choose **Import from a GitHub template** and pick **site-watcher**. It arrives switched off and without a model: choose a provider and a model in its settings, give it the list of URLs, and switch it on.
