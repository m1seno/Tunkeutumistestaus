# Fuzzy
Harjoitukset on tehty kotitoimistossa Kaarinassa. Koneena oli Lenovo V14 G4 AMN. Käyttöjärjestelmänä Windows 11 Pro version 25H2. Virtuaalikoneena oli Linux Kali 6.16.8+kali-amd64.

Harjoituksessa seurataan Teron kotisivujen Karvinen (Karvinen, T 22.3.2026) tehtävänantoa.

## Lue ja tiivistä
#### Find Hidden Web Directories - Fuzz URLs with ffuf (Karvinen, T 10.3.2023)
- Fuff on Joona "joohoi" Hoikkalan kehittämä työkalu, joka automatisoi hakemistohen fuzzakuksen.
- Artikkeli opettaa fuffin asennusta ja käyttöä. Tässä raportissa palaamme tähän tehtävään hieman myöhemmin.

#### ffuf README.md (Hoikkala, J 16.9.2023)
- Ffufin voi asentaa lataamalla binäärin githubista tai kloonaamalla repon. Nykyisin sen voi asentaa myös suoraan terminaalissa komennolla `sudo apt install ffuf`.
- Ffuf on tehokas työkalu. Sillä voi fuzzata, hakemistoja, piilotettuja virtuaalihosteja, GET-parametrien nimiä sekä POST-pyyntöjä.
- Kun ffuf ajetaan, osa url:sta korvataa sanalla FUZZ esim. `https://target/FUZZ`
- Yhtenä parametrina annetaan sanalista, jonka avulla voidaan fuzzata huomattavasti nopeammin kuin manuaalisesti.

## a) Ratkaise dirfuz-1 artikkelista Karvinen 2023: [Find Hidden Web Directories - Fuzz URLs with ffuf](https://terokarvinen.com/2023/fuzz-urls-find-hidden-directories/)

##### 26.4.2026 11:20
Aloitin asentamalla ffufin.
![](h5/install_ffuf.png)
Ilmeisesti ffuf oli jo asennettuna valmiiksi kalille, tai sitten olin asentanut sen aikaisemmin, en muista. Joka tapauksessa ffuf päivitettiin ja iso kasa paketteja asennettiin joita ei tarvitse. Poistin ne komennolla `sudo apt autoremove`.

Asensin myös SecLists -kirjaston, joka sisältää ison määrän sanalistoja, jotka ovat tarkoitettu pentestauksen avuksi. `sudo apt install -y seclists`.

Kävin lataamassa testikohteen Teron sivuilta ja annoin käyttäjälle ajo-oikeudet.
![](h5/chmod.png)

Käynnistin web-palvelun komennolla `./dirfuzt-0`, jonka jälkeen tarkistin selaimesta että se toimii.
![](h5/dirfuzt-0.png)

Avasin toisen terminaalin, irroitin koneen verkosta ja tarkistin ettei ping toimi.
![](h5/eth0_down.png)

Seuraamalla Hoikkalan ohjeita videolla (HelSec 16.5.2020), jolla hän demonstroi ffufin käyttöä, 


## Lähteet
Karvinen, T. 22.3.2026. Tunkeutumistestaus. Luettavissa: https://terokarvinen.com/tunkeutumistestaus/. Luettu: 24.4.2026.

Karvinen, T. 10.3.2023. Find Hidden Web Directories - Fuzz URLs with ffuf. Luettavissa: https://terokarvinen.com/2023/fuzz-urls-find-hidden-directories/. Luettu: 24.4.2026

Helsec. 0x03 Still Fuzzing Faster (U Fool) - joohoi - HelSec Virtual meetup #1 Katsottavissa: https://www.youtube.com/watch?v=mbmsT3AhwWU. Katsottu: 25.4.2026.

Hoikkala, J. 16.9.2023. ffuf - Fuzz Faster U Fool. Luettavissa: https://github.com/ffuf/ffuf/blob/master/README.md. Luettu: 24.4.2026.

