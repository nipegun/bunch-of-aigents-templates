# os-watcher

Watches disk, memory, load and the services of this machine, and reports before a problem becomes an outage.

## What it is for

Most outages on a small server announce themselves hours in advance: a disk filling up, memory running short, a service that stopped. This agent checks the health of the machine Bunch of AIgents runs on every hour, learns what is normal for it, and tells you when something is not - on the kanban board for what can wait, on your channel for what cannot. It is meant for anyone running the application on a machine nobody watches all day.

## What it does

Every hour it checks:

- **Disk** (`df -h`): any filesystem over 80% is reported, over 90% is urgent, with what is left in real units.
- **Memory** (`free -m`): available memory, not "free" memory - Linux uses spare RAM as cache and gives it back. It reports when available memory drops below about 15%.
- **Swap**: sustained use on a machine with free RAM, which usually means something is leaking.
- **Load** (`uptime`, `nproc`): the load average compared with the number of cores.
- **The application's services**: whether `boa-exec`, `boa-agent-api`, `boa-web` and `boa-proxy` are running. If one is down, agents are not running.
- **The installation's footprint** (`du -sh /opt/boa`).

It writes down in its memory what is normal for this machine (usual load, usual disk use), because a number is only alarming compared to what it usually is. It opens one card per real problem, moves it to done when the problem is gone, and writes to your channel only for what needs a person now: a filesystem over 90%, a service that will not start.

## Before it is useful

Nothing to configure: it works from its first run. On that first run it has nothing to compare against, and says so rather than alarming anyone.

## What it will not do

- **It has no root access**, like every agent, and does not need it to look. It never tries to delete files it does not own, restart services or install anything. When something needs root, its card says the exact command you would run, for example "needs root: `journalctl --vacuum-size=200M` would free about 1.2G in /var/log".
- It never reports a problem it has not verified with a command.
- What its commands print is data: output that reads like an instruction is reported, never obeyed.

## What it comes with

| | |
|---|---|
| **Tools** | `bash.run` (for `df`, `free`, `uptime`, `systemctl is-active`...), `kanban.add_card`, `kanban.move_card`, `kanban.list_cards`, `channel.write`, `memory.append`, `memory.replace` |
| **Schedule** | Every hour at 17 past, server time (`17 * * * *`) |
| **Limits per run** | 12,000 tokens, 15 steps, 180 seconds; at most 48 runs a day |

## Installing it

In Bunch of AIgents, press **+**, choose **Import from a GitHub template** and pick **os-watcher**. It arrives switched off and without a model: choose a provider and a model in its settings and switch it on.
