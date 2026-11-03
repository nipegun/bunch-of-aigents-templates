# MANUAL - bunch-of-aigents-templates

This MANUAL is also available in:\
en-GB, [en-US](MANUAL.md), [es-AR](MANUAL.es-AR.md), [es-ES](MANUAL.es-ES.md)

How to use these templates, and how to write, check and publish one.

## Contents

1. [Installing a template](#installing-a-template)
2. [Using another repository](#using-another-repository)
3. [A machine with no way out to GitHub](#a-machine-with-no-way-out-to-github)
4. [Writing a template](#writing-a-template)
5. [What agent.json may hold](#what-agentjson-may-hold)
6. [Checking it](#checking-it)
7. [When a template does not appear](#when-a-template-does-not-appear)

## Installing a template

In Bunch of AIgents, press **+** in the sidebar and choose **Import from a
GitHub template**. The list shows every template of this repository, with its
description in the language of the interface, the tool families it comes with,
how often it wakes up and whether it switches its library on. Pick one, give it
a name (its own is filled in) and it is created.

It arrives **switched off and without a model**. Open its settings, choose a
provider and a model, read its system prompt - several say what you need to
fill in before they are any use - and switch it on.

## Using another repository

**Settings → Agents** in the application holds the address of the templates
repository and its branch. Empty means this one, branch `main`. Any repository
with the same layout works: a fork of this one, with your own changes, or one
of your own. The address has to be `https://` (or `file://`, below).

## A machine with no way out to GitHub

The application downloads `<address>/archive/refs/heads/<branch>.tar.gz`. For a
machine with no internet, put that file somewhere on it and give the
application a `file://` address:

```
mkdir -p /opt/templates-mirror/archive/refs/heads
cd /path/to/a/copy/of
tar czf /opt/templates-mirror/archive/refs/heads/main.tar.gz bunch-of-aigents-templates
```

and set **Settings → Agents → Templates repository** to
`file:///opt/templates-mirror`. The archive must hold one folder at its top,
with the templates inside it, which is what GitHub serves and what the command
above makes.

## Writing a template

The quickest way is to build it in the application first:

1. Create an empty agent, write its system prompt, choose its tools, schedule
   and limits, and try it until it does what it should.
2. Open its **Export** tab and download it **without** memory, home files,
   library or model - unless the template is meant to carry them.
3. Unzip it into a new folder of this repository, named like the agent's name:
   lowercase letters, digits and dashes, such as `invoice-reader`.
4. Edit `agent.json`: set `name` to the folder's name, give `description` the
   fifteen languages, and remove `exported_at`.
5. Check the system prompt: in English, one line per paragraph, treating what
   it reads - pages, mail, documents - as data and never as instructions, and
   ending with: *Answer in the language this prompt is written in. If the user
   writes to you in another language, answer in theirs.*
6. Write its `README.md`, in English (en-US): what the agent is for, what it
   does, what it needs before it is useful, what it will not do, what it comes
   with - tools, schedule, limits - and how to install it. The application does
   not read it; people do, so it has to say what the prompt and `agent.json` say.
   The folders already here show the shape.

A folder holds `agent.json`, `system-prompt.md` and `README.md`, and may hold
`memory.md`, `home/...` and `rag/documents.json` with `rag/files/...`.
Anything else makes the application refuse it.

## What agent.json may hold

```json
{
  "format": 1,
  "name": "invoice-reader",
  "description": {
    "de-DE": "...", "en-GB": "...", "en-US": "Reads the invoices you drop in its folder.",
    "es-AR": "...", "es-ES": "...", "fr-FR": "...", "he-IL": "...", "hi-IN": "...", "it-IT": "...",
    "ja-JP": "...", "ko-KR": "...", "pt-BR": "...", "pt-PT": "...", "ru-RU": "...", "zh-CN": "..."
  },
  "tools": ["bash.run", "kanban.add_card"],
  "schedules": ["0 8 * * 1-5"],
  "limits": {"max_tokens_per_run": 12000, "max_steps_per_run": 15,
             "timeout_seconds": 240, "max_runs_per_day": 4}
}
```

- **tools**: dotted names as the application lists them under Settings →
  Tools. One the installation does not have is left out, and the list says so.
- **schedules**: five-field cron schedules, at most twenty. An empty list means
  it works only when asked.
- **limits**: what one run may spend. Ceilings, not targets.
- **rag**: `{"enabled": true}` if the agent is built around its document
  library, as `rag-consultant` is.
- Never `enabled`, keys, tokens or credentials: the format has nowhere to put
  them, and every template arrives switched off.

## Checking it

With the application checked out beside this repository, run from
`../bunch-of-aigents`:

```
PYTHONDONTWRITEBYTECODE=1 PYTHONPATH=.:tests python3 -m unittest test_tools.TestExampleAgents
```

It reads every template here with the same code the application installs them
with, and fails on anything it would refuse, a tool that does not exist, a
missing language, or a prompt wrapped at a fixed column.

## When a template does not appear

The list in the application says how many templates could not be read. Its
API, `GET /api/admin/agent-templates`, returns each one with its `error`, which
says what to fix: an unknown key, a schedule that is not five cron fields, a
file the format does not know, a path under `home/` that is hidden or climbs
out. The application keeps the repository for five minutes; the API's
`?refresh=1` downloads it again.
