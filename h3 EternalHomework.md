# EternalHomework
Harjoitukset on tehty kotitoimistossa Kaarinassa. Koneena oli Lenovo V14 G4 AMN. Käyttöjärjestelmänä Windows 11 Pro version 25H2. Virtuaalikoneena oli Linux Kali 6.16.8+kali-amd64.

## Lue ja tiivistä

## b) Tallenna porttiskannauksen tuloksia Metasploitin tietokantoihin

##### 8.4.2026 21:48
Aloitin harjoituksen tarkistamalla, että koneeni on host-only verkossa ja saa yhteyden ainoastaan metasploitableen.

![](h3/verkko.png)

##### 21:55
Avasin metasploitablen ja tarkistin että tietokanta on yhdistetty.

![](h3/tietokantayhteys.png)

##### 22:00
Tein skannauksen komennolla `db_nmap -n -sV -T4 -p- 192.168.56.102`

- `-n` Nmap ei yritä tehdä DNS -resoluutiota (nmap -h). Eli ei muunna ip-osoitetta hostnimeksi.
Tämä nopeuttaa skannausta jonkin verran.
- `-sV` Service/Version detection. Tässä olisi voinut ajaa myös parametrin -A,
sillä se sisältää myös -sV:n, mutta se sisältää myös käyttöjärjestelmän tunnistamisen,
traceroutin ja script scannauksen. (nmap -h)
- `-T4` Skannauksen nopeus.
- `-p-` Porit 1-65535.

![](h3/porttiskannaus.png)

Tuloksena tuli tutun näköinen lista portteja, joskin tämä on hieman suppeampi,
koska -A parametria ei annettu.

## c) Tarkastele Metasploitin tietokantoihin tallennettuja tietoja komennoilla "hosts" ja "services"

#### Hosts
##### 22:40
Komennolla `hosts` saa listan kaikista skannauksessa löydetyistä laitteista.

![](h3/hosts.png)

Tässä esimerkissä listassa on vain yksi kone, koska skannaus kohdistettiin yksinomaan Metasploitableen.

Oletuksena komento listaa kuvassa näkyvät tiedot. Saatavilla olevia kolumneja on
kuitenkin huomattavasti enemmän, jos haluaa nähdä itselleen hyödyllisempää tietoa.
Kolumneja voi vapaasti valita `-c` parametrin avulla. `-U` parametrin avulla tietoja
voi päivittää manuaalisesti. (hosts -h)


![](h3/hosts2.png)

Luonnollisesti tulosten kattavuus ja tarkkuus riippuu skannauksen laajuudesta ja agressiivisuudesta.

#### Services
Komennolla `services` saa listan avoimista porteista sekä niiden takana toimivista palveluista.

![](h3/services.png)

Parametrilla `-S` voi hakea tuloksia hakusanan perusteella. (services -h)

![](h3/services2.png)

## Lähteet

hosts -h. Metasploitin help-sivu hosts -komennolle.

nmap -h. Nmapin help -sivu linuxin terminalissa.

services -h. Metasploitin help-sivu services -komennolle.