# Schnittstellenliste: Umbau des WereWolf-Bots von IRC auf Discord

- **Status:** technische Schnittstellen- und Aufgabenliste, noch keine Implementierung
- **Prüfgrundlage:** `WereWolf-main.zip`, Archiv-/Commitstand `486755a20f9eae215ca877986f84d452da68b486`
- **Ziel:** eigenständiger Discord-Bot, installierbar auf mehreren Discord-Servern
- **Nicht-Ziel:** IRC-Betrieb, paralleler IRC-/Discord-Betrieb oder Erhalt des alten IRC-Protokolladapters
- **Leitlinie:** Spielregeln möglichst bewahren; Plattformgrenzen explizit machen; IRC-Komponenten erst löschen, wenn alle fachlichen Verbraucher migriert und getestet sind.

> Die Funktionsnamen und Dateiangaben unten wurden anhand des Quellcodes ermittelt. Die Liste ist eine konkrete Arbeitsgrundlage, aber keine Behauptung, dass jede indirekte Abhängigkeit bereits vollständig aufgelöst wurde. Vor jedem Löschschritt muss eine aktuelle Import-/Aufrufsuche erfolgen.

## Prioritäten

| Priorität | Bedeutung |
|---|---|
| **P0 – Fundament** | Muss vor einer verlässlichen Discord-Integration geklärt werden; betrifft Isolation, Zustandsmodell oder Testbarkeit. |
| **P1 – erster spielbarer Bot** | Benötigt für Start, Befehle, Ausgabe, private Rollenaktionen und Phasen. |
| **P2 – Funktionsparität** | Weitere Rollen, Modi, Adminfunktionen, Datenmigration und Betriebsqualität. |
| **P3 – Bereinigung/Komfort** | Reine Altlasten, Dokumentation, optionale Oberflächen und spätere narrative Erweiterungen. |

## Ziel-Schnittstellen (Vorschlag)

Diese Schnittstellen sind Zielentwurf, keine bereits vorhandenen Funktionen im Quellcode:

- `GameContext`: `guild_id`, `game_id`, `channel_id`, optional `thread_id`; keine global implizite Hauptpartie.
- `PlayerIdentity`: stabile `discord_user_id`; Anzeigename und Guild-Mitgliedschaft sind veränderliche Metadaten, nicht die Identität.
- `GameOutput`: `send_public(...)`, `send_private(...)`, `reply_interaction(...)`, `send_team(...)`.
- `PermissionService`: prüft Guild-Konfiguration, Discord-Berechtigungen und spielinterne Adminrechte.
- `GameScheduler`: asynchrone, abbrechbare Timer mit `game_id` und Phasen-Generation.
- `GameRepository`: persistiert Guild-Konfiguration und bei Bedarf Partien/Statistiken mit eindeutigen Guild-/Game-Schlüsseln.
- `PlatformActions`: gezielte Plattformoperationen wie Spielerzugriff auf einen privaten Kanal oder Berechtigungsänderung; Rollenmodule rufen keine Discord-API direkt auf.
- `NarrativeEvent`: optionale Darstellung bestätigter Spielereignisse; darf keine Regelentscheidung auslösen.

---

# 1. P0 – Fundament: Zustand, Tests und Plattformgrenze

## 1.1 `src/__init__.py` – Modulinitialisierung und Registrierung

**Aktuelle Rolle:** initialisiert Konfiguration und Module; Importreihenfolge ist wichtig, damit Rollen, Spielmodi, Listener und Befehle registriert werden.

**Zu prüfen/ändern:**
- Initialisierung von globalen Konfigurationsobjekten von Modulimporten entkoppeln.
- Wiederholte Initialisierung muss in Tests kontrolliert möglich sein oder explizit verhindert werden.
- Rollen-/Modus-/Listenerregistrierung einmalig und nachvollziehbar machen.
- Keine implizite Annahme, dass genau ein globaler Bot, Kanal oder Spielzustand existiert.

**Priorität:** P0  
**Abnahmekriterium:** Tests können Module ohne echte Discord-Verbindung laden; zwei unabhängige Spielkontexte teilen keine veränderlichen Spielzustände.

## 1.2 `src/config.py`, `src/defaultsettings.yml`, `botconfig.example.yml`

**Aktuelle Schnittstellen:** `init()`, `Config`, `merge(...)`; die alte Konfiguration enthält IRC-Transportpfade.

**IRC-Kopplung:** Konfigurationspfade wie `transports[0].type`, `transports[0].server_ping`, IRC-Server-/Nick-/Services-Einstellungen und transportbezogene User-Optionen.

**Umbau:**
- `discord`-Konfiguration für Token/Intents und Betriebsoptionen definieren; Token ausschließlich über Umgebungsvariable oder Secret-Management.
- Guild-Konfiguration getrennt von globalen Bot-Einstellungen modellieren.
- Alte IRC-Einstellungen nicht stillschweigend weiterreichen.
- Konfigurationsschema versionieren und Validierungsfehler verständlich ausgeben.

**Priorität:** P0/P1  
**Abnahmekriterium:** Bot startet ohne IRC-Parameter; ungültige Guild-Konfiguration kann eine andere Guild nicht beeinflussen.

## 1.3 `src/gamestate.py` – `PregameState`, `GameState`, `set_gamemode(...)`

**Aktuelle Kopplung:** `set_gamemode(...)` sendet Fehlermeldungen über `channels.Main`; `GameState` übernimmt Spieler und Modus aus `PregameState`; Timer und Rollenstatus hängen an Spielzustand.

**Umbau:**
- `guild_id`, `game_id`, `channel_id` bzw. `GameContext` an jeder Partie festhalten.
- `set_gamemode(...)` darf keine globale `channels.Main` voraussetzen; Fehler als Ergebnis/Event zurückgeben oder über einen expliziten Ausgabekontext melden.
- Timer- und Phasenidentität an die Spielinstanz binden.
- Globale Klassenregistrierungen für Rolleninitialisierung auf unbeabsichtigte Mehrfachausführung prüfen.

**Priorität:** P0  
**Abnahmekriterium:** Zwei Partien können parallel unterschiedliche Modi, Spieler und Phasen haben; ein Modusfehler wird nur im zugehörigen Kontext gemeldet.

## 1.4 `src/events.py` – `EventListener`, `event_listener`, `Event`

**Aktuelle Rolle:** Registrierung und Dispatch von Events mit Prioritäten und steuerbarem Ablauf.

**Umbau:** Grundmechanik übernehmen. Event-Payloads auf implizite globale Zustände prüfen. Für späteres Logging/Narration Ereignisse nur nach bestätigter Regelentscheidung ausgeben; reine Textausgabe darf die Spielmechanik nicht ändern.

**Priorität:** P0/P2  
**Abnahmekriterium:** Prioritäten, `prevent_default`/Stop-Verhalten und Listenerreihenfolge sind durch Tests abgesichert.

## 1.5 Testbasis: `test/`, `src/messages/message_lexer.py`, `src/config.py`

**Festgestellte Blocker im bisherigen Testlauf unter Python 3.13:**
- `src/messages/message_lexer.py` importiert `TextIO` aus `typing.io`, was in der getesteten Umgebung fehlschlug.
- Tests kollidierten mit mehrfacher Konfigurationsinitialisierung in `config.Main.load_metadata`.
- Diese Sammelfehler sind kein Beweis für defekte Spielregeln, verhindern aber verlässliche Regressionstests.

**Umbau:**
- Unterstützte Python-Version festlegen und CI-Umgebung pinnen.
- Test-Setup isoliert Konfiguration und globale Registrierungen.
- Unit-Tests ohne Netzwerk für Rollen, Siegbedingungen, Ereignisse, Abstimmungen, Phasen und Timer erstellen.

**Priorität:** P0  
**Abnahmekriterium:** `pytest` sammelt und führt Tests reproduzierbar aus, ohne IRC-Server oder Discord-Token.

---

# 2. P1 – Discord-Einstieg, Eingaben und Ausgaben

## 2.1 `wolfbot.py` – `main()`

**Aktuelle Schnittstelle:** `main()` verlangt `transports[0].type == "irc"`, erstellt `oyoyo.client.IRCClient` und startet `cli.mainLoop()`.

**IRC-Abhängigkeiten:** `IRCClient`, `TokenBucket`, IRC-Verbindungs-/Serverkonfiguration.

**Umbau:**
- Alten IRC-Einstieg nicht als Discord-Einstieg umetikettieren.
- Separaten Discord-Einstieg mit einer gepflegten Discord-Bibliothek einführen.
- Start, Login, Shutdown, Logging und Fehlerbehandlung für den Discord-Eventloop definieren.
- Bot-Token nie in Git oder in Protokolldateien speichern.

**Entfernen nach Migration:** IRC-Startpfad und `oyoyo`-Clientbibliothek.

**Priorität:** P1  
**Abnahmekriterium:** Der Bot meldet sich bei Discord an, ohne IRC-Code zu importieren.

## 2.2 `src/handler.py` – `on_privmsg(...)`, `parse_and_dispatch(...)`, `connect_callback(...)`, `ping_server(...)`

**Aktuelle Schnittstellen:**
- `on_privmsg(...)` übersetzt IRC-Nick/Kanal/Tags in `User`, `Channel` und `MessageDispatcher`.
- `parse_and_dispatch(...)` löst Befehlsnamen und Argumente auf.
- `connect_callback(...)` richtet Services, Hauptkanal, Verbindungs- und Protokollhandler ein.
- `ping_server(...)` und verschachtelte Verbindungsfunktionen behandeln IRC-Verbindung und Services.

**IRC-Abhängigkeiten:** `IRCClient`, `get_services()`, `channels.Main`, IRC-Account-Tags, NOTICE/PRIVMSG, CAP/SASL, WHO und Ping-Timer.

**Umbau:**
- `on_privmsg(...)` durch Discord-Interaktionsadapter ersetzen; Eingaben erhalten Guild-/Channel-/User-ID direkt.
- Befehlssemantik möglichst wiederverwenden, aber Parser und Slash-Command-Optionen bewusst abbilden.
- Interaction-Antworten rechtzeitig bestätigen/defer, Fehler kontrolliert zurückgeben.
- Jede Eingabe muss eindeutig einer Guild und Partie zugeordnet werden.
- `connect_callback(...)`, Services-Authentifizierung und `ping_server(...)` entfernen; Discord-Lifecycle getrennt implementieren.

**Priorität:** P1  
**Abnahmekriterium:** Slash-Command wird einer passenden Spielinstanz zugeordnet; fremde Guilds oder nicht erlaubte Kanäle können nicht versehentlich die Hauptpartie verändern.

## 2.3 `src/dispatcher.py` – `MessageDispatcher`

**Aktuelle Schnittstelle:** `__init__(source, target)`, `game_state`, `private`, `public`, `pm(...)`, `send(...)`, `reply(...)`.

**IRC-Kopplung:** Typen `users.User`, `channels.Channel`, `users.BotUser`; Ausgabe über `.send()` und Identität des globalen Bot-Ziels.

**Umbau:**
- `MessageDispatcher` in eine plattformneutrale Command-/Response-Abstraktion überführen oder durch `CommandContext` ersetzen.
- Ziel nicht länger durch `target is users.Bot` bzw. `target is channels.Main` bestimmen.
- Empfängerarten explizit ausdrücken: Interaktion, öffentlicher Spielkanal, private Nachricht, Teamkanal.
- `game_state` anhand des expliziten `GameContext` finden, nicht durch zufällige Priorität von Target/Source.

**Priorität:** P1  
**Abnahmekriterium:** Gleicher Befehl kann öffentlich oder privat verarbeitet werden, ohne IRC-Objekte zu benötigen.

## 2.4 `src/context.py` – `IRCContext`, `_who(...)`, `_send(...)`, `lower(...)`, `equals(...)`, `IRCFeatures`

**Aktuelle Rolle:** zentrale IRC-Abstraktion für Nachrichtenversand, NOTICE/PRIVMSG, WHO/WHOX, Nick-Casemapping, IRC-Features und Zeilenlänge.

**Umbau:**
- `IRCContext`, `IRCFeatures`, WHO/WHOX und IRC-Zeilenaufteilung nicht übernehmen.
- Nur nachweislich allgemeine Hilfsfunktionen separat herauslösen, falls sie tatsächlich benötigt werden.
- Discord-Ausgabe implementiert Discord-Limits, erlaubte Erwähnungen, Embeds/Threads nach Bedarf, Rate Limits und Fehler bei fehlenden Berechtigungen.
- Private Nachricht fehlgeschlagen: kontrollierter Fallback, ohne geheime Rolleninformationen öffentlich zu senden.

**Priorität:** P1  
**Abnahmekriterium:** Alle Spielnachrichten laufen über eine Discord-Ausgabeschicht; kein Aufruf erzeugt rohe IRC-Kommandos.

## 2.5 `src/users.py` – `get(...)`, `add(...)`, `users()`, `disconnected()`, `complete_match(...)`, `parse_rawnick(...)`, `User`, `FakeUser`, `BotUser`

**Aktuelle Kopplung:** Nick/Ident/Host/Account, Ghost-/Reconnect-Zustände, WHO/WHOIS, `get_services()`, `client.send("NICK", ...)`, `channels.Main.game_state`.

**Umbau:**
- Stabile Discord-User-ID als Identität verwenden.
- Discord User, Guild Member und internen Spielspieler auseinanderhalten.
- Spielzugehörigkeit pro `game_id`; Guild-Kontext pro `guild_id`.
- Nickname/Displayname nur als Anzeige- und Suchfeld verwenden, nicht als Primärschlüssel.
- `FakeUser`-Konsolenannahmen und `BotUser`-NICK-/IRC-Methoden entfernen oder durch Test-Doubles ersetzen.
- Aufrufe wie `User.game_state`, die implizit `channels.Main` verwenden, auf expliziten Spielkontext umstellen.

**Priorität:** P0/P1  
**Abnahmekriterium:** Zwei Nutzer mit gleichem Displaynamen sind eindeutig unterscheidbar; derselbe Nutzer kann in verschiedenen Guilds unabhängig an Spielen teilnehmen.

## 2.6 `src/channels.py` – `get(...)`, `add(...)`, `Channel`, `FakeChannel`

**Aktuelle Kopplung:** IRC-Channelobjekte, `JOIN`, `PART`, `KICK`, `MODE`, Channel-Prefixe, Voice-Status, IRC-Features und `Channel.game_state`.

**Umbau:**
- Datei nicht 1:1 als Discord-Channelmodell übernehmen.
- `channels.Main` durch explizite `GameContext`-/Repository-Auflösung ersetzen.
- Guild, konfigurierter Spielkanal, Teamkommunikation und Partie als getrennte Konzepte modellieren.
- Discord-Permissions, Rollen, private Threads oder private Channels gezielt wählen; benötigte Rechte dokumentieren.
- Alte `Channel.mode(...)`-Aufrufe in fachliche Aktionen übersetzen, z. B. `set_game_channel_locked(...)` oder `set_player_visibility(...)`.

**Priorität:** P0/P1  
**Abnahmekriterium:** Kein globaler Hauptkanal; eine Änderung an Spielkanal A verändert keine Rechte in Guild B.

## 2.7 `src/decorators.py` – `command`, `command.caller`, `command._thunk`, `command._caller`, `hook`

**Aktuelle Rolle:** Registrierung und Prüfung von Commands; Metadaten für `chan`, `pm`, `playing`, `silenced`, `phases`, `roles`, `flag`, `owner_only`, `in_game_only`, alternative Kanäle und Benutzer.

**IRC-/Altmodell-Kopplung:**
- `wrapper.public`, `wrapper.private`, `wrapper.target is channels.Main`.
- IRC-Account und Nickname in Command-Logs.
- `db.FLAGS[temp.account]`, `db.DENY[temp.account]`, `is_owner()`/`is_admin()` nach altem Modell.
- `update_account_data(...)` kann WHO/WHOIS bzw. Accountdaten abrufen.

**Umbau:**
- Fachliche Metadaten der Befehle möglichst erhalten.
- Discord-Registrierung für Slash Commands separat halten; vorhandene Commands intern aufrufbar machen.
- Guild-ID, Spielinstanz, Discord-User-ID und Interaktionskontext durchreichen.
- Phasen-/Rollen-/Spielerprüfung als eigenständigen Service testen.
- Adminberechtigungen mit Discord Permissions und Guild-Konfiguration verbinden; globale Betreiberrechte separat halten.
- Asynchrone Interaktion berücksichtigen und Commands nicht blockierend ausführen.

**Priorität:** P1  
**Abnahmekriterium:** Derselbe Rollenbefehl kann nur vom berechtigten Spieler, in der richtigen Phase und Partie ausgeführt werden.

## 2.8 `src/lineparse.py` und Befehlsmodule

**Aktuelle Schnittstelle:** `LineParser`, `LineParseError`, `RaiseHelp`, `WantsHelp`; Freitext-Argumente werden geparst.

**Umbau:** Parser für komplexe Rollenargumente kann nützlich bleiben. Wo Slash Commands strukturierte Optionen besser unterstützen, Argumente deklarativ definieren. Fehlerausgaben müssen als Discord-Interaction-Antwort formatiert werden.

**Priorität:** P1/P2  
**Abnahmekriterium:** Ungültige Argumente verursachen keine Teilaktionen oder inkonsistenten Spielzustände.

---

# 3. P1 – Spielablauf, Abstimmungen und Timer

## 3.1 `src/gamejoin.py` – `join(...)`, `join_player(...)`, `start(...)`, `expire_start_votes(...)`, `frole(...)`

**Aktuelle Kopplung:** `channels.Main`, `wrapper.target.mode("+m")`, Timer mit `threading.Timer`, globaler `GameState`-Aufbau.

**Umbau:**
- Join/Leave und Startlogik pro `GameContext`.
- `start(...)` darf nicht eine globale Partie starten oder den falschen Kanal sperren.
- Channel-Lock als Discord-Permission-Aktion modellieren, falls die gewünschte Spieloberfläche dies benötigt.
- Abstimmungen zum Spielstart und deren Ablauf pro Partie terminieren.
- Rolle-/Spielerzahlvalidierung als fachliche Logik behalten.

**Priorität:** P1  
**Abnahmekriterium:** Join, Start und Startabstimmung funktionieren in zwei parallel laufenden Testpartien unabhängig.

## 3.2 `src/pregame.py` – `wait(...)`, `fwait(...)`, `start_cmd(...)`, `fstart(...)`, `retract(...)`, `start(...)`, `on_reset(...)`

**Aktuelle Kopplung:** globale Hauptpartie, öffentliche IRC-Nachrichten, `Channel.mode("+m")`, `threading.Timer`.

**Umbau:** dieselben Start-/Lobby-Regeln mit explizitem Spielkanal und Discord-Ausgabe umsetzen. Lobbystatus nicht allein aus Channel-Modi ableiten. Alte Admin-/Ownerprüfungen auf Guild-Berechtigungen abbilden.

**Priorität:** P1  
**Abnahmekriterium:** Lobbyzustand, Spieler und Timer sind pro Spielinstanz isoliert.

## 3.3 `src/votes.py` – `day_vote(...)`, `abstain(...)`, `retract(...)`, `show_votes(...)`, `vote(...)`, `chk_decision(...)`

**Aktuelle Kopplung:** Ausgaben über `channels.Main.send(...)`, globaler Spielzustand und Textparser.

**Umbau:**
- Abstimmungsregeln und Entscheidungsschwellen erhalten.
- Interaktionsformen für Abstimmung festlegen (Slash Command, Buttons oder Select-Menü); Spielregeln nicht an die UI binden.
- Doppelte/ungültige Abstimmungen und Retract-Verhalten als reine Kernlogik testen.
- Sichtbarkeit von Stimmen und Abstimmungsergebnissen anhand der dokumentierten Regeln behandeln.
- Alle Meldungen an die zugehörige Partie senden.

**Priorität:** P1  
**Abnahmekriterium:** UI-Interaktion und textbasierte Tests ergeben identische Abstimmungsresultate.

## 3.4 `src/wolfgame.py` – Phasen-/Spielereignisse und Admin-/Spielbefehle

**Wichtige gefundene Funktionen:** `connect_callback(...)`, `sync_modes()`, `on_kill_players(...)`, `on_join(...)`, `account_change(...)`, `leave(...)`, `on_error(...)`, `get_help(...)`, `get_wiki_page(...)`, `vote_gamemode(...)`, `_get_gamemodes(...)`, `_call_command(...)`, `_say(...)` sowie diverse registrierte Befehle.

**Starke Kopplung:**
- `channels.Main` wird häufig direkt verwendet.
- `get_ircd()` liefert serverabhängige Quiet-/Mode-Regeln.
- `sync_modes()` spiegelt Spielerstatus über IRC-Voice-Modes.
- `on_join`, `account_change`, `leave`, Quit-/Kick-/Part-Handler erwarten IRC-Lebenszyklusereignisse.
- Adminbefehle enthalten teils Prozesssteuerung/Update-/Eval-Funktionen und brauchen eine eigenständige Sicherheitsprüfung.

**Umbau:**
- Datei in fachliche Spielaktionen, Plattformreaktionen und Betreiberwerkzeuge aufteilen.
- `sync_modes()` durch eine gezielte Discord-Sichtbarkeits-/Permission-Synchronisierung ersetzen oder vollständig entfernen, falls die Regel nicht benötigt wird.
- Discord-Mitglied verlässt Guild, verlässt Kanal oder geht offline: explizite, getrennte Fälle definieren; nicht blind mit IRC `QUIT` gleichsetzen.
- Adminbefehle prüfen; besonders Codeausführung, Prozessneustart, Git-Pull und beliebiges Senden nicht ungeprüft öffentlich anbieten.
- Alle `channels.Main`-Zugriffe auf expliziten Kontext umstellen.

**Priorität:** P0/P1  
**Abnahmekriterium:** Spielkern läuft mit einem künstlichen Output-/Permission-Adapter und ohne IRC-Channelobjekte.

## 3.5 `src/trans.py` – `hurry_up(...)`, `fnight(...)`, `fday(...)`, `begin_day(...)`, `night_timeout(...)`, `transition_day(...)`, `transition_night(...)`, `chk_nightdone(...)`, `stop_game(...)`, `chk_win(...)`, `chk_win_conditions(...)`, `reset_game(...)`, `reset(...)`

**Aktuelle Kopplung:** `get_ircd()`, `channels.Main.send(...)`, `channels.Main.mode(...)`, globale Timer-Registry `TIMERS`, `threading.Timer`.

**Umbau:**
- Phasen- und Sieglogik als Kern beibehalten.
- Nachrichtenausgabe als Ereignis/Output-Absicht an den Kontext weitergeben.
- IRC-Mode-Wiederherstellung in `reset(...)` durch Discord-spezifische Aufräumaktion ersetzen, falls erforderlich.
- `TIMERS` pro Spielinstanz verwalten; keine globalen Timer-Schlüssel, die Spiele überschreiben.
- Timeout-Callbacks müssen `game_id` und Phasen-Generation validieren.
- Siegbedingungen anhand dokumentierter Regeln mit Regressionstests sichern.

**Priorität:** P0/P1  
**Abnahmekriterium:** Phase, Timeout oder Spielende in Partie A können Partie B nicht beenden oder Nachrichten in deren Kanal senden.

## 3.6 Timer: `src/gamestate.py`, `src/gamejoin.py`, `src/pregame.py`, `src/roles/timelord.py`, `src/trans.py`, `src/handler.py`

**Aktueller Befund:** `threading.Timer` wird für Phasenlimits, Warnungen, Join-Fristen, Startabstimmungen, Zeitherr-Modifikationen und IRC-Pings eingesetzt.

**Umbau:**
- `asyncio`-basierte Scheduler-/Task-Schicht verwenden, die in den Discord-Eventloop integriert ist.
- Jeder Timer bekommt einen Spiel-/Phasenschlüssel und eine Abbruchmöglichkeit.
- Beim Reset, Rollen-/Moduswechsel und Spielende alle zugehörigen Tasks abbrechen oder invalidieren.
- IRC-Ping-Timer vollständig entfernen.
- Tests mit kontrollierter Uhr statt realem Warten ermöglichen.

**Priorität:** P0/P1  
**Abnahmekriterium:** Kein Callback kann nach einem Reset alte Zustände verändern.

---

# 4. P1/P2 – Rollen, Modi, Nachrichtensystem und Status

## 4.1 `src/roles/`, `src/status/`, `src/gamemodes/`

**Aktuelle Rolle:** konkrete Spielregeln und Event-Listener. Es gibt zahlreiche Rollen und Spielmodi; viele nutzen `player.send(...)`, `channels.Main.send(...)` oder andere Plattformobjekte.

**Arbeitsweise:**
1. Automatische Suchliste für direkte Kopplungen erstellen (`channels.Main`, `.send`, `.mode`, `.who`, `get_ircd`, `get_services`, `client.send`).
2. Treffer nach Funktion klassifizieren: Regelentscheidung, Benachrichtigung, Plattformpermission oder IRC-Protokoll.
3. Regelentscheidung im Spielkern belassen.
4. Nachricht und Plattformaktion über die Ziel-Schnittstellen routen.
5. Pro Rolle/Modus Regressionstest für Bedingungen und Resultate ergänzen.

**Beispiele bereits nachvollzogener Rollenstellen:**
- `src/roles/seer.py`: `see(...)` verarbeitet Ziel, Misdirection/Exchange und sichtbare Rollenkategorie; fachliche Prüfung erhalten, Ausgabe abstrahieren.
- `src/roles/monster.py`: `on_chk_win(...)`, `on_send_role(...)`, `on_remove_protection(...)`; Siegbedingung und Schutzmechanik testen, direkte Nachrichten-/Rollenversandkopplung abstrahieren.
- `src/roles/timelord.py`: Zeitänderung an zentralen Scheduler anbinden.

**Priorität:** P1 für Kernrollen, P2 für vollständige Rollen-/Modusparität  
**Abnahmekriterium:** Jede aktivierte Rolle hat mindestens einen Regressionstest; ein Modus kann nicht durch IRC-spezifische Nebenwirkungen abstürzen.

## 4.2 `src/messages/` – Lexer, Parser, Formatter, Listener, JSON-Vorlagen

**Wichtige Dateien:** `src/messages/__init__.py`, `_messages.py`, `formatter.py`, `lexer.py`, `message.py`, `parser.py`, `message_lexer.py`, `message_parser.py`, `messages/en.json`.

**Umbau:**
- Template-/Übersetzungslogik möglichst übernehmen.
- IRC-spezifische Formatierungsannahmen und Identitätsdarstellung prüfen.
- Discord-Nachrichtenlängen, Markdown, Embeds, Erwähnungen und private Nachrichten beachten.
- Verhindern, dass Formatierung ungewollte Pings auslöst.
- Template-Engine nicht ohne Tests durch hardcodierte Texte ersetzen.

**Priorität:** P1/P2  
**Abnahmekriterium:** bestehende Nachrichten werden korrekt formatiert; private Rolleninformationen werden nie in öffentliche Ausgabe geleitet.

## 4.3 `src/gamemodes/` – Modusregistrierung und Setup

**Umbau:** Rollenverteilung, Modusregeln und erlaubte Rollen übernehmen. Pro Modus direkte Ausgaben, Kanalannahmen, Spielergrenzen und Timer prüfen. Keine pauschale Löschung des Ordners.

**Priorität:** P2  
**Abnahmekriterium:** Jeder unterstützte Modus startet und beendet sich korrekt in isolierter Partie.

---

# 5. P2 – Datenhaltung, Moderation und Administration

## 5.1 `src/db/__init__.py` und SQL-Dateien

**Bereich:** `src/db/db.sql`, `migrate.sql`, `upgrade*.sql`, Datenbank-API in `src/db/__init__.py`.

**Übernehmen/umbauen:**
- SQLite und Migrationsprinzip grundsätzlich übernehmen.
- Primärschlüssel und Tabellen mit Nick/Account/Hostmask prüfen.
- Discord User-ID, Guild-ID und ggf. Game-ID ergänzen.
- Guild-lokale Adminflags und Konfiguration nicht als globale Nick-/Accountflags behandeln.
- Migrationen für leere Datenbank und bestehende Datenbank testen; nicht mehr benötigte IRC-Felder kontrolliert auslaufen lassen.

**Priorität:** P2  
**Abnahmekriterium:** Daten einer Guild können nur im korrekten Guild-Kontext gelesen oder verändert werden.

## 5.2 `src/gamecmds.py`

**Gefundene Schnittstellen:** `stats(...)`, `timeleft(...)`, `show_admins(...)`, `goat(...)`, `fgoat(...)` sowie zugehörige Event-Listener; `show_admins(...)` verwendet `channels.Main.who()`.

**Umbau:**
- Statistik und Zeitabfragen beibehalten und kontextgebunden ausgeben.
- `show_admins(...)` nicht per WHO ermitteln; Discord-Guild-Berechtigungen oder gespeicherte Guild-Konfiguration verwenden.
- Spaß-/Moderationsbefehle auf Spielregeln und Sicherheitswirkung prüfen.
- `channels.Main`-Vergleiche durch expliziten Kontext ersetzen.

**Priorität:** P2  
**Abnahmekriterium:** Adminliste und Stats sind pro Guild korrekt; kein IRC-WHO erforderlich.

## 5.3 `src/reaper.py`, `src/warnings.py`, `src/relay.py`, `src/game_stats.py`, `src/locations.py`

**Status:** Einzelfallprüfung; nicht pauschal entfernen.

- `reaper.py`: Inaktivität/Disconnects und Rückkehrverhalten darauf prüfen, ob es Discord-Offline-/Leave-Fälle gibt, die fachlich wirklich gleichwertig sind.
- `warnings.py`: alte Account-/Host-/Nickreferenzen auf Discord-IDs und Guild-Kontext umstellen, falls Warnungen übernommen werden.
- `relay.py`: prüfen, ob reine IRC-Relayfunktion oder noch benötigte fachliche Funktion; reines IRC-Relay entfernen.
- `game_stats.py`: Statistik erhalten, aber IDs und Guild-Isolation korrigieren.
- `locations.py`: feststellen, ob Spielinhalte oder Netzwerkdetails; danach übernehmen oder entfernen.

**Priorität:** P2/P3  
**Abnahmekriterium:** Jede Komponente hat eine dokumentierte Entscheidung samt Abhängigkeiten.

## 5.4 Betreiber-/Adminfunktionen in `src/wolfgame.py`

**Besonders zu prüfen:** `_restart_program(...)`, `restart_program(...)`, `_git_pull(...)`, `fpull(...)`, `update(...)`, `pyeval(...)`, `py(...)`, `fsend(...)`, `fsay(...)`, `fdo(...)`, `force(...)`, `rforce(...)`.

**Umbau:**
- Interne Betreiberwerkzeuge von normalen Guild-Adminbefehlen trennen.
- Beliebige Codeausführung nicht in öffentlich installierbaren Bot übernehmen, sofern kein zwingender, abgesicherter Betreiberbedarf besteht.
- Prozessneustart/Update nur im Betriebsdeployment und mit Authentifizierung erlauben.
- Beliebiges Senden nur über eng begrenzte Betreiberrechte und Audit-Logging.
- Keine IRC-Owner-/Nick-Identität als Sicherheitsgrenze verwenden.

**Priorität:** P1 vor öffentlichem Release  
**Abnahmekriterium:** Keine unautorisierte Codeausführung oder globale Botsteuerung durch Guild-Admins.

---

# 6. C – Entfernen (nach Ersatz und Nachweis)

| IRC-Bereich | Dateien/Schnittstellen | Bedingung vor Entfernung |
|---|---|---|
| IRC-Start/Client | `wolfbot.py`-IRC-Pfad, `oyoyo/`, `IRCClient`, `TokenBucket` | Discord-Einstieg funktioniert; kein Import verbleibt. |
| IRC-Handler | `src/handler.py`: `on_privmsg`, `connect_callback`, `ping_server`, CAP/SASL/Services-Funktionen | Discord-Adapter deckt alle benötigten Eingabe-/Lifecycle-Fälle ab. |
| IRC-Transport | `src/transport/irc/`, `get_ircd()`, `get_services()` | alle Verbraucher migriert; Import-/Aufrufsuche leer. |
| IRC-Kontext | `IRCContext`, `IRCFeatures`, `_who`, WHOX, NOTICE/PRIVMSG und 512-Byte-Aufteilung in `src/context.py` | Discord-Outputservice ist getestet. |
| IRC-Kanäle | JOIN/PART/KICK/MODE, Prefix-/Voice-/Ban-/Quiet-Listen in `src/channels.py` und `src/hooks.py` | Discord-Permissions und Spielkanalaktionen ersetzen benötigte Effekte. |
| IRC-Benutzerattribute | ident, hostmask, NickServ/Services, Account-WHOIS | Discord-ID-/Guild-Modell deckt Identität und Berechtigungen ab. |
| IRC-Protokollhooks | `src/hooks.py`: WHO/WHOIS, PING/PONG, CAP, NICK, ACCOUNT, JOIN/PART/KICK/QUIT, MODE | notwendige fachliche Ereignisse sind in Discord-Events übersetzt. |
| IRC-Ping und Netzwerkdiagnostik | Server-Ping-Timer und netzwerkspezifische Fehlerbehandlung | keine Discord-Funktion davon abhängig. |
| Alte IRC-Dokumentation | README-/Konfigurationsabschnitte für IRC, NickServ, Channel-Operatoren und IRC-Start | neue Discord-Installation dokumentiert; Credits/Lizenz bleiben erhalten. |

**Wichtig:** Die Dateinamen `src/handler.py`, `src/context.py`, `src/users.py` und `src/channels.py` müssen nicht zwingend vollständig verschwinden. Ihre IRC-spezifischen Implementierungen werden ersetzt; Dateien dürfen erst gelöscht werden, wenn ihre verbleibenden allgemeinen Funktionen anderswo liegen.

---

# 7. Querabhängigkeiten, die bei jeder Änderung geprüft werden müssen

Nach jedem Umbau diese Suchbegriffe erneut prüfen:

```text
channels.Main
IRCClient
IRCContext
get_ircd(
get_services(
client.send(
.mode(
.who(
threading.Timer
transports[0]
rawnick
ident
host
account
PRIVMSG
NOTICE
WHO
WHOIS
JOIN
PART
KICK
MODE
NICK
QUIT
```

Nicht jeder Treffer bedeutet automatisch, dass die Funktion entfernt werden muss: etwa `.send()` kann eine fachlich sinnvolle Nachricht senden, und `account` kann in anderem Kontext vorkommen. Jeder Treffer muss in seinem konkreten Kontext eingeordnet werden.

## Abhängigkeitskette heute

```text
IRC-Server / oyoyo
    ↓
src/handler.py
    ↓
User + Channel + IRCContext
    ↓
MessageDispatcher + decorators
    ↓
gamejoin / votes / wolfgame / roles / gamemodes
    ↓
channels.Main + IRC MODE/NOTICE/PRIVMSG
```

## Zielkette

```text
Discord Gateway / Interactions
    ↓
Discord Input Adapter
    ↓
CommandContext + GameContext (guild_id, game_id)
    ↓
plattformneutraler Command-/Spielkern
    ↓
Game Events + Rollen + Spielmodi
    ↓
GameOutput / PermissionService / GameScheduler
    ↓
Discord API
```

Der Spielkern soll weder `discord.py`-Objekte noch IRC-Objekte benötigen. Der Adapter übersetzt Discord-Ereignisse in interne Befehle und interne Ausgabeabsichten in Discord-Aktionen.

---

# 8. Umsetzungsplan mit konkreten Arbeitspaketen

## Phase 0 – Referenz und CI
1. Originalarchiv und Lizenz sichern; Entwicklung auf neuem Branch.
2. Testinitialisierung und Python-Kompatibilität reparieren.
3. Kernregeln durch Tests einfrieren: Rollenaktionen, Tod, Schutz, Abstimmung, Siegbedingungen, Nachtabschluss.
4. Automatische Quelltextsuche für IRC-Kopplungen in CI ergänzen.

## Phase 1 – Spielkontext
1. `GameContext` und eindeutige Guild-/Game-IDs einführen.
2. `GameState`/`PregameState` nicht mehr an `channels.Main` binden.
3. Spieleridentität auf Discord-ID umstellen.
4. Tests mit zwei gleichzeitigen Spielen aufsetzen.

## Phase 2 – Discord-Eingabe und -Ausgabe
1. Discord-Bot-Einstieg implementieren.
2. Slash-Command-Registrierung und Interaction-Adapter einführen.
3. `MessageDispatcher`/`CommandContext` und `GameOutput` entkoppeln.
4. Private Nachrichten, Teamkommunikation, öffentliche Ausgabe und Berechtigungsfehler absichern.

## Phase 3 – Spielfluss
1. Lobby, Beitritt und Start migrieren.
2. Abstimmungen migrieren.
3. Tag/Nacht-Übergänge und Siegprüfung migrieren.
4. Timer auf abbrechbare asynchrone Tasks umstellen.
5. Reset/Spielende und Discord-Berechtigungsbereinigung testen.

## Phase 4 – Inhalte und Parität
1. Kernrollen aktivieren und einzeln testen.
2. Weitere Rollen und Statusmodule schrittweise übernehmen.
3. Spielmodi und Sonderfälle ergänzen.
4. Nachrichten/Übersetzungen und Statistik anpassen.
5. Adminfunktionen sicher begrenzen.

## Phase 5 – Bereinigung und Release
1. Keine verbleibenden produktiven IRC-Imports oder Aufrufpfade.
2. IRC-Pakete und Altcode entfernen.
3. Discord-Einladung, benötigte Berechtigungen, Konfiguration und Datenschutz dokumentieren.
4. Mehrserver-Test mit mindestens zwei Guilds und parallelen Partien.
5. Credits und Lizenzhinweise prüfen und erhalten.

---

# 9. Definition of Done für die erste Discord-Version

- [ ] Start ohne IRC-Konfiguration und ohne IRC-Bibliotheken.
- [ ] Discord Slash Commands funktionieren mit sauberem Fehlerhandling.
- [ ] Spieleridentität beruht auf Discord-IDs.
- [ ] Lobby, Spielstart, Tag/Nacht, Abstimmung, Rollenaktionen und Spielende funktionieren.
- [ ] Private Rolleninformationen gehen ausschließlich an berechtigte Empfänger.
- [ ] Alle Zustände und Timer sind pro Spielinstanz isoliert.
- [ ] Mehrere Guilds können gleichzeitig spielen.
- [ ] Fehlende Discord-Rechte erzeugen einen kontrollierten Fehler, keinen stillen Zustandsbruch.
- [ ] Tests laufen ohne echte Discord-Verbindung.
- [ ] Rollen- und Siegbedingungen haben Regressionstests.
- [ ] Keine aktiven IRC-Imports/Protokollaufrufe verbleiben.
- [ ] Lizenz und ursprüngliche Credits sind erhalten.

## Herkunft und Credits

Die ursprüngliche Dokumentation nennt jcao219 als ursprünglichen Programmierer, moonmoon als Hauptentwickler und Iciloo für Tests; sie dankt außerdem weiteren Mitwirkenden, Bug-Reportern, Bot-Betreibern und Spielenden sowie LaneAtomic für das Nachrichtensystem. Die bestehende Lizenzdatei und Copyright-Hinweise müssen beim Umbau erhalten bleiben.

## Prüfgrenze

Diese Liste basiert auf dem genannten Quellstand und den darin erkennbaren Schnittstellen. Sie beschreibt konkrete Umbauaufgaben und verifizierte Kopplungen, ersetzt aber nicht die Prüfung jedes Rollenmoduls und jedes indirekten Aufrufpfads. Vor Löschung einer IRC-Komponente sind Import- und Aufrufsuche sowie Tests erneut auszuführen. An der Spiellogik wurde durch dieses Dokument nichts geändert.
