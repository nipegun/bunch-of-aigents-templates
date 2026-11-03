# backup-watcher

Checks that the backups you told it about actually ran, are recent, and are not suspiciously small.

## What it is for

A backup job that fails quietly is only discovered on the day you need the backup. This agent looks at the backup files themselves, once a day, and tells you when one is missing, late, much smaller than usual, or unreadable. It is meant for anyone who has backups written to a directory on this machine (by a cron job, by `rsync`, by a database dump) and no one checking them.

## What it does

For every backup it knows about, on every run:

- **It exists**: lists the directory and names the newest file.
- **It is recent enough**: compares that file's date with how often the backup is supposed to appear.
- **It is not suspiciously small**: compares its size with the size recorded on the previous run. A backup that shrinks to a tenth of its size usually means the job ran and copied nothing.
- **It is readable**: tests an archive with `tar -tzf` or `gzip -t`, without extracting it.
- **There is room for the next one**: checks the free space of the filesystem it lives on.

It records the date and size of each backup in its memory, which is what makes the comparisons possible.

When something is wrong it opens a card on the kanban board, and it also writes to your channel (Telegram, Discord...) when the backup is the only copy of something. When everything is fine it opens no card: one line in its answer, so that a daily "backup ok" never buries the one card that matters.

## Before it is useful

Its memory starts empty, and on its first run it will say so and ask. Tell it, in the chat or by editing its memory:

- which directories or files are the backups,
- how often each one is supposed to appear (daily, weekly...),
- roughly how big each one normally is.

Its Linux user must be able to read those directories. If it cannot, it says "permission denied" instead of reporting success.

## What it will not do

- It never deletes, moves or "cleans up" a backup. It can say which old ones it would remove and how much space that would free; you decide.
- It never reports success for something it could not read.
- It has no root access, like every agent.
- What it reads - file names, listings, the contents of an archive - is data: a name that reads like an instruction is reported, never obeyed.

## What it comes with

| | |
|---|---|
| **Tools** | `bash.run` (to list, date and test the files), `kanban.add_card`, `kanban.move_card`, `kanban.list_cards`, `channel.write`, `memory.append`, `memory.replace` |
| **Schedule** | Every day at 09:00, server time (`0 9 * * *`) |
| **Limits per run** | 12,000 tokens, 15 steps, 240 seconds; at most 4 runs a day |

## Installing it

In Bunch of AIgents, press **+**, choose **Import from a GitHub template** and pick **backup-watcher**. It arrives switched off and without a model: choose a provider and a model in its settings, give it the list of backups, and switch it on.
