<<<<<<< HEAD
# Lykos, the Open-Source Werewolf bot

## General information

### What does Open-Source mean?

Open-Source means that the code is available for free for everyone to view, download, use, and modify. We however have a [License][license], which requires that you include it in all derivative works and other works which include lykos code.

### What is Werewolf?

Werewolf is a popular party game, similar to and inspired by [mafia][mafia]. In Werewolf, several players take roles as either wolves or their allies, or as villagers - some with special powers - who try to figure out who the wolves are and eliminate them before it is too late. There may also be some other players who play towards their own goals, which may or may not help any side.

### Where can I play this game?

We run an instance of the bot in the [#werewolf channel][game_webchat] on Libera. Off-topic discussion happens in #werewolf-meta on the same network, and channel operators can be reached in #werewolf-ops. There are also other networks listed in the [Networks page][wikinet] of our [official wiki][wiki]. Keep in mind that, just because someone appears online, does not mean they are around or available to play.

## For players

### I've never played such a game before. How do I play?

You can join a new game with `!join`. There must be at least 6 players to be able to start a game. When the game is started, the bot will message you privately telling you your role, what command(s) you have access to as that role, and what your goal is. You may then send commands privately to the bot; for example `see person`. Some roles require you to use the command during the day, and sometimes in the channel. Make sure to pay attention to the message!

If you're a wolf or other wolf-aligned role, you probably have access to the wolfchat. To use it, simply message the bot, and your message will be relayed to all other wolfchat players. You will also get their messages.

The bot contains over 50 roles, so it can take a while to get used to all of them. Practice makes perfect!

### As a player, what commands can I use?

Lykos has a host of commands which can be used for various purposes. [You can view them on our wiki.](https://werewolf.chat/Commands)

## For bot operators

### I want to play this bot on my own network, how can I do that?

The bot requires some setup before it can be up and running. Here are the required steps:

- You need to have Python 3.11 or above installed on your machine or the server on which the bot will be running. Steps on how to install Python on your machine will not be covered here, although if you are using Windows, then you can simply download and install the [official Python binaries][pydownload].
- If building Python from source, you will need to include SQLite3 as part of the building process, as the bot uses it.
- To enable the ability to update the bot with the changes that we bring to it, you will need to [download and install Git][git].
- If your network supports it, create an account for your bot and give it automatic op upon joining your channel via your channel management service (typically the `+O` flag if using `ChanServ`).
- Copy the file `botconfig.example.yml` to `botconfig.yml` (make sure to make a copy and not simply rename), and open it with your favourite text editor. The file is extensively commented with examples and additional info. Modify the sections below. If a setting is not present here, it means the default is fine for most cases.
  * transports: update to point to your IRC network
  * access: point this to your account so you can control the bot over IRC
  * logging: see the comments and update accordingly. For a test bot, you could log everything to stdout (as shown in the first group). If you choose to use transport, make sure you pick a channel and check the comments for more info

### I entered everything correctly, but the bot doesn't work in some way. What can I do?

We can help you with these kinds of issues in our [development channel, #lykos][dev_webchat] on Libera. Ask directly in the channel, and wait for someone to answer. Please do not message people directly, as that prevents other people from helping as well. People who are voiced (typically a '+' before their name in the user list, or a blue circle for Hexchat) are developers and are more likely to be able to help.

### The bot works fine, but there's just something I'd like to tweak. Is that possible?

It absolutely is! For additional gameplay settings, see [the wiki's Configuration page](https://werewolf.chat/Configuration#gameplay). You can copy and paste these settings into your `botconfig.yml` file, and change them as you wish. Time-related settings are in seconds. You will need to restart the bot for the changes to take effect.

If there is something that you would like to tweak but can't find the setting for it, you may ask in [#lykos][dev_webchat]. It may be hidden somewhere, or may not exist. We are usually willing to add new settings to allow other bot owners to customize their bot to the fullest. You may also [open an issue][new_issue] on our bug tracker.

### The roles are nice, but I'd like to add my own. Can I do that?

Yes! You can create your own roles by putting them inside the `roles` folder. There is a base "skeleton" file `src/roles/_skel.py` that you can copy and paste to get some basic stuff in. You will need to define your own commands (if applicable) and [register events][events]. If you need assistance with this, we'll be happy to help you.

### Can I also add a new game mode to go with my new role(s) or just to change things up?

That's also possible! See the existing gamemodes in `src/gamemodes/` and the community modes in [our community-modes repository](https://github.com/lykoss/community-modes) for examples. Put custom modes into the `gamemodes/` directory (*not* `src/gamemodes/`). Then, copy `gamemodes/__init__.py.example` to `gamemodes/__init__.py` so your bot loads them.

### What admin commands can I use?

A [list of admin commands](https://werewolf.chat/Admin_commands) is available on our wiki.

### Our player base is mostly non-English speakers. Is there support for our language?

Not right now. However, Lykos is a community effort, and so we encourage you to submit your own translation! We invite you to [communicate with us][dev_webchat] for questions on how to proceed.

### We would like to do X with a role/gamemode, but the bot doesn't seem to support it. What can I do?

Let us know! We will do the best we can to accomodate third-party roles and modes, and make sure they work fine!

### Additional information for bot operators

You can run the bot by doing `./wolfbot.py` on Linux, or simply double-clicking the `wolfbot.py` file on Windows.

## Credits

This bot wouldn't be what it is today without the contribution of many people. Here are some of those people:

jcao219: Original programmer

moonmoon: Main developer  
Faely: Main developer

jacob1: Developer  
alexia: Developer

Iciloo: Testing

Everyone who contributed to the code, no matter how small.  
Everyone who opened issues in our bug tracker.  
And, of course, to all the bot owners and players who kept this project alive all this time!

Special thanks to LaneAtomic for their work on the messages system, which made coding future features a lot easier.

[mafia]: https://en.wikipedia.org/wiki/Mafia_(party_game)
[license]: https://github.com/lykoss/lykos/blob/master/LICENSE
[game_webchat]: https://web.libera.chat/?channels=#werewolf
[wikinet]: https://werewolf.chat/Networks
[wiki]: https://werewolf.chat/Main_Page
[pydownload]: https://www.python.org/downloads/
[git]: https://git-scm.com/downloads
[dev_webchat]: https://web.libera.chat/?channels=#lykos
[new_issue]: https://github.com/lykoss/lykos/issues/new
[events]: https://werewolf.chat/Events
=======
# WereWolf – Projektübergabe und Gesprächsstand

**Stand:** 17. Oktober 2026  
**Projekt:** WereWolf – geschichtsbasiertes Deduktionsspiel für Discord  
**Repository:** https://github.com/svdermant/WereWolf

Dieses Dokument dient als Neuauflage und Übergabe des bisherigen Gesprächs. Es soll sicherstellen, dass bei einer Unterbrechung keine wichtigen Informationen, Entscheidungen oder Arbeitsprioritäten verloren gehen.

---

## 1. Grundidee des Projekts

Das Ziel ist die Entwicklung eines neuen Discord-Bots für das Spiel WereWolf.

Der ursprüngliche Quellcode ist nicht mehr vorhanden. Als historische Referenz existieren jedoch noch eine englische JSON-Nachrichtendatei, Dokumentation und Informationen über die ursprünglichen Spielrollen und Spielmodi.

Der neue Bot soll nicht einfach eine Kopie des alten Systems werden. Er soll auf Grundlage der vorhandenen Informationen neu geplant und dokumentiert werden.

**Wichtig:** Zum aktuellen Planungsstand existiert noch keine neue Bot-Implementierung. Besprochene Commands, Event-Handler und Codebausteine sind zukünftige Konzepte, keine bereits vorhandenen Funktionen.

Zunächst soll der Bot auf einem einzelnen Discord-Server eingesetzt werden. Eine spätere Nutzung auf weiteren Servern ist denkbar, soll aber nicht unnötig kompliziert in die erste Planung einfließen.

## 2. Verbindliche Arbeitsreihenfolge

Die vollständige Dokumentation hat Vorrang vor der Berechtigungsplanung und der Programmierung.

Die vorgesehene Reihenfolge lautet:

1. Lore und historische Grundlagen dokumentieren und analysieren.
2. Vorhandene Nachrichten-JSON untersuchen und ihre Struktur dokumentieren.
3. Mögliche Discord-Event-Handler und interne Spielereignisse erfassen.
4. Wiederverwendbare Codebausteine und mögliche technische Komponenten dokumentieren.
5. Spielabläufe, Rolleninteraktionen, Spielmodi und weitere offene Dokumentationsbereiche vervollständigen.
6. Gesamte Dokumentation auf Vollständigkeit, Widersprüche und fehlende Themen prüfen.
7. Erst danach das Berechtigungssystem und die endgültigen Command-Funktionen ausarbeiten.
8. Anschließend die konkrete technische Umsetzung planen.

**Die Rollenberechtigungen und Command-Funktionen sollen jetzt nicht weiter ausgearbeitet werden.** Sie werden erst wieder aufgegriffen, wenn die Dokumentation vollständig abgeschlossen ist.

## 3. Wichtige Projektlinks

- Repository: https://github.com/svdermant/WereWolf
- README: https://github.com/svdermant/WereWolf/blob/main/README.md
- Wiki für Admin-Befehle: https://github.com/svdermant/WereWolf/wiki/Admin-Befehle
- Lore-Wiki: https://github.com/svdermant/WereWolf/wiki/Lore
- Commit mit der ursprünglichen englischen Nachrichten-JSON: https://github.com/svdermant/WereWolf/commit/ef9ea8b18fed4bac578759364809d22b3ccfac88
- Direkte Datei `messages/en.json` aus diesem Commit: https://raw.githubusercontent.com/svdermant/WereWolf/ef9ea8b18fed4bac578759364809d22b3ccfac88/messages/en.json

## 4. Lore und Hintergrundgeschichte

Die Lore-Seite wurde vom Projektinhaber erstellt und ist die Grundlage für die weitere inhaltliche Dokumentation.

Sie beschreibt unter anderem:

- Ursprünge und Hintergründe der Spielrollen.
- Beziehungen zwischen einzelnen Rollen und übernatürlichen Wesen.
- Besondere Fähigkeiten und Eigenschaften.
- Die Entstehungsgeschichten der verschiedenen Spielmodi.
- Historische Ereignisse und Zusammenhänge innerhalb der Spielwelt.

Die Spielmodi umfassen derzeit:

- Standard
- Töricht
- Wahnsinnig
- Zufällig
- Mahlstrom
- Aleatoire
- Wächter

Diese Geschichten beschreiben die damalige Entstehung der Spielmodi und teilweise deren Spielabläufe. Sie sollen nicht einfach zu einer einzigen linearen Geschichte zusammengefasst werden.

Langfristig können sie als erzählerische Epochen, Kapitel oder Szenarien in das neue System integriert werden.

### Namensabweichungen

Die ursprüngliche Version wurde auf einem englischsprachigen Server entwickelt. Da der Quellcode verloren ist, stammen manche Bezeichnungen noch aus der englischen Version. Bei der Erstellung der Lore wurden bereits zahlreiche Namen ins Deutsche übersetzt.

Beispiele:

- `werekitten` → Werkätzchen
- `minion` → Handlanger

Diese Zuordnungen sind Beispiele für die bisherige Übersetzungsarbeit. Weitere Rollenbezeichnungen sollen nach und nach vereinheitlicht werden.

Abweichende Bezeichnungen wie „Werkitten“ und „Werkätzchen“ sind deshalb nicht automatisch inhaltliche Widersprüche. Sie können aus der früheren englischen Namensgebung entstanden sein.

Bei der Vereinheitlichung sollen die bevorzugten deutschen Bezeichnungen des Projektinhabers maßgeblich sein.

### Grundsatz für die Lore

Die bestehende Lore soll nicht ohne Grund umgeschrieben werden. Unklare Beziehungen, widersprüchliche Angaben und offene Hintergrundfragen sollen zunächst gesammelt und anschließend mit dem Projektinhaber geklärt werden.

Es dürfen keine neuen Hintergrundinformationen als bereits feststehende Lore ausgegeben werden, wenn sie nicht durch die vorhandenen Texte oder Entscheidungen des Projektinhabers gedeckt sind.

## 5. Ziel: ein geschichtsbasierter Deduktions-Bot

Der neue Bot soll mehr sein als ein technisches System, das lediglich Befehle verarbeitet und Statusmeldungen ausgibt.

Die angestrebte Spielerfahrung verbindet drei Ebenen:

### Ebene A – Lore und Weltgeschichte

Diese Ebene erklärt die Welt, die Herkunft der Rollen, die Beziehungen zwischen Figuren und die Entstehung der Spielmodi.

### Ebene B – Spielmechanik und Ereignisse

Diese Ebene umfasst die tatsächlichen Spielabläufe: Nachtaktionen, Angriffe, Schutz, Abstimmungen, Tod, Rollenwechsel und weitere Wechselwirkungen.

### Ebene C – Erzählerische Darstellung

Diese Ebene beschreibt, wie der Bot die tatsächlichen Spielereignisse in eine Geschichte übersetzt.

Beispielsweise soll der Bot nicht bloß melden, dass der Tag begonnen hat. Er könnte beschreiben, wie die Bewohner auf den Dorfplatz kommen, welche Spuren sie entdecken und wie die Ereignisse der vergangenen Nacht das Dorf verändert haben.

Die Geschichte soll auf dem tatsächlichen Spielgeschehen basieren. Ein verhinderter Angriff darf beispielsweise nicht wie ein erfolgreicher Angriff erzählt werden.

Die Erzählung soll die Spieler in die Spielwelt integrieren. Handlungen der Spieler sollen sich in den nachfolgenden Szenen widerspiegeln.

Dafür müssen wir nicht zwingend künstliche Intelligenz verwenden. Auch vorbereitete, kombinierbare Textbausteine können dynamische und abwechslungsreiche Geschichten erzeugen.

## 6. Die historische Nachrichten-JSON

Ein wichtiges Überbleibsel des ursprünglichen Systems ist die Datei:

`messages/en.json`

Sie wurde im Commit `ef9ea8b18fed4bac578759364809d22b3ccfac88` hinzugefügt.

Die Datei umfasst laut bisheriger Betrachtung 1.507 Zeilen und enthält zahlreiche Nachrichten-Schlüssel, englische Texte und formatierte Platzhalter.

Sie ist eine historische Referenz für das ursprüngliche System.

### Bisher erkannte Inhalte

Die Datei enthält unter anderem:

- Rollennamen und Mehrzahlformen.
- Namen verschiedener Spielmodi.
- Command-Namen und Aliase.
- Nachrichten zu Spielbeginn und Phasenwechseln.
- Texte zu Abstimmungen und Todesfällen.
- Varianten für unterschiedliche Spielausgänge.
- Meldungen zu Rollenfähigkeiten und Wechselwirkungen.
- Technische Meldungen und Hilfetexte.
- Platzhalter für Spieler, Rollen, Rundennummern, Zeitangaben und weitere dynamische Informationen.

Beispiele für bisher betrachtete Schlüssel:

- `player_joined`
- `night_begin`
- `sunrise`
- `guardian_target_another`
- `gunner_victim_bodyguard`
- `fflags_usage`

Außerdem wurden Formatierungen wie `{0:@}`, `{1!role:bold}`, `{0:join(@)}` und `{=wolf!role}` festgestellt.

Solche Ausdrücke deuten auf dynamische Nachrichtenerzeugung und spezielle Formatierungsregeln hin. Ihre vollständige Bedeutung muss noch systematisch untersucht werden.

### Grenzen der JSON

Die JSON enthält Nachrichtentexte und Strukturinformationen, aber nicht zwangsläufig die vollständige ursprüngliche Spiellogik.

Aus einem Nachrichtenschlüssel lässt sich möglicherweise ableiten, welches Ereignis dargestellt wird. Er beweist jedoch nicht, wie die ursprüngliche Engine dieses Ereignis ausgelöst oder verarbeitet hat.

Bei der weiteren Analyse müssen wir unterscheiden zwischen:

1. Direkt aus der Datei belegbaren Informationen.
2. Plausiblen Schlussfolgerungen.
3. Noch offenen Fragen.

### Vorgesehene Dokumentation der JSON

Für die weitere Analyse soll eine Übersicht entstehen, die möglichst folgende Informationen enthält:

- Nachrichten-Schlüssel.
- Englischer Originaltext.
- Vorgesehene deutsche Fassung.
- Platzhalter und benötigte Parameter.
- Vermutetes oder belegtes auslösendes Ereignis.
- Betroffene Rolle oder Spielmodus, sofern bekannt.
- Varianten für unterschiedliche Ereignisausgänge.
- Offene Fragen.

Die Nachrichten sollen nicht automatisch unverändert übernommen werden. Sie dienen als Referenz, um die ursprüngliche Spielerfahrung zu erhalten und für das neue geschichtsbasierte System weiterzuentwickeln.

## 7. Noch zu dokumentierende Event-Handler und Ereignisse

Nach der Lore-Analyse und der strukturierten Untersuchung der JSON sollen mögliche Discord-Event-Handler und interne Spielereignisse dokumentiert werden.

Dabei müssen wir zwei unterschiedliche Kategorien auseinanderhalten:

**Discord-Events:** Ereignisse, die von Discord beziehungsweise der verwendeten Bibliothek ausgelöst werden, beispielsweise Nachrichten, Interaktionen oder Änderungen von Mitgliederdaten.

**Interne Spielereignisse:** Ereignisse innerhalb der Spielmechanik, beispielsweise der Beginn einer Nacht, die Auflösung eines Angriffs, ein Rollenwechsel oder das Spielende.

Mögliche Themen für die spätere Dokumentation:

- Beginn und Ende einer Spielphase.
- Aktionen einzelner Rollen.
- Erfolgreiche und verhinderte Angriffe.
- Schutzaktionen und deren Wechselwirkungen.
- Tod und mögliche Folgeeffekte.
- Wiederbelebung und Rollenwechsel.
- Abstimmungen und deren Ergebnisse.
- Besondere Ereignisse einzelner Spielmodi.
- Spielende und abschließende Erzählung.
- Fehlerbehandlung und ungültige Aktionen.

Diese Liste ist eine Sammlung möglicher Dokumentationsthemen, keine endgültige technische Spezifikation.

## 8. Mögliche Codebausteine

Nach den Event-Handlern sollen wiederverwendbare technische Komponenten untersucht werden.

Mögliche Themen sind:

- Verwaltung von Spielzuständen.
- Verwaltung von Rollen und Spielerzugehörigkeiten.
- Verarbeitung von Rollenaktionen.
- Auflösung von Ereignissen in einer festgelegten Reihenfolge.
- Auswahl und Formatierung von Nachrichten.
- Erzählerische Textbausteine und Varianten.
- Verwaltung von Spielmodi.
- Datenhaltung und Persistenz.
- Fehlerbehandlung und Protokollierung.

Es soll zunächst dokumentiert werden, welche Komponenten möglicherweise sinnvoll sind. Die konkrete Implementierung und endgültige Architektur bleiben einer späteren Planungsphase vorbehalten.

## 9. Vorläufige Command- und Berechtigungshinweise

Einige zukünftige Admin-Commands wurden bereits besprochen. Die Ergebnisse sind nur als vorläufige Planungshinweise zu verstehen und sollen während der Dokumentationsphase nicht weiter ausgearbeitet werden.

Bisherige Entscheidungen:

- `!fgame` und `!spectate`: Spielmoderatoren.
- `!revealroles` im Debug-Modus: Administratoren.
- `!fjoin` und `!fleave`: Spielmoderatoren.
- `!fstasis`: Administratoren.
- `!fsay` und `!fdo`: Administratoren.
- `!aftergame` und `!lastgame`: Administratoren.
- `!restart` und `!update`: ausschließlich Besitzer.
- `!fwarn add`: Spielmoderatoren.
- `!fwarn list/view`: Spielmoderatoren und höhere Rollen, nicht Helfer.
- `!fwarn set/del`: Administratoren und Besitzer.
- `!fday`, `!fnight`, `!fstart`, `!fstop`, `!fwait` und `!fgoat`: Spielmoderatoren.
- `!force`, `!rforce` und `!frole`: Administratoren.

Noch nicht endgültig bestätigt wurden unter anderem einzelne Vorschläge zu `!fdie`, `!refreshdb`, `!eval`, `!exec` und `!fspectate`.

Weitere Hinweise:

- `!fsend` soll im neuen Bot nicht mehr verfügbar sein.
- `!template` wird nicht mehr benötigt.
- Der korrekte Command-Name lautet `!fwarn`, nicht `!fwarm`.
- `!fflags` ist als zukünftiges dynamisches Rollen- und Berechtigungsmanagement gedacht.

Diese Themen werden erst nach Abschluss der Dokumentation wieder aufgenommen.

## 10. Vorgehensweise bei der weiteren Zusammenarbeit

Bei jeder weiteren Bearbeitung gelten folgende Grundsätze:

- Keine nicht vorhandene Implementierung unterstellen.
- Historische Informationen, feststehende Entscheidungen und neue Ideen klar voneinander trennen.
- Unklare Sachverhalte zunächst als offene Fragen markieren.
- Keine Lore eigenmächtig erfinden oder verändern.
- Englische Originalbezeichnungen und deutsche Übersetzungen nachvollziehbar zuordnen.
- Nachrichten und Spielereignisse mit den entsprechenden Rollen und Spielmodi verknüpfen, soweit die Quellen dies ermöglichen.
- Dokumentation vollständig abschließen, bevor die Berechtigungsplanung weitergeht.
- Bestehende Wiki-Seiten und GitHub-Dateien als Referenz verwenden, statt bereits dokumentierte Inhalte unnötig neu zu schreiben.

## 11. Nächste konkrete Arbeitsschritte

1. Die Lore als Grundlage der weiteren Dokumentation behandeln.
2. Die englische `messages/en.json` systematisch untersuchen.
3. Nachrichtenschlüssel, Parameter und Varianten katalogisieren.
4. Nachrichten mit Rollen, Spielmodi und möglichen Spielereignissen verknüpfen.
5. Offene Fragen zur ursprünglichen Spiellogik sammeln.
6. Mögliche Discord-Event-Handler und interne Spielereignisse dokumentieren.
7. Wiederverwendbare Codebausteine und weitere technische Themen dokumentieren.
8. Alle Wiki-Seiten und sonstigen Dokumentationsbereiche abschließend prüfen.
9. Erst nach vollständigem Dokumentationsabschluss die Berechtigungen und Commands weiter planen.

---

**Wichtigster Übergabepunkt:** Das Projekt befindet sich in der Dokumentations- und Konzeptionsphase. Die Lore und die historische Nachrichten-JSON sollen gemeinsam als Grundlage dienen, um das ursprüngliche Spiel besser zu verstehen. Das langfristige Ziel ist ein geschichtsbasierter Deduktions-Bot, bei dem die tatsächlichen Spielerhandlungen die Erzählung beeinflussen. Das Berechtigungssystem und die Programmierung sind vorerst zurückgestellt, bis die Dokumentation vollständig abgeschlossen ist.
>>>>>>> 590ee0ff42c8f2c975741fd82ab75a1bbceafd3f
