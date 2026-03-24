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


Then I sent the test pcap file in tcpreplay and checked the kibana alerts with localhost:5061 in the browser. 


And the alert was created. 


or thats the way I like to start the IDS. 


First alert generated in Kibana using tcpreplay.


## WEEK 8 & 9

Next up I was tasked to connect wazuh in the program. Wazuh is used as a backup and is connected directly to filebeat. 



Wazuh is used as log backup and compliance tool. It can be connected to the Kibana dashboard so you can see the stored logs.



For short - it has two elements. The Manager which is the server and the Agent which stores data logs. 


The installing process is quite simple. You can check it from here: https://documentation.wazuh.com/current/installation-guide/wazuh-server/step-by-step.html

For short: 
```
#Install the GPG key

curl -s https://packages.wazuh.com/key/GPG-KEY-WAZUH | sudo gpg --no-default-keyring --keyring gnupg-ring:/usr/share/keyrings/wazuh.gpg --import && chmod 644 /usr/share/keyrings/wazuh.gpg

#Add to the repo

echo "deb [signed-by=/usr/share/keyrings/wazuh.gpg] https://packages.wazuh.com/4.x/apt/ stable main" | tee -a /etc/apt/sources.list.d/wazuh.list


#And then update your system before installing it


apt-get -y install wazuh-manager


#Connect it to filebeat


curl -so /etc/filebeat/filebeat.yml https://packages.wazuh.com/4.14/tpl/wazuh/filebeat/filebeat.yml

```

There are few additions you need to make after this. You can check the rest of the installing from the official Wazuh documentation which i've linked up there and is also in the links part at the bottom of the page. 


I have taken a screenshot from the architecture to visualize the patch where Wazuh is located. The premise is that filebeat sends the traffic to Wazuh before it connects to elasticsearch. 


<img width="893" height="389" alt="image" src="https://github.com/user-attachments/assets/c75b57ec-2f1a-4cb8-a72a-d57380b40b10" />

You can view the full Architecture also from here: [View IDS Architecture](https://oskarihakamies.github.io/IDS-project/architecture/ids-architecture.html)


After with some problems regarding Kibana I finally got an alert showing. 

<img width="943" height="353" alt="image" src="https://github.com/user-attachments/assets/c6655c41-ac4d-4a10-8f30-be905458dae0" />


Next step is to design the own dashboard which includes the alerts shown and Wazuh showing data logs. 


## WEEK 11 & 12

The task for week 11 & 12 was to make the prototype testworthy of an outsider. In this case my roommate. But first I had to see if Kibana had any problems like previously (they were fixed by restarting Kibana).


I opened my VM and it had some kind of problem regarding packagekitd when I tried updating it.

<img width="618" height="113" alt="image" src="https://github.com/user-attachments/assets/e13b9a37-5176-4db7-ae44-917c1d882189" />

I just restarted it and tried updating the programs ones again it and started to work. 

And now for the test part. 

I loaded all of the parts in the program. 





For one test I used git in this program because I realized I still hadn't used it. The purpose was to make the picture of the architecture and commmit it to our repo. So first I cloned the git repo to my VM. 

<img width="420" height="97" alt="image" src="https://github.com/user-attachments/assets/46a7f08d-94c0-42de-afed-c528eb312eea" />


Then I changed to my project directory from which I pushed our architecture from. 

<img width="326" height="101" alt="image" src="https://github.com/user-attachments/assets/18977a9d-a08d-4667-8d74-032995e93382" />



After a while we wanted to test the thing by an "outsider" by exporting the IDS and testing it outside our systems. 

There were few problems that occurred for example the exporting to USB stick. My stick had format of FAT32 which only can download 4GB files at a time. 

<img width="312" height="311" alt="image" src="https://github.com/user-attachments/assets/830a3867-c957-4be3-8db3-c327f854e4c7" />

I fixed that by using Minitool Partition Wizard which is a minitool for changing the sticks format. 


But after some testing we finally got the video for first user test.



## TROUBLESHOOTING:

I initially had a memory issue, as I was unable to start Elasticsearch and the VM was lagging too much. I resolved the situation by increasing my laptop's memory allocation. The recommended VM memory would be at least 8 GB.

When installing Zeek, it's worth making sure your own interface is connected to it. First run the command ip a to confirm your interface, then run sudo nano /opt/zeek/etc/node.cfg to verify what is set in Zeek.

The same interface should be checked with suricata. I can show the example on status command if the interface is wrong:

<img width="506" height="185" alt="image" src="https://github.com/user-attachments/assets/eba2efc5-708f-43c8-b35e-f97c31aea067" />

And to change it you would need to check your own interface with ip a. Mine was enp0s3 so I changed it in suricata.yml by using nano. 

You should then locate the af-packet and change the correct interface. Save the file in the process.

<img width="270" height="65" alt="image" src="https://github.com/user-attachments/assets/5d10889d-2865-4689-84d8-70c8a5820365" />

Restart suricata and check the status on it. It should be active. For more research you can use the sudo tail command and the file name on for maybe 20 lines if the problem occurs. 


While installing Wazuh - make sure that it was added to right repo. To check it you can just update the system and see what error code it gives. 

You can just use the basic: 

```
cat /etc/apt/sources.list.d/wazuh.list

```

And see if it downloaded correctly. 




## Quick start IDS with Ansible: 

First you need to download it: 
```
sudo apt install -y ansible
```
And add the text below to playbook.yml using nano. 
```
nano playbook.yml
```

```
---
- name: Start IDS Project
  hosts: localhost
  become: yes

  tasks:

    - name: Start Elasticsearch
      systemd:
        name: elasticsearch
        state: started
        enabled: yes

    - name: Wait for Elasticsearch to be ready
      wait_for:
        port: 9200
        delay: 10
        timeout: 60

    - name: Start Kibana
      systemd:
        name: kibana
        state: started
        enabled: yes

    - name: Start Suricata
      systemd:
        name: suricata
        state: started
        enabled: yes

    - name: Start Filebeat
      systemd:
        name: filebeat
        state: started
        enabled: yes

    - name: Start Wazuh Manager
      systemd:
        name: wazuh-manager
        state: started
        enabled: yes

    - name: Start Wazuh Agent
      systemd:
        name: wazuh-agent
        state: started
        enabled: yes

    - name: Start Zeek
      command: zeekctl start
      ignore_errors: yes

    - name: Check all services
      command: systemctl is-active {{ item }}
      loop:
        - elasticsearch
        - kibana
        - suricata
        - filebeat
        - wazuh-manager
      register: service_status
      ignore_errors: yes

    - name: Print service statuses
      debug:
        msg: "{{ item.item }}: {{ item.stdout }}"
      loop: "{{ service_status.results }}"




```

You can then start the IDS whenever you'd like with command below. 

```
ansible-playbook ~/'owndirectory'/playbook.yml

```

After starting the IDS. You can play your own pcap- file. Whichever you choose really.

```
sudo tcpreplay --intf1=enp0s3 --multiplier=1.0 /home/$USER/portscan.pcap
```

And enter localhost:5061 to see the Kibana dashboards. 

## Closing the IDS with Ansible:

Same as before. Attach the text below to nano. You can name it for stop.yml for example. 

```
ansible-playbook ~/'owndirectory'/stop.yml
```

```

---
- name: Stop IDS Project
  hosts: localhost
  become: yes

  tasks:
    - name: Stop all IDS services
      systemd:
        name: "{{ item }}"
        state: stopped
      loop:
        - filebeat
        - kibana
        - elasticsearch
        - suricata
        - wazuh-manager
        - wazuh-agent

    - name: Stop Zeek
      command: zeekctl stop
      ignore_errors: yes


```

## Quick status check: 

```
sudo systemctl status suricata
sudo zeekctl status
sudo systemctl status filebeat
sudo systemctl status elasticsearch
sudo systemctl status kibana
sudo systemctl status wazuh-manager
sudo systemctl status wazuh-agent
```

## USEFUL LINKS:

Damon Garn, How to use tcpreplay: https://www.techtarget.com/searchsecurity/tutorial/How-to-use-tcpreplay-to-replay-network-packet-files 

A website and framework for testing NIDS detection: testmynids.org

PCAPs for Testing: https://docs.securityonion.net/en/2.4/pcaps.html

Installing the Wazuh server step by step:  https://documentation.wazuh.com/current/installation-guide/wazuh-server/step-by-step.html 
