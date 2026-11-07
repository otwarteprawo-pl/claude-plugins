---
name: citation-check
description: Check every citation of Polish law in a draft (pismo, opinia, umowa, wniosek) against the text in force on a date, with otwarteprawo.pl. Report, per citation, whether the act is law on that date, whether the unit exists, whether a quotation matches the text, and the Dziennik Ustaw or Monitor Polski PDF to check against. Use for requests like "sprawdź cytowania w projekcie", "czy te przepisy są aktualne na dzień", "zweryfikuj podstawę prawną", "check the citations in this draft".
allowed-tools: mcp__plugin_otwarteprawo_otwarteprawo__get_provision mcp__plugin_otwarteprawo_otwarteprawo__search_acts
---

# Check a draft's citations as of a date

Check each citation of a Polish act in the user's draft against the text the otwarteprawo.pl
server returns for a date. Report what the tool shows for each one, including where it shows
nothing.

Answer in the user's language. Legal terms stay in Polish where translation would lose
precision (ustawa, ustęp, tekst jednolity, Dziennik Ustaw).

## Steps

1. **Fix the date the draft is checked as of.** Use the date the user names, such as the day
   the pismo is filed or signed, as `YYYY-MM-DD`. If they name none, ask. If the check is at
   today, warn that many acts have no reconstructed text at today yet, so some citations may
   come back not checkable.
2. **List the citations before calling any tool.** Take every citation of a Polish act from the
   draft: the unit (`art. 5 ust. 1 pkt 2`, `§ 3`) and the act as written (its name, date and
   journal address if given). Show the list to the user.
3. **Send only the citations.** The draft's text, its parties and its facts never go to a tool.
   A query is the citation alone (`art. 5 ust. 1 ustawy o …`, `Dz.U. 2016 poz. 1444`).
4. **Resolve each citation.** Call `search_acts` with it. The cited unit comes first and is
   marked as the cited provision; take its `eli` and `eid`. If the draft gives the journal
   address, the eId table below builds the eId directly.
5. **Read each one on the date.** Call `get_provision` with `eli`, `eid` and `date`.
6. **Check each one.**
   - The act is law on the date: the answer is not `outside-validity`.
   - The unit exists in that version: the answer is not `unknown-provision`.
   - The journal address in the draft is the act's (`act.display_address`). If the draft cites
     a tekst jednolity and an answer names a newer one, say so.
   - Where the draft quotes the provision, compare the quotation word for word with the
     fragment, and show each difference.
   - Where the draft relies on the provision for a proposition, say whether its text supports
     it. That is your reading, so mark it as such.
   - Report the caveats the answer carries: an amending act not yet applied (`currency`),
     Trybunał Konstytucyjny rulings (`judgments`), units not reconstructed
     (`not_reconstructed`).
7. **Report.** First a table, one row per citation:

   | # | Cited as | `eli` `eid` | Answer on the date | Finding | Verification | Journal PDF |
   |---|---|---|---|---|---|---|

   - **Finding** is one of:
     - `matches`: the tool returned text, and the act, the unit, the address and any
       quotation agree with it;
     - `problem`, with what is wrong;
     - `not checkable`, with the absence named.
   - Never write `matches` for a citation the tool returned no text for.

   Below the table, quote the fragment for each row with a problem, with its citation block.

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
- the date, and the version's interval and link (`version.valid_from`, `version.valid_to`,
  `version.url`);
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
  the draft goes out.

## Report an absence as an absence

When `answer` is not `text`, the row says which answer came back and what it means, with the
links it carries. Do not fill the gap.

- `unknown-act`: no act is published at this address, either because it is outside the
  service's corpus or because the address names nothing. This is not proof the citation is
  wrong.
- `no-text`: the act is published without its text (`withheld_reason` says why).
- `outside-validity`: the act is not law on that date (`validity` gives its interval and what
  ended it). This is a finding about the citation.
- `past-horizon`: the service reconstructs text only up to `horizon`, and the date lies on or
  past it.
- `not-in-force`: no version the service serves is in force on that date.
- `withdrawn`: that version is no longer served.
- `not-reconstructed`: the unit keeps its number and heading, but its wording on that date is
  not reconstructed.
- `unknown-provision`: the act has no unit with that eId in that version. Check the eId by
  searching for the citation before reporting it as a problem.

## Keep client facts out of queries

`search_acts` sends each query's words to an embedding service (Scaleway, in the EU) to search
by meaning. A draft carries client matters under tajemnica zawodowa (tajemnica adwokacka,
tajemnica radcowska).

- Query by citation or address only.
- Never put a name, a company, an amount, a place, a date of the matter, a sentence of the
  draft, or any other fact of the case into a query.
- If a citation cannot be resolved without context from the draft, ask the user rather than
  send that context.
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
  Read it with `get_provision` at its journal address, as the draft gives it or as you ask the
  user for it. An address you know yourself may be tried, and is confirmed only by the title
  the answer returns.
- `date` is `YYYY-MM-DD`.

## When a call fails

- "The API key was refused": the user's key is missing or wrong. They set it under `/plugin` →
  otwarteprawo → Configure options, and get one at https://www.otwarteprawo.pl/klucz.
- A quota message: say when it resets, as the message gives it, and which citations are still
  unchecked.
- The search index is unavailable: `get_provision` still answers for a known `eli` and `eid`.
