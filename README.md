# IDS-project | OSIRIS-T

**Project**: Real-time Intrusion Detection System (IDS) Prototype. 


**Authors**: Oskari Häkämies, Nico Saario ja Arttu Mukkula


**Period**: 1.2026-5.2026


**Purpose**: Build and demonstrate a functional open-source IDS capable of detecting known attacks, enriching logs with context (e.g. geolocation), and visualizing results in dashboards.

## **TO INSTALL** Follow this link to the guide: 

https://github.com/oskarihakamies/IDS-project/blob/main/How-To-Install.md


## Project Overview

This school project implements a modular Intrusion Detection System (IDS) combining signature-based and behavior-based detection. The system monitors network traffic, generates alerts, enriches data with geolocation, backs up logs, and provides visual analysis via Wazuh dashboards. 


The prototype is meant to successfully detect common attack types such as DoS, port scans, probing, and brute-force attempts using benchmark datasets and live simulations. The entire stack is also deployed with a single command using Ansible, making it reproducible and easy to set up from scratch on any Debian 13 machine.
 

## Test & Runtime Environment

(These versions are used as of 2/4/2025)

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








## Contact


**Oskari Häkämies**



**Nico Saario**



**Arttu Mukkula**
