# otwarteprawo: a Claude plugin

**PL.** Wtyczka do Claude, która łączy Claude z serwerem MCP serwisu
[otwarteprawo.pl](https://www.otwarteprawo.pl): tekst polskich aktów prawnych w brzmieniu na
wybrany dzień, z linkiem do PDF-u Dziennika Ustaw lub Monitora Polskiego, jedynego tekstu
urzędowego. Zawiera trzy umiejętności: badanie przepisu po adresie i dacie, sprawdzenie
cytowań w projekcie pisma na dzień oraz ustalenie, jakie brzmienie obowiązywało w dniu
zdarzenia. Logowanie odbywa się adresem e-mail potwierdzanym przez otwarteprawo.pl.

**EN.** A Claude plugin that connects Claude to otwarteprawo.pl's MCP server: the text of
Polish law acts as it read on a given date, each fragment linked to the PDF of the journal that
promulgated it, Dziennik Ustaw or Monitor Polski, the only legally authoritative text.

## Install

In Claude Code:

```
/plugin marketplace add otwarteprawo-pl/claude-plugins
/plugin install otwarteprawo@otwarteprawo
```

## Sign in

The server signs you in through OAuth. When Claude first connects, it opens otwarteprawo.pl's
sign-in page in your browser. Enter your e-mail address and open the link otwarteprawo.pl sends
you, in the browser where the sign-in began. Claude then holds an API key issued to you, and
sends it only to `https://api.otwarteprawo.pl/mcp`. This repository holds no key, and the
plugin asks for none.

To sign in again, for instance after revoking a key, use the client's connector settings
(`/mcp` in Claude Code).

If you installed version 0.1, which asked for a key, the key you entered is no longer used by
the plugin. Signing in issues a new one; the earlier key stays valid until you revoke it.

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
directory. Claude Code signs in when it connects. Cowork, claude.ai and Claude Desktop are not
yet tried with this version.
