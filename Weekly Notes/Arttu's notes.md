## Week 5 & 6

Aloitin rakentmaan ympäristöä virtuaalikoneelle. Alku oli helpohkoa, mutta zeekin kanssa törmäsin pieniin haasteisiin
Ensimäisellä asennus kerralla vm:n levytila ei riittänyt zeekin lähdekoodin asennukseen, joten jouduin lisäämään levytilaa
ennen asennuksen onnistumista

suricata: sudo apt update & upgrade -y
sudo apt intstall suricata -y

Suricatan asennnus oli helpompaa, koska se löytyy suoraan debianin virallisesta pakettivarastosta.

Zeekin asennus oli haastavampaa, koska valmista pakettia ei debianille ole, joten käänsin sen lähdekoodista.
Tässä vaiheessa myös lisäsin virtuaalilevyn kapasiteettia, jotta asennus lähdekoodista onnistuisi. 

Lataus lähdekoodista: wget https://download.zeek.org/zeek-6.0.0.tar.gz
tar -xzf zeek-6.0.0.tar.gz

sudo make install


 ## Week 7-9 

 Zeekin ja suricatan asennusten jälkeen oli suunnitelmana saada ne tuottamaan logeja ja dataa elasticsearchiin. Aloitin lisäämällä elaticrepon, 
 jonka avulla saa ohjelmistopaketit virallisesta Elasticin reposta

 
<img width="1076" height="65" alt="Näyttökuva 2026-02-25 kello 13 58 36" src="https://github.com/user-attachments/assets/77a887a8-a53a-45e7-86b2-0da90b6822e2" />

Seuraavaksi vain:

```
sudo apt update     sudo apt install elasticsearch -y
```


Kun tiedosto oli asennettu laitoin sen käyntiin: sudo systemctl start elasticsearch, sudo systemctl status elasticsearch

<img width="943" height="223" alt="Näyttökuva 2026-02-25 kello 14 17 53" src="https://github.com/user-attachments/assets/f1399bde-ebd3-449c-b201-a11cccefab2e" />

Kibanan asennus:

```
sudo apt install kibana -y
```

Asennuksen jälkeen tutkin kibanan konfiguraatiotiedostoa, mutta en tehnyt sinne vielä muutoksia. Jätin sen kuuntelemaan vain localhostissa

<img width="1444" height="416" alt="Näyttökuva 2026-02-25 kello 14 40 17" src="https://github.com/user-attachments/assets/d7d12b3c-89e0-4022-93ad-1900cf31cf3d" />

http://localhost:5061 -> kibana

<img width="1359" height="582" alt="Näyttökuva 2026-02-25 kello 14 43 45" src="https://github.com/user-attachments/assets/f8e8f4be-4a3a-4c86-99aa-f7e0c6aed167" />

Asensin losgstash:

```
sudo apt install logstash -y
```

Konffitiedostot vielä työn alla

<img width="1467" height="269" alt="Näyttökuva 2026-02-25 kello 15 21 00" src="https://github.com/user-attachments/assets/a8c1878e-d8ce-423d-b662-f9ea5e3a4a70" />


Tällä viikolla työskenetelin suricatan asennusohjeiden parissa suurimmaksi osin


Asensin myös filebeatin 

```
curl -L -O https://artifacts.elastic.co/downloads/beats/filebeat/filebeat-9.3.1-amd64.deb
sudo dpkg -i filebeat-9.3.1-amd64.deb
```


https://www.elastic.co/docs/reference/beats/filebeat/filebeat-installation-configuration



Asennuksen jälkeen testasin toimintaa 

<img width="902" height="247" alt="Näyttökuva 2026-03-02 kello 23 21 16" src="https://github.com/user-attachments/assets/1e4e601c-4062-456b-9643-8dac3dd95404" />

```
sudo systemctl status filebeat
```

Status on active joten asennus onnistui, katsoin vielä kuitenkin onnistuinko konfiguraation parissa ja toimiiko yhteys elasticsearchiin

konffitiedostoon ->

```
sudo nano /etc/filebeat/filebeat.yml
```

muokkasin output.elasticsearch kohtaa ja laitoin hostiksi localhostin

jotta konffitiedosto toimii, se pitää yhdistää elasticsearchiin laittamalla käyttäjätunnus ja salasana konffitiedostoon


```
sudo filebeat test config    sudo filebeat test output
```



<img width="708" height="308" alt="Näyttökuva 2026-03-02 kello 23 20 29" src="https://github.com/user-attachments/assets/fea24607-2204-4852-ba29-38c2b8a21790" />



Myöhemmin viikolla oli ongelmia kibanan kanssa, joita yritimme tiimin kanssa yhdessä ratkaista


## Week 10 & 11

Suurin osa tämän viikon (vko 10) työstä meni Zeekin asennusohjeiden kirjoittamiseen ja niiden testaamiseen 

Asensin nginxin myös, koska sitä ei aiemmin syystä tai toisesta ollut.

```
sudo apt install nginx
```

-> tuli kuitenkin ongelma, yritin katsoa nginx statusta, mutta tuli ilmoitus että portti on varattu

Kokeilin etsiä, mikä on vikana ja vika selvisi heti ensimmäisessä vaiheessa. Portti 80 oli varattu toiselle palvellulle.

-> tällä komennolla: 

```
sudo ss -tulpn | grep :80
```

Huomasin että apache2 käytti porttia 80, joten suljin sen 


<img width="1317" height="85" alt="Näyttökuva 2026-03-15 kello 19 20 45" src="https://github.com/user-attachments/assets/45969f5d-0910-42fc-b21e-7eb8397104cc" />


```
sudo systemctl start nginx
```

```
sudo systemctl status nginx
```

Tämän operaation jälkeen asentelin lisää paketteja ja ahrjoittelin niiden käyttöä

## Week 12-14...


### tcpreplay & dummy

Ensiksi tcpreplay

```
sudo apt update, sudo apt install -y tcpreplay
```

Asennus onnistui, versiona 4.5.1. Nopella tarkistuksella tämä myös uusin versio mitä saatavilla.


<img width="700" height="305" alt="Näyttökuva 2026-03-17 kello 20 09 50" src="https://github.com/user-attachments/assets/ed1ebc04-bda6-4105-96e9-9a9b5196e51a" />

Asennuksen jälkeen olin hetken umpikujassa, sillä en ollut ihan varma mitä seuraavaksi pitäisi tehdä. Siispä aloin tuktia trpreplayta. 
Työkaluhan on siis loistava juuri tähän projektiin, sillä sen avulla helpohko toistaa verkkoliikennettä. 
Nikon vinkistä otin koppia ja aloin myös työstämään dummy-interfacea.


```
sudo ip link add dummy0 type dummy
sudo ip addr add 192.0.2.0/24 dev dummy0
sudo ip link set dummy0 up
```
-> komennolla luotiin uusi verkkolaite, PITÄÄ luoda uusiksi tietyin väliajoin (esim. rebootissa tai kun konffataan network asetuksia.

<img width="518" height="103" alt="Näyttökuva 2026-03-17 kello 20 54 38" src="https://github.com/user-attachments/assets/8c7896b5-2ed2-4639-97af-91c373b627c9" />

katsottiin että verkko luotiin ->

```
ip addr show dummy0
```

<img width="985" height="129" alt="Näyttökuva 2026-03-17 kello 20 57 56" src="https://github.com/user-attachments/assets/41d6419a-a571-4d0d-8669-e773e079140c" />

Laitoin sen kuuntelemaan Suricataa ja zeekiä

```
sudo suricata -i dummy0 -c /etc/suricata/suricata.yaml -D
```

```
sudo /opt/zeek/bin/zeek -i dummy0
```

Seuraaavaksi tuhlasin hieman aikaa, sillä luulin että minulla oli ongelma -> 

<img width="1470" height="418" alt="Näyttökuva 2026-03-17 kello 21 27 58" src="https://github.com/user-attachments/assets/00cc1795-cfc8-4617-9b4b-c0710338c366" />

Syynä oli siis eri prosessien toiminta, tuota systemd ei pysty siis tässä yhteydessä käyttämään. Manuualinen toimii



<img width="1100" height="88" alt="Näyttökuva 2026-03-17 kello 21 34 46" src="https://github.com/user-attachments/assets/d64d90fa-9caa-4fae-951e-8419cb476bdb" />


```
sudo nano /etc/filebeat/filebeat.yml
```


```
filebeat.inputs:
  - type: filestream
    id: suricata-eve
    enabled: true
    paths:
      - /var/log/suricata/eve.json
    parsers:
      - ndjson:
          target: ""

  - type: filestream
    id: zeek-logs
    enabled: true
    paths:
      - /opt/zeek/logs/current/*.log

output.elasticsearch:
  hosts: ["http://localhost:9200"]
```

Nyt toimii


<img width="1469" height="327" alt="Näyttökuva 2026-03-23 kello 12 27 29" src="https://github.com/user-attachments/assets/bed692cd-7308-48f6-811c-8703ef4d5fef" />


Yritin saada testitiedostoja toimimaan ja samalla harjoittelin miten ne toimivat ja miten niitä on mahdollista muokata, jotta saadaan enemmän dataa läpi


<img width="608" height="291" alt="Näyttökuva 2026-03-23 kello 13 22 45" src="https://github.com/user-attachments/assets/702589a5-8dae-4bd3-9c20-5a58f3980c2d" />

pingasin ja ajoin eri testikomentoja 

<img width="786" height="297" alt="Näyttökuva 2026-03-23 kello 13 23 22" src="https://github.com/user-attachments/assets/cf61186a-85ce-4b50-9087-4b7fd38a673e" />

<img width="775" height="85" alt="Näyttökuva 2026-03-23 kello 13 24 07" src="https://github.com/user-attachments/assets/436b0866-5eb7-4f72-bc7b-aabc0ffb7dbd" />

Testit nyt osoittavat sen, että tulokset näkyvät myös kibanassa

<img width="1170" height="615" alt="Näyttökuva 2026-03-23 kello 13 25 29" src="https://github.com/user-attachments/assets/8aa81da5-1ffd-4c8d-b26a-186583c3e1fa" />


Myöhemmässä vaiheessa tuli onglema kibanan kanssa, kun en saanut enää hälytykisä siellä.

<img width="670" height="505" alt="Näyttökuva 2026-03-23 kello 20 19 35" src="https://github.com/user-attachments/assets/fa76767b-9657-4c7c-8a1b-87d721aad913" />

Pienen ihmettelyn jälkeen ongelmaksi selvisi http protokolla, sillä sen piti olla https.

Nyt järjestelmän rakennus on jo loppusuoralla.

### Week 14

Palaverien jälkeen päätimme tehdä muutamia muutoksia projektiin, ensinäkin ansiblen käyttöönotto tässä vaiheessa oli käytännön must have tilanne.

Nyt käyttöön otettavan ansiblen myötä pohdimme olisiko Wazuh dashboard parempi ratkaisu, sillä tämä pystyisi korvaamaan kibanan mahdollisesti kokonaan. Päätin tutkia enemmän tätä asiaa nyt

Mutta ensin asensin ansible

```
sudo apt install ansible
```

Tein sille oman kansionn ja tein testi tiedoston sisällöllä:

```
- hosts: localhost
  connection: local
  tasks:
    - name: Test
      debug:
        msg: "Ansible futaa!"
```



<img width="1130" height="336" alt="Näyttökuva 2026-03-30 kello 11 18 44" src="https://github.com/user-attachments/assets/839b2861-a467-4db6-8878-6bb682914582" />

En kuitenkaan omaan labraani lähde enempää yrittämään, sillä muu ryhmä hoitaa sitä. Tarkoitus saada samanlainen labra kaikille zip- tiedoston avulla. 

Kokeilin saada Wazuhin omat dashboardit toimimaan, joka voisi tiputtaa siis kokonaan Kibanan esimerkiksi pois. 

Ennenkuin pääsen sen kimppuun, täytyy kuitenkin hieman tutkia wazuhia.

<img width="1181" height="92" alt="Näyttökuva 2026-03-30 kello 11 48 20" src="https://github.com/user-attachments/assets/52dd53b4-6d04-4af7-af47-2bd678dc9c98" />

```
curl -s https://packages.wazuh.com/key/GPG-KEY-WAZUH | sudo gpg --dearmor -o /usr/share/keyrings/wazuh.gpg
```

Avain toimii!

Seuraavaksi lisäsin repon 

```
echo "deb [signed-by=/usr/share/keyrings/wazuh.gpg] https://packages.wazuh.com/4.x/apt/ stable main" | sudo tee /etc/apt/sources.list.d/wazuh.list
```

Ja asensin wazuh-managerin, joka siis on serveri joka vastaanottaa dataa agenteilta, analysoi sitä ja generoi hälytyksiä.
Seuraavaksi asennan wazuh clientin 


<img width="940" height="729" alt="Näyttökuva 2026-03-30 kello 17 36 49" src="https://github.com/user-attachments/assets/09eca163-9491-4bbf-87ef-00cf43be5f50" />

Etsin tietoa miten dashboardit toimisivat tässä: 

https://documentation.wazuh.com/current/installation-guide/wazuh-dashboard/index.html

Seuraavaksi kuitenkin asensin wazuh-agentin. Agentti toimii yhdessä managerin kanssa.


<img width="865" height="490" alt="Näyttökuva 2026-03-30 kello 18 07 02" src="https://github.com/user-attachments/assets/46e52ab2-5f14-42fd-a678-751ecb371c36" />


Komento takaisin se ei toimikkaan samassa koneessa. Elikkä pääydin pitämään vain managerin.


<img width="884" height="539" alt="Näyttökuva 2026-03-30 kello 20 40 56" src="https://github.com/user-attachments/assets/291ffce8-a89c-4b93-b7c6-932edf3bdb07" />

Tein nyt myös uuden virtuaalikoneen, jolla tarkoitus myöhemmin ajaa testinä zip- tiedosto.

Nyt rakensin tiedostoa, mikä 

```
#!/bin/bash

set -e

echo "[+] Päivitetään järjestelmä..."
sudo apt update && sudo apt upgrade -y

echo "[+] Asennetaan riippuvuudet..."
sudo apt install curl apt-transport-https lsb-release gnupg -y

echo "[+] Lisätään Wazuh repo..."
curl -s https://packages.wazuh.com/key/GPG-KEY-WAZUH | sudo gpg --dearmor -o /usr/share/keyrings/wazuh.gpg

echo "deb [signed-by=/usr/share/keyrings/wazuh.gpg] https://packages.wazuh.com/4.x/apt/ stable main" | sudo tee /etc/apt/sources.list.d/wazuh.list

sudo apt update

echo "[+] Asennetaan Wazuh (all-in-one)..."
curl -sO https://packages.wazuh.com/4.7/wazuh-install.sh

sudo bash wazuh-install.sh -a --ignore-check

echo "[+] Odotetaan palveluiden käynnistymistä..."
sleep 30

echo "[+] Tarkistetaan palvelut..."
sudo systemctl status wazuh-manager --no-pager || true
sudo systemctl status wazuh-indexer --no-pager || true
sudo systemctl status wazuh-dashboard --no-pager || true


echo ""
echo "======================================"
echo " WAZUH VALMIS "
echo "======================================"
echo "Dashboard: https://localhost"
echo "User: admin"
echo "Password: löytyy /root/wazuh-passwords.txt"
echo ""
```

Lisäsin tämän tiedostoon

```
nano wazuh-install.sh
```


Filebeat, kibana ja elcsearch piti pysäyttää tässä vaiheessa, jotta ongelmia ei tulisi, sillä nämä järjesteömät ovat hyvin samankaltaisia ja käyttävät todennäköisesti samoja portteja. 

<img width="1437" height="624" alt="Näyttökuva 2026-03-30 kello 22 31 54" src="https://github.com/user-attachments/assets/3edfe817-3ac5-4d9a-b94b-b07f3c7c2ae4" />

Uusi skripti siis toimii.

<img width="840" height="788" alt="Näyttökuva 2026-03-30 kello 22 39 07" src="https://github.com/user-attachments/assets/d16d2d33-c667-4beb-9e04-76dd80224ead" />

Ei toimi, alan selvittämään missähän vika.

<img width="506" height="72" alt="Näyttökuva 2026-03-30 kello 22 43 02" src="https://github.com/user-attachments/assets/bbe54e50-0d49-45c1-a8e7-3846fc9710a6" />

Ongelma ei ole se että sitä ei olisi asennettua vaan se on jossain syvemmällä. 


<img width="479" height="109" alt="Näyttökuva 2026-03-30 kello 22 59 25" src="https://github.com/user-attachments/assets/68fb1292-16f7-43dc-a42b-ba8ad81cb21d" />

Poistin aiemman skrpitin kokonaan ja pyrin vain muokkaamaan wazuhn valmista skriptiä, en saanut haluttua lopputulosta


<img width="1051" height="87" alt="Näyttökuva 2026-03-30 kello 22 55 50" src="https://github.com/user-attachments/assets/edff3287-d893-43eb-b958-c940d325e52f" />


Löysin paljon tietoa siitä miten wazuh ei tue tätä debian versiota mikä minulla on 
