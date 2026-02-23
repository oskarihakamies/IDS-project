## WEEK 5 & 6

Aloitin jo ensimmäisen prototyypin rakennusta tyhjälle debian alustalle. Alusta tarvitsi aluksi hieman hienotuunausta, joten asensin perusohjelmat (curl, sources.listin, tekstieditorin, wget, htop, neofetch, git, unzip ja zip)

Tämän jälkeen lähdin suricatan ja zeekin sivuilta asentamaan ohjelmia, sekä suoraan elasticsearchin, beatsin ja kibanan elastic.co/docs sivulta. 

Sivu ELK stackin asennukseen: https://www.elastic.co/docs/deploy-manage/deploy/self-managed/install-elasticsearch-with-debian-package

Suricata: sudo apt install suricata -y

Zeek: https://www.howtoforge.com/how-to-install-zeek-network-security-monitoring-on-debian-12/

## WEEK 6 & 7



TROUBLESHOOTING:

Minulla oli alkujaan ongelma muistin kanssa, sillä en päässyt käynnistämään elasticsearchia ja VM lagasi liikaa. Korjasin tilanteen lisäämällä läppärini muistia. Suositeltava VM muisti olisi vähintään 8 GB

Kannattaa zeekia asentaessa varmistaan onko oma interface yhdistetty siihen. Komento *ip a* ensin varmistamaan oma, jonka jälkeen sudo nano /opt/zeek/etc/node.cfg varmistamaan, mikä zeekissa on. 


USEFUL LINKS:

Damon Garn, How to use tcpreplay: https://www.techtarget.com/searchsecurity/tutorial/How-to-use-tcpreplay-to-replay-network-packet-files 

A website and framework for testing NIDS detection: testmynids.org

PCAPs for Testing: https://docs.securityonion.net/en/2.4/pcaps.html
