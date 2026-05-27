# 🪐 ORIORIS - Infrastructure Agentique & Sécurisée (Bunker IA)

> **"Ne changez pas d'environnement, structurez-le."**
> Déploiement d'une architecture souveraine hybride (Local/Cloud) orientée automatisation et protection des données.

Ce dépôt présente l'architecture Docker au cœur du système **ORIORIS**. Conçu pour répondre à des exigences strictes de souveraineté des données, de réduction de la charge cognitive et de haute disponibilité, ce "Bunker IA" orchestre des LLMs, une base de données vectorielle et des agents d'automatisation dans un réseau local hermétique.

---

## 🏗️ Architecture du Réseau (Zero Trust)

Le système est conçu sur un modèle d'isolation stricte. L'accès externe est verrouillé et transite uniquement via des tunnels cryptés.

```mermaid
graph TD
    subgraph "🌐 Extérieur (Internet)"
        User[Utilisateur / Mobile]
    end

    subgraph "🛡️ Périmètre de Sécurité"
        CF[Cloudflare Tunnel]
        TS[Tailscale Node]
    end

    subgraph "🐳 Réseau Docker Interne (bunker_net)"
        N8N[n8n - Orchestrateur]
        LLM[LiteLLM - Routeur IA]
        VDB[Qdrant - Vector DB]
        DB[(PostgreSQL)]
        CHAT[Open WebUI]
        SEARCH[SearXNG - OSINT]
    end

    User -->|Accès Web Chiffré| CF
    User -->|VPN Mesh| TS
    
    CF --> CHAT
    TS --> N8N
    
    CHAT --> LLM
    N8N --> LLM
    N8N --> VDB
    N8N --> DB
    LLM --> SEARCH
⚙️ Stack Technique & Conteneurs
L'infrastructure est modulaire et orchestrée via docker-compose.

1. 🧠 Couche Intelligence & Routage
LiteLLM : Proxy et load-balancer. Uniformise les appels vers des API tierces (Groq, OpenAI, Google) et les modèles locaux (Ollama) tout en gérant les quotas (RPM/TPM).

Open WebUI : Interface de chat souveraine connectée à LiteLLM, isolée du web public.

2. ⚡ Couche Automatisation & Données
n8n : Orchestrateur de flux de travail. Déployé avec des variables d'environnement restrictives pour protéger le système de fichiers (N8N_FS_ALLOWED_PATHS).

PostgreSQL & Redis : Bases de données robustes pour la persistance de n8n et la mise en cache des requêtes LLM (optimisation de la latence).

Qdrant : Base de données vectorielle ultra-rapide pour l'ingestion de documents et l'architecture RAG (Retrieval-Augmented Generation).

3. 🔐 Couche Sécurité & Accès
Tailscale : Déployé en network_mode: host avec privilèges net_admin restreints pour intégrer le serveur dans un VPN Mesh privé (WireGuard).

Cloudflared : Tunnel sortant sécurisé, évitant l'ouverture de ports (NAT) sur le pare-feu matériel de l'infrastructure hôte.

🔒 Posture de Sécurité (SecOps)
Ce dépôt est une démonstration d'infrastructure. Les mesures de sécurité suivantes sont appliquées en production :

Air-Gap Logique : Tous les conteneurs sensibles sont isolés dans le réseau bunker_net (driver: bridge). Seuls les tunnels ont un accès entrant/sortant.

Gestion des Secrets : Aucun identifiant n'est hardcodé. Le déploiement s'appuie sur un fichier .env non versionné (voir .env.example).

Filtrage Git : Un .gitignore strict empêche la fuite de données locales (volumes de base de données, clés privées, configurations spécifiques).

🚀 Déploiement
Cloner le dépôt :

Bash
   git clone [https://github.com/ORIORIS-FR/orioris-infra-docker.git](https://github.com/ORIORIS-FR/orioris-infra-docker.git)
   cd orioris-infra-docker
Configurer l'environnement :

Bash
   cp .env.example .env
   # Éditer le fichier .env avec vos variables sécurisées
   nano .env
Lancer l'infrastructure :

Bash
   docker-compose up -d
