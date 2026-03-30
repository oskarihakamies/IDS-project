## Note. The product isn't fully finished, not completely ready and everything that happens is at users own risk

The pipeline is 
Suricata -> Wazuh Agent -> Wazuh Manager -> Filebeat -> Wazuh Indexer

# Howto?

1. Create a new VM or use an existing one. (Debian 13)

2. Download the zip file

[soc-project.zip](https://github.com/user-attachments/files/26360296/soc-project.zip)

3. Navigate to Downloads and look for the file

<img width="735" height="301" alt="kuva" src="https://github.com/user-attachments/assets/640f9480-8673-4f41-8bcc-154deb6fc857" />


4. Download zip ```sudo apt-get install zip```

5. Unzip the file ```unzip soc-project.zip```

6. Navigate to the unzipped file ```cd soc-project```, ```ls``` to list the files

7. Use ```sudo bash install.sh``` to launch it

8. Let it do its magic. This takes around 2-10 minutes. Grab a coffee.

9. When it's done, navigate to Firefox or w.e Browser

10. Type into the URL: localhost

11. NOTE: IF Wazuh sends an error, change the address to /app/home/ - this tends to happen.

12. user ```admin``` pass ```admin```
