
# Itemanalyse



## R-Pakete


In diesem Kapitel benötigen wir folgende R-Pakete:



```r
library(tidyverse)  # Datenjudo
library(sjmisc)  # recode
library(psych)  # Itemanalyse
```







## Daten



```r
data_url <- "https://raw.githubusercontent.com/sebastiansauer/modar/master/datasets/extra.csv"
extra <- read_csv(data_url)
#> Rows: 826 Columns: 34
#> ── Column specification ────────────────────────────────────
#> Delimiter: ","
#> chr  (8): timestamp, code, sex, presentation, clients, e...
#> dbl (25): i01, i02r, i03, i04, i05, i06r, i07, i08, i09,...
#> lgl  (1): i21
#> 
#> ℹ Use `spec()` to retrieve the full column specification for this data.
#> ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.
```

## Einführung

Unter *Itemanalyse* versteht man einen wesentlichen Teil der Validierung eines psychometrischen Tests. Zentrales Element ist die empirische Prüfung der Güte der Items eines Instruments. Entsprechend kann man von der *internen Validität* eines *Tests*^[nicht zu verwechseln mit der internen Validität einer Studie] sprechen. Üblicherweise werden dabei die folgenden Aspekte untersucht:

1. Itemschwierigkeit
2. Itemtrennschärfe
3. Reliabilität (Interne Konsistenz; z.B. mittels Cronbachs Alpha)
3. Faktorenstruktur


Die Analyse der Faktorenstruktur ist in dieser Analyse nicht umgesetzt.


Die ersten drei Punkte werden durch eine Funktion, `alpha()` aus dem Paket `psych` abgedeckt. Dieser Funktion übergibt man eine Tabelle mit Items; man bekommt einige Item-Statistiken zurück. Zuerst erstellen wir eine Tabelle, `extra_items` nur mit den Items.


```r
extra_items <- extra %>% 
  select(i01:i10) %>% 
  drop_na()
```


Dann führen wir `alpha()` mit dieser Tabelle aus: 


```r
psych::alpha(extra_items)
#> 
#> Reliability analysis   
#> Call: psych::alpha(x = extra_items)
#> 
#>   raw_alpha std.alpha G6(smc) average_r S/N   ase mean   sd
#>       0.77      0.79     0.8      0.27 3.7 0.012  2.9 0.45
#>  median_r
#>      0.27
#> 
#>     95% confidence boundaries 
#>          lower alpha upper
#> Feldt     0.75  0.77   0.8
#> Duhachek  0.75  0.77   0.8
#> 
#>  Reliability if an item is dropped:
#>      raw_alpha std.alpha G6(smc) average_r S/N alpha se
#> i01       0.74      0.75    0.76      0.25 3.0    0.014
#> i02r      0.75      0.76    0.77      0.26 3.2    0.013
#> i03       0.80      0.80    0.81      0.31 4.1    0.011
#> i04       0.74      0.75    0.76      0.25 3.0    0.014
#> i05       0.73      0.75    0.75      0.25 3.0    0.014
#> i06r      0.75      0.76    0.77      0.26 3.2    0.013
#> i07       0.75      0.76    0.78      0.27 3.3    0.013
#> i08       0.76      0.77    0.78      0.27 3.3    0.013
#> i09       0.76      0.77    0.79      0.28 3.4    0.013
#> i10       0.77      0.78    0.79      0.29 3.6    0.012
#>      var.r med.r
#> i01  0.019  0.25
#> i02r 0.019  0.27
#> i03  0.015  0.30
#> i04  0.017  0.27
#> i05  0.017  0.24
#> i06r 0.021  0.28
#> i07  0.022  0.27
#> i08  0.023  0.28
#> i09  0.022  0.29
#> i10  0.020  0.29
#> 
#>  Item statistics 
#>        n raw.r std.r r.cor r.drop mean   sd
#> i01  802  0.69  0.70  0.67   0.59  3.4 0.68
#> i02r 802  0.63  0.63  0.59   0.50  3.1 0.80
#> i03  802  0.34  0.32  0.17   0.15  1.8 0.90
#> i04  802  0.68  0.69  0.68   0.57  3.2 0.73
#> i05  802  0.71  0.71  0.70   0.60  3.1 0.77
#> i06r 802  0.60  0.61  0.55   0.48  2.9 0.76
#> i07  802  0.60  0.60  0.54   0.48  2.9 0.73
#> i08  802  0.58  0.57  0.49   0.43  2.9 0.87
#> i09  802  0.52  0.54  0.45   0.40  3.4 0.70
#> i10  802  0.49  0.47  0.38   0.32  2.2 0.88
#> 
#> Non missing response frequency for each item
#>         1    2    3    4 miss
#> i01  0.01 0.08 0.45 0.45    0
#> i02r 0.03 0.18 0.45 0.35    0
#> i03  0.45 0.32 0.18 0.05    0
#> i04  0.01 0.15 0.44 0.41    0
#> i05  0.02 0.21 0.47 0.30    0
#> i06r 0.04 0.20 0.54 0.21    0
#> i07  0.02 0.22 0.54 0.22    0
#> i08  0.06 0.23 0.43 0.28    0
#> i09  0.01 0.09 0.41 0.48    0
#> i10  0.23 0.43 0.26 0.08    0
```

Hilfe zur recht ausführlichen Ausgabe bekommt man `?alpha`.

Das `raw_alpha` ist das für gewöhnliche berichtete Alpha-Koeffizient ($\alpha$) berechnet auf Basis der Kovarianzen, nicht Korrelationen), als Schätzwert für die Reliabilität im Sinne der internen Konsistenz der Skala.  Außerdem wird ein Konfidenzbereich für den Alpha-Koeffizienten angegeben.  Interessant ist auch die Frage, ob bzw. wie sich die Reliabilität der Skala ändert, wenn man ein bestimmtes Items entfernt: Wird die Reliabilität besser nach Entfernen des Items, so ist das ein Hinweis, dass das Item umformuliert oder entfernt werden sollte. Im vorliegenden Fall ist das Item `i03` ein Kandidat zur Überarbeitung.

Darüber hinaus wird Guttmans $\lambda_6$ (Lambda-6) berichtet, der in dieser Analyse aber nicht weiter betrachtet wird. `average_r` ist die mittlere Korrelation aller Itempaare ("Interitemkorrelation"); dieser Koeffizient misst, wie stark die paarweise Korrelation der Items untereinander ist. Bei der Schwierigkeit ist es wünschenswert, dass ein breiter Bereich abgedeckt wird, also einige Items leicht und andere schwer sind, damit Personen mit geringer und hoher Ausprägung in der latenten Fähigkeit^["Fähigkeit" wird hier im breiten Sine verwendet, auch für Persönlichkeitskonstrukte wie Extroversion.] jeweils Items mit passender Schwierigkeit auffinden.


`mean` gibt den Mittelwert der Skala an (über alle Items); das ist ein Hinweis zur "Gesamtschwierigkeit" der Skala.Weiter unten in der Ausgabe wird für jedes einzelne Item die Schwierigkeit ausgegeben; `sd` ist die zugehörige Streuung. `S/N` steht für Signal-Noise-Ratio; dieser Koeffizient wird hier nicht weiter analysiert.

Die Itemtrennschärfe (synonym: Part-Whole-Korrelation) wird pro Item mit `raw.r` angezeigt.





## Literaturempfehlungen

@Buhner2011 bietet eine gute Einführung in die Analyse psychometrischer Daten, allerdings mit SPSS-Syntax, keine R-Syntax.



