---
name: research
description: Research Polish law by an act's address and a date with otwarteprawo.pl. Quote a provision as it read on that date, citing its eId, the Dziennik Ustaw or Monitor Polski PDF and how the text was verified. Use when the user asks what a Polish ustawa, kodeks or rozporządzenie says, now or on a date, e.g. "co mówi art. 299 ksh", "brzmienie art. 113 ust. 1 ustawy o VAT na dzień 1 stycznia 2020", "stan prawny na dzień", "jaki przepis reguluje", "Dz.U. 2016 poz. 1444".
allowed-tools: mcp__plugin_otwarteprawo_otwarteprawo__get_provision mcp__plugin_otwarteprawo_otwarteprawo__search_acts
---

# Research a provision by address and date

Answer a question about Polish law from the text the otwarteprawo.pl server returns, as it read
on a date, and cite every fragment so the user can check it against the official journal.

Answer in the user's language. Legal terms stay in Polish where translation would lose
precision (ustawa, ustęp, tekst jednolity, Dziennik Ustaw).

## Steps

1. **Fix the date.** Use the date the user names, as `YYYY-MM-DD`. If they name none, use today
   and say so, and pass the date explicitly in every call.
2. **Find the provision.**
   - With a citation (`art. 5 ust. 1 ustawy o …`, `§ 3 rozporządzenia …`), call `search_acts`
     with the citation. The cited unit comes first and is marked as the cited provision; take
     its `eli` and `eid`.
   - With an act's address (`Dz.U. 2016 poz. 1444`, `DU/2016/1444`), `search_acts` returns the
     act. Build the eId from the table below, or search for the unit by citation.
   - With a topic and no citation, call `search_acts` with the legal concept in Polish, under
     the client-facts rule below. Search covers acts in force today, and its hits are
     candidates, not answers. Read each one you rely on with `get_provision` on the date.
3. **Read it.** Call `get_provision` with `eli`, `eid` and `date`. Read a unit at the level the
   question needs: a whole article when its ustępy are read together, one ustęp or punkt when
   one is cited.
4. **Answer.**
   - Quote the fragment as the tool returned it.
   - Give the citation block below for it.
   - Report every caveat the answer carries.
   - Then give your reading, marked as your reading.

   Where the answer is an absence, report it (see below) and stop there for that provision.

## Quote only what the tool returned

- Provision text comes only from a `get_provision` answer whose `answer` is `text`. Its
  `fragment` is Akoma Ntoso XML: quote the text inside its elements word for word, in document
  order, with each unit's `num` and without the markup.
- Never write a provision's wording from memory, from another source, or by completing a
  fragment. This holds most of all when the tool returns no text.
- Your analysis is welcome, kept apart from the quoted text and labelled as your reading. Never
  present it as the act's words.

## Cite every fragment

For each fragment you quote, give:

- the act's title and its journal address (`act.display_address`);
- the eId (`eid`), and the unit's `num` and `heading` where the answer has them;
- the date asked for, and the version's interval and link (`version.valid_from`,
  `version.valid_to`, `version.url`);
- the official journal PDF (`act.du_pdf_url`). It is the only legally authoritative text, so
  say that;
- how the text was derived and verified, in the answer's own words and what they mean:
  - `derivation`:
    - `structured-source`: read from the Sejm's ELI HTML;
    - `inferred-from-pdf`: a model's reading of the printed page;
    - `re-ocr`: a machine transcript of the page images.
  - `verification`:
    - `editor-checked`: a person compared this text with the journal PDF;
    - `cross-checked`: an independent instrument compared it with another source and did not
      object. That is not a guarantee it is correct;
    - `converted`: produced by the pipeline and passed its own checks, with no independent
      check. Most of the corpus sits here;
    - `flagged`: an independent check objected, and the text is published anyway.

  Below `editor-checked`, tell the user to check the wording against the journal PDF before
  relying on it.

Report what the answer adds beside the text:

- `currency`: the text predates an amending act in force by the date. Name the act; whether it
  amends this provision is not stated;
- `not_reconstructed`: units whose wording is not reconstructed;
- `judgments`: Trybunał Konstytucyjny rulings on the provision;
- `dictionary_forms`: word forms the service generated;
- `unverified_parts`;
- `checked_against`: the tekst jednolity it was compared with.

## Report an absence as an absence

When `answer` is not `text`, tell the user which answer came back and what it means, and give
the links it carries: the journal PDF, the last version served, the newest tekst jednolity. Do
not fill the gap.

- `unknown-act`: no act is published at this address, either because it is outside the
  service's corpus or because the address names nothing.
- `no-text`: the act is published without its text (`withheld_reason` says why).
- `outside-validity`: the act is not law on that date (`validity` gives its interval and what
  ended it).
- `past-horizon`: the service reconstructs text only up to `horizon`, and the date lies on or
  past it.
- `not-in-force`: no version the service serves is in force on that date; `currency` counts the
  amending acts since.
- `withdrawn`: that version is no longer served.
- `not-reconstructed`: the unit keeps its number and heading, but its wording on that date is
  not reconstructed.
- `unknown-provision`: the act has no unit with that eId in that version. Check the eId by
  searching for the citation.

For now, many acts have no reconstructed text at today's date. That is the service's answer,
not an error: report it as it stands. You may offer to read the provision as of an earlier date
the service covers, saying plainly that it is not today's text.

## Keep client facts out of queries

`search_acts` sends each query's words to an embedding service (Scaleway, in the EU) to search
by meaning. A lawyer's question can carry tajemnica zawodowa (tajemnica adwokacka, tajemnica
radcowska).

- Query by legal concept, citation or address only, e.g. `odpowiedzialność członków zarządu za
  zobowiązania spółki`, `art. 299 ksh`, `DU/2016/1444`.
- Never put a name, a company, an amount, a place, a date of the matter, or any other fact of
  the case into a query. Restate the question in its abstract legal form first.
- If it cannot be asked without client facts, ask the user for the citation instead.
- `get_provision` sends only an act's address, an eId and a date.

## Addresses

- `eli` is the act's journal position: `DU/<year>/<position>` for Dziennik Ustaw,
  `MP/<year>/<position>` for Monitor Polski. `Dz.U. 2016 poz. 1444` is `DU/2016/1444`, and
  `Dz.U. 2004 nr 54 poz. 535` is `DU/2004/535`.
- A tekst jednolity is announced at an address of its own. The act keeps its original
  address, so find the act through `search_acts`.
- `eid` shapes:
  - `art_5`: artykuł. Articles stay bare, even inside a chapter;
  - `art_5__para_1`: ustęp;
  - `art_5__para_1__pnt_2`: punkt;
  - `art_5__para_1__pnt_2__lett_a`: litera;
  - `par_3`: § of a rozporządzenie;
  - `art_299__par_1`: § inside an article, as in a kodeks;
  - `att_<n>`: an annex. `<n>` counts positions, not the annex's own number, so confirm the
    annex by the `heading` the answer returns.

  When unsure of an eId, get it from `search_acts` by citation rather than guess.
- Search covers acts in force today, so an act no longer in force is not found by citation.
  Read it with `get_provision` at its journal address, as the user gives it or as you ask them
  for it. An address you know yourself may be tried, and is confirmed only by the title the
  answer returns.
- `date` is `YYYY-MM-DD`.

## When a call fails

- "Sign in to otwarteprawo.pl to use this tool.", or "The API key was refused": the user is
  not signed in, or their key was revoked. They sign in again from the client's connector
  settings (`/mcp` in Claude Code), with the e-mail address otwarteprawo.pl confirms.
- A quota message: say when it resets, as the message gives it.
- The search index is unavailable: `get_provision` still answers for a known `eli` and `eid`.
