# Setting up an MLOps project step by step 🚀

Bienvenue dans le guide de configuration ! Ici, nous allons décrire les étapes nécessaires pour configurer et mettre en œuvre les différentes premières étapes du pipeline MLOps. Suivez-nous et remplissez les détails au fur et à mesure que vous avancez dans chaque étape dans le carnet `workflow_steps.ipynb`.

Vous pouvez commencer par vous familiariser avec l'architecture du projet : 

```bash
+---.dvc
|  
+---.github
|   \---workflows
+---data
|           
+---logs
|       
+---metrics
+---notebooks
|       workflow_steps.ipynb
|       
+---src
|   |   common_utils.py
|   |   config.py
|   |   config.yaml
|   |   config_manager.py
|   |   entity.py
|   |   __init__.py
|   |   
|   +---app
|   |       app.py
|   |       
|   +---data_module_def
|   |   |   data_ingestion.py
|   |   |   data_transformation.py
|   |   |   data_validation.py
|   |   |   schema.yaml
|   |   |   __init__.p
|   |           
|   +---models_module_def
|   |       model_evaluation.py
|   |       model_trainer.py
|   |       params.yaml
|   |       __init__.py
|   |       
|   +---pipeline_steps
|   |       prediction.py
|   |       stage01_data_ingestion.py
|   |       stage02_data_validation.py
|   |       stage03_data_transformation.py
|   |       stage04_model_trainer.py
|   |       stage05_model_evaluation.py
|   |       __init__.py
|           
+---templates
|       index.html
|       login.html
|       register.html
|       results.html
|       
+---users
|   |   users.json
| 
|   .dvcignore
|   .gitignore
|   custom_logger.py
|   dvc.lock
|   dvc.yaml
|   README.md
|   requirements.txt
|   tree
|   __init__.py
|  
```
À travers ce projet, nous allons travailler avec un jeu de données sur le vin 🍷 L'objectif sera de mettre en place un modèle qui permettra de prédire sa qualité, le tout en respectant les bonnes pratiques de MLOps en termes de contrôle de version, d'utilisation de pipelines et des outils les plus couramment utilisés.

Tout d'abord, il faut commencer par forker et cloner le projet. Ensuite, vous devez créer un environnement virtuel dans lequel vous installerez toutes les bibliothèques nécessaires. Celles-ci se trouvent dans le fichier `requirements.txt` 📚 Assurez-vous d'activer votre environnement virtuel avant de l'utiliser 😉 .

Passons maintenant en revue les fichiers facilement disponibles.

## Configuration Files 📘
Regardons rapidement les trois fichiers `yaml` dans notre dossier `src`.

Vous pouvez commencer par regarder le `config.yaml` 📂 Vous verrez qu'il définit les chemins vers les différents fichiers qui seront utilisés et créés dans chacune des étapes que nous allons mettre en place.

Ensuite, dans le dossier `data_module_def` nous avons le `schema.yaml` 🗃️ Si vous y jetez un coup d'oeil vous verrez qu'il définit les types de données pour chaque colonne dans l'ensemble de données avec lequel nous allons travailler.

Enfin, dans le dossier `models_module_def` vous pouvez jeter un coup d'oeil à `params.yaml` 📊 Ce fichier définit les hyperparamètres du modèle que nous allons mettre en place.

⚠️ Le fichier `src/config.py` définit les variables globales contenant les chemins vers ces fichiers yaml pour faciliter leur accès. 

## Common Utilities 🛠️ 
Dans `src/common_utils.py` nous avons des fonctions réutilisables :

* read_yaml(filepath : str) -> dict
* create_directories(paths : List[str])
* save_json(path : str, data : dict)
* load_json

Ces utilitaires simplifieront le chargement des configurations et garantiront la création des répertoires nécessaires.

Mettons-nous au travail !

## The task
Pour les étapes suivantes, vous pouvez utiliser le notebook `workflow_steps.ipynb` pour vous guider à travers le code que vous devrez écrire sur chacun des fichiers correspondants 🧑‍💻 La tâche consiste en cinq étapes qui vous aideront à mettre en œuvre un flux de travail modulaire d'un projet MLOps.

## Step 1: Define Configuration Classes 🧩
Commencez par écrire les objets de configuration dans `src/entity.py`. Ces configurations aideront à gérer les réglages et les paramètres requis pour chaque étape d'une manière propre et organisée. En utilisant la section *Etape 1* du notebook, définissez les `dataclasses` pour les objets de configuration :

* DataIngestionConfig
* DataValidationConfig
* DataTransformationConfig
* ModelTrainerConfig
* ModelEvaluationConfig

## Step 2: Configuration Manager 🗄️
Créez la classe `ConfigurationManager` dans `src/config_manager.py` en utilisant l'*Etape 2* du notebook. Cette classe va :

* Lire les chemins depuis `config.yaml`.
* Lire les hyperparamètres depuis `params.yaml`.
* Lire les types de données depuis `schema.yaml`.
* Créer des objets de configuration pour chacune des étapes à l'aide des objets définis à l'étape précédente : DataIngestionConfig, DataValidationConfig, ModelTrainerConfig et ModelEvaluationConfig.
* Créer les dossiers nécessaires.

⚠️ Faites attention au `mlflow_uri` sur le `get_model_evaluation_config`, assurez-vous de l'adapter avec vos propres credentials dagshub. 

## Step 3: Data module definition and model module definition.
En utilisant la section *Etape 3* du cahier, dans les fichiers correspondants du dossier `src/data_module_def`, créez :

1. Module d'ingestion de données 📥

Cette classe va :
* Télécharger l'ensemble de données dans le dossier approprié.
* Dézipper le jeu de données dans le dossier approprié.

2. Module de validation des données ✅

Cette classe va :
* Valider les colonnes par rapport au schéma. Facultatif : vous pouvez également vérifier le type d'information.
* Produire un fichier texte indiquant si les données sont valides.

3. Module de transformation des données 🔄

Cette classe va :
* Diviser les données en ensembles de formation et de test.
* Enregistrer les fichiers csv correspondants dans le dossier approprié.

De même, dans les fichiers correspondants du dossier `src/models_module_def`, créer :

1. Module d'entraînement au modèle 🏋️‍♂️

Cette classe va :
* Entraîner le modèle en utilisant les hyperparamètres spécifiés dans `params.yaml`.
* Sauvegarder le modèle formé dans le dossier approprié.

2. Module d'évaluation du modèle 📝

Cette classe va
* Évaluer le modèle et les métriques d'enregistrement en utilisant MLFlow

## Step 4: Pipeline Steps 🚀
En utilisant le *Step 4* du notebook, dans `src/pipeline_steps` créez des scripts pour chaque étape du pipeline afin d'instancier et d'exécuter les processus :

* stage01_data_ingestion.py
* stage02_data_validation.py
* stage03_data_transformation.py
* stage04_model_trainer.py
* stage05_model_evaluation.py

Pour chaque script, vous devez compléter les classes avec deux méthodes : une `__init__` qui ne fait rien, et une `main` où vous devez implémenter le code dans chaque section de l'*Étape 4* du notebook.

## Step 5: Use DVC to connect the different stages of your pipeline 🦉
Commencez par configurer DagsHub en tant que stockage distant via DVC.

Pour initialiser le repo DVC :
``bash
dvc init
``
Initialiser le repo sous S3 :
```bash
dvc remote add origin s3://dvc
dvc remote modify origin endpointurl https://dagshub.com/stephaneMartinez/MLOps_Wines_Model.s3
dvc remote default origin
```

Initilialiser les credentials (dans dagshub "remote" "Data" > "DVC" > "Setup Credentials")
```bash
dvc remote modify origin --local access_key_id ddcdf3d811ab7bb6d3437387618f9956f72a9254
dvc remote modify origin --local secret_access_key ddcdf3d811ab7bb6d3437387618f9956f72a9254
```

Utilisez dvc pour connecter les différentes étapes de votre pipeline.

Par exemple, la commande pour ajouter la première étape du pipeline est la suivante : 


Pour initiliaser le pipeline :
```bash
dvc stage add -n data_ingestion \
              -d src/config.yaml \
              -d src/pipeline_steps/stage01_data_ingestion.py \
              -o data/raw/winequality-red.csv \
              python src/pipeline_steps/stage01_data_ingestion.py
```

```bash
dvc stage add -n data_validation \
              -d src/config.yaml \
              -d data/raw/winequality-red.csv \
              -d  src/pipeline_steps/stage02_data_validation.py \
              -o data/status.txt \
              python src/pipeline_steps/stage02_data_validation.py
```

```bash
dvc stage add -n data_transformation \
              -d src/config.yaml \
              -d data/raw/winequality-red.csv \
              -d data/status.txt \
              -d src/pipeline_steps/stage03_data_transformation.py \
              -o data/processed/X_test.csv \
              -o data/processed/X_train.csv \
              -o data/processed/y_test.csv \
              -o data/processed/y_train.csv \
              python src/pipeline_steps/stage03_data_transformation.py
```

```bash
dvc stage add -n model_trainer \
              -d src/config.yaml \
              -d data/processed/X_train.csv \
              -d data/processed/y_train.csv \
              -d src/pipeline_steps/stage04_model_trainer.py \
              python src/pipeline_steps/stage04_model_trainer.py
```

```bash
dvc stage add -n model_evaluation \
              -d src/config.yaml \
              -d data/processed/X_test.csv \
              -d data/processed/y_test.csv \
              -d models/model.joblib \
              -d src/pipeline_steps/stage05_model_evaluation.py \
              -o metrics/metrics.json \
              python src/pipeline_steps/stage05_model_evaluation.py
```

You can run the pipeline through the command `dvc repro`.

Congratulations! 🎉 Now that you have a structured and well-defined MLOps project you're ready for the next step which is the creation of the API.

Each step is modularized, making it easy to maintain, extend, and scale your Machine Learning pipeline. 
