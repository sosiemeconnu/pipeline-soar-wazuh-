# 🛡️ Pipeline SOAR : Remédiation Automatisée (Wazuh, Shuffle, VirusTotal, Discord)

## 🎯 Objectif du Projet
Ce projet démontre la mise en place d'une chaîne complète de réponse à incident (SOAR) automatisée. L'objectif est de détecter une activité réseau suspecte sur un agent Linux, d'enrichir l'alerte via de la Cyber Threat Intelligence (CTI), et d'exécuter une remédiation dynamique (blocage IP) sans intervention humaine, tout en notifiant l'équipe de sécurité.

## ⚙️ Architecture et Workflow

Le flux de données est orchestré par **Shuffle** et suit ces 5 étapes critiques :

1. **Détection :** L'agent Wazuh remonte une alerte de sécurité au Manager.
2. **Enrichissement (CTI) :** Shuffle interroge l'API **VirusTotal** pour analyser l'adresse IP source et obtenir un score de malveillance.
3. **Orchestration :** Formatage des données et ciblage précis de l'agent.
4. **Remédiation Active :** Appel API vers le Manager Wazuh pour déclencher le script natif `host-deny` directement sur la machine compromise.
5. **Notification :** Envoi d'un rapport de situation formaté vers un Webhook **Discord**.

```mermaid
sequenceDiagram
    participant Attaquant
    participant Agent_Debian as 🐧 Agent Debian (003)
    participant Wazuh_Manager as 🛡️ Manager Wazuh
    participant Shuffle as 🔀 Shuffle (SOAR)
    participant VirusTotal as 🦠 VirusTotal API
    participant Discord as 💬 Discord Webhook

    Attaquant->>Agent_Debian: 1. Activité suspecte / Connexion malveillante
    Agent_Debian->>Wazuh_Manager: 2. Remontée de l'alerte
    Wazuh_Manager->>Shuffle: 3. Déclenchement du flux webhook
    Shuffle->>VirusTotal: 4. Extraction IP & Requête CTI
    VirusTotal-->>Shuffle: 5. Retour du score de menace
    Shuffle->>Wazuh_Manager: 6. Ordre de remédiation (Action: host-deny)
    Wazuh_Manager->>Agent_Debian: 7. Mise à jour automatique de /etc/hosts.deny
    Shuffle->>Discord: 8. Envoi du rapport final d'intervention

## 📸 Démonstration en images

![Dashboard Wazuh et Agent Debian](images/wazuh-dashboard.png)
> **Figure 1 :** Interface du Manager Wazuh confirmant la connexion et la supervision de l'agent Debian cible (ID: 003).

![Flux d'orchestration complet dans Shuffle](images/flux-shuffle.png)
> **Figure 2 :** Flux d'orchestration global dans l'interface Shuffle, de la détection à la notification.

![Score de menace VirusTotal](images/virustotal-cti.png)
> **Figure 3 :** Enrichissement de l'alerte via l'API VirusTotal pour confirmer la malveillance de l'IP avant remédiation.

![Notification Discord dynamique](images/webhook-discord.png)
> **Figure 4 :** Succès de l'intégration du Webhook avec réception de l'alerte formatée sur le salon SOC.

![Mise à jour du fichier hosts.deny](images/hosts-deny-result.png)
> **Figure 5 :** Résultat de l'Active Response sur l'agent Debian : l'IP cible est bloquée dynamiquement au niveau du système.
