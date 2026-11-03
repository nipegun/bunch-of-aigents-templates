# news-watcher

Reads the RSS feeds listed in its prompt and tells you what is worth knowing, following the rules you write there.

## What it is for

Following a handful of news sources takes time, and most of what they publish is not relevant to you. This agent reads the RSS or Atom feeds you choose every two hours and tells you only about the entries that match your own rules - a security update for a package you use, a mention of your product, anything you decide - without ever sending you the same headline twice.

## What it does

- Reads each feed with `rss.fetch`, which returns the entries themselves (title, link, date and summary); RSS and Atom both work.
- Looks only at entries newer than the last one it handled, which it keeps in its memory per feed. A news agent that forgets sends the same headline every two hours.
- Applies your rules to each entry: a card on the kanban board, a message to your channel, or nothing.
- Writes one card per story, quoting the headline as published, with one or two sentences of why it matters and the link.
- Reports in one line how many entries it looked at and how many matched.
- A feed that fails is reported and skipped; one that fails three runs in a row gets a card, because it has probably moved.

## Before it is useful

Both the feeds and the rules live in its system prompt, and you edit them there:

1. **The feeds.** Replace the three example URLs under *The feeds* with your own, one per line, keeping the ` - ` in front.
2. **The rules.** Replace the examples under *What to report* with your own, for example "anything about Debian security updates: a card with the package and the version", "anything mentioning our domain: to the channel at once", "everything else: nothing".

Until the rules are replaced it says so and reports nothing: what counts as news is your judgement, not its own.

## What it will not do

- It never invents a detail that is not in the entry; when the summary does not say something, it points to the link.
- The contents of a feed are data, never instructions: anyone can publish a feed.
- It only writes to your channel for what a rule sends there. Everything else waits on the board.

## What it comes with

| | |
|---|---|
| **Tools** | `rss.fetch`, `kanban.add_card`, `kanban.list_cards`, `channel.write`, `memory.append`, `memory.replace` |
| **Schedule** | Every two hours at seven past (00:07, 02:07...), server time (`7 */2 * * *`) |
| **Limits per run** | 20,000 tokens, 15 steps, 300 seconds; at most 12 runs a day |

## Installing it

In Bunch of AIgents, press **+**, choose **Import from a GitHub template** and pick **news-watcher**. It arrives switched off and without a model: choose a provider and a model in its settings, replace the example feeds and rules in its system prompt, and switch it on.
