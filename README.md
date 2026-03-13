# 📊 Bilan Santé SRE — Rapport IA automatisé

> Collecte multi-sources → Agrégation → Diagnostic IA → Rapport HTML professionnel. En un clic.

![n8n](https://img.shields.io/badge/n8n-workflow-EA4B71?style=flat&logo=n8n&logoColor=white)
![Zabbix](https://img.shields.io/badge/Zabbix-monitoring-CC0000?style=flat)
![Loki](https://img.shields.io/badge/Loki-logs-F5A623?style=flat)
![Gemini](https://img.shields.io/badge/Google-Gemini-4285F4?style=flat&logo=google&logoColor=white)
![Proxmox](https://img.shields.io/badge/Proxmox-compatible-E57000?style=flat)
![License](https://img.shields.io/badge/license-MIT-green?style=flat)

---

## Description

Ce workflow génère automatiquement un **rapport de santé complet de l'infrastructure** en croisant Zabbix, Loki et une IA (Gemini). Le résultat est un rapport HTML visuel et professionnel, prêt à être partagé ou intégré dans un dashboard.

**Le problème résolu :** Faire le tour de l'infra chaque matin pour vérifier l'état des serveurs, lire les logs, identifier les alertes et prioriser les actions prend du temps. Ce workflow le fait en quelques secondes et produit un diagnostic structuré.

---

## Flux de traitement

```
Déclenchement manuel (ou CRON)
    │
    ▼
Zabbix — Récupération de tous les hôtes actifs
    │
    ▼
Split — Pour chaque hôte en parallèle :
    ├── Zabbix → CPU
    ├── Zabbix → RAM
    ├── Zabbix → Disque
    ├── Zabbix → Alertes actives (Triggers)
    └── Loki   → Logs erreurs 24h
    │
    ▼
Merge — Agrégation des 5 sources
    │
    ▼
Code — Transformation en structure unifiée par hôte
    │
    ▼
Aggregate — Consolidation globale
    │
    ▼
Gemini (IA) — Diagnostic structuré :
    ├── 🔴 Alertes prioritaires
    ├── 🟡 Analyse des services
    ├── 📊 Performance CPU/RAM/Disque
    └── 🛠 Plan d'action 3 recommandations
    │
    ▼
Code — Génération HTML
    │
    ▼
Rapport HTML visuel professionnel
```

---

## Stack technique

| Composant | Rôle |
|-----------|------|
| **Zabbix** | Métriques CPU/RAM/Disque + Triggers actifs |
| **Loki** | Logs erreurs/warnings des 24 dernières heures |
| **Google Gemini** | Analyse IA et diagnostic structuré |
| **n8n** | Orchestration et génération du rapport HTML |

Compatible **Proxmox** — les clés de métriques Proxmox sont gérées nativement dans le code d'agrégation.

---

## Exemple de rapport généré

Le rapport HTML produit contient :
- Alertes critiques avec contexte
- Analyse des services Docker et VMs Proxmox
- Métriques de performance par serveur (CPU/RAM/Disque)
- Plan d'action tactique en 3 points pour les 24h suivantes

---

## Prérequis

- n8n >= 1.0
- Zabbix >= 7.0 avec API REST activée
- Loki accessible via HTTP
- Clé API Google Gemini

---

## Installation

### 1. Importer le workflow

Dans n8n : **Settings → Import workflow** → sélectionner `workflow.json`

### 2. Configurer les credentials n8n

| Credential | Type |
|-----------|------|
| `Zabbix account` | Zabbix API |
| `Google Gemini(PaLM) Api account` | Google PaLM API |

### 3. Remplacer les placeholders

| Placeholder | Valeur |
|------------|--------|
| `YOUR_ZABBIX_HOST` | IP ou FQDN de votre serveur Zabbix |
| `YOUR_LOKI_HOST` | IP ou FQDN de votre serveur Loki |
| `YOUR_ZABBIX_CREDENTIAL_ID` | ID du credential Zabbix dans n8n |
| `YOUR_GEMINI_CREDENTIAL_ID` | ID du credential Gemini dans n8n |

### 4. Optionnel — Automatiser avec un CRON

Remplacer le nœud **"On clicking 'execute'"** par un nœud **Schedule Trigger** pour un rapport automatique chaque matin.

---

## Notes

- Le workflow supporte les métriques Linux standard ET Proxmox (détection automatique via la clé Zabbix)
- Les logs Loki sont filtrés sur les niveaux error/warn/crit/panic/fatal/failed
- Le rapport HTML est autonome et peut être envoyé par email ou affiché dans un dashboard
- Testé sur Zabbix 7.4 et Proxmox 8.x

---

## Licence

MIT — Libre d'utilisation, modification et distribution.

---

*Partagé par [Xavier Maugendre](https://www.linkedin.com/in/xmaugendre/) — Expert Observabilité & SRE*
