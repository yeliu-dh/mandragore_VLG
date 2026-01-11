

# 📜 Localisation des mots clés dans des manuscrits médiévaux de la collection Mandragore 
# mandragore_vision_language_grounding

## 🎯 Objectif du projet
Collection MANDRAGORE de Bnf: https://mandragore.bnf.fr/

Ce projet a pour objectif de localiser des labels textuels (noms d’animaux) dans des images, en identifiant leur position précise dans des manuscrits médiévaux illustrés.

## 📂 Données

La collection de manuscrits "Latin 6838B": 63 images 

Métadonnées de "Latin 6838B" : df_latin.csv, contenant des annotations au niveau de l’image :

mots_cles : noms des animaux présents dans l’image (en latin)

keywords : traduction anglaise correspondante des labels

Chaque image peut contenir 0 ou plusieurs animaux.



## ⚙️ Méthode (Pipeline)

Notre approche se déroule en quatre étapes principales :


### 1️⃣DINO: Détection des bounding box des animales et des oiseaux 

Pour chaque image :

Nous utilisons DINO afin de détecter automatiquement les zones contenant des animaux ou des oiseaux

Cette étape produit des bounding boxes candidates


### 2️⃣ SAM3 :Segmentation de contours

À l’intérieur de chaque bounding box :

Nous appliquons SAM 3 pour extraire les contours précis des objets

Si SAM génère plus de 10 masques pour une image :

les masques spatialement proches sont fusionnés

les masques trop petits sont ignorés

Cela permet de réduire les fragments non pertinents.


### 3️⃣ CLIP : Association image–texte

Chaque ROI (Region of Interest) est découpée à partir des contours fournis par SAM

Les labels textuels associés à l’image sont transformés en prompts, par exemple :

“a medieval manuscript illustration of an animal {label}”

Les ROI visuelles et les prompts textuels sont encodés dans un espace vectoriel commun à l’aide de CLIP

Pour chaque ROI :

nous calculons la similarité cosinus avec tous les labels candidats

le label ayant le score le plus élevé est sélectionné si la similarité dépasse un seuil de 0,2


### 4️⃣ Visualisation et sauvegarde des résultats

Les contours, labels prédits et scores de confiance sont affichés sur l’image

Les résultats sont :

sauvegardés sous forme d’images annotées: output_clip/vis_clip

enregistrés dans un fichier de résultats (JSON):output_clip/result_clip.json
