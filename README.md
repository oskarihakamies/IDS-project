# IDS-project | OSIRIS-T

**Project**: Real-time Intrusion Detection System (IDS) Prototype. 


**Authors**: Oskari Häkämies, Nico Saario ja Arttu Mukkula


**Period**: 1.2026-5.2026


**Purpose**: Build and demonstrate a functional open-source IDS capable of detecting known attacks, enriching logs with context (e.g. geolocation), and visualizing results in dashboards.



## Project Overview

This school project implements a modular Intrusion Detection System (IDS) combining signature-based and behavior-based detection. The system monitors network traffic, generates alerts, enriches data with geolocation, backs up logs, and provides visual analysis via Kibana dashboards.


The prototype is meant to successfully detect common attack types such as DoS, port scans, probing, and brute-force attempts using benchmark datasets and live simulations.


## Tools & Technologies Used

  
     Tool                 Purpose
|**Suricata**         | Signature-based IDS/IPS, alert generation                    
| **Zeek**            | Network Security Monitor, protocol analysis & detailed logs   
| **Filebeat**        | Log collection and forwarding to Elasticsearch               
| **Elasticsearch**   | Log storage, indexing and search engine                     
| **Kibana**          | Dashboards, visualizations, GeoIP maps, alert exploration     
| **Wazuh**           | SIEM layer, backups, alert correlation and management       
| **tcpreplay**       | PCAP replay for offline attack simulation                     
| **MaxMind GeoLite2**| Geolocation enrichment for IP addresses                        
| **Docker**          | Container management (mainly for ELK Stack & Wazuh components)



## Test & Runtime Environment

(These versions are used as of 2/4/2025)

**Hypervisor**: VirtualBox 7.2.4

- **Primary OS**: Debian 13

  
VM specs: 

- BM 4096 MB
- 2 CPU 
- Disk size 20,00 GB
- Network: Internal Network or Bridged Adapter (for traffic mirroring)



**Attack Simulation OS**:


- VM specs: 2–4 GB RAM, 2 vCPUs

**Test Datasets**:

- Small manual PCAP files


## Quick Setup Summary

1. Create Debian VM in VirtualBox → `sudo apt update && sudo apt upgrade`
2. Install Suricata, Zeek, Filebeat, ELK Stack, Wazuh (via official repositories)
3. Configure monitoring interface in `suricata.yaml` and `node.cfg` (Zeek)
4. Enable Filebeat modules: `filebeat modules enable suricata zeek`
5. Start services: `systemctl enable --now elasticsearch kibana filebeat`
6. Test traffic generation: `sudo tcpreplay --intf1=ens160 sample.pcap`
7. Access Kibana → http://localhost:5601 → create index pattern `filebeat-*` → build dashboards








## Contact


**Oskari Häkämies**



**Nico Saario**



**Arttu Mukkula**
