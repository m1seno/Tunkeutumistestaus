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

Sekä hosts että services ovat hyödyllisiä työkaluja. Metasploitissa toimii myös clear -komento, eli jos haluaa "siivota" terminaalia, niin hostit ja portit saa aina uudestaan näkyviin tietokannasta. 

## d) Internet famous
EternalBlue on NSA:n kehittämä hyökkäystyökalu, joka hyödynsi Microsoftin SMB-protokollan nollapäivähaavoittuvuutta. Vuonna 2017 Eternal Blueta hyödynnettiin sekä WannaCry että NotPetya -kyberhyökkäyksissä, joista arvioiden mukaan koitui yhteensä noin 14 miljardin dollarin vahingot. (Wikipedia)

##### 9.4.2026 17:58
Exploitti löytyy metasploitista hakusanalla "eternalblue".

![](h3/searchEternalBlue.png)

Koska en tiedä tarkalleen mikä windows-versio metasploitablessa on, valitsin "Automatic target", eli `use 1`.

Asetin rhostin ja lhostin ip-osoitteet ja tarkistin että konfiguraatiot on oikein.

![](h3/setEternalBlue.png)

##### 18:34
Ajoin hyökkäyksen pariin otteeseen, mutta molemmilla kerroilla tuli ilmoitus "Host does not appear vulnerable".

![](h3/runEternalBlue.png)

Vasta tässä kohtaa tajusin olleeni tyhmä. EternalBlue on windows haavoittuvuus, eli se ei tietenkään toimi metasploitableen.

## e) Vertaile nmap:n omaa tiedostoon tallennusta (-oA foo) ja db_nmap:n tallennusta tietokantoihin

#### Metasploit database
Metasploitin tietokannan sisältöä voi käsitellä monilla eri komennoilla (metasploit help). Näistä hyödyllisimmät ovat varmaankin juuri services ja hosts joita käsiteltiin aiemmassa harjoituksessa. Analyze kuulostaa paperilla hyvältä, mutta se ei löydä ainakaan metasploitablesta suoraan haavoittuvuuksia, vaikka ajoin nmapin uudestaan ``-A`` flagilla.

Tiedot esitetään järjestellyssä ja siistityssä muodossa. Eli esim verbose -tiedot eivät näy edellä mainituilla komennoilla.

![](h3/metasploit_db.png)

Tietokannan sisällön voi myös ``db_export -f xml metasploit_db`` exportata tiedostoon, jolloin siitä pysty hakemaan grepin avulla, mutta oman testaukseni perusteella en näe tässä hirveästi lisäarvoa.

- `-f` Määrittele formaatti (vaihtoehtoina xml ja pwdump)
- `xml` Valittu formaatti
- `metasploit_db` Tiedoston nimi

![](h3/dbFile.png)

#### Nmap save to file

Tein uuden porttiskannauksen samoilla parametreilla,
mutta nyt lisäsin -oA flagin, joka tallentaa
skannauksen tulokset kolmeen eri formaattiin.

![](h3/nmap.png)
![](h3/files.png)

Näistä gnmap ja xml ovat melko ikävää luettavaa, joten unohdetaan
ne tässä kohtaa. Nmap formaatti sen sijaan on suora kopio itse skannauksesta.
Mainittavampana erona metasploitin tietokantaan on se, että myös verbose tiedot tallentuvat.
Näin ollen käyttäjä voi nähdä mitä "taustalla" on tapahtunut skannauksen aikana.

## Murtaudu Metasploitablen vsftpd-palveluun

Tein tämän jo viime viikolla metasploitin avulla.
Yritän siis nyt tehdä saman käyttäen alkuperäistä metodia,
josta näin sattumalta videoan viime viikolla kun yritin opetella
metasploitin käyttöä. (Bombal 14.3.2025)

Tarvittavat työkalut ovat nmap ja ncat.
Ncat on Nmap-projektiin kuuluva komentorivityökalu,
jolla voidaan lähettää ja vastaanottaa dataa verkon yli
TCP- ja UDP-protokollilla.

Aloitetaan tarkastelemalla tiedostoon tallennettuja porttiskannauksen tuloksia.

![](h3/nmap_vsftpd.png)

Vsftp -palvelin on portin 21 takana. Se tulee siis olemaan hyökkäyksen kohteena.

Ensimmäinen tehtävä on otta yhteys porttiin 21 netcatilla.
220-koodi kertoo että yhteys on avattu.

Palvelimelle täytyy antaa käyttäjänimi komennolla USER sekä salasana komennolla PASS.
Käyttäjänimeksi kelpaa mitä tahansa, kunhan se loppuu hymiöön :). Hymiö on se, mikä laukaiseen varsinaisen haavoittuvuuden.
Salasanaksi kelpaa mitä tahansa.

Tämän jälkeen palvelin ei kerro että mitään olisi tapahtunut,
mutta nyt portin 6200 taaksi olisi pitänyt avautua root shell.
Ctrl-C:llä pääsee pois netcatin yhteydestä.

![](h3/attack_vsftpd.png)

Voimme tarkistaa onko portti todella auki skannaamalla sen nmapilla.

![](h3/port_6200.png)

Skannaus osoittaa että portti on auki. 
Tässä on tärkeää käyttää `-sS` -flagia sillä se suorittaa vain osittaisen handshaken (SYN) (nmap -h).
Ilmeisesti skannaamalla portin ilman tätä voi aiheuttaa portin sulkeutumisen (Bombal 14.3.2025).

Sitten otetaan vain yhteys kyseiseen porttiin, mikä antaa root-oikeudet kohdekoneelle.

![](h3/access_6200.png)



## Lähteet
Bombal, D. 14.3.2025. Metasploit Hacking Demo (includes password cracking). Katsottavissa: https://www.youtube.com/watch?v=bBut8D7usKA&t=95s. Katsottu: 4.4.2026.

hosts -h. Metasploitin help-sivu hosts -komennolle.

metasploit help. Metasploitin help sivu.

nmap.org. Ncat. Luettavissa: https://nmap.org/ncat/. Luettu 10.4.2026.

nmap -h. Nmapin help -sivu linuxin terminalissa.

services -h. Metasploitin help-sivu services -komennolle.

Wikipedia. EternalBlue. Luettavissa: https://en.wikipedia.org/wiki/EternalBlue. Luettu: 9.4.2026