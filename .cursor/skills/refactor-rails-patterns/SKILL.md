---
name: refactor-rails-patterns
description: >-
  Refactors existing Ruby on Rails backend code toward Gang of Four design
  patterns (smell → pattern → incremental extract), reusing implement-rails-patterns
  guides. Use when the user asks to refactor toward Strategy/Adapter/Factory/
  Observer/Command/etc., replace conditionals or god objects with a pattern, or
  extract services/adapters/jobs from messy Rails code.
---

# Refactor Rails Design Patterns

Refactorise du code Rails backend **existant** vers un pattern GoF, en style **prod** (pas le style pédagogique `conceptual/main.rb`).

S’appuie sur le skill frère **implement-rails-patterns** (fiches + conventions) et, si disponible, sur le repo pédagogique **design-patterns-ruby** pour la structure conceptuelle.

## Quand utiliser ce skill

- L’utilisateur demande de **refactorer** / extraire / découpler vers un pattern
- Code smells clairs (gros `case`, modèle obèse, IO collée au domaine, etc.)
- Remplacer un amas de conditions par Strategy, State, Chain, etc.

Ne pas utiliser pour une feature greenfield → skill [implement-rails-patterns](../implement-rails-patterns/SKILL.md).  
Ne pas utiliser pour une revue seule (skill revue, si présent).

## Dépendances (lire avant d’écrire)

| Ressource | Chemin | Rôle |
|-----------|--------|------|
| Workflow & tableau besoins | [../implement-rails-patterns/SKILL.md](../implement-rails-patterns/SKILL.md) | Même vocabulaire GoF → Rails |
| Conventions dossiers / naming | [../implement-rails-patterns/rails-conventions.md](../implement-rails-patterns/rails-conventions.md) | Où poser le code extrait |
| Fiche pattern | [../implement-rails-patterns/patterns/](../implement-rails-patterns/patterns/) (`patterns/<kebab-name>.md`) | Forme Rails cible + pièges |
| Smells → pattern | [smells-to-patterns.md](smells-to-patterns.md) | Diagnostic |
| Playbook sûr | [playbook.md](playbook.md) | Étapes incrémentales & garde-fous |

Les deux skills doivent être installés **côte à côte** :

```
.cursor/skills/
  implement-rails-patterns/
  refactor-rails-patterns/
```

### Exemples GoF dans ce repo (`src/`)

Ce fork **est** la source pédagogique + skills. Pour les rôles / structure GoF, lire (sans recopier le style dans `app/`) :

```
src/<pattern_snake>/conceptual/main.rb
src/<pattern_snake>/real_world/main.rb   # s’il existe (ex. adapter, bridge, abstract_factory)
```

Si les skills sont **copiés** dans une app Rails Wally et que ce repo n’est pas le workspace :

- Cloner ce fork en voisin, ou
- Ouvrir ce repo en multi-root / référence, puis lire `src/...` via ce chemin

**Interdit** : copier commentaires EN/RU, noms `ConcreteStrategyA`, ou un unique `main.rb` dans `app/`.  
La cible reste la fiche `implement-rails-patterns/patterns/*.md` + conventions Rails du projet.

## Workflow

Copier et suivre :

```
Refactor pattern:
- [ ] 1. Cadrer le problème (fichier·s, smell, comportement à préserver)
- [ ] 2. Choisir le pattern (smells-to-patterns.md ; confirmer si ambigu)
- [ ] 3. Lire fiche implement patterns/<pattern>.md (+ rails-conventions.md)
- [ ] 4. (Optionnel) Lire src/<pattern>/conceptual/main.rb pour les rôles GoF
- [ ] 5. Caractériser : tests existants ou characterization specs minimales
- [ ] 6. Refactor incrémental (playbook.md) — petits commits logiques
- [ ] 7. Note GoF courte + vérifier les specs
```

### 1. Cadrer

Identifier :

- Point d’entrée (controller, job, service, modèle)
- Comportement observable à ne pas casser (API, side effects, retours)
- Contraintes (pas de changement de schéma DB sauf nécessité)

### 2. Choisir le pattern

1. Lire [smells-to-patterns.md](smells-to-patterns.md)
2. Un **seul** pattern principal par passe (éventuellement enchaîner plus tard)
3. Si hésitation : le plus simple qui réduit le smell ; mentionner l’alternative en une ligne
4. Si l’utilisateur nomme déjà le pattern → l’utiliser, sauf inadaptation claire (l’expliquer)

### 3–4. Lire les références

Ordre :

1. Fiche `../implement-rails-patterns/patterns/<pattern>.md`
2. `../implement-rails-patterns/rails-conventions.md` + conventions **locales** du repo app
3. Optionnel : `src/<pattern>/conceptual/main.rb` pour mapper Context / Strategy / Adapter → noms métier

### 5. Sécuriser le comportement

Avant d’extraire massivement :

- Lancer les specs concernées si elles existent
- Sinon, ajouter des **characterization tests** (comportement actuel, pas le design idéal)
- Mocker les IO (HTTP, mailers, APIs)

Détail : [playbook.md](playbook.md).

### 6. Refactor incrémental

Principes (voir playbook) :

1. Extraire sans changer le comportement (move / extract class)
2. Introduire le port / interface duck-typed
3. Brancher le nouvel objet derrière l’ancien point d’entrée
4. Supprimer le code mort seulement quand les appels sont migrés
5. Aligner naming / dossiers sur l’existant (`app/services`, `app/adapters`, …)

### 7. Note GoF + vérif

Comme pour implement :

```ruby
# GoF: Strategy — extrait les règles de pricing autrefois en case/when dans Order#total.
```

Relancer les specs touchées ; corriger avant de déclarer terminé.

## Anti-patterns du refactor

- Big-bang rewrite sans filet de tests
- Introduire un pattern « pour le pattern » (YAGNI)
- Dupliquer implement + laisser l’ancien chemin divergent
- Copier le style pédagogique du repo Refactoring.Guru dans `app/`
- Cascader 3 patterns d’un coup sans étapes intermédiaires

## Sortie attendue

1. Code Rails refactoré (noms métier, injectable)
2. Specs vertes (existantes ou characterization)
3. Note GoF courte
4. Résumé en 3–5 bullets : smell → pattern → fichiers touchés
