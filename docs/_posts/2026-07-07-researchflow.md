---

title: "ResearchFlow: The AI Health Literature Assistant Designed to get your Research Running"
date: 2026-07-07
categories: Projects
categories:
  - Projects
tags:
  - LangGraph
  - LangChain
  - Streamlit
  - Agentic AI
  - NLP
  - Healthcare AI
layout: single
toc: true
toc_label: "Sections"
toc_icon: "list"
toc_sticky: true
---
[Watch the Demo](https://youtu.be/wz89dV10qFM){: .btn .btn--primary}

## Why I Built ResearchFlow

Throughout my undergraduate studies in Data Science and Public Health, as well as my work as a research assistant, one task consistently consumed more time than any other: literature reviews.

The process was always the same. I'd start with a research question, search PubMed, sift through dozens of papers, open article after article, read dense abstracts, discard irrelevant studies, organize citations, and finally begin synthesizing the evidence. By the time I reached the actual writing stage, I'd often spent hours simply finding the right information.

Large language models can summarize text remarkably well, but they don't solve the workflow surrounding literature reviews. They still require relevant evidence, transparent citations, and mechanisms to determine whether enough information has been gathered before writing begins.

I wanted to build a system that automated this entire workflow and not just the writing, allowing me to get a research evidence base quickly and thoroughly.

That idea became **ResearchFlow**, an agentic AI assistant that automates literature reviews through retrieval, semantic ranking, iterative reasoning, and structured review generation.

---

## The Goal

Rather than treating a literature review as a single prompt, I wanted to model it as a sequence of reasoning steps.

At a high level, the workflow looks like this:

Research Question

↓

Generate Search Queries

↓

Search PubMed

↓

Rank Papers

↓

Analyze Evidence Coverage

↓

Summarize Papers

↓

Generate Literature Review

↓

Critique & Revise

↓

Export Results

Each step performs a single responsibility while sharing information through a common state object.

---

## Why LangGraph?

Initially, I considered implementing the workflow as a traditional sequential chain. While this would have been simpler, literature reviews aren't naturally linear.

Two important questions arise throughout the process:

* Have enough relevant papers been collected?
* Is the generated review actually good enough?

Answering either question requires branching logic.

This led me to LangGraph, which allows workflows to be represented as directed graphs instead of fixed pipelines. Every node reads from and writes to a shared state, making intermediate results available to downstream components without repeatedly invoking the language model.

This stateful architecture became the foundation of the project.

---

## Agentic Loop #1: Evidence Collection

Retrieving papers turned out to be much more challenging than simply querying PubMed once.

A single search often misses important studies due to differences in terminology or indexing.

Instead of assuming the first search is sufficient, ResearchFlow performs an evidence coverage analysis after ranking retrieved papers.

If coverage is determined to be incomplete, the workflow automatically generates follow-up search queries and searches PubMed again before reranking the expanded collection.

This creates the first reasoning loop:

Coverage Analysis

↓

Coverage sufficient?

↓

No

↓

Generate Follow-up Queries

↓

Search Again

↓

Rank Papers

↓

Coverage Analysis

Rather than relying on increasingly broad searches, the system reasons about missing evidence and adapts its retrieval strategy accordingly.

---

## Agentic Loop #2: Review Refinement

Once the most relevant papers have been summarized, the system synthesizes a first draft of the literature review.

Instead of immediately returning this draft, a second agent evaluates it.

The review is critiqued for issues such as missing themes, unsupported claims, weak organization, or incomplete discussion.

If improvements are needed, the review is revised and evaluated again.

This creates a second iterative reasoning loop:

Literature Review

↓

Critique

↓

Needs Revision?

↓

Revise Review

↓

Critique Again

Only after the critique stage is satisfied does the workflow finalize the review.

This iterative process consistently produced more complete and coherent summaries than a single-pass generation approach.

---

## Semantic Paper Ranking

Sending every retrieved paper directly to the language model would be both inefficient and expensive.

Instead, ResearchFlow ranks papers locally using semantic embeddings.

The research question and each retrieved paper are embedded into a shared vector space, allowing similarity scores to be computed without additional LLM calls.

Only the highest-ranking papers proceed to summarization.

This approach offers several advantages:

* Lower inference costs
* Reduced context size
* Faster generation
* More relevant evidence

Switching from API-based embeddings to a local embedding model also eliminated rate limits while significantly improving development speed.

---

## Building a Stateful Workflow

One of the biggest lessons from this project was learning how important state management becomes in agentic systems.

Rather than treating each prompt independently, every node contributes information to a shared graph state.

For example, the workflow gradually accumulates:

* generated search queries
* retrieved papers
* ranked papers
* paper summaries
* citations
* coverage analysis
* literature review drafts
* revision history
* exported files

Because every node operates on shared state, intermediate results never need to be recomputed.

This makes the workflow both more efficient and easier to debug.

---

## Challenges Along the Way

Like most engineering projects, ResearchFlow evolved through a series of technical challenges.

Some of the more interesting problems included:

* Designing effective semantic ranking for biomedical literature
* Managing asynchronous LangGraph execution inside Streamlit
* Avoiding embedding model rate limits during development
* Parsing structured PubMed metadata and citations
* Building conditional routing for multiple reasoning loops
* Automatically generating exportable Markdown with hyperlinks and references

Many of these issues had little to do with prompt engineering and much more to do with software architecture.

That was one of the most valuable takeaways from the project.

---

## What I Learned

ResearchFlow reinforced several ideas that I believe will shape future AI systems.

First, powerful AI applications are rarely a single prompt.

They are workflows composed of retrieval, reasoning, evaluation, and iteration.

Second, retrieval quality often matters more than increasingly sophisticated prompting.

Finally, modular workflows built around explicit state are significantly easier to extend, debug, and evaluate than monolithic pipelines.

---

## Looking Ahead

Although ResearchFlow already automates much of the literature review process, there are several directions I'd like to explore next:

* Support for additional scientific databases beyond PubMed
* PDF and Word document export
* Interactive evidence exploration
* Human feedback integrated into the revision loop
* Quantitative evaluation of review quality against expert-written literature reviews

---

## Final Thoughts

ResearchFlow began as an attempt to automate one of the most repetitive parts of academic research.

Along the way, it became an exploration of stateful agentic AI systems, semantic retrieval, workflow orchestration, and iterative reasoning.

More than anything, this project taught me that building practical AI systems is less about writing increasingly clever prompts and more about designing workflows that combine language models, tools, and structured state into reliable software.

I'm excited to continue building systems like this that bridge AI research and real-world applications.
