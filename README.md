## Pré-requis
Avant de commencer, assurez-vous d'avoir les éléments suivants installés sur votre machine :

- Python (version 3.7 ou plus)
- Pip (gestionnaire de paquets Python)
  
### 📌 **README.md : Analyse des Données Netflix**

```markdown
# 📊 Projet d'Analyse des Données Netflix

## 🎯 Objectif du Projet
Ce projet vise à analyser et structurer la base de données des contenus Netflix en appliquant différentes techniques de **manipulation des données**, de **nettoyage**, et de **visualisation**.

Nous avons effectué les étapes suivantes :
✔️ Extraction des données depuis des fichiers CSV  
✔️ Nettoyage des données (`NaN`, incohérences, normalisation)  
✔️ Création d'identifiants uniques pour chaque élément (`type_id`, `country_id`...)  
✔️ Génération de requêtes SQL pour l'import en base de données  
✔️ Visualisation des tendances et des anomalies avec `matplotlib`  

---

## 📂 Fichiers du Projet
| Fichier | Description |
|---------|------------|
| `netflix_titles.csv` | Base principale des films et séries Netflix |
| `type_table.csv` | Liste des types (`Movie`, `TV Show`) avec `type_id` |
| `country_table.csv` | Liste des pays de production avec `country_id` |
| `listed_in_table.csv` | Liste des genres Netflix (`Comedy`, `Horror`, etc.) |
| `show_type.csv` | Relation entre `show_id` et `type_id` |
| `show_country.csv` | Relation entre `show_id` et `country_id` |
| `show_listed_in.csv` | Relation entre `show_id` et `listed_in_id` |

---

## 🛠️ Étapes du Projet

### **1️⃣ Chargement des Données**
Nous avons commencé par charger les fichiers CSV contenant les données Netflix.  
Les données contiennent des informations sur les films et séries, telles que :
- **Le titre**
- **Le pays de production**
- **Le genre**
- **La date d’ajout sur Netflix**
- **L’année de sortie**

📌 **Technologies utilisées** : `pandas` pour la manipulation des données.

```python
import pandas as pd
file_path = "dataframes/netflix_titles.csv"
netflix_data = pd.read_csv(file_path, delimiter=';')
```

---

### **2️⃣ Nettoyage et Préparation des Données**
Les données brutes contenaient des **valeurs manquantes (`NaN`)** et des **données mal formatées**.  
Nous avons effectué plusieurs traitements :
- Suppression ou remplacement des valeurs `NaN`
- Conversion des dates (`date_added`) et des années (`release_year`)
- Détection et correction des erreurs où `date_added` < `release_year`
- Normalisation des données (`country`, `listed_in`) pour éviter les valeurs multiples en une seule cellule

📌 **Exemple de correction des valeurs nulles** :

```python
# Remplacement des valeurs manquantes dans 'rating' par 'Unknown'
netflix_data['rating'] = netflix_data['rating'].fillna('Unknown')
```

---

### **3️⃣ Génération des Identifiants Uniques**
Pour éviter les **données dupliquées**, nous avons généré des **identifiants uniques (`id`)** pour les catégories suivantes :
- **Les types (`Movie`, `TV Show`)**
- **Les pays (`USA`, `France`, `Japan`, etc.)**
- **Les genres (`Comedy`, `Drama`, etc.)**
- **Les réalisateurs et acteurs**
- **Les années de sortie**
- **Les dates d’ajout sur Netflix**

📌 **Exemple de génération d'ID unique pour les types** :

```python
def create_id_table(df, column_name, id_name):
    """Crée une table avec ID unique basé sur une colonne spécifique"""
    unique_values = df[[column_name]].drop_duplicates().reset_index(drop=True)
    unique_values[id_name] = range(1, len(unique_values) + 1)
    return unique_values

type_table = create_id_table(netflix_data, "type", "type_id")
```

---

### **4️⃣ Création des Relations entre les Tables**
Une fois les identifiants générés, nous avons créé des **tables relationnelles** pour faciliter les jointures.  
Chaque film ou série est **associé à son type, son pays et son genre via son `show_id`**.

📌 **Exemple de création de la table `show_type` (relation `show_id ↔ type_id`)** :

```python
show_type = netflix_data[["show_id", "type"]].merge(type_table, on="type", how="left")[["show_id", "type_id"]]
```

📌 **Autres relations créées** :
| Table | Contenu |
|---------|------------|
| `show_type.csv` | Relation entre `show_id` et `type_id` |
| `show_country.csv` | Relation entre `show_id` et `country_id` |
| `show_listed_in.csv` | Relation entre `show_id` et `listed_in_id` |

---

### **5️⃣ Génération des Requêtes SQL**
Nous avons généré **des requêtes SQL** permettant d'insérer les données normalisées dans une base relationnelle.

📌 **Exemple de requête SQL générée** :
```sql
INSERT INTO show_title (show_id, title_id) VALUES ('s1', 101);
```

---

### **6️⃣ Création des Visualisations**
Pour mieux comprendre les données Netflix, nous avons utilisé **Matplotlib** et **Seaborn** pour visualiser les tendances.

📌 **Graphique : Présence des valeurs nulles**
```python
import matplotlib.pyplot as plt

missing_values = netflix_data.isnull().sum()
missing_percentage = (missing_values / len(netflix_data)) * 100

plt.figure(figsize=(12, 6))
plt.bar(missing_percentage.index, missing_percentage.values, color="red", alpha=0.7)
plt.xlabel("Colonnes")
plt.ylabel("Pourcentage de valeurs nulles (%)")
plt.title("Présence des valeurs nulles dans Netflix Data")
plt.xticks(rotation=45)
plt.show()
```

📌 **Autres analyses effectuées** :
- 📊 Nombre de films ajoutés par année
- 🎭 Genres les plus populaires
- 🛠 Correction des erreurs `date_added < release_year`

---

## 🛠️ Technologies Utilisées
- **Python** : Manipulation des données avec `pandas`
- **Matplotlib / Seaborn** : Création de visualisations
- **SQL** : Génération des requêtes d'insertion en base de données

---



## 📌 Lancer le Projet
1. **Installer les bibliothèques nécessaires**  
```bash
pip install pandas matplotlib seaborn sqlalchemy
```
2. **Exécuter le script principal**
```bash
python netflix_analysis.py
```
3. **Consulter les fichiers de sortie dans le dossier `dataframes/`**

---
