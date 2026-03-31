# h1 Hei Ansiblen maailma

## **x) Artikkelit**

**Karvinen 2026: SSH public key - Login without password**
- SSH kirjautuminen mahdollista automatisoida ssh-keygen avulla
- Automatisointi vaatii toimiakseen toimivan SSH-yhteyden
- Helpottaa varsinkin ssh:ta käyttävien komentojen käyttöä kun ei tarvitse erikseen kirjautua joka kerta

**Karvinen 2026: Hello Ansible**
- Ansible työkalulla voi hallinnoida useita tietokoneita, infrastruktuuri koodina periatteella
- Toimii SSH:n avulla, jota kautta hallinnointi tapahtuu
- Helpottaa ja nopeuttaa useiden tietokoneiden konfigurointia, kun komento / komentoja voidaan ajaa samanaikaisesti useammalle tietokoneelle 

## **a) Sshecrets. Asenna SSH-demoni ja testaa se kirjautumalla SSH:lla.**

Aloitin SSH:n asentamisen aiemmin luotuun uuteen Debianin (13.4.) virtuaalikoneesen. Ajoin ensiksi komennon sudo apt-get update jonka jälkeen itse asennus komennon sudo apt-get -y install ssh. Tämän jälkeen käynnistin SSH:n komennolla sudo systemctl enable --now ssh. Tämän jälkeen kokeilin vielä että SSH toimii lokaalisti.

<img width="895" height="305" alt="Kuva 1" src="https://github.com/user-attachments/assets/e08f5cd1-958a-406f-b815-6398ca0a92dc" />


## **b) Pubkey. Automatisoi ssh-kirjautuminen julkisella avaimella.**

Seuraavaksi automatisoin SSH kirjautumisen, luomalla ensiksi keypairin kommennolla ssh-keygen. Sen jälkeen kopioisin avaimen localhostiiin komennolla ssh-copy-id localhost. Lopuksi vielä kokeilin SSH kirjautumista kommennolla ssh localhost ja kirjautuminen toimi onnistuneesti ilman salasanaa.

<img width="1052" height="467" alt="Kuva 2" src="https://github.com/user-attachments/assets/1fbf3c4e-5b9a-4a01-8caf-f4eba71754c6" />

## **c) Hei Ansible. Tee hei maailma ansiblella ja kokeile sitä SSH:n yli.**

Ansiblen käyttöönotto alkoi asennuksella ja Ansiblen lisäksi asensin samalla myös micro editorin, bash-completion ja treen komennolla sudo apt-get install ansible micro bash-completion tree. Seuraavaksi lähdin luomaan kansioita ja tiedostoja ansiblea varten sivun https://terokarvinen.com/hello-ansible/ ohjeistuksen mukaisesti.

Loin ensiksi ansible kansion ja sinne hosts.ini tiedoston johon lisäsin localhost. Tämän jälkeen testasin ansiblea ssh:n kautta. Tuloksena koneen uptime, joten ansible toimi oikein. 

<img width="1276" height="403" alt="Kuva 3" src="https://github.com/user-attachments/assets/e517a65a-f968-41ea-9a96-5c4ab94c8bce" />

Ansible herjasi pythonin versiosta, joten lisäsin hosts.ini tiedostoon Teron sivujen mukaisin tekstin. Muutoksen jälkeen ei tullut enää herjaa Pythonista.

<img width="721" height="275" alt="Kuva 4" src="https://github.com/user-attachments/assets/ac3eb9e6-9323-4570-9d32-98ffa4d15488" />

Seuraavaksi jatkoin luomalla ansible.cfg tiedoston jonne lisäsin määrityksen inventorylle. Tämän jälkeen sain ajettua ansible komennon ilman "-i hosts.ini" parametria.

<img width="572" height="166" alt="Kuva 5" src="https://github.com/user-attachments/assets/ac6500f9-dbba-4c39-9467-66e739ee8589" />

Site.yml tiedosto puuttui, joten loin site.yml tiedoston seuraavaksi ja lisäsin sinne hello -roolin. Komento ansible-playbook site.yml antoi samanlaisen virheilmoituksen kuin Teron sivulla oleva esimerkki. 

<img width="1260" height="278" alt="Kuva 6" src="https://github.com/user-attachments/assets/7201e26f-165d-4618-ab23-979eb491a347" />

Virheilmoitus johtui puuttuvasta hello-roolista, joten main.yml tiedosto on lisättävä kansioon roles/hello/tasks. Tiedoston lisäyksen jälkeen ansible-playbook site.yml komento meni läpi virheittä.

<img width="1249" height="563" alt="Kuva 7" src="https://github.com/user-attachments/assets/ea1e27e5-3481-4f53-992b-ad9222776984" />

Kokeilin vielä kirjautua ulos SSH:sta ja ajaa ansiblen ssh:n yli komennolla 'ssh localhost 'cat /tmp/hello-ansible'. Komento meni onnistuneesti läpi ja "hello ansible" testi suoritettu onnistuneesti!

<img width="647" height="196" alt="Kuva 8" src="https://github.com/user-attachments/assets/b88b78e5-02e8-4451-bde9-6d4624b3c5ac" />



## Lähteet

- Palvelinten Hallinta https://terokarvinen.com/palvelinten-hallinta/
- SSH public key - Login without password https://terokarvinen.com/ssh-public-key-login-without-password/
- Hello Ansible https://terokarvinen.com/hello-ansible/ 


