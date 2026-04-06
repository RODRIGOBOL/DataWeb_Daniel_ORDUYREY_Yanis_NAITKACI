# 🚀 NASA History Knowledge Graph & RAG Pipeline

**Membres du groupe :** Daniel ORDUYREY & Yanis NAITKACI  


## 📝 Présentation du projet
Ce projet réalise une pipeline de bout-en-bout permettant de transformer des pages web non structurées (histoire de la NASA) en un **Graphe de Connaissances RDF** massif, enrichi par Wikidata, et interrogeable en langage naturel.

L'originalité de l'approche réside dans l'utilisation de modèles **Transformers** pour l'extraction, de modèles d'**Embeddings (KGE)** pour la prédiction de liens, et d'un système **RAG (Retrieval-Augmented Generation)** pour l'interface utilisateur.

---

## 🛠️ Pipeline Technique Détaillée

### 1. Extraction d'Information (IE)
* **Web Crawling :** Utilisation de `httpx` et `trafilatura` pour extraire le texte utile des archives de la NASA (sites `history.nasa.gov`), avec respect strict du fichier `robots.txt`.
* **NER (Reconnaissance d'Entités) :** Emploi du modèle de pointe `en_core_web_trf` (RoBERTa) pour identifier avec précision les Personnes, Organisations, Lieux et Dates.
* **Extraction de Relations :** * *Stratégie A (Syntaxique) :** Analyse des dépendances (Sujet-Verbe-Objet) via spaCy.
    * *Stratégie B (Co-occurrence) :** Capture de contextes sémantiques quand le lien syntaxique est trop complexe.

### 2. Construction & Alignement du Graphe (KG)
* **Modélisation RDF :** Création d'une ontologie personnalisée (TBox) sous `rdflib`.
* **Entity Linking :** Alignement automatique des entités extraites vers **Wikidata** via l'API MediaWiki (utilisation d'`owl:sameAs`).
* **Expansion Massive :** Enrichissement du graphe par des requêtes SPARQL fédérées sur Wikidata (méthode par "Hops").
    * **Résultat :** Un graphe de **52 716 triplets** et **196 prédicats distincts**.

### 3. Raisonnement & KGE (Knowledge Graph Embeddings)
* **Raisonnement Logique :** Intégration de règles **SWRL** via `owlready2` (ex: détection automatique des lauréats de prix Nobel de physique).
* **Apprentissage de Représentation (KGE) :** Entraînement de modèles vectoriels avec **PyKeen**.
    * **Modèles :** Comparaison entre **TransE** (translationnel) et **ComplEx** (espace complexe).
    * **Prédiction de liens :** Capacité du modèle à prédire des affiliations ou des prix non explicitement présents dans le texte.

### 4. Système RAG (Natural Language to SPARQL)
* **Interface LLM :** Utilisation de **Llama 3** (via Ollama) pour traduire les questions des utilisateurs en requêtes SPARQL 1.1.
* **Mécanisme d'Auto-Réparation :** Si la requête SPARQL générée est syntaxiquement fausse, une boucle de rétroaction renvoie l'erreur au LLM pour correction immédiate.
* **Schema Summary :** Injection dynamique des métadonnées du graphe (classes et propriétés disponibles) dans le prompt pour guider le modèle.

---

## 🚀 Installation & Configuration

### 1. Prérequis
* Python 3.10+
* [Ollama](https://ollama.com/) (pour le moteur RAG)

### 2. Installation de l'environnement
```bash
# Installation des bibliothèques Python
pip install -r requirements.txt

# Téléchargement du modèle Transformer pour le NER
python -m spacy download en_core_web_trf
```

### 3. Lancer le modèle local
```bash
ollama run llama3
```

---

## 📂 Organisation du Dépôt
```text
.
├── 1_Extraction_IE/           # Phase de Crawling et NER
├── 2_Knowledge_Graph/         # Construction RDF et Extension Wikidata
├── 3_KGE_Embeddings/          # Raisonnement SWRL et PyKeen
├── 4_RAG_System/              # Pipeline NL-to-SPARQL (Demo)
├── rapport/                   # Rapport final (PDF)
├── requirements.txt           # Liste des dépendances
└── README.md                  # Ce fichier
```

---

## 📊 Résultats Clés

| Métrique | Valeur |
| :--- | :--- |
| **Nombre de Triplets** | 52 716 |
| **Nombre d'Entités** | 26 266 |
| **Relations Wikidata** | 185 |
| **Modèle KGE optimal** | ComplEx |
| **Performance RAG** |  Réponses de questions |

---

**Daniel ORDUYREY & Yanis NAITKACI - 2026**
