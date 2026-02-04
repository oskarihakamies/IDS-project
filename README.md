# IDS-project | OSIRIS-T

**Project**: Real-time Intrusion Detection System (IDS) Prototype. 


**Author**: Oskari Häkämies, Nico Saario ja Arttu Mukkula


**Period**: 1.2026-5.2026


**Purpose**: Build and demonstrate a functional open-source IDS capable of detecting known attacks, enriching logs with context (e.g. geolocation), and visualizing results in dashboards.



## Project Overview

This school project implements a modular Intrusion Detection System (IDS) combining signature-based and behavior-based detection. The system monitors network traffic, generates alerts, enriches data with geolocation, backs up logs, and provides visual analysis via Kibana dashboards.


The prototype is meant to successfully detect common attack types such as DoS, port scans, probing, and brute-force attempts using benchmark datasets and live simulations.


## Tools & Technologies Used

  
     Tool                      Purpose
|**Suricata**         | Signature-based IDS/IPS, alert generation                    
| **Zeek**            | Network Security Monitor, protocol analysis & detailed logs   
| **Filebeat**        | Log collection and forwarding to Elasticsearch               
| **Elasticsearch**   | Log storage, indexing and search engine                     
| **Kibana**          | Dashboards, visualizations, GeoIP maps, alert exploration     
| **Wazuh**           | SIEM layer, backups, alert correlation and management       
| **tcpreplay**       | PCAP replay for offline attack simulation                     
| **MaxMind GeoLite2**| Geolocation enrichment for IP addresses                        
| **Docker**          | Container management (mainly for ELK Stack & Wazuh components)
