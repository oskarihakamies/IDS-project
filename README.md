# IDS-project | OSIRIS-T

**Project**: Real-time Intrusion Detection System (IDS) Prototype. 


**Authors**: Oskari Häkämies, Nico Saario ja Arttu Mukkula


**Period**: 1.2026-5.2026


**Purpose**: Build and demonstrate a functional open-source IDS capable of detecting known attacks, enriching logs with context (e.g. geolocation), and visualizing results in dashboards.

## Validation 15/04/2026

We were able to test this in a course with the help of Tero Karvinen that teaches the class. You can find more information here: https://terokarvinen.com/palvelinten-hallinta/

Most of the students have a little bit of knowledge / background from Linux but most of them are fairly new.

There were 18 participants that ran the whole project from scratch. 

Only pre-requirement was that they installed the Debian 13 ISO - image we sent them beforehand.

*Results*

In 40 minutes first 2 participants were able to build it from scratch and get notifications to Wazuh.

After 70 minutes the results were surprisingly good:

8 in phase 1 (downloading/installing) -> Some of them had to download the full image (missed e-mail ect.)
7 in phase 2 (trying to run the script) -> Most of the problems came with Debian ISO - image or setting it up instead of the actual project
3 in phase 3 (tcpreplay) -> All 3 had Wazuh running or set up

Overall our test was successfull. Even beginners are able to set it up in live test with only us giving help in details such as setting up your keyboard for Linux, updating ```apt/sources.list```file so that Debian is able to update

## **TO INSTALL** Follow this link to the guide: 

https://github.com/oskarihakamies/IDS-project/blob/main/How-To-Install.md


## Project Overview

This school project implements a modular Intrusion Detection System (IDS) combining signature-based and behavior-based detection. The system monitors network traffic, generates alerts, enriches data with geolocation, backs up logs, and provides visual analysis via Wazuh dashboards. 


The prototype is meant to successfully detect common attack types such as DoS, port scans, probing, and brute-force attempts using benchmark datasets and live simulations. The entire stack is also deployed with a single command using Ansible, making it reproducible and easy to set up from scratch on any Debian 13 machine.
 

## Test & Runtime Environment

(These versions are used as of 2/4/2026)

**Hypervisor**: VirtualBox 7.2.4

- **Primary OS**: Debian 13

  
VM specs: 

- RAM 8-12 GB 
- 2-4 CPU 
- Disk size +30,00 GB
- Network: NAT While installing + Internal Network for attacking
  


**Attack Simulation OS**:


- VM specs: 2-6 GB RAM, 2 vCPUs

**Test Datasets**:

- Small manual PCAP files


## Architecture


```
┌─────────────────────────────────────────────────────────┐
│                    OSIRIS-T Stack                       │
│                                                         │
│  ┌──────────┐    ┌──────────────┐    ┌───────────────┐  │
│  │ tcpreplay│    │     nmap     │    │    tshark     │  │
│  │  (PCAP)  │    │  (scanning)  │    │  (capture)    │  │
│  └────┬─────┘    └──────┬───────┘    └───────┬───────┘  │
│       └─────────────────┴───────────────────┘           │
│                         │ network packets               │
│                         ▼                               │
│              ┌───────────────────┐                      │
│              │     Suricata      │  ET Open rules       │
│              │  Signature IDS    │  40 000+ signatures  │
│              └────────┬──────────┘                      │
│                       │ /var/log/suricata/eve.json      │
│                       ▼                                 │
│              ┌───────────────────┐                      │
│              │   Wazuh Manager   │  Rule correlation    │
│              │      SIEM         │  Alert generation    │
│              └────────┬──────────┘  port 1514 / 1515    │
│                       │ /var/ossec/logs/alerts/         │
│                       ▼                                 │
│         ┌─────────────────────────┐                     │
│         │  wazuh-alerts-shipper   │  Python systemd     │
│         │    (custom Python v2)   │  Batch · Retry      │
│         │                         │  Registry · TLS     │
│         └────────────┬────────────┘                     │
│                      │ HTTPS REST /_bulk                │
│                      ▼                                  │
│         ┌─────────────────────────┐                     │
│         │     Wazuh Indexer       │  OpenSearch 2.19.4  │
│         │  (OpenSearch engine)    │  port 9200 · TLS    │
│         └────────────┬────────────┘                     │
│                      │                                  │
│                      ▼                                  │
│         ┌─────────────────────────┐                     │
│         │    Wazuh Dashboard      │  https://127.0.0.1  │
│         │     Web UI / HTTPS      │  admin / admin      │
│         └─────────────────────────┘                     │
└─────────────────────────────────────────────────────────┘   
```

---

## Tools & Technologies

| Tool | Version | Purpose |
|------|---------|---------|
| **Suricata** | 7.0.10 | Signature-based IDS, alert generation |
| **Wazuh Manager** | 4.14.4 | SIEM, log correlation, alert management |
| **Wazuh Indexer** | 4.14.4 | OpenSearch-based log storage and indexing |
| **Wazuh Dashboard** | 4.14.4 | Web UI, security event visualization |
| **wazuh-alerts-shipper** | v2 (custom) | Python shipper with TLS, registry, batch sending |
| **Ansible** | 2.x | Full automated deployment |
| **Python 3** | 3.x | Custom alert shipper runtime |
| **tcpreplay** | latest | PCAP replay for attack simulation |
| **nmap** | latest | Port scanning for test traffic |
| **tshark** | latest | Network packet capture to PCAP |
| **OpenSSL** | latest | TLS certificate generation and inspection |


---

## Ansible Deployment

The playbook deploys all components in the correct dependency order:

```
common → wazuh_indexer → wazuh_manager → wazuh_dashboard → sensors
```

---


### Role breakdown

**`common`**
- Installs baseline packages: curl, wget, gnupg, unzip, python3
- Installs test tools: tcpreplay, nmap, tshark, net-tools, openssl
- Creates `/etc/apt/keyrings/` for external repositories

**`wazuh_indexer`**
- Adds Wazuh APT repository and GPG key
- Installs wazuh-indexer (OpenSearch 2.19.4)
- Generates TLS/SSL certificates with wazuh-certs-tool.sh
- Reads admin certificate DN dynamically with openssl
- Writes opensearch.yml with correct admin_dn **before** starting indexer
- Copies securityconfig files to correct location
- Starts indexer and runs securityadmin.sh to initialize security plugin

**`wazuh_manager`**
- Installs wazuh-manager 
- Creates correct systemd service file (`Type=oneshot`)
- Fixes `MANAGER_IP` placeholder in ossec.conf automatically
- Installs and starts **wazuh-alerts-shipper v2** as systemd service

**`wazuh_dashboard`**
- Installs wazuh-dashboard
- Copies TLS certificates to dashboard directory
- Configures opensearch_dashboards.yml (port 443, SSL enabled)
- Waits for dashboard to be available on port 443

**`sensors`**
- Installs Suricata and updates ET Open rules
- Auto-detects network interface with `ansible_default_ipv4.interface`
- Sets correct permissions for Wazuh Manager to read Suricata logs
- Adds localfile configuration to Manager ossec.conf
- Note: wazuh-agent is **not** installed because it conflicts with wazuh-manager on Debian

---

## Contact


**Oskari Häkämies**



**Nico Saario**



**Arttu Mukkula**
