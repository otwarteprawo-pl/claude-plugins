# otwarteprawo: a Claude Code plugin

**PL.** Wtyczka do Claude Code, która łączy Claude z serwerem MCP serwisu
[otwarteprawo.pl](https://www.otwarteprawo.pl): tekst polskich aktów prawnych w brzmieniu na
wybrany dzień, z linkiem do PDF-u Dziennika Ustaw lub Monitora Polskiego, jedynego tekstu
urzędowego. Zawiera trzy umiejętności: badanie przepisu po adresie i dacie, sprawdzenie
cytowań w projekcie pisma na dzień oraz ustalenie, jakie brzmienie obowiązywało w dniu
zdarzenia. Klucz API wydaje [otwarteprawo.pl/klucz](https://www.otwarteprawo.pl/klucz).

**EN.** A Claude Code plugin that connects Claude to otwarteprawo.pl's MCP server: the text of
Polish law acts as it read on a given date, each fragment linked to the PDF of the journal that
promulgated it, Dziennik Ustaw or Monitor Polski, the only legally authoritative text.

## Install

In Claude Code:

```
/plugin marketplace add otwarteprawo-pl/claude-plugins
/plugin install otwarteprawo@otwarteprawo
```

When the plugin is enabled, Claude Code asks for your API key. Get one at
<https://www.otwarteprawo.pl/klucz>. The key is kept in your system's secure storage and is
sent only to `https://api.otwarteprawo.pl/mcp`, as the `Authorization` header. This
repository holds no key.

## What it gives Claude

The server's two tools:

- `get_provision`: one provision of an act as it read on a date, or the reason there is no
  text (the act is unknown, not law on that date, past the date the text is reconstructed to,
  and so on);
- `search_acts`: provisions found by a citation (`art. 299 ksh`), an act's address
  (`Dz.U. 2016 poz. 1444`) or what they say.

Three skills that tell Claude how to use them:

- `research`: what a provision says on a date, quoted as the tool returns it, citing its eId,
  the journal's PDF and how the text was derived and verified;
- `citation-check`: every citation in a draft checked against the text in force on a date;
- `applicable-text`: the text that applied on the date of an event, and whether it has
  changed since.

Every skill reports an absence as an absence. Where the service has no text for a date, Claude
says so and does not fill the gap.

## Client confidentiality

`search_acts` sends each query's words to an embedding service in the EU to search by meaning.
Each skill tells Claude to keep client facts out of queries and to search by legal concept,
citation or address only. `get_provision` sends only an act's address, a provision's eId and a
date. See the [privacy notice](https://www.otwarteprawo.pl/prywatnosc).

## Status

This repository is public. The plugin is handed out directly and is not listed in any
directory. It is built for Claude Code. Cowork loads its skills but not its server, because it
does not ask for the key.
