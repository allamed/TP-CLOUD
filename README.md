# Cas pratiques — Session 1 : Comprendre le cloud

Formation CRTS · Le cloud au service de la télédétection et des SIG

## Avant de commencer

- Ouvrez **QGIS** et un **navigateur web** (Firefox de préférence).
- Ouvrez ce fichier à l'écran : vous copierez les liens, ne les retapez pas.
- Aucun compte n'est nécessaire. Aucune donnée n'est à télécharger.
- Ne chargez aucune couche de votre administration pendant ces exercices.

Toutes les manipulations portent sur une même image : **Rabat vue par le satellite Sentinel-2 le 16 novembre 2026**.

### Liens utilisés

| Nom | Contenu | Taille |
| --- | --- | --- |
| TCI | Image en couleurs naturelles | 348 Mo |
| B04 | Bande rouge | 234 Mo |
| B08 | Bande proche infrarouge | 234 Mo |
| Aperçu | Vignette JPEG | 49 Ko |

**TCI**

```
https://sentinel-cogs.s3.us-west-2.amazonaws.com/sentinel-s2-l2a-cogs/29/S/QT/2026/11/S2B_29SQT_20261116_0_L2A/TCI.tif
```

**B04**

```
https://sentinel-cogs.s3.us-west-2.amazonaws.com/sentinel-s2-l2a-cogs/29/S/QT/2026/11/S2B_29SQT_20261116_0_L2A/B04.tif
```

**B08**

```
https://sentinel-cogs.s3.us-west-2.amazonaws.com/sentinel-s2-l2a-cogs/29/S/QT/2026/11/S2B_29SQT_20261116_0_L2A/B08.tif
```

**Aperçu**

```
https://sentinel-cogs.s3.us-west-2.amazonaws.com/sentinel-s2-l2a-cogs/29/S/QT/2026/11/S2B_29SQT_20261116_0_L2A/preview.jpg
```

---

## A. Ouvrir une image de 348 Mo en quelques secondes

**Outil :** QGIS · **Durée :** 4 min

**Objectif :** afficher une image satellite stockée dans le cloud sans la télécharger.

1. Dans QGIS, ouvrez le **Gestionnaire des sources de données** (Ctrl+L).
2. Choisissez l'onglet **Raster**.
3. Dans « Type de source », sélectionnez **Protocole : HTTP(S), cloud, etc.**
4. Dans « Type », laissez **HTTP/HTTPS/FTP**.
5. Collez le lien **TCI** dans le champ **URI**, puis cliquez sur **Ajouter** et fermez la fenêtre.
6. L'image entière apparaît. Zoomez sur l'estuaire du Bouregreg, puis sur un quartier de Rabat.
7. Faites un clic droit sur la couche, puis **Propriétés > Information**. Relevez les dimensions de l'image et le chemin du fichier.

**À observer**

- Combien de temps l'image a-t-elle mis à s'afficher ?
- Que se passe-t-il à chaque zoom ?
- Par quoi commence le chemin du fichier ?

**À retenir :** le fichier est resté sur le serveur. QGIS n'a lu que la partie affichée.

> Ne collez pas ce lien dans la barre d'adresse du navigateur : il téléchargerait les 348 Mo.

---

## B. Interroger un catalogue d'images depuis le navigateur

**Outil :** navigateur web (Firefox de préférence) · **Durée :** 5 min

**Objectif :** comprendre qu'un catalogue STAC est une simple adresse web, et que sa réponse est faite pour être lue par des logiciels.

> **Ce que vous allez voir n'est pas une carte.** Le navigateur affiche du texte structuré, au format **JSON**. C'est normal : c'est la réponse brute du catalogue, celle que QGIS, Earth Engine ou un script reçoivent et interprètent à votre place. Le but de l'exercice est d'y trouver trois informations.

### 1. Lire l'adresse avant de l'ouvrir

L'adresse ci-dessous est une question posée au catalogue. Chaque morceau après le `?` est un critère :

```
https://earth-search.aws.element84.com/v1/search?collections=sentinel-2-l2a&bbox=-6.90,33.95,-6.78,34.05&datetime=2026-11-01T00:00:00Z/2026-11-20T23:59:59Z&limit=3
```

| Morceau de l'adresse | Signification |
| --- | --- |
| `earth-search.aws.element84.com/v1/search` | Le catalogue interrogé, et l'action : rechercher |
| `collections=sentinel-2-l2a` | Quelles images : Sentinel-2, niveau 2A (corrigé de l'atmosphère) |
| `bbox=-6.90,33.95,-6.78,34.05` | Où : un rectangle sur Rabat (ouest, sud, est, nord, en degrés) |
| `datetime=2026-11-01.../2026-11-20...` | Quand : du 1er au 20 novembre 2026 |
| `limit=3` | Combien de réponses : 3 au maximum |

En clair : « Donne-moi au plus 3 images Sentinel-2 sur Rabat, prises entre le 1er et le 20 novembre 2026. »

### 2. Ouvrir la réponse

1. Ouvrez **Firefox** et collez l'adresse dans la barre d'adresse. Validez.
2. Firefox affiche la réponse sous forme d'arborescence repliable. Si vous voyez un bloc de texte compact, cliquez sur l'onglet **JSON** en haut de la page.
3. Avec **Chrome** ou **Edge**, cochez la case **Impression élégante** (« Pretty-print ») en haut de la page pour que le texte soit indenté.

### 3. Trouver les trois informations

La réponse contient une liste `features` : **une entrée par image trouvée**, numérotées `0`, `1`, `2`. Dépliez l'entrée `0`, puis :

| À chercher | Où le trouver | Ce que c'est |
| --- | --- | --- |
| La date | `properties` > `datetime` | Date et heure de prise de vue |
| Les nuages | `properties` > `eo:cloud_cover` | Pourcentage de l'image couvert de nuages |
| Le fichier | `assets` > `visual` > `href` | Le lien du fichier image en couleurs naturelles |

Astuce Firefox : tapez `cloud_cover` dans le champ **Filtrer le JSON**, en haut à droite, pour n'afficher que les pourcentages de nuages des trois images.

### 4. Faire le lien avec le cas A

1. Copiez le lien trouvé dans `assets` > `visual` > `href`.
2. Comparez-le au lien **TCI** utilisé au cas A. C'est le même fichier : au cas A, vous l'aviez reçu tout fait ; ici, vous l'avez trouvé par une recherche.
3. Toujours dans `assets`, dépliez `thumbnail` et ouvrez son lien `href` dans un nouvel onglet : c'est l'aperçu JPEG de 49 Ko, qui suffit pour juger une image avant de l'utiliser.

### 5. Changer la question

Dans la barre d'adresse, modifiez un critère et validez à nouveau :

- remplacez `limit=3` par `limit=10` : combien d'images obtenez-vous ?
- ou changez les quatre nombres de `bbox` pour interroger une autre ville.

| Ville | bbox |
| --- | --- |
| Casablanca | `-7.70,33.50,-7.50,33.65` |
| Marrakech | `-8.10,31.55,-7.90,31.70` |
| Tanger | `-5.90,35.70,-5.70,35.82` |

**À observer**

- Quelle est l'image la moins nuageuse des trois ?
- Quelles autres bandes sont disponibles dans `assets` (`red`, `nir`, etc.) ?

**À retenir :** une recherche d'images est une simple adresse web. La réponse, en JSON, donne pour chaque image sa fiche (date, nuages) et les liens de ses fichiers. Vous n'aurez pas à la lire au quotidien : QGIS la lit pour vous (cas E). Mais c'est ce même langage commun qui permet d'interroger de la même façon les catalogues de Copernicus, de Microsoft ou de la NASA.

---

## C. La preuve par le réseau

**Démonstration du formateur** · **Durée :** 3 min

**Objectif :** vérifier que seule une petite partie du fichier transite par le réseau.

Suivez la démonstration à l'écran. Le formateur interroge le fichier **TCI** de 348 Mo et affiche le volume réellement transféré.

**À noter pendant la démonstration**

- Volume transféré pour lire la description du fichier : ……………
- Volume transféré pour extraire un carré d'environ 10 km de côté : ……………
- Taille totale du fichier : 348 Mo

**À retenir :** le fichier est découpé en tuiles, avec des aperçus pour les vues d'ensemble. Le logiciel ne demande que les tuiles utiles.

*Facultatif, si vous disposez du terminal OSGeo4W Shell :*

```
gdalinfo /vsicurl/https://sentinel-cogs.s3.us-west-2.amazonaws.com/sentinel-s2-l2a-cogs/29/S/QT/2026/11/S2B_29SQT_20261116_0_L2A/TCI.tif --config CPL_VSIL_SHOW_NETWORK_STATS YES
```

---

## D. Calculer un NDVI sans rien télécharger

**Outil :** QGIS · **Durée :** 5 min

**Objectif :** produire un indice de végétation à partir de deux fichiers distants et n'enregistrer que le résultat.

1. Chargez les liens **B04** et **B08** comme au cas A (un lien à la fois). Les couches s'appellent `B04` et `B08`.
2. Zoomez sur une zone qui mêle ville et végétation, par exemple la ceinture verte de Rabat ou la forêt de la Maâmora.
3. Ouvrez le menu **Raster > Calculatrice raster**.
4. Cochez l'option de **création d'un raster à la volée** (sans écriture sur le disque).
5. Saisissez l'expression suivante, puis validez :

```
("B08@1" - "B04@1") / ("B08@1" + "B04@1")
```

6. Ouvrez le style de la nouvelle couche. Choisissez **Pseudo-couleur à bande unique**, avec un minimum de 0 et un maximum de 0,8.
7. Faites un clic droit sur la couche, puis **Exporter > Enregistrer sous**. Limitez l'emprise à **l'emprise du canevas de la carte** et enregistrez.

**À observer**

- Quelles valeurs prennent la végétation, le bâti et l'eau ?
- Quelle est la taille du fichier enregistré, comparée aux 468 Mo des deux bandes ?

**À retenir :** le calcul s'est fait sur des données restées dans le cloud. Seul le résultat est sur votre poste. C'est ce fichier que vous pourrez croiser, en interne, avec les couches de votre administration.

---

## E. Chercher des images depuis QGIS

**Outil :** QGIS 3.42 ou plus récent · **Durée :** 4 min · *Cas complémentaire*

**Objectif :** interroger le catalogue sans quitter le SIG.

1. Ouvrez le **Gestionnaire des sources de données** (Ctrl+L), onglet **STAC**.
2. Créez une **nouvelle connexion**. Nom : `Earth Search`. Adresse :

```
https://earth-search.aws.element84.com/v1
```

3. Connectez-vous, puis ouvrez les **filtres**.
4. Sélectionnez la collection **Sentinel-2 Level-2A**, limitez la recherche à **l'emprise de la carte** et au mois en cours.
5. Parcourez les résultats : l'emprise de chaque image se dessine sur la carte.
6. Choisissez une image peu nuageuse et ajoutez sa couche **visual** à la carte.

**À retenir :** c'est la recherche du cas B, faite depuis QGIS.

---

## F. Mesurer l'occupation du sol de sa zone

**Outil :** QGIS · **Durée :** 6 min · *Cas complémentaire*

**Objectif :** obtenir des statistiques d'occupation du sol sur une zone dessinée à la main.

1. Chargez comme au cas A la carte mondiale d'occupation du sol ESA WorldCover 2021 (tuile Rabat, Casablanca, Kénitra) :

```
https://esa-worldcover.s3.eu-central-1.amazonaws.com/v200/2021/map/ESA_WorldCover_10m_2021_v200_N33W009_Map.tif
```

2. Créez une couche temporaire : **Couche > Créer une couche > Nouvelle couche temporaire en mémoire**, de type **Polygone**.
3. Dessinez un polygone sur la zone de votre choix, puis enregistrez les modifications.
4. Ouvrez la **Boîte à outils de traitements** et lancez **Histogramme zonal**.
5. Couche raster : la tuile WorldCover. Couche vecteur : votre polygone. Exécutez.
6. Ouvrez la table d'attributs du résultat : chaque colonne donne le nombre de pixels d'une classe.

| Code | Classe |
| --- | --- |
| 10 | Arbres |
| 20 | Arbustes |
| 30 | Herbacées |
| 40 | Cultures |
| 50 | Bâti |
| 60 | Sol nu ou végétation éparse |
| 80 | Eau permanente |
| 90 | Zone humide herbacée |

Un pixel couvre environ 100 m² : 100 pixels représentent environ un hectare.

**À retenir :** une statistique par zone, à partir d'un fichier mondial que vous ne possédez pas.

---

## G. Lire le relief pour la gestion de crise

**Outil :** QGIS · **Durée :** 4 min · *Cas complémentaire*

**Objectif :** afficher le relief et les pentes à partir d'un modèle numérique de terrain distant.

1. Chargez comme au cas A le modèle Copernicus DEM à 30 m (tuile 34° N, 7° O) :

```
https://copernicus-dem-30m.s3.amazonaws.com/Copernicus_DSM_COG_10_N34_00_W007_00_DEM/Copernicus_DSM_COG_10_N34_00_W007_00_DEM.tif
```

2. Ouvrez le style de la couche et choisissez le rendu **Ombrage**.
3. Dupliquez la couche, puis donnez à la copie un rendu **Pseudo-couleur à bande unique** pour lire les altitudes.
4. Dans la **Boîte à outils de traitements**, lancez l'outil **Pente** sur la couche.

**À observer**

- Où se trouvent les zones basses proches de l'oued ?
- Où sont les versants les plus raides ?

**À retenir :** un relief exploitable sans base de données locale.

---

## H. Explorer des millions de bâtiments

**Démonstration du formateur** · **Durée :** 2 min · *Cas complémentaire*

**Objectif :** voir que le principe vaut aussi pour les données vecteur.

1. Ouvrez l'explorateur d'Overture Maps dans le navigateur :

```
https://docs.overturemaps.org/getting-data/explore/
```

2. Zoomez sur un quartier d'une ville marocaine.
3. Cliquez sur un bâtiment pour lire ses attributs et sa source.

**À retenir :** derrière la carte, des fichiers GeoParquet mondiaux, lus par morceaux, sans base de données.

---

## En cas de difficulté

| Problème | Que faire |
| --- | --- |
| La couche ne se charge pas | Vérifiez que le lien est copié en entier, sans espace au début ni à la fin. |
| Rien ne s'affiche après le chargement | Clic droit sur la couche, puis **Zoomer sur la couche**. |
| Erreur de réseau | Signalez-le au formateur : le réseau de la salle peut bloquer l'adresse. |
| La calculatrice ne reconnaît pas l'expression | Vérifiez que les couches s'appellent bien `B04` et `B08`, et copiez l'expression telle quelle. |
| La réponse du catalogue est un bloc de texte | C'est normal, c'est du JSON. Dans Firefox, cliquez sur l'onglet **JSON** ; dans Chrome ou Edge, cochez **Impression élégante**. |
| La liste `features` est vide | Vérifiez les dates et les quatre nombres de `bbox` : ouest, sud, est, nord, dans cet ordre. |
| Les menus ne portent pas le même nom | Les intitulés varient selon la version de QGIS : demandez au formateur. |

## Rappel

Ces exercices utilisent uniquement des **données ouvertes**. Avant de déposer une donnée de votre administration sur un service en ligne, appliquez la règle de tri vue en séance et consultez votre DSI.
