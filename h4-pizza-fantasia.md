# h4 Pizza Fantasia

## **x) Artikkelit**

**Karvinen 2023: Configuration Management of Distributed Systems over Unreliable and Hostile Networks**

*4.12.1 Size and Complexity of Some DSLs (112. Ominaisuuksien määrä.)*
- Artikkelista kuvataan DSL:ien (domain specific language) monimutkaisuutta ja kokoa
- Salt DSL:ssa on 510 ominaisuutta, hyödyntää Jinja 2 templateja. Puppet tarjoaa 113 ominaisuutta, joka perustuu sen omaan konseptiin, esim. virtuaaliresurssit.
  
*4.12.2 Use of DSL Functions in Case Configuration (112-115. Mitä oikeasti käytetään.)*
- Mozilla Release Engineering puppet manifests: Eniten käytetyt ominaisuudet ovat case, file, ja package 
- United States Government Configuration Baseline (USGCB): Eniten käytetyt ovat package-file-service, exec ja group.
  
*4.12.3.1 Dependencies Between Main Functions (115-117. Tärkeimmät rakennuspalikat.)*
- Käydään läpi tärkeimmät riippuvuudet toimintojen välillä. 
- Yleisimpiä toimintoja ovat pakettien, tiedostojen, käyttäjien ja ryhmien hallinta. Yleisiä käyttötapaukset ovat taas daemon ja app konfiguraatiot. Jossain tapauksissa myös käyttäjien ja ryhmien luonti. 

## a) Räpylä 

*Asenna itse valitsemasi demoni käsin. Jokin muu kuin tunnilla tai kotitehtävissä aiemmin asennettu, eli ei apache, ngninx eikä openssh-server. Kuten aina, testaa lopputulos.*

Valitsin demoniksi Maria db:n koska tietokannoilla on aina käyttöä. Käytin asennuksessa apuna RoseHostingin dokumenttia 'How to Install MariaDB on Debian 13'. Otin SSH-yhteyden virtuaalikoneeseen ja aloitin asennus hommat päivittämällä paketit. 

<img width="1203" height="549" alt="Kuva 1" src="https://github.com/user-attachments/assets/81c59aa0-7121-4bd8-95ca-e58c4bec6116" />

Kun paketit oli päivitetty, jatkettiin Maria db:n asennukseen kommennolla 'sudo apt install mariadb-server -y.  

<img width="1203" height="549" alt="Kuva 2" src="https://github.com/user-attachments/assets/35c4f79d-9d80-495d-84d9-8222091dee68" />

Kun asennus oli mennyt onnistuneesti läpi, käynnistin ja laitoin mariadb:n päälle komennolla 'sudo systemctl start mariadb && sudo systemctl enable mariadb'. Tämän jälkeen tarkistin vielä että mariab oli oikeasti päällä.

<img width="1238" height="524" alt="Kuva 3" src="https://github.com/user-attachments/assets/e565c033-7384-469d-87aa-06577d62f338" />


## b) Automaatti 

*Automatisoi valitsemasi demonin asennus Ansiblella.*

Tein ansiblea varten tarvittavat tiedostot ja hakemistot. Main.yml tiedostoon määritin että mariadb asennetaan ja asennuksen jälkeen varmistetaan että mariadb on päällä.

<img width="740" height="655" alt="Kuva 4" src="https://github.com/user-attachments/assets/d5a8b7b2-6ea2-493e-b460-48be38060920" />

Seuraavaksi kokeilin ajaa mariadb:n asennus playbookin. Playbook näytti menevän onnistuneesti läpi. 

<img width="1227" height="327" alt="Kuva 5" src="https://github.com/user-attachments/assets/79536c5a-4eed-4abb-b302-aa34586351c6" />


## c) Asetus 

*Muuta asetustiedostoa herralla (master, "control node") ja aja ansible uudestaan. Osoita, että asetukset tulivat käyttöön.*

Tein main.yml muutoksen että mariadbtä ei käynnistetä asennuksen jälkeen. Muutoksen jälkeen ajoin playbookin uudestaan läpi. 

<img width="1219" height="331" alt="Kuva 6" src="https://github.com/user-attachments/assets/76f06cb5-f686-40f1-b736-baa6fb24c04b" />

Muutos oli tullut voimaan ja maridb ei ollut käynnissä asennuksen jälkeen. 

<img width="1219" height="835" alt="Kuva 7" src="https://github.com/user-attachments/assets/264b67c7-e6ff-4f54-8db2-212a722e6a5d" />


## d) Paikka remonttiin

*Riko jotain asetuksia. Voit esimerkiksi poistaa demonin 'sudo apt-get purge foobar' (purge poistaa myös asetustiedostoja), poistaa asennuksen yhteydessä tulevan kansion (sudo rm -r /etc/foobar/ # vaarallista) tms. Ja sitten ajaa ansible-roolisi uudestaan ja todeta, että se korjaa tilanteen.*

Poistin demonin mariadb-server-core joka on yksi mariadb:n riippuvuuksista, jonka mariadb asentaa asennuksen yhteydessä. Poiston jälkeen mariadb ei lähtenyt enää päälle, joten olin onnistunut rikkomaan mariadb:n. 

<img width="1219" height="685" alt="Kuva 8" src="https://github.com/user-attachments/assets/ed5ba0f1-b2fa-4fb3-8679-0313e47e5be6" />

Seuraavaksi muutin ansiblen main.yml tiedostoa siten että mariadb käynnistetään asennuksen jälkeen. Muutoksen jälkeen ajoin playbookin ja tarkistin korjaako ansible mariadb:n asennuksen. Playbook teki muutoksia mariadb:n, ja vaikutti asentavan puuttuvan demonin takaisin koska mariadb oli päällä. Kokeilin ajaa vielä 'sudo systemctl start mariadb' komennon, joka sekin meni läpi virheittä. Lopuksi halusin tarkistaa vielä komennolla 'dpkg -l | grep mariadb-server-core' että puuttuva mariadb-server-core demoni oli asentunut takaisin 

<img width="1219" height="1047" alt="Kuva 9" src="https://github.com/user-attachments/assets/a5d88290-35d1-4823-8349-53d43458e905" />


## e) Idempotentti

*Osoita, että tilasi on idempotentti.*

Ajoin playbookin uudestaan, ja koska sama playbook oli ajettu läpi aiemmassa tehtävässä eikä muutoksia ollut tehty, playbook ei tehnyt muutoksia. 

<img width="1226" height="330" alt="Kuva 10" src="https://github.com/user-attachments/assets/b3616f41-ae2d-49bc-99cd-5dbfd8d8cb03" />


## Lähteet

- Palvelinten Hallinta https://terokarvinen.com/palvelinten-hallinta/
- Karvinen 2023: Configuration Management of Distributed Systems over Unreliable and Hostile Networks https://westminsterresearch.westminster.ac.uk/download/4cc417566aa9af60fe3826d690719e390abdb7a3c8672f3d51b1eb4ca75e7624/1427236/karvinen-2023-configuration-management-of-distributed-systems.pdf
- RoseHosting: How to Install MariaDB on Debian 13 https://www.rosehosting.com/blog/how-to-install-mariadb-on-debian-13/?srsltid=AfmBOooSp96Rz_eijg7c0lxnfGEMs5gF3yA2sCupQXg2WCxGZ7TtcNLs

