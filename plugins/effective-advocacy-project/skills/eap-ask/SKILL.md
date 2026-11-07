---
name: eap-ask
description: Answer questions about the user's Effective Advocacy Project (EAP) campaigns with the EAP tools - what happened (weekly bulletins, the latest week on a topic or country), where countries and actors stand (landscapes, arguments, voices), the status of policies and what is coming up, what an organisation or person has said, how members of parliament and governments act and vote (roll calls, amendments), and the campaign's source documents. Use whenever the user asks about their campaign, an actor, a statement, a policy, a vote or a document EAP may hold, even if they do not name EAP; never answer these from memory.
---

# Using the EAP tools

The EAP tools read the campaigns this user has been granted, and nothing
else. Each campaign has a slug; every tool takes it as `project_slug`.

## If the EAP tools are missing

Installing the plugin does not connect it. If you cannot find
`list_my_projects` or the other EAP tools, do not answer from memory or
invent data: tell the user the EAP connector is not connected yet, and how
to connect it.

1. Open Customize (Settings in Claude Desktop), then Plugins, then
   **Effective Advocacy Project**.
2. Open its **Connectors** tab and click **Connect** next to `eap`.
3. Click **Continue**, leave the sign-in options as they are, click
   **Add**, and sign in with their EAP portal account.
4. Then send the question again, in a new chat if the tools still do not
   appear.

If they see no plugin there, they can follow the steps on
https://app.effectiveadvocacyproject.org/connect instead.

## Start

- If the user has not named a campaign, call `list_my_projects` first. With
  one campaign you may omit `project_slug`; with several, ask which one, or
  pick the one the question clearly names, and pass its slug everywhere.
- `list_my_projects` also says what each grant reaches.
- Every tool takes an optional `intent`: one sentence about the user's goal
  (never their words, never names of private people).

<!-- data-guide: a verbatim copy of efi_mcp/data_guide.md, which Ask's own prompt reads too;
     efi_platform/tests/test_data_guide.py fails when the two differ. Edit the file, then paste it here. -->
## EAP's data, by kind

Each reply states its scope (window, topic, country, unit) and date; its notes on the result ("SUPPRESSED", "outside_filter", "not a count") are part of it.

### Curated artefacts, written by the EAP team
- `bulletins`: what the editors judged mattered each week, with sources; published standard editions only. For "what happened last week", "what did the bulletin say about X", a recap. An edition covers its week as of its issue date: the days since are not in it, and a week without one is not a week with nothing. Cite the documents its text cites (`[doc:<id>]`), not the bulletin.
- `landscapes`: a review of one country, or one topic across Europe: camps, arguments, who decides next. The first read for "where does NL stand on X", "who are the key players". As of its "current to" date; later events are in votes, documents and Latest blocks. A summary, not a primary source: cite what it names.
- `topic_latest` / `country_latest`: the most recent week on a topic or country, with what is coming up. For "what's new on X", "what's next in FR". Its own week only.
- For all three, "none published yet" means none written, not nothing happening.

### Policy
- `policies`: the campaign's measures with legal status, path to decision and votes on their files. Nightly. For "status of the meat-names ban", "what is in negotiation in FR". A status is the latest cited line, as precise as its source (a month is a month); "not recorded" is unknown, never "no status". Its votes are on files that can hold other measures, so carry no side: the split is in `vote_detail`.
- `agenda`: what is coming up, about 90 days past the nightly export. Nothing past; "not followed" is unknown, not "nothing coming".
- Votes and amendments: `search_votes`, `vote_detail` (split by group, rebels, one country's ballots), `voting_profile` (one member), `parliament_overview` (members by category; `group` / `tier` for names), `parliament_stats`, `search_amendments`. Nightly. Only as complete as each chamber publishes; some (Tweede Kamer show of hands, Austrian Nationalrat club decisions) record parties, not members. A category is a label, not a score; a rapporteur's or committee's amendment is not the deputy's own position.
- Executive: `executive_overview`, `executive_profile`: what a government and its members did, said, promised, whom they met. Doing and saying are never added; "thin", "no record" and "no formal acts collected" are not neutral, not zero.

### Discourse
- Talking points: `talking_points` (recurring arguments per side), `talking_point_statements` (the arguments behind a point or voice), `top_voices` (who carries each side). Nightly; arguments only. The first and last count documents, the middle one arguments. The campaign's default topic unless `topic='all'`. Credit goes to the principal, including an argument a journalist reports. "Too thin to rank" (suppressed) is not "nobody". `speaker_country` is the speaker's, `publisher_country` the outlet's; pass `period`, not dates.
- One-pass read: `statements` (who said what), `positions` (stances per issue and document; a side only where two models agree), `new_arguments` (reasons matching no talking point). As of the reply's `as_of`. Credit goes to the text's owner (byline, person quoted, signers): an actor can be absent here and hold a stance in the talking points. A side is on the issue's own polarity.
- `country_landscape`: each country's parliament record, government reading and top arguments and voices, or one country's page. Nightly. "not collected" is unknown, not zero.

### Actors
`find_actor`, `actor_profile`, `get_actor_dossier`, `actor_statements`, `list_actor_quotes`, `list_actor_mentions` (others naming them), `actor_stats` (counts, leaderboards). As collected. Who someone is and what they said themselves; an argument reported about them is in the talking points by `actor_id`.

### Search and documents
`search_documents`: enumerate the project's held documents, newest first, with an exact filtered total and an opaque continuation cursor. Optional lexical query over titles and digest-or-lead (not full text), configured topic, publisher country, speaker registry country, inclusive dates, language, channel and any source kind. Filters apply before paging; reuse the same filters with the cursor and restart if an export/scope changes. Topic/speaker filters require analysed arguments, so they cannot rule out unread or unclassified material. Returns document ids and citation URLs for `get_document`. No topic means all documents, including unread ones.

`document_coverage`: the same filters and total as `search_documents`, with a publisher-country overview and paged source-family counts, date ranges and undated/future counts. `include_eu27=true` includes every member state even when no evidence is held; countries excluded by a publisher filter are labelled outside that filter, not zero. Shows collection counts before query/topic/speaker filtering alongside matching counts. Zero means no held evidence in that scope, never no political activity. Publisher location and speaker registry country do not establish the country discussed; no subject-country field is inferred. Counts are collection breadth, not independent corroboration, political influence or reading completion. Documents and analysis exports each state their own as-of date; unavailable exports or query indexes are unknown, not zero. Grants: `search_documents` requires the Documents grant; `document_coverage` requires the explicit Statistics grant.

`search_chunks`: the most relevant passages, any language, refreshed daily. Any held source kind can be filtered (including national parliamentary/government families). Never a count, never proof of absence: it returns `top_k` whatever exists. `get_chunk`, `get_document` read in full.

### Counts
`corpus_stats` (documents collected, by month, source, channel), `coverage` (collected and read per country and channel; each source's first and last date: whether a silence is "not collected"). A page ("30 of 412") is not a count: quote its `total`. Counts over time measure our collection as much as the debate: never say anything grew, fell or peaked, nor compare periods.

## Where to start
- What happened, what's new: `bulletins`, `topic_latest` / `country_latest`, then documents for the days since.
- Who stands where: `landscapes`, `country_landscape`; check `top_voices`, `positions`, votes.
- Where actor X stands: `actor_profile`, `statements`, `positions`; before concluding, `talking_point_statements` with their `actor_id` and `topic='all'`, and `top_voices`.
- How they voted: `search_votes`, `vote_detail`, `voting_profile`.
- Status of a policy: `policies`, then `agenda`, `search_votes`.
- Counts: `corpus_stats`, `parliament_stats`, `actor_stats`, `coverage`.
- Evidence breadth or country gaps: `document_coverage`, then page `search_documents` under the same filters.
- A quote: `search_chunks`, `actor_statements`, `talking_point_statements`.

## Method
1. Start with the source built for the question.
2. Answer.
3. Before stating an absence, a "nothing changed", a ranking or a count, rest it on the source built for that, and state its scope and date. Where a curated artefact is older than the period asked about, fill the gap from the nightly sources and say so. Compare numbers only on the same scope.
<!-- /data-guide -->

Parliament tools take `chamber` (`"ep"` for the European Parliament, `"an"` for the
Assemblée nationale, `"senat"`; omit it for the campaign's first chamber). A campaign
without parliament access in `list_my_projects` is refused by them: say so instead of retrying.

## Answering

- `parliament_overview`, `voting_profile`, `actor_profile`,
  `actor_statements`, `talking_points`, `talking_point_statements` and
  `top_voices` show
  an interactive panel with the detail. Summarise
  what matters in a few sentences; do not repeat the panel's lists.
- Cite a document by its title and outlet (and date when useful), with its
  link (`url` / `source_url`). Cite an amendment by its number and official
  link, a vote by its `ballot_url`. Never cite or show internal identifiers
  (document hashes, chunk ids, argument ids, actor ids, `cite_urn`) to the
  user.
- Quote word for word, in the source's language; put a translation after it
  in parentheses, never inside the quote marks, and mark a cut with […].
- Never decide a question falls outside EAP's sources before looking: an
  actor or institution outside Europe can still be in them.
- Quote statements verbatim from the passage the tool returned.
- Never present "thin" or "no record" as neutral or as zero, nor an absence
  as silence: no statement where nothing was collected is "not collected".
- Say a voice is "credited in X% of the side's documents" (shares do not add
  up to 100%), and describe categories in plain words, never as scores.
- Do not state more than the tools returned. If a list was truncated or a
  search found nothing, say so.
- Answer in the user's language, whatever the language of the documents.

## Feedback

- When a tool errors, returns something that looks wrong, cannot answer the
  question directly, or you see a concrete improvement (a filter, field or
  view that would have answered better), report it at once with
  `send_feedback(kind="agent")`: name the tool, what you tried and what it
  should offer, nothing personal. Then tell the user in one line. At most one
  note per distinct idea per conversation.
- When the user voices a wish or a complaint about EAP, offer to send it
  with `send_feedback(kind="user")`, and send it only once they agree.
- When the USER says one of EAP's assessments is wrong or right (an
  amendment's direction, a member's category, a statement's relevance),
  record it with `rate_assessment`, their reason as `comment`. Only on the
  user's word, never on your own judgement.
