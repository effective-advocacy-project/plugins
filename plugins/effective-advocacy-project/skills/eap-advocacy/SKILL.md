---
name: eap-advocacy
description: Answer questions about the user's Effective Advocacy Project (EAP) campaigns with the EAP tools - which actors matter, what an organisation or person has said, and how MEPs, deputies and senators vote, with amendments and roll calls. Use whenever the user asks about their advocacy campaign, its actors or statements, or parliamentary votes, positions, groups or amendments on it.
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
- `list_my_projects` also says what each grant reaches. If a campaign has no
  parliament access, the parliament tools refuse it: say so plainly instead
  of retrying.
- Every tool takes an optional `intent`: one sentence about the user's goal
  (never their words, never names of private people).

## Which tool for which question

**Parliament** (`chamber` is `"ep"` for the European Parliament, `"an"` for
the Assemblée nationale or `"senat"`; omit it for the campaign's first chamber —
a campaign shows only some, and refuses the others):

- Who stands where on the campaign: `parliament_overview`. It returns counts
  per category and per political group. To get NAMES, pass `group` (its
  short label as the counts print it, e.g. `RN`, `EPR`, `SOC`; for MEPs `PPE`, `S&D`, `Renew`) and/or `tier`
  (a category spelled exactly as the tool lists it, e.g. `Persuadable`).
  "Which RN deputies are persuadable?" is one call.
- One member's record: `voting_profile` with `member` (a name is fine).

**Executive** (`jurisdiction` is `"EU"` for the Commission, `"FR"` or `"GB"`):

- What the government did, said, promised and whom it met on the campaign,
  and each minister, Commissioner, ministry or DG with a record:
  `executive_overview`. Doing and saying are two readings, never added
  together; "thin" and "no record" are not neutral and not zero.
- One person's or department's record: `executive_profile` with `actor` (the
  actor id from the overview, or a name with `jurisdiction`). Acts of the
  departments under a minister's portfolio are context, not their own acts.
- Amendments: `search_amendments`. Put the topic in `query` (free text,
  ranked by meaning and by words, any language), and narrow with filters:
  `direction` (`pro` / `anti` for the campaign), `group`, `author`,
  `outcome`, `dossier`, dates, `min_cosigners`. Without a query, `sort` by
  `date` or `cosigners`. An amendment carried by a `rapporteur` or
  `committee` is not that deputy's own position.
- Roll calls: `search_votes`, same idea (`query`, `direction`, `outcome`,
  `dossier`, dates, `max_margin`; `sort` by `date` or `closest`). Then
  `vote_detail` with a `vote_id` for the split by group and the members who
  voted against their group's majority.

Describe categories in plain words, never as scores.

**Actors and what they said:**

- Who an organisation or person is in the campaign: `actor_profile`.
- What they have said, in their own words: `actor_statements` (filter with
  `q` for words every passage must contain, or `register`: parliament,
  government, lobbying, press, social, other; page with `cursor`).
- To resolve an ambiguous name first: `find_actor`.

**What is being said** (where the grant includes talking points):

- The most present arguments for and against the campaign over a period:
  `talking_points` (`period` 30d, 3m — the default — 12m or all; `side` pro
  or anti). For "what is rising", compare each point's share of documents
  with its share in the previous period (`previous_documents` /
  `previous_documents_total`), not raw counts: the corpus grows. Do not
  draw or describe a trend line from `series`: its periods may have been
  read by different readers, so a change across them can be the reader's.
- `language` is the documents' language, not a country: say so when you
  compare by language.
- The statements behind one: `talking_point_statements` with its id or label.
- Who carries each side (people and organisations, by period, country,
  language, actor category, person / organisation): `top_voices`. Say
  "credited in X% of the side's documents" (shares do not add up to 100%),
  and report `coverage` (arguments) and `confidence` with the names; a
  `suppressed` side is too thin to rank, which is not "nobody". To quote a voice:
  `talking_point_statements` with its `actor_id`, cited by document.

**Open questions** about the campaign's documents (news, statements,
speeches): `search_chunks` with a plain-language `query`; it reaches
documents in other languages too. If the reply carries a `notice`, the
results are word matches only: retry with terms in the documents' own
language. Use `get_chunk` to read a passage in full.

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
- Quote statements verbatim from the passage the tool returned.
- Never present "thin" or "no record" as neutral or as zero, nor an absence
  as silence: check `coverage` before saying nobody spoke; no statement
  where nothing was collected is "not collected".
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
