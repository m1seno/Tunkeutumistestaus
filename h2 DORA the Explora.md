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

## Porttiskannaus


## Lähteet
Buuri, M. 31.3.2026. DORA and TLPT testing. Payment Systems Department, Bank of Finland. Luettavissa: https://terokarvinen.com/buuri-2026-dora-and-threat-lead-penetration-testing/buuri-2026-dora-and-threat-lead-penetration-testing--teros-pentest-course.pdf. Luettu: 1.4.2026.

DORA. Article 26, Advanced testing of ICT tools, systems and proesses vased on TLPT. Luettavissa: https://www.digital-operational-resilience-act.com/Article_26.html. Luettu 2.4.2026.

DORA. Article 27, Requirements for testers for the carrying out of TLPT. Luettavissa: https://www.digital-operational-resilience-act.com/Article_27.html. Luettu: 2.4.2026.

Karvinen, T. 22.3.2026. Tunkeutumistestaus. Luettavissa: https://terokarvinen.com/tunkeutumistestaus/. Luettu: 24.3.2026.

Suomen Pankki. 12.3.2025. TIBER-FI Procedures and Guidelines. Luettavissa: https://www.suomenpankki.fi/globalassets/bof/en/money-and-payments/the-bank-of-finland-as-catalyst-payments-council/tiber-fi/tiber-fi-2.0-procedures-and-guidelines.pdf. Luettu: 2.4.2026