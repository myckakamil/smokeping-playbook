# Ansible Playbook — Smokeping Deployment

This repository contains an **Ansible playbook** that automates the installation and configuration of [Smokeping](https://oss.oetiker.ch/smokeping/). It configures Apache, adjusts Smokeping’s internal parameters, and deploys templated configuration files for monitoring your targets.

## Usage
### 1. Clone this repository
```
git clone https://github.com/your-org/ansible-smokeping.git
cd ansible-smokeping
```
### 2. Adjust your inventory
```ini
[server]
yourserver.example.com ansible_user=admin
```
### 3. Run the playbook
```
ansible-playbook -i inventory playbook.yaml --ask-vault-pass
```
### 4. Access the web UI
Once deployed, Smokeping should be available at:
```
http://yourserver.example.com/smokeping
```

## Repository Structure
```
.
├── ansible.cfg
├── files
│   ├── Database
│   ├── Presentation
│   ├── Probes
│   ├── smokeping.conf
│   └── Targets
├── inventory.ini
├── main.yaml
├── README
├── templates
│   └── Targets_Manual.j2
└── vars
    ├── vault.yaml
    └── vault.yaml.example
```

## Variables
Sensitive variables like client IP addresses and thir client number are stored in `vars/vault.yaml`. To edit:
```
ansible-vault edit vars/vault.yaml
```

## How the Template Works
The template `Targets_Manual.j2` dynamically creates sections based on defined target groups:
```jinja
+ {{ target_group }}
menu = {{ target_group }}
title = {{ target_group }}

++ Clients
{% for h in client %}
+++ {{ h.name }}
host = {{ h.ip_address }}
{% endfor %}
```
Example variable structure:
```yaml
---
target_group: "ISP_hosts"
client:
  - {name: "U12345", ip_address: "100.54.30.51"}
  - {name: "U12346", ip_address: "100.54.30.52"}
  - {name: "U20298", ip_address: "100.54.30.53"}
infra:
  - {name: "20-30-R1", ip_address: "10.20.30.1"}
  - {name: "20-30-S1", ip_address: "10.20.30.11"}

```

