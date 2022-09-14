# Visualisointiverkosto 14.9.2022

Tälle sivulle on koottu neljä esimerkkiä R:n eri paketeilla tehdyistä visualisoinneista aineistoineen 

Tietoja on visualisoitu seuraavalla tavalla:
1. Pylväsdiagrammi (ggplot2)
2. Sanapilvi (wordcloud2)
3. Sankey-kuvio (networkD3)
4. Kalenterikuvio (calendR)

## 1.  Riistaonnettomuudet eläinlajeittain, maakunnittain ja kuukausittain

1. Ladataan pohjadataksi maakunnittainen riistaonnettomuusaineisto PX-Web-taulukosta. Ladataan tiedot eläinlajeittain, maakunnittian ja kuukasitasolla. Tallennetaan tiedot relaatiotiedostoon, jotta tietoja on helppo käsitellä seuraavassa vaiheessa. Jätetään yhteensä-luokat pois.
<img src="kuvat\relaatiotiedosto2.png">

Datan voi ladata myös [täältä](https://github.com/mkokkone/visualisointikoulutus/blob/gh-pages/001_12sg_2021m12_20220311-154122.txt):


2. Asennetaan ggplot2-kirjasto: Packages -> Install packages ja valitaan listalta ggplot2
3. Avataan R ja avataan uusi skripti: FIle -> New script
4. Ladataan ggplot2-kirjasto
```
library(ggplot2)
```
5. Luetaan aineisto sisään nimelle riista
```
riista <- read.delim("/001_12sg_2021m12_20220225-112559.txt")
```
6. Tehdään kuukausittain peruskuva ggplotilla, ensimmäisessä blokissa määritellään: aineisto = riista, x=kuukausi ja y=Riistaonnettomuuudet eli onnettomuuksien lukumäärä, fill=Osallinen.eläinlaji eli pylväät täytetään osallisten eläinlajien mukaan. Jälkimmäisessä osiossa määritellään että halutaan pylväsdiagrammi: geom_bar ja halutaan laskea tapauksien lukumäärä: stat = "identity".
```
ggplot(riista , aes(Kuukausi, Riistaonnettomuudet, fill=Osallinen.eläinlaji)) +    geom_bar(stat = "identity")
```
<img src="kuvat\kuva1.png">

7. Jaetaan tiedot maakunnittaisiin kuviin lisäämällä ensimmäisen rivin loppuun + -merkki ja facet_wrap -koodi, jossa kerrotaan että tiedot jaetaan useampaan kuvaan Alue-muuttujan avulla
```
ggplot(riista , aes(Kuukausi, Riistaonnettomuudet, fill=Osallinen.eläinlaji)) +    geom_bar(stat = "identity")+
facet_wrap(vars(Alue))
```

<img src="kuvat\kuva2.png">

8. Maakuntien erot onnettomuusmäärissä ovat isoja, joten vapautetaan akseleiden skaalaus, scales = "free" -optiolla

```
ggplot(riista , aes(Kuukausi, Riistaonnettomuudet, fill=Osallinen.eläinlaji)) +    geom_bar(stat = "identity")+
facet_wrap(vars(Alue), scales = "free")
```

<img src="kuvat\kuva3.png">

9. X-akselin otsikot menevät päällekkäin toistensa kanssa, käännetään niitä 45 astetta: angle-optiolla

```
ggplot(riista , aes(Kuukausi, Riistaonnettomuudet, fill=Osallinen.eläinlaji)) +    geom_bar(stat = "identity", na.rm=TRUE)+
facet_wrap(vars(Alue), scales = "free")+ 
theme(axis.text.x = element_text(angle=45)) 
```

<img src="kuvat\kuva5.png">

10. Toinen vaihtoehto olisi käyttää priorisointia ja estää akselin otsikoiden päällekäisyys

```
ggplot(riista , aes(Kuukausi, Riistaonnettomuudet, fill=Osallinen.eläinlaji)) +    geom_bar(stat = "identity")+
facet_wrap(vars(Alue), scales = "free")+ 
theme(strip.text.x=element_text()) +
scale_x_discrete(guide = guide_axis(check.overlap = TRUE))
```

<img src="kuvat\kuva6.png">

11. Pysytellään kuitenkin kääntämisessä, aikasemmassa esimerkissä akselin otsikot menivät kuvion päälle, joten joten siirretään niitä hjust-optiolla alaspäin 

```
ggplot(riista , aes(Kuukausi, Riistaonnettomuudet, fill=Osallinen.eläinlaji)) +    geom_bar(stat = "identity")+
facet_wrap(vars(Alue), scales = "free")+
theme(  axis.text.x = element_blank()) +
theme(axis.text.x = element_text(angle=45)) 
```

<img src="kuvat\kuva7.png">

12. Tähän asti on käytetty oletusväriskaalaa. asetetaan kuvioon Tilastokeskuksen väriskaalan mukaiset värit ja otetaan ne käyttöön scale_fill_manual -optiolla

```
rhg_cols <- c("#0073B0", "#ea7404", "#a40084", "#8a8a9e", "#253081", 
              "#049de3", "#722ea5")

ggplot(riista , aes(Kuukausi, Riistaonnettomuudet, fill=Osallinen.eläinlaji)) +    geom_bar(stat = "identity")+
facet_wrap(vars(Alue), scales = "free")+ 
theme(  axis.text.x = element_blank()) +
theme(axis.text.x = element_text(angle=45))+
scale_fill_manual(values = rhg_cols)

```

<img src="kuvat\kuva8.png">

13. Havainnot ovat nyt aakkosjärjestyksessä eläinlajin mukaan, asetetaan ne suuruusjärjestykseen manuaalisesti

```
ggplot(riista , aes(Kuukausi, Riistaonnettomuudet, fill=factor(Osallinen.eläinlaji,levels=c("Valkohäntäpeura", "Metsäkauris", "Hirvi", "Metsäpeura", "Villisika", "Kuusipeura")))) +    geom_bar(stat = "identity")+
facet_wrap(vars(Alue), scales = "free_y") +
theme(  axis.text.x = element_blank()) +
theme(axis.text.x = element_text(angle=45))+
scale_fill_manual(values = rhg_cols)
```
<img src="kuvat\kuva9.png">

14. Viimeistellään kuvio korjaamalla selitteen otsikko, asettamalla x- ja y akseleille otsikot ja antamalla koko kuviolla otsikko

```
kuva<-ggplot(riista , aes(Kuukausi, Riistaonnettomuudet, fill=factor(Osallinen.eläinlaji,levels=c("Valkohäntäpeura", "Metsäkauris", "Hirvi", "Metsäpeura", "Villisika", "Kuusipeura")))) +    geom_bar(stat = "identity")+
facet_wrap(vars(Alue), scales = "free") + theme_light() 
kuva + 
theme(  axis.text.x = element_blank()) +
theme(axis.text.x = element_text(angle=45))+
scale_fill_manual(values = rhg_cols)+
guides(fill=guide_legend(title="Eläinlaji"))+
ggtitle("Riistaonnettomuudet maakunnitain vuonna 2020") +
xlab("Kuukausi") + 
ylab("Onnettomuuksien lukumäärä")+
theme(plot.title = element_text(size=16,hjust = 0.5))
```

<img src="kuvat\kuva10.png">

## 2. Sanapilvi (wordcloud2)

Sanapilven ainesto on kerätty Suomi maailman kärjessä -sivustolta, kopioimalla teksti Exceliin ja poistamalla tekstistä linkit. Tämän jälkeen teksti on käytetty [perusmuotoistajan](https://www.tarmo.fi/perusmuotoon/) läpi ja tämän jälkeen perustmuotoistajan tuloksena syntynyt aineisto on taulukoitu ja siihen on laskettu jokaisen yksittäisen sanan esiintymisten lukumäärä. Tuloksista on poistettu joitain yksittäisiä sanoja, kuten "olla"-verbit. 

Aineisto on tämä jälkeen tallennettu csv-tiedostoksi ja se on ladattavissa [täältä](https://mkokkone.github.io/visualisointiverkosto_14092022/sanapilvi_vertailu.csv). Lähtöaineisto näyttää seuraavanlaiselta:

```
word;freq
toinen;25
kolmas;20
hyvä;18
eniten;14
Eurooppa;12
kaupunki;12
OECD-maa;10
```
Aineisto luetaan sisään read.csv-lauseella. Enkoodaus on ääkkösten takia "latin1".
```
sanat <- read.csv("/sanapilvi_vertailu.csv", sep=";", encoding="latin1")
```
Ladataan wordcloud2-paketti
```
library(wordcloud2)
```
Perus-sanapilvi voidaan tuottaa yksinkertaisesti seuraavalla tavalla:
```
wordcloud2(data = sanat)
```
<img src="kuvat\sanapilvi1.png">

Sanoille on mahdollista määritellä joko yksi väri:
```
wordcloud2(data = sanat,rotateRatio = 1,shape="circle", color="blue")
```
<img src="kuvat\Sanapilvi2.png">

Tai useampia kovakoodattuja värejä:
```
wordcloud2(data = sanat,rotateRatio = 1,shape="circle", color=rep_len( c("#0073b0","#ea7404","#a40084","#8a8a9e","#253081","#049de3","#722ea5","#21a4a0","#O03d44","#fd484e"), nrow(sanat)))
```
<img src="kuvat\Sanapilvi3.png">

Muotoa ja sanojen suuntaa on myös mahdollista muuttaa
```
wordcloud2(data = sanat,shape="diamond", color=rep_len( c("#0073b0","#ea7404","#a40084","#8a8a9e","#253081","#049de3","#722ea5","#21a4a0","#O03d44","#fd484e"), nrow(sanat)), minRotation = -pi/6, maxRotation = -pi/6, rotateRatio = 1)
```
<img src="kuvat\sanapilvi4.png">


Paketin dokumentaatio löytyy [täältä](https://cran.r-project.org/web/packages/wordcloud2/vignettes/wordcloud.html)

## 3. Sankey-kuvio (networkD3)

Sankey-kuvion aineistona käytetään muuttoliike-tilaston maahanmuutto ja maastamuutto -tietokantataulukkoa. Aineisto on ladattu maanosa ja maakunta -tasolla, jotta luokkia ei tule liikaa kuviota varten. Aineisto on käsitelty Excelissä ja aineistosta on muodostettu kaksi dataa eli ns. reitti -data, jossa on lähtöpaikan id ja määräpaikan id sekä muuttoliikkeen määrä ja data, jossa on lähtö- ja määräpaikan id:t ja muuttujien arvojen selitteet.

Reitti-data näyttää seuraavanlaiselta ja se on ladattavissa [täältä](https://mkokkone.github.io/visualisointiverkosto_14092022/sankey_data.csv)::
```
source;target;N
0;30;250
1;30;22
2;30;5
3;30;10
4;30;34
5;30;5
```

Ja lähtö- ja määräpaikan sekä muuttujien arvojen liitteet sisältvä data seuraavanlaiselta ja se on ladattavissa [täältä](https://mkokkone.github.io/visualisointiverkosto_14092022/sankey_label.csv):
```
label
Uusimaa
Varsinais-Suomi
Satakunta
Kanta-Häme
Pirkanmaa
Päijät-Häme
```
Aineisto ovat ladattavissa [täältä](https://mkokkone.github.io/visualisointiverkosto_14092022/sanapilvi_vertailu.csv)


Aineistot yhdistetään source ja target-muuttujien arvolla ja selitedatan rivi-indeksillä. Huomaa että maanosilla on seliteet kahteen kertaan, koska määrämaanosilla on eri id kuin lähtömaanosilla.

Ladataan aineistot csv-tiedostoina
```
library(networkD3)
data <- read.csv("/sankey_data.csv", sep=";", encoding="latin1")
label <- read.csv("/sankey_label.csv", sep=";", encoding="latin1")
```
Kuvio voidaan muodostaa suoraan, koska aineisto on käsitelty jo edellisessä vaiheessa
```
p <- sankeyNetwork(Links = data, Nodes = label, Source = "source",
                   Target = "target", Value = "N", NodeID = "label",
                   units = "N", fontSize = 12, nodeWidth = 30)
p
```
<img src="kuvat\sankey1.png">

Lisätään kuvioon vielä halutut värit määrittelemällä ne ennen kuvion muodostusta ja lisäämällä ne kuvioon colourScale-lauseella.

```

colorJS <- paste('d3.scaleOrdinal(["#0073b0","#ea7404",	"#a40084","#8a8a9e","#253081","#049de3","#722ea5","#21a4a0","#O03d44","#fd484e","#b2b2b2","#000000","#c30045","#e21776","#9c1c36","#f8941e","#fecb00","#c0d730","#7ab800","#00833e","#003d44","#51312d"])')

p <- sankeyNetwork(Links = data, Nodes = label, Source = "source",
                   Target = "target", Value = "N", NodeID = "label",
                   units = "N", fontSize = 12, nodeWidth = 30,
                   colourScale = colorJS)

p

```
<img src="kuvat\sankey2.png">

Paketin dokumentaatio löytyy [täältä](https://cran.r-project.org/web/packages/networkD3/networkD3.pdf)

## 4. Kalenterikuvio (calendR)

Kalenterikuvion aineisto on kerätty Tilastokeskuksen vuoden 2022 julkaisukalenterista päiväkohtaisesti sekä jo [julkaistujen tietojen](https://stat.fi/fi/julkaisut) että [tulevien julkaisujen](https://stat.fi/fi/tulevat-julkaisut) osalta. Tiedot kuukausittaisista suodatuksista Exceliin, jonka jälkeen päivämäärät on eroteltu muun tekstin joukosta ja julkaisujen päiväkohtainen lukumäärä on taulukoitu. Aineisto on tallennettu rivi-indeksin mukaisesti päiväkohtaisena summana csv-tiedostoon ja se näyttää seuraavanlaiselta:
```
0
0
0
0
1
0
0
0
0
4
0
2
```
Ladataan calendR-paketti ja haetaan csv-tiedosto R:ään ilman sarakeotsikoita.
```
library(calendR)


data2 <- read.csv("G:/visualisointiverkosto/R/julkaisukalenteri.csv", sep=";", header=FALSE)

```
Luodaan kalenteri-kuvio ladatusta aineistosta. Special.days-osoittaa päivät, joille halutaan visualisoida julkaisujen kokonaismäärä
```
calendR(year = 2022,
        special.days = data2$V1,
        gradient = TRUE,
        low.col = "#FFFFED",

        
        special.col = "#ea7404")

```
<img src="kuvat\kalenteri1.png">

Muutetaan viikko alkamaan maanantaista ja samalla voidaan muuttaa värejä eli päivät, joilla on 0-julkaisua muutetaan valkoisiksi.
```
calendR(year = 2022,
        special.days = data2$V1,
        gradient = TRUE,
        low.col = "white",
        legend.pos = "right",
        
        start="M",
              
        special.col = "#ea7404")
```
<img src="kuvat\kalenteri2.png">

Asetetaan kuviolle otsikko ja muutetaan korostusväriä.
```
calendR(year = 2022,
        special.days = data2$V1,
        gradient = TRUE,
        low.col = "white",
        legend.pos = "right",
        
        start="M",
        title  = "Julkaisujen määrä päivää kohti, vuosi 2022",
        special.col = "#0073b0")

```
<img src="kuvat\kalenteri3.png">

Tallennetaan kuvio PDF-tiedostoksi. Kuvio löytyy R:n working directorystä, jonka sijainnin voi tarkistaa kirjoittamalla "getwd()" -konsoliin.
```

calendR(year = 2022,
        special.days = data2$V1,
        gradient = TRUE,
        low.col = "white",
        legend.pos = "right",
        
        start="M",
        title  = "Julkaisujen määrä päivää kohti, vuosi 2022",
        special.col = "#0073b0",

        pdf = TRUE)
```
Paketin dokumentaatio löytyy [täältä](https://cran.r-project.org/web/packages/networkD3/networkD3.pdf)](https://r-coder.com/calendar-plot-r/)
