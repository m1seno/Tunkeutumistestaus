# Toukokuu2026!
Harjoitukset on tehty kotitoimistossa Kaarinassa. Koneena oli Lenovo V14 G4 AMN. Käyttöjärjestelmänä Windows 11 Pro version 25H2. Virtuaalikoneena oli Linux Kali 6.16.8+kali-amd64.

Harjoituksessa seurataan Teron kotisivujen (Karvinen, T 22.3.2026) tehtävänantoa.

## [Cracking Passwords with Hashcat](https://terokarvinen.com/2022/cracking-passwords-with-hashcat/) (Karvinen, T 6.4.2022)

- Tyypillisesti järjestelmissä ei säilytetä selkokielisiä salasanoja, vaan salasanojen hash-arvoja. Hashing on yksisuuntainen funktio, eli hashia ei voi kääntää takaisin selkokieliseksi tekstiksi. Sen sijaan hasheja voi vertailla keskenään, esim hashaamalla kaikki mahdolliset sanat ja vertailemalla niitä salasanan hashiin.
- hashcatin voi asentaa komennolla `sudo apt-get -y install hashcat`. Lisäksi kannattaa myös asentaa hashid, joka tunnistaa satoja eri hash-tyyppejä (google ai-yhteenveto), sekä jonkin sortin sanalista.
- hashcatin ajo ottaa parametreina hash-tyypin id:n, hashin joka halutaan crackata sekä sanalistan johon sitä verrataan. Lopuksi määritellään tiedoston nimi mihin crackatty salasana tallennetaan.

## [Crack File Password With John](https://terokarvinen.com/2023/crack-file-password-with-john/) (Karvinen, T 9.2.2023)

- Artikkeli opettaa Jumbo Johnin asennusta ja käyttöä, sekä sen kääntämistä lähdekoodista toimivaksi binääriksi.
- Ohjelma asennetaan kloonaamalla github repo. Tämän jälkeen ohjelmasta tehdään make-tiedosto komennolla `./configure`. Lopuksi ohjelma käännetään komennolla `make -s clean && make -sj4`.
- Ajamalla Johnin run-hakemistossa sen pitäisi palauttaa versionumero jos kaikki on kondiksessa.
- Ajamalla tiedoston joka sisältää hashin `john/run/john <tiedosto>` Jumbo John tunnistaa automaattisesti mikä hash muoto on kyseessä ja vertaa sitä sisäänrakennettuun sanalistaan. 


## Lähteet

Karvinen, T. 22.3.2026. Tunkeutumistestaus. Luettavissa: https://terokarvinen.com/tunkeutumistestaus/. Luettu: 7.5.2026.

Karvinen, T. 9.2.2023. Crack File Password With John. Luettavissa: https://terokarvinen.com/2023/crack-file-password-with-john/ Luettu: 7.5.2026.

Karvinen, T. 6.4.2022. Cracking Passwords with Hashcat. Luettavissa: https://terokarvinen.com/2022/cracking-passwords-with-hashcat/. Luettu: 7.5.2026.
