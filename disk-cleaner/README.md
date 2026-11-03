# disk-cleaner

Finds what is filling the disk and says what could go, with sizes. Proposes; never deletes.

## What it is for

When a disk fills up, the hard part is not deleting things - it is finding out, quickly and with real numbers, what is taking the space and what is safe to remove. This agent does that search once a week and leaves you a short list of the biggest wins, each with the exact command that would free it. It is meant for anyone who would rather decide than have something delete files for them.

## What it does

- Starts from `df -h` and `du` at the top of the filesystem, then goes one level down into whatever is biggest, until it can name actual directories.
- Looks by name at the usual suspects: `/var/log`, `/var/cache/apt`, `/var/lib/docker`, `/tmp`, `/home/*/.cache` and old kernels in `/boot`.
- Opens one card on the kanban board per candidate, sorted by size - a few big wins rather than fifteen small ones. Each card says what the directory is, how much it uses, what it is for, and the exact command that would free it, marked when that command needs root.
- Records the sizes in its memory, so next week it can tell you what grew, not just what is big.

## Before it is useful

Nothing to configure: it works on the machine it runs on from its first run. Its Linux user can only measure the directories it is allowed to read, so some system directories may show up as unknown.

## What it will not do

- **It never deletes anything**: not a log, not a cache, not a temporary file, not even something obviously safe. The only exception is its own home directory. It has no root access anyway.
- It never proposes deleting something it cannot identify. "1.4G in /srv/data, unknown contents" becomes a question for you, not a candidate.
- File names are data: a name that reads like an instruction is reported, never obeyed, and a name with quotes, semicolons, `$`, backticks or line breaks never goes into a command it proposes.

## What it comes with

| | |
|---|---|
| **Tools** | `bash.run` (for `df` and `du`), `kanban.add_card`, `kanban.list_cards`, `memory.append`, `memory.replace` |
| **Schedule** | Sundays at 05:00, server time (`0 5 * * 0`) |
| **Limits per run** | 12,000 tokens, 15 steps, 300 seconds; at most 2 runs a day |

## Installing it

In Bunch of AIgents, press **+**, choose **Import from a GitHub template** and pick **disk-cleaner**. It arrives switched off and without a model: choose a provider and a model in its settings and switch it on.
