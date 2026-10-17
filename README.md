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
