# Scrum personnel opéré par Claude

Un template de **gestion de tâches en scrum personnel**, opéré par une IA (Claude) sur des fichiers **Markdown purs**, versionnés avec **git**.

Le principe : une semaine = un sprint. Chaque tâche est un fichier `TD-XXXX.md` avec un frontmatter YAML, rattachée à un projet et à un sprint. Une IA opératrice (vous lui donnez un nom) anime les rituels — kickoff le lundi, standup quotidien, clôture et bilan le dimanche — via les tâches planifiées de Claude, tient les métriques, fait respecter les règles et committe chaque changement.

Les règles complètes du système sont dans [`SYSTEME.md`](SYSTEME.md) — c'est le fichier de référence, pensé pour être lu par l'IA à chaque session.

## Ce que le système garantit

- **1 tâche = 1 sprint** : jamais de tâche fantôme qui traîne sur plusieurs semaines. Une tâche non finie est *reprise* par une nouvelle tâche liée (`Reprend`), traçable.
- **Une kill rule** : après 2 reports, une tâche doit être priorisée, redécoupée ou abandonnée — fini les zombies.
- **Des récurrentes déclaratives** : vos habitudes (sport, sommeil, factures…) sont définies une fois dans `recurrentes/` et instanciées automatiquement à chaque sprint.
- **Des rituels qui ne peuvent pas échouer en silence** : chaque exécution laisse une trace dans `journal/`, chaque rituel manqué est rattrapé et consigné.
- **3 métriques par sprint** : taux de complétion, reports, tenue des récurrentes. Pas plus, pas moins.
- **Miroir Google Calendar** (optionnel) : les tâches datées apparaissent dans votre agenda ; les fichiers restent la seule source de vérité.

## Et Obsidian ?

**Optionnel, mais recommandé.** Le système fonctionne intégralement sans lui : tout est Markdown + YAML, lisible et éditable avec n'importe quel outil — c'est l'IA qui fait vivre les fichiers. Ouvrir le dossier comme vault Obsidian ajoute le confort visuel : le kanban du sprint actif et les vues backlog (dossier `bases/`, format Obsidian Bases), et le graphe des liens entre tâches, projets et reprises (wikilinks `[[...]]`). Sans Obsidian, ces vues sont simplement ignorées.

## Structure

```
taches/       TD-XXXX.md — une tâche par fichier
projets/      une fiche par projet suivi
sprints/      Sprint-YYYY-Wxx.md — un sprint par semaine ISO
recurrentes/  REC-XX.md — définitions des tâches récurrentes
bases/        vues Obsidian Bases (kanban du sprint actif, backlog…) — optionnel
journal/      comptes rendus d'exécution des rituels de l'IA
```

## Installation

1. **Créez votre repo** depuis ce template (bouton « Use this template » → repo **privé** — votre vie va dedans).
2. **Clonez-le** sur votre machine (ex. `Documents/monvault`). Si vous utilisez Obsidian, ouvrez ce dossier comme vault.
3. **Créez un projet Claude** (claude.ai) dédié, avec une instruction du type : *« Tu es [NOM_OPERATEUR], l'opérateur de mon système scrum. Lis SYSTEME.md avant toute action. »* Attachez-y vos sessions Cowork et connectez le dossier du vault.
4. **Personnalisez `SYSTEME.md`** : remplacez tous les champs `[ENTRE_CROCHETS]` — nom de l'opérateur, langue/ton, compte Google Calendar, jours et heures des rituels.
5. **Donnez à l'IA un accès git autonome** (optionnel mais recommandé) :
   - déclarez Claude Code en serveur MCP local dans l'app desktop Claude — fichier de config de l'app, section `mcpServers` : `{ "claude-code": { "type": "stdio", "command": "cmd", "args": ["/c", "claude", "mcp", "serve"], "env": {} } }` (Windows ; adaptez `command` sur macOS/Linux) ;
   - créez un fine-grained PAT GitHub limité à votre repo (permission Contents : Read and write) et laissez l'IA l'enregistrer dans le coffre d'identifiants de votre machine.
6. **Créez les tâches planifiées** (kickoff, standup, clôture) depuis une session Claude, aux horaires définis dans `SYSTEME.md`, avec notifications selon vos préférences.
7. Créez vos premières fiches projet, vos récurrentes, quelques tâches — et lancez votre premier sprint.

## Personnalisation — champs à remplacer dans SYSTEME.md

| Champ | Rôle |
|---|---|
| `[NOM_OPERATEUR]` | le nom de votre IA opératrice |
| `[LANGUE]`, `[TON …]` | comment elle vous parle |
| `[EMAIL_CALENDRIER]` | le compte Google Calendar miroir (si utilisé) |
| `[JOUR_KICKOFF]`, `[JOURS_STANDUP]`, `[JOUR_CLOTURE]` | les horaires de vos rituels |

Les commits effectués par l'IA sont signés `Claude <noreply@anthropic.com>` ; les vôtres gardent votre identité git habituelle.

## Philosophie

Ce système est né d'un constat d'échec : un premier scrum personnel mort d'un rollover manqué de trop et de tâches reportées à l'infini sans décision. La v2 corrige les racines — automatisation réelle avec traçabilité, validation de schéma, et une règle d'abandon assumée. Le reste est volontairement minimal : des fichiers Markdown lisibles sans outil, qui vous survivront.
