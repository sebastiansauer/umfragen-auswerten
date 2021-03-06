
# Fallstudie Popup-Stores








## R-Pakete


In diesem Kapitel benötigen wir folgende R-Pakete:



```r
library(tidyverse)  # Datenjudo
library(sjmisc)  # Datenhausmeister
library(janitor)  # Auch ein Hausmeister
library(easystats)  # Stats made easy :-)
library(flextable)  # html Tabellen, schick
```



## Einleitung

In einer Studie untersuchte Frau Prof. Dr. Klug Ursachen von Entscheidungen im Rahmen von Einstellungen und Verhalten bei Pop-up Stores.

U.a. wurden folgende Fragen untersucht:

- Welchen (kausalen) Effekt hat die Distanz zum und Lage des Pop-up-Stores hinsichtlich der AV?
- Wie stark ist der Moderatoreffekt von Variablen wie z.B. Innovationsorientierung, Shopping-Relevnaz und Soziodemografika?
- Ist ein Effekt auf Einstellung, Verhaltensintention und Verhalten zu beobachten?


Es handelt sich um ein experimentelles Design mit zwei Faktoren (Lage und Distanz) mit jeweils 3 Stufen.

Ein Teil der Daten ist (nur) für Lehrzwecke freigeben.


Folgende Materialien stehen bereit:

- [Roh-Datensatz](https://raw.githubusercontent.com/sebastiansauer/Lehre/main/data/popupstore/data/d1a.csv), $n=90$, Gruppen 1-3
- [Studienkonzept](https://github.com/sebastiansauer/Lehre/blob/main/data/popupstore/materials/0_Konzept_Pop-up_Location_2018-10-30.pdf)
- [Frageobgen](https://github.com/sebastiansauer/Lehre/blob/main/data/popupstore/materials/A_FB_Pop-up_Location_neu_mitKonstrukten.pdf)
- [Codebook](https://github.com/sebastiansauer/Lehre/blob/main/data/popupstore/docs/codebook.xlsx)


## Aufgaben


1. Entfernen Sie leere Zeilen und Spalten aus dem Datensatz. Tipp: Nutzen Sie das R-Paket `{{janitor}}`.
2. Entfernen Sie konstante Variablen. Tipp: Nutzen Sie das R-Paket `{{janitor}}`.
3. Prüfen Sie auf Duplikate, d.h. doppelte Zeilen. Tipp: Nutzen Sie das R-Paket `{{janitor}}`.
4. Entfernen Sie alle Spalten, die Zeit-Objekte enthalten.
5. Ersetzen Sie leere Zellen sowie Zellen mit Inhalt `"N/A"` durch `NA`, also durch einen fehlenden Wert. Tipp: `na_if()` aus `{{dplyr}}`.
6. Rekodieren Sie die Anker (Labels) der Ratingskala in Zahlen und zwar von -3 bis +3! Tipp: Nutzen Sie `recode()` aus `{{dplyr}}`.
7. Berechnen Sie Spalten-Mittelwerte für alle Konstrukte, die die Ratingskala verwenden. Tipp: Nutzen Sie `rowwise()` und `c_across()`.
8. Exportieren Sie die Daten als CSV- und als XLSX-Datei. Tipp:  Nutzen Sie das R-Paket `{{rio}}`.
9. Berechnen Sie Cronbachs Alpha!  Tipp: Nutzen Sie das R-Paket `{{psych}}`.
10. Berechnen Sie gängige deskriptive Statistiken für die Mittelwerte der Konstrukte.  Tipp: Nutzen Sie das R-Paket `{{easystats}}` und daraus die Funktion `describe_distribution()`.
11. Importieren Sie diese Tabelle nach Word!  Tipp: Nutzen Sie das R-Paket `{{flextable}}`.
12. Kurz vor Abgabe Ihres Studienberichts fällt Ihnen ein, dass Sie vergessen haben, das Item `v033` zu invertieren. Das möchten Sie noch schnell nachholen. Tipp: Einfaches Rechnen.


## Lösungen

### Ad 1

Daten laden:


```r
d_url <- "https://raw.githubusercontent.com/sebastiansauer/Lehre/main/data/popupstore/data/d1a.csv"

d1a <- read_csv(d_url)

dim(d1a)
#> [1]  90 196
```

Die Tabelel umfasst 90 Zeilen und 196 Spalten.

Leere Zeilen/Spalten entfernen:


```r
library(janitor)
d2 <-
  d1a %>% 
  remove_empty()
```


### Ad 2



```r
library(janitor)
d3 <-
  d2 %>% 
  remove_constant()
```


### Ad 3


```r
d3 %>% 
  get_dupes()
#> # A tibble: 0 × 84
#> # … with 84 variables: v001 <dbl>, v002 <dttm>, v003 <dbl>,
#> #   v005 <dbl>, v006 <dttm>, v007 <dttm>, v008 <chr>,
#> #   v009 <chr>, v010 <chr>, v011 <chr>, v012 <chr>,
#> #   v013 <chr>, v014 <chr>, v015 <chr>, v016 <chr>,
#> #   v017 <chr>, v018 <chr>, v019 <chr>, v020 <chr>,
#> #   v021 <chr>, v022 <chr>, v023 <dbl>, v033 <chr>,
#> #   v034 <chr>, v035 <chr>, v036 <chr>, v037 <chr>, …
```


Keine Duplikate zu finden.


### Ad 4

```r
d4 <-
  d3 %>% 
  select(-c(v002, v006, v007))
```



### Ad 5
.

```r
d4 %>% 
  mutate(v001 = na_if(v001, ""),
         v001 = na_if(v001, "N/A"))
#> # A tibble: 90 × 80
#>     v001  v003      v005 v008  v009  v010  v011  v012  v013 
#>    <dbl> <dbl>     <dbl> <chr> <chr> <chr> <chr> <chr> <chr>
#>  1   794    25    1.03e9 2a02… <NA>  Ja    Ja    Nein  Nein 
#>  2   146    25    1.38e9 2a02… <NA>  Ja    Ja    Nein  Nein 
#>  3   459     4    3.55e8 2003… http… Nein  Ja    Nein  Ja   
#>  4   324    25    9.95e8 134.… http… Ja    Ja    Nein  Nein 
#>  5   257    25    6.89e8 2003… http… Nein  Nein  Nein  Ja   
#>  6   182    25    1.70e9 2003… http… Nein  Nein  Nein  Nein 
#>  7    95    25    1.70e9 93.1… http… Ja    Nein  Ja    Nein 
#>  8   355    25    1.60e9 2a02… http… Ja    Nein  Nein  Ja   
#>  9   570    25    8.10e8 2003… http… Nein  Nein  Nein  Nein 
#> 10   173    25    7.67e7 134.… http… Ja    Nein  Nein  Nein 
#> # … with 80 more rows, and 71 more variables: v014 <chr>,
#> #   v015 <chr>, v016 <chr>, v017 <chr>, v018 <chr>,
#> #   v019 <chr>, v020 <chr>, v021 <chr>, v022 <chr>,
#> #   v023 <dbl>, v033 <chr>, v034 <chr>, v035 <chr>,
#> #   v036 <chr>, v037 <chr>, v038 <chr>, v039 <chr>,
#> #   v040 <chr>, v041 <chr>, v042 <chr>, v043 <chr>,
#> #   v044 <chr>, v045 <chr>, v046 <chr>, v047 <chr>, …
```

Und so weiter für alle Spalten ...

Puh, geht das nicht schlauer?

Ja, geht. Hier ein kleiner Trick:



```r
d5 <-
  d4 %>% 
  map_df(na_if, "") %>% 
  map_df(na_if, "N/A")
```


Mit `map_df()` kann man eine Funktion, hier `na_if()` auf jede Spalte der Tabelle (hier: `d5`) anwenden.
Als Ergebnis dieses "Funktions-Mapping" soll wieder eine Tabelle - daher `map_df` zurückgegeben werden.

Mal ein Check: 
Die Anzahl der fehlenden Werte müsste sich jetzt  erhöht haben im Vergleich zur letzten Version des Datensatz, `d4`:


```r
sum(is.na(d4))
#> [1] 1806
```


```r
sum(is.na(d5))
#> [1] 1893
```


Hm, g.ar nicht so viele mehr. Aber grundsätzlich hat es funktioniert :-)


Sie brauchen `map_df()` *nicht* zu verwenden. Es geht auch ohne. Mit `map_df()` ist es nur komfortabler.


### Ad 6

Die Item-Positionen, wann also die Items der Ratingskala beginnen und wann (an welcher Spaltenposition) sie enden,
ist im Fragebogen ersichtlich.


```r
d5 %>% 
  mutate(v033_r = recode(v033,
      "lehne voll und ganz ab" = -3,
      "lehne ab" = -2,
      "lehne eher ab" = -1,
      "weder/noch" = 0,
      "stimme eher zu" = 1,
      "stimme zu" = 2,
      "stimme voll und ganz zu" = 3,
      .default = NA_real_  # Ansonsten als NA und zwar NA vom Typ "reelle Zahl"
  )) %>% 
  select(v001, v033, v033_r) %>% 
  head(10)
#> # A tibble: 10 × 3
#>     v001 v033                    v033_r
#>    <dbl> <chr>                    <dbl>
#>  1   794 stimme voll und ganz zu      3
#>  2   146 stimme eher zu               1
#>  3   459 <NA>                        NA
#>  4   324 stimme eher zu               1
#>  5   257 lehne eher ab               -1
#>  6   182 stimme zu                    2
#>  7    95 stimme eher zu               1
#>  8   355 stimme zu                    2
#>  9   570 stimme eher zu               1
#> 10   173 lehne eher ab               -1
```

Das hat also funktioniert. Aber das jetzt für alle Spalte zu übernehmen,
puh, viel zu langweilig. 
Gibt's da vielleicht einen Trick?

Ja, gibt es.



```r
d6 <-
  d5 %>%
  mutate(across(
    .cols = c(v033:v056, v087:v104),
    .fns = ~ recode(.,
      "lehne voll und ganz ab" = -3,
      "lehne ab" = -2,
      "lehne eher ab" = -1,
      "weder/noch" = 0,
      "stimme eher zu" = 1,
      "stimme zu" = 2,
      "stimme voll und ganz zu" = 3,
      .default = NA_real_  # Andere Wete als NA (Fehlende Werte) vom Typ "reelle Zahl" kennzeichnen
    )
  ))
```


Mit `across()` kann man eine Funktion (oder mehrere), `.fns`, über mehrere Spalten, `.cols` anwenden,
hier wenden wir `recode()` auf alle Spalten der Ratingskala an.



### Ad 7



```r
d7 <-
  d6 %>%
  rowwise() %>%  # Zeilenweise arbeiten im Folgenden
  mutate(
    exp_avg = mean(c_across(v033:v039), na.rm = TRUE),
    neu_avg = mean(c_across(v040:v042), na.rm = TRUE),
    att_avg = mean(c_across(v043:v047), na.rm = TRUE),
    ka_avg = mean(c_across(v048:v053), na.rm = TRUE), 
    wom_avg = mean(c_across(v054:v056), na.rm = TRUE),
    innp_avg = mean(c_across(v087:v092), na.rm = TRUE),
    imp_avg = mean(c_across(v093:v096), na.rm = TRUE),
    hedo_avg = mean(c_across(v097:v100), na.rm = TRUE),
    sho1_avg = mean(c_across(v101:v104), na.rm = TRUE)
  ) %>%
  relocate(ends_with("_avg"), .after = v008)  # wir verschieben alle Spalten, die mit `_avg` enden nach vorne
```


`c_across()` ist wie `c()`. Allerdings funktioniert `c()` leider *nicht* für zeilenweise Operationen.
Daher braucht es einen Freund, der das kann, `c_across()`.



### Ad 8



```r
library(rio)
export(d7, file = "d7.csv")
export(d7, file = "d7.xlsx")
```




### Ad 9


```r
library(psych)

d7 %>% 
  select(v087:v092) %>% 
  alpha(title = "Skala Innovationsorientierung")
#> 
#> Reliability analysis  Skala Innovationsorientierung  
#> Call: alpha(x = ., title = "Skala Innovationsorientierung")
#> 
#>   raw_alpha std.alpha G6(smc) average_r S/N  ase  mean  sd
#>       0.87      0.88    0.88      0.54   7 0.02 -0.25 1.3
#>  median_r
#>      0.52
#> 
#>  lower alpha upper     95% confidence boundaries
#> 0.83 0.87 0.91 
#> 
#>  Reliability if an item is dropped:
#>      raw_alpha std.alpha G6(smc) average_r S/N alpha se
#> v087      0.84      0.84    0.83      0.52 5.4    0.026
#> v088      0.83      0.83    0.83      0.50 5.0    0.028
#> v089      0.87      0.87    0.87      0.57 6.5    0.022
#> v090      0.84      0.84    0.84      0.51 5.1    0.028
#> v091      0.87      0.87    0.87      0.57 6.5    0.023
#> v092      0.87      0.87    0.87      0.58 6.9    0.022
#>      var.r med.r
#> v087 0.013  0.51
#> v088 0.015  0.51
#> v089 0.024  0.55
#> v090 0.022  0.51
#> v091 0.018  0.51
#> v092 0.018  0.55
#> 
#>  Item statistics 
#>       n raw.r std.r r.cor r.drop  mean  sd
#> v087 61  0.83  0.83  0.82   0.73  0.16 1.8
#> v088 61  0.87  0.87  0.87   0.80  0.49 1.5
#> v089 61  0.74  0.73  0.65   0.60 -0.75 1.8
#> v090 61  0.86  0.86  0.83   0.78 -0.80 1.7
#> v091 61  0.71  0.73  0.65   0.60  0.00 1.4
#> v092 61  0.70  0.70  0.62   0.57 -0.59 1.6
#> 
#> Non missing response frequency for each item
#>        -3   -2   -1    0    1    2    3 miss
#> v087 0.07 0.15 0.20 0.08 0.26 0.15 0.10 0.32
#> v088 0.02 0.11 0.13 0.18 0.31 0.15 0.10 0.32
#> v089 0.13 0.30 0.21 0.15 0.05 0.10 0.07 0.32
#> v090 0.21 0.18 0.18 0.20 0.11 0.08 0.03 0.32
#> v091 0.07 0.10 0.16 0.25 0.30 0.13 0.00 0.32
#> v092 0.15 0.16 0.18 0.25 0.16 0.10 0.00 0.32
```


### Ad 10


```r
library(easystats)

d7 %>% 
  select(ends_with("_avg")) %>% 
  describe_distribution()
#> Variable |  Mean |   SD |  IQR |         Range | Skewness |  Kurtosis |  n | n_Missing
#> --------------------------------------------------------------------------------------
#> exp_avg  |  0.90 | 1.12 | 1.57 | [-1.86, 3.00] |    -0.45 |     -0.16 | 76 |        14
#> neu_avg  |  1.22 | 1.25 | 1.33 | [-2.67, 3.00] |    -0.96 |      0.79 | 70 |        20
#> att_avg  |  1.04 | 1.13 | 1.20 | [-2.60, 3.00] |    -1.16 |      1.93 | 68 |        22
#> ka_avg   |  0.91 | 1.20 | 1.21 | [-2.17, 3.00] |    -1.07 |      0.54 | 66 |        24
#> wom_avg  |  0.31 | 1.16 | 1.25 | [-2.33, 3.00] |     0.35 |      0.23 | 64 |        26
#> innp_avg | -0.25 | 1.28 | 1.50 | [-2.83, 2.67] |     0.07 |     -0.28 | 61 |        29
#> imp_avg  | -0.28 | 1.18 | 1.50 | [-3.00, 2.50] |    -0.32 | -7.40e-03 | 61 |        29
#> hedo_avg |  0.34 | 1.40 | 1.50 | [-3.00, 3.00] |    -0.69 |      0.40 | 60 |        30
#> sho1_avg | -0.80 | 1.51 | 2.75 | [-3.00, 2.25] |     0.28 |     -0.85 | 60 |        30
```


### Ad 11


Es gibt mehrere Wege, das Ziel zu erreichen. 
Einer sieht so aus.



```r
library(flextable)

flex1 <- 
  d7 %>% 
  select(ends_with("_avg")) %>% 
  describe_distribution() %>% 
  flextable()

flex1
```

```{=html}
<template id="e0497cff-89bc-4612-a32a-a20581e10e5d"><style>
.tabwid table{
  border-spacing:0px !important;
  border-collapse:collapse;
  line-height:1;
  margin-left:auto;
  margin-right:auto;
  border-width: 0;
  display: table;
  margin-top: 1.275em;
  margin-bottom: 1.275em;
  border-color: transparent;
}
.tabwid_left table{
  margin-left:0;
}
.tabwid_right table{
  margin-right:0;
}
.tabwid td {
    padding: 0;
}
.tabwid a {
  text-decoration: none;
}
.tabwid thead {
    background-color: transparent;
}
.tabwid tfoot {
    background-color: transparent;
}
.tabwid table tr {
background-color: transparent;
}
</style><div class="tabwid"><style>.cl-07ac4498{}.cl-07a5cd98{font-family:'Helvetica';font-size:11pt;font-weight:normal;font-style:normal;text-decoration:none;color:rgba(0, 0, 0, 1.00);background-color:transparent;}.cl-07a5ea12{margin:0;text-align:left;border-bottom: 0 solid rgba(0, 0, 0, 1.00);border-top: 0 solid rgba(0, 0, 0, 1.00);border-left: 0 solid rgba(0, 0, 0, 1.00);border-right: 0 solid rgba(0, 0, 0, 1.00);padding-bottom:5pt;padding-top:5pt;padding-left:5pt;padding-right:5pt;line-height: 1;background-color:transparent;}.cl-07a5ea1c{margin:0;text-align:right;border-bottom: 0 solid rgba(0, 0, 0, 1.00);border-top: 0 solid rgba(0, 0, 0, 1.00);border-left: 0 solid rgba(0, 0, 0, 1.00);border-right: 0 solid rgba(0, 0, 0, 1.00);padding-bottom:5pt;padding-top:5pt;padding-left:5pt;padding-right:5pt;line-height: 1;background-color:transparent;}.cl-07a632f6{width:54pt;background-color:transparent;vertical-align: middle;border-bottom: 0 solid rgba(0, 0, 0, 1.00);border-top: 0 solid rgba(0, 0, 0, 1.00);border-left: 0 solid rgba(0, 0, 0, 1.00);border-right: 0 solid rgba(0, 0, 0, 1.00);margin-bottom:0;margin-top:0;margin-left:0;margin-right:0;}.cl-07a63300{width:54pt;background-color:transparent;vertical-align: middle;border-bottom: 0 solid rgba(0, 0, 0, 1.00);border-top: 0 solid rgba(0, 0, 0, 1.00);border-left: 0 solid rgba(0, 0, 0, 1.00);border-right: 0 solid rgba(0, 0, 0, 1.00);margin-bottom:0;margin-top:0;margin-left:0;margin-right:0;}.cl-07a63301{width:54pt;background-color:transparent;vertical-align: middle;border-bottom: 2pt solid rgba(102, 102, 102, 1.00);border-top: 0 solid rgba(0, 0, 0, 1.00);border-left: 0 solid rgba(0, 0, 0, 1.00);border-right: 0 solid rgba(0, 0, 0, 1.00);margin-bottom:0;margin-top:0;margin-left:0;margin-right:0;}.cl-07a6330a{width:54pt;background-color:transparent;vertical-align: middle;border-bottom: 2pt solid rgba(102, 102, 102, 1.00);border-top: 0 solid rgba(0, 0, 0, 1.00);border-left: 0 solid rgba(0, 0, 0, 1.00);border-right: 0 solid rgba(0, 0, 0, 1.00);margin-bottom:0;margin-top:0;margin-left:0;margin-right:0;}.cl-07a6330b{width:54pt;background-color:transparent;vertical-align: middle;border-bottom: 2pt solid rgba(102, 102, 102, 1.00);border-top: 2pt solid rgba(102, 102, 102, 1.00);border-left: 0 solid rgba(0, 0, 0, 1.00);border-right: 0 solid rgba(0, 0, 0, 1.00);margin-bottom:0;margin-top:0;margin-left:0;margin-right:0;}.cl-07a63314{width:54pt;background-color:transparent;vertical-align: middle;border-bottom: 2pt solid rgba(102, 102, 102, 1.00);border-top: 2pt solid rgba(102, 102, 102, 1.00);border-left: 0 solid rgba(0, 0, 0, 1.00);border-right: 0 solid rgba(0, 0, 0, 1.00);margin-bottom:0;margin-top:0;margin-left:0;margin-right:0;}</style><table class='cl-07ac4498'>
```

```{=html}
<thead><tr style="overflow-wrap:break-word;"><td class="cl-07a63314"><p class="cl-07a5ea12"><span class="cl-07a5cd98">Variable</span></p></td><td class="cl-07a6330b"><p class="cl-07a5ea1c"><span class="cl-07a5cd98">Mean</span></p></td><td class="cl-07a6330b"><p class="cl-07a5ea1c"><span class="cl-07a5cd98">SD</span></p></td><td class="cl-07a6330b"><p class="cl-07a5ea1c"><span class="cl-07a5cd98">IQR</span></p></td><td class="cl-07a6330b"><p class="cl-07a5ea1c"><span class="cl-07a5cd98">Min</span></p></td><td class="cl-07a6330b"><p class="cl-07a5ea1c"><span class="cl-07a5cd98">Max</span></p></td><td class="cl-07a6330b"><p class="cl-07a5ea1c"><span class="cl-07a5cd98">Skewness</span></p></td><td class="cl-07a6330b"><p class="cl-07a5ea1c"><span class="cl-07a5cd98">Kurtosis</span></p></td><td class="cl-07a6330b"><p class="cl-07a5ea1c"><span class="cl-07a5cd98">n</span></p></td><td class="cl-07a6330b"><p class="cl-07a5ea1c"><span class="cl-07a5cd98">n_Missing</span></p></td></tr></thead><tbody><tr style="overflow-wrap:break-word;"><td class="cl-07a63300"><p class="cl-07a5ea12"><span class="cl-07a5cd98">exp_avg</span></p></td><td class="cl-07a632f6"><p class="cl-07a5ea1c"><span class="cl-07a5cd98">0.8966165</span></p></td><td class="cl-07a632f6"><p class="cl-07a5ea1c"><span class="cl-07a5cd98">1.116262</span></p></td><td class="cl-07a632f6"><p class="cl-07a5ea1c"><span class="cl-07a5cd98">1.571429</span></p></td><td class="cl-07a632f6"><p class="cl-07a5ea1c"><span class="cl-07a5cd98">-1.857143</span></p></td><td class="cl-07a632f6"><p class="cl-07a5ea1c"><span class="cl-07a5cd98">3.000000</span></p></td><td class="cl-07a632f6"><p class="cl-07a5ea1c"><span class="cl-07a5cd98">-0.44852912</span></p></td><td class="cl-07a632f6"><p class="cl-07a5ea1c"><span class="cl-07a5cd98">-0.155202928</span></p></td><td class="cl-07a632f6"><p class="cl-07a5ea1c"><span class="cl-07a5cd98">76</span></p></td><td class="cl-07a632f6"><p class="cl-07a5ea1c"><span class="cl-07a5cd98">14</span></p></td></tr><tr style="overflow-wrap:break-word;"><td class="cl-07a63300"><p class="cl-07a5ea12"><span class="cl-07a5cd98">neu_avg</span></p></td><td class="cl-07a632f6"><p class="cl-07a5ea1c"><span class="cl-07a5cd98">1.2238095</span></p></td><td class="cl-07a632f6"><p class="cl-07a5ea1c"><span class="cl-07a5cd98">1.248150</span></p></td><td class="cl-07a632f6"><p class="cl-07a5ea1c"><span class="cl-07a5cd98">1.333333</span></p></td><td class="cl-07a632f6"><p class="cl-07a5ea1c"><span class="cl-07a5cd98">-2.666667</span></p></td><td class="cl-07a632f6"><p class="cl-07a5ea1c"><span class="cl-07a5cd98">3.000000</span></p></td><td class="cl-07a632f6"><p class="cl-07a5ea1c"><span class="cl-07a5cd98">-0.95903822</span></p></td><td class="cl-07a632f6"><p class="cl-07a5ea1c"><span class="cl-07a5cd98">0.790211603</span></p></td><td class="cl-07a632f6"><p class="cl-07a5ea1c"><span class="cl-07a5cd98">70</span></p></td><td class="cl-07a632f6"><p class="cl-07a5ea1c"><span class="cl-07a5cd98">20</span></p></td></tr><tr style="overflow-wrap:break-word;"><td class="cl-07a63300"><p class="cl-07a5ea12"><span class="cl-07a5cd98">att_avg</span></p></td><td class="cl-07a632f6"><p class="cl-07a5ea1c"><span class="cl-07a5cd98">1.0441176</span></p></td><td class="cl-07a632f6"><p class="cl-07a5ea1c"><span class="cl-07a5cd98">1.133925</span></p></td><td class="cl-07a632f6"><p class="cl-07a5ea1c"><span class="cl-07a5cd98">1.200000</span></p></td><td class="cl-07a632f6"><p class="cl-07a5ea1c"><span class="cl-07a5cd98">-2.600000</span></p></td><td class="cl-07a632f6"><p class="cl-07a5ea1c"><span class="cl-07a5cd98">3.000000</span></p></td><td class="cl-07a632f6"><p class="cl-07a5ea1c"><span class="cl-07a5cd98">-1.16348010</span></p></td><td class="cl-07a632f6"><p class="cl-07a5ea1c"><span class="cl-07a5cd98">1.927188688</span></p></td><td class="cl-07a632f6"><p class="cl-07a5ea1c"><span class="cl-07a5cd98">68</span></p></td><td class="cl-07a632f6"><p class="cl-07a5ea1c"><span class="cl-07a5cd98">22</span></p></td></tr><tr style="overflow-wrap:break-word;"><td class="cl-07a63300"><p class="cl-07a5ea12"><span class="cl-07a5cd98">ka_avg</span></p></td><td class="cl-07a632f6"><p class="cl-07a5ea1c"><span class="cl-07a5cd98">0.9090909</span></p></td><td class="cl-07a632f6"><p class="cl-07a5ea1c"><span class="cl-07a5cd98">1.202981</span></p></td><td class="cl-07a632f6"><p class="cl-07a5ea1c"><span class="cl-07a5cd98">1.208333</span></p></td><td class="cl-07a632f6"><p class="cl-07a5ea1c"><span class="cl-07a5cd98">-2.166667</span></p></td><td class="cl-07a632f6"><p class="cl-07a5ea1c"><span class="cl-07a5cd98">3.000000</span></p></td><td class="cl-07a632f6"><p class="cl-07a5ea1c"><span class="cl-07a5cd98">-1.06540680</span></p></td><td class="cl-07a632f6"><p class="cl-07a5ea1c"><span class="cl-07a5cd98">0.539760618</span></p></td><td class="cl-07a632f6"><p class="cl-07a5ea1c"><span class="cl-07a5cd98">66</span></p></td><td class="cl-07a632f6"><p class="cl-07a5ea1c"><span class="cl-07a5cd98">24</span></p></td></tr><tr style="overflow-wrap:break-word;"><td class="cl-07a63300"><p class="cl-07a5ea12"><span class="cl-07a5cd98">wom_avg</span></p></td><td class="cl-07a632f6"><p class="cl-07a5ea1c"><span class="cl-07a5cd98">0.3072917</span></p></td><td class="cl-07a632f6"><p class="cl-07a5ea1c"><span class="cl-07a5cd98">1.161257</span></p></td><td class="cl-07a632f6"><p class="cl-07a5ea1c"><span class="cl-07a5cd98">1.250000</span></p></td><td class="cl-07a632f6"><p class="cl-07a5ea1c"><span class="cl-07a5cd98">-2.333333</span></p></td><td class="cl-07a632f6"><p class="cl-07a5ea1c"><span class="cl-07a5cd98">3.000000</span></p></td><td class="cl-07a632f6"><p class="cl-07a5ea1c"><span class="cl-07a5cd98">0.35118856</span></p></td><td class="cl-07a632f6"><p class="cl-07a5ea1c"><span class="cl-07a5cd98">0.232676307</span></p></td><td class="cl-07a632f6"><p class="cl-07a5ea1c"><span class="cl-07a5cd98">64</span></p></td><td class="cl-07a632f6"><p class="cl-07a5ea1c"><span class="cl-07a5cd98">26</span></p></td></tr><tr style="overflow-wrap:break-word;"><td class="cl-07a63300"><p class="cl-07a5ea12"><span class="cl-07a5cd98">innp_avg</span></p></td><td class="cl-07a632f6"><p class="cl-07a5ea1c"><span class="cl-07a5cd98">-0.2486339</span></p></td><td class="cl-07a632f6"><p class="cl-07a5ea1c"><span class="cl-07a5cd98">1.276795</span></p></td><td class="cl-07a632f6"><p class="cl-07a5ea1c"><span class="cl-07a5cd98">1.500000</span></p></td><td class="cl-07a632f6"><p class="cl-07a5ea1c"><span class="cl-07a5cd98">-2.833333</span></p></td><td class="cl-07a632f6"><p class="cl-07a5ea1c"><span class="cl-07a5cd98">2.666667</span></p></td><td class="cl-07a632f6"><p class="cl-07a5ea1c"><span class="cl-07a5cd98">0.06946976</span></p></td><td class="cl-07a632f6"><p class="cl-07a5ea1c"><span class="cl-07a5cd98">-0.280800172</span></p></td><td class="cl-07a632f6"><p class="cl-07a5ea1c"><span class="cl-07a5cd98">61</span></p></td><td class="cl-07a632f6"><p class="cl-07a5ea1c"><span class="cl-07a5cd98">29</span></p></td></tr><tr style="overflow-wrap:break-word;"><td class="cl-07a63300"><p class="cl-07a5ea12"><span class="cl-07a5cd98">imp_avg</span></p></td><td class="cl-07a632f6"><p class="cl-07a5ea1c"><span class="cl-07a5cd98">-0.2827869</span></p></td><td class="cl-07a632f6"><p class="cl-07a5ea1c"><span class="cl-07a5cd98">1.177458</span></p></td><td class="cl-07a632f6"><p class="cl-07a5ea1c"><span class="cl-07a5cd98">1.500000</span></p></td><td class="cl-07a632f6"><p class="cl-07a5ea1c"><span class="cl-07a5cd98">-3.000000</span></p></td><td class="cl-07a632f6"><p class="cl-07a5ea1c"><span class="cl-07a5cd98">2.500000</span></p></td><td class="cl-07a632f6"><p class="cl-07a5ea1c"><span class="cl-07a5cd98">-0.32165605</span></p></td><td class="cl-07a632f6"><p class="cl-07a5ea1c"><span class="cl-07a5cd98">-0.007398344</span></p></td><td class="cl-07a632f6"><p class="cl-07a5ea1c"><span class="cl-07a5cd98">61</span></p></td><td class="cl-07a632f6"><p class="cl-07a5ea1c"><span class="cl-07a5cd98">29</span></p></td></tr><tr style="overflow-wrap:break-word;"><td class="cl-07a63300"><p class="cl-07a5ea12"><span class="cl-07a5cd98">hedo_avg</span></p></td><td class="cl-07a632f6"><p class="cl-07a5ea1c"><span class="cl-07a5cd98">0.3375000</span></p></td><td class="cl-07a632f6"><p class="cl-07a5ea1c"><span class="cl-07a5cd98">1.401290</span></p></td><td class="cl-07a632f6"><p class="cl-07a5ea1c"><span class="cl-07a5cd98">1.500000</span></p></td><td class="cl-07a632f6"><p class="cl-07a5ea1c"><span class="cl-07a5cd98">-3.000000</span></p></td><td class="cl-07a632f6"><p class="cl-07a5ea1c"><span class="cl-07a5cd98">3.000000</span></p></td><td class="cl-07a632f6"><p class="cl-07a5ea1c"><span class="cl-07a5cd98">-0.68659230</span></p></td><td class="cl-07a632f6"><p class="cl-07a5ea1c"><span class="cl-07a5cd98">0.397527258</span></p></td><td class="cl-07a632f6"><p class="cl-07a5ea1c"><span class="cl-07a5cd98">60</span></p></td><td class="cl-07a632f6"><p class="cl-07a5ea1c"><span class="cl-07a5cd98">30</span></p></td></tr><tr style="overflow-wrap:break-word;"><td class="cl-07a6330a"><p class="cl-07a5ea12"><span class="cl-07a5cd98">sho1_avg</span></p></td><td class="cl-07a63301"><p class="cl-07a5ea1c"><span class="cl-07a5cd98">-0.8041667</span></p></td><td class="cl-07a63301"><p class="cl-07a5ea1c"><span class="cl-07a5cd98">1.506407</span></p></td><td class="cl-07a63301"><p class="cl-07a5ea1c"><span class="cl-07a5cd98">2.750000</span></p></td><td class="cl-07a63301"><p class="cl-07a5ea1c"><span class="cl-07a5cd98">-3.000000</span></p></td><td class="cl-07a63301"><p class="cl-07a5ea1c"><span class="cl-07a5cd98">2.250000</span></p></td><td class="cl-07a63301"><p class="cl-07a5ea1c"><span class="cl-07a5cd98">0.27889917</span></p></td><td class="cl-07a63301"><p class="cl-07a5ea1c"><span class="cl-07a5cd98">-0.847516886</span></p></td><td class="cl-07a63301"><p class="cl-07a5ea1c"><span class="cl-07a5cd98">60</span></p></td><td class="cl-07a63301"><p class="cl-07a5ea1c"><span class="cl-07a5cd98">30</span></p></td></tr></tbody></table></div></template>
<div class="flextable-shadow-host" id="a98a0bed-eb1d-4806-a7ec-20e8b6905b4b"></div>
<script>
var dest = document.getElementById("a98a0bed-eb1d-4806-a7ec-20e8b6905b4b");
var template = document.getElementById("e0497cff-89bc-4612-a32a-a20581e10e5d");
var caption = template.content.querySelector("caption");
if(caption) {
  caption.style.cssText = "display:block;text-align:center;";
  var newcapt = document.createElement("p");
  newcapt.appendChild(caption)
  dest.parentNode.insertBefore(newcapt, dest.previousSibling);
}
var fantome = dest.attachShadow({mode: 'open'});
var templateContent = template.content;
fantome.appendChild(templateContent);
</script>

```


Vielleicht noch die Anzahl der Dezimalstellen beschneiden:


```r
flex1 <- 
  d7 %>% 
  select(ends_with("_avg")) %>% 
  describe_distribution() %>% 
  adorn_rounding(digits = 2) %>% 
  flextable()

flex1
```

```{=html}
<template id="1fa03a5f-8034-40c4-8ae1-7c2add88843b"><style>
.tabwid table{
  border-spacing:0px !important;
  border-collapse:collapse;
  line-height:1;
  margin-left:auto;
  margin-right:auto;
  border-width: 0;
  display: table;
  margin-top: 1.275em;
  margin-bottom: 1.275em;
  border-color: transparent;
}
.tabwid_left table{
  margin-left:0;
}
.tabwid_right table{
  margin-right:0;
}
.tabwid td {
    padding: 0;
}
.tabwid a {
  text-decoration: none;
}
.tabwid thead {
    background-color: transparent;
}
.tabwid tfoot {
    background-color: transparent;
}
.tabwid table tr {
background-color: transparent;
}
</style><div class="tabwid"><style>.cl-07d1ce48{}.cl-07cc6688{font-family:'Helvetica';font-size:11pt;font-weight:normal;font-style:normal;text-decoration:none;color:rgba(0, 0, 0, 1.00);background-color:transparent;}.cl-07cc72cc{margin:0;text-align:left;border-bottom: 0 solid rgba(0, 0, 0, 1.00);border-top: 0 solid rgba(0, 0, 0, 1.00);border-left: 0 solid rgba(0, 0, 0, 1.00);border-right: 0 solid rgba(0, 0, 0, 1.00);padding-bottom:5pt;padding-top:5pt;padding-left:5pt;padding-right:5pt;line-height: 1;background-color:transparent;}.cl-07cc72d6{margin:0;text-align:right;border-bottom: 0 solid rgba(0, 0, 0, 1.00);border-top: 0 solid rgba(0, 0, 0, 1.00);border-left: 0 solid rgba(0, 0, 0, 1.00);border-right: 0 solid rgba(0, 0, 0, 1.00);padding-bottom:5pt;padding-top:5pt;padding-left:5pt;padding-right:5pt;line-height: 1;background-color:transparent;}.cl-07ccae2c{width:54pt;background-color:transparent;vertical-align: middle;border-bottom: 0 solid rgba(0, 0, 0, 1.00);border-top: 0 solid rgba(0, 0, 0, 1.00);border-left: 0 solid rgba(0, 0, 0, 1.00);border-right: 0 solid rgba(0, 0, 0, 1.00);margin-bottom:0;margin-top:0;margin-left:0;margin-right:0;}.cl-07ccae2d{width:54pt;background-color:transparent;vertical-align: middle;border-bottom: 0 solid rgba(0, 0, 0, 1.00);border-top: 0 solid rgba(0, 0, 0, 1.00);border-left: 0 solid rgba(0, 0, 0, 1.00);border-right: 0 solid rgba(0, 0, 0, 1.00);margin-bottom:0;margin-top:0;margin-left:0;margin-right:0;}.cl-07ccae36{width:54pt;background-color:transparent;vertical-align: middle;border-bottom: 2pt solid rgba(102, 102, 102, 1.00);border-top: 0 solid rgba(0, 0, 0, 1.00);border-left: 0 solid rgba(0, 0, 0, 1.00);border-right: 0 solid rgba(0, 0, 0, 1.00);margin-bottom:0;margin-top:0;margin-left:0;margin-right:0;}.cl-07ccae37{width:54pt;background-color:transparent;vertical-align: middle;border-bottom: 2pt solid rgba(102, 102, 102, 1.00);border-top: 0 solid rgba(0, 0, 0, 1.00);border-left: 0 solid rgba(0, 0, 0, 1.00);border-right: 0 solid rgba(0, 0, 0, 1.00);margin-bottom:0;margin-top:0;margin-left:0;margin-right:0;}.cl-07ccae40{width:54pt;background-color:transparent;vertical-align: middle;border-bottom: 2pt solid rgba(102, 102, 102, 1.00);border-top: 2pt solid rgba(102, 102, 102, 1.00);border-left: 0 solid rgba(0, 0, 0, 1.00);border-right: 0 solid rgba(0, 0, 0, 1.00);margin-bottom:0;margin-top:0;margin-left:0;margin-right:0;}.cl-07ccae41{width:54pt;background-color:transparent;vertical-align: middle;border-bottom: 2pt solid rgba(102, 102, 102, 1.00);border-top: 2pt solid rgba(102, 102, 102, 1.00);border-left: 0 solid rgba(0, 0, 0, 1.00);border-right: 0 solid rgba(0, 0, 0, 1.00);margin-bottom:0;margin-top:0;margin-left:0;margin-right:0;}</style><table class='cl-07d1ce48'>
```

```{=html}
<thead><tr style="overflow-wrap:break-word;"><td class="cl-07ccae41"><p class="cl-07cc72cc"><span class="cl-07cc6688">Variable</span></p></td><td class="cl-07ccae40"><p class="cl-07cc72d6"><span class="cl-07cc6688">Mean</span></p></td><td class="cl-07ccae40"><p class="cl-07cc72d6"><span class="cl-07cc6688">SD</span></p></td><td class="cl-07ccae40"><p class="cl-07cc72d6"><span class="cl-07cc6688">IQR</span></p></td><td class="cl-07ccae40"><p class="cl-07cc72d6"><span class="cl-07cc6688">Min</span></p></td><td class="cl-07ccae40"><p class="cl-07cc72d6"><span class="cl-07cc6688">Max</span></p></td><td class="cl-07ccae40"><p class="cl-07cc72d6"><span class="cl-07cc6688">Skewness</span></p></td><td class="cl-07ccae40"><p class="cl-07cc72d6"><span class="cl-07cc6688">Kurtosis</span></p></td><td class="cl-07ccae40"><p class="cl-07cc72d6"><span class="cl-07cc6688">n</span></p></td><td class="cl-07ccae40"><p class="cl-07cc72d6"><span class="cl-07cc6688">n_Missing</span></p></td></tr></thead><tbody><tr style="overflow-wrap:break-word;"><td class="cl-07ccae2d"><p class="cl-07cc72cc"><span class="cl-07cc6688">exp_avg</span></p></td><td class="cl-07ccae2c"><p class="cl-07cc72d6"><span class="cl-07cc6688">0.90</span></p></td><td class="cl-07ccae2c"><p class="cl-07cc72d6"><span class="cl-07cc6688">1.12</span></p></td><td class="cl-07ccae2c"><p class="cl-07cc72d6"><span class="cl-07cc6688">1.57</span></p></td><td class="cl-07ccae2c"><p class="cl-07cc72d6"><span class="cl-07cc6688">-1.86</span></p></td><td class="cl-07ccae2c"><p class="cl-07cc72d6"><span class="cl-07cc6688">3.00</span></p></td><td class="cl-07ccae2c"><p class="cl-07cc72d6"><span class="cl-07cc6688">-0.45</span></p></td><td class="cl-07ccae2c"><p class="cl-07cc72d6"><span class="cl-07cc6688">-0.16</span></p></td><td class="cl-07ccae2c"><p class="cl-07cc72d6"><span class="cl-07cc6688">76</span></p></td><td class="cl-07ccae2c"><p class="cl-07cc72d6"><span class="cl-07cc6688">14</span></p></td></tr><tr style="overflow-wrap:break-word;"><td class="cl-07ccae2d"><p class="cl-07cc72cc"><span class="cl-07cc6688">neu_avg</span></p></td><td class="cl-07ccae2c"><p class="cl-07cc72d6"><span class="cl-07cc6688">1.22</span></p></td><td class="cl-07ccae2c"><p class="cl-07cc72d6"><span class="cl-07cc6688">1.25</span></p></td><td class="cl-07ccae2c"><p class="cl-07cc72d6"><span class="cl-07cc6688">1.33</span></p></td><td class="cl-07ccae2c"><p class="cl-07cc72d6"><span class="cl-07cc6688">-2.67</span></p></td><td class="cl-07ccae2c"><p class="cl-07cc72d6"><span class="cl-07cc6688">3.00</span></p></td><td class="cl-07ccae2c"><p class="cl-07cc72d6"><span class="cl-07cc6688">-0.96</span></p></td><td class="cl-07ccae2c"><p class="cl-07cc72d6"><span class="cl-07cc6688">0.79</span></p></td><td class="cl-07ccae2c"><p class="cl-07cc72d6"><span class="cl-07cc6688">70</span></p></td><td class="cl-07ccae2c"><p class="cl-07cc72d6"><span class="cl-07cc6688">20</span></p></td></tr><tr style="overflow-wrap:break-word;"><td class="cl-07ccae2d"><p class="cl-07cc72cc"><span class="cl-07cc6688">att_avg</span></p></td><td class="cl-07ccae2c"><p class="cl-07cc72d6"><span class="cl-07cc6688">1.04</span></p></td><td class="cl-07ccae2c"><p class="cl-07cc72d6"><span class="cl-07cc6688">1.13</span></p></td><td class="cl-07ccae2c"><p class="cl-07cc72d6"><span class="cl-07cc6688">1.20</span></p></td><td class="cl-07ccae2c"><p class="cl-07cc72d6"><span class="cl-07cc6688">-2.60</span></p></td><td class="cl-07ccae2c"><p class="cl-07cc72d6"><span class="cl-07cc6688">3.00</span></p></td><td class="cl-07ccae2c"><p class="cl-07cc72d6"><span class="cl-07cc6688">-1.16</span></p></td><td class="cl-07ccae2c"><p class="cl-07cc72d6"><span class="cl-07cc6688">1.93</span></p></td><td class="cl-07ccae2c"><p class="cl-07cc72d6"><span class="cl-07cc6688">68</span></p></td><td class="cl-07ccae2c"><p class="cl-07cc72d6"><span class="cl-07cc6688">22</span></p></td></tr><tr style="overflow-wrap:break-word;"><td class="cl-07ccae2d"><p class="cl-07cc72cc"><span class="cl-07cc6688">ka_avg</span></p></td><td class="cl-07ccae2c"><p class="cl-07cc72d6"><span class="cl-07cc6688">0.91</span></p></td><td class="cl-07ccae2c"><p class="cl-07cc72d6"><span class="cl-07cc6688">1.20</span></p></td><td class="cl-07ccae2c"><p class="cl-07cc72d6"><span class="cl-07cc6688">1.21</span></p></td><td class="cl-07ccae2c"><p class="cl-07cc72d6"><span class="cl-07cc6688">-2.17</span></p></td><td class="cl-07ccae2c"><p class="cl-07cc72d6"><span class="cl-07cc6688">3.00</span></p></td><td class="cl-07ccae2c"><p class="cl-07cc72d6"><span class="cl-07cc6688">-1.07</span></p></td><td class="cl-07ccae2c"><p class="cl-07cc72d6"><span class="cl-07cc6688">0.54</span></p></td><td class="cl-07ccae2c"><p class="cl-07cc72d6"><span class="cl-07cc6688">66</span></p></td><td class="cl-07ccae2c"><p class="cl-07cc72d6"><span class="cl-07cc6688">24</span></p></td></tr><tr style="overflow-wrap:break-word;"><td class="cl-07ccae2d"><p class="cl-07cc72cc"><span class="cl-07cc6688">wom_avg</span></p></td><td class="cl-07ccae2c"><p class="cl-07cc72d6"><span class="cl-07cc6688">0.31</span></p></td><td class="cl-07ccae2c"><p class="cl-07cc72d6"><span class="cl-07cc6688">1.16</span></p></td><td class="cl-07ccae2c"><p class="cl-07cc72d6"><span class="cl-07cc6688">1.25</span></p></td><td class="cl-07ccae2c"><p class="cl-07cc72d6"><span class="cl-07cc6688">-2.33</span></p></td><td class="cl-07ccae2c"><p class="cl-07cc72d6"><span class="cl-07cc6688">3.00</span></p></td><td class="cl-07ccae2c"><p class="cl-07cc72d6"><span class="cl-07cc6688">0.35</span></p></td><td class="cl-07ccae2c"><p class="cl-07cc72d6"><span class="cl-07cc6688">0.23</span></p></td><td class="cl-07ccae2c"><p class="cl-07cc72d6"><span class="cl-07cc6688">64</span></p></td><td class="cl-07ccae2c"><p class="cl-07cc72d6"><span class="cl-07cc6688">26</span></p></td></tr><tr style="overflow-wrap:break-word;"><td class="cl-07ccae2d"><p class="cl-07cc72cc"><span class="cl-07cc6688">innp_avg</span></p></td><td class="cl-07ccae2c"><p class="cl-07cc72d6"><span class="cl-07cc6688">-0.25</span></p></td><td class="cl-07ccae2c"><p class="cl-07cc72d6"><span class="cl-07cc6688">1.28</span></p></td><td class="cl-07ccae2c"><p class="cl-07cc72d6"><span class="cl-07cc6688">1.50</span></p></td><td class="cl-07ccae2c"><p class="cl-07cc72d6"><span class="cl-07cc6688">-2.83</span></p></td><td class="cl-07ccae2c"><p class="cl-07cc72d6"><span class="cl-07cc6688">2.67</span></p></td><td class="cl-07ccae2c"><p class="cl-07cc72d6"><span class="cl-07cc6688">0.07</span></p></td><td class="cl-07ccae2c"><p class="cl-07cc72d6"><span class="cl-07cc6688">-0.28</span></p></td><td class="cl-07ccae2c"><p class="cl-07cc72d6"><span class="cl-07cc6688">61</span></p></td><td class="cl-07ccae2c"><p class="cl-07cc72d6"><span class="cl-07cc6688">29</span></p></td></tr><tr style="overflow-wrap:break-word;"><td class="cl-07ccae2d"><p class="cl-07cc72cc"><span class="cl-07cc6688">imp_avg</span></p></td><td class="cl-07ccae2c"><p class="cl-07cc72d6"><span class="cl-07cc6688">-0.28</span></p></td><td class="cl-07ccae2c"><p class="cl-07cc72d6"><span class="cl-07cc6688">1.18</span></p></td><td class="cl-07ccae2c"><p class="cl-07cc72d6"><span class="cl-07cc6688">1.50</span></p></td><td class="cl-07ccae2c"><p class="cl-07cc72d6"><span class="cl-07cc6688">-3.00</span></p></td><td class="cl-07ccae2c"><p class="cl-07cc72d6"><span class="cl-07cc6688">2.50</span></p></td><td class="cl-07ccae2c"><p class="cl-07cc72d6"><span class="cl-07cc6688">-0.32</span></p></td><td class="cl-07ccae2c"><p class="cl-07cc72d6"><span class="cl-07cc6688">-0.01</span></p></td><td class="cl-07ccae2c"><p class="cl-07cc72d6"><span class="cl-07cc6688">61</span></p></td><td class="cl-07ccae2c"><p class="cl-07cc72d6"><span class="cl-07cc6688">29</span></p></td></tr><tr style="overflow-wrap:break-word;"><td class="cl-07ccae2d"><p class="cl-07cc72cc"><span class="cl-07cc6688">hedo_avg</span></p></td><td class="cl-07ccae2c"><p class="cl-07cc72d6"><span class="cl-07cc6688">0.34</span></p></td><td class="cl-07ccae2c"><p class="cl-07cc72d6"><span class="cl-07cc6688">1.40</span></p></td><td class="cl-07ccae2c"><p class="cl-07cc72d6"><span class="cl-07cc6688">1.50</span></p></td><td class="cl-07ccae2c"><p class="cl-07cc72d6"><span class="cl-07cc6688">-3.00</span></p></td><td class="cl-07ccae2c"><p class="cl-07cc72d6"><span class="cl-07cc6688">3.00</span></p></td><td class="cl-07ccae2c"><p class="cl-07cc72d6"><span class="cl-07cc6688">-0.69</span></p></td><td class="cl-07ccae2c"><p class="cl-07cc72d6"><span class="cl-07cc6688">0.40</span></p></td><td class="cl-07ccae2c"><p class="cl-07cc72d6"><span class="cl-07cc6688">60</span></p></td><td class="cl-07ccae2c"><p class="cl-07cc72d6"><span class="cl-07cc6688">30</span></p></td></tr><tr style="overflow-wrap:break-word;"><td class="cl-07ccae37"><p class="cl-07cc72cc"><span class="cl-07cc6688">sho1_avg</span></p></td><td class="cl-07ccae36"><p class="cl-07cc72d6"><span class="cl-07cc6688">-0.80</span></p></td><td class="cl-07ccae36"><p class="cl-07cc72d6"><span class="cl-07cc6688">1.51</span></p></td><td class="cl-07ccae36"><p class="cl-07cc72d6"><span class="cl-07cc6688">2.75</span></p></td><td class="cl-07ccae36"><p class="cl-07cc72d6"><span class="cl-07cc6688">-3.00</span></p></td><td class="cl-07ccae36"><p class="cl-07cc72d6"><span class="cl-07cc6688">2.25</span></p></td><td class="cl-07ccae36"><p class="cl-07cc72d6"><span class="cl-07cc6688">0.28</span></p></td><td class="cl-07ccae36"><p class="cl-07cc72d6"><span class="cl-07cc6688">-0.85</span></p></td><td class="cl-07ccae36"><p class="cl-07cc72d6"><span class="cl-07cc6688">60</span></p></td><td class="cl-07ccae36"><p class="cl-07cc72d6"><span class="cl-07cc6688">30</span></p></td></tr></tbody></table></div></template>
<div class="flextable-shadow-host" id="471e8bd1-d6c9-4a5d-8985-813996d8afa7"></div>
<script>
var dest = document.getElementById("471e8bd1-d6c9-4a5d-8985-813996d8afa7");
var template = document.getElementById("1fa03a5f-8034-40c4-8ae1-7c2add88843b");
var caption = template.content.querySelector("caption");
if(caption) {
  caption.style.cssText = "display:block;text-align:center;";
  var newcapt = document.createElement("p");
  newcapt.appendChild(caption)
  dest.parentNode.insertBefore(newcapt, dest.previousSibling);
}
var fantome = dest.attachShadow({mode: 'open'});
var templateContent = template.content;
fantome.appendChild(templateContent);
</script>

```



Und so speichert man als Word-Datei:



```r
save_as_docx(flex1, path = "flex1.docx")
```


### Ad 12

Wie kann ich Items konvertieren? Also negativ gepolte Items positiv umkodieren, also "umdrehen"?


Die Skala erstreckt sich von -3 bis +3.
Mit `recode()` kann man wie oben auch entsprechend umkodieren.








```r
d8 <-
  d7 %>% 
  mutate(v033_i = dplyr::recode(v033,
                         `-3` = +3,
                         `-2` = +2,
                         `-1` = 1,
                         `0` = 0,
                         `1` = -1,
                         `2` = -2,
                         `3` = -3))
```

Die Backticks brauchen wir, 
weil es sich bei `-1` etc. nicht um syntaktisch korrekte Variablennamen handelt.

Tipp: Einfach mal in die Hilfe schauen.


```r
i <- 1
`i ist eins` <- 1
i1 <- 1
```

