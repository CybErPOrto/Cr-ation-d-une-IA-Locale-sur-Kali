# Installation d’une stack IA locale sur Kali

LM Studio + AnythingLLM (Docker) + Mistral

## Vue d’ensemble

L’objectif est de mettre en place une stack IA 100% locale sur Kali Linux :

- **LM Studio** sert de serveur LLM local (ex : Mistral 7B Instruct).
    
- **AnythingLLM** tourne en **Docker** et fournit l’interface web (workspaces, RAG, agents) sur `http://localhost:3001`.
    
- AnythingLLM est configuré pour **appeler LM Studio** via son API OpenAI-compatible.
    

---

## Pré-requis sur Kali

1. **Session graphique** (interface KDE/GNOME/etc.)
    
    - Ouvrir un terminal depuis le bureau.
        
    - Vérifier que la variable `DISPLAY` est définie :
        
        bash
        
        `echo $DISPLAY`
        
        Un résultat comme `:0` confirme que tu es bien en session graphique (sinon, Firefox ne pourra pas s’ouvrir).
        
2. **Docker installé**
    
    - Vérifier la version :
        
        bash
        
        `docker --version`
        
        Par exemple : `Docker version 28.5.2+dfsg4, build ...` confirmé sur ta machine.[](https://github.com/Mintplex-Labs/anything-llm/blob/master/docker/HOW_TO_USE_DOCKER.md)[](https://www.youtube.com/watch?v=C8rteQKL5bU)[](https://docs.anythingllm.com/installation-docker/local-docker)
        
3. **LM Studio installé + CLI `lms` disponible**
    
    - LM Studio fournit un CLI `lms` permettant de lancer un serveur API local compatible OpenAI.
        

---

## Étape 1 : Préparer le dossier IA

Dans le compte utilisateur `cyberporto` :

bash

`mkdir -p /home/cyberporto/ai-pentest/workspaces`

Ce dossier servira à stocker les données persistantes d’AnythingLLM (workspaces, config).

---

## Étape 2 : Installer AnythingLLM en Docker

## 2.1 – Télécharger l’image Docker

bash

`docker pull mintplexlabs/anythingllm:latest`

Cette image contient l’application serveur AnythingLLM, utilisable via Docker.

## 2.2 – Créer le conteneur AnythingLLM

On crée un conteneur nommé `anythingllm`, qui écoute sur le port 3001 et utilise ton dossier `workspaces` comme stockage :

bash

`docker run -d \   --name anythingllm \  -p 3001:3001 \  -e STORAGE_DIR=/app/server/storage \  -v /home/cyberporto/ai-pentest/workspaces:/app/server/storage \  mintplexlabs/anythingllm:latest`

Explications :

- `-p 3001:3001` : expose l’UI sur `http://localhost:3001`.
    
- `-e STORAGE_DIR=/app/server/storage` : indique à AnythingLLM où stocker ses données.
    
- `-v /home/cyberporto/ai-pentest/workspaces:/app/server/storage` : monte ton dossier local pour rendre les données persistantes entre les redémarrages.
    

Vérifier :

bash

`docker ps`

Tu dois voir une ligne :

- `NAMES` : `anythingllm`
    
- `PORTS` : `0.0.0.0:3001->3001/tcp`  
    Cela confirme que l’UI est disponible sur `http://localhost:3001`.[](https://www.youtube.com/watch?v=C8rteQKL5bU)
    

---

## Étape 3 : Démarrer le serveur LM Studio

LM Studio fournit un serveur API local pour exposer tes modèles (par ex. Mistral 7B Instruct).

## 3.1 – Démarrer le serveur LM Studio

Dans un terminal en `cyberporto` :

bash

`lms server start --port 1234`

Ce serveur écoute sur `http://127.0.0.1:1234`.

## 3.2 – Vérifier le statut

bash

`lms server status`

Tu dois voir :

text

`The server is running on port 1234.`

Tu peux aussi tester l’API :

bash

`curl http://127.0.0.1:1234/v1/models`

Une réponse JSON listant les modèles indique que LM Studio expose correctement les modèles via une API OpenAI-compatible.

---

## Étape 4 : Ouvrir l’interface AnythingLLM

Depuis ta session graphique (terminal ouvert depuis le bureau) :

bash

`firefox http://localhost:3001 &`

Tu accèdes à l’UI d’AnythingLLM qui tourne dans Docker sur le port 3001.

Lors du premier lancement, AnythingLLM te demande :

- de créer un compte admin,
    
- de configurer le **LLM provider**,
    
- de choisir un dossier de stockage (déjà géré par le volume Docker).
    

---

## Étape 5 : Configurer AnythingLLM pour utiliser LM Studio

Tu veux que AnythingLLM envoie les requêtes LLM à LM Studio, qui lui-même sert ton modèle Mistral.

## 5.1 – Choisir le provider

Dans AnythingLLM :

1. Menu **Settings** → **LLM Configuration** (System LLM).
    
2. Si ta version propose **“LM Studio”** comme provider local :
    
    - sélectionne **LM Studio**.[](https://docs.useanything.com/setup/llm-configuration/local/lmstudio)[](https://www.youtube.com/watch?v=UG8uftJXcNs)
        
3. Si tu ne vois pas “LM Studio”, utilise **“Generic OpenAI”** comme wrapper OpenAI-compatible.
    

## 5.2 – Paramètres pour LM Studio (via Generic OpenAI dans Docker)

Depuis un conteneur Docker, pour joindre un service tournant sur le host (Kali), AnythingLLM doit utiliser une adresse adaptée.

Dans la configuration **Generic OpenAI** :

- **Base URL** :
    
    text
    
    `http://172.17.0.1:1234/v1`
    
    172.17.0.1 est l’IP la plus courante du host vue depuis Docker (à adapter si besoin).
    
- **API key** :
    
    text
    
    `dummy`
    
    AnythingLLM exige souvent une API key non vide, mais LM Studio n’en a pas besoin en local.
    
- **Model** : Mets le **nom exact** du modèle Mistral que tu as chargé dans LM Studio (tel qu’il apparaît dans LM Studio).
    
- Sauvegarde, puis utilise le bouton **Test** ou envoie un message dans un workspace pour valider la connexion.
    

Si un provider “LM Studio” natif est disponible dans ta version :

- **Base URL** :
    
    text
    
    `http://172.17.0.1:1234`
    
- **Model** : idem, nom du modèle LM Studio.
    

---

## Étape 6 : Cycle de démarrage / arrêt

## 6.1 – Démarrer la stack à la main

1. Démarrer LM Studio :
    
    bash
    
    `lms server start --port 1234`
    
2. Démarrer AnythingLLM (Docker) :
    
    bash
    
    `docker start anythingllm`
    
3. Ouvrir l’interface :
    
    bash
    
    `firefox http://localhost:3001 &`
    

## 6.2 – Arrêter proprement

Quand tu as fini :

1. Arrêter LM Studio :
    
    bash
    
    `lms server stop`
    

2. Arrêter AnythingLLM :
    
    bash
    
    `docker stop anythingllm`
    

[](https://github.com/Mintplex-Labs/anything-llm/blob/master/docker/HOW_TO_USE_DOCKER.md)[](https://www.youtube.com/watch?v=C8rteQKL5bU)[](https://docs.anythingllm.com/installation-docker/local-docker)

---

## Étape 7 : Aliases pratiques (`pentest-ai` / `pentest-stop`)

## 7.1 – Alias `pentest-ai` (démarrage)

Dans `/home/cyberporto/.zshrc` :

bash

`nano ~/.zshrc`

Ajoute :

bash

`alias pentest-ai='lms server start --port 1234 && docker start anythingllm'`

Recharge :

bash

`source ~/.zshrc`

Usage :

bash

`pentest-ai`

Cela démarre LM Studio et relance le conteneur AnythingLLM.[](https://lmstudio.ai/docs/developer/core/headless_llmster)[](https://www.youtube.com/watch?v=C8rteQKL5bU)

## 7.2 – Alias `pentest-stop` (arrêt)

Toujours dans `~/.zshrc` :

bash

`alias pentest-stop='lms server stop && docker stop anythingllm'`

Recharge :

bash

`source ~/.zshrc`

Usage :

bash

`pentest-stop`

---

## Étape 8 : Usage typique pour tes pentests

1. **Démarrage** :
    
    bash
    
    `pentest-ai firefox http://localhost:3001 &`
    
2. Dans AnythingLLM, sélectionne le workspace correspondant (ex. `Pentest-AD`), charge tes notes de lab/snapshots, et utilise ton prompt modèle pour générer :
    
    - sections de rapport,
        
    - plans d’attaque,
        
    - résumés pour client non technique.
        
3. **Arrêt** :
    
    bash
    
    `pentest-stop`