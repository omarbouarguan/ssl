# Apprentissage Auto-Supervisé pour la Détection d'ARP Spoofing

Ce projet explore l'utilisation de l'Apprentissage Auto-Supervisé (Self-Supervised Learning - SSL), spécifiquement la tâche de **Prédiction de Caractéristiques Masquées**, pour apprendre des représentations utiles à partir de données de trafic ARP non étiquetées. Ces représentations sont ensuite utilisées pour entraîner un classifieur simple afin de détecter les attaques d'ARP Spoofing (tâche downstream).

## Contexte

L'ARP Spoofing est une attaque Man-in-the-Middle courante sur les réseaux locaux. Détecter ces attaques est crucial pour la sécurité réseau. L'approche SSL permet potentiellement d'améliorer la détection en exploitant la grande quantité de trafic réseau normal (souvent non étiqueté) disponible.

## Approche SSL Utilisée

* **Tâche Prétexte : Prédiction de Caractéristiques Masquées (Masked Feature Prediction)**
    * Le modèle (un Multi-Layer Perceptron - MLP) reçoit en entrée des paquets ARP dont certaines caractéristiques (ex: opcode, longueur, parties d'adresses) ont été aléatoirement masquées (remplacées par 0 ou une autre valeur).
    * Le modèle est entraîné à prédire les valeurs originales de ces caractéristiques masquées.
    * L'objectif est que l'encodeur du modèle apprenne les relations statistiques et structurelles typiques d'un paquet ARP normal.
* **Tâche Downstream : Classification Binaire**
    * L'encodeur pré-entraîné par SSL est "gelé" (ses poids ne sont plus modifiés).
    * Une petite tête de classification est ajoutée à la sortie de l'encodeur.
    * Cette tête est entraînée sur un petit sous-ensemble de données **étiquetées** (Normal vs Spoofing) pour effectuer la détection finale.

## Structure du Dépôt

* `ssl.ipynb` : Le notebook Jupyter  contenant tout le code pour le chargement, prétraitement, entraînement SSL, et évaluation downstream.
* `requirements.txt`: Liste des bibliothèques Python nécessaires.
* `Readme.md`: Ce fichier.
*  `arp_train_dataset`:`arp_test_dataset`  dataset train et test utilisés .
*  Lien vers la vidéo de présentation

## Dataset 

Le code suppose un fichier CSV (`arp_train_dataset.csv`) contenant des paquets ARP avec les colonnes suivantes :

* `frame_len`: Longueur de la trame (Numérique)
* `arp_opcode`: Code d'opération ARP (Catégorielle, ex: 1=request, 2=reply)
* `arp_src_hw_mac`: Adresse MAC source (Texte/Catégorielle)
* `arp_src_proto_ipv4`: Adresse IPv4 source (Texte/Catégorielle)
* `arp_dst_hw_mac`: Adresse MAC destination (Texte/Catégorielle)
* `arp_dst_proto_ipv4`: Adresse IPv4 destination (Texte/Catégorielle)
* `label`: Étiquette pour l'évaluation downstream (Numérique/Catégorielle, ex: 0=Normal, 1=Spoofing)


## Installation

1.  Clonez ce dépôt :
    ```bash
    git clone https://github.com/omarbouarguan/ssl/
    cd ssl
    ```
2.  Créez un environnement virtuel (recommandé) :
    ```bash
    python -m venv env
    source env/bin/activate  # Sur Linux/macOS
    # ou
    .\env\Scripts\activate  # Sur Windows
    ```
3.  Installez les dépendances :
    ```bash
    pip install -r requirements.txt
    ```

## Exécution

1.  Assurez-vous que votre dataset (ex: `arp_train_dataset.csv`) est présent et que le chemin `DATA_PATH` dans le notebook/script est correct.
2.  Adaptez la section de prétraitement du code à votre dataset.
3.  Exécutez le notebook Jupyter  :
    ```bash
    jupyter notebook notebook_ssl_arp.ipynb
    

## Résultats (Exemple)

      --- Évaluation sur le Set de Test ---
      
      --- Résultats de l'Évaluation Downstream (Test Set) ---
      Accuracy:  0.9310
      Precision (pour classe 1 - Spoofing): 0.8835
      Recall (pour classe 1 - Spoofing):    0.6149
      F1-Score (pour classe 1 - Spoofing):  0.7251
      
      Matrice de Confusion:
                Predicted 0 (Normal)  Predicted 1 (Spoofing)
      Actual 0 (Normal)    840                 12                
      Actual 1 (Spoofing)  57                  91

## Présentation Vidéo

https://drive.google.com/drive/u/0/folders/1mOOKJElXvS6rvIk-yqSL2ylhR3DbpR95
