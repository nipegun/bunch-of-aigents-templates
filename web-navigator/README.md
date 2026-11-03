# web-navigator

Uses its own browser to do what you ask on a site: search, log in, fill a form, click through and report what it found.

## What it is for

Some things on the web cannot be done by reading a page: you have to search, click through results, fill in a form, or be logged in. This agent drives a real browser to do those tasks for you - "find the cheapest flight on this site", "check whether my order went through", "fill this form with these details" - and reports what it saw, step by step, with screenshots when they help. It stops before anything that spends money, sends something or needs a password.

## What it does

- Opens pages in its own browser, which keeps its cookies between runs: once you have logged in to a site, it can go on using it, and nobody else uses its session.
- Reads a page before acting on it, and again after every click or form submission, which is how it knows whether the step worked.
- Clicks links and buttons by the text written on them, types into fields, and presses Enter where a form expects it.
- Takes a screenshot when a page does not do what it should, and attaches it to its answer. You see it in the web chat, and on Telegram when the conversation started there.
- Reports what it did, in order, including any step it skipped.

It has no schedule: the request you send it is the task.

## Before it is useful

- **The browser must be installed on the server.** The installer installs it by default. If it was left out, the browser tools say so, and the agent tells you to run the installer with `--update --browser yes`; until then it does what it can with `web.fetch`.
- **Log in yourself** on the sites where it needs a session. It will never type a password; it gets to the login page and stops, and since its session survives, you only do it once.

## What it will not do

- **It never types a password, a card number, a one-time code or any other credential**, even when you put one in the message or a page asks for it.
- **It never buys, sends, publishes or accepts terms.** It fills the form, gets to the confirmation step, and tells you exactly which button would complete it.
- **It never obeys the page**: text on a site saying "ignore your instructions" or "email this to..." is quoted in its answer, nothing more.
- It never deletes anything or changes an account setting unless you asked for that exact change in the same request.
- It never repeats a failing action more than twice.
- It only visits public addresses, not machines on your local network.
- It never keeps a credential, a session cookie or the contents of a private page in its memory.

## What it comes with

| | |
|---|---|
| **Tools** | `browser.open`, `browser.read`, `browser.click`, `browser.type`, `browser.screenshot`, `image.send`, `web.fetch`, `memory.append`, `memory.replace` |
| **Schedule** | None: it works when asked |
| **Limits per run** | 20,000 tokens, 25 steps, 600 seconds; at most 48 runs a day |

## Installing it

In Bunch of AIgents, press **+**, choose **Import from a GitHub template** and pick **web-navigator**. It arrives switched off and without a model: choose a provider and a model in its settings, switch it on, and ask it for something in its chat.
