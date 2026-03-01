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
