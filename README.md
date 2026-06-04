# Creation-d-une-IA-Locale-sur-Kali
[Installation d’une stack IA locale sur Kali   LM Studio + AnythingLLM (Docker) + Mistral.md](https://github.com/user-attachments/files/28604582/Installation.d.une.stack.IA.locale.sur.Kali.LM.Studio.%2B.AnythingLLM.Docker.%2B.Mistral.md)
# 🔵 ÉTAPE 1 — Vérifie ta machine

### RAM disponible (besoin de 16GB min)

==free -h==

### Espace disque (besoin de 30GB min)

==df -h ~==

### GPU NVIDIA ?

==nvidia-smi 2>/dev/null || echo "Pas de GPU NVIDIA détecté"==

# 🔵 ÉTAPE 2 — Installe LM Studio

### Installe LM Studio (CLI + app)

==curl -fsSL https://lmstudio.ai/install.sh | bash==

### Recharge ton shell pour avoir la commande 'lms'

==source ~/.bashrc==

### Vérifie que ça marche

==lms --version==

==npx lmstudio install-cli==

# 🔵 ÉTAPE 3 — Démarre le serveur LM Studio

## Lance le serveur local (API compatible OpenAI)

==lms server start --port 1234==

## Vérifie qu'il tourne

curl http://localhost:1234/v1/models
### → doit retourner du JSON (même vide c'est bon)

# 🔵 ÉTAPE 4 — Télécharge WhiteRabbitNeo

## Si tu as 32GB+ RAM → version 13B (cybersécurité spécialisé)

==lms get WhiteRabbitNeo/WhiteRabbitNeo-13B-v1==

## Si tu as 16GB RAM → version 7B plus légère

==lms get TheBloke/Mistral-7B-Instruct-v0.2-GGUF==

## Charge le modèle dans le serveur

==lms load WhiteRabbitNeo/WhiteRabbitNeo-13B-v1==

## Vérifie qu'il est bien chargé

==lms ps==

##### ps ⏳ Le téléchargement peut prendre 10-30 min selon ta connexion (8-15 GB)

# 🔵 ÉTAPE 5 — Installe AnythingLLM

## Télécharge le script officiel

==curl -fsSL https://cdn.anythingllm.com/latest/installer.sh -o installer.sh==

## Rends-le exécutable

==chmod +x installer.sh==

## Lance l'installation

==./installer.sh==

## AnythingLLM s'installe dans `~/AnythingLLMDesktop/`. Lance-le avec :

==~/AnythingLLMDesktop/start &==

# 🔵 ÉTAPE 6 — Connecte AnythingLLM à LM Studio

## Ouvre Firefox et va sur **`http://localhost:3001`**[](https://docs.vllm.ai/en/stable/deployment/frameworks/anything-llm/)

### Puis dans l'interface :

1. ==Clique sur ⚙️ (icône clé en bas à gauche)==
2. ==→ "AI Providers" → "LLM"==
3. ==Choisis : LLM Provider → "LM Studio"==
4. ==Base URL → http://localhost:1234/v1==
5. ==Clique "Save changes"==

# 🔵 ÉTAPE 7 — Crée ton workspace Pentest

### Dans AnythingLLM :

1. ==Clic sur "New Workspace"==
2. ==Nomme-le "Active Directory" (ou "Pentest" etc.)==
3. ==Clique sur 📎 (trombone) pour uploader tes docs==
4. ==Upload tes notes Obsidian / cheatsheets==
5. =="Save and Embed" → ton IA peut maintenant les utiliser==

# 🔵 ÉTAPE 8 — Installe le script de lancement rapide

## Télécharge le script 
#### (le fichier setup-ia-pentest.sh téléchargé précédemment)

==chmod +x setup-ia-pentest.sh==
==./setup-ia-pentest.sh==

#### Ensuite pour tout lancer en 1 commande :

==source ~/.bashrc==
==pentest-ai==

## ✅ Résumé des URLs

| Service          | URL                         |
| ---------------- | --------------------------- |
| LM Studio API    | ==`http://localhost:1234`== |
| AnythingLLM Chat | ==`http://localhost:3001`== |
![[Préparation_Kali 1.py]]   1
![[Création_IA.sh]]          2
![[Cloture.py]]              3
