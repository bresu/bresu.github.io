+++
title= "Scraping Austria - Eine Sisyphus-Aufgabe"
author = "Bresu"
description = "Vom Versuch, die Websites aller österr. Gemeinden zu finden"
date= 2020-06-10
tags=["python","scraping","webcrawling"]
+++
![Hallstatt-Foto](/pics/scraping_austria/headerImage.png "Hallstatt")
Servus! Die Inspiration für dieses Projekt kommt von diesem Youtube-Video[^1]. Darin zeigt ein sehr
sympathischer Typ, wie er mit ein wenig Aufwand tausende E-Mails an zig Botschaften in der ganzen
Welt verschickt. In der Mail fragt er die Botschaft nach der Flagge des Landes.
Das Video hat mir dann so gut gefallen, dass ich mich dazu entschlossen habe, auch sowas zu machen.

Botschaften scrapen und anschreiben konnte ich ja definitiv nicht mehr bringen, also dachte ich kurz
nach, was man noch so alles scrapen könnte (was im besten Fall noch gar nicht gescraped wurde).

Dann kam mir die Idee:

# Alle Websiten aller österreichischen Gemeinden + deren E-Mail!

Mit diesem Ziel im Visier begann ich damit, einen Plan auszudenken. Der lautete dann ca. so:

1. Liste aller Gemeinden finden
2. Mögliche Domains anhand des Gemeinde-Namens generieren
3. Schauen welche Domains existieren
4. Verbleibende Domains auf Authentizität prüfen (Keine Domain-Registrierungen und schon gar keine
Business-Sites!)

_Let's go!_

# Die Suche nach der Liste - Statistik Austria Returns

Anfangs dachte ich, eine aktuelle Liste aller österreichischen Gemeinden wäre möglicherweise schwer
aufzufinden - aber im Gegenteil! Statistik Austria hat halt wirklich ALLES (Man muss nur lange und
gründlich suchen!). So bin ich dann auf diese Tabellen[^2] gestoßen, die jede Gemeinde und deren
zugehörige Postleitzahl(PLZ) und **Gemeindekennziffer(GKZ)** beinhaltet.

:monocle_face: _Was zum F*ck ist eine Gemeindekennziffer?_

![Confused Dog](/pics/scraping_austria/confusedDoggo.webp)

Gute Frage! Es handelt sich dabei um eine eindeutige Nummer, die jeder Gemeinde zugeordnet wird, sehr ähnlich wie eine PLZ - aber eben keine PLZ. Die GKZ besteht aus 5 Ziffern, die PLZ bekanntlich nur aus 4. PLZs sind alt und nicht sehr schön, man kann aus ihnen nicht eindeutig ablesen, in welchem Bundesland die Gemeinde liegt. 

Ein Beispiel:

* 2384 ist die PLZ von Breitenfurt, Niederösterreich
* 3012 ist die PLZ für Wolfsgraben, auch Niederösterreich, nur 4km von Breitenfurt entfernt

Solche Überschneidungen kommen auch noch mit Süd-Tirol und Kärnten, sowie Vorarlberg und dem anderen Teil von Tirol vor.

Man sieht: PLZs sind hässlich, vor allem sind sie nicht gut, um Verwaltungsgrenzen (wie es die Bundesländer sind) für eine Gemeinde zu definieren. Deswegen hat sich irgendwer die GKZ ausgedacht!

# GKZ love :heart:

Die 5 Zahlen der GKZ funktionieren wie folgt:

```
1. Stelle: Bundesland (Alphabetisch)
2 + 3. Stelle: Bezirk (Alphabetisch)
4 + 5. Stelle: Gemeinde-Code (Alphabetisch)
```

Zur Veranschaulichung, my hometown[^3] _Bisamberg_ mit der GKZ

**3 12 01**

- 3: Niederösterreich
- 12: Bezirk Korneuburg
- 01: Bisamberg

Die PLZ kann einpacken gehen, GKZ ist der neue heiße Scheiß!

# Kombinatorik & Permutation

Es gibt in Österreich ca. 2100 Gemeinden. Das ist eine relativ große Zahl, vor allem wenn man pro Gemeinde mehrere Domains checken muss. Das schaut dann ungefähr so aus:

```python
    gemeinde_name = "Bisamberg".lower()

    list_possible_domains = [
    
    # Official Gov-Domain
    "http://www.{gemeinde}.gv.at".format(gemeinde=gemeinde_name)
    
    # Nicht jeder machts anscheinend offiziell
    "http://www.{gemeinde}.at".format(gemeinde=gemeinde_name)
    
    # Super Patrioten haben noch(!) eine Domain-Ending [.tirol, .bgld und .ooe]
    "http://www.{gemeinde}.tirol.gv.at".format(gemeinde=gemeinde_name)

    # und so weiter
    ]
```

Dieser Herangehensweise eignete sich hervorragend für ein-wörtrige Gemeinden, aber für Gemeinden mit dem Format `"x an der y"`, `"x bei y"` oder `"St. x"` wird's schwieriger. Veranschaulicht habe ich es am Beispiel _Krems an der Donau_

```python
unschoener_gemeinde_name = "Krems an der Donau".lower()
splitted_gemeinde_name = unschoener_gemeinde_name.split(" ") 
# ['krems','an','der','donau'] 

# Dann hab ich ein paar richtig unschöne Funktionen geschrieben um diese Liste zu permutieren
# Die Details erspare ich euch, aber das Ergebnis schaut dann ca. so aus

list_possible_domains = [

    "http://www.krems.gv.at"
    "http://www.krems-an-der-donau.gv.at"
    "http://www.krems-donau.gv.at"
    "http://www.krems.at"
    "http://www.krems-an-der-donau.at"
    "http://www.krems-donau.at"
    # und so weiter...
]
```

# Bist du da?! 

Nachdem ich eine Liste von Links generiert hatte, bin ich die dann einfach einen nach dem anderen durchgegangen. Zuerst waren es nur einfache GET-Requests, um überhaupt die Existenz der Domain zu bestätigen.
```python
import requests

for domain in list_possible_domains:
    try:
        rsp = requests.get(domain)
        if rsp.status_code == 200:
            # domain abspeichern
    except:
        continue
```
# False positives

Nachdem ich so ca. 10.000 Domains auf ihre Existenz überprüft hatte, wurde es ernster.
Es gab nämlich Websiten, die zwar existent waren, jedoch dann einer Firma (Meistens im Bereich Tourismus ansässig) gehörten, oder eben nur "geparkt" waren. Dann bin ich noch einmal alle existenten Sites durch gegangen und habe währenddessen nach Keywords wie "Rathaus" oder "Gemeindeamt" Ausschau gehalten. Aber auch nach all diesen Checks wird es mit ziemlicher Sicherheit noch immer falsche Einträge in dem Datensatz geben. 

> Ohne E-Mail, ohne Liebe,
> ist das Leben wirklich trübe :e-mail:  [^4]

Als ich dann endlich eine vollständige Liste mit den richtigen Domains hatte, ging das Spiel von vorne los. Anstatt jedoch nach Keywords zu suchen, war ich jetzt auf der Jagd nach E-Mailadressen. Dazu musste ich meistens die "Kontakt"-oder "Impressum"-Seite finden. Dafür habe ich dann im Endeffekt Selenium[^5] verwendet, da viele Seiten nur mit Javascript-Rendering ihre E-Mail preisgeben. 
Dank Selenium war dieser ganze Prozess recht schnell gecodet, dafür musste aber auch mehr rechenpower fürs Rendern der Website her.

Nachdem ich alle E-Mails gescraped hatte, schaute ich nochmal schnell über den Datensatz drüber (zB ob E-Mails doppelt vorgekommen sind weil Webmaster etc.) und musste noch ein bisschen mit Encodings rumschustern. 
Dann war ich endlich fertig!

Ich habe durch dieses Projekt einiges über das Web-Scraping gelernt und es hat auch wirklich Spaß gemacht! 

Den Datensatz gibt's als CSV, JSON und XLSX auf meinem [Github](https://github.com/bresu/oe_gemeinden)


[^1]: Super [YT-Video](https://www.youtube.com/watch?v=Jbix9y8iV38)
[^2]: Danke Statistik Austria für [das hier]()
[^3]: [:musical_note: My Hometown](https://www.youtube.com/watch?v=77gKSp8WoRg)
[^4]: Erwin Koch
