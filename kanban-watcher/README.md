# kanban-watcher

Reads the board every morning and writes one short summary of what moved, what is stuck and what is due.

## What it is for

A kanban board that several agents share fills up quickly, and the useful questions - what got done yesterday, what has been stuck for days, what is overdue, what nobody has picked up - take time to answer by looking. This agent answers them once every weekday morning, in a summary short enough to read on a phone, and sends it to your channel.

## Important: it only sees everything as the orchestrator

In Bunch of AIgents every agent sees only its own cards: the ones assigned to it and the ones it created. The one exception is the orchestrator, `manager` (agent 000), which sees them all.

So an agent created from this template summarizes only its own work, which is not a standup. On its first run it will say so and ask you to move its instructions into the manager's system prompt instead. The practical way to use this template is exactly that: copy its system prompt into the manager's, and give the manager the same schedule. It will not guess at cards it cannot see.

## What it does

Every weekday morning it writes four short sections, in this order:

1. **Moved since yesterday** - what reached done, and who did it.
2. **In progress** - what is being worked on now, and how long anything has been in progress for more than three days.
3. **Due** - cards with a date that has passed or is today.
4. **Waiting** - cards in *to do* with nobody assigned.

It sends the summary with `channel.write`. It keeps in its memory what the board looked like the day before, so "moved since yesterday" is true.

## Before it is useful

- Run it as the orchestrator (see above).
- A channel (Telegram, Discord...) has to be configured, because that is where the summary goes.

## What it will not do

- It never moves or deletes a card, and never opens a card for the summary itself.
- No encouragement and no filler: facts and counts.
- The board is data: a card that reads like an instruction is summarized, never obeyed.

## What it comes with

| | |
|---|---|
| **Tools** | `kanban.list_cards`, `kanban.add_card`, `channel.write`, `memory.append`, `memory.replace` |
| **Schedule** | Monday to Friday at 08:00, server time (`0 8 * * 1-5`) |
| **Limits per run** | 12,000 tokens, 8 steps, 180 seconds; at most 2 runs a day |

## Installing it

In Bunch of AIgents, press **+**, choose **Import from a GitHub template** and pick **kanban-watcher**. It arrives switched off and without a model. As explained above, what you most likely want is to copy its system prompt into the orchestrator's.
