# Azure DevOps Server 2022 Automation

Deze repository zorgt voor de installatie van Azure Devops Server 2022 met ansible.

## What works
* Provision the AZDO Configuration database (just provisioning, no management)
* Make sure the AZDO service account has db owner permissions in the AZDO Configuration database

## What is on the todo list
* Unattended installation of Azure Devops Server

## Structuur
```text
├──inventory/
│  └── dev/
│  
│      └── group_vars/
│          ├── all.yml          # Variabelen die gelden voor ALLE omgevingen
│          └── <groupname>.yml  # Variabelen die gelden voor specifieke groepen in de dev omgeving
│── playbooks/
│   └── group_vars/             # Variablen die gelden op playbook niveau
├── provisioning/               # Bestanden en documentatie voor installatie van een ansible worker
├── roles/                      # Rollen voor het installeren van Azure DevOps Server
└── templates/                  # Ansible taken die worden gebruikt door meerdere rollen
```