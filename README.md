# Lykos, der Open-Source-Werwolf-Bot

## Allgemeine Informationen

### Was bedeutet Open-Source?

Open-Source bedeutet, dass der Quellcode für jedermann frei verfügbar ist, um ihn einzusehen, herunterzuladen, zu verwenden und zu verändern.

### Was ist Werwolf?

Werwolf ist ein beliebtes Partyspiel, das Werwölfen oder deren Verbündeten sowie Dorfbewohnern – einige mit besonderen Fähigkeiten – eine Rolle zuweist. Die Dorfbewohner versuchen herauszufinden, wer die Wölfe sind, und sie zu eliminieren, bevor es zu spät ist. Es kann auch andere Spieler geben, die eigene Ziele verfolgen, welche möglicherweise oder möglicherweise auch nicht irgendeiner Seite helfen.

### Wo konnte ich dieses Spiel spielen?

Wir betrieben eine Instanz des Bots im Kanal [#werewolf] auf QuakeNet. Off-Topic-Diskussionen fanden in #werewolf-meta im selben Netzwerk statt, und Kanalbetreiber waren in #werewolf-ops erreichbar. Es gab auch andere Netzwerke. Denken Sie daran, dass die bloße Tatsache, dass jemand als online erscheint, nicht bedeutet, dass er anwesend war oder zum Spielen verfügbar war.

## Für Spieler

### Ich habe noch nie ein solches Spiel gespielt. Wie konnte man es spielen?

Man konnte einem neuen Spiel mit `!join` beitreten. Es mussten mindestens 6 Spieler vorhanden sein, um ein Spiel starten zu können. Wenn das Spiel gestartet war, sendet Ihnen der Bot eine private Nachricht, in der er Ihnen Ihre Rolle mitteilt, auf welche Befehle Sie als diese Rolle Zugriff haben und welches Ihr Ziel ist. Man konnte dann private Befehle an den Bot senden, zum Beispiel `see person`. Einige Rollen erfordern, dass Sie den Befehl während des Tages und manchmal im Kanal verwenden. Achten Sie unbedingt auf die Nachricht

Wenn man ein Wolf oder eine andere wolfsgesinnte Rolle war, hatte man wahrscheinlich Zugriff auf den Wolfchat. Um diesen zu nutzen, schrieb man einfach eine Nachricht an den Bot, und die Nachricht wird an alle anderen Wolfchat-Spieler weitergeleitet. Sie erhalten auch deren Nachrichten.

Der Bot enthält über 50 Rollen, daher kann es eine Weile dauern, bis man sich an alle gewöhnt hat. Übung macht den Meister!

### Welche Befehle konnte ich als Spieler verwenden?

Lykos verfügt über eine Vielzahl von Befehlen, die für verschiedene Zwecke verwendet werden können. Die Liste der Befehle findet man heute unter: https://github.com/svdermant/WereWolf/wiki/Bot-Commands

## Für Bot-Betreiber

### Ich möchte diesen Bot in meinem eigenen Netzwerk betreiben, wie kann ich das machen?

Der Bot erfordert einige Einrichtungsschritte, bevor er einsatzbereit ist. Hier sind die erforderlichen Schritte:

- Sie müssen Python 3.11 oder höher auf Ihrem Computer oder dem Server installiert haben, auf dem der Bot läuft. Schritte zur Installation von Python auf Ihrem Computer werden hier nicht behandelt, aber wenn Sie Windows verwenden, können Sie einfach die [offiziellen Python-Binärdateien] [pydownload] herunterladen und installieren.
- Wenn Sie Python aus dem Quellcode erstellen, müssen Sie SQLite3 als Teil des Build-Prozesses einbinden, da der Bot es verwendet.
- Um die Möglichkeit zu aktivieren, den Bot mit den von uns bereitgestellten Änderungen zu aktualisieren, müssen Sie [Git herunterladen und installieren] [git].
- Wenn Ihr Netzwerk dies unterstützt, erstellen Sie ein Konto für Ihren Bot und geben Sie ihm über Ihren Kanalverwaltungsdienst automatisches Op beim Beitreten zu Ihrem Kanal (typischerweise das Flag `+O`, wenn Sie `ChanServ` verwenden).
- Kopieren Sie die Datei `botconfig.example.yml` nach `botconfig.yml` (stellen Sie sicher, dass Sie eine Kopie erstellen und sie nicht einfach umbenennen), und öffnen Sie sie mit Ihrem bevorzugten Texteditor. Die Datei ist ausführlich mit Beispielen und zusätzlichen Informationen kommentiert. Modifizieren Sie die folgenden Abschnitte. Wenn eine Einstellung hier nicht vorhanden ist, bedeutet dies, dass der Standardwert für die meisten Fälle in Ordnung ist.
  * transports: Aktualisieren Sie dies, um auf Ihr IRC-Netzwerk zu verweisen
  * access: Verweisen Sie hiermit auf Ihr Konto, damit Sie den Bot über IRC steuern können
  * logging: Lesen Sie die Kommentare und aktualisieren Sie sie entsprechend. Für einen Test-Bot könnten Sie alles nach stdout protokollieren (wie in der ersten Gruppe gezeigt). Wenn Sie Transport verwenden möchten, stellen Sie sicher, dass Sie einen Kanal auswählen, und lesen Sie die Kommentare für weitere Informationen

### Ich habe alles korrekt eingegeben, aber der Bot funktioniert in irgendeiner Weise nicht. Was kann ich tun?

Wir haben damals bei solchen Problemen in unserem [Entwicklungskanal #lykos] [dev_webchat] auf Quakenet geholfen. Man konnte dort direkt im Kanal fragen und warten, bis jemand antwortet. Bitte schreiben Sie Personen nicht direkt an, da dies andere Personen daran hindert, ebenfalls zu helfen. Personen mit Voice (typischerweise ein '+' vor ihrem Namen in der Benutzerliste oder ein blauer Kreis für Hexchat) sind Entwickler und können eher helfen.

### Der Bot funktioniert einwandfrei, aber es gibt da etwas, das ich anpassen möchte. Ist das möglich?

Auf jeden Fall! Weitere Spieleinstellungen finden Sie Ihre `botconfig.yml`-Datei und können sie nach Belieben ändern. Zeitbezogene Einstellungen sind in Sekunden angegeben. Sie müssen den Bot neu starten, damit die Änderungen wirksam werden.

Wenn es etwas gab, das Sie anpassen möchten, aber die Einstellung dafür nicht finden können, konnte man in [#lykos] [dev_webchat] nachfragen. Es ist möglicherweise irgendwo versteckt oder existiert nicht. Wir waren in der Regel bereit, neue Einstellungen hinzuzufügen, damit andere Bot-Besitzer ihren Bot optimal anpassen konnten. Man konnte auch [ein Problem aufmachen] [new_issue] in unserem Bug-Tracker.

### Die Rollen sind schön, aber ich möchte meine eigenen hinzufügen. Kann ich das tun?

Ja! Sie können Ihre eigenen Rollen erstellen, indem Sie sie in den Ordner `roles` legen. Es gibt eine Basis-„Skelettdatei“ `src/roles/_skel.py`, die Sie kopieren und einfügen können, um einige grundlegende Dinge zu erhalten. Sie müssen Ihre eigenen Befehle definieren (falls zutreffend) und [Ereignisse registrieren] [events]. Wenn Sie dabei Hilfe benötigen, halfen wir gerne weiter.

### Kann ich auch einen neuen Spielmodus hinzufügen, der zu meiner neuen Rolle / meinen neuen Rollen passt oder einfach um die Dinge abzuwechseln?

Das ist ebenfalls möglich! Schauen Sie sich die bestehenden Spielmodi in `src/gamemodes/` als Beispiele an. Legen Sie benutzerdefinierte Modi im Verzeichnis `gamemodes/` ab (*nicht* in `src/gamemodes/`). Kopieren Sie dann `gamemodes/__init__.py.example` nach `gamemodes/__init__.py`, damit Ihr Bot sie lädt.

### Welche Administrator-Befehle kann ich verwenden?

Eine [Liste der Administrator-Befehle](https://github.com/svdermant/WereWolf/wiki/Admin-Befehle) ist in unserem Wiki verfügbar.

### Unsere Spielerbasis besteht größtenteils aus Nicht-Englischsprachigen. Gibt es Unterstützung für unsere Sprache?

Damals nicht. Lykos ist jedoch eine Gemeinschaftsarbeit, und deshalb ermutigen wir Sie, Ihre eigene Übersetzung einzureichen! Wir laden Sie ein, [mit uns zu kommunizieren] [dev_webchat], wenn Sie Fragen zum weiteren Vorgehen haben.

### Wir möchten X mit einer Rolle/einem Spielmodus machen, aber der Bot scheint das nicht zu unterstützen. Was kann ich tun?

Lassen Sie es uns wissen! Wir werden unser Bestes tun, um Rollen und Modi von Drittanbietern unterzubringen und sicherzustellen, dass sie einwandfrei funktionieren!

### Zusätzliche Informationen für Bot-Betreiber

Sie können den Bot ausführen, indem Sie `./wolfbot.py` unter Linux eingeben oder einfach doppelt auf die Datei `wolfbot.py` unter Windows klicken.

## Mitwirkende (Credits)

Dieser Bot wäre nicht das, was er heute ist, ohne den Beitrag vieler Menschen. Hier sind einige dieser Personen:

jcao219: Ursprünglicher Programmierer

moonmoon: Hauptentwickler, auch bekannt als Serkons oder Svdermant 

Iciloo: Tests

Jeder, der zum Code beigetragen hat, egal wie klein der Beitrag war.  
Jeder, der Probleme in unserem Bug-Tracker gemeldet hat.  
Und natürlich an alle Bot-Besitzer und Spieler, die dieses Projekt all die Zeit am Leben erhalten haben!

Besonderer Dank geht an LaneAtomic für ihre Arbeit am Nachrichtensystem, die das Programmieren zukünftiger Funktionen erheblich erleichtert hat.
