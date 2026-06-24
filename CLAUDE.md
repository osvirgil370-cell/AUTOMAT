# CLAUDE.md — AUTOMAT

## Apercu du projet

AUTOMAT est un systeme de surveillance automatise qui suit la disponibilite des modeles IA gratuits sur [OpenRouter](https://openrouter.ai). Il effectue des scans hebdomadaires (chaque lundi) pour detecter les nouveaux modeles et ceux qui ont disparu, maintient une base de donnees curee et genere des rapports d'analyse en francais.

Il n'y a **aucun code applicatif** dans ce depot — c'est un projet exclusivement compose de fichiers de donnees JSON et de rapports Markdown, mis a jour par des sessions automatisees Claude Code.

## Structure du depot

```
AUTOMAT/
├── CLAUDE.md                        # Ce fichier
└── opencode/
    ├── opencode-config.json         # Configuration du modele recommande
    ├── opencode-models.json         # Base de donnees complete des modeles gratuits
    └── rapport-YYYY-MM-DD.md        # Rapports d'analyse hebdomadaires
```

## Fichiers cles

### `opencode/opencode-config.json`
- Specifie le modele gratuit actuellement recommande pour les taches de programmation
- Reference le schema de configuration `opencode.ai`
- Mis a jour chaque semaine en meme temps que la base de modeles

### `opencode/opencode-models.json`
- Liste complete de tous les modeles gratuits sur OpenRouter
- Chaque entree contient : `id`, `name`, `context_length`
- Trie par `context_length` (ordre decroissant)
- Inclut un champ `count` et un horodatage `updated_at`

### `opencode/rapport-YYYY-MM-DD.md`
- Rapports hebdomadaires en Markdown, rediges en francais
- Structure coherente : statut du modele recommande, nouveaux modeles, modeles disparus, top 5, total, sources, notes techniques

## Processus de mise a jour hebdomadaire

Chaque lundi, un scan est effectue :

1. **Collecter** les modeles gratuits actuels depuis OpenRouter (via des sources web publiques — l'API n'est pas accessible depuis l'environnement sandbox)
2. **Comparer** avec le `opencode-models.json` de la semaine precedente pour detecter les ajouts et suppressions
3. **Evaluer** si le modele recommande par defaut doit changer, selon ces criteres de priorite :
   - Providers privilegies : NVIDIA, Google, Meta, OpenAI, Anthropic
   - Grande fenetre de contexte
   - Nombre eleve de parametres (modeles denses preferes aux MoE)
   - Bonnes capacites en programmation
4. **Mettre a jour** `opencode-config.json` et `opencode-models.json`
5. **Generer** un nouveau rapport `rapport-YYYY-MM-DD.md`
6. **Committer** avec le format de message : `chore: update free models YYYY-MM-DD`

## Conventions

### Langue
- Tous les rapports et notes sont rediges en **francais**
- Les noms de fichiers et les cles JSON utilisent l'**anglais**

### Nommage des fichiers
- Fichiers JSON : kebab-case (`opencode-config.json`)
- Rapports : `rapport-YYYY-MM-DD.md` (format de date ISO)

### Messages de commit
- Format : `chore: update free models YYYY-MM-DD`
- Auteur : `opencode-routine <osvirgil370@gmail.com>`
- Chaque commit reference la session Claude Code qui l'a produit

### Format des identifiants de modeles
- Utilise le format OpenRouter : `provider/model-name:free`
- Le fichier de config prefixe avec le namespace `openrouter/`

### Structure des rapports
Chaque rapport suit ce modele :
1. Titre avec la date
2. Recommandation du modele par defaut (precedent, nouveau, justification)
3. Nouveaux modeles avec descriptions
4. Modeles disparus
5. Tableau top 5 des modeles recommandes (provider, contexte, parametres)
6. Nombre total de modeles gratuits
7. Sources (avec liens)
8. Notes techniques sur les limitations de collecte de donnees

## Notes sur la collecte de donnees

- L'API OpenRouter (`https://openrouter.ai/api/v1/models`) n'est **pas accessible** depuis l'environnement d'execution sandbox en raison de restrictions reseau
- Les donnees sont compilees a partir de sources web publiques
- Cette limitation est documentee dans la section notes techniques de chaque rapport

## Regles importantes pour les assistants IA

1. **Conserver la langue francaise** dans tous les rapports et notes des fichiers de donnees
2. **Maintenir la structure existante des rapports** — ne pas reorganiser les sections
3. **Trier les modeles par `context_length`** (decroissant) dans `opencode-models.json`
4. **Mettre a jour le champ `count`** dans `opencode-models.json` lors d'ajout/suppression de modeles
5. **Mettre a jour `updated_at`** dans les deux fichiers JSON lors de modifications
6. **Utiliser le format de message de commit etabli** pour les mises a jour hebdomadaires
7. **Ne pas fabriquer de donnees de modeles** — n'inclure que les modeles verifies depuis les sources
8. **Conserver la reference au schema de config** dans `opencode-config.json`
