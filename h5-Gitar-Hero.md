# h5 Gitar Hero

## **x) Artikkelit**

**Chacon and Straub 2014: Pro Git, 2ed: 1.3 Getting Started - What is Git?**
- Versionhallinta pitää yllä kirjaa muutoksista. Versionhallintoja on monenlaisia kuten mm. paikallinen versionhallinta (kansiot jokaiselle versiolle paikallisesti), keskitetty versionhallinta jossa muutokset yhdellä serverillä sekä hajaitettu version hallinta, jossa käyttäjä saa täydellisen kopion reposta sisältäen koko historian. Tämä toimii myös täydellisenä varmuuskopiona. 
- Verrattuna muihin versionhallintoihin, git tallentaa tiedoston muutokset snapshottiin. Mikäli ei ole muutosta, git viittaa aiempaan versioon. Lopputuloksena ketju snapshotteja. 

**Gitin käyttö on lähinnä 'git add --all && git commit; git pull && git push'. Selitä tuon komennon jokainen osa. Käytä apuna itse valitsemiasi lähteitä ja viittaa niihin.**
- git add --all: Lisää tiedot uusista tai muutetuista tiedostoista indexiin seuraavaa commitia varten. (Lähde: https://git-scm.com/docs/git-add)
- git commit: Luo uuden commitin eli version niistä tiedostoista joita muutettu tai ovat uusia (kts. aiempi kohta git add)(Lähde: https://git-scm.com/docs/git-commit)
- git pull: Hakee muutokset etärepositoriosta ja ne yhdistää ne paikalliseen repoon (Lähde: https://git-scm.com/docs/git-pull)
- git push: Puskee muutokset paikallisesta repositoriosta etärepositorioon. (Lähde: https://git-scm.com/docs/git-push)

## a) Online 

*Tee uusi varasto GitHubiin (tai Gitlabiin tai mihin vain vastaavaan palveluun). Varaston nimessä ja lyhyessä kuvauksessa tulee olla sana "sunshine". Aiemmin tehty varasto ei kelpaa. (Muista tehdä varastoon tiedostoja luomisvaiheessa, esim README.md ja GNU General Public License 3) Edistyneemmille voi olla hauskaa etsiä ja kokeilla jokin muu palvelu kuin Github.*

Lähdin luomaan uutta repoa oman Github-tilini alle ohjeistuksen mukaisesti. Repon nimeksi tuli sunshine ja sama tuli laitettua myös kuvaukseen. Laitoin repon julkiseksi, lisäsin siihen readmen ja lisenssiksi tuli valittua GNU General Public License 3.

<img width="855" height="796" alt="Kuva 1" src="https://github.com/user-attachments/assets/ca83866e-6fb6-45f3-8602-243ce02aaaa0" />

Ohessa alla vielä kuvakaappaus että sunshine repo luotu onnistuneesti.

<img width="1258" height="702" alt="Kuva 2" src="https://github.com/user-attachments/assets/06201347-5bb3-4948-afbf-1b7f1183a8c0" />

## b) Dolly 

*Kloonaa edellisessä kohdassa tehty uusi varasto itsellesi, tee muutoksia omalla koneella, puske ne palvelimelle, ja näytä, että ne ilmestyvät weppiliittymään.*

Julkisen SSH-avaimen (cat .ssh/id_ed25519.pub) olin lisännyt jo aiemmin Githubiin. 

<img width="1297" height="387" alt="Kuva 3" src="https://github.com/user-attachments/assets/1510e6df-79f6-432f-a8c6-dcea19f9af60" />


Seuraavaksi lähdin kloonaamaan repoa aiemmin tehtyyn code-hakemistoon. Kloonaus sujui onnistuneesti jonka jälkeen tein pienin muutoksen readme-tiedostoon ja puskin muutoksen githubiin.

<img width="957" height="596" alt="Kuva 4" src="https://github.com/user-attachments/assets/e3bbc672-5f60-47c2-bd7a-be08f8df6b63" />

Muutos oli päivittynyt onnistuneesti myös githubin sivulle. 

<img width="1540" height="823" alt="Kuva 5" src="https://github.com/user-attachments/assets/4b878cb0-9940-4f01-a424-b6a3593bab10" />


## c) Doh! 

*Tee tyhmä muutos gittiin, älä tee commit:tia. Tuhoa huonot muutokset ‘git reset --hard’. Huomaa, että tässä toiminnossa ei ole peruutusnappia.*

Lisäsin tiedoston newfile.md githubin web-liittymän kautta. Lisäyksen jälkeen tajusin että lisäys teki samalla commitin mitä ei olisi ilmeisesti saanut tehdä.

<img width="1540" height="823" alt="Kuva 6" src="https://github.com/user-attachments/assets/8021cfd7-89ea-48b0-828b-5b2370da1d21" />

Seuraavaksi lähdin kokeilemaan miten saan poistettua tiedoston ja committin gitistä. Ajoin ensiksi git pull komennon jolla uusi ei toivottu tiedosto tuli koneelle. Git push komennon ajoin epähuomiossa, mutta ls-komento paljasti että tiedosto oli hakemistossa. Kommennolla 'git reset --hard HEAD~1' commit oli poistettu ja palautunut aiempaan vaiheeseen. Seuraavaksi puskin muutoksen vielä gittiin kommennolla 'git push origin main --force'. Nyt tiedosto oli poistunut koneelta sekä myös githubin webbiliittymästä. 

<img width="802" height="388" alt="Kuva 7" src="https://github.com/user-attachments/assets/f0d868f5-104b-4949-ad74-71a153a1faec" />

<img width="1578" height="831" alt="Kuva 8" src="https://github.com/user-attachments/assets/54394667-3771-4206-a30d-3d96fd6e3520" />

## d) Tukki

*Tarkastele ja selitä varastosi lokia. Näytä myös, mitä muutoksia tiedostoihin on tehty. Tarkista, että nimesi ja sähköpostiosoitteesi näkyy haluamallasi tavalla ja korjaa tarvittaessa.*

Tutkin lokia kommennolla 'git log --patch'. Lokissa näkyi ensimmäiseksi viimeisin commit eli tässä tapauksessa commit jossa olin tehnyt muutoksia readme-tiedostoon. Seuraava rivi näytti initial commitin eli koska repo oli tehty ja mitä tiedostoja oli lisätty repon luomisen yhteydessä. Nimi ja sähköposti olivat oikein. 

<img width="986" height="713" alt="Kuva 9" src="https://github.com/user-attachments/assets/ca0e0481-465a-42fb-bd62-4edc00cd4772" />

--

<img width="986" height="468" alt="Kuva 10" src="https://github.com/user-attachments/assets/d7d29115-804a-4c87-ab7b-4a4a27cd947d" />


## e) Gitanbile

*Laita Ansible-kansio versionhallintaan. Tee jokin muutos, aja ansiblella, tallenna versio (commit).*

Tein uuden repon githubiin nimeltä ansible-OS-info.

<img width="960" height="856" alt="Kuva 11" src="https://github.com/user-attachments/assets/ca533dd2-2d22-4e6a-98e9-2746757f761c" />

Repon luonnin jälkeen kloonasin sen code kansioon.

<img width="960" height="249" alt="Kuva 12" src="https://github.com/user-attachments/assets/1919e067-658c-4a6d-b042-db504508cacb" />

Seuraavaksi kopioisin kloonattuun kansioon aiemmin tehdyn ansiblen. 

<img width="960" height="243" alt="Kuva 13" src="https://github.com/user-attachments/assets/f52ff732-e18c-42ea-b2d1-03df093048a7" />

Kun tarvittavat tiedostot oli kopioitu, puskin tiedostot seuraavaksi githubiin. 

<img width="960" height="365" alt="Kuva 14" src="https://github.com/user-attachments/assets/36825ae2-7e11-4012-88cc-258294fea1e3" />

<img width="1627" height="874" alt="Kuva 15" src="https://github.com/user-attachments/assets/59318610-7c6c-4d88-8ca8-cea34ad9ac9e" />

Kokeilin seuraavaksi että ansible toimii edelleen ajamalla playbookin. Ansible näytti tiedot käyttöjärjestelmästä joten vaikutti pelaavan. 

<img width="1097" height="334" alt="Kuva 16" src="https://github.com/user-attachments/assets/11eac2cf-4658-4e90-b6da-c93564cea27e" />

Tein muutoksen main.yml tiedostoon lisäämällä sinne hostnamen. Muutoksen jälkeen ajoin playbookin uudestaan ja ansible näytti hostnamen. 

<img width="1302" height="493" alt="Kuva 17" src="https://github.com/user-attachments/assets/e7b2bbb5-02f5-4cf4-9d6c-8901f73d6018" />

Lopuksi vielä lähetin muutoksen githubiin eli tein commitin. 

<img width="1302" height="282" alt="Kuva 18" src="https://github.com/user-attachments/assets/9b48a01e-6ea2-4fcf-b5b9-e27497093536" />

<img width="1606" height="835" alt="Kuva 19" src="https://github.com/user-attachments/assets/8f1ab102-c32c-49b8-aee2-09778b1e2199" />

<img width="1606" height="835" alt="Kuva 20" src="https://github.com/user-attachments/assets/54cb8bd1-6d40-42af-9c5b-203d11575655" />


## f) Hae pari projektiin Moodlen keskustelusta.

Kaveria huhuiltu projektiin Moodle-alustalla muutaman päivän ajan mutta toistaiseksi ei ole onnistunut (tilanne 28.4.)


## Lähteet

- Palvelinten Hallinta https://terokarvinen.com/palvelinten-hallinta/
- Chacon and Straub 2014: Pro Git, 2ed: 1.3 Getting Started - What is Git?, https://git-scm.com/book/en/v2, https://git-scm.com/book/en/v2/Getting-Started-What-is-Git%3F



