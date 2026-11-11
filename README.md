# otwarteprawo: wtyczka do Claude / a Claude plugin

[Polski](#polski) · [English](#english)

## Polski

Wtyczka łączy Claude z serwerem MCP serwisu [otwarteprawo.pl](https://www.otwarteprawo.pl):
tekst polskich aktów prawnych w brzmieniu na wybrany dzień, z odnośnikiem do PDF-u Dziennika
Ustaw lub Monitora Polskiego, jedynego tekstu urzędowego. Zawiera trzy umiejętności (skills):
badanie przepisu po adresie i dacie, sprawdzenie cytowań w projekcie pisma na dzień oraz
ustalenie, jakie brzmienie obowiązywało w dniu zdarzenia.

### Instalacja

W Claude Code:

```
/plugin marketplace add otwarteprawo-pl/otwarteprawo.pl
/plugin install otwarteprawo@otwarteprawo
```

W claude.ai: Customize › Plugins › Add › Add marketplace › Add from a repository, wpisz
`otwarteprawo-pl/otwarteprawo.pl`, a potem Add przy wtyczce Otwarte Prawo. Na karcie Connectors
wtyczki kliknij Connect przy `otwarteprawo`.

### Logowanie

Serwer loguje przez OAuth. Przy pierwszym połączeniu Claude otwiera w przeglądarce stronę
logowania otwarteprawo.pl. Podaj adres e-mail i otwórz link, który wyśle otwarteprawo.pl, w tej
samej przeglądarce, w której zaczęło się logowanie. Claude otrzymuje wtedy klucz API wydany
Tobie i wysyła go wyłącznie do `https://api.otwarteprawo.pl/mcp`. To repozytorium nie zawiera
żadnego klucza, a wtyczka o żaden nie prosi.

Aby zalogować się ponownie, na przykład po unieważnieniu klucza, użyj ustawień łączników
(connectors) w kliencie (`/mcp` w Claude Code).

Jeśli zainstalowano wersję 0.1, która prosiła o klucz, wtyczka nie używa już wpisanego wtedy
klucza. Logowanie wydaje nowy; poprzedni pozostaje ważny, dopóki go nie unieważnisz.

### Co daje Claude

Dwa narzędzia serwera:

- `get_provision`: jeden przepis aktu w brzmieniu na dany dzień albo powód, dla którego tekstu
  nie ma (akt jest nieznany, nie jest prawem w tym dniu, dzień wykracza poza zakres
  odtworzonego tekstu itd.);
- `search_acts`: przepisy znalezione po cytowaniu (`art. 299 ksh`), po adresie publikacyjnym
  aktu (`Dz.U. 2016 poz. 1444`) albo po treści.

Trzy umiejętności, które mówią Claude, jak z nich korzystać:

- `research`: co stanowi przepis w danym dniu, zacytowany tak, jak zwraca go narzędzie, z
  identyfikatorem jednostki (eId), PDF-em dziennika urzędowego oraz informacją, jak tekst
  odtworzono i zweryfikowano;
- `citation-check`: każde cytowanie w projekcie sprawdzone z tekstem obowiązującym w danym
  dniu;
- `applicable-text`: brzmienie, które obowiązywało w dniu zdarzenia, i to, czy zmieniło się od
  tego czasu.

Każda umiejętność podaje brak tekstu jako brak. Gdy serwis nie ma tekstu na dany dzień, Claude
mówi o tym wprost i nie uzupełnia luki.

### Tajemnica zawodowa

Aby wyszukiwać po znaczeniu, `search_acts` przekazuje treść każdego zapytania zewnętrznej
usłudze wyszukiwania semantycznego, działającej w UE. Każda umiejętność nakazuje Claude nie umieszczać w zapytaniach faktów dotyczących
klienta i wyszukiwać wyłącznie po pojęciu prawnym, cytowaniu lub adresie aktu. `get_provision`
wysyła tylko adres aktu, eId przepisu i datę. Zob.
[informację o przetwarzaniu danych](https://www.otwarteprawo.pl/prywatnosc).

### Stan

To repozytorium jest publiczne. Wtyczka jest przekazywana bezpośrednio i nie figuruje w żadnym
katalogu. Claude Code loguje się przy połączeniu. claude.ai loguje się po kliknięciu Connect
na karcie Connectors wtyczki. Claude Desktop korzysta z tego samego konta co claude.ai,
więc wtyczka i jej połączenie są tam od razu. To samo dotyczy dodatku Claude for Microsoft
365 w Wordzie (sprawdzone w Wordzie w przeglądarce): wtyczka i jej łącznik są dostępne pod
przyciskiem +. Cowork nie został jeszcze sprawdzony z tą wersją.

### Licencja

MIT, zob. [LICENSE](LICENSE).

## English

A plugin that connects Claude to the MCP server of [otwarteprawo.pl](https://www.otwarteprawo.pl):
the text of Polish law acts as it read on a given date, each fragment linked to the PDF of the
journal that promulgated it, Dziennik Ustaw or Monitor Polski, the only legally authoritative
text. It carries three skills: research by an act's address and a date, a citation check of a
draft as of a date, and the text that applied on the date of an event.

### Install

In Claude Code:

```
/plugin marketplace add otwarteprawo-pl/otwarteprawo.pl
/plugin install otwarteprawo@otwarteprawo
```

In claude.ai: Customize › Plugins › Add › Add marketplace › Add from a repository, enter
`otwarteprawo-pl/otwarteprawo.pl`, then Add on the Otwarte Prawo plugin. On the plugin's
Connectors tab, click Connect beside `otwarteprawo`.

### Sign in

The server signs you in through OAuth. When Claude first connects, it opens otwarteprawo.pl's
sign-in page in your browser. Enter your e-mail address and open the link otwarteprawo.pl sends
you, in the browser where the sign-in began. Claude then holds an API key issued to you, and
sends it only to `https://api.otwarteprawo.pl/mcp`. This repository holds no key, and the
plugin asks for none.

To sign in again, for instance after revoking a key, use the client's connector settings
(`/mcp` in Claude Code).

If you installed version 0.1, which asked for a key, the key you entered is no longer used by
the plugin. Signing in issues a new one; the earlier key stays valid until you revoke it.

### What it gives Claude

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

### Client confidentiality

`search_acts` sends each query's words to an embedding service in the EU to search by meaning.
Each skill tells Claude to keep client facts out of queries and to search by legal concept,
citation or address only. `get_provision` sends only an act's address, a provision's eId and a
date. See the [privacy notice](https://www.otwarteprawo.pl/prywatnosc).

### Status

This repository is public. The plugin is handed out directly and is not listed in any
directory. Claude Code signs in when it connects. claude.ai signs in when you click Connect on
the plugin's Connectors tab. Claude Desktop shares the claude.ai account, so the plugin and
its connection are already there. The same holds for the Claude for Microsoft 365 add-in in
Word (tried in Word on the web): the plugin and its connector are under the + button. Cowork
is not yet tried with this version.

### License

MIT, see [LICENSE](LICENSE).
