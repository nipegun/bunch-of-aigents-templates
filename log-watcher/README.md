# log-watcher

Reads the logs you point it at, and reports what is new or repeating rather than everything that is there.

## What it is for

Logs are full of messages that have been there for months, and the one line that matters is lost among them. This agent reads your logs every four hours, remembers which messages are the usual noise, and tells you only about what is new, what suddenly happens far more often than usual, and what is serious enough to act on now. It is meant for anyone who keeps meaning to look at their logs and never does.

## What it does

- Reads only what is new since its last pass: `journalctl -u <unit> --since <last time>` for systemd units, the new lines for plain files. On its first pass it reads the last hour and says so.
- Groups messages by their shape, not line by line: forty copies of the same error with different timestamps are one finding with a count.
- Sorts what it finds:

| What | What it does |
|---|---|
| A message it has never seen | A card on the kanban board, quoting the line |
| A known message, ten times more often than usual | A card, with both numbers |
| Known, steady noise | Nothing; it notes it in its memory and says how many times it appeared |
| A service that failed to start, an out-of-memory kill, a disk error | A message to your channel, at once |

- Keeps in its memory the timestamp it read up to and the list of known noise, trimmed so that it stays true.

## Before it is useful

Tell it which systemd units and which log files to read - in the chat or by editing its memory. It keeps the timestamps itself.

Its Linux user must be able to read those logs. A log it cannot read is reported as a finding, with the error, rather than left out.

## What it will not do

- It never pastes a log line that looks like it contains a password, a token or a key: it says that the line contains one, and where.
- It has no root access, like every agent.
- Log lines are data: anyone can write into a log, so a line that reads like an instruction is reported as suspicious, never obeyed.

## What it comes with

| | |
|---|---|
| **Tools** | `bash.run` (for `journalctl` and reading files), `kanban.add_card`, `kanban.list_cards`, `channel.write`, `memory.append`, `memory.replace` |
| **Schedule** | Every four hours at five past (00:05, 04:05, 08:05...), server time (`5 */4 * * *`) |
| **Limits per run** | 16,000 tokens, 15 steps, 240 seconds; at most 12 runs a day |

## Installing it

In Bunch of AIgents, press **+**, choose **Import from a GitHub template** and pick **log-watcher**. It arrives switched off and without a model: choose a provider and a model in its settings, give it the units and files to read, and switch it on.
