# DocusMojis

**DocusMojis** est un outil léger permettant de transformer des documents textuels contenant des emojis en **PDF**, tout en permettant de **personnaliser et transformer les emojis** présents dans le document.

L'idée est de ne pas considérer un emoji comme un simple caractère Unicode, mais comme un élément pouvant être traité, transformé et rendu graphiquement.

Par exemple, un document contenant :

```text
Bonjour 👋
Bienvenue 🚀
Attention ⚠️
```

pourrait produire un PDF dans lequel les emojis sont :

* correctement rendus ;
* recolorés ;
* redimensionnés ;
* transformés ;
* éventuellement remplacés par une autre représentation graphique.

## Pourquoi DocusMojis ?

Les emojis sont généralement utilisés comme de simples caractères :

```text
Bonjour 👋
```

Mais dans un document destiné à être transformé en PDF, ils peuvent devenir de véritables éléments graphiques.

DocusMojis cherche donc à créer une chaîne simple :

```text
Emoji Unicode
      │
      ▼
Interprétation
      │
      ▼
Transformation
      │
      ▼
Rendu graphique
      │
      ▼
PDF
```

L'objectif est de pouvoir contrôler l'apparence finale des emojis sans avoir besoin d'un logiciel graphique ou d'une suite bureautique complète.

---

# Fonctionnement

Le fonctionnement général envisagé est le suivant :

```text
                    Document source
                   TXT / HTML / ...
                         │
                         ▼
                ┌─────────────────┐
                │     Parser      │
                └────────┬────────┘
                         │
                         ▼
                ┌─────────────────┐
                │ Document interne│
                │                 │
                │ Texte           │
                │ Emojis          │
                │ Structure       │
                └────────┬────────┘
                         │
                         ▼
                ┌─────────────────┐
                │ DocusMojis      │
                │ Renderer        │
                │                 │
                │ Transformation  │
                │ Couleur         │
                │ Taille          │
                │ Rotation        │
                │ Style           │
                └────────┬────────┘
                         │
                         ▼
                       PDF
```

## Personnalisation des emojis

Une partie importante du projet consiste à pouvoir modifier l'apparence des emojis.

### Couleur

Un emoji pourrait par exemple être recoloré :

```text
❤️
```

vers une autre couleur ou une palette personnalisée.

Exemple conceptuel :

```text
❤️ → 💙
```

ou, plus généralement, appliquer une transformation graphique sans changer l'emoji source.

### Taille

Les emojis pourraient avoir une taille indépendante du texte environnant :

```text
Bonjour 👋
```

pourrait devenir :

```text
Bonjour        👋
               ↑
          taille différente
```

### Rotation

Un emoji pourrait être transformé géométriquement :

```text
🚀
```

→ rotation de `15°`

→ rotation de `45°`

→ rotation de `90°`

### Transformations

Le moteur pourra éventuellement prendre en charge plusieurs transformations :

```text
Translation
Rotation
Scale
Flip
Transformation géométrique
```

Une transformation pourrait être représentée conceptuellement par :

```text
emoji:
  rotation: 25deg
  scale: 1.5
  translate:
    x: 10
    y: -5
```

### Effets graphiques

D'autres transformations pourront être étudiées :

* transparence ;
* contraste ;
* luminosité ;
* saturation ;
* ombre ;
* contour ;
* filtres ;
* remplacement par une autre représentation graphique.

Ces fonctionnalités ne sont pas nécessairement prévues dans la première version.

---

# L'emoji comme objet graphique

Un principe important de DocusMojis est de **séparer le caractère Unicode de son rendu graphique**.

Par exemple :

```text
👨‍💻
```

reste le contenu logique du document.

Mais lors du rendu, DocusMojis peut décider comment le représenter graphiquement.

On peut donc imaginer une représentation interne :

```text
Emoji {
    unicode: "👨‍💻",
    position: ...,
    size: ...,
    rotation: ...,
    color: ...,
    transform: ...
}
```

Le PDF final ne dépend alors plus directement de la représentation originale de l'emoji.

Cela ouvre la possibilité d'utiliser différentes stratégies de rendu.

---

# Formats sources

La première version se concentre sur les **documents textuels**.

Formats envisagés :

* `TXT`
* `HTML`
* `Markdown`
* autres formats textuels simples

Le projet ne cherche pas initialement à devenir un convertisseur universel de documents.

---

# Format de sortie

Le format de sortie principal est :

```text
PDF
```

Le PDF est au centre du projet.

DocusMojis doit notamment permettre de produire un document dans lequel les emojis restent correctement visibles après génération.

Mais le rendu ne doit pas simplement reproduire l'emoji original.

Il doit pouvoir appliquer les transformations demandées avant de produire le PDF.

---

# Philosophie technique

DocusMojis doit rester un outil **léger et autonome**.

Le projet ne cherche pas à accumuler de nombreuses bibliothèques ou dépendances lourdes.

La priorité est donnée à :

* un exécutable compilé ;
* peu de dépendances ;
* des bibliothèques légères ;
* une architecture simple ;
* un traitement local ;
* une distribution Docker ;
* une CLI facilement utilisable.

L'objectif est de pouvoir obtenir quelque chose proche de :

```text
Document source
       │
       ▼
   DocusMojis
       │
       ▼
     PDF
```

sans devoir installer une importante chaîne logicielle.

---

# Docker

DocusMojis sera disponible sous forme d'image Docker.

Une utilisation possible serait :

```bash
docker run --rm \
  -v "$PWD:/documents" \
  docusmojis \
  /documents/document.txt \
  /documents/document.pdf
```

La syntaxe définitive sera déterminée pendant le développement.

L'image Docker devra rester aussi petite que possible.

---

# Configuration des transformations

Une réflexion importante du projet concerne la manière de décrire les transformations.

Une première possibilité serait d'utiliser un fichier de configuration :

```yaml
emojis:
  "🚀":
    color: "#3366FF"
    scale: 1.5
    rotation: 15

  "❤️":
    color: "#FF00AA"
    scale: 2
```

Le document source resterait alors relativement simple :

```text
Bienvenue 🚀

DocusMojis ❤️
```

et les transformations seraient appliquées séparément.

Une autre possibilité serait de permettre des annotations directement dans certains formats sources.

Par exemple, avec HTML :

```html
<span data-emoji="🚀"
      data-color="#3366FF"
      data-scale="1.5">
</span>
```

Cette partie reste à définir.

---

# Architecture envisagée

L'architecture pourrait progressivement prendre cette forme :

```text
                ┌──────────────────┐
TXT ───────────►│                  │
HTML ──────────►│      Parser      │
Markdown ─────►│                  │
                └────────┬─────────┘
                         │
                         ▼
                ┌──────────────────┐
                │ Document interne │
                │                  │
                │ Texte            │
                │ Emojis           │
                │ Structure        │
                └────────┬─────────┘
                         │
                         ▼
                ┌──────────────────┐
                │ Emoji Processor  │
                │                  │
                │ Identification   │
                │ Transformation   │
                │ Couleur          │
                │ Géométrie        │
                │ Rendu            │
                └────────┬─────────┘
                         │
                         ▼
                ┌──────────────────┐
                │   PDF Renderer   │
                └────────┬─────────┘
                         │
                         ▼
                       PDF
```

Cette architecture est une **base de réflexion** et n'est pas figée.

---

# Questions techniques à étudier

Le développement devra notamment répondre à plusieurs questions.

## Représentation des emojis

Comment transformer un emoji Unicode en représentation graphique manipulable ?

Plusieurs solutions pourront être étudiées :

* polices Unicode ;
* glyphes ;
* images ;
* SVG ;
* rendu vectoriel ;
* combinaison de plusieurs représentations.

## Emojis couleur

La gestion des emojis couleur constitue un point important.

Il faudra notamment étudier les différences entre :

```text
Emoji Unicode
     │
     ├── Police monochrome
     │
     ├── Police couleur
     │
     ├── Image
     │
     └── SVG
```

et déterminer quelle représentation permet le meilleur compromis entre :

**simplicité + qualité + personnalisation + taille du logiciel**.

## Séquences Unicode

Certains emojis sont constitués de plusieurs caractères Unicode.

Par exemple :

```text
👨‍💻
👩‍🔬
👍🏽
🏳️‍🌈
❤️
```

DocusMojis devra donc traiter correctement les séquences Unicode et ne pas nécessairement considérer chaque point de code comme un emoji indépendant.

---

# Ce que DocusMojis n'est pas

DocusMojis n'a pas pour objectif initial de devenir :

* un traitement de texte ;
* un éditeur PDF complet ;
* un logiciel de dessin ;
* un navigateur web ;
* un moteur HTML complet ;
* une suite bureautique ;
* un convertisseur universel de documents.

Le projet se concentre sur une problématique précise :

> **Transformer des documents textuels en PDF tout en donnant aux emojis un véritable contrôle graphique.**

---

# Objectifs

## Objectif principal

Créer un outil capable de transformer des documents textuels contenant des emojis en PDF, avec un contrôle sur leur rendu graphique.

## Objectifs techniques

* gérer correctement Unicode ;
* détecter les emojis ;
* représenter les emojis graphiquement ;
* modifier leur apparence ;
* appliquer des transformations ;
* générer un PDF ;
* limiter les dépendances ;
* fournir un exécutable autonome ;
* fournir une image Docker légère.

---

# État du projet

🚧 **Projet en phase de réflexion / conception**

Les choix concernant :

* le langage ;
* le moteur PDF ;
* le système de rendu des emojis ;
* les formats graphiques ;
* les polices ;
* les transformations ;
* le format de configuration ;
* les formats sources ;

restent à étudier.

Ce README sert de **base de réflexion pour définir l'architecture et les choix techniques du projet**.

---

# Vision

DocusMojis part d'une idée simple :

> **Un emoji présent dans un document ne devrait pas être limité à son apparence par défaut.**

Le document définit le contenu.

DocusMojis définit la manière dont ce contenu est rendu.

L'objectif final est donc de disposer d'un petit moteur capable de prendre :

```text
Document
   +
Emojis
   +
Transformations
   │
   ▼
DocusMojis
   │
   ▼
PDF personnalisé
```

avec une distribution simple, légère et facilement déployable.

# LICENCE GPL
