## WEEK 5 & 6

I already started building the first prototype on a blank Debian platform. The platform initially needed some fine-tuning, so I installed the basic programs (curl, sources.list, a text editor, wget, htop, neofetch, git, unzip, and zip).

After this, I went to the Suricata and Zeek websites to install the programs, as well as directly from the elastic.co/docs site for Elasticsearch, Beats, and Kibana.

Page for ELK stack installation: https://www.elastic.co/docs/deploy-manage/deploy/self-managed/install-elasticsearch-with-debian-package

**ELK stack** is installed quite similarly as Suricata. For example elasticsearch installs:
```
sudo apt install elasticsearch -y
```
**Suricata:** 
```
sudo apt install suricata -y
```
**Zeek:**

https://www.howtoforge.com/how-to-install-zeek-network-security-monitoring-on-debian-12/

The install is same in Debian 13 as in 12. To check if Zeek was downloaded you can just use: 
```
zeek --version  
```
Or if you run to the same problem as me that Zeek wasn't connected fully to PATH you can use: 
```
/opt/zeek/bin/zeek --version
```
It prints out the version

<img width="346" height="44" alt="image" src="https://github.com/user-attachments/assets/e738fb91-786c-4264-b4ef-b30bec2fcd07" />





## WEEK 6 & 7

After the basic installs for the IDS were installed, I wanted to make the first alert visible in Kibana using a older PCAP file and playing it in TCPreplay.

BTW you can use which ever pcap file you like while first testing this IDS. I still recommend downloading the full pcap file list from netresec here: https://share.netresec.com/s/nF5zNcaXLgwdQFZ?openfile=true

Deployment of the programs is quite simple. The first step for the first version of our IDS is to enable the tools for it. 
```
sudo systemctl enable --now elasticsearch


sudo systemctl enable --now kibana
```

                                                          
You can test if the elasticsearch is responding by using: 
```

curl http://localhost:9200

```
<img width="410" height="80" alt="image" src="https://github.com/user-attachments/assets/87ae6446-cbf4-457f-9e1d-4e4f830c9d5a" />


It's going to print a JSON format with a elasticsearch as a cluster name. Other way you can check the programs is to normal systemctl status command on whichever program you would like to check. For example elasticsearch will show active:


<img width="656" height="155" alt="image" src="https://github.com/user-attachments/assets/9dacaba1-b357-4f07-a22a-d135517094f8" />



Now to the BTW part on pcap files. You can take any you want. I found one online and got it with wget command. 

I used the tcpreplay here with 

```
sudo tcpreplay --intf1=enp0s3 --multiplier=1.0 /home/$USER/portscan.pcap
```

I used multiplier but there are more commands to use after the interface part. For example top speed, loopback etc. 


And then checked the printout on the command. 

<img width="415" height="149" alt="image" src="https://github.com/user-attachments/assets/9341c8f7-02a5-45ac-b4c2-b0d16d5f89dd" />





After that I enabled rest of the programs. 



```
sudo filebeat modules enable suricata zeek

sudo filebeat setup    # downloads the already ready Kibana dashboards - takes like 1-2min

sudo systemctl enable --now filebeat
```








or thats the way I like to start ut 



First alert generated in Kibana using tcpreplay.

TROUBLESHOOTING:

I initially had a memory issue, as I was unable to start Elasticsearch and the VM was lagging too much. I resolved the situation by increasing my laptop's memory allocation. The recommended VM memory would be at least 8 GB.

When installing Zeek, it's worth making sure your own interface is connected to it. First run the command ip a to confirm your interface, then run sudo nano /opt/zeek/etc/node.cfg to verify what is set in Zeek.

The same interface should be checked with suricata. I can show the example on status command if the interface is wrong:

<img width="506" height="185" alt="image" src="https://github.com/user-attachments/assets/eba2efc5-708f-43c8-b35e-f97c31aea067" />

And to change it you would need to check your own interface with ip a. Mine was enp0s3 so I changed it in suricata.yml by using nano. 




USEFUL LINKS:

Damon Garn, How to use tcpreplay: https://www.techtarget.com/searchsecurity/tutorial/How-to-use-tcpreplay-to-replay-network-packet-files 

A website and framework for testing NIDS detection: testmynids.org

PCAPs for Testing: https://docs.securityonion.net/en/2.4/pcaps.html
