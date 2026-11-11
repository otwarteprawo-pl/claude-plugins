---
name: applicable-text
description: Find which text of a Polish law provision applied on the date of an event (zawarcie umowy, powstanie obowiązku podatkowego, wydanie decyzji, czyn), with otwarteprawo.pl. Quote the provision as it read on that date, show whether it has changed since, and cite the Dziennik Ustaw or Monitor Polski PDF. Use for requests like "jakie brzmienie obowiązywało w dniu zdarzenia", "stan prawny na dzień zawarcia umowy", "który przepis stosować do zdarzenia z 2019 r.", "przepisy przejściowe".
allowed-tools: mcp__plugin_otwarteprawo_otwarteprawo__get_provision mcp__plugin_otwarteprawo_otwarteprawo__search_acts
---

# The text that applied on the date of an event

Find the wording of a provision on the date an event happened, with the text the
otwarteprawo.pl server returns, and show whether that wording has changed since.

Answer in the user's language. Legal terms stay in Polish where translation would lose
precision (ustawa, ustęp, przepisy przejściowe, tekst jednolity).

## Steps

1. **Fix the event's date** as `YYYY-MM-DD`. If the user has not stated it, ask. The event's
   facts are for choosing the provisions only, and never go into a query.
2. **Find the provisions.** With a citation, call `search_acts` with it and take the cited
   unit's `eli` and `eid`. Without one, search by the legal concept alone (rule below).
3. **Read each on the event's date.** Call `get_provision` with that date.
   - Quote the fragment with its citation block.
   - Give the version's interval (`version.valid_from` to `version.valid_to`, open-ended when
     there is no end). A version spans the whole act, so a new version does not by itself
     mean this provision changed.
4. **Compare with a later date:** today, or the date the user cares about, such as the date of
   a judgment or a decision. Call `get_provision` on that date as well.
   - The same version answering both dates means the same wording.
   - Different versions: compare the two fragments and quote what changed.
   - If the later date's answer is an absence, say that the comparison cannot be made from
     the service, and give what the absence carries (the last version served, the newest
     tekst jednolity).
5. **Name the intertemporal question.** Which text governs an event is a legal question, not
   a date lookup:
   - the amending act's przepisy przejściowe may keep the old text for pending matters, or
     apply the new one;
   - lex retro non agit;
   - in criminal matters, art. 4 § 1 kk applies the law more favourable to the sprawca.

   The service shows what a provision read on a date, not which version a court will apply.
   `currency` names the amending acts in force by the date, and their przepisy przejściowe
   can be read with `get_provision`. Give your view on the intertemporal question, marked as
   your reading.

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
- `dictionary_forms`;
- `unverified_parts`;
- `checked_against`.

## Report an absence as an absence

When `answer` is not `text`, tell the user which answer came back and what it means, and give
the links it carries. Do not fill the gap.

- `unknown-act`: no act is published at this address, either because it is outside the
  service's corpus or because the address names nothing.
- `no-text`: the act is published without its text (`withheld_reason` says why).
- `outside-validity`: the act was not law on that date (`validity` gives its interval and what
  ended it). For an event, that is itself an answer: the act did not apply on that date.
- `past-horizon`: the service reconstructs text only up to `horizon`, and the date lies on or
  past it.
- `not-in-force`: no version the service serves is in force on that date; `currency` counts the
  amending acts since.
- `withdrawn`: that version is no longer served.
- `not-reconstructed`: the unit keeps its number and heading, but its wording on that date is
  not reconstructed.
- `unknown-provision`: the act has no unit with that eId in that version. The unit may have
  been added later or renumbered; check by searching for the citation.

## Keep client facts out of queries

`search_acts` sends each query's words to an embedding service (Scaleway, in the EU) to search
by meaning. An event in a client's matter is covered by tajemnica zawodowa (tajemnica
adwokacka, tajemnica radcowska).

- Query by legal concept, citation or address only.
- Never put a name, a company, an amount, a place, the event's date or any other fact of the
  case into a query. The date goes only to `get_provision`, as its `date`.
- If the question cannot be asked without client facts, ask the user for the citation instead.
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
  - `att_<n>`: an annex. `<n>` counts positions, not the annex's own number.

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
