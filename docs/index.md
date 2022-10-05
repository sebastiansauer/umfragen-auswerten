--- 
title: "Umfragen auswerten"
author: "Sebastian Sauer"
date: "2022-10-05 13:45:17"
site: bookdown::bookdown_site
documentclass: book
bibliography: [book.bib, packages.bib]
# url: your book url like https://bookdown.org/yihui/bookdown
# cover-image: path to the social sharing image like images/cover.jpg
description: |
  Umfragen auswerten: Statistische Analyse psychometrisch fundierter Ratingskalen
biblio-style: apalike
#   csl: chicago-fullnote-bibliography.csl
---









# Einleitung




<div class="figure" style="text-align: center">
<img src="img/IMG_0788.JPG" alt="Lustige Tierchen, buntes Treiben: Willkommen beim Auswerten von Umfragedaten" width="100%" />
<p class="caption">(\#fig:unnamed-chunk-1)Lustige Tierchen, buntes Treiben: Willkommen beim Auswerten von Umfragedaten</p>
</div>


Fragebogendaten zu psychologischen Variablen (wie z.B. Extraversion), die wissenschaftlichen Ansprüchen genügen, bezeichnet man als *psychometrische Daten* [vgl. @Steyer1993]. 
Der Analyse psychometrischer Daten kommt große Bedeutung innerhalb der Psychologie (und angrenzender Gebiete wie Marketing) zu. 
Der Grund ist, dass Daten häufig in Form von Fragebogen, psychometrischer Fragebogen, erhoben werden. 
Besonders für Persönlichkeitskonstrukte und Einstellungen, kurz für Fragestellungen der Persönlichkeitspsychologie und, davon abgeleitet, der Diagnostik, 
erfreuen sie sich weiter Verbreitung.


Ziel dieses Beitrags ist es, eine praktische Anleitung für typische (und grundlegende) psychometrischen Analysen mittels R zu geben. 
Dabei soll demonstriert und erläutert werden, welche Analysen und wie durchgeführt werden 
in einer grundständigen psychometrischen Analyse. 
Es wird sowohl der Einzelfall-Diagnostik Rechnung getragen, als auch der Testvalidierung. 
Anders gesagt, dieses Dokument hilft, u.a. folgende Fragen zu beantworten: 
"Wie leistungsfähig war der Applikant im Vergleich zu seiner Referenzgruppe?", 
"Wie ist die Qualität dieses Testverfahrens einzuschätzen?".

*Test* wird hier verstanden sensu @Lienert1998 (S. 14f): 

>   ein wissenschaftliches Routineverfahren zur Untersuchung eines oder mehrerer empirisch abgrenzbarer Persönlichkeitsmerkmale mit dem Ziel einer möglichst quantitativen Aussage über den relativen Grad der individuellen Merkmalsausprägung.


Inhalte werden im Rahmen einer *Fallstudie* erarbeitet; 
d.h. es wird ein echter Datensatz analysiert, wobei einige Hintergründe zu den verwendeten Methoden erläutert werden.







## Hilfe zu R


Eine praktische Einführung zu Datenanalyse bietet [ModernDive](https://moderndive.com/) (auf Englisch); eine umfassende Einführung in Englisch findet sich bei @Sauer2019.


## Zu messendes Konstrukt: Extraversion


@Satow2012 definiert Extraversion wie folgt:
  
  >   Extraversion (E) (Gegenteil: Introversion): Bereits C.G. Jung (1921) hatte beobachtet, dass Menschen entweder eher nach außen (gesellig, gesprächig, abenteuerlustig) oder nach innen orientiert (nachdenklich, in-sich-gekehrt) sind. Aufgrund dieser Beobachtungen hielt Eysenck (1947) Extraversion für einen der drei wesentlichen Persönlichkeitsdimensionen. Spätere Untersuchungen konnten zeigen, dass erfolgreiche Führungskräfte häufig eher extravertiert sind und dass Arbeitsleistung und Arbeitszufriedenheit generell mit Extraversion korrelieren (Judge et al., 2002; Lim \& Ployhart, 2004) – wobei es jedoch auch Ausnahmen gibt. So sind überraschend viele erfolgreiche Unternehmensgründer und besonders innovative Persönlichkeiten wie Bill Gates, Warren Buffett oder Steven Spielberg häufig überraschend introvertiert (Jones, 2006).


## Messinstrument

Es wird ein Datensatz zur Extraversion analysiert. 
Extraversion wurde operationalisiert mit dem Inventar *B5T* von Satow [@satow_b5t_2020]. 
Das Instrument besteht aus 10 Items mit vier Likert-Antwortstufen (von "trifft gar nicht zu" bis "trifft voll und ganz zu") und ist für Forschungszwecke kostenlos nutzbar^[https://www.drsatow.de/tests/persoenlichkeitstest/]:
  
  >    der B5T ist als Paper-Pencil-Version, Excel-Version sowie als Online-Version verfügbar und kann für nichtkommerzielle Forschungs- und für Unterrichtszwecke kostenlos verwendet werden. Der B5T wurde offiziell in die PSYNDEX-Testdatenbank (Tests-Nr. 9006357) und in das elektronische Testarchiv des Leibniz-Zentrums für Psychologische Information und Dokumentation (ZPID) aufgenommen.


An anderer Stelle^[https://www.drsatow.de/cgi-bin/testorder/testorder17_0.pl?t=B5T&s=1] ist zur Lizenz zu lesen:
  
  >   Lizenz: Sie dürfen den Test ausschließlich zu Forschungs- und Unterrichtszwecken einsetzen und übersetzen. Sie müssen die Quelle nennen und ein elektronisches Belegexemplar oder die Quellenangabe (Autor, Titel, Zeitschrift/Buch, Erscheinungsjahr) Ihrer Veröffentlichung an mailATdrsatow.de senden.


Zur Reliabilität berichtet @Satow2012, dass Cronbachs Alpha bei .87 liege, was ein guter Wert ist. Machen wir diese Variable für R verfügbar:


```r
extra_alpha <- .87
```



Der Test hat einige Validierungsstudien erfahren, allerdings ist die Qualität dieser Studien zum Teil fraglich, da es sich um "graue" Literatur handelt, also Literatur, die nicht öffentlich (einfach) zugänglich ist. Außerdem gibt es kaum Fachartikel, die ein Blind-Begutachtungsverfahren durchlaufen hätten; die meisten Studien zum B5T basieren auf Abschlussarbeiten; auch einige von FOM-Studierenden sind zu finden.

Die Items zum Test sind über die Webseite des Anbieters abrufbar^[https://www.psychomeda.de/online-tests/persoenlichkeitstest.html].


## R-Pakete

Folgende R-Pakete werden für diesen Kurs benötigt. 
Bitte stellen Sie sicher, dass Sie diese Pakete vor Beginn der Analyse installiert haben. 
Sie installieren ein R-Paket mit dem Befehl `install.packages(name_des_pakets)`.



```r
library(mosaic)  # Statistik allgemein
library(tidyverse)  # Datenjudo
library(sjmisc)  # Deskriptive Statistik
# library(apa)  # Statistiken nach APA formatieren
library(mice)  # Hilfen für fehlende Werte
library(devtools)  # Pakete von Github installieren
library(ggstatsplot)  # Visualisierung
library(janitor)  # Daten aufräumen
library(ggpubr)  # Visualisierung
library(psych)  # psychometrische Analyse
```





## Vertiefung: Infos zu Paketen

Sie fragen sich, was das Paket `praise()`, `fortunues()` oder `R_for_superheros()`^[noch nicht entwickelt] für Sie bereithält? Mit folgender Funktion bekommen Sie die Hilfe-Seiten eines Pakets angezeigt.


```r
help(package = "apa") 
```



## Daten

### Daten - aus Paket `pradadata`

Die Daten können über mehrere Weg abgerufen werden. Eine Möglichkeit bietet das R-Paket `pradadata`^[https://github.com/sebastiansauer/pradadata], da es auf Github^[https://github.com/] zu finden ist, muss zuerst ein Paket verfügbar sein, dass R-Paket von dort aus installiert. Dazu verwenden wir das Paket `devtools`; wie jedes Paket muss es zuerst installiert sein:
  

```r
install.packages("devtools")  # nur einmalig
```


Dann können wir das Paket installieren:
  
  

```r
install_github("sebastiansauer/pradadata")
```


laden und daraus den Datensatz zur Extraversion:
  

```r
library("pradadata")
data(extra)
```


Betrachten wir den Datensatz


```r
inspect(extra)
```


### Daten - via Webseite

*Alternativ* zur Installation via R-Paket `pradadata` stehen die Daten  unter diesem Link zum Herunterladen bereit:
  

```r
data_url <- "https://raw.githubusercontent.com/sebastiansauer/modar/master/datasets/extra.csv"
extra <- read_csv(data_url)
```


### Umfrage zu den Daten

Die Daten wurden [mit dieser Umfrage](https://forms.gle/c44Rx5H443Z4iC5s6) erhoben.



## Technische Hinweise

Sessioninfo:





- Datum: 2022-07-27
- R-Version: R version 4.2.0 (2022-04-22)
- Betriebssystem: 

