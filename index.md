# Viesti tehokkaasti 11.3.2021

Tälle sivulle on koottu neljä esimerkkiä miten eri avoimen lähdekoodin välineillä voidaan visualisoida [riistaonnettomuudet-tilaston](https://www.stat.fi/tup/kokeelliset-tilastot/riistaonnettomuudet/index.html) tilastotietoa

Tietoja on visualisoitu seuraavien välineiden avulla:
1. PX-Webin sisäänrakennettu kuviotyökalu
2. Teemakartta Qgis-karttaohjelmalla
3. R:n ggplot2-kirjasto
4. D3js-kirjasto Javascriptillä

## 1. PX-Web

1. Valitaan riistaonnettomuudet tilaston [tietokantataulukosta] (https://pxnet2.stat.fi/PXWeb/pxweb/fi/Kokeelliset_tilastot/Kokeelliset_tilastot__riista/koeti_riista_pxt_12sh.px/) kaikki maakunnat 
<img src="kuvat\maakuntavalinta.png">
ja vuodet 2019-2021. 
<img src="kuvat\vuosivalinta.png">
Muita muuttujia ei tarvitse erikseen valita vaan muista muuttujista on esivalittuna yhteensä-arvot

2. Valitaan esitysmuodoksi "Vaakapylväskuvio"
<img src="kuvat\vaakapylvas.png">

3. Käännetään kuutiota kerran myötäpäivään, jotta saadaan maakunnat pystyakselille
<img src="kuvat\kaanna.png">

4. Muokataan kuvion kuvioasetuksia; asetetaan otsikko, muokataan korkeutta ja laitetaan otsikot vaakaan
<img src="kuvat\kuvioasetukset.png">

5. Tallennetaan kuvio palvelimelle tallennetuksi hauksi, jolloin se voidaan jakaa sellaisenaan. Kuvion voi myös tallentaa haluamassaan formaatissa.
<img src="kuvat\tallenna_poiminta.png">

6. Kopioidaan linkki talteen. Linkki osoittaa suoraan äsken luotuun png-tiedostomuotoiseen kuvioon.
<img src="kuvat\linkki.png">

Edellä luotu kuvio on liitetty osaksi tätä sivustoa ja kuva luetaan tälle sivulle suoraan px-webistä.

<img src="https://pxnet2.stat.fi:443/PXWeb/sq/6679640d-e464-402c-a3d7-10e118e8b688" alt="Kuvioissa riistaonnettomuudet maakunnittain vuosina 2019-2021. Riistaonnettomuuksia tapahtui vuosittain eniten Varsinais-Suomen, Uudenmaan, Pirkanmaan, Satakunnan maakunnissa ja Kanta-Hämeen maakunnissa.">

## 2. Teemakartta Qgis-karttaohjelmalla

Luodaan teemakartta, jossa maakunnittainen riistaonnettomuuksien määrä on suhteutettu liikennemäärään. 

1. Ladataan aineistot tilastovuodelle 2020 [riistaonnettomuuksien tietokantataulukosta](https://pxnet2.stat.fi:443/PXWeb/sq/c3064768-9ed1-4e6c-b90b-a095ea25b13b) ja [tietilaston tietokantataulukosta](https://pxnet2.stat.fi:443/PXWeb/sq/64b0f6c1-eda4-4653-a884-b8ce728f7028). Ladatessa valitaan muodoksi "Lataa puolipiste-eroteltu csv-tiedosto (otsikollinen)"
<img src="kuvat\lataa_puolipiste.png">

2. Muokataan csv-tiedoston otsikoita ja dataa siten että poistetaan maakuntien nimistä MK-etuliite ja lisätään koodi-muuttuja, jossa on pelkkä maakuntakoodi:

Alkuperäinen:
```
"12jz -- Maanteiden pituus ja liikennesuorite kunnittain, 2017-2020"

"Alue";"Vuosi";"Liikennesuorite"
"MK01 Uusimaa";"2020";7657
"MK02 Varsinais-Suomi";"2020";2960
...
```

Muokattu:
```
"12jz -- Maanteiden pituus ja liikennesuorite kunnittain, 2017-2020"

"Koodi";"Alue";"Tieluokka";"Päällyste";"Vuosi";"Liikennesuorite"
"01";"Uusimaa";"Yhteensä";"Yhteensä";"2020";7657
"02";"Varsinais-Suomi";"Yhteensä";"Yhteensä";"2020";2960
...
```
3. Tuodaan muokatut csv-tiedostot Qgis-karttaohjelmaan, "Layer - Taso" - valikon kautta -> Add Layer -> Add Delimited Text File
<img src="kuvat\import.png">

4. Lisätään ensin Tilastokeskuksen WFS-rajapinta ([Tilastokeskus avoimet paikkatietoaineistot](https://www.stat.fi/org/avoindata/paikkatietoaineistot.html) Qgis-karttaohjelmaan, "Layer - Taso" - valikon kautta -> Add Layer -> Add WFS Layer -> New -> Annetaan nimeksi "Stat" ja URL-kenttään liitetään kuntapohjaisten tilastoalueiden WFS-rajapinta-osoite: "https://geo.stat.fi/geoserver/tilastointialueet/wfs"
<img src="kuvat\wfs.png">

5. Painetaan Connect ja valitaan listalta Maakunnat 2020 -karttataso ja painetaan Add.
<img src="kuvat\maakunnat.png">

6. Liitetään tilastotiedot karttatasoon painamalla oikeaa hiiren painiketta tilastointialueet-tason päällä ja valitaan "Joins". Painetaan plus-painiketta ja painetaan OK, toistetaan kunnes sekä riistaonnettomuus-taso (001-alkuinen) ja liikennesuorite-taso (004-alkuinen) on liitettynä maakuntatasoon.
<img src="kuvat\properties.png">

7. Mennään Symbology-välilehdellä ja valitaan visualisointityyliksi "Graduated". Painetaan Value-rivin kohdalla olevaa "Epsilon" -painiketta
<img src="kuvat\join.png">

8. Valitaan Fields and values - kentästä 001-taulun lukumäärä ja 004-taulun liikennesuorite. Liikennesuoritteen yksikkö on miljardi autokilometriä. Jaetaan se kymmennellä, jotta saadaan jakajan yksiköksi 100 000 autokilometriä. Lisätään myös to_int-funktiot muuttujien eteen, koska ne on luettu sisään merkkimuotoisina.
<img src="kuvat\kaava.png">

9. Painetaan Classify-painiketta, vaihdetaan Mode-kentästä Pretty Breaks ja valitaan color rampi-valikosta Magma. Valitaan samasta valikosta vielä Invert color ramp, koska tumma väri sopii paremmin kuvaamaan alueita, jossa paljon onnettomuuksia. Painetaan OK ja nyt maakunnat on luokiteltu.
<img src="kuvat\classify.png">

```markdown
Syntax highlighted code block

# Header 1
## Header 2
### Header 3

- Bulleted
- List

1. Numbered
2. List

**Bold** and _Italic_ and `Code` text

[Link](url) and ![Image](src)
```

For more details see [Basic writing and formatting syntax](https://docs.github.com/en/github/writing-on-github/getting-started-with-writing-and-formatting-on-github/basic-writing-and-formatting-syntax).

### Jekyll Themes

Your Pages site will use the layout and styles from the Jekyll theme you have selected in your [repository settings](https://github.com/mkokkone/visualisointikoulutus/settings/pages). The name of this theme is saved in the Jekyll `_config.yml` configuration file.

### Support or Contact

Having trouble with Pages? Check out our [documentation](https://docs.github.com/categories/github-pages-basics/) or [contact support](https://support.github.com/contact) and we’ll help you sort it out.
