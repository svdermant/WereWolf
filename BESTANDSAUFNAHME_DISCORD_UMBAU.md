# Bestandsaufnahme: Umbau des Lykos-WereWolf-Bots zu einem eigenständigen Discord-Bot

- **Status:** technische Bestandsaufnahme, noch kein Umbau
- **Prüfgrundlage:** bereitgestelltes Archiv `WereWolf-main.zip`, Archivstand/Commit `486755a20f9eae215ca877986f84d452da68b486`
- **Zielplattform:** ausschließlich Discord
- **Nicht-Ziel:** IRC-Unterstützung, IRC-Netzwerke oder ein dauerhaft dualer IRC-/Discord-Betrieb
- **Wichtig:** Diese Datei dokumentiert Empfehlungen und Befunde. Der Quellcode wurde für diese Bestandsaufnahme nicht fachlich umgeschrieben.

## 1. Zielbild und Bewertungsregeln

Der vorhandene Bot ist die fachliche Ausgangsbasis für ein neues Discord-Spielsystem. Die vorhandene Rollen- und Spielmechanik soll möglichst erhalten bleiben; IRC-spezifische Infrastruktur wird dagegen entfernt und durch Discord-Funktionen ersetzt. Der spätere Bot soll über eine Einladung auf beliebigen Discord-Servern installiert werden können. Konfiguration, Berechtigungen und aktive Spiele müssen dabei serverübergreifend voneinander getrennt sein.

Die drei Kategorien bedeuten:

1. **ÜBERNEHMEN** – fachliche Funktion oder Komponente ist grundsätzlich plattformneutral und soll erhalten bleiben; Anpassungen und Tests können trotzdem nötig sein.
2. **FÜR DISCORD UMBAUEN** – Zweck bleibt bestehen, aber Schnittstelle, Datenmodell oder technische Umsetzung muss geändert werden.
3. **ENTFERNEN** – Funktion ist ausschließlich für IRC oder das alte Betriebsmodell erforderlich und gehört nicht in das Discord-Zielsystem. Relevante Spielfunktionen, die aktuell damit vermischt sind, müssen zuvor ersetzt werden.

## 2. Zusammenfassung

Der Quellcode enthält einen umfangreichen und wiederverwendbaren Spielkern, ist aber nicht sauber von IRC getrennt. Die IRC-Abhängigkeit ist nicht nur auf den Programmeinstieg beschränkt: Benutzerobjekte, Nachrichtenkontext, Kanäle, Befehlsverarbeitung, Spielphasen, Rollenaktionen und Übergänge greifen an vielen Stellen auf IRC-Konzepte zurück.

**Empfehlung:** Kein pauschales Neuschreiben aller Rollen, aber auch kein einfacher Austausch von `IRCClient` gegen eine Discord-Bibliothek. Zuerst die Plattformgrenzen sichtbar machen, dann die fachlichen Kernkomponenten von Discord trennen und schließlich einen Discord-Adapter mit mehreren voneinander isolierten Server-Spielinstanzen aufbauen.

Im untersuchten Bestand wurden zuvor statisch gezählt: 155 Python-Dateien unter `src/`, 64 Python-Dateien direkt unter `src/roles/`, 24 direkt unter `src/gamemodes/`, 509 Dekorator-Vorkommen `@event_listener(...)` in 90 Dateien und 131 `@command(...)`-Vorkommen in 46 Dateien. Das sind Quelltextzählungen, keine Anzahl unabhängig geprüfter Features oder Fehler.

## 3. Kategorie A – ÜBERNEHMEN

### A1. Fachliche Rollen und Spielmechaniken

**Betroffene Bereiche:** `src/roles/`, `src/status/`, große Teile von `src/gamemodes/`

Rollenmodule enthalten die konkreten Spielregeln, Nachtaktionen, Informationsfähigkeiten, Schutzmechaniken, Statuswirkungen und Reaktionen auf Todesfälle. Diese Regeln sind nicht grundsätzlich an IRC gebunden. Die Module sollen daher einzeln übernommen werden, nachdem ihre Plattformabhängigkeiten identifiziert wurden.

Beispiele für wiederverwendbare fachliche Logik:
- Rollenaktionen und ihre Bedingungen, etwa Seher, Orakel, Ermittler, Schutzrollen und Angriffsrollen.
- Rollen- und Teamzuordnungen, Status-Effekte, Schutz, Verzauberung, Flüche und Rollenwechsel.
- Siegbedingungen und die Auswertung konkurrierender Teams.
- Ereignisreaktionen, die die Spielmechanik verändern.

**Einschränkung:** Viele Rollen senden direkt über `player.send(...)`, lesen IRC-bezogene Identitätsdaten oder greifen auf `channels.Main` zu. Solche Stellen sind nicht ohne Anpassung übernehmbar. Die Regel selbst soll erhalten bleiben; der Nachrichtenversand und Plattformzugriff werden ersetzt.

### A2. Ereignissystem

**Datei:** `src/events.py`

Das Event-System mit `Event`, `EventListener`, Prioritäten und dem Registrieren von Listenern ist ein wertvoller Kernbestandteil. Es ermöglicht Rollen, Spielmodi und Statusmodule, auf Spielereignisse zu reagieren, ohne jede Regel zentral in eine einzelne Datei zu schreiben.

**Vorgehen:** Grundstruktur übernehmen; Ereignisnamen, Reihenfolge, Fehlerbehandlung und Nebenwirkungen mit Tests absichern. Für die spätere Erzählung können bestätigte Spielereignisse als Grundlage für narrative Ereignisse dienen, ohne die Spielregeln an die Textausgabe zu koppeln.

### A3. Rollen- und Spielmodus-Inventar

**Bereiche:** `src/roles/`, `src/gamemodes/`

Die Rollen- und Modusdateien sind ein umfangreicher Fundus für die Funktionsübernahme. Die Modusdefinitionen sollen nicht blind unverändert kopiert werden: Abhängigkeiten zu Kanälen, Zeitlimits, Spielerzahlen und speziellen IRC-Ausgaben müssen geprüft werden. Das Inventar selbst und die fachlichen Regeln sind zu bewahren.

### A4. Nachrichten- und Übersetzungssystem als fachliche Grundlage

**Bereich:** `src/messages/`, Nachrichtendateien wie `messages/en.json`

Das eigene Nachrichten-Template-System mit Lexer, Parser und Formatter ist eine größere vorhandene Funktionalität und sollte nicht vorschnell durch einzelne fest codierte Discord-Texte ersetzt werden.

**Vorgehen:** Template- und Formatierungslogik übernehmen, danach Discord-spezifische Ausgabe, Embeds, Längenlimits, Erwähnungen und private Nachrichten anpassen. Die bestehende Grammatik muss auf Discord-Kompatibilität getestet werden.

### A5. SQLite-Datenbank und Migrationen

**Bereich:** `src/db/`, insbesondere SQL-Schema und Upgrade-Dateien

SQLite und das Migrationskonzept sind nicht IRC-spezifisch. Bestehende Statistik-, Warnungs-, Spieler- und Konfigurationsdatenmodelle können als Referenz dienen.

**Einschränkung:** Historische Konten, Nicknames, Hostmasken, IRC-Zugriffsflags und kanalbezogene Annahmen passen nicht automatisch zu Discord-IDs und mehreren Guilds. Schema und Abfragen müssen vor Übernahme klassifiziert werden. Für Discord sollte die Discord-User-ID die stabile Benutzeridentität sein; Guild-bezogene Einstellungen und Daten müssen eine eindeutige Guild-Zuordnung erhalten.

### A6. Hilfsfunktionen und Datenstrukturen

**Bereiche:** `src/functions.py`, `src/containers.py`, `src/match.py`, `src/random.py`, Teile von `src/cats.py`

Allgemeine Auswahl-, Container-, Zufalls- und Match-Funktionen können grundsätzlich weiterverwendet werden, sobald geprüft ist, dass sie keine IRC-Identitäts- oder Channel-Annahmen enthalten. `src/match.py` und die vorhandenen Container sind außerdem Kandidaten für gezielte Kompatibilitätstests.

### A7. Credits und Lizenzhinweise

**Dateien:** `README.md`, `LICENSE` und Copyright-Kopfzeilen im Quellcode

Die vorhandenen Mitwirkenden- und Community-Credits sowie die ursprünglichen Lizenz- und Copyright-Hinweise müssen erhalten bleiben. Das Projekt nennt unter anderem jcao219 als ursprünglichen Programmierer, moonmoon als Hauptentwickler, Iciloo für Tests und würdigt weitere Code-Beitragende, Bug-Reporter, Bot-Betreiber, Spielende sowie LaneAtomic für das Nachrichtensystem.

Die Lizenzdatei ist bei Übernahme und Veröffentlichung beizubehalten. Vor einer Veröffentlichung sind die konkreten Lizenzbedingungen der vorhandenen Dateien und eingebundenen Komponenten weiterhin zu beachten.

## 4. Kategorie B – FÜR DISCORD UMBAUEN

### B1. Programmeinstieg und Konfiguration

**Dateien:** `wolfbot.py`, `src/__init__.py`, `src/config.py`, `src/defaultsettings.yml`, `botconfig.example.yml`

`wolfbot.py` verlangt derzeit ausdrücklich `transports[0].type == "irc"`, liest IRC-Server-, Port-, Nick-, Ident-, SASL-, Services- und Flood-Einstellungen ein und startet `oyoyo.client.IRCClient.mainLoop()`.

**Umbau:** neuen Discord-Einstiegspunkt mit einer unterstützten Discord-Bibliothek schaffen. Bot-Token und Intents sicher konfigurieren; Secrets nicht in Git einchecken. Konfiguration nach globalen Einstellungen und Guild-spezifischen Einstellungen trennen. `src/__init__.py` initialisiert viele globale Module in einer festen Reihenfolge; diese Initialisierung ist für Tests und mehrere Spielinstanzen zu überprüfen.

### B2. Benutzeridentität und Benutzerobjekte

**Datei:** `src/users.py`

Die bestehende Nutzerverwaltung arbeitet mit Nick, Ident, Host, IRC-Account, Ghost-/Reconnect-Zuständen und `src.transport.irc.get_services`. Discord verwendet stabile User-IDs, Guild-Mitgliedschaften, Rollen und Berechtigungen; Nicknames können sich ändern und sind nicht eindeutig.

**Umbau:** Benutzeridentität auf Discord-IDs ausrichten. Spielteilnahme und Spielerstatus müssen pro Spielinstanz geführt werden, nicht in einem globalen Nickname-Register. Discord-User, Guild-Member und internen Spielspieler als unterschiedliche Konzepte modellieren. Wiederverbindungs-/Reconnect-Verhalten nur dort übernehmen, wo es für Discord tatsächlich erforderlich ist.

### B3. Kontext, private Nachrichten und Ausgabe

**Datei:** `src/context.py`

`IRCContext` importiert `IRCClient`, versendet `PRIVMSG`/`NOTICE`, berechnet die IRC-512-Byte-Grenze und führt WHO-/Serverfunktionen aus.

**Umbau:** einen Discord-Ausgabedienst einführen, der öffentliche Nachrichten, private DMs, Antworten, Threads und gegebenenfalls Embeds abbildet. IRC-Zeilenlängen, WHO, NOTICE und Protokollformatierung entfernen. Discord-Limits, Berechtigungsfehler, Rate Limits, nicht zustellbare DMs und erlaubte Erwähnungen explizit behandeln.

### B4. Nachrichten-Dispatcher

**Datei:** `src/dispatcher.py`

`MessageDispatcher` ist aktuell ausdrücklich für rohe IRC-Nachrichten beschrieben und hängt an `users.User`, `channels.Channel`, `BotUser` und `.send()`.

**Umbau:** die fachliche Entscheidung „Antwort an Spielkanal oder privat“ als plattformneutrale Absicht ausdrücken und anschließend über den Discord-Ausgabedienst ausführen. Die aktuelle Annahme, dass `users.Bot` bzw. `channels.Main` das Ziel festlegt, reicht für mehrere Discord-Server und mehrere Spiele nicht aus.

### B5. Kanalmodell und Spielräume

**Datei:** `src/channels.py`

Die Datei modelliert IRC-Kanäle, Channel-Modes, JOIN/PART/KICK, Voice-/Statuspräfixe und IRC-spezifische Mitgliedschaftszustände.

**Umbau:** das IRC-Channel-Modell nicht 1:1 in Discord nachbauen. Stattdessen Guild, konfigurierten Spielkanal, optionalen Wolfchat-/Teamkanal und interne Spielinstanz getrennt modellieren. Discord-Rollen, Channel-Permissions, private Threads oder private Kanäle sind mögliche Mittel, aber die Entscheidung muss anhand der gewünschten Privatsphäre, Rechteverwaltung und Spielgröße getroffen werden.

### B6. Eingehende Befehle und Autorisierung

**Dateien:** `src/handler.py`, `src/decorators.py`, `src/lineparse.py`, `src/gamecmds.py`, `src/gamejoin.py`

Die vorhandenen Dekoratoren enthalten wertvolle Regeln für Phase, Rolle, Spielstatus, Kanal und Berechtigungen. Der Eingang selbst ist jedoch IRC-basiert: Handler erhalten rohe IRC-Nachrichten und verwenden Nick/Channel-Kontexte.

**Umbau:** Discord-Slash-Commands, Context-Menüs oder gegebenenfalls Nachrichtenbefehle als Eingabeschicht hinzufügen. Die vorhandenen Befehlsmetadaten und Prüfregeln möglichst bewahren, aber in Discord-Interaktionen übersetzen. Interaktionsantworten müssen innerhalb der Discord-Fristen bestätigt oder zurückgestellt werden. Befehle müssen immer eindeutig einer Guild und einer aktiven Spielinstanz zugeordnet werden.

### B7. Spielkern und Phasenübergänge

**Dateien:** `src/wolfgame.py`, `src/gamestate.py`, `src/trans.py`, `src/votes.py`, `src/pregame.py`

Hier liegt ein großer Teil des eigentlichen Spielflusses. Er ist nicht vollständig plattformneutral: `src/wolfgame.py` greift häufig auf `channels.Main` zu; `src/trans.py` importiert `get_ircd`, setzt Channel-Modes und verwendet IRC-Ausgaben; Abstimmungen und Phasenwechsel senden ebenfalls direkt in den Hauptkanal.

**Umbau:** Regeln und Zustandsübergänge erhalten, aber Ausgaben, Sichtbarkeit, Abstimmungsoberfläche und Rechteänderungen in explizite Plattformaktionen umwandeln. Ein globaler `channels.Main`-Zugriff muss durch den Kontext der jeweiligen Spielinstanz ersetzt werden. Für mehrere Server darf eine Phase oder Abstimmung in Spiel A niemals Spiel B beeinflussen.

### B8. Zeitsteuerung und asynchrone Verarbeitung

**Dateien:** `src/gamestate.py`, `src/gamejoin.py`, `src/pregame.py`, `src/roles/timelord.py`, `src/trans.py`, `src/handler.py`

Der Quellcode verwendet `threading.Timer` für Phasenlimits, Warnungen, Beitrittsfristen und Server-Pings. Ein Discord-Bot arbeitet typischerweise mit `asyncio`; Thread-Timer können dadurch mit Zustandsänderungen, Event-Loops und gleichzeitig laufenden Spielen kollidieren.

**Umbau:** Timer in einen zentralen, testbaren asynchronen Scheduler überführen oder durch eine klar gekapselte Zeitsteuerung ersetzen. Veraltete Timer müssen nach Phasenwechseln und Spielende wirkungslos bleiben. Vorhandene ID-/Generation-Prüfungen für Phasenwechsel sind zu erhalten und zu testen. IRC-Server-Ping-Timer werden entfernt.

### B9. Event- und Rollenlogik mit Plattform-Nebenwirkungen

**Bereiche:** `src/roles/*.py`, `src/status/*.py`, `src/gamemodes/*.py`

Die Dekorator-/Eventstruktur soll bleiben, aber Listener mit direktem `channels.Main`, `player.send`, `.mode()`, `.who()` oder IRC-spezifischen Benutzerfeldern müssen einzeln angepasst werden. Diese Kopplung erklärt, warum eine rein mechanische Ersetzung des Netzwerkclients nicht ausreicht.

**Umbau:** Plattformaktionen an wenigen klaren Grenzen bündeln, etwa `send_public`, `send_private`, `set_player_permissions`, `start_timer` und `resolve_game_context`. Rollen sollen eine Spielaktion auslösen können, ohne rohe Discord- oder IRC-API-Aufrufe zu enthalten.

### B10. Mehrserverbetrieb und Isolation

**Betroffene Bereiche:** globaler Zustand in `src/channels.py`, `src/users.py`, `src/gamestate.py`, `src/trans.py` und weiteren Modulen

Die alte Architektur nimmt einen zentralen Hauptkanal (`channels.Main`) und umfangreiche globale Register an. Das ist für einen öffentlich installierbaren Bot mit mehreren Guilds und mehreren gleichzeitigen Partien nicht ausreichend.

**Umbau:** mindestens folgende Schlüssel sauber trennen:
- Discord-Guild-ID,
- Spielinstanz-ID,
- Discord-User-ID,
- konfigurierte Kanäle und Rollen je Guild,
- laufender Spielzustand und Timer je Spielinstanz.

Keine Guild darf Rollen, Abstimmungen, private Teamkommunikation, Konfiguration oder Spielzustand einer anderen Guild lesen oder verändern können. Mehrere Spiele pro Guild sollten entweder von Anfang an unterstützt oder bewusst als erste Produktgrenze dokumentiert werden.

### B11. Berechtigungen und Adminfunktionen

**Dateien:** `src/gamecmds.py`, `src/decorators.py`, `src/db/__init__.py`, Konfiguration

Die bisherigen IRC-Flags, Nick-/Account-Zuordnungen, ChanOp-Annahmen und `WHO`-Prüfungen können nicht direkt auf Discord übertragen werden.

**Umbau:** Discord-Berechtigungen und Guild-spezifische Adminrollen/-konfiguration einführen. Globale Bot-Administration und lokale Serveradministration müssen getrennt sein. Gefährliche Adminaktionen müssen serverseitig geprüft werden, nicht nur über die sichtbare Befehlsoberfläche.

### B12. Datenbankidentität und Mehrserver-Schema

**Bereich:** `src/db/`

**Umbau:** alte Statistik- und Warnungsdaten nur nach Prüfung migrieren. Für serverbezogene Einstellungen und Spielstatistiken Guild-IDs und gegebenenfalls Spielinstanz-IDs ergänzen. Globale personenbezogene Präferenzen und Guild-lokale Berechtigungen nicht vermischen. Migrationen benötigen Tests mit leerer Datenbank und mit einer vorhandenen Datenbank.

### B13. Tests und Testinitialisierung

**Bereich:** `test/`, `src/__init__.py`, `src/config.py`, `src/messages/message_lexer.py`

Beim zuvor ausgeführten `python -m pytest -q` unter Python 3.13 scheiterte die Sammlung unter anderem an `from typing.io import TextIO` in `src/messages/message_lexer.py` sowie an wiederholter Konfigurationsinitialisierung (`config.Main.load_metadata` mit Assertion, dass `_metadata` noch `None` sei). Das beweist nicht, dass die Spiellogik defekt ist, verhindert aber eine zuverlässige Testbasis.

**Umbau:** zuerst Test-Bootstrap und Python-Kompatibilität gezielt reparieren. Tests sollen nicht auf eine echte Discord-Verbindung angewiesen sein. Danach Unit-Tests für Rollenregeln, Ereignisreihenfolge, Siegbedingungen, Timer, parallele Guilds und Berechtigungsgrenzen ergänzen.

### B14. Nachrichten, Erzählung und Discord-UX

**Bereiche:** `src/messages/`, `src/trans.py`, `src/wolfgame.py`, Rollenmodule

**Umbau:** vorhandene Nachrichtenvorlagen weiterverwenden, aber Ausgabe an Discord anpassen. Die erzählerische Ebene sollte nicht allein auf vorformulierten Erfolgsmeldungen basieren. Besser ist eine strukturierte Beschreibung bestätigter Spielereignisse (Aktion, Ziel, Ergebnis, Schutz/Tod, Informationssichtbarkeit), aus der sowohl die Spielausgabe als auch die narrative Darstellung erzeugt werden.

## 5. Kategorie C – ENTFERNEN

Die folgenden Komponenten gehören nicht in den Zielbetrieb. Vor dem Löschen ist sicherzustellen, dass keine benötigte Spielfunktion ausschließlich über sie umgesetzt wurde.

### C1. IRC-Programmeinstieg und Clientbibliothek

- `wolfbot.py` in seiner jetzigen IRC-Form ersetzen; die IRC-spezifische Startlogik entfernen.
- `oyoyo/` als IRC-Clientbibliothek entfernen, sobald kein verbleibender Import mehr davon abhängt.
- IRC-Verbindungsaufbau, IRC-Server-/Port-/SSL-/SASL- und NickServ-Services-Konfiguration entfernen.

### C2. IRC-Transportvarianten

- `src/transport/irc/` und IRCd-/Services-Sonderfälle entfernen, einschließlich `get_ircd()` und `get_services()`, nachdem alle Verbraucher umgebaut sind.
- Transport-spezifische Konfigurationsoptionen wie Server-Ping, IRC-Flood-Token-Bucket und netzwerkabhängige Protokollmerkmale entfernen.

### C3. IRC-Protokollhandler und Hooks

- `src/hooks.py` in seiner heutigen Funktion entfernen, soweit die Listener WHO/WHOX, PING/PONG, CAP, NICK, QUIT, MODE und weitere IRC-Protokollereignisse behandeln.
- Entsprechende Teile von `src/handler.py` entfernen, darunter IRC-Verbindungsaufbau, Nick-/Services-Authentifizierung und Protokollereignisse.
- Kein IRC-Protokollparsing, keine IRC-Rohbefehle und keine IRC-512-Byte-Nachrichtenberechnung im Discord-Zielsystem.

### C4. IRC-spezifische Benutzer- und Channel-Mechanik

Nach Migration der erforderlichen Fachlogik entfernen:
- `IRCContext` und WHO-/NOTICE-/PRIVMSG-spezifische Sendewege aus `src/context.py`.
- IRC-Channel-Modes, JOIN/PART/KICK, Prefix-/Voice-Verwaltung und netzwerkspezifische Channel-Zustände aus `src/channels.py`.
- Ident-/Hostmask-/NickServ-/IRCd-spezifische Identitätslogik in `src/users.py`.

Die Dateien als Ganzes sollten erst gelöscht werden, wenn die benötigten plattformneutralen Funktionen bereits an anderer Stelle umgesetzt und getestet sind.

### C5. Veraltete IRC-Dokumentation und Konfigurationsbeispiele

README-Abschnitte über QuakeNet, IRC-Netzwerke, IRC-Channelbetrieb, NickServ/ChanServ, `#lykos`, `botconfig.yml`-Transportparameter und das alte Startverfahren ersetzen. Historische Angaben können in einer klar gekennzeichneten Migrations-/Geschichtsnotiz erhalten bleiben, sollten aber nicht als Anleitung für den neuen Discord-Bot erscheinen.

### C6. IRC-spezifische Status- und Debugausgaben

Alle reinen IRC-Transportdiagnosen, serverabhängigen Sonderfälle, WHO-basierte Prüfungen und IRC-Mode-Wiederherstellungsroutinen entfernen, sobald ihre fachliche Notwendigkeit ausgeschlossen oder durch Discord-Funktionen ersetzt ist.

## 6. Querschnittsbereiche – weder pauschal übernehmen noch pauschal löschen

| Bereich | Entscheidung | Grund |
|---|---|---|
| `src/__init__.py` | Umbauen | Initialisierungsreihenfolge und globale Registrierungen sind kritisch für Tests und mehrere Spielinstanzen. |
| `src/config.py` | Umbauen | Konfigurationsparser kann teilweise nützlich sein; Schema und globale Annahmen müssen für Discord/Guilds neu bewertet werden. |
| `src/logger.py`, `src/debug/` | Übernehmen/umbauen | Logging und Debug-Hilfen sind nützlich; IRC- und globale Zustandsannahmen prüfen. |
| `src/reaper.py`, `src/warnings.py`, `src/relay.py` | Einzelfallprüfung | Je nach Funktion können Moderation, Inaktivität oder Relay IRC-spezifisch oder weiterhin fachlich nützlich sein. Nicht allein anhand des Dateinamens löschen. |
| `src/game_stats.py` | Übernehmen/umbauen | Spielstatistiken sind nützlich, benötigen aber Discord-IDs und klare Guild-Zuordnung. |
| `src/locations.py` | Einzelfallprüfung | Prüfen, ob die Daten fachliche Spielinhalte oder IRC-Netzwerkdetails enthalten. |
| `src/messages/` | Übernehmen/umbauen | Template-Engine behalten, Ausgabeformate und Discord-Limits anpassen. |
| `src/gamemodes/` | Übernehmen/umbauen | Modusregeln bewahren; alle direkten Channel-/Transportzugriffe pro Modus bereinigen. |

## 7. Empfohlene Reihenfolge der Umsetzung

1. **Quellstand einfrieren und dokumentieren.** Originalarchiv, Commit-ID, Lizenz und Credits erhalten. Umbau in einem separaten Git-Branch oder neuen Repository durchführen.
2. **Abhängigkeiten kartieren.** Direkte Imports und Aufrufe von `IRCClient`, `IRCContext`, `channels.Main`, `.mode()`, `.who()`, `get_ircd()` und `get_services()` nachverfolgen. Diese Liste muss nach jedem Umbau kleiner werden.
3. **Testbasis reparieren.** Python-Kompatibilität und globale Konfigurationsinitialisierung stabilisieren; erste Regressionstests für die dokumentierten Rollen und Siegbedingungen erstellen.
4. **Plattformneutrale Spielinstanz definieren.** Jede Partie bekommt ihren eigenen Zustand, Spielerbestand, Kanalbezug, Timer und Konfiguration. Globale Zustände, die Partien miteinander verbinden könnten, schrittweise beseitigen.
5. **Discord-Einstieg und Interaktionsadapter bauen.** Token, Intents, Slash-Commands, Fehlerbehandlung und Guild-Kontext integrieren.
6. **Nachrichten- und Berechtigungsdienst implementieren.** Öffentliche Ausgabe, private Rollenaktionen und Teamkommunikation über Discord absichern.
7. **Phasen und Timer migrieren.** Asynchrone Zeitsteuerung und Schutz vor veralteten Callback-Aufrufen testen.
8. **Rollen und Spielmodi schrittweise aktivieren.** Je Rolle/Modus fachliche Regressionstests ausführen und direkte IRC-Nebenwirkungen beseitigen.
9. **Mehrserver-Isolation testen.** Mindestens zwei Test-Guilds parallel simulieren; Spielzustände, Einstellungen, Teamnachrichten und Berechtigungen dürfen sich nicht vermischen.
10. **Erzählung integrieren.** Erst auf Basis verlässlicher Spielereignisse; Darstellung darf keine falschen Spielresultate erzeugen.
11. **Dokumentation und Installation erneuern.** Discord-Einladungs-/OAuth2-Setup, benötigte Berechtigungen, sichere Konfiguration, Datenschutz und Betreiberanleitung dokumentieren.

## 8. Akzeptanzkriterien für einen ersten nutzbaren Discord-Bot

- Der Bot startet ohne IRC-Konfiguration, IRC-Server oder IRC-Bibliothek.
- Nutzer können über Discord-Befehle einem Spiel beitreten und Rollenaktionen ausführen.
- Öffentliche Spielausgaben und private Rolleninformationen gehen an die richtigen Empfänger.
- Spielphasen, Abstimmungen, Todesfälle und Siegbedingungen verhalten sich gemäß den dokumentierten Regeln.
- Timer werden bei Spielende oder Phasenwechsel zuverlässig invalidiert.
- Eine Guild kann den Bot unabhängig konfigurieren; andere Guilds sehen oder verändern ihre Spielzustände nicht.
- Fehlende Discord-Berechtigungen und nicht zustellbare private Nachrichten führen zu kontrollierten Fehlern statt zu inkonsistenten Spielen.
- Regressionstests laufen ohne echte Discord-Verbindung.
- Credits und Lizenzhinweise bleiben erhalten.

## 9. Grenzen dieser Bestandsaufnahme

Diese Datei ist eine strukturierte statische Bestandsaufnahme und ein Umbauplan, keine vollständige formale Prüfung jeder einzelnen Funktion und kein Nachweis, dass alle Rollen mit Discord bereits kompatibel sind. Die genannten Datei- und Modulbereiche wurden anhand des Quellcodes und der vorhandenen Abhängigkeiten eingeordnet; vor dem Entfernen einer Komponente sind sämtliche Imports und indirekten Aufrufpfade erneut zu prüfen.

Es wurden keine Spielregeln absichtlich verändert und keine Discord-Integration implementiert. Die nächste technische Aufgabe sollte die Schnittstellenliste mit konkreten Funktionsaufrufen und Abhängigkeiten sein, gefolgt von der Stabilisierung der Testumgebung.

## 10. Credits und Herkunft

Die Originaldokumentation nennt jcao219 als ursprünglichen Programmierer, moonmoon als Hauptentwickler, Iciloo für Tests und dankt allen weiteren Mitwirkenden, Bug-Reportern, Bot-Betreibern und Spielenden sowie LaneAtomic für das Nachrichtensystem. Diese Historie ist Bestandteil des Projekts und soll beim Umbau transparent erhalten bleiben.
