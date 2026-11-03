# CODE - bunch-of-aigents-templates

This CODE is also available in:\
en-GB, [en-US](CODE.md), [es-AR](CODE.es-AR.md), [es-ES](CODE.es-ES.md)

Technical reference of this repository for people and language models. Jump to
the section you need; each one stands on its own.

## Index

1. [Architecture and design decisions](#1-architecture-and-design-decisions)
2. [Module map](#2-module-map)
3. [Key symbol index](#3-key-symbol-index)
4. [Main flows](#4-main-flows)
5. [Entry point and route map](#5-entry-point-and-route-map)
6. [Impact analysis](#6-impact-analysis)
7. [Extension points](#7-extension-points)

---

## 1. Architecture and design decisions

This repository holds no code. It is data read by another program:
[Bunch of AIgents](https://github.com/nipegun/bunch-of-aigents) (the
"application"), checked out beside it as `../bunch-of-aigents`. Every folder at
the top that holds an `agent.json` is one agent template, and the application
offers them under **+ → Import from a GitHub template**.

**Why a repository of its own.** The templates used to ship inside the
application, one Markdown file each. Out here, a new or improved template
reaches every installation the next time somebody opens the list, without an
update of the application, and an installation can be pointed at a fork or at
a repository of its own (Settings → Agents in the application).

**Why one download.** The application does not walk this repository through
the GitHub API. It downloads the one `.tar.gz` GitHub serves for a branch,
`<repository>/archive/refs/heads/<branch>.tar.gz` - the same address its
installer downloads the application from - and keeps it for five minutes. One
request, no rate limit, and the same layout works from a `file://` path on a
machine with no way out.

**Why the same format as an export.** A template folder is exactly what the
**Export** tab of an agent produces, unzipped: `agent.json` and
`system-prompt.md`, and optionally `memory.md`, `home/...` and a library in
`rag/`. So the application installs a template and imports a `.zip` with the
same code, and the quickest way to write a template is to build the agent in
the application and export it.

**Why so strict.** A template is a set of permissions as much as a prompt: it
names the tools the agent arrives with and when it wakes up. The application
checks every template before offering it and again before installing it, and
refuses rather than guesses: an unknown key in `agent.json`, a schedule with
anything but five cron fields, a hidden file under `home/`, a path that climbs
out, a link. A refused template is still listed, with its error, so its author
sees why.

**What a template can never do.** There is nowhere in the format for
`enabled`: every template is installed switched off and without a model unless
it names one. There is nowhere for API keys, tokens, channel credentials or
channel subscriptions. Its schedules become crontab lines that start the agent
and nothing else.

## 2. Module map

| Module | Path | Responsibility | Depends on | Used by |
|---|---|---|---|---|
| One template | `<id>/` | One agent: its identity, permissions, schedule and prompt | - | The application's `agent_templates` and `agent_package` |
| Manifest | `<id>/agent.json` | Name, descriptions, tools, schedules, limits, library settings | The application's format 1 | `agent_package.fValidateManifest` |
| Prompt | `<id>/system-prompt.md` | The system prompt, in English | - | Installed as the agent's `system-prompt.md` |
| Description | `<id>/README.md` | What the agent does and what it is for, for a person reading the repository; en-US | `agent.json`, `system-prompt.md` | People; ignored by the application |
| Optional contents | `<id>/memory.md`, `<id>/home/...`, `<id>/rag/...` | What an export can also carry | - | `agent_io.fInstallPackage` |
| Documentation | `README.md`, `doc/` | This repository explained | - | People |
| Rules | `AGENTS.md`, `CLAUDE.md`, `RulesForThisProjectOnly.md`, `Rules/` | How to work on this repository | - | People and assistants |

## 3. Key symbol index

The "symbols" of a data repository are the keys of `agent.json`. The
authoritative list is `lManifestKeys` in the application's
`backend/core/agent_package.py:80`. Line numbers are those of the application
at the time of writing; if one no longer matches, search the function name in
the same file.

| Key | Where it is read | File:line in the application | What it does |
|---|---|---|---|
| `format` | `agent_package.fValidateManifest` | `backend/core/agent_package.py:404` | Must be `1`. A package of another format is refused |
| `name` | `agents.fValidateAgentName` | `backend/core/agents.py:77` | 2 to 40 letters, digits, spaces, dots, dashes, underscores. Here, the folder's name |
| `description` | `agent_package.fValidateDescription` | `backend/core/agent_package.py:359` | Text, or an object of texts by language (`xx-YY`). Templates carry all fifteen |
| `tools` | `agent_package.fValidateManifest` | `backend/core/agent_package.py:404` | Dotted tool names. Ones the installation lacks are left out and listed |
| `skills` | `agent_package.fValidateManifest` | `backend/core/agent_package.py:404` | Skill names; the installation keeps the ones it has |
| `schedules` | `agents.fValidateScheduleList` | `backend/core/agents.py:117` | Five-field cron schedules, at most twenty and at most 288 starts a day between them. Empty: works when asked |
| `limits` | `agent_package.fValidateManifest` | `backend/core/agent_package.py:404` | `max_tokens_per_run`, `max_steps_per_run`, `timeout_seconds`, `max_runs_per_day`, `max_memory_characters` |
| `rag` | `rag_settings.fValidateSettings` | `backend/core/rag_settings.py:53` | Library settings; `{"enabled": true}` switches the library on |
| `kanban_enabled` | `agent_package.fValidateManifest` | `backend/core/agent_package.py:404` | `false` to create it without the board |
| `provider`, `fallback_provider` | `agent_package.fValidateProviderEntry` | `backend/core/agent_package.py:386` | `name`, `model`, `base_url`. Never a key. Templates usually leave them out |
| `exported_at` | `agent_package.fValidateManifest` | `backend/core/agent_package.py:404` | Informative, written by an export |

## 4. Main flows

Listing, in the application:

```
+ → Import from a GitHub template
  GET /api/admin/agent-templates?language=<interface language>
    agent_templates.fLoadTemplates → download <repo>/archive/refs/heads/<branch>.tar.gz (5 min cache)
      agent_package.fReadRepositoryArchive    one source per top folder with an agent.json
    agent_package.fReadPackage                checks each; a refused one is listed with its error
```

Installing:

```
POST /api/admin/agents {name, template: <id>, language}
  agent_templates.fReadTemplate(<id>)        downloaded and checked again, by the server
  agent_io.fInstallPackage
    create_agent (switched off, tools, skills, limits, rag, schedules)
    memory, home files, library documents    only if the folder carries them
    any failure deletes the agent again
```

Checking, while writing one:

```
cd ../bunch-of-aigents
PYTHONDONTWRITEBYTECODE=1 PYTHONPATH=.:tests python3 -m unittest test_tools.TestExampleAgents
  fLoadRepositoryTemplates → agent_package.DirSource(<this repository>/<id>) + fReadPackage
```

## 5. Entry point and route map

| What | Address | Read by |
|---|---|---|
| The whole repository, one branch | `https://github.com/nipegun/bunch-of-aigents-templates/archive/refs/heads/main.tar.gz` | `agent_templates.fDownloadArchive` |
| A mirror for a machine with no way out | `file:///<path>/archive/refs/heads/<branch>.tar.gz` | `agent_templates.fDownloadArchive` |
| The repository and branch used | Settings → Agents (`templates_repo_url`, `templates_repo_branch`) | `agent_templates.fReadRepositorySettings` |

## 6. Impact analysis

| Component | Changing it affects |
|---|---|
| A folder's name | The template's id. Installed agents keep working (they are copies), but anything that names the template - documentation, tests - must follow |
| `tools` of a template | What every agent installed from it from now on can do. A tool removed from the application makes the template list it as missing |
| `schedules` of a template | How often its agents wake up, and so what they cost |
| `system-prompt.md` | Only agents installed after the change. An installed agent has its own copy. Its `README.md` has to say the same |
| The package format (application's `agent_package`) | Every template here and every exported `.zip`. A new key needs the application version that reads it; a changed meaning needs a new `format` |

## 7. Extension points

### A new template

1. Build and try the agent in the application, then **Export** it (without
   memory, home or library unless the template is meant to carry them) and
   unzip it into a new folder named like its `name`.
2. Give `agent.json` a `description` in the fifteen languages and remove what
   a template should not carry (`provider`, `exported_at`).
3. Make sure `system-prompt.md` is in English, in whole lines, treats what it
   reads as data, and ends with the rule about the language of the answer.
4. Write its `README.md`, in en-US: what it is for, what it does, what it needs
   before it is useful, what it will not do, what it comes with and how to
   install it. Copy the shape of an existing one.
5. Run `TestExampleAgents` in `../bunch-of-aigents`.
6. Add its row, linked to its README, to the tables in the four READMEs here (`README.md` and
   `doc/README.*.md`) and the fifteen MANUALs of the application
   (`../bunch-of-aigents/doc/MANUAL*.md`).

### A new key in agent.json

It is a change of the application first (`agent_package.lManifestKeys` and its
validation), then of the templates that use it. Until installations run that
version, a template with the new key is refused by them.
