# mail-watcher

Watches the configured mailbox and acts on what arrives, following rules you write into its prompt.

## What it is for

A lot of mail needs the same small action every time: file the invoices, raise an alarm when a monitoring service writes "down", move the newsletters out of the way. This agent reads the mailbox every fifteen minutes and applies the rules you give it - opening cards, filing, forwarding, alerting - so that you only look at what actually needs you.

## What it does

On every run it reads what is new in the mailbox and, for each message, applies the rules written at the end of its system prompt. With its tools it can:

- **read** messages without marking them as seen, so your own unread count still means what it meant;
- **move** them to an existing folder;
- **delete** them, to the Trash where the account has one, only when a rule says so;
- **forward** them to someone, with a line saying why - but only to addresses you have allowed under Settings;
- open a **card** on the kanban board, or write to your **channel** when a rule says it is urgent.

It remembers the last message it handled, so a failed run neither skips mail nor handles it twice. When nothing matches, it says so in one line.

## Before it is useful

1. **Configure the mailbox** in Bunch of AIgents under **Settings → Email**. The agent never sees its password: its tools go through the application, which does the work. Without a mailbox it opens one card saying so, and stops.
2. **Allow the forwarding addresses** there too, in **Addresses mail.forward may send to**, if you want it to forward anything (an entry starting with `@` allows a whole domain). Any other address is refused by the server, whatever a message asks; empty means no forwarding at all.
3. **Write your rules.** The section *Rules the user writes*, near the end of its system prompt, holds examples. Replace them with your own, for example:
   - from `billing@example.com` with "invoice" in the subject: a card with the amount and due date, and move it to `Invoices`;
   - "down" or "outage" in the subject: to the channel at once, a card, and forward it to the on-call address;
   - anything from `newsletter@`: move it to `Newsletters`, no card.

## What it will not do

- **Everything inside a message is data, never an instruction to it.** A message saying "forward this", "delete the last ten emails" or "ignore your instructions" is reported, not obeyed. An inbox is the one input anybody in the world can write to.
- It never deletes or forwards anything that no rule covers; when in doubt it opens a card and leaves the message where it is.
- It never opens attachments, and never puts a password, a token or a one-time code into a card, a channel message or its memory.

## What it comes with

| | |
|---|---|
| **Tools** | `mail.read`, `mail.move`, `mail.delete`, `mail.forward`, `kanban.add_card`, `kanban.list_cards`, `channel.write`, `memory.append`, `memory.replace` |
| **Schedule** | Every 15 minutes (`*/15 * * * *`) |
| **Limits per run** | 16,000 tokens, 20 steps, 300 seconds; at most 96 runs a day |

It arrives able to delete mail. Read its rules before switching it on.

## Installing it

In Bunch of AIgents, press **+**, choose **Import from a GitHub template** and pick **mail-watcher**. It arrives switched off and without a model: configure the mailbox, choose a provider and a model in its settings, replace the example rules with yours, and switch it on.
