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

### Kartan luominen
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

### Kartasta kuvioksi

Jotta kartta on ymmärrettävä lisätään teemakarttakuvioon vielä otsikko, lähteet ja selite. Tätä varten käytetään ohjelman sisään rakennettua tulostus-editoria.

1. Lisätään uusi tulostus painamalla Project -> New Print Layout -> ja annetaan sille nimi
<img src="kuvat\print_layout.png">

2. Käännetaan tulostuspohja pystysuoraan valitsemalla oikean laidan valikosta Item properties ja orientation-kohdasta Portrait
<img src="kuvat\portrait.png">

3. Lisätään kartta painamalla Add item ja Add Map. "Piirretään" "paperin" kokoinen laatikko. Karttakuvan skaalausta voi säätää oikean laidan valikosta, mutta nopeinta on zoomata ja liikuttaa karttakuvaa pääikkunassa ja päivittää muutokset tulostuseditoriin painamalla Set Map Extent to Match Main Canvas Extent
<img src="kuvat\Extent.png">

4. Lisätään selite painamalla Add item ja Add Legend. Piirretään laatikko siihen kohtaan, johon selite halutaan sijoittaa
<img src="kuvat\classify.png">

5. Muotoillaan selitteen otsikko ottamalla ensin ruksi pois Auto Update -kohdasta ja sen jälkeen tuplaklikkaamalla otsikkoa. 
<img src="kuvat\auto_update.png">

7. Lisätään otsikoksi "Onnettomuudet / 100 000 autokilometriä".
<img src="kuvat\legend-otsikko.png">

8. Piilotetaan tilastotasot 
<img src="kuvat\piilotus.png">

9. Lisätään otsikko Add item ja Add Label. Lisätään otsikoksi "Riistaonnettomuudet suhteessa liikennesuoritteeseen maakunnittain Manner-Suomessa vuonna 2020". Font-valikosta voi muokata tekstin kokoa ja tyyliä.
<img src="kuvat\Otsikko.png">

10. Lisätään lähteet samaan tapaan kuin otsikko.

11. Tallennetaan lopputulos kuvaksi Layout -> Export as Image


## R Riistaonnettomuudet eläinlajeittain, maakunnittain ja kuukausittain

1. Ladataan pohjadataksi maakunnittainen riistaonnettomuusaineisto PX-Web-taulukosta. Ladataan tiedot eläinlajeittain, maakunnittian ja kuukasitasolla. Tallennetaan tiedot relaatiotiedostoon, jotta tietoja on helppo käsitellä seuraavassa vaiheessa. Jätetään yhteensä-luokat pois.
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

8. Maakuntien erot onnettomuusmäärissä ovat isoja, joten vapautetaan akseleiden skaalaus, scales = "free" -optiolla.
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

11. Pysytellään kuitenkin kääntämisessä, aikasemmassa esimerkissä akselin otsikot menivät kuvion päälle, joten blankotaan ensin ja asetetaan sitten akselin otsikot uudestaan
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


## D3js - Mihin eläimeen törmätään eniten Manner-Suomen eri maakunnissa?


```
<body>
 <meta charset="utf-8">


 <style>


div.tooltip {	
    position: absolute;

    text-align: left;			
    width: 150px;					
    height: 40px;					
    padding: 2px;				
    font: 14px sans-serif;		
    background: black;	
    border: 6px;		
    border-radius: 8px;			
    pointer-events: none;

}
svg {
  background: url('kuvat/maakunta700.png') no-repeat;
}
</style>




 <!-- Add 2 buttons-->

<script src="https://d3js.org/d3.v4.min.js">
</script>



<div id="chart"> 





<script>

//(function test () {
	var width = 700,
	height=700;
//data
	var data = [
{id:'MK01', name: 'Uusimaa', onn:2982, xk:302, yk:649 ,kuva: 'kuvat/valkohantapeura.png' , altt:'Maakunnassa Uusimaa tapahtui vuonna 2020 yhteensä 2982 riistaonnettomuutta. Eniten kolareita aiheutti valkohäntäpeura.' },
{id:'MK02', name: 'Varsinais-Suomi', onn:3309, xk:232, yk:630 ,kuva: 'kuvat/valkohantapeura.png' , altt:'Maakunnassa Varsinais-Suomi tapahtui vuonna 2020 yhteensä 3309 riistaonnettomuutta. Eniten kolareita aiheutti valkohäntäpeura.' },
{id:'MK05', name: 'Kanta-Häme', onn:1341, xk:289, yk:614 ,kuva: 'kuvat/valkohantapeura.png' , altt:'Maakunnassa Kanta-Häme tapahtui vuonna 2020 yhteensä 1341 riistaonnettomuutta. Eniten kolareita aiheutti valkohäntäpeura.' },
{id:'MK07', name: 'Päijät-Häme', onn:474, xk:330, yk:599 ,kuva: 'kuvat/metsakauris.png' , altt:'Maakunnassa Päijät-Häme tapahtui vuonna 2020 yhteensä 474 riistaonnettomuutta. Eniten kolareita aiheutti metsäkauris.' },
{id:'MK08', name: 'Kymenlaakso', onn:165, xk:372, yk:628 ,kuva: 'kuvat/metsakauris.png' , altt:'Maakunnassa Kymenlaakso tapahtui vuonna 2020 yhteensä 165 riistaonnettomuutta. Eniten kolareita aiheutti metsäkauris.' },
{id:'MK09', name: 'Etelä-Karjala', onn:121, xk:412, yk:603 ,kuva: 'kuvat/hirvi.png' , altt:'Maakunnassa Etelä-Karjala tapahtui vuonna 2020 yhteensä 121 riistaonnettomuutta. Eniten kolareita aiheutti hirvi.' },
{id:'MK04', name: 'Satakunta', onn:1462, xk:217, yk:569 ,kuva: 'kuvat/valkohantapeura.png' , altt:'Maakunnassa Satakunta tapahtui vuonna 2020 yhteensä 1462 riistaonnettomuutta. Eniten kolareita aiheutti valkohäntäpeura.' },
{id:'MK06', name: 'Pirkanmaa', onn:1732, xk:269, yk:563 ,kuva: 'kuvat/valkohantapeura.png' , altt:'Maakunnassa Pirkanmaa tapahtui vuonna 2020 yhteensä 1732 riistaonnettomuutta. Eniten kolareita aiheutti valkohäntäpeura.' },
{id:'MK13', name: 'Keski-Suomi', onn:444, xk:326, yk:504 ,kuva: 'kuvat/metsakauris.png' , altt:'Maakunnassa Keski-Suomi tapahtui vuonna 2020 yhteensä 444 riistaonnettomuutta. Eniten kolareita aiheutti metsäkauris.' },
{id:'MK14', name: 'Etelä-Pohjanmaa', onn:339, xk:255, yk:492 ,kuva: 'kuvat/metsakauris.png' , altt:'Maakunnassa Etelä-Pohjanmaa tapahtui vuonna 2020 yhteensä 339 riistaonnettomuutta. Eniten kolareita aiheutti metsäkauris.' },
{id:'MK15', name: 'Pohjanmaa', onn:444, xk:220, yk:467 ,kuva: 'kuvat/metsakauris.png' , altt:'Maakunnassa Pohjanmaa tapahtui vuonna 2020 yhteensä 444 riistaonnettomuutta. Eniten kolareita aiheutti metsäkauris.' },
{id:'MK10', name: 'Etelä-Savo', onn:170, xk:393, yk:561 ,kuva: 'kuvat/hirvi.png' , altt:'Maakunnassa Etelä-Savo tapahtui vuonna 2020 yhteensä 170 riistaonnettomuutta. Eniten kolareita aiheutti hirvi.' },
{id:'MK11', name: 'Pohjois-Savo', onn:186, xk:385, yk:470 ,kuva: 'kuvat/hirvi.png' , altt:'Maakunnassa Pohjois-Savo tapahtui vuonna 2020 yhteensä 186 riistaonnettomuutta. Eniten kolareita aiheutti hirvi.' },
{id:'MK12', name: 'Pohjois-Karjala', onn:134, xk:463, yk:483 ,kuva: 'kuvat/hirvi.png' , altt:'Maakunnassa Pohjois-Karjala tapahtui vuonna 2020 yhteensä 134 riistaonnettomuutta. Eniten kolareita aiheutti hirvi.' },
{id:'MK16', name: 'Keski-Pohjanmaa', onn:88, xk:290, yk:444 ,kuva: 'kuvat/metsakauris.png' , altt:'Maakunnassa Keski-Pohjanmaa tapahtui vuonna 2020 yhteensä 88 riistaonnettomuutta. Eniten kolareita aiheutti metsäkauris.' },
{id:'MK17', name: 'Pohjois-Pohjanmaa', onn:295, xk:340, yk:374 ,kuva: 'kuvat/metsakauris.png' , altt:'Maakunnassa Pohjois-Pohjanmaa tapahtui vuonna 2020 yhteensä 295 riistaonnettomuutta. Eniten kolareita aiheutti metsäkauris.' },
{id:'MK18', name: 'Kainuu', onn:92, xk:416, yk:377 ,kuva: 'kuvat/hirvi.png' , altt:'Maakunnassa Kainuu tapahtui vuonna 2020 yhteensä 92 riistaonnettomuutta. Eniten kolareita aiheutti hirvi.' },
{id:'MK19', name: 'Lappi', onn:226, xk:359, yk:182 ,kuva: 'kuvat/hirvi.png' , altt:'Maakunnassa Lappi tapahtui vuonna 2020 yhteensä 226 riistaonnettomuutta. Eniten kolareita aiheutti hirvi.' }


							
];


//määritellään tooltippi
var dataFilter = data.map(function(d){return {kuva: d.kuva, name: d.name, xk: d.xk, yk: d.yk,value: d.onn,onn: d.onn, altt: d.altt } });

//määritellään kuvio
	var svg = d3.select("#chart")
	.append("svg")
	.attr("height", height)
	.attr("width", width)
	.append("g")
	.attr("transform", "translate(0,0)")
	
	d3.select('#tooltip')
	.append('div')

	.attr('style', 'position: absolute; opacity: 0;');


	var defs = svg.append("defs");
	defs.append("pattern")
	.attr("id", "elain")
	.attr("height", "100%")
	.attr("width", "100%")
	.attr("patternContentUnits", "objectBoundingBox")
	.append ("image")
	.attr("height",1)
	.attr("width",1)
	.attr("preserveAspectRatio", "none")
	.attr("xmlns:xlink", "http://www.w3.org/1999/xlink")
	.attr("xlink:href","IMG_20210707_1458482.jpg")

//skaalataan pallojen koko
	var radiusScale = d3.scaleSqrt().domain([1,10000]).range([1,50])	
	
	//var forceY = d3.forceY(height/2).strength(0.05)
	
	// if (d.maakunta=== '01') { return 300
	//} else if (d.maakunta=== '02') { return 350
	//} else if (d.maakunta=== '04') { return 400
	//} else if (d.maakunta=== '05') { return 450
	//} else if (d.maakunta=== '06') { return 500
	//} else if (d.maakunta=== '07') { return 550
	//} else if (d.maakunta=== '08') { return 600
	//} else if (d.maakunta=== '09') { return 660
	//} else if (d.maakunta=== '10') { return 700
	//} else if (d.maakunta=== '11') { return 750
	//} else if (d.maakunta=== '12') { return 800
	//} else if (d.maakunta=== '13') { return 850
	//} else if (d.maakunta=== '14') { return 900
	//} else if (d.maakunta=== '15') { return 950
	//} else if (d.maakunta=== '16') { return 1000
	//} else if (d.maakunta=== '17') { return 1050
	//} else if (d.maakunta=== '18') { return 1100
	//} else  return 1150
	
	
	var forceX = d3.forceX(function(d) {
		return d.xk
	}).strength(1.0)
	var forceY = d3.forceY(function(d) {
		return d.yk
	}).strength(1.0)
	
	
		
	//}).strength(0.05)

//	var forceX = d3.forceX(function(d) {
//	  return d.xk
//	} else if (d.maakunta=== '02') { return 700
//	} else if (d.maakunta=== '04') { return 500
//	} else if (d.maakunta=== '05') { return 550
//	} else if (d.maakunta=== '06') { return 500
//	} else if (d.maakunta=== '07') { return 525
//	} else if (d.maakunta=== '08') { return 500
//	} else if (d.maakunta=== '09') { return 500
//	} else if (d.maakunta=== '10') { return 450
//	} else if (d.maakunta=== '11') { return 400
//	} else if (d.maakunta=== '12') { return 350
//	} else if (d.maakunta=== '13') { return 400
//	} else if (d.maakunta=== '14') { return 400
//	} else if (d.maakunta=== '15') { return 300
//	} else if (d.maakunta=== '16') { return 250
//	} else if (d.maakunta=== '17') { return 200
//	} else if (d.maakunta=== '18') { return 150
	
	
	
		
//	}).strength(0.05)


	var simulation = d3.forceSimulation()
	//.force("x", d3.forceX(width/2).strength(0.05))

	.force("x", forceX)
	.force("y", forceY)

	//.force("y", d3.forceY(width/2).strength(0.05))
	.force("collide", d3.forceCollide(function(d){
		return radiusScale(d.value)+1;
	}))


	var tooltip = d3.select("#chart")
      .append("div")
      .style("opacity", 0)
      .attr("class", "tooltip")
      .style("background-color", "#ea7404")
      .style("border-radius", "10px")
      .style("padding", "10px")
      .style("color", "black")

	var showTooltip = function(d) {
	
		tooltip
		  .transition()
		  .duration(200)
		tooltip
		  .style("opacity", 1)
	.html("<b>" +d.name+"</b>" + "<br> Onnettomuuksia: " + d.onn)	       
		//.style("left", (d3.mouse(this)[0]+50) + "px")
		//.style("top", (d3.mouse(this)[1]+14550) + "px")
		//.style("left", (d3.mouse(this)[0]+10) + "px")
		//.style("top", (d3.mouse(this)[1]+0) + "px")
      		.style("left", (parseInt(d3.mouse(this)[0]+10) + document.getElementById("chart").offsetLeft) + "px")     
      		.style("top", (parseInt(d3.mouse(this)[1]+10) + document.getElementById("chart").offsetTop) + "px")
		  
	  }
	  var moveTooltip = function(d) {

		tooltip
	    
        
		 //.style("left", (d3.mouse(this)[0]+50) + "px")
		 //.style("top", (d3.mouse(this)[1]+14550) + "px")
	      	 .style("left", (parseInt(d3.mouse(this)[0]+10) + document.getElementById("chart").offsetLeft) + "px")     
      		 .style("top", (parseInt(d3.mouse(this)[1]+10) + document.getElementById("chart").offsetTop) + "px") 


		  
	  }
	  var hideTooltip = function(d) {
		tooltip
		  .transition()
		  .duration(200)
		  .style("opacity", 0)
	  }


	//laitetaan data "jonoon"
function testi () {
d3.queue()
.await(ready)
function ready (error, datapoints) {
		defs.selectAll(".artist-pattern")
		.data(dataFilter)
		.enter().append("pattern")
		.attr("class", "artist-pattern")
		.attr("id", function(d) {
			return d.name.replace(" ","-")
		})
		.attr("height", "100%")
		.attr("width", "100%")
		.attr("patternContentUnits", "objectBoundingBox")
		.append ("image")
		.attr("height",1)
		.attr("width",1)
		.attr("preserveAspectRatio", "none")
		.attr("xmlns:xlink", "http://www.w3.org/1999/xlink")
		.attr("xlink:href",function(d) {
			return d.kuva
		})

		
		var circles = svg.selectAll(".artist")
		.data(dataFilter)
		.enter().append("circle")
		.attr("class", "artist")
		.attr("r", function(d) {
			return radiusScale(d.value)
		})
		.attr("fill", "#0073b0")
		.on("mouseover.teksti", showTooltip )
		.on("mousemove.teksti", moveTooltip )
		.on("mouseleave.teksti", hideTooltip )
		.on('mouseover', function(d) {
			d3.select('#tooltip').style('opacity', 1).text(d)
		 })
		 // on('mouseout', function() {
		//	d3.select('#tooltip').style('opacity', 0)
		//  })
		.on('mouseover', function(d, i) {
			console.log("mouseover on", this)
			console.log("mouseover on", d.value)

      // make the mouseover'd element
      // bigger and red




      d3.select(this)
		//.data(data)
        .transition()
        .duration(100)
        .attr('r', function(d) {
			return radiusScale(d.value)+20
		})
        .attr('fill', function (d) {
		return "url(#"+ d.name.replace(" ","-") +")"
			//.attr('fill', function (d) {
				//return "url(#elain)"
		})


    })

    .on('mouseout', function(d, i) {
      console.log("mouseout", this);
      // return the mouseover'd element
      // to being smaller and black

      d3.select(this)
        .transition()
        .duration(100)
        .attr('r', function(d) {
			return radiusScale(d.value)
		})
		//poista kommentit, jos halutaan palauttaa sininenn pallura
        //.attr('fill', 'lightblue');
    })

	  
		simulation.nodes(dataFilter)

		.on('tick', ticked)
		function ticked() {
			circles
			.attr("cx", function(d) {
				return d.x
			})
			.attr("cy", function(d) {
				return d.y
			})	


		}




}}
testi()



function vaihdaluku(color){
	


	
	dataFilter = data.map(function(d){return {kuva: d.kuva, name: d.name, xk: d.xk, yk: d.yk,value: d.onn,onn: d.onn,onnkvl: d.onnkvl,kaavio: d.kaavio} });
	g.selectAll(".circle")
	//testi() 

 }

 
//})()




</script>
</div>

</body>
```



Katso alla olevasta kuviosta mikä riistaeläin aiheuttaa eniten kolareita kunnittain ja miten onnettomuudet jakautuvat vuoden sisällä eläinlajeittain. Alla on mallikuvat eläimistä tunnistamisen helpottamiseksi.
<img src="kuvat\pjimage.jpg">



<body>
 <meta charset="utf-8">


 <style>


div.tooltip {	
    position: absolute;

    text-align: left;			
    width: 150px;					
    height: 40px;					
    padding: 2px;				
    font: 14px sans-serif;		
    background: black;	
    border: 6px;		
    border-radius: 8px;			
    pointer-events: none;

}
svg {
  background: url('kuvat/maakunta700.png') no-repeat;
}
</style>




 <!-- Add 2 buttons-->

<script src="https://d3js.org/d3.v4.min.js">
</script>



<div id="chart"> 





<script>

//(function test () {
	var width = 700,
	height=700;
//data
	var data = [
{id:'MK01', name: 'Uusimaa', onn:2982, xk:302, yk:649 ,kuva: 'kuvat/valkohantapeura.png' , altt:'Maakunnassa Uusimaa tapahtui vuonna 2020 yhteensä 2982 riistaonnettomuutta. Eniten kolareita aiheutti valkohäntäpeura.' },
{id:'MK02', name: 'Varsinais-Suomi', onn:3309, xk:232, yk:630 ,kuva: 'kuvat/valkohantapeura.png' , altt:'Maakunnassa Varsinais-Suomi tapahtui vuonna 2020 yhteensä 3309 riistaonnettomuutta. Eniten kolareita aiheutti valkohäntäpeura.' },
{id:'MK05', name: 'Kanta-Häme', onn:1341, xk:289, yk:614 ,kuva: 'kuvat/valkohantapeura.png' , altt:'Maakunnassa Kanta-Häme tapahtui vuonna 2020 yhteensä 1341 riistaonnettomuutta. Eniten kolareita aiheutti valkohäntäpeura.' },
{id:'MK07', name: 'Päijät-Häme', onn:474, xk:330, yk:599 ,kuva: 'kuvat/metsakauris.png' , altt:'Maakunnassa Päijät-Häme tapahtui vuonna 2020 yhteensä 474 riistaonnettomuutta. Eniten kolareita aiheutti metsäkauris.' },
{id:'MK08', name: 'Kymenlaakso', onn:165, xk:372, yk:628 ,kuva: 'kuvat/metsakauris.png' , altt:'Maakunnassa Kymenlaakso tapahtui vuonna 2020 yhteensä 165 riistaonnettomuutta. Eniten kolareita aiheutti metsäkauris.' },
{id:'MK09', name: 'Etelä-Karjala', onn:121, xk:412, yk:603 ,kuva: 'kuvat/hirvi.png' , altt:'Maakunnassa Etelä-Karjala tapahtui vuonna 2020 yhteensä 121 riistaonnettomuutta. Eniten kolareita aiheutti hirvi.' },
{id:'MK04', name: 'Satakunta', onn:1462, xk:217, yk:569 ,kuva: 'kuvat/valkohantapeura.png' , altt:'Maakunnassa Satakunta tapahtui vuonna 2020 yhteensä 1462 riistaonnettomuutta. Eniten kolareita aiheutti valkohäntäpeura.' },
{id:'MK06', name: 'Pirkanmaa', onn:1732, xk:269, yk:563 ,kuva: 'kuvat/valkohantapeura.png' , altt:'Maakunnassa Pirkanmaa tapahtui vuonna 2020 yhteensä 1732 riistaonnettomuutta. Eniten kolareita aiheutti valkohäntäpeura.' },
{id:'MK13', name: 'Keski-Suomi', onn:444, xk:326, yk:504 ,kuva: 'kuvat/metsakauris.png' , altt:'Maakunnassa Keski-Suomi tapahtui vuonna 2020 yhteensä 444 riistaonnettomuutta. Eniten kolareita aiheutti metsäkauris.' },
{id:'MK14', name: 'Etelä-Pohjanmaa', onn:339, xk:255, yk:492 ,kuva: 'kuvat/metsakauris.png' , altt:'Maakunnassa Etelä-Pohjanmaa tapahtui vuonna 2020 yhteensä 339 riistaonnettomuutta. Eniten kolareita aiheutti metsäkauris.' },
{id:'MK15', name: 'Pohjanmaa', onn:444, xk:220, yk:467 ,kuva: 'kuvat/metsakauris.png' , altt:'Maakunnassa Pohjanmaa tapahtui vuonna 2020 yhteensä 444 riistaonnettomuutta. Eniten kolareita aiheutti metsäkauris.' },
{id:'MK10', name: 'Etelä-Savo', onn:170, xk:393, yk:561 ,kuva: 'kuvat/hirvi.png' , altt:'Maakunnassa Etelä-Savo tapahtui vuonna 2020 yhteensä 170 riistaonnettomuutta. Eniten kolareita aiheutti hirvi.' },
{id:'MK11', name: 'Pohjois-Savo', onn:186, xk:385, yk:470 ,kuva: 'kuvat/hirvi.png' , altt:'Maakunnassa Pohjois-Savo tapahtui vuonna 2020 yhteensä 186 riistaonnettomuutta. Eniten kolareita aiheutti hirvi.' },
{id:'MK12', name: 'Pohjois-Karjala', onn:134, xk:463, yk:483 ,kuva: 'kuvat/hirvi.png' , altt:'Maakunnassa Pohjois-Karjala tapahtui vuonna 2020 yhteensä 134 riistaonnettomuutta. Eniten kolareita aiheutti hirvi.' },
{id:'MK16', name: 'Keski-Pohjanmaa', onn:88, xk:290, yk:444 ,kuva: 'kuvat/metsakauris.png' , altt:'Maakunnassa Keski-Pohjanmaa tapahtui vuonna 2020 yhteensä 88 riistaonnettomuutta. Eniten kolareita aiheutti metsäkauris.' },
{id:'MK17', name: 'Pohjois-Pohjanmaa', onn:295, xk:340, yk:374 ,kuva: 'kuvat/metsakauris.png' , altt:'Maakunnassa Pohjois-Pohjanmaa tapahtui vuonna 2020 yhteensä 295 riistaonnettomuutta. Eniten kolareita aiheutti metsäkauris.' },
{id:'MK18', name: 'Kainuu', onn:92, xk:416, yk:377 ,kuva: 'kuvat/hirvi.png' , altt:'Maakunnassa Kainuu tapahtui vuonna 2020 yhteensä 92 riistaonnettomuutta. Eniten kolareita aiheutti hirvi.' },
{id:'MK19', name: 'Lappi', onn:226, xk:359, yk:182 ,kuva: 'kuvat/hirvi.png' , altt:'Maakunnassa Lappi tapahtui vuonna 2020 yhteensä 226 riistaonnettomuutta. Eniten kolareita aiheutti hirvi.' }


							
];


//määritellään tooltippi
var dataFilter = data.map(function(d){return {kuva: d.kuva, name: d.name, xk: d.xk, yk: d.yk,value: d.onn,onn: d.onn, altt: d.altt } });

//määritellään kuvio
	var svg = d3.select("#chart")
	.append("svg")
	.attr("height", height)
	.attr("width", width)
	.append("g")
	.attr("transform", "translate(0,0)")
	
	d3.select('#tooltip')
	.append('div')

	.attr('style', 'position: absolute; opacity: 0;');


	var defs = svg.append("defs");
	defs.append("pattern")
	.attr("id", "elain")
	.attr("height", "100%")
	.attr("width", "100%")
	.attr("patternContentUnits", "objectBoundingBox")
	.append ("image")
	.attr("height",1)
	.attr("width",1)
	.attr("preserveAspectRatio", "none")
	.attr("xmlns:xlink", "http://www.w3.org/1999/xlink")
	.attr("xlink:href","IMG_20210707_1458482.jpg")

//skaalataan pallojen koko
	var radiusScale = d3.scaleSqrt().domain([1,10000]).range([1,50])	
	
	//var forceY = d3.forceY(height/2).strength(0.05)
	
	// if (d.maakunta=== '01') { return 300
	//} else if (d.maakunta=== '02') { return 350
	//} else if (d.maakunta=== '04') { return 400
	//} else if (d.maakunta=== '05') { return 450
	//} else if (d.maakunta=== '06') { return 500
	//} else if (d.maakunta=== '07') { return 550
	//} else if (d.maakunta=== '08') { return 600
	//} else if (d.maakunta=== '09') { return 660
	//} else if (d.maakunta=== '10') { return 700
	//} else if (d.maakunta=== '11') { return 750
	//} else if (d.maakunta=== '12') { return 800
	//} else if (d.maakunta=== '13') { return 850
	//} else if (d.maakunta=== '14') { return 900
	//} else if (d.maakunta=== '15') { return 950
	//} else if (d.maakunta=== '16') { return 1000
	//} else if (d.maakunta=== '17') { return 1050
	//} else if (d.maakunta=== '18') { return 1100
	//} else  return 1150
	
	
	var forceX = d3.forceX(function(d) {
		return d.xk
	}).strength(1.0)
	var forceY = d3.forceY(function(d) {
		return d.yk
	}).strength(1.0)
	
	
		
	//}).strength(0.05)

//	var forceX = d3.forceX(function(d) {
//	  return d.xk
//	} else if (d.maakunta=== '02') { return 700
//	} else if (d.maakunta=== '04') { return 500
//	} else if (d.maakunta=== '05') { return 550
//	} else if (d.maakunta=== '06') { return 500
//	} else if (d.maakunta=== '07') { return 525
//	} else if (d.maakunta=== '08') { return 500
//	} else if (d.maakunta=== '09') { return 500
//	} else if (d.maakunta=== '10') { return 450
//	} else if (d.maakunta=== '11') { return 400
//	} else if (d.maakunta=== '12') { return 350
//	} else if (d.maakunta=== '13') { return 400
//	} else if (d.maakunta=== '14') { return 400
//	} else if (d.maakunta=== '15') { return 300
//	} else if (d.maakunta=== '16') { return 250
//	} else if (d.maakunta=== '17') { return 200
//	} else if (d.maakunta=== '18') { return 150
	
	
	
		
//	}).strength(0.05)


	var simulation = d3.forceSimulation()
	//.force("x", d3.forceX(width/2).strength(0.05))

	.force("x", forceX)
	.force("y", forceY)

	//.force("y", d3.forceY(width/2).strength(0.05))
	.force("collide", d3.forceCollide(function(d){
		return radiusScale(d.value)+1;
	}))


	var tooltip = d3.select("#chart")
      .append("div")
      .style("opacity", 0)
      .attr("class", "tooltip")
      .style("background-color", "#ea7404")
      .style("border-radius", "10px")
      .style("padding", "10px")
      .style("color", "black")

	var showTooltip = function(d) {
	
		tooltip
		  .transition()
		  .duration(200)
		tooltip
		  .style("opacity", 1)
	.html("<b>" +d.name+"</b>" + "<br> Onnettomuuksia: " + d.onn)	       
		//.style("left", (d3.mouse(this)[0]+50) + "px")
		//.style("top", (d3.mouse(this)[1]+14550) + "px")
		//.style("left", (d3.mouse(this)[0]+10) + "px")
		//.style("top", (d3.mouse(this)[1]+0) + "px")
      		.style("left", (parseInt(d3.mouse(this)[0]+10) + document.getElementById("chart").offsetLeft) + "px")     
      		.style("top", (parseInt(d3.mouse(this)[1]+10) + document.getElementById("chart").offsetTop) + "px")
		  
	  }
	  var moveTooltip = function(d) {

		tooltip
	    
        
		 //.style("left", (d3.mouse(this)[0]+50) + "px")
		 //.style("top", (d3.mouse(this)[1]+14550) + "px")
	      	 .style("left", (parseInt(d3.mouse(this)[0]+10) + document.getElementById("chart").offsetLeft) + "px")     
      		 .style("top", (parseInt(d3.mouse(this)[1]+10) + document.getElementById("chart").offsetTop) + "px") 


		  
	  }
	  var hideTooltip = function(d) {
		tooltip
		  .transition()
		  .duration(200)
		  .style("opacity", 0)
	  }


	//laitetaan data "jonoon"
function testi () {
d3.queue()
.await(ready)
function ready (error, datapoints) {
		defs.selectAll(".artist-pattern")
		.data(dataFilter)
		.enter().append("pattern")
		.attr("class", "artist-pattern")
		.attr("id", function(d) {
			return d.name.replace(" ","-")
		})
		.attr("height", "100%")
		.attr("width", "100%")
		.attr("patternContentUnits", "objectBoundingBox")
		.append ("image")
		.attr("height",1)
		.attr("width",1)
		.attr("preserveAspectRatio", "none")
		.attr("xmlns:xlink", "http://www.w3.org/1999/xlink")
		.attr("xlink:href",function(d) {
			return d.kuva
		})

		
		var circles = svg.selectAll(".artist")
		.data(dataFilter)
		.enter().append("circle")
		.attr("class", "artist")
		.attr("r", function(d) {
			return radiusScale(d.value)
		})
		.attr("fill", "#0073b0")
		.on("mouseover.teksti", showTooltip )
		.on("mousemove.teksti", moveTooltip )
		.on("mouseleave.teksti", hideTooltip )
		.on('mouseover', function(d) {
			d3.select('#tooltip').style('opacity', 1).text(d)
		 })
		 // on('mouseout', function() {
		//	d3.select('#tooltip').style('opacity', 0)
		//  })
		.on('mouseover', function(d, i) {
			console.log("mouseover on", this)
			console.log("mouseover on", d.value)

      // make the mouseover'd element
      // bigger and red




      d3.select(this)
		//.data(data)
        .transition()
        .duration(100)
        .attr('r', function(d) {
			return radiusScale(d.value)+20
		})
        .attr('fill', function (d) {
		return "url(#"+ d.name.replace(" ","-") +")"
			//.attr('fill', function (d) {
				//return "url(#elain)"
		})


    })

    .on('mouseout', function(d, i) {
      console.log("mouseout", this);
      // return the mouseover'd element
      // to being smaller and black

      d3.select(this)
        .transition()
        .duration(100)
        .attr('r', function(d) {
			return radiusScale(d.value)
		})
		//poista kommentit, jos halutaan palauttaa sininenn pallura
        //.attr('fill', 'lightblue');
    })

	  
		simulation.nodes(dataFilter)

		.on('tick', ticked)
		function ticked() {
			circles
			.attr("cx", function(d) {
				return d.x
			})
			.attr("cy", function(d) {
				return d.y
			})	


		}




}}
testi()



function vaihdaluku(color){
	


	
	dataFilter = data.map(function(d){return {kuva: d.kuva, name: d.name, xk: d.xk, yk: d.yk,value: d.onn,onn: d.onn,onnkvl: d.onnkvl,kaavio: d.kaavio} });
	g.selectAll(".circle")
	//testi() 

 }

 
//})()




</script>
</div>

</body>
