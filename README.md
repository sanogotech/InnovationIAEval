
Ce notebook Python charge des données d'idées et de critères à partir de fichiers Excel, puis évalue chaque idée en fonction de critères pondérés en utilisant une similarité de cosinus sur des vecteurs TF-IDF. Le classement final est affiché dans l'ordre décroissant des scores.

### Explication des sections de code

1. **Chargement des bibliothèques et fonctions principales** :
   ```python
   import pandas as pd
   from sklearn.feature_extraction.text import TfidfVectorizer
   from sklearn.metrics.pairwise import cosine_similarity
   ```
   Les bibliothèques `pandas`, `TfidfVectorizer` et `cosine_similarity` sont importées pour la manipulation de données, la vectorisation de texte, et le calcul des similarités.

2. **Fonction `load_excel`** :
   ```python
   def load_excel(file_path, sheet_name=0):
       return pd.read_excel(file_path, sheet_name=sheet_name)
   ```
   Cette fonction permet de charger un fichier Excel en utilisant le chemin spécifié et renvoie un DataFrame.

3. **Fonction `evaluate_ideas`** :
   ```python
   def evaluate_ideas(ideas_df, criteria_df):
       criteria_weights = criteria_df.set_index('Critère')['Poids'].to_dict()
       idea_descriptions = ideas_df['Description'].tolist()
       tfidf_vectorizer = TfidfVectorizer()
       tfidf_matrix = tfidf_vectorizer.fit_transform(idea_descriptions)
       
       scores = []
       for i in range(len(idea_descriptions)):
           idea_vector = tfidf_matrix[i]
           total_score = 0
           for criterion, weight in criteria_weights.items():
               criterion_vector = tfidf_vectorizer.transform([criterion])
               similarity = cosine_similarity(idea_vector, criterion_vector)[0][0]
               total_score += similarity * weight
           scores.append(total_score)
       ideas_df['Score'] = scores
       ranked_ideas = ideas_df.sort_values(by='Score', ascending=False).reset_index(drop=True)
       return ranked_ideas
   ```
   Cette fonction :
   - Transforme les descriptions d'idées en vecteurs TF-IDF.
   - Calcule le score de chaque idée en fonction de sa similarité avec les critères pondérés.
   - Retourne les idées classées par score.

4. **Chargement et exécution du classement des idées** :
   ```python
   ideas_file = "/Users/adoumathurin/Desktop/Innovation/ideas.xlsx"
   criteria_file = "/Users/adoumathurin/Desktop/Innovation/criteria.xlsx"
   
   ideas_df = load_excel(ideas_file)
   criteria_df = load_excel(criteria_file)
   
   ranked_ideas = evaluate_ideas(ideas_df, criteria_df)
   print("Classement des idées d'innovation :")
   print(ranked_ideas[['Idée', 'Score']])
   ```
   Cette section charge les données d’idées et de critères, les évalue, puis affiche les idées classées par score.

### Formatage pour GitHub

Pour rendre ce code lisible sur GitHub :
1. Utiliser un fichier `.ipynb` pour le notebook afin que GitHub affiche le code, les cellules et les sorties.
2. Ajouter des commentaires aux cellules de code pour expliquer chaque étape.
3. Structurer le notebook avec des cellules Markdown qui décrivent chaque section de code (par exemple, “## Chargement des bibliothèques”, “## Fonction pour évaluer les idées”, etc.).
4. Enregistrer le notebook dans Jupyter Notebook avec l'extension `.ipynb` avant de le publier sur GitHub.

Ce format facilitera la lecture et la compréhension du code sur GitHub, tout en gardant le contexte clair pour les utilisateurs.

-----------------------

# Pour créer l'environnement virtuel en python:

python -m venv innov

# pour l'activer:  

source innov/bin/activate

# les bibliothèques que j'ai installé:  

pip install pandas scikit-learn openpyxl
