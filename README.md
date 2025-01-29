# Azure DevOps Server 2022 Automation

This repository deploys and manages Azure DevOps Server 2022 using ansible.

## What works
* Provision the AZDO Configuration database (just provisioning, no management)
* Make sure the AZDO service account has db owner permissions in the AZDO Configuration database

## What is on the todo list
* Unattended installation of Azure Devops Server

## Provisioning ansible worker
### Systeem packages

To install the Kerberos libraries on a RHEL/Fedora based system:
```
sudo dnf install krb5-devel krb5-libs krb5-workstation python3-devel
```

For a Debian/Ubuntu based system:

```
sudo apt-get install -y gcc git krb5-user libkrb5-dev python3 python3-dev python3-pip python3-venv
```

### Python modules

### Ansible collections

### Git setup
```
git config user.name "Your Name"
git config user.email "youremail@yourdomain.com"
git config credential.helper "/mnt/c/Program\ Files/Git/mingw64/bin/git-credential-manager.exe"
git config credential.https://dev.azure.com.useHttpPath true
```

### WSL Setup
#### Gebruik DNS Server(s) van lab omgeving

1. Neem deze config op in `/etc/wsl.conf`
   ```
   [network]
   generateResolvConf = false
   ```
2. Stop WSL
   ```
   wsl --shutdown
   ```
3. Herstart WSL, verwijder de symbolic link voor `/etc/resolv.conf` en maak een nieuw bestand aan
   ```
   sudo rm /etc/resolv.conf && sudo vi /etc/resolv.conf
   ```
4. Voeg de DNS servers en domein(en) toe
   ```
   nameserver 192.168.147.10
   search azdopoc.localdomain
   ```