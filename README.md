# 🤖 RAG Chatbot avec Claude (Anthropic)

> Système de Retrieval-Augmented Generation (RAG) permettant de chatter avec vos documents en utilisant les modèles Claude d'Anthropic. Avec mémoire conversationnelle multi-tours.

![Python](https://img.shields.io/badge/Python-3.10+-blue.svg)
![LangChain](https://img.shields.io/badge/LangChain-0.3+-green.svg)
![Claude](https://img.shields.io/badge/Claude-Sonnet_4.6-orange.svg)
![License](https://img.shields.io/badge/License-MIT-green.svg)

---

## 📋 Description

Ce projet implémente un pipeline RAG complet en 3 étapes :

1. **Ingestion** : chargement de documents textes → découpage en chunks → vectorisation par embeddings → stockage dans une base vectorielle (ChromaDB)
2. **Récupération** : recherche sémantique des passages pertinents pour une question donnée
3. **Génération conversationnelle** : réponse contextualisée par Claude avec gestion de l'historique multi-tours (reformulation des questions de suivi)

**Applications** : assistant documentaire, Q&A sur base de connaissances interne, analyse automatisée de rapports techniques (ex : vulnérabilités CVE, documentation produit, etc.).

## 🎯 Architecture

```
   ┌─────────────┐    ┌──────────────┐    ┌──────────────┐
   │  Documents  │───▶│   Chunking   │───▶│  Embeddings  │
   │   (.txt)    │    │ (Recursive)  │    │   (OpenAI)   │
   └─────────────┘    └──────────────┘    └──────┬───────┘
                                                  │
                                                  ▼
   ┌─────────────┐    ┌──────────────┐    ┌──────────────┐
   │   Question  │───▶│  Reformulate │───▶│   ChromaDB   │
   │  (user)     │    │  (history)   │    │  (vector)    │
   └─────────────┘    └──────────────┘    └──────┬───────┘
                                                  │
                            ┌─────────────────────┘
                            ▼
                     ┌──────────────┐    ┌──────────────┐
                     │   Claude     │───▶│   Réponse    │
                     │ (Sonnet 4.6) │    │ (avec sources)│
                     └──────────────┘    └──────────────┘
```

## 🚀 Installation

```bash
# Cloner le dépôt
git clone https://github.com/VOTRE-USERNAME/rag-claude-chatbot.git
cd rag-claude-chatbot

# Créer l'environnement virtuel
python -m venv .venv
.venv\Scripts\activate    # Windows

# Installer les dépendances
pip install -r requirements.txt
```

## 🔑 Configuration

1. Copiez `.env.example` en `.env`
2. Remplissez vos clés API :

```env
ANTHROPIC_API_KEY=sk-ant-api03-xxxxxxxxxxxx
OPENAI_API_KEY=sk-xxxxxxxxxxxx
```

> 💡 Obtenir une clé Anthropic : https://console.anthropic.com/

## 💻 Utilisation

### Étape 1 — Préparer vos documents

Placez vos fichiers `.txt` dans le dossier `docs/`.

### Étape 2 — Ingestion (une seule fois)

```bash
python src/1_ingestion_pipeline.py
```

Cela crée la base vectorielle dans `db/chroma_db/`.

### Étape 3 — Tester la récupération (optionnel)

```bash
python src/2_retrieval_pipeline.py
```

### Étape 4 — Chat conversationnel

```bash
python src/3_history_aware_generation.py
```

Exemple d'interaction :
```
Your question: What was the original name of Microsoft?
Answer: Microsoft was originally named "Micro-Soft"...

Your question: When was it founded?
Searching for: When was Microsoft founded?    ← reformulation automatique
Answer: Microsoft was founded on April 4, 1975...
```

## 🛠️ Stack technique

- **Python 3.10+**
- **LangChain** — orchestration RAG
- **langchain-anthropic** — intégration Claude
- **ChromaDB** — base vectorielle
- **OpenAI Embeddings** (text-embedding-3-small) — vectorisation
- **Claude Sonnet 4.6** — génération

## 🔬 Choix techniques

| Décision | Justification |
|---|---|
| **Claude vs GPT-4o** | Meilleur rapport qualité/prix, fenêtre de contexte 200k tokens |
| **ChromaDB vs FAISS** | Persistance locale simple, métadonnées riches |
| **Cosine similarity** | Standard pour les embeddings normalisés |
| **chunk_size=1000** | Équilibre entre contexte et précision de récupération |
| **Reformulation des questions** | Permet le multi-tours sans perdre le contexte |

## 📊 Performance

Sur un corpus de 5 documents (~50 pages au total) :

| Métrique | Valeur |
|---|---|
| Temps d'ingestion | ~10 secondes |
| Temps de récupération | < 100 ms |
| Temps de génération (Claude Sonnet) | ~2 secondes |
| Précision (Q&A factuel) | XX% (à mesurer sur votre corpus) |

## 🔄 Migration OpenAI → Anthropic

Ce projet a initialement été développé avec GPT-4o, puis migré vers Claude Sonnet pour démontrer la portabilité entre fournisseurs LLM. Le changement se résume à :

```python
# Avant
from langchain_openai import ChatOpenAI
model = ChatOpenAI(model="gpt-4o")

# Après
from langchain_anthropic import ChatAnthropic
model = ChatAnthropic(model="claude-sonnet-4-6", temperature=0)
```

Le reste du code (LangChain abstraction) reste identique.

## 📚 Ressources utiles

- [Documentation Anthropic](https://docs.claude.com/)
- [LangChain Docs](https://python.langchain.com/)
- [ChromaDB Docs](https://docs.trychroma.com/)

## 📄 Licence

MIT

## 👤 Auteur

**Pham Duc Thang** — Étudiant ingénieur, INSA Centre-Val de Loire
- LinkedIn : [votre profil]
- Email : votre.email@example.com
