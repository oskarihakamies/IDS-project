Week 5 & 6

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


