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

 ## Week 7 & 8

 Zeekin ja suricatan asennusten jälkeen oli suunnitelmana saada ne tuottamaan logeja ja dataa elasticsearchiin. Aloitin lisäämällä elaticrepon, 
 jonka avulla saa ohjelmistopaketit virallisesta Elasticin reposta

 
<img width="1076" height="65" alt="Näyttökuva 2026-02-25 kello 13 58 36" src="https://github.com/user-attachments/assets/77a887a8-a53a-45e7-86b2-0da90b6822e2" />

Seuraavaksi vain: sudo apt update, sudo apt install elasticsearch -y

Kun tiedosto oli asennettu laitoin sen käyntiin: sudo systemctl start elasticsearch, sudo systemctl status elasticsearch

<img width="943" height="223" alt="Näyttökuva 2026-02-25 kello 14 17 53" src="https://github.com/user-attachments/assets/f1399bde-ebd3-449c-b201-a11cccefab2e" />

Kibanan asennus: sudo apt install kibana -y

Asennuksen jälkeen tutkin kibanan konfiguraatiotiedostoa, mutta en tehnyt sinne vielä muutoksia. Jätin sen kuuntelemaan vain localhostissa

<img width="1444" height="416" alt="Näyttökuva 2026-02-25 kello 14 40 17" src="https://github.com/user-attachments/assets/d7d12b3c-89e0-4022-93ad-1900cf31cf3d" />

http://localhost:5061 -> kibana

<img width="1359" height="582" alt="Näyttökuva 2026-02-25 kello 14 43 45" src="https://github.com/user-attachments/assets/f8e8f4be-4a3a-4c86-99aa-f7e0c6aed167" />

Asensin losgstash: sudo apt install logstash -y

Konffitiedostot vielä työn alla

<img width="1467" height="269" alt="Näyttökuva 2026-02-25 kello 15 21 00" src="https://github.com/user-attachments/assets/a8c1878e-d8ce-423d-b662-f9ea5e3a4a70" />

