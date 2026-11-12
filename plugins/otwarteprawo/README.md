# Otwarte Prawo

**PL.** Wtyczka łączy Claude z serwerem MCP serwisu [otwarteprawo.pl](https://www.otwarteprawo.pl):
tekst polskich aktów prawnych w brzmieniu na wybrany dzień, z linkiem do PDF-u Dziennika Ustaw
lub Monitora Polskiego, jedynego tekstu urzędowego. Zawiera trzy umiejętności: badanie przepisu
po adresie i dacie, sprawdzenie cytowań w projekcie pisma na dzień oraz ustalenie, jakie
brzmienie obowiązywało w dniu zdarzenia. Logowanie odbywa się adresem e-mail potwierdzanym przez
otwarteprawo.pl.

**EN.** The plugin connects Claude to otwarteprawo.pl's MCP server: the text of Polish law acts
as it read on a given date, each fragment linked to the PDF of the journal that promulgated it,
Dziennik Ustaw or Monitor Polski, the only legally authoritative text. It carries three skills:
research by an act's address and a date, a citation check of a draft as of a date, and the text
that applied on the date of an event. Sign-in is by an e-mail address otwarteprawo.pl confirms.

In Claude Code:

```
/plugin marketplace add otwarteprawo-pl/otwarteprawo.pl
/plugin install otwarteprawo@otwarteprawo
```

It runs nothing locally and fetches nothing but `https://api.otwarteprawo.pl/mcp`. claude.ai,
sign-in, client confidentiality and status: see the [repository README](../../README.md).
Privacy: <https://www.otwarteprawo.pl/prywatnosc>. License: MIT.
