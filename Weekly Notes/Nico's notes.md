## Weeks 5 & 6

I was supposed to be installing the whole idea of ours, 
test out the first working model and figure out how to Mount the ISO so that we can share it when the project is finished...

- I ran into a few problems during the installation - phase.
1st was finding the right installers for the actual software, f.ex Zeek.
After searching for a while, managed to find them. Install them, test out that they work individually.

 It was time to see if they work together but I ran into another trouble, which was with the VM itself - which is what I'm troubleshooting now with a good friend of mine, Copilot.

![onkelma](https://github.com/user-attachments/assets/378f402f-7c77-4cc7-b165-e3edebb6f638)

Could be a lack of memory.
I believe this was caused when I started to install Wazuh, and while it was installing, it got stuck and the computer decided that it's time to recharge and shut down completely.

Even the GUI won't start, so time to start debugging with CLI.

Decided not worth the time, doing a fresh install even thought I decided to do a fresh one with installation guides after this "Hello World" - would've been successful.

### Installing and QuickStart

Tcpreplay
```
sudo apt install build-essential libpcap-dev
wget https://github.com/appneta/tcpreplay/releases/download/v4.4.3/tcpreplay-4.4.3.tar.gz
tar -xzf tcpreplay-4.4.3.tar.gz
cd tcpreplay-4.4.3
./configure
make
sudo make install
```

Suricata:

```sudo apt-get install suricata```
```sudo systemctl status suricata```
<img width="743" height="352" alt="kuva" src="https://github.com/user-attachments/assets/07c8bbb7-80e3-462f-bbe6-00cf3f04ee78" />

Zeek based on this source:

https://github.com/zeek/zeek/wiki/Binary-Packages

For Debian 13:

```
echo 'deb http://download.opensuse.org/repositories/security:/zeek/Debian_13/ /' | sudo tee /etc/apt/sources.list.d/security:zeek.list
curl -fsSL https://download.opensuse.org/repositories/security:zeek/Debian_13/Release.key | gpg --dearmor | sudo tee /etc/apt/trusted.gpg.d/security_zeek.gpg > /dev/null
sudo apt update
sudo apt install zeek
```

## ELK - Stack
From [here](https://www.elastic.co/docs/deploy-manage/deploy/self-managed/install-elasticsearch-with-debian-package) Under "Download and install the Debian package manually"

Dependencies: 
```sudo apt-get install wget```
```
wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-9.3.0-amd64.deb
wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-9.3.0-amd64.deb.sha512
shasum -a 512 -c elasticsearch-9.3.0-amd64.deb.sha512
sudo dpkg -i elasticsearch-9.3.0-amd64.deb
```

### Kibana

```
wget https://artifacts.elastic.co/downloads/kibana/kibana-9.3.0-amd64.deb
shasum -a 512 kibana-9.3.0-amd64.deb
sudo dpkg -i kibana-9.3.0-amd64.deb
```

<img width="735" height="150" alt="kuva" src="https://github.com/user-attachments/assets/e78fb11f-e337-410a-9cf2-a99d06a53238" />

### Filebeat

I was a bit lost on this one for a bit but found it [here](https://www.elastic.co/docs/reference/beats/filebeat/filebeat-installation-configuration)

```
curl -L -O https://artifacts.elastic.co/downloads/beats/filebeat/filebeat-9.3.0-amd64.deb
sudo dpkg -i filebeat-9.3.0-amd64.deb
```


### Dummy-interface

- I went into this whole thing by just figuring out an alternative way to test TCPREPLAY so that I can isolate the network not to leak anything out.
- I found a thread discussing about dummy interfaces which is built into the Linux Kernel from [here](https://superuser.com/questions/1789157/black-hole-output-interface-for-tcpreplay).
- I fell into the wormhole of digging deeper, chatting with Copilot and searching online and figured this might be worth the shot.

Because it doesn't have any physical connections and Linux creates a virtual network card by using it, the packets will be dropped instead of jumping into the NIC that is actually being used. It is widely used for testing is what I gathered.

Built in, no need to install
```
sudo ip link add dummy0 type dummy
sudo ip addr add 192.0.2.0/24 dev dummy0
sudo ip link set dummy0 up
```

### Testing and setting everything up

1st the dummy which we already set up

<img width="752" height="150" alt="kuva" src="https://github.com/user-attachments/assets/30825238-6060-4471-b271-25f287be6ac4" />

Running Suricata

```sudo suricata -i dummy0 -c /etc/suricata/suricata.yaml -D```
Listens to dummy0

Zeek to listen

```sudo zeek -i dummy0```

"Command not found". Didn't somehow have it installed, let's try again

<img width="655" height="146" alt="kuva" src="https://github.com/user-attachments/assets/67324a17-7dec-4207-9f2d-5be8729df77a" />

Forgot path, so ```sudo /opt/zeek/bin/zeek -i dummy0```

Configuring Filebeat with Copilot (didn't have enough time to figure this on my own)

```
sudo nano /etc/filebeat/filebeat.yml
```
Suricata eve.json
```
filebeat.inputs:
  - type: filestream
    id: suricata-eve
    paths:
      - /var/log/suricata/eve.json
    parsers:
      - ndjson:
          target: ""
```
Zeek-logs
```
  - type: filestream
    id: zeek-logs
    paths:
      - /opt/zeek/logs/current/*.log
```

Output to Elasticsearch
```
output.elasticsearch:
  hosts: ["http://localhost:9200"]
```
Save and close.

```
sudo systemctl enable filebeat
sudo systemctl restart filebeat
sudo systemctl status filebeat
```

Didn't work, took everything out and replaced with:
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
Works!

<img width="1205" height="463" alt="kuva" src="https://github.com/user-attachments/assets/81d5ede4-b55e-483e-a3a9-e455efd55a60" />

Kibana:

<img width="530" height="206" alt="kuva" src="https://github.com/user-attachments/assets/ba39619a-d51e-4524-a8b3-3449defb6b68" />

Currently having trouble installing it. 
<img width="641" height="878" alt="kuva" src="https://github.com/user-attachments/assets/9931146b-8f15-42f6-9d20-406aac5f8d40" />

Tried force removing and purging 15 different times, no luck so far.

```curl -fsSL https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo gpg --dearmor -o /usr/share/keyrings/elastic.gpg```

```
wget https://artifacts.elastic.co/downloads/kibana/kibana-9.3.0-linux-x86_64.tar.gz
tar -xzf kibana-9.3.0-linux-x86_64.tar.gz
cd kibana-9.3.0-linux-x86_64
./bin/kibana
```
Correcting certificates:
```
sudo /usr/share/elasticsearch/bin/elasticsearch-reset-password -u elastic -i
sudo /usr/share/elasticsearch/bin/elasticsearch-create-enrollment-token -s kibana
```

I got an answer from GPT that this is ok to remove from Lab environment and should be removed since there's  a conflict between Debian 13 and Kibana/Elasticsearch

In elasticsearch.yml:

```
xpack.security.enabled: false
xpack.security.http.ssl.enabled: false
xpack.security.transport.ssl.enabled: false
```

<img width="722" height="484" alt="kuva" src="https://github.com/user-attachments/assets/e258f7d0-c7da-469f-a77c-5bf844503ce1" />
Woohooo! It works!


### Testing

```sudo tcpreplay --intf1=dummy0 test.pcap``` which I downloaded from netresec.com
<img width="1128" height="294" alt="kuva" src="https://github.com/user-attachments/assets/ed083a37-366a-426c-8702-f7a836a06ff3" />

<img width="1155" height="530" alt="kuva" src="https://github.com/user-attachments/assets/810d7269-ac66-4890-928a-897e151e2e9c" />

<img width="1145" height="149" alt="kuva" src="https://github.com/user-attachments/assets/ef7e8713-301f-4bb3-a8f9-c67c76f771bf" />

### Kibana

<img width="1902" height="933" alt="kuva" src="https://github.com/user-attachments/assets/4a6eb9ea-d38b-4e00-ae36-04b193491cd9" />

Test successfull! First demo completed.

I used 

```sudo tcpreplay --intf1=dummy0 test.pcap``` to "shoot" the pcap - file in
```sudo suricata -i dummy0 -D``` to activate Suricata (doesn't function normally when using dummy0)
```sudo /opt/zeek/bin/zeek -i dummy0```to listen on NIC dummy0
```tail f /var/log/suricata/eve.json``` Suricata eve - file
```./bin/kibana``` To start Kibana
2nd attempt:

<img width="1148" height="293" alt="kuva" src="https://github.com/user-attachments/assets/86d8b78c-f1d8-4790-a1f8-2c919da7145a" />

<img width="1709" height="945" alt="kuva" src="https://github.com/user-attachments/assets/c31b48ca-f670-446f-8e20-2153ce72a909" />


Will be ending this run 24/02/2026 1:57 AM.

Started from scratch 23/02/2026 at 9:40ish PM


# Week 9

<img width="749" height="851" alt="kuva" src="https://github.com/user-attachments/assets/f4c5320a-bdd1-47ed-a42e-2a631fbf10fa" />

This is where all the trouble is right now. After ```sudo apt-get update```kibana cannot once again find its packages. I'm once again in the loop of trying to figure out how to get it to work. This has been an issue with the whole team and we're figuring out a way to get it installed to be stable.

### Update on Kibana
I did some research, troubleshooting and figured out that the main problem was that the ELK - stack was missing its keyring-file which caused a problem each time the VM launched and tried to do the ```sudo apt-get update```

The same issue was with Wazuh

This causes somewhat a security issue where the APT is not willing to update because of safety reasons.

Command for Kibana:
```curl -fsSL https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo gpg --dearmor -o /usr/share/keyrings/elasticsearch-keyring.gpg```

It basically does this: 
- It goes and loads up the official GPG - key from online
- Loads up the key to the gpg
- Transforms the key from ASCII to binary keyring (--dearmor)
- Saves it to ```-o /usr/share/keyrings/elasticsearch-keyring.gpg```

### New machine, updates ect.

/etc/apt/sources.list
```
deb http://deb.debian.org/debian/ trixie main non-free-firmware contrib non-free
deb-src http://deb.debian.org/debian/ trixie main non-free-firmware contrib non-free

deb http://security.debian.org/debian-security trixie-security main non-free-firmware contrib non-free
deb-src http://security.debian.org/debian-security trixie-security main non-free-firmware contrib non-free

deb http://deb.debian.org/debian/ trixie-updates main non-free-firmware contrib non-free
deb-src http://deb.debian.org/debian/ trixie-updates main non-free-firmware contrib non-free
```

Getting the copy+paste to work so I don't have to search this up every single time I make a new one

"Clipboard -> Bidirectional"

```sudo apt update && sudo apt install build-essential dkms linux-headers-$(uname -r)```
Insert Guest additions - CD

```
sudo mount /dev/cdrom /mnt
sudo /mnt/VBoxLinuxAdditions.run
```

### New ERA, new plans
After a ton of research and some guidance from Tero, we decided to start figuring out Ansible. It basically functions the same as Salt but being agentless. 

The new plan:
Figure out a way to install our whole project from a Zip - file. Also by troubleshooting Kibana countless amounts of times, I figured out that there's a possibility to make our project smaller and smoother by throwing out Kibana and Elasticsearch out the window and to replace it completely with Wazuh.

What this does is narrow down the things we have to build from scratch - Wazuh can do it pretty much out of the Box. Wazuh is also more of a security platform that stores data whereas ELK is more of a data platform that can do security [source](https://news.fmisec.com/02-wazuh-vs-ossec-graylog-and-elk-stack-a-real-world-comparison)

### Ansible first test run
We start this by making a project folder. This basically creates the whole hierarchy as well

```
mkdir -p soc-project/roles/{common,wazuh_indexer,wazuh_manager,wazuh_dashboard,sensors,logging}/tasks
cd soc-project
```


<img width="560" height="122" alt="kuva" src="https://github.com/user-attachments/assets/f174ee4e-cfd3-4cb2-ad29-0edaf068bb0c" />

As you can see, the command creates the folders as described

<img width="590" height="192" alt="kuva" src="https://github.com/user-attachments/assets/0f92f729-6589-4cbf-99db-da6f98cd7d79" />

After that it's the time to do a install.sh which will be the command run by anyone that will use the project

This next part is completely created by Gemini Pro to save time and help to turn this project around

```
#!/bin/bash
# install.sh
# Purpose: Bootstrap the Debian system, install Ansible, and run the SOC deployment playbook.

# Ensure the script is run as root
if [ "$EUID" -ne 0 ]; then
  echo "Error: Please run as root (e.g., sudo bash install.sh)"
  exit 1
fi

echo "--- Updating system and installing Ansible ---"
apt-get update -y
apt-get install -y ansible git curl gnupg python3-apt

echo "--- Starting SOC environment deployment ---"
# Run the Ansible playbook locally on this machine
ansible-playbook -i inventory.ini -c local playbook.yml

echo "--- Deployment process finished! ---"
```

It needs permission ```chmod +x install.sh```

<img width="556" height="346" alt="kuva" src="https://github.com/user-attachments/assets/e7aa3775-c824-4a94-a4e0-28dbcedf7a8a" />

After that we need two files: ```inventory.ini``` that defines that the installations will run locally and ```playbook.yml``` that will complete the installation in the order that we defined.

```inventory.ini```:

```[soc_server]
localhost ansible_connection=local
```
<img width="561" height="339" alt="kuva" src="https://github.com/user-attachments/assets/bbe6f939-d107-4199-a0fa-21816a8539c8" />

playbook.yml:

```
---
- name: Deploy Native Debian SOC Environment
  hosts: soc_server
  become: yes
  
  tasks:
    - name: Update apt cache before starting
      apt:
        update_cache: yes
        cache_valid_time: 3600

  roles:
    - common
    # - wazuh_indexer
    # - wazuh_manager
    # - wazuh_dashboard
    # - sensors
    # - logging
```

Commenting will be temporary for testing. 

Then we'll create a "common" role basic tools, f.ex ```curl```and ```gnupg```that will be needed later for Wazuh and Suricata keys.

```micro roles/common/tasks/main.yml```: 

```
---
# tasks file for common role
- name: Install required baseline packages
  apt:
    name:
      - apt-transport-https
      - ca-certificates
      - curl
      - gnupg
      - unzip
      - wget
      - jq
    state: present

- name: Ensure /etc/apt/keyrings directory exists for external repositories
  file:
    path: /etc/apt/keyrings
    state: directory
    mode: '0755'
```

Time to test now so we don't have to follow every step back later:
Navigate to ```soc-project```, run ```sudo bash install.sh```
And now we wait... 

<img width="857" height="205" alt="kuva" src="https://github.com/user-attachments/assets/3db0fe8d-0521-45de-978f-64b51c6f266c" />

Seems like Ansible couldn't read our inventory.ini - file. 

I had accidentally put the name as ```invventory.ini```, let's try again:

<img width="460" height="54" alt="kuva" src="https://github.com/user-attachments/assets/64c07630-90c4-48df-9b50-37a31124c999" />

<img width="851" height="298" alt="kuva" src="https://github.com/user-attachments/assets/fb8bb214-b052-4009-9cba-75411cfee7c4" />

I feel like this has something to do with the actual file. Let's go ahead and look what te made. I have another typo there...

<img width="466" height="119" alt="kuva" src="https://github.com/user-attachments/assets/8231a2e5-17a8-4001-bc0d-a4cf74ee8d77" />

Seems like everything runs smoothly!

<img width="985" height="410" alt="kuva" src="https://github.com/user-attachments/assets/52364a52-6d8a-44ab-95e1-466f5efbbf83" />


I asked Gemini about that python error and it said that "Debian 13 uses Python 3.13 and the WARNING - message is normal. You can ignore it".

So basically what the install.sh does is it creates the foundation for our automation.
It:

- Updates ```sudo apt-get update```
- Ensures that it's run by root
- Installs Ansible, curl, gnupg, python3, git
- Installs mandatory packages such as ```apt-transport-https and ca-certificates```
- Defines where and how the keyrings will be saved --> ```/etc/apt/keyrings```
- Tells us what went well, what went wrong

### Ansible + Wazuh

I basically know how to read this and work it out but again in order to save my precious time, I will ask Gemini help with setting the files

```roles/wazuh_indexer/tasks/main.yml```:

```
---
# tasks file for wazuh_indexer

- name: Download Wazuh GPG key
  get_url:
    url: https://packages.wazuh.com/key/GPG-KEY-WAZUH
    dest: /tmp/GPG-KEY-WAZUH

- name: Import Wazuh GPG key
  command: gpg --no-default-keyring --keyring gnupg-ring:/etc/apt/keyrings/wazuh.gpg --import /tmp/GPG-KEY-WAZUH
  args:
    creates: /etc/apt/keyrings/wazuh.gpg

- name: Set permissions on Wazuh GPG key
  file:
    path: /etc/apt/keyrings/wazuh.gpg
    mode: '0644'

- name: Add Wazuh APT repository
  apt_repository:
    repo: "deb [signed-by=/etc/apt/keyrings/wazuh.gpg] https://packages.wazuh.com/4.x/apt/ stable main"
    state: present
    filename: wazuh

- name: Install wazuh-indexer package
  apt:
    name: wazuh-indexer
    state: present
    update_cache: yes

- name: Create configuration for certificate generation
  copy:
    dest: /tmp/config.yml
    content: |
      nodes:
        indexer:
          - name: node-1
            ip: "127.0.0.1"
        server:
          - name: wazuh-1
            ip: "127.0.0.1"
        dashboard:
          - name: dashboard
            ip: "127.0.0.1"

- name: Download Wazuh certificate generation tool
  get_url:
    url: https://packages.wazuh.com/4.9/wazuh-certs-tool.sh
    dest: /tmp/wazuh-certs-tool.sh
    mode: '0755'

- name: Generate SSL/TLS certificates for the entire SOC stack
  command: bash /tmp/wazuh-certs-tool.sh -A
  args:
    creates: /tmp/wazuh-certificates/node-1.pem

- name: Copy indexer certificates to the correct directory
  copy:
    src: "/tmp/wazuh-certificates/{{ item }}"
    dest: "/etc/wazuh-indexer/certs/{{ item }}"
    remote_src: yes
    owner: wazuh-indexer
    group: wazuh-indexer
    mode: '0400'
  loop:
    - node-1.pem
    - node-1-key.pem
    - admin.pem
    - admin-key.pem
    - root-ca.pem

- name: Enable and start wazuh-indexer service
  systemd:
    name: wazuh-indexer
    enabled: yes
    state: started

- name: Wait for Wazuh Indexer to start (can take up to a minute)
  wait_for:
    port: 9200
    delay: 10
    timeout: 120

- name: Initialize Wazuh Indexer security (Admin passwords)
  command: /usr/share/wazuh-indexer/bin/indexer-security-init.sh
  environment:
    JAVA_HOME: /usr/share/wazuh-indexer/jdk
  args:
    creates: /var/wazuh-indexer-security-initialized.flag

- name: Mark security initialization as complete
  file:
    path: /var/wazuh-indexer-security-initialized.flag
    state: touch
  ```

Then we move to activate the previously commented lines from "playbook.yml"

<img width="984" height="340" alt="kuva" src="https://github.com/user-attachments/assets/a9044c43-a5c4-4441-8588-d6ce5d6fe7b7" />

I also wanted to see that not running root worked :)

<img width="351" height="32" alt="kuva" src="https://github.com/user-attachments/assets/8ac63144-0f6c-45b6-854e-bb0dab8d1a3e" />

No need to be a professor to see what went wrong:

<img width="988" height="303" alt="kuva" src="https://github.com/user-attachments/assets/9b01ec06-f1fe-4688-8071-b3eade4272d7" />

By deleting the comments, I think I accidentally changed the YAML syntax which causes the errors..

I will also do a couple of changes so that it doesn't start to panic:

<img width="593" height="323" alt="kuva" src="https://github.com/user-attachments/assets/79c43d7c-10f4-4681-9a39-7988f8631f24" />

NOTE: This will possible take minutes to run. Mine took 4 minutes and 12 seconds and failed again

I feel like this error is caused by a missing directory? <img width="1101" height="227" alt="kuva" src="https://github.com/user-attachments/assets/13ad2ac1-f559-4794-a4f8-86c01926a3bd" />

<img width="691" height="46" alt="kuva" src="https://github.com/user-attachments/assets/81038c05-dd35-475d-a81b-82ff6b3e73ee" />

So now what we wan't to do is actually not change it manually but to use the script in order-to-get it to work for the final product

We will change the code to ensure that the directory exists:

<img width="633" height="377" alt="kuva" src="https://github.com/user-attachments/assets/1daaea37-bbf9-48cb-aa40-762a390871c5" />

Note: Ansible is great, because it remembers what it already did so it continues straight from where we left :)

Final code in ```main.yml```

```
---
# tasks file for wazuh_indexer

- name: Download Wazuh GPG key
  get_url:
    url: https://packages.wazuh.com/key/GPG-KEY-WAZUH
    dest: /tmp/GPG-KEY-WAZUH

- name: Import Wazuh GPG key
  command: gpg --no-default-keyring --keyring gnupg-ring:/etc/apt/keyrings/wazuh.gpg --import /tmp/GPG-KEY-WAZUH
  args:
    creates: /etc/apt/keyrings/wazuh.gpg

- name: Set permissions on Wazuh GPG key
  file:
    path: /etc/apt/keyrings/wazuh.gpg
    mode: '0644'

- name: Add Wazuh APT repository
  apt_repository:
    repo: "deb [signed-by=/etc/apt/keyrings/wazuh.gpg] https://packages.wazuh.com/4.x/apt/ stable main"
    state: present
    filename: wazuh

- name: Install wazuh-indexer package
  apt:
    name: wazuh-indexer
    state: present
    update_cache: yes

- name: Create configuration for certificate generation
  copy:
    dest: /tmp/config.yml
    content: |
      nodes:
        indexer:
          - name: node-1
            ip: "127.0.0.1"
        server:
          - name: wazuh-1
            ip: "127.0.0.1"
        dashboard:
          - name: dashboard
            ip: "127.0.0.1"

- name: Download Wazuh certificate generation tool
  get_url:
    url: https://packages.wazuh.com/4.9/wazuh-certs-tool.sh
    dest: /tmp/wazuh-certs-tool.sh
    mode: '0755'

- name: Generate SSL/TLS certificates for the entire SOC stack
  command: bash /tmp/wazuh-certs-tool.sh -A
  args:
    creates: /tmp/wazuh-certificates/node-1.pem

- name: Ensure certificate directory exists
  file:
    path: /etc/wazuh-indexer/certs
    state: directory
    owner: wazuh-indexer
    group: wazuh-indexer
    mode: '0500'


- name: Copy indexer certificates to the correct directory
  copy:
    src: "/tmp/wazuh-certificates/{{ item }}"
    dest: "/etc/wazuh-indexer/certs/{{ item }}"
    remote_src: yes
    owner: wazuh-indexer
    group: wazuh-indexer
    mode: '0400'
  loop:
    - node-1.pem
    - node-1-key.pem
    - admin.pem
    - admin-key.pem
    - root-ca.pem

- name: Enable and start wazuh-indexer service
  systemd:
    name: wazuh-indexer
    enabled: yes
    state: started

- name: Wait for Wazuh Indexer to start (can take up to a minute)
  wait_for:
    port: 9200
    delay: 10
    timeout: 120

- name: Initialize Wazuh Indexer security (Admin passwords)
  command: /usr/share/wazuh-indexer/bin/indexer-security-init.sh
  environment:
    JAVA_HOME: /usr/share/wazuh-indexer/jdk
  args:
    creates: /var/wazuh-indexer-security-initialized.flag

- name: Mark security initialization as complete
  file:
    path: /var/wazuh-indexer-security-initialized.flag
    state: touch
```

I had no idea what this was, had to ask once again from Gemini.

<img width="1269" height="195" alt="kuva" src="https://github.com/user-attachments/assets/08217994-b736-444f-89e6-2959af8e6ffb" />

We need to do some sort of a sorcery for modifying the "vm.max_map_count" to make it so that the OS is able to handle more rows of logs. It also decided to programm the database brains ```opensearch.yml```. One key point is that now it monitors all the network cards, not just in local network ```network.host: 0.0.0.0```.

As for short: The first mission is to make sure the OS doesn't suffocate the database and the other task ties the database to use the secret keys we helped it to create previously.

So we add this to the main.yml - file:

```- name: Configure kernel parameters for Wazuh Indexer (vm.max_map_count)
  sysctl:
    name: vm.max_map_count
    value: '262144'
    state: present
    reload: yes

- name: Configure Wazuh Indexer settings
  copy:
    dest: /etc/wazuh-indexer/opensearch.yml
    owner: wazuh-indexer
    group: wazuh-indexer
    mode: '0660'
    content: |
      network.host: 0.0.0.0
      node.name: "node-1"
      cluster.initial_master_nodes:
        - "node-1"
      cluster.name: "wazuh-cluster"
      path.data: /var/lib/wazuh-indexer
      path.logs: /var/log/wazuh-indexer
      plugins.security.ssl.transport.pemcert_filepath: /etc/wazuh-indexer/certs/node-1.pem
      plugins.security.ssl.transport.pemkey_filepath: /etc/wazuh-indexer/certs/node-1-key.pem
      plugins.security.ssl.transport.pemtrustedcas_filepath: /etc/wazuh-indexer/certs/root-ca.pem
      plugins.security.ssl.transport.enforce_hostname_verification: false
      plugins.security.ssl.http.enabled: true
      plugins.security.ssl.http.pemcert_filepath: /etc/wazuh-indexer/certs/node-1.pem
      plugins.security.ssl.http.pemkey_filepath: /etc/wazuh-indexer/certs/node-1-key.pem
      plugins.security.ssl.http.pemtrustedcas_filepath: /etc/wazuh-indexer/certs/root-ca.pem
      plugins.security.allow_unsafe_democertificates: false
      plugins.security.allow_default_init_securityindex: true
      plugins.security.authcz.admin_dn:
        - "CN=admin,OU=Wazuh,O=Wazuh,L=California,C=US"
      plugins.security.nodes_dn:
        - "CN=node-1,OU=Wazuh,O=Wazuh,L=California,C=US"
```

Complete yml:

```---
# tasks file for wazuh_indexer

- name: Download Wazuh GPG key
  get_url:
    url: https://packages.wazuh.com/key/GPG-KEY-WAZUH
    dest: /tmp/GPG-KEY-WAZUH

- name: Import Wazuh GPG key
  command: gpg --no-default-keyring --keyring gnupg-ring:/etc/apt/keyrings/wazuh.gpg --import /tmp/GPG-KEY-WAZUH
  args:
    creates: /etc/apt/keyrings/wazuh.gpg

- name: Set permissions on Wazuh GPG key
  file:
    path: /etc/apt/keyrings/wazuh.gpg
    mode: '0644'

- name: Add Wazuh APT repository
  apt_repository:
    repo: "deb [signed-by=/etc/apt/keyrings/wazuh.gpg] https://packages.wazuh.com/4.x/apt/ stable main"
    state: present
    filename: wazuh

- name: Install wazuh-indexer package
  apt:
    name: wazuh-indexer
    state: present
    update_cache: yes

- name: Create configuration for certificate generation
  copy:
    dest: /tmp/config.yml
    content: |
      nodes:
        indexer:
          - name: node-1
            ip: "127.0.0.1"
        server:
          - name: wazuh-1
            ip: "127.0.0.1"
        dashboard:
          - name: dashboard
            ip: "127.0.0.1"

- name: Download Wazuh certificate generation tool
  get_url:
    url: https://packages.wazuh.com/4.9/wazuh-certs-tool.sh
    dest: /tmp/wazuh-certs-tool.sh
    mode: '0755'

- name: Generate SSL/TLS certificates for the entire SOC stack
  command: bash /tmp/wazuh-certs-tool.sh -A
  args:
    creates: /tmp/wazuh-certificates/node-1.pem

- name: Ensure certificate directory exists
  file:
    path: /etc/wazuh-indexer/certs
    state: directory
    owner: wazuh-indexer
    group: wazuh-indexer
    mode: '0500'


- name: Copy indexer certificates to the correct directory
  copy:
    src: "/tmp/wazuh-certificates/{{ item }}"
    dest: "/etc/wazuh-indexer/certs/{{ item }}"
    remote_src: yes
    owner: wazuh-indexer
    group: wazuh-indexer
    mode: '0400'
  loop:
    - node-1.pem
    - node-1-key.pem
    - admin.pem
    - admin-key.pem
    - root-ca.pem

- name: Configure kernel parameters for Wazuh Indexer (vm.max_map_count)
  sysctl:
    name: vm.max_map_count
    value: '262144'
    state: present
    reload: yes

- name: Configure Wazuh Indexer settings
  copy:
    dest: /etc/wazuh-indexer/opensearch.yml
    owner: wazuh-indexer
    group: wazuh-indexer
    mode: '0660'
    content: |
      network.host: 0.0.0.0
      node.name: "node-1"
      cluster.initial_master_nodes:
        - "node-1"
      cluster.name: "wazuh-cluster"
      path.data: /var/lib/wazuh-indexer
      path.logs: /var/log/wazuh-indexer
      plugins.security.ssl.transport.pemcert_filepath: /etc/wazuh-indexer/certs/node-1.pem
      plugins.security.ssl.transport.pemkey_filepath: /etc/wazuh-indexer/certs/node-1-key.pem
      plugins.security.ssl.transport.pemtrustedcas_filepath: /etc/wazuh-indexer/certs/root-ca.pem
      plugins.security.ssl.transport.enforce_hostname_verification: false
      plugins.security.ssl.http.enabled: true
      plugins.security.ssl.http.pemcert_filepath: /etc/wazuh-indexer/certs/node-1.pem
      plugins.security.ssl.http.pemkey_filepath: /etc/wazuh-indexer/certs/node-1-key.pem
      plugins.security.ssl.http.pemtrustedcas_filepath: /etc/wazuh-indexer/certs/root-ca.pem
      plugins.security.allow_unsafe_democertificates: false
      plugins.security.allow_default_init_securityindex: true
      plugins.security.authcz.admin_dn:
        - "CN=admin,OU=Wazuh,O=Wazuh,L=California,C=US"
      plugins.security.nodes_dn:
        - "CN=node-1,OU=Wazuh,O=Wazuh,L=California,C=US"

- name: Enable and start wazuh-indexer service
  systemd:
    name: wazuh-indexer
    enabled: yes
    state: started

- name: Wait for Wazuh Indexer to start (can take up to a minute)
  wait_for:
    port: 9200
    delay: 10
    timeout: 120

- name: Initialize Wazuh Indexer security (Admin passwords)
  command: /usr/share/wazuh-indexer/bin/indexer-security-init.sh
  environment:
    JAVA_HOME: /usr/share/wazuh-indexer/jdk
  args:
    creates: /var/wazuh-indexer-security-initialized.flag

- name: Mark security initialization as complete
  file:
    path: /var/wazuh-indexer-security-initialized.flag
    state: touch
```

Test it again by ```sudo bash install.sh```

Woohoo! Gotta appreciate how decently accurate the AIs have become lately in troubleshooting. Like 4 - 6 months ago I would've been in a massive loop modifying the code over and over and over again.

Even Gemini decided to celebrate:

<img width="717" height="96" alt="kuva" src="https://github.com/user-attachments/assets/2cade52a-2fbe-4ff5-9492-b1598d89ffd6" />
<img width="596" height="87" alt="kuva" src="https://github.com/user-attachments/assets/2d7248dd-fa9a-4723-9f48-580bf6f0239a" />

<img width="1271" height="759" alt="kuva" src="https://github.com/user-attachments/assets/963c955e-d038-4386-ad4d-b0c24f5d1e5d" />

So now we have the database Wazuh Indexer set up. This was hopefully the hardest part.

### Wazuh Manager

Next up we will create the installing code to wazuh_manager/tasks/

```
---
# tasks file for wazuh_manager

- name: Install wazuh-manager package
  apt:
    name: wazuh-manager
    state: present
    update_cache: yes

- name: Enable and start wazuh-manager service
  systemd:
    name: wazuh-manager
    enabled: yes
    state: started
```

It just installs and starts at the background! How simple and sweet. Now we modify once again the ```playbook.yml```. I decided to do this manually instead of taking out the comments, idk why but got scared because of the YAML - language once again.

<img width="472" height="255" alt="kuva" src="https://github.com/user-attachments/assets/a023d209-eea3-432f-9008-cc8b45362d22" />

Now we run it once again and see how or if it works. Be aware, this might take a while to progress. For me it took roughly 4 minutes.

We once again see what it does. Don't mind the ^[[B^[[A]. I tested wether it crashed or not.

<img width="1775" height="870" alt="kuva" src="https://github.com/user-attachments/assets/db5edd96-c345-45ef-b67a-9527fc86ad4a" />

It works! Onto the next part - The Mail Delivery - Guy.

### Filebeat - The Mail Delivery Guy

So we have Wazuh Indexer as the database on Wazuh Manager as the brains. To get the alerts to work with each other and carry them in real time and safely into the database, we need the Filebeat in order to work. This is pretty straightforward:

```roles/wazuh_manager/tasks/main.yml```: Complete code

```
---
# tasks file for wazuh_manager

- name: Install wazuh-manager package
  apt:
    name: wazuh-manager
    state: present
    update_cache: yes

- name: Enable and start wazuh-manager service
  systemd:
    name: wazuh-manager
    enabled: yes
    state: started

- name: Install filebeat package
  apt:
    name: filebeat
    state: present

- name: Download Wazuh Filebeat configuration
  get_url:
    url: https://packages.wazuh.com/4.9/tpl/wazuh/filebeat/filebeat.yml
    dest: /etc/filebeat/filebeat.yml
    mode: '0640'

- name: Download Wazuh Filebeat module
  unarchive:
    src: https://packages.wazuh.com/4.x/filebeat/wazuh-filebeat-0.3.tar.gz
    dest: /usr/share/filebeat/module
    remote_src: yes

- name: Ensure Filebeat certs directory exists
  file:
    path: /etc/filebeat/certs
    state: directory
    mode: '0500'

- name: Copy certificates for Filebeat to connect to Indexer
  copy:
    src: "/tmp/wazuh-certificates/{{ item }}"
    dest: "/etc/filebeat/certs/{{ item }}"
    remote_src: yes
    mode: '0400'
  loop:
    - node-1.pem
    - node-1-key.pem
    - root-ca.pem

- name: Configure Filebeat certificates in filebeat.yml
  replace:
    path: /etc/filebeat/filebeat.yml
    regexp: '{{ item.regexp }}'
    replace: '{{ item.replace }}'
  loop:
    - { regexp: '/etc/filebeat/certs/wazuh-server.pem', replace: '/etc/filebeat/certs/node-1.pem' }
    - { regexp: '/etc/filebeat/certs/wazuh-server-key.pem', replace: '/etc/filebeat/certs/node-1-key.pem' }

- name: Create Filebeat keystore for Indexer password
  command: filebeat keystore create
  args:
    creates: /var/lib/filebeat/filebeat.keystore

- name: Add default admin password to Filebeat keystore
  shell: echo "admin" | filebeat keystore add password --stdin --force

- name: Enable and start filebeat service
  systemd:
    name: filebeat
    enabled: yes
    state: started
  ```

Why do we do it this way? Isn't it troublesome to go over and over again to change a file?
- Yes, but it's less troubleshooting if something goes wrong.
- This code goes ahead and gets Filebeat, copies the secret keys and the Indexer let's Filebeat in. It also tells the settings that we have it named node-1.pem and not the default
- It also saves the password to Filebeats own vault with pass admin so it doesn't have to be written in normal language. Normally we wouldn't use it, but as this is a school project and not yet in production, all good.

It works!

<img width="1408" height="855" alt="kuva" src="https://github.com/user-attachments/assets/97293b31-ff6a-4984-a62c-b12bfc8194da" />

### Wazuh Dashboard

The next part is for the Wazuh dashboard to replace kibana

```roles/wazuh_dashboard/tasks/main.yml```:

```
---
# tasks file for wazuh_dashboard

- name: Install wazuh-dashboard package
  apt:
    name: wazuh-dashboard
    state: present
    update_cache: yes

- name: Ensure Dashboard certs directory exists
  file:
    path: /etc/wazuh-dashboard/certs
    state: directory
    owner: wazuh-dashboard
    group: wazuh-dashboard
    mode: '0500'

- name: Copy certificates for Dashboard
  copy:
    src: "/tmp/wazuh-certificates/{{ item }}"
    dest: "/etc/wazuh-dashboard/certs/{{ item }}"
    remote_src: yes
    owner: wazuh-dashboard
    group: wazuh-dashboard
    mode: '0400'
  loop:
    - dashboard.pem
    - dashboard-key.pem
    - root-ca.pem

- name: Configure Wazuh Dashboard settings
  copy:
    dest: /etc/wazuh-dashboard/opensearch_dashboards.yml
    owner: wazuh-dashboard
    group: wazuh-dashboard
    mode: '0640'
    content: |
      server.host: 0.0.0.0
      server.port: 443
      opensearch.hosts: https://127.0.0.1:9200
      opensearch.ssl.verificationMode: certificate
      server.ssl.enabled: true
      server.ssl.certificate: /etc/wazuh-dashboard/certs/dashboard.pem
      server.ssl.key: /etc/wazuh-dashboard/certs/dashboard-key.pem
      opensearch.ssl.certificateAuthorities: [ "/etc/wazuh-dashboard/certs/root-ca.pem" ]
      uiSettings.overrides.defaultRoute: /app/wazuh

- name: Enable and start wazuh-dashboard service
  systemd:
    name: wazuh-dashboard
    enabled: yes
    state: started
  ```

And once again ```micro playbook.yml```

<img width="1117" height="582" alt="kuva" src="https://github.com/user-attachments/assets/a139e9dc-cdcc-45a2-a9b6-aafa4af6fd2d" />

This might take a while for everything to start up. Patience is the key to victory.

<img width="815" height="330" alt="kuva" src="https://github.com/user-attachments/assets/29df1de7-e52b-4fc0-a325-40613b71c939" />

I went into the search bar, typed in "localhost", went in -> user and pass ad

It didn't launch. went into ```localhost/app/home/``` and now it works! Need to test this to see if it works later down the road.

<img width="1204" height="754" alt="kuva" src="https://github.com/user-attachments/assets/3c87306a-838b-410e-b5e6-02a0194709d7" />

<img width="1909" height="1086" alt="kuva" src="https://github.com/user-attachments/assets/f389b3b2-3336-4ade-b43c-ee56d004a72c" />


### Suricata

Now that Wazuh runs smoothly as a butterfly, we need to go ahead and tell Wazuh Manager to read what Suricata keeps on noticing. 

This is the sensor part of our operations

```roles/sensors/tasks/main.yml```:

```
---
# tasks file for sensors (Suricata)

- name: Install Suricata package
  apt:
    name: suricata
    state: present
    update_cache: yes

- name: Set correct network interface in Suricata config
  replace:
    path: /etc/suricata/suricata.yaml
    regexp: 'interface: eth0'
    replace: 'interface: {{ ansible_default_ipv4.interface }}'

- name: Set correct network interface in Suricata default file (Debian)
  lineinfile:
    path: /etc/default/suricata
    regexp: '^IFACE='
    line: 'IFACE={{ ansible_default_ipv4.interface }}'

- name: Ensure Suricata service is enabled and running
  systemd:
    name: suricata
    enabled: yes
    state: started

- name: Configure Wazuh Manager to read Suricata logs
  blockinfile:
    path: /var/ossec/etc/ossec.conf
    marker: "  "
    insertbefore: "</ossec_config>"
    content: |
      <localfile>
        <log_format>json</log_format>
        <location>/var/log/suricata/eve.json</location>
      </localfile>

- name: Restart Wazuh Manager to apply new configuration
  systemd:
    name: wazuh-manager
    state: restarted
```

It basically installs it, makes sure that Suricata is up and running and kicks Wazuh Manager to get it to understand that something has changed.

Once again -> playbook.yml and add the sensors

Ran into another issue, but fixed it in the code ABOVE! As we configured in the earlier parts, Suricata needs a network card to follow. We told it to follow with Ansibles default ipv4 interface.

<img width="1028" height="554" alt="kuva" src="https://github.com/user-attachments/assets/a2caafad-1983-4a46-8846-be39b76f3ae3" 
 />


Now it works :)

<img width="985" height="408" alt="kuva" src="https://github.com/user-attachments/assets/7a19f7b6-9320-4aa6-a626-02aebf2112d3" />

I tried this test site ```curl http://testmynids.org/uid/index.html```which is used for testing out f.ex Suricata. It didn't show up in Wazuh, decided to try and look at Suricata logs. Figured out Suricata noticed it -> Went ahead and updated Suricata. Now putting it back online

Didn't work, but everything else seems to be running fine...

<img width="1028" height="395" alt="kuva" src="https://github.com/user-attachments/assets/725fce38-851d-4ae0-ab98-865bbc40e769" />

I thought everything works, but is Filebeat on?

<img width="405" height="135" alt="kuva" src="https://github.com/user-attachments/assets/da18db2e-42ec-48df-92e9-332d6b1c8b89" />

Got a WARN secure connection disabled... It only likes HTPPS?

Did a bit of offline - troubleshooting.. Problem was with Filebeat not liking HTTPS. When we installed Suricata, it did something to Filebeat that caused the error. While I fix these, I also fix the homepage not working


wazuh_manager/tasks/main.yml:

```
---
# Filebeat-asennus ja konfigurointi
- name: Install Filebeat package
  apt:
    name: filebeat
    state: present

- name: Download official Wazuh Filebeat template
  get_url:
    url: https://packages.wazuh.com/4.9/tpl/wazuh/filebeat/filebeat.yml
    dest: /etc/filebeat/filebeat.yml
    force: yes

- name: Apply Wazuh specific configurations to Filebeat
  replace:
    path: /etc/filebeat/filebeat.yml
    regexp: "{{ item.regexp }}"
    replace: "{{ item.replace }}"
  with_items:
    # Pakotetaan HTTPS ja localhost osoite
    - { regexp: 'hosts: \["127.0.0.1:9200"\]', replace: 'hosts: ["https://127.0.0.1:9200"]' }
    # Korjataan sertifikaatit (node-1 on meidän käyttämä nimi)
    - { regexp: 'wazuh-server.pem', replace: 'node-1.pem' }
    - { regexp: 'wazuh-server-key.pem', replace: 'node-1-key.pem' }
    # Korjataan autentikointi
    - { regexp: 'username: \$\{username\}', replace: 'username: "admin"' }
    - { regexp: 'password: \$\{password\}', replace: 'password: "admin"' }
    # Varmistetaan SSL-varmennuksen asetukset (hyödyllinen POC-ympäristössä)
    - { regexp: 'ssl.verification_mode: "full"', replace: 'ssl.verification_mode: "none"' }

- name: Restart and enable Filebeat
  systemd:
    name: filebeat
    state: restarted
    enabled: yes
```

wazuh_dashboard:

```---
---
# tasks file for wazuh_dashboard

- name: Install wazuh-dashboard package
  apt:
    name: wazuh-dashboard
    state: present
    update_cache: yes

- name: Ensure Dashboard certs directory exists
  file:
    path: /etc/wazuh-dashboard/certs
    state: directory
    owner: wazuh-dashboard
    group: wazuh-dashboard
    mode: '0500'

- name: Copy certificates for Dashboard
  copy:
    src: "/tmp/wazuh-certificates/{{ item }}"
    dest: "/etc/wazuh-dashboard/certs/{{ item }}"
    remote_src: yes
    owner: wazuh-dashboard
    group: wazuh-dashboard
    mode: '0400'
  loop:
    - dashboard.pem
    - dashboard-key.pem
    - root-ca.pem

- name: Configure Wazuh Dashboard settings
  copy:
    dest: /etc/wazuh-dashboard/opensearch_dashboards.yml
    owner: wazuh-dashboard
    group: wazuh-dashboard
    mode: '0640'
    content: |
      server.host: 0.0.0.0
      server.port: 443
      opensearch.hosts: https://127.0.0.1:9200
      opensearch.ssl.verificationMode: certificate
      server.ssl.enabled: true
      server.ssl.certificate: /etc/wazuh-dashboard/certs/dashboard.pem
      server.ssl.key: /etc/wazuh-dashboard/certs/dashboard-key.pem
      opensearch.ssl.certificateAuthorities: [ "/etc/wazuh-dashboard/certs/root-ca.pem" ]
      uiSettings.overrides.defaultRoute: /app/wazuh

- name: Enable and start wazuh-dashboard service
  systemd:
    name: wazuh-dashboard
    enabled: yes
    state: started
```

sensors:

```
---
# tasks file for sensors (Suricata)

- name: Install Suricata package
  apt:
    name: suricata
    state: present
    update_cache: yes

- name: Update Suricata rules
  command: suricata-update
  register: rule_update
  changed_when: "'No changes' not in rule_update.stdout"
  # Ignore errors if no internet connection is available
  ignore_errors: yes

- name: Set correct network interface in Suricata config
  replace:
    path: /etc/suricata/suricata.yaml
    # Regex to find 'interface: ' followed by any word (like eth0)
    regexp: 'interface: \w+'
    replace: "interface: {{ ansible_default_ipv4.interface }}"

- name: Set correct network interface in Suricata default file (Debian)
  lineinfile:
    path: /etc/default/suricata
    regexp: '^IFACE='
    line: "IFACE={{ ansible_default_ipv4.interface }}"

- name: Ensure Suricata log directory has correct permissions
  file:
    path: /var/log/suricata
    state: directory
    # Directories need 0755 so Wazuh can enter them (execution bit)
    mode: '0755'
    owner: root
    group: root

- name: Ensure eve.json exists with correct permissions
  file:
    path: /var/log/suricata/eve.json
    state: touch
    mode: '0644'
    owner: root
    group: root

- name: Ensure Suricata service is enabled and running
  systemd:
    name: suricata
    enabled: yes
    state: restarted

- name: Configure Wazuh Manager to read Suricata logs
  blockinfile:
    path: /var/ossec/etc/ossec.conf
    # Use a clear marker so Ansible doesn't get confused on multiple runs
    marker: ""
    insertbefore: "</ossec_config>"
    content: |
      <localfile>
        <log_format>json</log_format>
        <location>/var/log/suricata/eve.json</location>
      </localfile>

- name: Restart Wazuh Manager to apply new configuration
  systemd:
    name: wazuh-manager
    state: restarted
```

Everything seems to work, but still no alerts... Need to keep troubleshooting

Update: Mail Delivery guy has not recieved enough instructions - let's give it some. This adds a guidebook for Filebeat to understand to talk to Wazuh.

wazuh_manager:

```
---
# tasks file for wazuh_manager

- name: Install wazuh-manager package
  apt:
    name: wazuh-manager
    state: present
    update_cache: yes

- name: Enable and start wazuh-manager service
  systemd:
    name: wazuh-manager
    enabled: yes
    state: started

- name: Install filebeat package
  apt:
    name: filebeat
    state: present

- name: Download Wazuh Filebeat configuration
  get_url:
    url: https://packages.wazuh.com/4.9/tpl/wazuh/filebeat/filebeat.yml
    dest: /etc/filebeat/filebeat.yml
    mode: '0640'

# Fixed to version 0.3
- name: Download and extract Wazuh Filebeat module
  unarchive:
    src: https://packages.wazuh.com/4.x/filebeat/wazuh-filebeat-0.3.tar.gz
    dest: /usr/share/filebeat/module
    remote_src: yes

- name: Ensure Filebeat certs directory exists
  file:
    path: /etc/filebeat/certs
    state: directory
    mode: '0500'

- name: Copy certificates for Filebeat
  copy:
    src: "/tmp/wazuh-certificates/{{ item }}"
    dest: "/etc/filebeat/certs/{{ item }}"
    remote_src: yes
    mode: '0400'
  loop:
    - node-1.pem
    - node-1-key.pem
    - root-ca.pem

- name: Apply configuration changes to filebeat.yml
  replace:
    path: /etc/filebeat/filebeat.yml
    regexp: "{{ item.regexp }}"
    replace: "{{ item.replace }}"
  loop:
    - { regexp: 'hosts: \["127.0.0.1:9200"\]', replace: 'hosts: ["https://127.0.0.1:9200"]' }
    - { regexp: 'wazuh-server.pem', replace: 'node-1.pem' }
    - { regexp: 'wazuh-server-key.pem', replace: 'node-1-key.pem' }
    - { regexp: 'ssl.verification_mode: "full"', replace: 'ssl.verification_mode: "none"' }
    - { regexp: 'username: \$\{username\}', replace: 'username: "admin"' }
    - { regexp: 'password: \$\{password\}', replace: 'password: "admin"' }

- name: Run Filebeat setup
  command: "filebeat setup {{ item }} -E output.elasticsearch.ssl.verification_mode=none"
  loop:
    - "--pipelines"
    - "--index-management"
  register: filebeat_setup
  changed_when: false
  until: filebeat_setup is succeeded
  retries: 5
  delay: 10

- name: Restart and enable Filebeat
  systemd:
    name: filebeat
    state: restarted
    enabled: yes
```

<img width="752" height="575" alt="kuva" src="https://github.com/user-attachments/assets/981327fb-38ea-40dd-a1d6-3648bb31e6ef" />

After 7 hours of troubleshooting... Suricata sees it... Does Wazuh?

<img width="1332" height="607" alt="kuva" src="https://github.com/user-attachments/assets/09e5d00e-927b-48e0-aa16-8eb93e7a0871" />

Wazuh works and gets info from Suricata but yet doesn't show anything on dashboard...

<img width="1331" height="549" alt="kuva" src="https://github.com/user-attachments/assets/43282985-489d-4cb0-a047-0237dfd76db8" />

I did a test with filebeat+suricata+testsite to see if wazuh sees anything.. It just drops them all. 

```
krotti@ansibel:~/soc-project$ sudo tail -f /var/ossec/logs/ossec.log [sudo] password for krotti: 2026/03/30 18:40:37 wazuh-analysisd: ERROR: Too many fields for JSON decoder. 2026/03/30 18:40:37 wazuh-analysisd: ERROR: Too many fields for JSON decoder. 2026/03/30 18:40:37 wazuh-analysisd: ERROR: Too many fields for JSON decoder. 2026/03/30 18:40:37 wazuh-analysisd: ERROR: Too many fields for JSON decoder. 2026/03/30 18:40:37 wazuh-analysisd: ERROR: Too many fields for JSON decoder. 2026/03/30 18:40:37 wazuh-analysisd: ERROR: Too many fields for JSON decoder. 2026/03/30 18:40:37 wazuh-analysisd: ERROR: Too many fields for JSON decoder. 2026/03/30 18:40:37 wazuh-analysisd: ERROR: Too many fields for JSON decoder. 2026/03/30 18:40:37 wazuh-analysisd: ERROR: Too many fields for JSON decoder. 2026/03/30 18:40:37 wazuh-analysisd: ERROR: Too many fields for JSON decoder.
```




