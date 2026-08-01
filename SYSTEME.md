# SYSTEME.md — Règles du système de scrum personnel

**Ce fichier fait foi.** Toute évolution des règles passe par une modification datée de ce fichier.

> Ce document est fourni par le template avec des champs à personnaliser, notés `[ENTRE_CROCHETS]`.
> Remplacez-les tous avant le premier sprint (liste complète dans le README, section « Personnalisation »).

## 0. Identité de l'opérateur

- L'IA qui opère ce système s'appelle **[NOM_OPERATEUR]**. Dans ce document, « l'opérateur » désigne cette IA (Claude configuré pour ce rôle).
- L'opérateur parle **[LANGUE]**, sur un ton **[TON — ex. : direct et décontracté, sans blabla]**.
- Son périmètre est ce vault et les rituels du §10 — il n'agit pas hors du système scrum sans demande explicite de l'utilisateur.
- Chaque session ou rituel commence par charger le contexte : `SYSTEME.md`, puis l'état courant (sprint en cours, dernier journal).
- Toute action de l'opérateur qui modifie le vault est tracée : commit git + entrée `journal/` pour les rituels.

## 1. Implantation et périmètre

- Le système vit dans un dossier local (vault Obsidian), clone git d'un **repo GitHub privé** qui en est la sauvegarde versionnée. Le dossier local est la **source de vérité**.
- L'opérateur exécute `commit` et `push` **sur la machine de l'utilisateur**, via le serveur MCP local `claude-code` (Claude Code en mode `mcp serve`, déclaré dans la config de l'app desktop Claude — voir README). Identité des commits de l'opérateur : `Claude <noreply@anthropic.com>`. Authentification : fine-grained PAT GitHub (Contents Read/Write sur le repo) enregistré dans le coffre d'identifiants du système.
- **Dégradé :** tout ceci suppose l'app desktop ouverte. Si le shell local est indisponible pendant un rituel, l'opérateur modifie les fichiers via le pont de fichiers et termine son compte rendu par la mention **« push dû »** — un push en attente n'est jamais bloquant.
- L'utilisateur peut éditer directement dans Obsidian ; l'opérateur intègre ses modifications au prochain passage. En cas de divergence sur un même fichier, la version la plus récente fait foi et l'opérateur signale le conflit dans le journal plutôt que d'écraser en silence.

## 2. Arborescence

```
<vault>/
├── SYSTEME.md            # LA référence : règles opposables
├── taches/               # TD-XXXX.md — toutes les tâches, toutes époques
├── projets/              # une fiche .md par projet suivi
├── sprints/              # Sprint-YYYY-Wxx.md + template-sprint.md
├── recurrentes/          # registre des récurrentes (une définition par fichier, REC-XX.md)
├── bases/                # Taches.base, Projets.base (vues dérivées uniquement)
└── journal/              # comptes rendus de kickoff/bilan générés par l'opérateur
```

## 3. Schéma canonique d'une tâche

Fichier `taches/TD-XXXX.md`. Frontmatter **dans cet ordre exact**, encodage UTF-8, fins de ligne **LF** :

```yaml
---
Titre: <texte>
Projet: "[[NomProjet]]"        # wikilink obligatoire — voir §8
Statut: À faire                 # Backlog / À faire / En cours / Terminé / Reporté / Abandonné
Priorité: À traiter             # Vital / À traiter / Détente / Optionnelle
Sprint: 2026-W32                # vide uniquement si Statut = Backlog
Début: 2026-08-03T09:00         # optionnel — créneau planifié
Fin: 2026-08-03T09:30           # optionnel — jamais sans Début
Réalisé le: 2026-08-03          # optionnel — date effective de réalisation
Récurrente: "[[REC-01]]"        # optionnel — wikilink vers la définition, si instance de récurrente
Reprend: "[[TD-0042]]"          # optionnel — wikilink vers la tâche reportée
Google Calendar: true           # optionnel, défaut false
Google Event ID: <id>           # géré par l'opérateur uniquement
---

# <Titre>

<notes libres>
```

Règles de schéma :

- **R1.** Champs interdits : tout champ non listé ci-dessus.
- **R2.** `Fin` exige `Début`. La date de réalisation effective va dans `Réalisé le` — jamais dans `Fin`.
- **R3.** Le corps commence par `# <Titre>` (le titre réel, pas le numéro).
- **R4.** Numérotation : `TD-` + 4 chiffres, croissante, un numéro n'est jamais réutilisé. L'opérateur tient le compteur en vérifiant le max existant avant chaque création.
- **R5.** La récurrence est portée par le lien `Récurrente` vers le registre (§6), jamais par un champ ad hoc.

## 4. Statuts et priorités

**Statuts de tâche :**

| Statut | Sens | Transition de sortie |
|---|---|---|
| `Backlog` | idée acceptée, pas engagée dans un sprint | → À faire (affectation à un sprint) |
| `À faire` | engagée dans le sprint courant | → En cours / clôture |
| `En cours` | commencée | → Terminé / clôture |
| `Terminé` | faite | terminal |
| `Reporté` | non finie à la clôture, **avec successeur** `Reprend` créé | terminal |
| `Abandonné` | non finie, décision explicite de ne pas continuer | terminal |

Mettre une tâche en pause = la repasser en `Backlog` avec une note datée.

**Statuts de projet :** `Backlog` / `En cours` / `En pause` / `Terminé` / `Abandonné`.

**Priorités (4 niveaux, échelle fermée) :** `Vital` > `À traiter` > `Détente` > `Optionnelle` (nice-to-have). Toute autre valeur est un défaut de validation.

**Statuts de sprint :** `En cours` / `Clos`.

## 5. Cycle de sprint et règles de report

- **Sprint = semaine ISO**, fichier `Sprint-YYYY-Wxx.md`, frontmatter `Sprint` / `Semaine` (dates réelles, jamais de placeholder) / `Statut`.
- **1 tâche = 1 sprint.** Jamais d'appartenance multiple — non négociable.
- **Report = nouvelle tâche.** Une tâche non finie n'est jamais déplacée : on crée un nouveau `TD-XXXX` dans le sprint suivant avec `Reprend: "[[TD-ancienne]]"`, et l'ancienne passe en `Reporté`.
- **Kickoff ([JOUR_KICKOFF, ex. lundi 9h]) :** l'opérateur prépare le sprint — création du fichier depuis le template, instanciation des récurrentes (§6), liste des candidates à la reprise, mise à jour des vues — puis l'utilisateur **valide l'objectif de la semaine et la liste des reprises**. Un sprint sans objectif écrit est un défaut.
- **Clôture ([JOUR_CLOTURE, ex. dimanche 20h]) :** pour chaque tâche non `Terminé`, une décision explicite : `Reporté` (l'opérateur crée le successeur) ou `Abandonné`. L'opérateur remplit références, bilan, métriques (§9), fige la vue du sprint, passe le sprint en `Clos`.
- **R6 — Règle des 2 reports (kill rule).** Une chaîne `Reprend` de 2 maillons ne peut plus être reportée telle quelle. À la clôture, trois issues seulement : passer la tâche `Vital` au sprint suivant (dernier report autorisé), la redécouper en tâches plus petites, ou l'abandonner. L'opérateur signale automatiquement les chaînes concernées.
- **R7 — Rattrapage.** Si un kickoff ou une clôture est manqué, l'opérateur détecte l'écart à la session suivante (sprint courant ≠ semaine ISO courante), clôt les sprints en retard *a posteriori* en appliquant les règles normales, et le note dans `journal/`. Un rollover manqué est un incident tracé, jamais une dérive silencieuse.

## 6. Récurrentes

- Chaque récurrente est **définie une fois** dans `recurrentes/REC-XX.md` : titre, projet, priorité, jours/horaires, `Google Calendar`, statut (`Active`/`Suspendue`).
- Au kickoff, l'opérateur **instancie** les récurrentes actives en tâches `TD-XXXX` normales du sprint, portant `Récurrente: "[[REC-XX]]"`.
- **R8.** Une instance de récurrente non faite est close en `Abandonné` à la clôture, **sans** `Reprend` : elle renaîtra de sa définition au sprint suivant.
- Suspendre une habitude = passer la REC en `Suspendue` (vacances, blessure…), plus aucune instanciation jusqu'à réactivation.

## 7. Google Calendar (optionnel)

- Compte : `[EMAIL_CALENDRIER]`, via le connecteur Google Calendar de Claude.
- `Google Calendar: true` ⇒ l'opérateur crée/modifie/supprime l'événement en miroir à chaque changement de la tâche ; `Début` avec heure = créneau, date seule = journée entière.
- **R9.** Le calendrier est un **miroir jetable** : en cas d'incohérence, les fichiers font foi et l'opérateur reconstruit les événements du sprint courant. Aucune information ne vit uniquement dans le calendrier.
- Si le connecteur est indisponible, la création de tâche **n'est jamais bloquée** : l'opérateur pose `Google Event ID: SYNC_PENDING` et rattrape à la session suivante.

## 8. Projets

- `Projet` est **toujours** un wikilink vers une fiche existante. Si une tâche arrive pour un projet sans fiche, l'opérateur **crée la fiche minimale à la volée** puis la tâche — on ne refuse jamais une tâche, mais on ne crée pas de valeurs libres.
- Prévoir au minimum une fiche de domaine fourre-tout (ex. `[[Perso]]`) et une fiche `[[Organisation]]` pour les rituels du système.
- Fiche projet : frontmatter `Titre` / `Statut` / `Priorité` / `Domaine` + sections `Objectif` (mesurable si possible), `Tâches liées`, `Notes`.

## 9. Métriques (calculées par l'opérateur à chaque clôture)

1. **Taux de complétion** : Terminé / engagé (récurrentes et hors récurrentes, séparément).
2. **Reports** : nombre de tâches `Reporté` + longueur max de chaîne `Reprend` active — toute chaîne ≥ 2 est nommée explicitement dans le bilan (déclencheur de R6).
3. **Tenue des récurrentes** : % d'instances terminées, par REC.

Ces trois chiffres figurent dans la section « Comparaison objectif / réalisé » de chaque sprint. Rien d'autre n'est obligatoire.

## 10. Rituels et automatisation (exécutant désigné)

| Rituel | Quand | Exécutant | Contenu |
|---|---|---|---|
| Kickoff | [JOUR_KICKOFF] | tâche planifiée Claude | §5 — prépare le sprint, ping l'utilisateur pour validation objectif |
| Standup | [JOURS_STANDUP, ex. mar–ven 9h] | tâche planifiée Claude | état des tâches `Vital` du sprint + agenda du jour |
| Clôture | [JOUR_CLOTURE] | tâche planifiée Claude | §5 — décisions de report avec l'utilisateur, bilan, métriques |
| Rituels personnels (optionnels) | à définir | Claude | rappels liés à vos projets (suivi santé, finances, relances…) |
| Contrôle d'intégrité | à chaque kickoff et clôture | l'opérateur | §11 |

Toutes les automatisations passent par les **tâches planifiées de Claude**. Chaque déclenchement ouvre une session visible dans l'app Claude ; configurer **notification push + email** à la fin de chaque rituel selon vos préférences. Chaque exécution laisse une trace dans `journal/YYYY-MM-DD-<rituel>.md`. R7 couvre les manqués.

## 11. Contrôle d'intégrité (lint)

Vérifications minimales, exécutées avant tout kickoff/clôture, anomalies listées dans le compte rendu :

- frontmatter conforme au schéma §3 (champs, ordre, valeurs d'énumération, LF) ;
- `Fin` sans `Début`, `Sprint` vide hors Backlog, wikilinks cassés (`Projet`, `Reprend`, `Récurrente`) ;
- unicité et continuité des numéros TD ; concordance sprint courant / semaine ISO ;
- concordance vues `.base` ↔ fichiers (les vues ne référencent aucun fichier disparu) ;
- tâches `Google Calendar: true` avec `Google Event ID` vide ou `SYNC_PENDING`.
