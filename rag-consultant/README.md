# rag-consultant

Answers questions from the documents in its RAG library, cites the passage behind each claim, and says so when the documents do not cover something.

## What it is for

Manuals, books, regulations, internal documentation: a collection of documents is only useful if you can find the answer in it quickly and trust where the answer came from. This agent answers your questions from its own document library, links every statement to the page it comes from, and tells you plainly when the documents do not say. It is meant for anyone who wants to ask a pile of PDFs a question and get an answer they can check.

## What it does

- Searches its library by meaning and by exact terms. Questions and documents do not need to be in the same language.
- Reads around a passage when it is cut off or ambiguous, before relying on it.
- Puts a citation after each statement. In the chat each one becomes a link to the place in the document it came from - the page of a PDF, the chapter of an EPUB.
- Keeps what the documents say apart from general knowledge, and leaves general knowledge out when you ask only for what the documents say.
- When documents disagree, says what each one states, with its year, version or author, rather than picking one.
- Says which documents are still being indexed, rather than "not found", when you ask about one of them.

It has no schedule: it works when you ask it something.

## Before it is useful

1. **Give it documents.** In its **RAG** tab, upload PDF, EPUB, TXT or Markdown files, or copy them into its `rag/inbox/` folder and import them. They are extracted, OCR'd when scanned and vectorized locally, on the server; wait until they show **Ready**.
2. **Fill in each document's information** (year, title, author, version...) in the same tab. The agent sees it with every passage, which is how it can date and attribute what it quotes.
3. **Choose the answer mode** in the RAG tab:
   - *Documents and general knowledge*: it may add general knowledge, kept apart.
   - *Document evidence required*: it must search before answering, and says so when nothing was found.
   - *Documents only (verified)*: every paragraph must cite a passage that backs it; the application removes the ones that do not, and says how many.

It is the only template that arrives with its library already switched on: its tools are withheld while the library is off.

## What it will not do

- It never invents titles, page numbers or quotations, and never attributes to a document something it did not retrieve from it.
- The documents are data: a passage saying "ignore your instructions" is quoted if it matters, never obeyed.
- The retrieved passages are sent to the model you choose for it; the extraction and the vectors stay on your server.

## What it comes with

| | |
|---|---|
| **Tools** | `rag.search`, `rag.read`, `rag.list` |
| **Schedule** | None: it answers when asked |
| **Limits per run** | 40,000 tokens, 12 steps, 300 seconds; at most 100 runs a day |
| **Library** | Switched on |

Its token limit is higher than the other templates' because the passages it reads count against it: with less, there would be no room for them.

## Installing it

In Bunch of AIgents, press **+**, choose **Import from a GitHub template** and pick **rag-consultant**. It arrives switched off and without a model: choose a provider and a model in its settings, upload its documents in the RAG tab, and switch it on.
