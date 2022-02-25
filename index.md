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

Muita muuttujia ei tarvitse erikseen valita vaan muista muuttujista on esivalittuna yhteensä-arvot
2. Valitaan esitysmuodoksi "Vaakapylväskuvio"
3. Käännetään kuutiota kerran myötäpäivään, jotta saadaan maakunnat pystyakselille
4. Muokataan kuvion kuvioasetuksia; asetetaan otsikko, muokataan korkeutta ja laitetaan otsikot vaakaan
5. Tallennetaan kuvio palvelimelle tallennetuksi hauksi, jolloin se voidaan jakaa sellaisenaan. Kuvion voi myös tallentaa haluamassaan formaatissa.

Edellä luotu kuvio on liitetty osaksi tätä sivustoa ja kuva luetaan tälle sivulle suoraan px-webistä.

<img src="https://pxnet2.stat.fi:443/PXWeb/sq/6679640d-e464-402c-a3d7-10e118e8b688" alt="Kuvioissa riistaonnettomuudet maakunnittain vuosina 2019-2021. Riistaonnettomuuksia tapahtui vuosittain eniten Varsinais-Suomen, Uudenmaan, Pirkanmaan, Satakunnan maakunnissa ja Kanta-Hämeen maakunnissa.">

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
