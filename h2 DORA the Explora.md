# DORA the Explora
Harjoitukset on tehty kotitoimistossani Kaarinassa. Koneena oli Lenovo V14 G4 AMN. Käyttöjärjestelmänä Windows 11 Pro version 25H2. Virtuaalikoneena oli Linux Kali 6.16.8+kali-amd64.

Harjoituksessa on seurattu Teron kurssisivun (Karvinen 22.3.2026) tehtävänantoa ja vinkkejä.

## Lue ja Tiivistä
#### DORA and TLPT testing (Buuri 2026)
- Digital Operations Resilience Act (DORA), on Euroopan Unionin määrittelemä standardi joka velvoittaa finanssialan sektorin yritykset huolehtimaan digitaalisten järjestelmiensä turvallisuudesta.
- TIBER-EU tarjoaa yhteisen viitekehyksen Red teamaukselle(Realistinen hyökkäyssimulaatio joka testaa organisaation puollustuskykyä). Sitä on päivitetty vastaamaan DORA:n esittämiin vaatimuksiin.

#### Article 26 (DORA)
- Finanssialan organisaatioiden on tehtävä TLTP-testaus vähintään 3 vuoden välein. Testien laajuus ja säännöllisyys määräytyy kyseisen uhkatason perusteella.
- Testauksen tulee sisältää kaikki kriittiset toiminnot ja ICT-järjestelmät, mukaan lukien kaikki ulkoistetut palvelut.

#### Article 27 (DORA)
- Organisaatioiden tulee käyttää TLPT-tastauksessa ainoastaan sertifioituja alan huippuosaajia, joilla on riittävä kokemus ja osaaminen vastaamaan työn vaatimuksiin.

#### TIBER-FI (Suomen Pankki)
- Red team -testaus sisältää kaksi vaihetta: suunnitelma sekä aktiivinen hyökkäyssimulaatio. Hyökkäyssimulaatiossa pyritään saavuttamaan ennalta määritellyt tavoitteet.

- Testin tehokkuutta voidaan parantaa testaajille annettavalla lisätiedolla (leg-up), koska testejä rajoittavat aika, sekä eettiset ja lailliset rajat.

## Asenna Metasploitable 2

Latasin Metasploitable 2 -virtuaalikoneen osoitteesta https://sourceforge.net/projects/metasploitable/. Asennus oli suoraviivainen eikä aiheuttanut ongelmia. Valitsin default muistin (512MB) ja CPU:den (1 kpl) määrän. Verkkokortiksi laitoin Host-only Adapter.
![](h2/metasploitable.png)

## Virtuaaliverkko

VirtualBoxissa oli valmiiksi määritelty Host-only verkko, joten sitä ei tarvinnut luoda erikseen.
![](h2/hostOnly.png)

Kali -koneelleni annoin kaksi virtuaalista verkkokorttia. Toisena on NAT, joka reitittää pois lähiverkosta, ja toisena Host-only, joka toimii labraverkkona virtuaalikoneiden välillä.
![](h2/kaliNIC.png)

## Verkon testaaminen
Käynnistin Kalin ja tarkistin ip-osoitteen komennolla `ip a`. Eth0 -liitäntä on NAT:n käytössä ja eth1 on host-only. Suljin eth0 -liitännän komennolla `sudo ip link set eth0 down`. Nyt näemme että liitäntä on down -tilassa. Kokeilin pingata 8.8.8.8 ja syötteessä ilmoitettiin välittömästi, että verkko ei ole tavoitettavissa.

Seuraavaksi käynnistin Metasploitablen ja tarkistin ip osoitteen. Ip -osoite oli samassa verkkoavaruudessa kuin Kali. Metasploitablelta ei myöskään saanut yhteyttä internettiin.
![](h2/metaIp.png)

Lopuksi pingasin Kalilta Metasploitableen ja tämä onnistui, eli kaiken pitäisi olla kunnossa porttiskannausta varten.
![](h2/kaliIp.png)

## Etsi Metasploitable nmapilla.
Skannasin koko lähiverkon komennolla `namp -sn 192.168.56.0/24`. -sn flagi jättää portit skannaamatta ja etsii vain hosteja(nmap -h).
![](h2/nmap%20ip%20skannaus.png)

Nmap löysi 4 hostia verkosta. Tiedämme, että .1 on default gateway, .100 on dns-palvelin, .101 on kali, joten 102 on oltava metasploitable. Metasploitablen web-sivu vahvisti asian.
![](h2/meta%20selaimessa.png)

## Porttiskannaus
Tein porttiskannauksen `nmap -A -T4 -p- 192.168.56.102`.

Kiinnostavat portit:
#### 21/tcp
Tämän portin takana on Very Secure File Transfer Protocol Daemon (vsftpd). Mikä tekee siitä kiinnostavan on versionumero 2.3.4, joka sisältää tunnetun haavoittuvuuden; Jos hyökkääjä ottaa FTP-yhteyden porttiin ja kirjoittaa hymiön käyttäjänimen perään, esim. käyttäjä:), tämä avaa root shellin porttiin 6200, josta saa suoran pääsyn järjestelmään. (Google 1)

#### 23/tcp
Kyseinen portti on aina kiinnostava, koska sitä käyttää Telnet, joka ei tunnetusti salaa liikennettä.

#### 80/tcp
Kyseistä porttia käyttää HTTP, joka ei salaa liikennettä. Lisäksi web-palvelut tarjoavat monenlaisia tapoja hyökätä järjestelmää vastaan (XSS, SQL injektio, File upload).

Palveluna tässä toimii Apache 2.2.8 jonka tuki lopetettiin lähes vuosikymmen sitten. Se sisältää lukuisia haavoittuvuuksia. (apache.org)

## Korkataan

## Lähteet
Apache. Apache HTTP Server 2.2 vulnerabilities. Luettavissa: https://httpd.apache.org/security/vulnerabilities_22.html. Luettu: 3.4.2026.

Buuri, M. 31.3.2026. DORA and TLPT testing. Payment Systems Department, Bank of Finland. Luettavissa: https://terokarvinen.com/buuri-2026-dora-and-threat-lead-penetration-testing/buuri-2026-dora-and-threat-lead-penetration-testing--teros-pentest-course.pdf. Luettu: 1.4.2026.

DORA. Article 26, Advanced testing of ICT tools, systems and proesses vased on TLPT. Luettavissa: https://www.digital-operational-resilience-act.com/Article_26.html. Luettu 2.4.2026.

DORA. Article 27, Requirements for testers for the carrying out of TLPT. Luettavissa: https://www.digital-operational-resilience-act.com/Article_27.html. Luettu: 2.4.2026.

Google 1. AI-yhteenveto. "vsftpd 2.3.4 vulnerability"

Karvinen, T. 22.3.2026. Tunkeutumistestaus. Luettavissa: https://terokarvinen.com/tunkeutumistestaus/. Luettu: 24.3.2026.

Nmap help page (nmap -h).

Suomen Pankki. 12.3.2025. TIBER-FI Procedures and Guidelines. Luettavissa: https://www.suomenpankki.fi/globalassets/bof/en/money-and-payments/the-bank-of-finland-as-catalyst-payments-council/tiber-fi/tiber-fi-2.0-procedures-and-guidelines.pdf. Luettu: 2.4.2026