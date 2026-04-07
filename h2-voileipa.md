# h2 Voileipä

## **x) Artikkelit**

**Karvinen 2026: Sudo without password**
- Sudo mahdollista ilman salasanaa, kun lisätään käyttäjä tai ryhmä sudoers-tiedostoon
- Artikkellissa kerrotaan esimerkin avuin miten luodaan uusi käyttäjä ja lisätään se uuteen ryhmään, ja miten ryhmä lisätään sudoers-tiedostoon
- Lopuksi on hyvä testata että sudo toimii ilman salasanaa esim. kommennolla sudo echo "hello". Mikäli komnento menee läpi ilman salasanaa, salasanaton sudo toimii oikein.

**Munroe 2006: xkcd 149: Sandwitch**
- Kuva kertoo enemmän kuin tuhat sanaa... :)
- Sudolla tarpeeksi voimaa tekemiseen. Ilman sudoa on rajoitetut valtuudet tehdä asioita. 

**Karvinen 2026: Passwordless Sudo with Ansible**
- Ansiblella mahdollista käyttää sudoa ilman salasanaa. Vaatii toimiakseen toimivan ansiblen.
- Root-tason muutokset vaativat 'become: true' parametrin käyttöä site.yml tiedostossa

**Ansiblen sisäänrakennettu dokumentaatio ansible-doc -kommennolla.**

**'ansible-doc copy'**
- kopio tiedostoja tai kansioita paikallisesta tai etäkoneesta paikalliseen tai etäkoneeseen
Optiot:
- content: määritetään esim. luotavan tiedoston sisältö
- dest: kohde johon kopioidaan
- src: lähde mistä kopioidaan
- owner: käyttäjä joka on objektin omistaja
- group: ryhmä joka on objektin omistaja 
- mode: Kohden tiedoston tai kansion oikeudet
- Esimerkki (tiedoston kopiointi):
  name: Copy file with owner and permissions
  ansible.builtin.copy:
    src: /srv/myfiles/foo.conf
    dest: /etc/foo.conf
    owner: foo
    group: foo
    mode: '0644'
  
**'ansible-doc apt'**
- Hallinnoi apt-paketteja
Optiot:
- name: määritetään asennettavat paketit, esim. foo tai htop
- state: asennettavan paketin tila. Esim. 'latest' asentaa viimeisimmän version
- update_cache: ajaa tarvittaessa 'apt-get udpdate' ennen operaatiota
- Esimerkki (foo-paketin asennus):
  name: Install the package "foo"
  ansible.builtin.apt:
    name: foo
  
**'ansible-doc file'**
- Muuosten tekeminen yksittäisiin tiedostoihin
Optiot:
 - path: muutettavan tiedoston polku
 - recurse: atribuuttien muuttaminen hakemiston sisällä oleville tiedostoille. Toimii jos 'state'-kohtaan asetettu 'directory'
 - src: tiedoton polku johon linkki viittaa
 - state: lisämääritys tiedostoille
 - owner: objektin omistaja (käyttäjä)
 - group: objektin omistaja (ryhmä)
 - mode: käyttöoikeuksien määritys objektille
- Esimerkki (tiedoston omistajan, ryhmän ja oikeuksien muutos):
   name: Change file ownership, group and permissions
  ansible.builtin.file:
    path: /etc/foo.conf
    owner: foo
    group: foo
    mode: '0644'
   
**'ansible-doc user'**
-  Käyttäjä tilien ja atribuuttien hallinnointi
Optiot:
- name: Käyttätunnus joka luodaa, muutetaan tai poistetaan
- create_home: luodaanko kotihakemisto
- comment: valinnainen kuvaus käyttäjätilille
- groups: käyttäjän ryhmä tai ryhmät
- shell:  shell-määritys käyttäjälle
- state: tilin olemassa olon määritys
- system: tilin määritys järjestelmätiliksi, mikäli tähän asetetaan 'true'
- Esimerkki (Uuden käyttäjätilin johnd luonti kotihakemistolla):
  name: Create a user 'johnd' with a home directory
  ansible.builtin.user:
  name: johnd
  create_home: yes

**'ansible-doc authorized_key'**
- Julkisten authorized SSH-avaimien lisäys tai poisto tietylle käyttäjätilille
Optiot:
- user: etätietokoneen käyttäjä jonka authorized avaimia muutetaan
- key: julkinen SSH avain
- Esimerkki (avaimen kopiointi tiedostosta):
    name: Set authorized key taken from file
    ansible.posix.authorized_key:
    user: charlie
    state: present
    key: "{{ lookup('file', '/home/charlie/.ssh/id_rsa.pub') }}"



## a) Sudoless

*Tee ansiblea varten tunnus, jolla voi käyttää sudoa ilman salasanaa. Sekä ssh-kirjautuminen että sudon käyttö tulee olla ansbilea varten automatisoitu.*

Kirjautusin Debianin virtuaalikoneeseen ja aloitin luomaan uutta käyttäjää sudo adduser komennolla, jonka jälkeen lisäsin uuden käyttäjän sudo ja adm-ryhmiin. 

<img width="470" height="254" alt="Kuva 1" src="https://github.com/user-attachments/assets/53c7c896-16d9-4cca-8468-a02e12bd53d0" />

Seuraavaksi muokkasin sudoers-tiedostoa ja lisäsin sinne uuden luodun käyttäjän user1ans. Sudo pitäisi toimia nyt käyttäjällä ilman salasanaa. 

<img width="1074" height="499" alt="Kuva 2" src="https://github.com/user-attachments/assets/f1ada1d7-59bd-4817-b09c-56fb9f2025b3" />


Tämän jälkeen automatisoin vielä SSH-kirjautumisen ilman salasanaa. Kirjautumalla ensiksi localhostiin SSH:llä (ssh user1ans@localhost) ja sen jälkeen luomalla avaimen komennolla ssh-keygen jonka kopioisin localhostiin komennolla ssh-copy-id localhost

<img width="1075" height="893" alt="Kuva 3" src="https://github.com/user-attachments/assets/50932c57-37a8-4c38-ba8c-ee5cfc5d598e" />

Kokeilin vielä komennolla ssh localhost ja kirjautuminen toimi ilman salasanaa. Vastaavasti myös sudo echo "Hello" toimi ilman salasanaa.

<img width="1025" height="220" alt="Kuva 4" src="https://github.com/user-attachments/assets/afaa6136-9ee9-4c37-a611-7d0400f7c45f" />

Kun palasin takaisin aiempaan ssh-istuntoon (käyttäjä sami), huomasin että salasanaton kirjautuminen ei toiminut täältä käyttäjälle user1ans. Ajoin käyttäjällä sami komennon 'ssh-copy-id user1ans@localhost' jonka jälkeen kirjautuminen toimi käyttäjälle user1ans ilman salasanaa.

<img width="1025" height="401" alt="Kuva 5" src="https://github.com/user-attachments/assets/2bc1180f-15c8-4ec5-9aab-97d94da70d65" />

## b) Antero

*Tee salasanaton, automaattisesti ssh:lla kirjautuva tunnus Ansiblella.*

Lähdin tekemään tehtävää Teron artikkelin "Passwordless Sudo with Ansible" mukaan. Loin ensiksi kansion 'Antero' jonne tein ansiblea varten tarvittavat tiedostot ja kansiot. Tehtävää varten tein sudoless ryhmän komennolla 'sudo groupadd sudoless' ja lisäsin sen sudoers-tiedostoon (%sudoless ALL = (ALL) NOPASSWD: ALL). Main.yml tiedostoa varten tarvitsin julkisen ssh avaimen jonka hain komennolla 'cat .ssh/id_ed25519.pub'.

<img width="1093" height="688" alt="Kuva 6" src="https://github.com/user-attachments/assets/1c09c2d7-2a37-49bb-b2bf-3e7b9b11b816" />

Kun ansiblea varten oli tarvittavat tiedostot ja kansiot luotu. Ajoin ansiblen komennolla 'ansible-playbook site.yml --ask-become-pass'. Ansible-komento toimi onnistuneesti ja loi uuden käyttäjän 'antero', joka lisättiin ryhmiin sudoless, sudo ja adm. Tämän lisäksi se lisäsi julkisen ssh avaimen käyttäjälle automaattista ssh-kirjautumista varten.  Testasin kirjautumista uudella käyttäjällä 'antero' jonka ansible oli luonut, käyttäen komentoa ssh antero@localhost. Kirjautuminen onnistui ilman salasanaa.  

<img width="1103" height="489" alt="Kuva 7" src="https://github.com/user-attachments/assets/29aebc5a-b7db-4a16-9071-e293f4eac8c2" />

<img width="1093" height="196" alt="Kuva 8" src="https://github.com/user-attachments/assets/d4a0347e-5ebc-4cf4-936e-b4f4a44c3406" />


## c) Package

*Asenna kaksi pakettia ansiblella.*

Loin Ansiblea varten kansion installpkg ja sinne tarvittavat tiedostot ja kansiot. Main.yml tiedostoon määritin että asennetaan paketit htop ja ncdu ja näistä viimeisimmät versiot. Samalla päivitetään myös pakettien listaus (update_cache: true). 

<img width="943" height="651" alt="Kuva 9" src="https://github.com/user-attachments/assets/17119be4-6dbb-4ac6-a882-02e9976eee96" />

Ajoin lopuksi ansiblen kommennolla 'ansible-playbook site.yml --ask-become-pass' joka asensi paketit onnistuneesti.

<img width="1097" height="324" alt="Kuva 10" src="https://github.com/user-attachments/assets/019c86b1-d526-495f-8686-8912e5fed407" />


## d) File

*Kirjoita orjalle useamman rivin mittainen tiedosto Ansiblella. Määrittele sen omistaja, omistava ryhmä ja oikeudet. Käytä oikeuksille oktaalinumeroa, esim. "0600". Kerro, mitä oikeudet ovat symbolisessa muodossa, esim. "-rwxr--r--". Selitä, mitä kukin käyttäjä saa tehdä tuolle tiedostolle.*

Tein Ansiblea varten ansfile-nimisen kansion jonne tein tarvittavat tiedostot ja kansiot. Main.yml tiedostoon määritin että luodaan secret.txt niminen tiedosto jonka omistaa käyttäjä sami, ja ainoastaan käyttäjä pääsee tiedostoon. Tiedosto sisältää myös kaksi riviä. Ansiblella pystyy tekemään useita rivejä tiedostoon putkella (lähde 'How to do multiline shell script in Ansible'). 

<img width="1097" height="634" alt="Kuva 11" src="https://github.com/user-attachments/assets/38a8474a-5c20-4cfc-8d7b-f9f07cc97651" />

Kokeilen luoda secret.txt tiedoston luodulla ansiblella ajamalla komennon 'ansible-playbook site.yml --ask-become-pass'. Komento meni onnistuneesti läpi ja teki kaksi rivisen tiedoston. Tiedoston omistajalla on ainoastaan read-write (0600) oikeudet. Lähde: 'University of Cambridge: File Permissions and Sharing Files'.

<img width="1097" height="324" alt="Kuva 12" src="https://github.com/user-attachments/assets/4cdd63ee-515d-4314-89ec-8da09428c35b" />

<img width="1097" height="270" alt="Kuva 13" src="https://github.com/user-attachments/assets/075028bc-6e9f-454c-8ab9-a34ee32042cd" />

<img width="759" height="185" alt="Kuva 14" src="https://github.com/user-attachments/assets/3fab9d14-5dad-431c-87a9-76255d5731b9" />


## e) Jotain muuta

*Näytä esimerkki ansiblen käskystä, jota ei ole vielä käsitelty kurssilla tai kotitehtävissä. Voit ottaa jonkun muun modulin kuin apt, file, copy, user tai authorized_key. Tai voit käyttää ominaisuutta, jota ei vielä ole demonstroitu. Jos tiivistystehtävässä x on mainittu ominaisuuksia, joita ei tunneilla tai läksyissä kokeiltu, nekin kelpaavat.*

Kokeilin tehdä ansiblen joka näyttäisi perustietoja hostista kuten OS ja kernel versio tiedot. Tein ansiblen nimeltä ansfacts ja käytin apuna lähdettä 'OneUpTime: How to Use the Ansible debug Module with msg and var'. Kun olin tehnyt tarvittavat tiedostot ja kansiot en saanut menemään ajoa läpi. Ekalla kerralla en ollut jättänyt välityöntiä ennen name-parametria ja toisella yrittämällä hankaluuksia tuli rivittämisen kanssa. Laitoin lopuksi viestin yhdelle riville, jolloin ansible meni läpi. Tuloksena versio tiedot käyttöjärjestelmästä ja kernelistä. 

<img width="630" height="380" alt="Kuva 15" src="https://github.com/user-attachments/assets/35ca6f52-7dd7-40c3-a394-b52fe05b2c19" />

<img width="942" height="324" alt="Kuva 16" src="https://github.com/user-attachments/assets/3972881a-2dff-46f6-af4c-8388642645fe" />

<img width="1091" height="604" alt="Kuva 17" src="https://github.com/user-attachments/assets/f3cf5399-8ea9-4732-a71d-fd84ca1603d3" />


## Lähteet

- Palvelinten Hallinta https://terokarvinen.com/palvelinten-hallinta/
- Sudo without password https://terokarvinen.com/passwordless-sudo/
- xkcd 149: Sandwitch https://xkcd.com/149/
- Passwordless Sudo with Ansible https://terokarvinen.com/passwordless-sudo-with-ansible/
- Ansiblen sisäänrakennettu dokumentaatio (ansible-doc)
- How to do multiline shell script in Ansible https://medium.com/@haroldfinch01/how-to-do-multiline-shell-script-in-ansible-69a4cc4b4239
- File Permissions and Sharing Files https://www.maths.cam.ac.uk/computing/linux/unixinfo/perms
- How to Use the Ansible debug Module with msg and var https://oneuptime.com/blog/post/2026-02-21-how-to-use-the-ansible-debug-module-with-msg-and-var/view


