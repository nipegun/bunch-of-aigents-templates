# Bunch of AIgents - agent templates

This README is also available in:\
[en-GB](doc/README.en-GB.md), en-US, [es-AR](doc/README.es-AR.md), [es-ES](doc/README.es-ES.md)

Ready-made agents for [Bunch of AIgents](https://github.com/nipegun/bunch-of-aigents),
the self-hosted application that runs AI agents on your own Debian or Alpine
server. Each folder of this repository is one agent: its instructions, the
tools it needs, how often it wakes up and what it may spend.

## Using them

You do not download anything from here yourself. In Bunch of AIgents, press
**+** in the sidebar and choose **Import from a GitHub template**: the
application downloads this repository, lists the agents in it - with what each
one does, the tools it comes with and its schedule - and installs the one you
pick. It arrives switched off and without a model, so nothing runs until you
choose one and turn it on.

| Template | What it does |
|---|---|
| [**backup-watcher**](backup-watcher/README.md) | Checks that your backups ran, are recent, and are not suspiciously small. |
| [**cert-watcher**](cert-watcher/README.md) | Warns when a TLS certificate is about to expire, while there is still time. |
| [**disk-cleaner**](disk-cleaner/README.md) | Finds what is filling the disk and says what could go. Proposes; never deletes. |
| [**kanban-watcher**](kanban-watcher/README.md) | Reads the board every weekday morning and writes one short summary. |
| [**log-watcher**](log-watcher/README.md) | Reads the logs you point it at and reports what is new or newly frequent. |
| [**mail-watcher**](mail-watcher/README.md) | Watches a mailbox and acts on what arrives, following rules you write into its prompt. |
| [**news-watcher**](news-watcher/README.md) | Reads the RSS feeds you list in its prompt and reports what matches your rules. |
| [**os-watcher**](os-watcher/README.md) | Watches the machine: disk, memory, swap, load, and whether the services are up. |
| [**rag-consultant**](rag-consultant/README.md) | Answers questions from the documents in its library, citing the passage behind each claim. |
| [**site-watcher**](site-watcher/README.md) | Checks that the sites you list answer, answer quickly, and still say what they used to. |
| [**web-navigator**](web-navigator/README.md) | Drives its own browser to do what you ask on a site, and stops before any password or payment. |

## Using your own

Under **Settings -> Agents** you can point an installation at another
repository - a fork of this one, or one of your own with the same layout - and
at another branch. A machine with no way out to GitHub can use a `file://`
path holding `archive/refs/heads/<branch>.tar.gz`, the same layout the
installer accepts.

## Writing a template

A template is a folder with three files: `agent.json`, `system-prompt.md` and a
`README.md` that describes it. The first two are the same format the
**Export** tab of an agent downloads, so the quickest way to write one is to
build the agent in the application, export it, unzip it here and add its
README. The format, what is checked and why, is in
[doc/CODE.md](doc/CODE.md); how to write and publish one, step by step, in
[doc/MANUAL.md](doc/MANUAL.md).

## License

MIT, like the application. See [LICENSE](LICENSE).
