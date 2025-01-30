# Installatie van POC omgeving

Deze instructie beschrijft hoe een POC omgeving op te zetten voor het testen van de ansible playbooks in deze repository.

## Vereisten
De POC omgeving heeft de volgende systeemeisen:
* Windows 10 of Windows 11
  * Linux zou eventueel ook kunnen, maar dit vereist een kleine aanpassing in de playbooks `start-lab.yml` en `shutdown-lab.yml`.
* VMware workstation
  * Dit is tegenwoordig gratis te gebruiken, [ook voor zakelijke gebruikers,](https://blogs.vmware.com/cloud-foundation/2024/11/11/vmware-fusion-and-workstation-are-now-free-for-all-users/). Dit in tegenstelling tot bijvoorbeeld [virtalbox](https://forum.virtualbox.org/wiki/Licensing_FAQ).
* Een Windows server geconfigureerd met de AD DS rol en een statisch IP adres.
* Een Windows member server met een installatie van SQL Server.
* Een of meerdere Windows server member servers die applicatie servers

## Provisioning ansible worker
### Systeem packages

To install the Kerberos libraries on a RHEL/Fedora based system:
```bash
sudo dnf install krb5-devel krb5-libs krb5-workstation python3-devel
```

For a Debian/Ubuntu based system:

```bash
sudo apt-get update && sudo apt-get install -y gcc git krb5-user libkrb5-dev python3 python3-dev python3-pip python3-venv
```

Uitzoeken hoe deze instellingen te automatiseren:
* Default kerberos 5 realm (azdopoc.localdomain)
* Kerberos servers (azdo-adserver.azdopoc.localdomain)
* Administrative server (azdo-adserver.azdopoc.localdomain)
Voor deze instellingen krijg je momenteel een prompt.

### Python setup

#### Setup virtual environment en shell
Pas voor je hiermee aanvangt de omgevingsvariablen `VENV_DIR` en `REPO_DIR` aan naar de gewenste paden.

```bash
cd ~
export VENV_DIR=~/pgb-ansible
python3 -m venv $VENV_DIR
sh -c 'echo "\n# Start python virtual environment\nsource $VENV_DIR/bin/activate" >> ~/.bashrc'
source ~/.bashrc
export REPO_DIR=~/pgb-azdo
mkdir $REPO_DIR
sh -c 'echo "# Custom aliases\nalias start-lab=\"ansible-playbook $REPO_DIR/playbooks/start-lab.yml -i $REPO_DIR/inventory/dev/hosts.yml\"\nalias shutdown-lab=\"ansible-playbook $REPO_DIR/playbooks/shutdown-lab.yml -i $REPO_DIR/inventory/dev/hosts.yml\"" >> ~/.bash_aliases'
```

### Pull repository
De repository is nodig omdat hier de setup files in zitten voor python en ansible

#### Git setup
```bash
git config --global user.name "Your Name"
git config --global user.email "youremail@yourdomain.com"
git config --global credential.helper "/mnt/c/Program\ Files/Git/mingw64/bin/git-credential-manager.exe"
git config --global credential.https://dev.azure.com.useHttpPath true
```

### Ansible setup
```bash
python3 -m pip install --upgrade -r $REPO_DIR/provisioning/requirements.txt
```

### WSL Setup
#### Gebruik DNS Server(s) van lab omgeving

1. Neem deze config op in `/etc/wsl.conf`
   ```bash
   sudo sh -c 'echo "[network]\ngenerateResolvConf = false" >> /etc/wsl.conf'
   ```
2. Stop WSL.
   ```bat
   wsl --shutdown
   ```
   Als je meerdere distributies geïnstalleerd hebt kun je eerst de lijst van actieve distributies opvragen met:
   ```bat
   wsl --list --running
   ```
   En sluit de gewenste distributie af met:
   ```bat
   wsl --terminate DistroNaam
   ```
3. Herstart WSL, verwijder de symbolic link voor `/etc/resolv.conf` en maak een nieuw bestand aan
   ```bash
   sudo rm /etc/resolv.conf && sudo vi /etc/resolv.conf
   ```
4. Voeg de DNS servers en domein(en) toe
   ```bash
   nameserver 192.168.147.10
   search azdopoc.localdomain
   ```