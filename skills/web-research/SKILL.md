---
name: web-research
description: Use when the user asks a question that requires current, external, or public information not available in the workspace data model — market data, competitor info, news, industry benchmarks, regulatory/compliance facts, general "look this up" or "search the web" requests, or anything requiring figures from outside the warehouse. Also use when the user asks you to verify or supplement an internal number with external context.
---

# Web Research

Use this skill whenever a question needs information that cannot come from the governed data model
or the warehouse — e.g. industry benchmarks, competitor pricing, market sizing, news, regulatory
changes, or general factual lookups. Internal metrics (revenue, orders, customers, etc.) always come
from `search_fields` and `sql_query` first; only reach for web research to fill gaps the data model
cannot answer.

## Core Rules

1. **Never fabricate a number, fact, or citation.** Every external figure or claim you present MUST
   come from an actual web search result. If you cannot find a reliable source, say so plainly instead
   of guessing.
2. **Always cite sources.** Every externally sourced number or claim must include the source name and
   a link (or clear reference) so the user can verify it. Do not present web-derived figures as if
   they were governed data model output — keep the two clearly distinct in your answer.
3. **Prefer primary and recent sources.** Favor original reporting, official company/government pages,
   and recent publications over aggregators, forums, or outdated pages. Check publication or "last
   updated" dates when the question is time-sensitive.
4. **Cross-check material claims.** For any number that will materially affect a business decision
   (e.g. market size, competitor pricing, growth rate), try to confirm it against a second independent
   source before presenting it as fact. If sources disagree, report the range and note the discrepancy
   rather than picking one silently.
5. **Separate fact from inference.** State clearly what the source says verbatim versus what you are
   inferring or summarizing from it.
6. **Respect recency for volatile topics.** For anything that changes often (pricing, market share,
   news, regulations), prefer the most recent available source and note the as-of date in your answer.

## Workflow

1. Identify exactly what external fact(s) the user needs and whether the data model can answer any
   part of it internally first (run `search_fields` if there is any chance the question touches
   workspace data).
2. Run the web search tool available in this conversation with a specific, targeted query. Avoid
   overly broad queries that return low-signal results.
3. Read enough of the result to confirm it actually answers the question — do not rely on a headline
   or snippet alone if the number matters.
4. If the question is time-sensitive or high-stakes, run a second search with different phrasing to
   cross-check the figure.
5. Summarize the finding concisely, blending it with any internal data pulled from the workspace, and
   cite every external source used.
6. If no reliable source can be found, tell the user directly rather than presenting an unsupported
   answer.

## Output Format

- Lead with the direct answer.
- Cite sources inline or in a short "Sources" list at the end, with names and links.
- When combining internal and external data (e.g. internal revenue vs. an external market-size
  estimate), label each number's origin so the user knows which is governed data and which is
  external research.
