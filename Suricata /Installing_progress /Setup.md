# Suricata Installation to VM

### Setup 

- VM (Virtual Box, UTM)
- Debian 13
- Atleast 4 CPU cores and 20 GB RAM

### Installation

When you have an VM this is how you install and configure suricata.

Make basic updates and upgrades:

```
sudo apt update
```
```
sudo apt upgrade -y
```


And then install it
```
sudo apt install suricata -y
```

### Configuring

Find out your gateway and dev

```
ip -p -j route show default
```

<img width="384" height="159" alt="Näyttökuva 2026-03-01 kello 20 22 26" src="https://github.com/user-attachments/assets/dced5775-8a76-4311-91d1-0f56a54c4cbb" />

```
sudo nano /etc/suricata/suricata.yaml
```

change af-packet to your own 

<img width="274" height="38" alt="Näyttökuva 2026-03-02 kello 19 24 29" src="https://github.com/user-attachments/assets/984efbc0-048d-46a8-85a7-f1fbcfd3fc33" />

and your own ip

<img width="337" height="74" alt="Näyttökuva 2026-03-02 kello 19 28 18" src="https://github.com/user-attachments/assets/661842b5-913c-4737-8378-0c0c9a01eb50" />


