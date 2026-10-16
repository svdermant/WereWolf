# Projektübergabe: Dokumentation des Werwolf-Bots

**Stand:** 16. Oktober 2026  
**Zweck:** Diese Datei in einer neuen ChatGPT-Unterhaltung verlinken oder hochladen, damit die Dokumentation ohne erneutes Erklären fortgesetzt werden kann.

## Arbeitsweise und Ziel

Wir sammeln und strukturieren die Regeln, Rollen, Kategorien, Totems und Spielmodi eines Werwolf-/Mafia-Bots. Das ist zunächst Dokumentation, noch keine Implementierung.

- Antworte auf Deutsch.
- Erfinde keine Regeln. Wenn eine Quelle unklar oder widersprüchlich ist, kennzeichne das ausdrücklich als offen.
- Neuere Korrekturen des Nutzers haben Vorrang vor älteren Notizen.
- Der Nutzer liest teils aus einer Referenz vor; Spracherkennungsfehler und ungewöhnliche Modusnamen können vorkommen. Namen nicht eigenmächtig normalisieren.
- Für Rollenverteilungen in Bildern können GitHub-Dateilinks verwendet werden. Besonders hilfreich sind direkte `raw.githubusercontent.com`-Links. Die Bilddatei zum Rapidfire-Modus ließ sich auf diesem Weg öffnen; erneutes Hochladen ist dafür nicht zwingend nötig.
- Wenn ein neues Gespräch beginnt, zuerst diese Zusammenfassung lesen und dann an der zuletzt bearbeiteten Stelle fortsetzen. Keine bereits beantworteten Fragen erneut stellen.

## Globale Team- und Siegregeln

- Jeder Spieler hat genau eine **Hauptrolle** und kann zusätzlich null oder mehrere **Templates/Zweitrollen** haben. Ein Template ersetzt die Hauptrolle nicht.
- Das Dorf gewinnt, wenn alle Spieler mit der Kategorie `Wolf` getötet oder anderweitig beseitigt sind. Andere Rollen, die mit Wölfen gewinnen oder Wolfchat haben, zählen nicht automatisch als tatsächliche Wolfrollen.
- Das Wolfteam gewinnt, wenn die Zahl der lebenden Spieler mit `Wolfchat` mindestens so groß ist wie die Zahl der lebenden Spieler ohne `Wolfchat`.
- Alle tatsächlichen Wolfrollen haben `Wolfchat`; außerdem haben manche Nicht-Wolfrollen (z. B. Zauberer, Hexe, Hexenmeister) Wolfchat und zählen für diese Schwelle mit.
- Kultführer, Kultist und Scherge gewinnen mit den Wölfen, zählen aber für diese zahlenmäßige Schwelle wie Dorfbewohner.
- Kategorien sind wichtig für Interaktionen. Nicht jede Rolle gehört allen naheliegenden Kategorien an; Widersprüche der Quelle nicht stillschweigend glätten.

## Kategorien (Kurzreferenz)

- `Wolfteam`: gewinnt mit Wölfen; Augur sieht rot, Mystic zählt.
- `Dorf`: gewinnt mit dem Dorf; Augur sieht blau.
- `Neutral`: eigene Siegbedingung; Augur sieht grau.
- `Versteckt`: Spieler kennt seine wahre Rolle nicht und erhält je nach Modus die Anzeige Dorfbewohner oder Kultist; gewinnt entsprechend der angezeigten Teamzugehörigkeit. Augur sieht die tatsächliche Teamfarbe.
- `Verflucht`: Seher und Orakel sehen Wolf. Beispiele: Verrückter Wissenschaftler, Sukkubus.
- `Innocent`/`Unschuldig`: Seher und Orakel sehen immer Dorfbewohner. Kann nicht zugleich `Shooter` oder `Cursed Villager` sein. Beispiele: Werkitten, Priester, Narr.
- `Mörder`: tötet aktiv im Spiel; passive Todesauslöser wie Verrückter Wissenschaftler oder Rachsüchtiger Geist zählen nicht dazu.
- `Nachtaktiv`: kann jede Nacht handeln; einmalige oder nicht jede Nacht aktive Rollen wie Jäger, Überläufer und Kuppler sind ausgenommen. Werkrähe beobachtet solche Rollen als „nicht im Bett“.
- `Sicher`: Seher sieht tatsächliche Rolle statt pauschal Dorf/Wolf. `Sicher` + `Innocent` bedeutet sichere Dorfanzeige; `Verflucht` hat Vorrang. Wolf-Mystic zählt diese Rollen nachts.
- `Spion`: sammelt aktiv Informationen über Spieler/Rollen/Kategorien; Zauberer kann solche Rollen erkennen.
- `Intuitive`: erhält passiv Informationen, die veraltet sein können. Zauberer zählt den Hexenmeister nicht allein deshalb, weil dieser weiß, wen er verflucht hat.
- `Team Switcher`: kann die Teamzugehörigkeit wechseln.
- `Win Stealer`: kann bei eigener Siegbedingung normale Team-Siege verdrängen; muss außerdem `Neutral` sein. Vigilant kann diese Rollen als gültige Ziele haben; Wolf-Mystic zählt sie.
- `Wolf`: tatsächliche Wolfrolle. Sollte auch `Wolfteam` sein. Seher/Orakel sehen Wolf; Schütze tötet sie sofort; Vigilant kann sie töten; Hure stirbt beim Besuch; Diener erhält zu Beginn eine Liste der Wolfrollen. Dorf muss alle `Wolf`-Rollen beseitigen.
- `Wolfchat`: Zugang zum Wolfchat; üblicherweise auch `Wolfteam`, mit dokumentierten Ausnahmen/Unstimmigkeiten.

## Bereits dokumentierte Dorfrollen und Templates

Die Details wurden im Gespräch ausführlicher erarbeitet. Die folgenden Punkte sind die wichtigsten Regeln, um den Kontext wiederherzustellen.

### Seher
- `see Nick` nachts; erhält Team-/Kategorieresultat, nicht exakte Rolle.
- Verfluchte Dorfbewohner erscheinen als Wolf; manche Wolfchat-Rollen wie Hexen/Überläufer erscheinen als Dorfbewohner.
- Kann nicht selbst Verfluchter Dorfbewohner sein.
- Werwolfrollen können den Seher entdecken/blockieren; Werkrähe kann aktive Rollen erkennen, Zauberer den Seher bei seinem Scan erkennen.

### Detektiv
- Tagsüber privat `ID Nick`; tatsächliche Rolle mit 100 % Genauigkeit.
- Erkennt u. a. Überläufer, verfluchte Dorfbewohner, Seher und Orakel. Amnesiac erscheint als die Rolle, in die er sich später verwandeln wird.
- Bei jeder Benutzung besteht eine 40-%-Chance, dass die Wölfe privat erfahren, wer der Detektiv ist.

### Orakel
- `oracle`/entsprechende Aktion nachts: Ja/Nein, ob Ziel Wolf ist.
- Verfluchte Dorfbewohner erscheinen als Wolf; die meisten anderen Nicht-Wolfrollen als Nicht-Wolf.
- Kann nicht selbst Verfluchter Dorfbewohner sein.
- Wird durch Biss des Alpha-Wolfs zum Doomsayer.

### Hure
- `visit Nick`, `pass` oder Selbstbesuch, um zu Hause zu bleiben.
- Besuchtes Ziel erfährt privat, wer es besucht hat.
- Stirbt beim Besuch einer Wolfrolle oder des Wolfsopfers.
- Wird sie von Wölfen angegriffen, während sie jemand anderen besucht, scheitert der Wolfsangriff auf sie.
- Offen: Ob ein durch Biss verwandelter ehemaliger Dorfbewohner beim Besuch als Wolfrolle zählt.

### Schutzengel
- `Guard Nick` oder `Pass`, Selbstschutz möglich.
- Schützt das Ziel vor jedem Todesgrund in dieser Nacht; Ziel erfährt, dass es geschützt wurde, nicht von wem.
- Darf nicht zwei Nächte hintereinander dasselbe Ziel schützen.
- Durch Alpha-Biss eventuell gefallener Engel.

### Leibwächter
- `Guard Nick` oder `Pass`.
- Stirbt anstelle des Ziels, wenn dieses in der Nacht aus irgendeinem Grund sterben würde.
- Darf dasselbe Ziel mehrere Nächte hintereinander schützen.
- Offen: Selbstschutz.

### Priester
- `Bless Nick`: segnet ein anderes Ziel; nicht sich selbst. Gesegnete brauchen zwei Angriffe in derselben Nacht oder zwei Stimmen am Tag, um zu sterben.
- Quelle widerspricht sich, ob Segen einmal pro Spiel oder einmal pro Spieltag möglich ist.
- `Konsekrate Nick`: heiligt einen Leichnam und verhindert Untoten-Wiederbelebung; bei Rachsüchtigem Geist verhindert es dessen Tötung in der folgenden Nacht.
- Nach der Leichenweihe kann der Priester an diesem Tag nicht abstimmen und wird für die Dorfzählung bei der Siegprüfung nicht berücksichtigt.
- Im Schlafmodus können bei Tod des Priesters bestimmte Rollen korrumpiert/verwandelt werden.

### Ermittler
- Tagsüber `ID Nick1 Nick2`; sagt, ob zwei Ziele zum selben Team gehören.
- Liebespaare gelten als gleiches Team, auch bei unterschiedlichen echten Teams.
- Zwei neutrale Rollen werden als gleiches Team angezeigt; Verhalten bei einer neutralen und einer nicht-neutralen Rolle ist offen.
- Erkennt Überläufer auf Wolfseite; durchschaut verfluchte Dorfbewohner und Amnesiacs.

### Prophet
- `pray <role>` nachts. Existiert die Rolle unter den Lebenden, erhält er eine Liste von einem Drittel der lebenden Spieler, von denen mindestens einer die Rolle hat. Sonst wird die Abwesenheit gemeldet und das Gebet ist verbraucht.
- Prophet steht nie selbst auf der Liste, wenn er nach seiner eigenen Rolle fragt.
- Rundung und ungültige Rollenangaben offen.

### Augur
- `see Nick` nachts; Aura: blau = Dorf, rot = Wolfteam inkl. Kultisten/Scherge, grau = echtes Neutral.
- Zeigt aktuelle Teamzugehörigkeit; Lykaner erscheint vor Verwandlung blau. Liebespaare können verborgene Agenda haben.

### Mystic
- Meldet zu Beginn jeder Nacht die Zahl lebender Mitglieder der Kategorie `Wolfteam`, inklusive passender Wolfchat-Rollen und einiger gewinnender Neutraler.
- Aktualisiert sich während der Nacht nicht, sondern erst zu Beginn der nächsten.
- Quellenlisten zu gezählten Rollen sind teilweise uneinheitlich.

### Zeitherr
- Verborgene Rolle; Spieler erhält nachts die Anzeige „Dorfbewohner“.
- Bei Tod: „Tick, tock. Da der Zeitherr gestorben ist, wird der Tag jetzt nur noch 60 Sekunden dauern und die Nacht dauert jetzt nur noch 30 Sekunden.“
- Danach Tagesphase 60 Sekunden, Nacht 30 Sekunden; mehrere Zeitherren verkürzen nicht weiter.
- Seher/Orakel sehen Dorfbewohner, Detektiv erkennt die echte Rolle.
- Offen: genauer Zeitpunkt, ab dem die Verkürzung gilt.

### Kuppler
- Erste Nacht `Choose Nick1 Nick2`; darf sich selbst wählen.
- Bei ausbleibender Wahl wird zufälliges Paar bestimmt; Kuppler erfährt das nicht zwingend.
- Liebende erfahren den Partner, aber nicht dessen Rolle. Stirbt einer, stirbt der andere.
- Kein drittes Team. Überleben beide bis zum Ende und gewinnt mindestens einer regulär, erhalten beide individuellen Sieg.
- Ermittler sieht Liebende als gleiches Team.
- Im Modus Valentine Gifts ist Kuppler Standardrolle statt Dorfbewohner.

### Verrückter Wissenschaftler
- Dorfrolle; beim Tod sterben die lebenden Nachbarn in ursprünglicher Beitrittsreihenfolge, kreisförmig, Tote werden übersprungen.
- Seher/Orakel sehen Wolf.

### Jäger
- `Kill Nick` einmal pro Spiel nachts oder `Pass`; Dorfrolle.

### Vigilant
- `Kill Nick` oder `Pass` nachts.
- Muss tatsächliche Wolfrollen oder andere für den Dorfsieg zu beseitigende Rollen treffen (z. B. Win Stealer wie Succubus/Dämon/Monster/Pfeifer).
- Wolfteam-Rollen ohne `Wolf`-Kategorie (z. B. Kultist, Scherge, Zauberer, unveränderter Überläufer) sind ungültige Ziele.
- Ungültiges Ziel führt zum Tod von Ziel und Vigilant.
- Sonderfälle bei verwandelten Rollen sind zu beachten.

### Schamane
- Jede Nacht zufälliges Totem; Schamane kennt Typ und Wirkung.
- `give Nick`; Selbstziel erlaubt; derselbe Spieler darf nicht zwei Nächte nacheinander dasselbe Totem erhalten.
- Ohne Ziel wird Empfänger zufällig bestimmt. Am Morgen wird öffentlich nur der Empfänger bekanntgegeben.

### Doktor
- Tagsüber `GIF Nick`; macht einen Dorfbewohner dauerhaft immun gegen Wolfverwandlung.
- Beißt der Alpha-Wolf ein immunisiertes Ziel, stirbt es statt sich zu verwandeln.
- Immunisierung kann Lykaner in normalen Dorfbewohner verwandeln.
- Anzahl der Anwendungen offen.

### Schlafwandler
- Am Ende der Nacht Meldung, ob die Nachbarn (ursprüngliche Beitrittsreihenfolge, Tote überspringen) wach waren.
- Wach = Rolle mit jede Nacht zu nutzender Nachtaktion; einmalige/nicht jede Nacht aktive Rollen zählen als schlafend, selbst wenn sie die Fähigkeit genutzt haben.
- Rolle bleibt „wach“, auch wenn die Aktion nicht genutzt wird.
- Offen: ob Namen der schlafenden Nachbarn genannt werden.

### Wichtige Templates
- **Verfluchter Dorfbewohner:** Seher/Orakel sehen Wolf. Als Hauptrolle dorfseitig. Darf als Template nicht auf bestimmte Rollen (Seher, Orakel, Wolfrollen usw.) gelegt werden; genaue Ausnahmen sind teils unklar. Hexenmeister kann nachts verfluchen.
- **Gesegneter Dorfbewohner:** braucht zwei Angriffe in einer Nacht oder zwei Stimmen am Tag zum Sterben. Kann durch bestimmte Effekte wie Gefallener Engel umgangen werden. Quelle/Moduszuordnung teils uneinheitlich.
- **Schütze/Gunner:** tagsüber öffentlich `Shoot Nick`, normalerweise zwei Kugeln. Gegen Nicht-Wölfe: 75 % Treffer, 20 % Fehlschuss, 5 % Waffe explodiert und tötet Schützen; Treffer hat separat 20 % Kopfschuss-Chance. Wolfrollen werden sofort getötet; Werkitten lässt Schuss immer verfehlen, Zäher Wolf wird nur verwundet. Verwundete können nicht abstimmen. Bei Tod durch Wölfe mit Munition 25 % Chance, einen Wolf zu töten; verbleibende Waffe kann den Wölfen mit einer Kugel zufallen. Viele offene Fragen zur Munitionsverbrauchs-/Wunddauerlogik.
- **Scharfschütze:** Template, nicht eigenständige Rolle; 20-%-Chance. Eine Kugel bei 4–16 Spielern, zwei bei 17–24. Trifft und tötet fast jedes Ziel, Werkitten ist Ausnahme. Kann bei Tod durch Wölfe mit Munition einen Wolf treffen; sonst bekommen Wölfe die Waffe mit einer Kugel. In vielen Gunner-Modi vorhanden, nicht in Alea Teure.
- **Bürgermeister:** wird erst bei erster Tagesabstimmung gegen ihn enthüllt; die Gruppe lässt ihn dann leben und niemand scheidet an diesem Tag aus. Spätere Abstimmung gegen ihn tötet ihn normal. Kann nicht auf Rollen mit `Innocent` oder `Win Stealer` gelegt werden.
- **Assassine:** nachts `Target Nick`; bei eigenem Tod stirbt das Ziel mit. Kann ein neues Ziel wählen, solange beide leben; sonst Ziel bleibt. Wenn bei möglicher Auswahl keine gewählt wird, wird zufällig eins gewählt. Modusliste: Alliierte Teure, Alpha, Charmant, Betrunkenfeuer, Verrückt, Schlammig, Schnellfeuer. Viele mögliche Basisklassen, genaue Kategoriebedingungen sind in der Quelle unklar.

## Totems (Kurzreferenz)

- **Todestotem:** Empfänger stirbt automatisch am nächsten Tag. Schutz-/Todeswirkungen können sich gegenseitig beeinflussen.
- **Enthüllungstotem:** Überlebt Empfänger die Tagesabstimmung, wird seine echte Rolle öffentlich enthüllt. Amnesiac erinnert sich sofort.
- **Schweigetotem:** verhindert Sonderaktionen am nächsten Tag und in der folgenden Nacht.
- **Verzweiflungstotem:** wird Empfänger abgewählt, stirbt auch der letzte Abstimmende.
- **Schutztotem:** schützt vor externen Todesursachen, nicht vor Liebeskummer-/Liebestod.
- **Ungeduldstotem:** zählt wie Stimmen für alle außer sich selbst.
- **Friedfertigkeitstotem/Pazifismustotem:** zählt wie Enthaltung.
- **Einflusstotem:** Stimme zählt doppelt.
- **Narkolepsietotem:** keine Tagesabstimmung; zählt für die Team-Siegzählung wie verwundeter Schütze.
- **Austauschtotem:** erstes Ziel der Empfängerrolle in der folgenden Nacht tauscht mit ihr Rollen; beide werden informiert, frühere Ziel-/Besuchseffekte werden zurückgesetzt. Wolfziel kann sofort getauscht werden.
- **Lykanthropietotem:** bei Wolfsangriff in der folgenden Nacht wird Empfänger Wolf statt zu sterben. Spezialverwandlungen: Schutzengel → Gefallener Engel; Seher/Orakel/Augur → Doomsayer; Schamane/Verrückter Schamane → Wolf-Schamane.
- **Gückstotem:** Zielaktionen am folgenden Tag/Nacht treffen einen lebenden Nachbarn statt des eigentlichen Ziels.
- **Pestilenz-/Krankheitstotem:** wenn Empfänger in der folgenden Nacht von Wölfen getötet wird, können Wölfe in der darauffolgenden Nacht nicht töten.
- **Vergeltungstotem:** stirbt Empfänger in derselben Nacht, stirbt zufälliger Angreifer ebenfalls; tagsüber erschossen: Schütze stirbt. Rachsüchtiger Geist kann dadurch seine Tötungsfähigkeit verlieren.
- **Irreführungstotem:** nächste Aktion des Empfängers trifft einen lebenden Nachbarn statt des Ziels.
- **Täuschungstotem:** invertiert Seher-/Orakel-Ergebnisse für Empfänger; wenn Seher/Orakel selbst betroffen ist, sind alle Visionen der Nacht invertiert. Wenn beide Seiten betroffen sind, hebt es sich auf; mehrere Totems auf derselben Person stapeln nicht.

## Wolfrollen (Kurzreferenz)

- **Wolf:** normale Nachtabstimmung `kill <nick>`; alle Wölfe müssen dasselbe Ziel wählen, `retract` zum Zurückziehen. Stirbt sofort durch Gunner.
- **Werkrähe:** tötet wie Wolf; `observe <nick>` und erfährt morgens, ob Ziel die Nacht „im Bett“ war. `Nachtaktiv` gilt als wach/außer Bett, auch ohne Aktion; einmalige/nicht jede Nacht aktive Rollen gelten als im Bett.
- **Wolfjunges:** keine Eigenfähigkeit; stirbt es (außer Inaktivität/Verlassen), erhalten Wölfe in der folgenden Nacht zwei Kills. Wenn alle anderen Wolfrollen tot sind, werden lebende Wolfjunge zu Wölfen.
- **Werkitten:** Seher/Orakel sehen Dorfbewohner; Augur/Detektiv echte Identität/Team. Gunner/Scharfschütze verfehlen immer. Hure stirbt beim Besuch. Wolfteam/Wolfchat/Wolf/Killer/Nacht/Innocent.
- **Alpha-Wolf:** einmal pro Spiel nach Tod einer anderen Wolfrolle `Bite Nick`; Ziel verwandelt sich am Ende der Nacht. Darf gleichzeitig beißen und töten. Doktorimmunität lässt Ziel sterben statt verwandeln; Schutz kann Biss verhindern. Spezialverwandlungen wie oben.
- **Gefallener Engel:** kann wie Wolf töten und umgeht jeden Schutz, auch Monster-Schutz. Leibwächter stirbt beim Schützen des Ziels; Schutzengel überlebt mit 50 % Chance. Schutzengel wird bei Verwandlung zusätzlich Assassine.
- **Wolf-Mystic:** meldet jede Nacht Zahl lebender Dorf-Sonderrollen aus einer festen, aber in Quellen leicht widersprüchlichen Liste. Kein Update während der Nacht.
- **Wolf-Schamane:** wie Schamane, kennt zufälliges Totem, vergibt es nachts, kann zugleich töten. Schamane/Verrückter Schamane werden bei Alpha-Biss zu Wolf-Schamanen.
- **Doomsayer:** `Sieh Nick` nachts, zufälliges Schicksal: Tod (Ziel wird dem Wolfteam genannt, Team entscheidet über Kill), Lykanthropie (Ziel erhält Lykanthropie und wird bei Wolfsangriff Wolf), Krankheit (am nächsten Tag keine Stimme und keine Sonderaktionen). Kann zusätzlich `Töte Nick`.
- **Zäher Wolf:** überlebt einmal die Tagesabstimmung; Gunner verwundet statt tötet. Andere Todesarten töten normal.
- **Wolf-Gunner:** Wolf mit Waffe. Tagsüber öffentlich `Shoot Nick`, nachts `Kill Nick`. Meist erbt er eine Waffe nach dem nächtlichen Tod von Gunner/Scharfschütze, mit genau einer Kugel. 70 % Treffer, 30 % Fehlschuss, 60 % der Treffer Kopfschuss; keine Explosion. Schießt absichtlich daneben, wenn Ziel ein Wolf ist.
- **Verräter:** Wolfchat, zählt zur Wolfchat-Siegschwelle, aber nicht als tatsächliche Wolfrolle. Wenn alle Wölfe/Werkrähen tot sind, verwandelt er sich in Wolf. Wird er als Verräter getötet, wird öffentlich Dorfbewohner angezeigt. Detektiv erkennt ihn; Seher/Orakel sehen Dorfbewohner, Augur Wolfteam.
- **Hexe:** Wolfchat, zählt zur Siegschwelle, ist aber keine tatsächliche Wolfrolle. `Hex Nick` lähmt Ziel für den nächsten Tag und die folgende Nacht.
- **Zauberer:** Wolfteam/Wolfchat/Spion. `Observer Nick` prüft, ob Ziel Seher, Orakel oder Augur ist, unabhängig davon, ob diese ihre Aktion genutzt haben. Seher/Orakel sehen Dorfbewohner, Detektiv echte Rolle.
- **Hexenmeister:** Wolfchat; `Curse Nick` verwandelt Ziel dauerhaft in Verfluchten Dorfbewohner. Wirkt sofort, kann selbst gewählt werden.
- **Kultführer:** gewinnt mit Wölfen, Wolfchat-Zugang, aber für zahlenmäßige Siegbedingung wie Dorfbewohner gezählt; keine Fähigkeit.
- **Kultist:** gewinnt mit Wölfen, keine Fähigkeit oder Extra-Info; zählt numerisch wie Dorfbewohner.
- **Scherge/Minion:** gewinnt mit Wölfen; erfährt erste Nacht Liste tatsächlicher Wolfrollen, nicht deren genaue Rollen. Wölfe wissen nicht, wer Scherge ist; zählt numerisch wie Dorfbewohner.

## Neutrale Rollen (Kurzreferenz)

- **Lykaner:** beginnt im Dorf; bei Wolfsangriff verwandelt er sich in Wolf statt zu sterben. Doktor-Immunisierung verhindert Verwandlung.
- **Überläufer/Turncoat:** startet neutral; kann zwischen Dorf und Wolf wechseln (`Side Villagers`, `Side Wolves`, `Pass`), nicht an zwei Nächten hintereinander. Muss am Ende eine Seite gewählt haben, um zu gewinnen. Zeitpunkt der Wirksamkeit offen.
- **Rachsüchtiger Geist:** verborgene Rolle. Lebend gewinnt er mit Hauptrolle; nach Tod wechselt er gegen das Team des Mörders, kann danach jede Nacht ein Mitglied dieses Teams töten. Sonderregeln für neutrale Täter, Totems und Vergeltungstotem.
- **Amnesiac:** startet neutral und erhält zunächst eine Standardrollen-Anzeige; erinnert sich frühestens zu Beginn der dritten Nacht an echte Rolle und erhält dann deren Team/Fähigkeiten. Kann keine Rolle bekommen, deren Team im Modus fehlt. Einige Informationsrollen erkennen die echte Rolle schon vorher.
- **Klon:** wählt erste Nacht `Clone Nick`; stirbt das Ziel, übernimmt der Klon dessen Hauptrolle, nicht Templates. Bis dahin Dorfteam. Keine Wahl → zufälliges Ziel.
- **Verrückter Schamane:** neutral; vergibt nachts zufälliges Totem ohne dessen Art zu kennen. Kann nicht gewinnen; Spiele zählen nicht zur Gewinnrate.
- **Narr/Fool:** gewinnt nur, wenn er tagsüber abgewählt wird; Spiel endet sofort und Narr ist alleiniger Sieger.
- **Jester:** wie Narr, aber Spiel geht nach eigenem Sieg weiter.
- **Monster:** Win Stealer; gewinnt bei Überleben bis Spielende anstelle normaler Gewinner. Wölfe können es normalerweise nicht töten; Gefallener Engel umgeht Schutz.
- **Dämon:** gewinnt anstelle normaler Gewinner, wenn bei Spielende alle Wölfe tot sind und er überlebt.
- **Pfeifer/Flötenspieler:** verzaubert nachts bis zu zwei Spieler; wenn alle übrigen lebenden Spieler verzaubert sind und der Pfeifer den Tagesbeginn erlebt, gewinnt er vorrangig. Verzauberte kennen einander, nicht den Pfeifer, und behalten Rolle, aber verlieren Sonderfähigkeiten.
- **Sukkubus:** besucht und verzaubert nachts Spieler. Wird sie von einem Wolf besucht? Nein: Sie besucht ein Ziel; Besuch eines Wolfs verzaubert den Wolf, Besuch des Wolfsopfers tötet den Sukkubus. Sind alle lebenden Spieler verzaubert, gewinnt Sukkubus mit allen Verzauberten und Spiel endet sofort. Mehrere Sukkubi teilen Ziele und kennen einander.
- **Dullahan:** erhält Zielliste, muss nicht selbst alle töten und muss nicht überleben, um Zusatzsieg zu erhalten, wenn alle Ziellisten-Spieler tot sind. Bei eigenem Tod tötet er zufälliges Listenziel. Nach Tod aller Ziele keine Nachtkills.
- **Wild Child:** beginnt Dorf, wählt erste Nacht Idol; wenn Idol stirbt, wird es Wolf. Seher/Orakel-Ergebnisse ändern sich je nach Quelle/Modus; tatsächliche Rolle wird bei Tod immer Wild Child angezeigt.
- **Teleportationsmeister:** nur Kaboom; vertauscht abends zwei Spielerpositionen, kann dadurch Nachbarschafts-/Zielmechaniken beeinflussen. Kann nicht gewinnen.

## Spielmodi: allgemeine Regeln

- Es gibt eine Hauptrotation mit zufälliger Auswahl und abstimmbare/abwählbare Modi.
- Spezialmodi können nur mit `!fgame` direkt gesetzt werden, nicht direkt per Abstimmung.
- Wenn niemand `!game` oder `!vote` nutzt, wird zufällig nach den eingetragenen Häufigkeiten ausgewählt.
- Stimmen erhöhen die Wahrscheinlichkeit; stimmen mindestens 50 % der Spieler für einen Modus, ist dieser garantiert, sofern die Spielerzahl gültig ist.
- Stimmen für Modi außerhalb ihres erlaubten Spielerzahlbereichs werden ignoriert.

## Hauptrotation – dokumentierte Modi

- **Standard:** 6–24 Spieler, Gewicht 40 (ca. 41,24 %). Häufigster Modus. Kultisten ab 7 Spielern, neutraler Verrückter Schamane ab 9, Liebende ab 11, Monster ab 16.
- **Töricht/Foolish:** 8–24, Gewicht 5 (ca. 5,15 %). Narr versucht, Sieg durch Tagesabwahl zu stehlen. Jäger ab 9.
- **Verrückt/Crazy:** 7–24, Gewicht 5. Verrückter Wissenschaftler tötet Nachbarn bei Tod; Kultist ab 12, Rachsüchtiger Geist ab 14. Gunner immer nur eine Kugel.
- **Lykaner/Lycan:** 7–24, Gewicht 5. Viele Lykaner verwandeln sich bei Wolfsangriff in Wölfe, um wenige anfängliche Wölfe auszugleichen.
- **Alea Teure/Aleatoire (Name aus Diktat, Schreibweise nicht bestätigt):** 8–24, Gewicht 5. Viele unsichere/verdeckte Rollen. Zwei Verfluchte Dorfbewohner bei 8; Rachsüchtiger Geist ab 9 oder 10 (Quelle uneinheitlich); Amnesiac und Hexe ab 12; Assassine ab 13; Zeitherr ab 20 oder 21 (uneinheitlich). Veränderte Schamanen-Totemwahrscheinlichkeiten: Tod 20 %, Schutz 40 %, Schweigen 10 %, Verzweiflung 5 %, Pestilenz 5 %, Vergeltung 20 %. Strategie: Dorf sollte verborgen bleiben, wichtige Rollen schützen und keine voreiligen sicheren Behauptungen aufstellen; Wölfe koordinieren sich mit verbündeten Rachsüchtigen Geistern.
- **Alpha:** 10–24, Gewicht 5. Alpha-Wolf kann Rollen beißen und verwandeln; viele Verwandlungen erzeugen spezielle Wolfrollen.
- **NoReveal/Noreveal:** 6–21, Gewicht 1 (ca. 1,03 %). Rollen werden beim Tod nicht enthüllt; Mystic und Wolf-Mystic erhalten Zähl-Informationen.
- **Teufelsdorf/Evil Village:** 6–18, Gewicht 5. Mehrheit Kultisten, die mit Wölfen kooperieren. Enthaltung deaktiviert. Dorf gewinnt durch Eliminierung aller Wölfe; Wolfteam gewinnt durch Eliminierung aller Dorfrollen. Scherge muss nicht sterben. Bei gleichzeitiger Eliminierung von Dorf und Wölfen gewinnt niemand. Quelle enthält weitere, nicht ganz klare Sonderbedingungen für Dorf-Sieg.
- **Charmant:** 6–24, Gewicht 5. Pfeifer verzaubert alle; sobald alle lebenden Spieler verzaubert sind und Tagesbeginn erreicht wird, gewinnt der Pfeifer.
- **Schläfrig/Sleepy:** 8–24, Gewicht 5. Viele Sonderrollen/Verwandlungen; Priester ab 10, Gesegneter Dorfbewohner und Prophet; Dullahan immer dabei. **Neuere Korrektur:** Priester steht nicht mehr garantiert auf der Zielliste des Dullahan, obwohl ältere Strategienotizen das noch behaupten. Beim Tod des Priesters gibt es rollenspezifische Verwandlungen mit jeweils erwähnten 70 %: Seher → Doomsayer, Kultisten → Dämonen, Rachsüchtige Geister → Narren (unklar, ob Rolle ersetzt oder zusätzlich bleibt). Falls Seher zu Doomsayer wird, verwandelt sich Verräter oder, wenn keiner existiert, ein Wolf zum Monster. Details weiter prüfen.
- **Schlammig/Mudkip:** 6–17, Gewicht 5. Jeder Spieler hat eine Zweitrolle; viele Täuschungsmechaniken. Startet tagsüber. Tag endet sofort, wenn alle abgestimmt haben; keine Mehrheit nötig, höchste Stimmenzahl scheidet aus, Gleichstand eliminiert alle Gleichplatzierten, außer wenn das alle Spieler wären – dann niemand stirbt. Doomsayer ab 9, Priester ab 12. Strategien hängen stark von Rollenverteilung ab.
- **Boreal:** 6–24, Gewicht 1. Alle Spieler sind Schamanen oder Wolf-Schamanen. Wolf-Schamanen können nachts nicht töten. Hunger-/Nahrungstotems: normale Schamanen haben Hungerlevel, sterben bei 5; drei nicht aufeinanderfolgende hungrige Nächte bringen Wolf-Sieg. Totemwerte werden am Ende der Nacht verrechnet, alle erhalten pro Nacht +1 Hunger. Exil per Tagesabstimmung. Alle Wolf-Schamanen exiliert → Dorf gewinnt. Exil oder Verhungern normaler Schamanen kann diese zu Rachsüchtigen Geistern machen, begrenzt durch lebende Wolf-Schamanen. Phase 2 beginnt, sobald ein Geist aktiv wird; Geister greifen nur normale Schamanen an, zufällige normale Schamanen erhalten Vergeltungstotem. Wenn nach Nacht 7 die Geister nicht gewonnen haben, gewinnt das Dorf.
- **Wächter:** 7–24, Gewicht 5. Schutzengel und Werkitten; Spiel endet erst, wenn alle Wölfe oder alle Schutzengel tot sind, auch wenn Wölfe zahlenmäßig überlegen sind. Alpha-Wolf ab 12 mit zweitem Schutzengel. Bei 7 Spielern ist Werkitten der einzige Wolf, der für den Seher wie Dorf aussieht; zwei Verfluchte Dorfbewohner bei 7. Ab 8 tatsächlicher Wolf, wodurch Seher unzuverlässiger ist.
- **Maskerade:** 6–24, Gewicht 5. Aus Rollenpaaren wird zufällig eine ausgewählt, Spieler wissen nicht, welche. Beispiel: Schutzengel/Vigilant ab 7; ab 13 kommen beide hinzu. Wild Child ist enthalten und wird bei Tod des Idols zum Wolf. Augur sieht Team vor/nach Verwandlung; Seher-Ergebnis kann bei Wild Child ab 10 Sonderfall sein. Bei 16 Wolf-Schamane statt Schamane.

## Abstimmbare/abwählbare Modi – aktueller Arbeitsstand

### Rapidfire / Schnellfeuer
- 6–24 Spieler, Häufigkeit 0.
- Chaotischer Modus mit vielen Tötungsrollen und Todesketten.
- Kürzere Standard-Tagesphase.
- Sonderregel: Wenn alle verbleibenden Spieler sterben, gewinnen alle statt dass niemand gewinnt.
- Bild zur Rollenverteilung über GitHub geöffnet: Commit `9c8b43e59353737fcf17e7119ed2d4cdd16a3cf3`.
- Bild-URL: https://raw.githubusercontent.com/svdermant/WereWolf/9c8b43e59353737fcf17e7119ed2d4cdd16a3cf3/Rollenverteilung/Rapidfire-Rollenverteilung.png
- Aus dem Bild wurde notiert: kein Augur vor 15 Spielern; Verrückter Wissenschaftler als Hauptrolle, Anzahl steigt ab 18 Spielern auf zwei. Bei Neustart kann Bild erneut über URL geöffnet und vollständig ausgelesen werden.

### Trunkenfeuer / Drunken Fire
- 8–17 Spieler; Häufigkeit noch nicht genannt.
- Benannt nach dem betrunkenen Gunner; viele Schützen und betrunkene Dorfbewohner.
- Bei 16 Spielern bis zu fünf betrunkene Dorfbewohner und neun Schützen.
- Schusswahrscheinlichkeiten für nicht betrunkene Schützen sind modifiziert. Die diktierte Erklärung war unklar („die meisten Kopfschüsse und Wölfe nur entweder Kopfschuss oder Fehlschuss“); nicht interpretieren, bis die Referenz klarer ist.
- Tages- und Nachtphasen kürzer als normal.
- Rollenverteilungstabelle fehlt noch; Nutzer wollte möglicherweise einen GitHub-Bildlink schicken.

## Nächster Schritt

Bei neuer Unterhaltung: Diese Datei zuerst lesen. Dann dem Nutzer kurz bestätigen, dass der Kontext wiederhergestellt wurde, und bei **Trunkenfeuer** weitermachen. Der Nutzer kann einen direkten GitHub-`raw`-Bildlink senden. Keine Uploads verlangen, wenn ein Link ausreicht.

## Weitere noch dokumentierte Rollen (knappe Ergänzung)

- **Hexe:** siehe Wolfrollen.
- **Pfeifer und Sukkubus:** getrennte Rollen; Pfeifer verzaubert über Nachtaktionen bis zu zwei Ziele, Sukkubus durch Besuche. Sukkubus ist nicht der Dämon.
- **Fallen Angel/Fallen Angel:** deutscher Name Gefallener Engel.
- **Win Stealer:** Narr, Jester, Monster, Dämon, Pfeifer u. a. erfüllen individuelle Siegbedingungen.
- Modusnamen aus Spracherkennung wie „Alea Teure“, „Nofreal“, „Lycana“, „Erziehungsberechtigte“, „Betrunkenfeuer“ nicht automatisch in vermeintlich korrekte Namen umwandeln.


