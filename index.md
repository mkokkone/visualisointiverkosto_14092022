# Viesti tehokkaasti 11.3.2021

Tälle sivulle on koottu neljä esimerkkiä R:n eri paketeilla tehdyistä visualisoinneista aineistoineen 

Tietoja on visualisoitu seuraavalla tavalla:
1. Pylväsdiagrammi (ggplot2)
2. Sanapilvi (wordcloud2)
3. Sankey-kuvio (networkD3)
4. Kalenterikuvio (calendR & openair)

## 1.  R Riistaonnettomuudet eläinlajeittain, maakunnittain ja kuukausittain

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


