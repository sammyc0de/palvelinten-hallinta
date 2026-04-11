# h3 Demoni

## **x) Artikkelit**

**Karvinen 2026: Apache installed with Ansible - quick notes**
- Apachen asennus mahdollista automatisoida ansiblen avulla
- Vaatii pohjalle toimivan ansiblen
- Kopioi tarvittavat tiedostot myös, jotka Apache vaatii toimiakseen

**Ansible Community Documentation: Handlers: running operations on change**
- Handlers: Ansiblen tehtäviä ja ajetaan kun ne saavat ilmoituksen. Esim. tehtävän ajaminen kun konfiguraatio muuttunut.
- Notifying handlers: Tehtävän ajon yhdeydessä handler kutsutaan notify-sanalla. Esim. mikäli notify:n alle on määritetty "restart apache", kutsutaan tällä "restart apache" nimistä "tehtävää" joka handlers alla.

**'ansible-doc service'**
- Käytetään palveluiden hallintaan ansiblella. 

Optiot:
- enabled: käynnistetäänkö palvelu käynnistyksen yhteydessä
- name: palvelun nimi
- state: palvelun tilan määrittäminen
```
Esimerkki (httpd palvelun käynnistys, mikäli ei ole jo käynnistetty):
- name: Start service httpd, if not started
  ansible.builtin.service:
    name: httpd
    state: started
```


## a) Apassi 

*Asenna Apache 2 käsin. Weppisivun tulee näkyä palvelimen etusivulla. Sivun tulee olla tavallisen käyttäjän muokattavissa, ilman root- tai sudo-oikeuksia.*

Otin ensiksi SSH-yhteyden debianin virtuaalikoneeseen. Apachen asentamiseksi käytin apuna DigitalOceanin ohjetta 'Install Apache Web Server on Debian: Full Guide'. Aloitin asennushommat ajamalla ensiksi 'sudo apt-get update' päivittämällä pakettien listauksen. Tämän jälkeen asensin apachen komennolla 'sudo apt-get install apache2 -y'. 

<img width="1030" height="404" alt="Kuva 1" src="https://github.com/user-attachments/assets/8894014b-10f1-414a-8b45-1a03ddfc03df" />

Debianin virtuaalikone saa osoitteen DHCP:ltä joten tarkistin seuraavaksi koneeni ip-osoitteeni (yleensä servereillä on kiinteä ip-osoite, mutta tälle harjoituskoneelle en ole vielä määrittänyt sitä). Virtuaalikoneessani ei ole graafista käyttöliittymää joten kokeilin toisen koneen selaimella että saan yhteyden apachen web-palvelimeen. Sivu näyttii vastaavan suoraan localhostista ja täten toimivan. 

<img width="1030" height="299" alt="Kuva 2" src="https://github.com/user-attachments/assets/d5f04995-7150-4fb6-a57e-8fc26c890294" />

<img width="1468" height="710" alt="Kuva 3" src="https://github.com/user-attachments/assets/95dc959f-25c3-409d-8814-c131b79cd17f" />

Sivun pitäisi olla tavallisen käyttäjän muokattavissa, joten tein hakemiston newdefault hakemiston /var/www/ - alle. Seuraavaksi määritin käyttäjän hakemiston omistajaksi ja kommennolla 'sudo chmod -R 755 /var/www/newdefault' määritin että omistaja saa muokata sivuja ilman sudoa, ja muut vain lukea ja suorittaa.

Tämän jälkeen kokeilin tehdä uuden index.html sivun ilman sudo-oikeukia komennolla 'micro /var/www/newdefault/index.html'. Komento meni läpi ja pääsin tekemään uutta etusivua ilman sudoa.

<img width="1041" height="134" alt="Kuva 4" src="https://github.com/user-attachments/assets/892ab212-7b83-434f-83bc-8e949bbff07d" />

<img width="1041" height="782" alt="Kuva 5" src="https://github.com/user-attachments/assets/9a437b3a-71b7-4344-bdff-31e07594b8de" />

Seuraavaksi piti luoda vielä uusi virtual host tiedosto, joka oli minulle jo ennustaan tuttua. Tein uuden virtual host tiedoston 'newdefault.conf', jonne määritin mm. että uusi sivu ohjautuu localhostiin. 

Uusi virtual host piti ottaa käyttöön komennolla 'sudo a2ensite newdefault.conf' ja vanha vastaavasti pois päältä komennolla 'sudo a2dissite 000-default.conf'. Seuraavaksi apachen uudelleen käynnistys ja komennolla 'sudo apache2ctl configtest' testaus vielä että uusi konfig toimii virheittä. Tuloksena syntax ok, joten kaikki pitäisi olla kunnossa. Ennen testausta selaimella halusin katsoa vielä miltä näyttää curl localhost, mutta se vaati curl-asennuksen. Sen jälkeen meni curl localhost läpi ja näytti uuden etusivun. Tein lopuksi vielä testin selaimella että uusi etusivu näkyy. 

<img width="1293" height="843" alt="Kuva 6" src="https://github.com/user-attachments/assets/35807dc1-c9f5-4b46-af86-69543990e043" />

<img width="1041" height="781" alt="Kuva 7" src="https://github.com/user-attachments/assets/d33c3f22-e597-4932-88e3-809c70b6b7c0" />

<img width="1266" height="853" alt="Kuva 8" src="https://github.com/user-attachments/assets/4333998c-7396-4b47-9290-2e984dd03980" />

## b) Moottorix 

*Asenna Nginx käsin. Weppisivun tulee näkyä palvelimen etusivulla. Sivun tulee olla tavallisen käyttäjän muokattavissa, ilman root- tai sudo-oikeuksia. (Muista sammuttaa Apache ensin.)*

Tässä tehtävässä oli vuorossa asentaa nginx web-palvelin. Käytin tässäkin apuna DigitalOceanin ohjetta, 'How to Install and Configure Nginx on Ubuntu | Step-by-Step Guide'. Koska samaan aikaan ei voi ajaa serverillä apachea ja nginx:ia, otin ensiksi apachen pois päältä komennolla 'sudo systemctl stop apache2' ja sen jälkeen ajoin vielä komennon 'sudo systemctl disable apache2', joka estää apachen käynnistymisen automaattisesti serverin käynnistymisen yhteydessä.

<img width="996" height="220" alt="Kuva 9" src="https://github.com/user-attachments/assets/ccf6f98c-118d-4adf-842b-30417cfaa1cc" />

Kun apache oli sammutettu, jatkoin nginxin asennuksella, 'sudo apt-get install nginx -y'. 

<img width="1103" height="365" alt="Kuva 10" src="https://github.com/user-attachments/assets/b8a5cfa5-2198-4f9f-bef0-dd07f336484e" />

Tämän jälkeen testasin nginxin toimivuutta lähettämällä http head pyynnön localhostiin, 'curl -I http://localhost'. Vastauksena tuli takaisin '200 OK' eli palvelin toimii.

<img width="600" height="224" alt="Kuva 11" src="https://github.com/user-attachments/assets/9ef3e672-5cc4-4d48-9d9e-5c6d0d5c65e1" />

Käytin tässä tehtävässä hyväkseni aiemmassa tehtävässä tekemääni sivustoa (newdefault), joten määritin nginxin server blockkiin. Server blockit vastaavat apachen virtual hosteja. Serverin blockin loin komennolla 'sudo micro /etc/nginx/sites-available/newdefault' ja käytin apuna DigitalOceanin ohjetta. Server blockiin määritin server_nameksi localhost jolloin newdefault site pitäisi näkyä suoraan serverin etusivulla. 

<img width="1088" height="536" alt="Kuva 12" src="https://github.com/user-attachments/assets/971c63bd-0550-4abd-a885-51dbdfb16833" />

Seuraavaksi laitoin sivun päälle komennolla 'sudo ln -s /etc/nginx/sites-available/newdefault /etc/nginx/sites-enabled/' jonka jälkeen nginxin uudellen käynnistys 'sudo systemctl reload nginx'.  Seuraavaksi kokeilin curl-komennolla että sivu näkyy oikein localhostista. Lopuksi otin vielä default server blockin pois käytöstä komennolla 'sudo unlink /etc/nginx/sites-enabled/default' ja tein pienin muutoksen webbisivulle lisäämäällä sinne nginx-tekstin. Sivu päivittyi oikein localhostiin. 

<img width="1039" height="543" alt="Kuva 13" src="https://github.com/user-attachments/assets/3e23c030-cc50-4f38-bab8-2272b3730dd0" />

## c) Automoottorix 

*Automatisoi Nginx asennus Ansiblella. Ylläpitäjän osuus Ansiblella riittää, itse HTML-weppisivut voi tehdä käsin.*

Käytin tehtävssä apuna Digital Oceanin artikkelia 'How To Deploy a Static HTML Website with Ansible on Ubuntu 20.04 (Nginx)'. Tein tarvittavat tiedostot ja hakemistot ansiblea varten. Roles-hakemiston alle tein erilliset files, handlers ja tasks hakemistot. Files alle sijoitin nginxin konfiguraatio templaten. Handlers ja tasks hakemistoihin main.yml tiedostot, joissa ansiblen konfigia. 

Kun kokeilen ajaa ensimmäisen kerran ansiblen playbookia, se ei mennyt läpi, sillä 'Enable site' -kohdassa en ollut määrittänyt destination kohtaa oikein. Olin määrittänyt pelkän hakemiston, en koko polkua. Korjasin tämän lisäämällä dest-kohtaan koko polun jolloin ansiblen playbook meni läpi. Lopuksi vielä tarkistin curlilla että websivu elossa localhostissa.

<img width="944" height="854" alt="Kuva 14" src="https://github.com/user-attachments/assets/cd9e5d0a-42bc-4fa9-93b4-8dfa07079b9e" />

<img width="944" height="352" alt="Kuva 15" src="https://github.com/user-attachments/assets/f01dffe5-b0cb-4c5e-9480-20bd2cfbf5b6" />

<img width="1639" height="938" alt="Kuva 16" src="https://github.com/user-attachments/assets/d355cdf0-cd8a-4e0d-8415-cc797b54489d" />

## d) Vapaaehtoinen bonus 

*Osiris-T. Osiris-T asentaa Wazuhin ja tarvittavat virtuaalikoneet. Voit lähettää vihamielistä verkkoliikennettä (tcpreplay), siepata sen (suricata) ja saat tulokset suoraan dashboardille (wazuh). Enemmän alpha kuin se kreikkalainen kirjain. Mutta Oskari, Nico ja Arttu ilahtuvat, jos kokeilet. Häkämies, Saario, Mukkula 2026: Osiris-T. Häkämies etal 2026: How to Install.*

Vapaaehtoista bonusta en ehtinyt tekemään.


## Lähteet

- Palvelinten Hallinta https://terokarvinen.com/palvelinten-hallinta/
- Karvinen 2026: Apache installed with Ansible - quick notes https://terokarvinen.com/apache-ansible/
- Karvinen 2012: Short HTML5 page https://terokarvinen.com/2012/short-html5-page/
- Digital Ocean: Install Apache Web Server on Debian: Full Guide https://www.digitalocean.com/community/tutorials/how-to-install-the-apache-web-server-on-debian-11
- Digital Ocean: How to Install and Configure Nginx on Ubuntu | Step-by-Step Guide https://www.digitalocean.com/community/tutorials/how-to-install-nginx-on-ubuntu-20-04
- Digital Ocean: How To Deploy a Static HTML Website with Ansible on Ubuntu 20.04 (Nginx) https://www.digitalocean.com/community/tutorials/how-to-deploy-a-static-html-website-with-ansible-on-ubuntu-20-04-nginx

