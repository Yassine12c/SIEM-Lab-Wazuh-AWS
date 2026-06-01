# 🛡️ SIEM Lab — Wazuh sur AWS EC2

Laboratoire de cybersécurité complet simulant une architecture SOC réelle avec Wazuh comme SIEM, déployé sur AWS EC2.

---

## 🏗️ Architecture

| Machine | OS | Rôle | IP Privée |
|---|---|---|---|
| VM_SERVER_WAZUH | Ubuntu 22.04 | Wazuh Manager + OpenSearch + Dashboard | 172.31.3.98 |
| VM_CLIENT_WAZUH | Ubuntu 22.04 | Machine cible — Agent Wazuh installé | 172.31.3.122 |
| VM_KALI_ATTACKER | Ubuntu 22.04 | Machine attaquante — Nmap, Hydra | 172.31.4.93 |

---

## 🔧 Technologies utilisées

- **Wazuh 4.7.5** — SIEM / XDR
- **OpenSearch** — Indexation des logs
- **OpenSearch Dashboards** — Visualisation
- **Wireshark / tshark** — Capture réseau
- **Hydra** — Simulation brute force SSH
- **Nmap** — Scan de ports
- **AWS EC2** — Infrastructure cloud

---

## 🚀 Installation

### 1. Wazuh Server
```bash
curl -sO https://packages.wazuh.com/4.7/wazuh-install.sh
sudo bash wazuh-install.sh -a -i
```

### 2. Agent Wazuh sur machine cible
```bash
wget https://packages.wazuh.com/4.x/apt/pool/main/w/wazuh-agent/wazuh-agent_4.7.5-1_amd64.deb
sudo WAZUH_MANAGER='172.31.3.98' WAZUH_AGENT_NAME='VM_CLIENT_WAZUH' dpkg -i ./wazuh-agent_4.7.5-1_amd64.deb
sudo systemctl daemon-reload && sudo systemctl enable wazuh-agent && sudo systemctl start wazuh-agent
```

---

## ⚔️ Scénarios d'attaque testés

### 1. Brute Force SSH — Hydra
```bash
hydra -l ubuntu -P /home/ubuntu/rockyou.txt ssh://172.31.3.122 -t 4
```
**Résultat Wazuh** : 128 alertes — MITRE T1110 Credential Access détecté

### 2. Scan de ports — Nmap
```bash
sudo nmap -sS 172.31.3.122
```
**Résultat Wireshark** : Paquets SYN capturés sur tous les ports

### 3. File Integrity Monitoring — FIM
```bash
sudo bash -c 'echo "test" >> /etc/test-wazuh-fim.txt'
```
**Résultat Wazuh** : MITRE T1565.001 Stored Data Manipulation — hash MD5/SHA256 modifiés détectés

### 4. Élévation de privilèges
```bash
sudo cat /etc/shadow
sudo su
```
**Résultat Wazuh** : MITRE T1548.003 Privilege Escalation détecté

---

## 📊 Résultats

| Test | Alertes | Niveau | MITRE |
|---|---|---|---|
| Brute Force SSH | 128 | 10 | T1110 |
| FIM | 1 | 7 | T1565.001 |
| Élévation de privilèges | 14 | 3 | T1548.003 |
| Scan Nmap | Wireshark | — | — |

---

## 🎓 Compétences démontrées

- Déploiement d'un SIEM complet sur AWS
- Configuration d'agents Wazuh sur Linux
- Simulation d'attaques réelles et corrélation d'événements
- Analyse forensique avec hash MD5/SHA256
- Capture et analyse de trafic réseau avec Wireshark
- Utilisation du framework MITRE ATT&CK
