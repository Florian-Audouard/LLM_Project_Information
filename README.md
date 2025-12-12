### Mini projet : Système de Questions-Réponses avec Classement de Qualité

**Objectif** : Créer un système qui utilise un LLM pour répondre à des questions sur un domaine spécifique et évaluer la qualité des réponses.

**Durée estimée** : 3-4 heures

**Description du projet** :
1. **Créer un dataset de questions-réponses** : Définir 10-15 questions sur un domaine de votre choix (technologie, cinéma, histoire, etc.) avec leurs réponses de référence
2. **Tester différentes approches de prompt** : 
    - Zero-shot
    - One-shot 
    - Few-shot
3. **Comparer les paramètres de génération** : Tester différentes combinaisons de température, top_k, top_p pour voir leur impact
4. **Évaluation automatique** : Créer une fonction qui compare la réponse du LLM avec la réponse de référence (vous pouvez utiliser des métriques simples comme la longueur, les mots-clés communs, etc.)
5. **Visualisation des résultats** : Afficher un tableau comparatif des performances selon les différentes approches

**Livrables attendus** :
- Code documenté avec des commentaires
- Analyse des résultats en markdown (quelle approche fonctionne le mieux ?)
- Au moins 2 visualisations (graphiques ou tableaux)