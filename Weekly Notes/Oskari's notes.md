## WEEK 5 & 6

I already started building the first prototype on a blank Debian platform. The platform initially needed some fine-tuning, so I installed the basic programs (curl, sources.list, a text editor, wget, htop, neofetch, git, unzip, and zip).

After this, I went to the Suricata and Zeek websites to install the programs, as well as directly from the elastic.co/docs site for Elasticsearch, Beats, and Kibana.

Page for ELK stack installation: https://www.elastic.co/docs/deploy-manage/deploy/self-managed/install-elasticsearch-with-debian-package

Suricata: sudo apt install suricata -y

Zeek: https://www.howtoforge.com/how-to-install-zeek-network-security-monitoring-on-debian-12/

## WEEK 6 & 7

After the basic installs for the IDS were installed, I wanted to make the first alert visible in Kibana using a older PCAP file and playing it in TCPreplay

First alert generated in Kibana using tcpreplay.

TROUBLESHOOTING:

I initially had a memory issue, as I was unable to start Elasticsearch and the VM was lagging too much. I resolved the situation by increasing my laptop's memory allocation. The recommended VM memory would be at least 8 GB.

When installing Zeek, it's worth making sure your own interface is connected to it. First run the command ip a to confirm your interface, then run sudo nano /opt/zeek/etc/node.cfg to verify what is set in Zeek.



USEFUL LINKS:

Damon Garn, How to use tcpreplay: https://www.techtarget.com/searchsecurity/tutorial/How-to-use-tcpreplay-to-replay-network-packet-files 

A website and framework for testing NIDS detection: testmynids.org

PCAPs for Testing: https://docs.securityonion.net/en/2.4/pcaps.html
