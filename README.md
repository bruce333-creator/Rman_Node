RenderMan Node Wrangler pour Maya

Auteur : Enzo Freminet (Copyright © 2026)

RenderMan Node Wrangler est un script utilitaire Python léger, conçu pour accélérer considérablement votre workflow de création de matériaux PBR dans Autodesk Maya avec Pixar's RenderMan.

Au lieu de créer et connecter manuellement des dizaines de nœuds (nodes) à chaque fois que vous configurez un matériau, cet outil automatise tout le processus. Glissez simplement vos textures (Color, Roughness, Normal, Metallic, Emissive), et le Wrangler construira le réseau nodal parfait, appliquera les règles de colorimétrie appropriées et le connectera à un matériau `PxrSurface` en un seul clic.

---

## Avertissement Légal et Conditions d'Utilisation

Ceci est un outil NON OFFICIEL, EXPÉRIMENTAL et communautaire.** Ce script n'est ni affilié, ni approuvé, ni supporté par Pixar Animation Studios, Autodesk ou leurs partenaires.

* Modifications : Les modifications du code source de cet outil sont **autorisées dans un cadre strictement personnel**. Toute redistribution, copie non autorisée ou vente du script est interdite sans la permission explicite de l'auteur.
* Absence de garantie : L'auteur (Enzo Freminet) décline **TOUTE RESPONSABILITÉ** en cas de bugs, corruption de scène, perte de données, plantages du logiciel ou erreurs de rendu liés à l'utilisation de cet outil. 
* Vous utilisez ce script à vos propres risques et périls.** Il est vivement recommandé de sauvegarder vos scènes Maya avant d'utiliser des outils d'automatisation.

---

## Fonctionnalités

* Installation Drag-and-Drop (Glisser-Déposer) : Aucune configuration complexe. Glissez le fichier `.py` dans le viewport de Maya, et un bouton avec une icône personnalisée sera automatiquement ajouté à votre étagère (Shelf) active. Une fenêtre de secours s'ouvre si le chemin du fichier est perdu lors du glissement.

* Reconnaissance Intelligente des Textures : Détecte automatiquement l'utilité de vos textures (Base Color, Roughness, Normal, Metallic, Emissive) selon leurs conventions de nommage habituelles (`_color`, `_albedo`, `_rough`, `_nrm`, `_metal`, etc.).

* Ignorance Sécurisée du Displacement : Le script ignore volontairement les maps de Displacement pour éviter tout bug ou instabilité liés au Viewport 2.0 et LookdevX de Maya.
* Réseau Nodal Automatique : Génère un réseau propre autour d'un `PxrSurface`, avec `PxrTexture`, `PxrNormalMap` et un node `PxrManifold2D` partagé. Le shader est correctement relié au Shading Group (connexions `surfaceShader` ET `rman__surface`).

* Application du Linear Workflow :** L'outil active automatiquement l'attribut *Linearize* pour les maps de couleurs (Color, Emissive) et le désactive pour les maps de données (Roughness, Normal, Metallic).

* Normal Maps Intelligentes :** Détecte automatiquement si votre Normal Map est au format OpenGL (`_gl`) ou DirectX (`_dx`) et configure l'attribut `orientation` du `PxrNormalMap` en conséquence.

* Contrôle de la Roughness : Insère automatiquement un nœud `PxrRemap` entre votre texture de Roughness et le `PxrSurface` pour vous permettre d'ajuster facilement la brillance.

* Bouton d'Assignation : Permet d'assigner le matériau sélectionné ou nouvellement créé directement aux objets sélectionnés dans le viewport.

* Boutons Utilitaires de Scène : 
Default Lighting Setup : Passe le moteur sur RenderMan, configure une résolution 4K (3840x2160), active le moteur XPU et définit les Max Samples à 256.
Color Correction Workspace : Force la configuration colorimétrique (OCIO Maya2022-default) et bascule la vue sur `Un-tone-mapped` (ou `Un-tone-mapped (sRGB)`) pour un LookDev fidèle à RenderMan.

---

## Installation

1. Téléchargez le fichier `renderman_node_wrangler.py` sur votre ordinateur.
2. Ouvrez Autodesk Maya.
3. Glissez et déposez directement le fichier `.py` depuis votre explorateur de fichiers jusque dans le Viewport 3D de Maya.
4. *(Si Maya bloque le glissement pour des raisons de sécurité, une fenêtre d'explorateur s'ouvrira pour vous demander de sélectionner le fichier manuellement).*
5. Une boîte de dialogue confirmera le succès de l'installation, et un bouton **RM_Wrang** apparaîtra dans votre Shelf actuel.

---

## Comment l'utiliser

### Étape 1 : Choisir la cible (Target PxrSurface)
* Utilisez le menu déroulant pour sélectionner un matériau `PxrSurface` déjà existant dans votre scène afin de le modifier ou de le compléter.
* OU laissez sur "— Create new —" et tapez le nom désiré dans le champ "New material name" pour générer un tout nouveau matériau.

### Étape 2 : Charger les Textures
* Glissez-déposez vos fichiers images (ou un dossier contenant les images) directement dans la zone en pointillée.
* Formats supportés : `.png`, `.jpg`, `.jpeg`, `.exr`, `.tif`, `.tiff`, `.tx`, `.hdr`.
* La liste des textures reconnues s'affichera. Utilisez le bouton "Remove selected" si vous avez importé une image par erreur.

### Étape 3 : Build & Assign
* Cliquez sur Build Shader. Le script va instantanément construire le réseau nodal dans le Hypershade.
* Si vous avez des objets sélectionnés dans le viewport, vous pouvez cliquer sur Assign Material to Selection pour leur appliquer directement le matériau fraîchement configuré.

---

## Conventions de Nommage Reconnues

L'outil se base sur des mots-clés présents dans le nom de vos fichiers images. Assurez-vous que vos textures comportent l'un des termes suivants :

| Type de Texture | Mots-clés reconnus (Insensibles à la casse) |
| :--- | :--- |
| **Couleur / Diffuse** | `color`, `diffuse`, `albedo`, `base_col`, `basecolor` |
| **Roughness** | `roughness`, `rough`, `_rgh` |
| **Normal Map** | `normal`, `nrm`, `nor`, `_nml` |
| **Metallic** | `metallic`, `metalness`, `metal`, `_mtl` |
| **Emissive** | `emissive`, `emission`, `emit` |

*Exemple de nommage parfait :* `Plancher_Bois_albedo.tx`, `Plancher_Bois_roughness.tx`, `Plancher_Bois_normal_gl.tx`.

### Système Avancé : PxrLayerSurface (Matériaux Superposés)

Le RenderMan Node Wrangler inclut une section avancée (dépliable via le bouton `▶ Layered Materials (Advanced)`) dédiée à la création de matériaux complexes multicouches (ex: de la rouille sur du métal, de la poussière sur du plastique, etc.).

Plutôt que de recâbler manuellement toute l'architecture de mixage (qui peut s'avérer très fastidieuse dans le Node Editor), cet outil permet de fusionner deux matériaux `PxrSurface` existants et de transférer toutes leurs textures vers un **`PxrLayerSurface`**.

Comment l'utiliser :
1. Créez un nœud `PxrLayerSurface` vierge dans votre scène Maya.
2. Dans l'interface du Wrangler, sélectionnez votre matériau de fond dans Base Layer.
3. Sélectionnez votre matériau du dessus dans Layer 1.
4. Sélectionnez votre nœud de destination dans Target PxrLayerSurface.
5. Cliquez sur Build Layer Shader.

Ce que fait le script automatiquement :
* Il génère un nœud `PxrLayerMixer` et deux nœuds `PxrLayer` intermédiaires.
* Il connecte la hiérarchie complète (`PxrLayer` -> `PxrLayerMixer` -> `PxrLayerSurface`).
* Il re-route intelligemment toutes les connexions de vos textures (Diffuse, Roughness, Normal, Metallic, etc.) depuis vos anciens `PxrSurface` vers les entrées correspondantes des nouveaux `PxrLayer`.

IMPORTANT / ATTENTION :
**IL FAUT ABSOLUMENT ACTIVER LE "PRIMARY SPECULAR" (COCHER LA CASE "ENABLE PRIMARY SPECULAR") DANS LES NŒUDS PXRLAYER (LAYER BASE ET LAYER 1) POUR QUE LES REFLETS FONCTIONNENT CORRECTEMENT AU RENDU.** *(Bien que le script tente de forcer cette option et de mettre le Gain à 1.0 automatiquement, les changements d'API selon les versions de RenderMan exigent souvent une vérification manuelle de votre part dans l'Attribute Editor).*

FAQ/Questions fréquentes :

Pourquoi mes objets sont super brillants ?
Par défaut le script attribue à vos PxrSurface une valeur HSV de 0,0,0.5 pour la Primary Specular et la Edge color. Il convient donc que vous modifiez manuellement ce paramètre pour obtenir le résulat souhaité

Pourquoi une fenêtre s'ouvre quand je drag and drop le fichier .py ?
Parfois il se peut que ça arrive mais ça n'arrive pas à tous les coups. Dans ce cas, veuillez simplement sélectionner le fichier .py dans vos fichiers pour permettre l'installation complète.

Pourquoi mon PxrSurface que je viens de créer ne s'affiche pas dans la liste ?
Si vous avez ouvert le script avant de créer un nouveau PxrSurface, celui-ci ne s'affichera tant que vous n'aurez pas cliquer sur le bouton Refresh

Pourquoi mon PxrLayerSurface ne brille pas ?
Lorsque vous créez et relier un PxrLayer Surface vous devez manuellement activer la primary specular, veuillez donc cliquer sur Enable dans l'onglet Primary Specular des PxrLayer1 et 2
