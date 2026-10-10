# Rman Node pour Autodesk Maya (v2.0)

**Auteur : Enzo Freminet (Copyright 2026)**  
*Compatible Maya 2022+ · RenderMan 24+ / 25+ / 26+ · Python 3 (PySide2 / PySide6)*

---

Rman Node est un script utilitaire de pointe conçu pour automatiser, simplifier et accélérer considérablement la création, l'organisation et l'inspection de vos matériaux PBR dans Autodesk Maya avec Pixar's RenderMan.

Finie la configuration manuelle et répétitive de dizaines de nœuds : **l'utilisateur peut directement importer ses textures sans devoir créer de `PxrSurface` auparavant dans Maya**. Glissez simplement vos images dans le plugin pour générer automatiquement un shader RenderMan fonctionnel, son Shading Group dédié et un réseau nodal optimisé, calibré pour le Linear Workflow et connecté aux canaux physiques de RenderMan (`PxrSurface`).

---

## Avertissement Légal & Conditions d'Utilisation

> [!CAUTION]
> **Licence Propriétaire & Protection des Droits :**
> Toute modification, redistribution, revente, ingénierie inverse (*reverse engineering*) ou décompilation de ce logiciel et de son code source sont **strictement interdites** sans autorisation écrite préalable et expresse d'**Enzo Freminet**.
>
> **Mentions Légales & Marques Déposées :**
> * **RenderMan®** est une marque déposée de **Pixar Animation Studios**.
> * **Maya®** est une marque déposée d'**Autodesk, Inc.**
> Ce script est un outil tiers indépendant développé par **Enzo Freminet**. Il n'est en aucun cas affilié, sponsorisé, soutenu, approuvé ou associé à **Pixar Animation Studios**, **The Walt Disney Company**, ou **Autodesk, Inc.**

---

## Fonctionnalités Majeures (v2.0)

### 1. Nouvelle Interface Ultra-Compacte (Instance Unique & Réinitialisation Propre)
- **Format ultra-compact sans ascenseur** : Dimensions de base optimisées (510×340 px) permettant d'afficher l'intégralité des commandes d'un coup d'œil, sans espace vide inutile et sans apparition de barre de défilement (scrollbar).
- **Gestion stricte mono-fenêtre** : Cliquer sur le bouton Shelf réactive instantanément la fenêtre déjà ouverte sans jamais créer de doublon.
- **Réinitialisation automatique** : Chaque réouverture ou fermeture remet l'interface dans son état compact initial (textures vidées, nom par défaut, preset par défaut, sélecteur réinitialisé).
- **En-tête de contrôle en 3 colonnes** :
  - `SHADER` : Liste déroulante des `PxrSurface` existants dans la scène ou option *-- Create new --*.
  - `PRESET` : 15 pré-réglages physiques prédéfinis prêts à l'emploi (Generic PBR, Plastic, Metal, Glass, Skin, Fabric, Rubber, Wood, Stone, Ceramic, Car Paint, Leaves, Snow, Ice, Water).
  - `NAME` : Nom personnalisé pour votre nouveau matériau.
  - **Bouton Rafraîchir (Refresh)** : Synchronisation instantanée des shaders de la scène.
- **Zone de Dépôt Interactive Compacte (Drag & Drop)** :
  - Cadre tactile épuré supportant le glisser-déposer direct depuis l'Explorateur Windows ou via clic sélecteur.
  - Formats supportés : `.png`, `.jpg`, `.jpeg`, `.exr`, `.tif`, `.tiff`, `.tx`, `.hdr`.

### 2. Liste Visuelle des Textures Importées (Cards System)
- **Détection intelligente automatique** : Reconnaissance instantanée du type de canal (*Base Color, Roughness, Normal, Metallic, Displacement, Opacity, Emissive, Subsurface*).
- **Aperçu miniature 36×36 px** : Décodage rapide et net de chaque map.
- **Pastille de statut** : Pastille verte si le canal est identifié et prêt à être connecté, grise si non reconnu.
- **Boutons d'action par texture** : Remplacer ou supprimer individuellement chaque map.
- **Bouton `Clear Textures`** : Permet de vider toute la liste en un seul clic.

### 3. Bouton `Build Material` Pleine Largeur & Mode `Update Material` Dynamique
- **Support & Détection de l'Ambient Occlusion (AO)** :
  - Détection automatique des textures d'AO (`ao`, `ambient_occlusion`, `occlusion`, `_ao`).
  - Câblage automatique via **`PxrBlend`** en mode **Multiply** (`operation = 18`) :
    - `AO.resultRGB` -> `PxrBlend.topRGB`
    - `Color.resultRGB` -> `PxrBlend.bottomRGB`
    - `PxrBlend.resultRGB` -> `PxrSurface.diffuseColor`
    - Si l'AO est importée seule, le `PxrBlend` multiplie la teinte diffuse par défaut du shader.
- **Support 100% Automatique du Displacement (Glisser-Déposer)** :
  - Création automatique du nœud **`PxrDisplace`** (`<Nom>_displace`) connecté à `sg.rman__displacement`.
  - Valeur par défaut du `dispAmount` calibrée à **0.1** (évite d'exploser le maillage).
  - Chaîne complète générée : `PxrTexture` -> `PxrDispTransform` (dispRemapMode Centered = 2) -> `PxrDisplace.dispScalar`.
- **Application automatique du Preset PBR** :
  - À la création d'un shader, applique les paramètres optiques et physiques exacts du matériau sélectionné dans la liste `PRESET`.
- **Import direct & création 100% automatique (aucun `PxrSurface` préalable requis)** :
  - Déposez vos maps et cliquez simplement sur **`Build Material`** : le plugin instancie automatiquement le shader `PxrSurface`, génère son Shading Group dédié nommé proprement (`<Nom>_SG`), configure l'intégralité du réseau PBR (`PxrManifold2D`, `PxrTexture`, `PxrNormalMap`, `PxrRemap`, etc.) et assigne le shader aux objets 3D sélectionnés si nécessaire.
  - **Affichage Viewport 2.0 fiable & sans cube noir / vert fluo** :
    - **Rendu RenderMan** : `surface.outColor` -> `sg.rman__surface` (conserve le `PxrSurface` complet et toutes ses maps PBR).
    - **Maya Viewport 2.0** : shader Lambert neutre standard (`HSV: 0, 0, 0.500`) relié à `sg.surfaceShader` pour garantir un rendu gris propre.
    - **Primary Specular Calibré** : Face Color initialisé à **HSV: 0, 0, 0.18** (RGB: 0.18, 0.18, 0.18) et Edge Color à **HSV: 0, 0, 0.500** (RGB: 0.5, 0.5, 0.5).
- **Mode `Update Material` intelligent** :
  - Dès qu'un matériau existant est sélectionné dans la liste déroulante `SHADER`, le bouton principal se colore en vert et devient **`Update Material '<nom>'`**.
  - Remplacement non-destructif des chemins fichiers sans altérer vos réglages (`PxrRemap`, gains, contrastes).

### 4. Inspecteur HUD Compact Snug (280×344 px) avec Épinglage (Pin)
- **Format Épuré & Snug (280×344 px)** :
  - Réduction maximale des marges latérales (seulement 10 px de chaque côté).
  - Boutons supérieurs explicites combinant icône et texte :
    - `[ ↻ Refresh ]` : recharge la texture depuis le disque.
    - `[ 📌 Pin ]` / `[ 📌 Pinned ]` : maintient la fenêtre ouverte pendant l'édition des paramètres dans l'Attribute Editor.
    - `[ ✕ Close ]` : ferme la fenêtre d'inspection (également fermable via Echap ou simple clic).
  - Zone centrale avec aperçu carré haute fidélité **260×260 px** sur fond sombre `#0e0e10`.
  - Zone inférieure d'informations :
    - Nom du fichier complet affiché en **bleu vif** (`#007AFF`, gras).
    - Spécifications techniques complètes en gris doux : Résolution (`1024×1024 px`), poids (`0.9 MB`), et bornes de `PxrRemap` si applicable.
- **Déclenchement instantané** : **Maj + V** (`Shift + V`) ou **Clic Molette (MMB)** sur un nœud dans l'Hypershade, le Node Editor ou le Viewport.
- **Écouteur d'événements Qt universel** : Détecte les frappes clavier et clics de manière globale dans toute l'application Maya, sans dépendre du système de hotkeys restreint de Maya.
- **Nœuds textures supportés** : `PxrTexture`, `PxrNormalMap`, `PxrNormal`, `PxrRemap`, nœuds `file` Maya.
- **Mise à jour en direct (Live Refresh)** :
  - Surveillance automatique en direct via `scriptJob` des variations des bornes de `PxrRemap` (`inputMin`, `inputMax`, `outputMin`, `outputMax`).

### 5. Duplication Nœud Isolé (Isolated Node Copy)
- Accessible directement via le menu **`Tools > Duplicate Selected Node (Isolated Copy)`**.
- Duplique **n'importe quel nœud sélectionné dans Maya** (shader, utilitaire, texture, bump, etc.) :
  - Copie exacte et intégrale de l'ensemble de ses propriétés et valeurs d'attributs.
  - Déconnexion totale de l'ensemble des connexions entrantes et sortantes : produit un nœud 100% propre et autonome.

### 6. Barre d'Outils Inférieure Élargie (Bottom Dock)
4 grands boutons tactiles compacts et toujours accessibles au bas de la fenêtre :
- **Open sourceimages Folder** : Ouvrir directement le dossier `sourceimages` dans l'Explorateur Windows.
- **Organize Nodes** : Réordonner automatiquement le graphe dans l'Hypershade ou le Node Editor.
- **Default Lighting Setup** : Calibre automatiquement le rendu en résolution **1920×1080** (Full HD) et active le moteur **RenderMan RIS** (`prman`).
- **Color Correction Workspace** : Réinitialise instantanément la gestion colorimétrique de Maya à ses valeurs par défaut (`changeColorMgtPrefsConfigFilePath("legacy")`).

---

## 15 Presets Physiques Prédéfinis

L'outil applique les coefficients optiques réels selon le matériau sélectionné :

| Preset | Diffuse | Specular Face / Edge | Roughness | Spécificités |
| :--- | :---: | :---: | :---: | :--- |
| **Generic PBR** | 1.0 | 0.18 / 0.5 | 0.50 | Configuration standard équilibrée |
| **Plastic** | 1.0 | 0.04 / 1.0 | 0.20 | Réflexions spéculaires nettes |
| **Metal** | 0.0 | 1.00 / 1.0 | 0.15 | Conduction métallique totale |
| **Glass** | 0.0 | 0.00 / 0.0 | 0.00 | Réfraction 1.0, IOR 1.50 |
| **Skin** | 0.0 | 0.04 / 1.0 | 0.35 | Subsurface Scattering 1.0 (teinte chair) |
| **Fabric** | 1.0 | 0.02 / 0.1 | 0.80 | Effet duvet Fuzz activé (angle 16°) |
| **Rubber** | 1.0 | 0.04 / 0.1 | 0.70 | Spéculaire étalé et mat |
| **Wood** | 1.0 | 0.04 / 1.0 | 0.35 | Satiné naturel |
| **Stone** | 1.0 | 0.04 / 0.2 | 0.80 | Spéculaire diffus et rugueux |
| **Ceramic** | 1.0 | 0.04 / 1.0 | 0.10 | Brillance lisse type émail |
| **Car Paint** | 1.0 | 0.00 / 0.0 | 0.00 | Clearcoat vernis actif (0.04 / 1.0, rgh 0.02) |
| **Leaves** | 1.0 | 0.04 / 1.0 | 0.30 | Translucidité & Double Sided actifs |
| **Snow** | 0.5 | 0.04 / 1.0 | 0.40 | SSS neigeux blanc éclatant |
| **Ice** | 0.0 | 0.00 / 0.0 | 0.10 | Réfraction 1.0, IOR 1.31 (glace pure) |
| **Water** | 0.0 | 0.00 / 0.0 | 0.00 | Réfraction 1.0, IOR 1.33 (eau liquide) |

---

## Conventions de Nommage Automatique

Le script identifie vos maps à partir de mots-clés dans les noms de fichiers (insensibles à la casse) :

| Canal Détecté | Nœud RenderMan | Mots-clés reconnus |
| :--- | :--- | :--- |
| **Base Color / Diffuse** | `PxrTexture` | `color`, `diffuse`, `diff`, `albedo`, `base_col`, `basecolor` |
| **Ambient Occlusion (AO)** | `PxrTexture` + `PxrBlend` (Multiply) | `ao`, `ambient_occlusion`, `ambientocclusion`, `occlusion`, `_ao` |
| **Roughness** | `PxrTexture` + `PxrRemap` | `roughness`, `rough`, `_rgh` |
| **Normal Map** | `PxrNormalMap` | `normal`, `nrm`, `nor`, `_nml` *(détection OpenGL / DirectX auto)* |
| **Displacement / Height** | `PxrTexture` + `PxrDispTransform` | `displacement`, `displace`, `height`, `disp`, `_dsp` |
| **Metallic** | `PxrTexture` | `metallic`, `metalness`, `metal`, `_mtl` |
| **Emissive** | `PxrTexture` | `emissive`, `emission`, `emit` |
| **Opacity / Alpha** | `PxrTexture` | `opacity`, `alpha`, `mask`, `cutout`, `transparency` |
| **Subsurface** | `PxrTexture` | `scatter`, `scattering`, `sss`, `subsurface`, `subsurfacecolor` |

---

## Raccourcis Clavier

| Raccourci | Action | Description |
| :--- | :--- | :--- |
| **`Ctrl + Shift + T`** | **Principled PBR Setup** | Ouvre un sélecteur de fichiers et câble automatiquement les textures sur le `PxrSurface` sélectionné. |
| **`Maj + V` (`Shift + V`)** | **Texture HUD & Collage** | Affiche l'inspecteur snug 280×344 px sur le nœud sélectionné (ou le collage complet du matériau). |
| **`Clic Molette (MMB)`** | **Texture HUD & Collage** | Clic simple sur un nœud dans l'éditeur pour ouvrir l'aperçu instantané. |

---

## Installation Rapide (Drag & Drop)

1. Téléchargez le fichier d'installation autonome **`Installer_Rman_Node.py`**.
2. Lancez Autodesk Maya.
3. Glissez-déposez **`Installer_Rman_Node.py`** directement depuis l'Explorateur de fichiers vers le **Viewport 3D de Maya** *(ou exécutez-le dans le Script Editor Python de Maya)*.
4. L'installateur déploie automatiquement :
   - Le package modulaire **`Rman_Node/`** dans votre dossier de scripts utilisateur Maya.
   - Le launcher de compatibilité **`rman_node_v205.py`**.
   - L'icône officielle **`Rman_Node/data/icon.png`**.
5. Un bouton **`Rman_Node`** est automatiquement créé / mis à jour dans votre **Shelf actif** avec l'icône officielle.
6. Une boîte de dialogue de confirmation vous propose de lancer l'outil immédiatement !

*Pour lancer manuellement l'outil depuis le Script Editor Python de Maya :*
```python
import rman_node_v205
rman_node_v205.show_ui()
```

---

## Structure du Package Modulaire

```text
Rman_Node/
├── __init__.py
├── plugin.py
├── config/           # Constantes globales, tags de textures, canaux PBR
├── presets/          # Gestionnaire de presets et 15 configurations physiques
├── core/             # Logique centrale : builder de shaders, détection, connexions, displacement, layer shader
├── viewport/         # Aperçu fidèle Viewport 2.0 (Lambert neutre + rman__surface)
├── ui/               # Interface Qt ultra-compacte (PySide2/PySide6), cards, HUD, inspecteur
├── utils/            # Utilitaires Maya, gestion des nœuds, isolation
└── data/             # Ressources et icône officielle icon.png

Fichiers d'installation et de lancement :
├── Installer_Rman_Node.py  # Installateur tout-en-un autonome (Drag & Drop Maya)
└── rman_node_v205.py       # Launcher et façade de compatibilité descendante
```

---

## Support & Contact

Développé par **Enzo Freminet** (2026). Pour tout retour, suggestion ou rapport de bug, n'hésitez pas à ouvrir une issue ou à contacter le développeur.
