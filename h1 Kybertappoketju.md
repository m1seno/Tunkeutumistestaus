# Kybertappoketju

Harjoitukset on tehty kotitoimistossa Kaarinassa. Koneena oli Lenovo V14 G4 AMN. Käyttöjärjestelmänä Windows 11 Pro version 25H2. Virtuaalikoneena oli Linux Kali 6.16.8+kali-amd64.

## x

#### Intrusion Kill Chain (Amin, Cloppert & Hutchins 2011, 4-5)
- Intrusion Kill Chain on kyberhyökkäyksen toimintatapa, joka on johdettu Yhdysvaltojen armeijan vastaavanlaisesta lähestymistavasta. Se kuvaa hyökkäyksen etenemistä useassa peräkkäisessä vaiheessa, joista jokainen on tärkeä kokonaisuuden onnistumisen kannalta.
- Toimintatapa sisältää seuraavat vaiheet:
    - Reconnaissance, jossa eri menetelmiä hyödyntäen kerätään mahdollisimman kattavasti tietoa kohteesta.
    -  Weaponization, jossa haittaohjelma yhdistetään hyötykuormaksi, esimerkiksi tiedostoon.
    - Delivery, jossa hyötykuorma toimitetaan kohteelle.
    - Exploitation, jossa kohteen haavoittuvuutta hyödynnetään hyökkäyksen käynnistämiseksi.
    - Installation, jossa haittaohjelma asennetaan kohdejärjestelmään ja mahdollistetaan pysyvä pääsy.
    - Command and Control, jossa hyökkääjä saa etäyhteyden saastuneeseen koneeseen.
    - Actions on Objectives, jossa hyökkääjä toteuttaa varsinaisen tavoitteensa, kuten tiedon varastamisen tai käyttää järjestelmää muihin hyökkäyksiin.

#### KKO:2003:36 (Finlex 2003)
- Vuonna 1998 17-vuotias henkilö suoritti porttiskannauksen Osuuspankkikeskus-OPK:n tietojärjestelmään, tarkoituksenaan löytää siitä haavoittuvuuksia .
- Tapaus eteni korkeimpaan oikeuteen asti, jossa henkilö todettiin syylliseksi tietoliikenteen häirintään sekä tietomurron yritykseen. Hänet tuomittiin maksamaan asianomaisille yhteensä 75 000 markan korvaukset.
- Tapaus korostaa tietomurtoihin liittyvien rikosten vakavuutta, sillä jo pelkästä tietomurron yrityksestä seurasi merkittävät korvausvelvoitteet.

#### The Art of Hacking (McCoy, C., Santos, O., Sternstein, J. & Taylor R. Huhtikuu 2019)
- Video käy läpi erilaisten porttiskannereiden käyttöä käytännönläheisesti.
- Ensimmäisenä työkaluna esitellään Nmapin käyttöä. Esimerkissä lähetetään TCP SYN -paketti ja se kohdistetaan yhteen tiettyyn kohteeseen. Toisessa esimerkissä avataan TCP yhteys kolmivaiheisellä kättelyllä joka kohdistetaan verkon kaikille laitteille.
- Video esittelee myös masscan-työkalua, joka soveltuu paremmin isojen verkkojen skannaukseen nopeutensa ansiosta. Esimerkissä skannataan HTTP ja HTTPS portteja kohdeverkosta.
- Kolmantena työkaluna esitellään udpprotoscanner, joka on UDP porttien skannaukseen kehitetty työkalu.

#### Phrack (Rhysider 3.2.2026)
- Jaksossa haastatellaan legendaarisen hakkeri-julkaisu Phrackin työntekijöitä. Haastattelussa käsitellään julkaisun historiaa ja merkittävimpiä artikkeleita ja käännekohtia.

- Phrackilla on merkittävä asema hakkerikulttuurin historiassa. Se perustettiin vuonna 1985, ja monet tunnetut hakkerit ja tietoturva-alan ammattilaiset ovat kirjoittaneet siihen.

- Julkaisun historiassa on nähty myös kiistanalaisa tapauksia, esimerkiksi kun siinä julkaistiin Yhdysvaltojen kansallisen turvallisuuden kannalta kriittisen 911-hätäpuhelujärjestelmän teknisen dokumentin. Phrack on myös julkaissut exploitteja sekä muunmuassa ohjeita miten rakentaa kotitekoisia pommeja.

## a

Asensin Kalin virtuaalikoneen tiistain tunnilla, kun en enää muistanut Linux-kurssilla asentamani Debianin salasanaa. Asennus oli suoraviivainen eikä aiheuttanut ongelmia.

## b
26.3.2026 17:10

Kytkin Hacktheboxin starting_points VPN yhteyden päälle. Kokeilin pingata Googlen DNS-palvelinta ja yksikään paketti ei mennyt läpi. Myöskään selain ei avaa nettisivuja.

## Lähteet
Amin, R., Cloppert, M. & Hutchins, E. Tammikuu 2011. Intelligence-Driven Computer Network Defense
Informed by Analysis of Adversary Campaigns and
Intrusion Kill Chains. Luettavissa: https://lockheedmartin.com/content/dam/lockheed-martin/rms/documents/cyber/LM-White-Paper-Intel-Driven-Defense.pdf. Luettu: 24.3.2026.

Rhysider, J. 3.2.2026. Phrack. Darknet Diaries -podcast. Kuunneltavissa: https://open.spotify.com/episode/0G8Q2d8x1XeXu7Zh0mxWEK?si=78d80a9c9ea449a8. Kuunneltu: 25.3.2026.

Finlex. KKO:2003:36. Luettavissa: https://www.finlex.fi/fi/oikeuskaytanto/korkein-oikeus/ennakkopaatokset/2003/36. Luettu: 24.3.2026.

McCoy, C., Santos, O., Sternstein, J. & Taylor R. Huhtikuu 2019. 4.3 Surveying Essential Tools for Active Reconnaissance: Port Scanning and Web Service Review. O'Reilly. Video. Katsottavissa: https://learning.oreilly.com/videos/the-art-of/9780135767849/9780135767849-SPTT_04_03/. Katsottu: 25.3.2026.