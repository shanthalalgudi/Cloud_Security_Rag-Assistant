# Cloud Security RAG Assistant

A retrieval-augmented (RAG) assistant built on Amazon Bedrock that answers cloud security competitive questions with grounded, cited answers. Built to develop hands-on AI fluency in the SASE/SSE space I market in.

> Built with publicly available vendor documentation, for demonstration purposes.

## The Problem

Competitive research is slow and scattered. To answer a question like "how does Vendor A's DLP compare to Vendor B's," a product marketer reads across multiple vendor pages and docs, then synthesizes an answer by hand. It's tedious, easy to get wrong, and goes stale every time a competitor ships something new.

## What It Does

Ask a cloud security comparison question in plain English and get an answer grounded in the source content, with citations back to where each claim came from. Instead of hunting through vendor pages, an enablement team gets a fast, sourced answer they can verify.

![Cloud security RAG assistant answering a DLP capabilities question with source citations](/dlp-capabilities.png)

## How It Works

This is a two-step RAG pipeline: **retrieve**, then **generate**.

1. **Retrieve** — the assistant searches an Amazon Bedrock Knowledge Base (built by crawling vendor documentation pages) and pulls back the most relevant passages for the question.
2. **Build context** — those passages are combined with the question into a single prompt.
3. **Generate** — the prompt goes to the model, which writes an answer grounded only in the retrieved passages, not its general training.
4. **Cite** — the answer includes source citations so the reader can verify it.

A guardrail is applied on the generation step to keep answers on-topic and reduce the risk of fabricated claims.

## Tech Used

- **Amazon Bedrock** — foundation model access and orchestration
- **Bedrock Knowledge Bases (Web Crawler data source)** — managed retrieval over crawled vendor pages
- **Bedrock Guardrails** — output screening on the generation step
- **Amazon Nova Lite** — the generation model
- **Python (boto3)** — the application code

## Repo Contents

- `knowledge_base_query.py` — the core retrieve + generate assistant
- `kb_query_with_guardrail.py` — the same flow with a guardrail applied
- `images/` — screenshots of the assistant in use

## Setup

1. Create a Bedrock Knowledge Base with a **Web Crawler** data source, pointing at the vendor documentation URLs you want to index.
2. Sync the data source so Bedrock crawls the pages and builds the index.
3. In the scripts, replace `REPLACE_WITH_YOUR_KB_ID` with your Knowledge Base ID (and `REPLACE_WITH_YOUR_GUARDRAIL_ID` if using the guardrail version).
4. Run: `python knowledge_base_query.py`

## Honest Limits

This is a demonstration of the pattern, not a production system.

- **Answers reflect the last crawl.** The knowledge base is a snapshot of the vendor pages from when they were last crawled. Vendor sites change, so answers can go stale unless the crawl is re-run. Someone has to keep it current.
- **Retrieval is capped.** It pulls a fixed number of passages, so a question whose answer is scattered across many pages can come back incomplete. Citations are the safeguard — verify before relying on an answer.
- **Output needs a marketer's edit.** The assistant drafts; it doesn't replace judgment on tone, accuracy, or positioning.


