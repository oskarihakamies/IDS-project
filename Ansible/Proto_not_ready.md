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


#

... (alkuosa pysyy samana asennukseen asti)

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
      plugins.security.ssl.http.clientauth_mode: OPTIONAL
      plugins.security.ssl.http.pemcert_filepath: /etc/wazuh-indexer/certs/node-1.pem
      plugins.security.ssl.http.pemkey_filepath: /etc/wazuh-indexer/certs/node-1-key.pem
      plugins.security.ssl.http.pemtrustedcas_filepath: /etc/wazuh-indexer/certs/root-ca.pem
      plugins.security.allow_unsafe_democertificates: false
      plugins.security.allow_default_init_securityindex: true
      plugins.security.authcz.admin_dn:
        - "CN=admin,OU=Wazuh,O=Wazuh,L=California,C=US"
      plugins.security.nodes_dn:
        - "CN=node-1,OU=Wazuh,O=Wazuh,L=California,C=US"

- name: Force restart wazuh-indexer
  systemd:
    name: wazuh-indexer
    state: restarted

- name: Wait for Wazuh Indexer to be fully ready (Security plugin takes time)
  pause:
    seconds: 45

- name: Initialize Wazuh Indexer security (Explicit command)
  command: >
    /usr/share/wazuh-indexer/plugins/opensearch-security/tools/securityadmin.sh
    -cd /etc/wazuh-indexer/opensearch-security/
    -icl -nhnv
    -cacert /etc/wazuh-indexer/certs/root-ca.pem
    -cert /etc/wazuh-indexer/certs/admin.pem
    -key /etc/wazuh-indexer/certs/admin-key.pem
    -h 127.0.0.1
  environment:
    JAVA_HOME: /usr/share/wazuh-indexer/jdk
  register: security_init
  until: security_init.rc == 0
  retries: 3
  delay: 10

- name: Mark security initialization as complete
  file:
    path: /var/wazuh-indexer-security-initialized.flag
    state: touch
