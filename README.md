# Projet LLM : Système de Questions-Réponses avec Évaluation Automatique

## 📋 Description du Projet

Ce projet implémente un système de questions-réponses basé sur un modèle de langage (LLM) avec une évaluation automatique de la qualité des réponses. L'objectif est d'explorer l'impact des différents paramètres de génération et des techniques de prompting (zero-shot, few-shot) sur la qualité des réponses générées.

---

## 🏗️ Architecture du Code

### 1. Dataset de Questions-Réponses

J'ai créé un dataset de **15 questions-réponses** sur le domaine de la **programmation informatique**. Les sujets couverts incluent :

-   Concepts fondamentaux (variables, fonctions, boucles)
-   Programmation orientée objet
-   Développement web (frontend/backend, API)
-   Outils de développement (Git, SQL)
-   Bonnes pratiques (debugging, algorithmes)

### 2. Classes Principales

#### Classe `Data`

Conteneur simple pour stocker une paire question/réponse avec les méthodes `__str__` et `__eq__` pour la représentation et la comparaison.

#### Classe `Dataset`

Gère la collection de données avec les fonctionnalités suivantes :

-   `get_random_entry()` : Sélection aléatoire d'une entrée
-   `generate_random_entries()` : Génération d'entrées aléatoires uniques
-   `build_prompt()` : Construction du prompt formaté avec les exemples few-shot
-   `generate_prompt()` : Génération d'un prompt complet avec un nombre d'exemples spécifié
-   `generate_multiple_prompts()` : Génération de multiples prompts pour différents nombres de shots

#### Classe `LLM`

Wrapper pour le modèle de langage qui permet :

-   Chargement du tokenizer et du modèle
-   Génération de réponses avec paramètres configurables (température, top_k, top_p)
-   Nettoyage automatique des balises `<think>...</think>` dans les réponses

#### Classe `Evaluator`

Évaluateur utilisant la **similarité cosinus** entre les embeddings pour mesurer la qualité sémantique des réponses.

#### Classe `ParameterPipeline`

Pipeline d'expérimentation permettant de :

-   Tester toutes les combinaisons de paramètres
-   Calculer les scores moyens, minimum et maximum
-   Afficher les résultats avec un gradient de couleur

---

## 🤖 Modèles Utilisés

| Modèle                                   | Rôle          | Description                                           |
| ---------------------------------------- | ------------- | ----------------------------------------------------- |
| `Gensyn/Qwen2.5-1.5B-Instruct`           | LLM principal | Modèle de génération de texte léger (1.5B paramètres) |
| `sentence-transformers/all-MiniLM-L6-v2` | Embeddings    | Modèle pour calculer la similarité sémantique         |

---

## 📊 Métrique d'Évaluation

### Similarité Cosinus des Embeddings

La métrique utilisée est la **similarité cosinus** entre les embeddings de la réponse générée et de la réponse de référence :

$$\text{score} = \cos(\theta) = \frac{\mathbf{A} \cdot \mathbf{B}}{|\mathbf{A}| \times |\mathbf{B}|}$$

Où :

-   $\mathbf{A}$ = embedding de la réponse générée
-   $\mathbf{B}$ = embedding de la réponse de référence

**Interprétation du score :**

-   **1.0** : Réponses sémantiquement identiques
-   **0.5-0.8** : Réponses partiellement correctes
-   **< 0.5** : Réponses peu pertinentes

Cette approche permet d'évaluer la **qualité sémantique** plutôt qu'une correspondance exacte des mots.

---

## ⚙️ Paramètres Testés

### Paramètres de Prompting

| Paramètre         | Valeurs testées | Description                      |
| ----------------- | --------------- | -------------------------------- |
| `number_of_shots` | 0, 2, 4         | Nombre d'exemples dans le prompt |

### Paramètres de Génération

| Paramètre     | Valeurs testées | Description                                        |
| ------------- | --------------- | -------------------------------------------------- |
| `temperature` | 0.0, 0.5, 1.0   | Contrôle la créativité (0 = déterministe)          |
| `top_k`       | 10, 50, 100     | Nombre de tokens candidats à considérer            |
| `top_p`       | 0.7, 0.95       | Seuil de probabilité cumulative (nucleus sampling) |

**Note importante :** Pour `temperature=0.0`, le modèle utilise le décodage glouton (greedy decoding), rendant les paramètres `top_k` et `top_p` sans effet. Le code optimise cela en évitant les combinaisons redondantes.

---

## 🔬 Méthodologie Expérimentale

1. **Génération des prompts** : Pour chaque nombre de shots différent, cinq prompts sont générés.
   Les prompts avec un nombre de shots plus élevé reprennent la base de ceux générés précédemment.

2. **Génération des réponses** : Le LLM génère une réponse pour chaque prompt
3. **Évaluation** : Chaque réponse est comparée à la référence via similarité cosinus
4. **Agrégation** : Calcul de la moyenne, minimum et maximum des scores

### Structure du Prompt

```
QUESTION:
[Question exemple 1]
ANSWER:
[Réponse exemple 1]

QUESTION:
[Question exemple 2]
ANSWER:
[Réponse exemple 2]

QUESTION:
[Question à répondre]
ANSWER:
```

---

## 📈 Résultats et Analyse

### Observations Attendues

1. **Impact du Few-Shot Learning** :

    - Le zero-shot (0 exemples) donne généralement des résultats moins précis
    - Le few-shot (2-4 exemples) améliore la compréhension du format attendu

2. **Impact de la Température** :

    - `temperature=0.0` : Réponses cohérentes mais parfois répétitives
    - `temperature=0.5` : Bon équilibre créativité/précision
    - `temperature=1.0` : Plus de variabilité, parfois moins précis

3. **Impact de top_k et top_p** :
    - Valeurs faibles : Réponses plus conservatrices
    - Valeurs élevées : Plus de diversité dans les réponses

### Visualisation des Résultats

Le pipeline affiche un **tableau interactif** avec :

-   Gradient de couleur (rouge → jaune → vert) sur le score moyen
-   Tri par score décroissant
-   Scores min/max pour évaluer la variance

---

## 🎯 Conclusion

Ce projet démontre l'importance du **réglage des hyperparamètres** dans les systèmes de génération de texte :

1. **Le few-shot learning** améliore significativement la qualité des réponses en fournissant un contexte et un format de réponse attendu au modèle.

2. **La température** est le paramètre le plus impactant : une valeur de 0 garantit la reproductibilité, tandis que des valeurs plus élevées introduisent de la variabilité.

3. **L'évaluation par embeddings** offre une mesure robuste de la similarité sémantique, plus pertinente qu'une simple comparaison de chaînes de caractères.

4. **L'automatisation des tests** via le `ParameterPipeline` permet d'explorer efficacement l'espace des hyperparamètres et d'identifier les configurations optimales.

---

## 📁 Structure du Projet

```
LLM_Project_Information/
├── main.ipynb      # Notebook principal avec tout le code
└── README.md       # Documentation du projet
```

---

## 🛠️ Dépendances

```python
torch                    # Framework deep learning
transformers             # Modèles Hugging Face
sentence-transformers    # Embeddings de phrases
pandas                   # Manipulation de données
tqdm                     # Barres de progression
```

---

## 🚀 Exécution

1. Exécuter les cellules d'installation des dépendances
2. Charger les modèles (LLM et embeddings)
3. Lancer le pipeline d'expérimentation
4. Analyser le tableau de résultats généré

---

_Projet réalisé dans le cadre du cours sur les Large Language Models (LLM)_
