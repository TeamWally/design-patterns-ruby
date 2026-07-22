---
name: implement-rails-patterns
description: >-
  Implements Gang of Four design patterns as production-ready Ruby on Rails
  backend code (services, adapters, jobs, forms, etc.), with a short GoF
  conceptual note. Use when the user asks to implement a design pattern, apply
  Strategy/Adapter/Factory/Observer/Command/Decorator/etc. in Rails, scaffold
  a pattern-based feature, or generate backend code following GoF patterns.
---

# Implement Rails Design Patterns

Applique les patterns GoF en **code Rails backend prêt pour la prod**. Style pédagogique du repo source = référence conceptuelle seulement.

## Quand utiliser ce skill

- L’utilisateur demande d’**implémenter** un pattern nommé (Strategy, Adapter, …)
- Il demande une feature backend clairement adaptée à un pattern GoF
- Il veut un squelette Rails aligné sur les conventions de l’équipe

Ne pas utiliser pour un gros refactor de code existant → skill [refactor-rails-patterns](../refactor-rails-patterns/SKILL.md).  
Ne pas utiliser pour une revue de code seule (skill revue, si présent).

## Workflow

Copier et suivre :

```
Implémentation pattern:
- [ ] 1. Identifier le pattern (nommé ou déduit)
- [ ] 2. Lire la fiche patterns/<pattern>.md
- [ ] 3. Lire rails-conventions.md si emplacement / naming incertains
- [ ] 4. Explorer le codebase (dossiers existants, style voisin)
- [ ] 5. Implémenter en style Rails prod (noms métier)
- [ ] 6. Ajouter la note GoF (courte)
- [ ] 7. Proposer / écrire les specs RSpec minimales
```

### 1. Identifier le pattern

Si l’utilisateur nomme le pattern → utiliser ce nom.

Si le besoin est décrit sans nom → choisir **un** pattern principal via le tableau ci-dessous, confirmer en une phrase, puis implémenter.

| Besoin backend | Pattern |
|----------------|---------|
| Plusieurs algos interchangeables (prix, export, scoring) | Strategy |
| API / gem tierce avec interface incompatible | Adapter |
| Création d’objets sans coupler aux classes concrètes | Factory Method |
| Familles d’objets cohérentes (multi-provider) | Abstract Factory |
| Construction pas à pas d’un objet complexe | Builder |
| Cloner un modèle / config plutôt que reconstruire | Prototype |
| Une seule instance globale de config/runtime | Singleton *(éviter si DI possible)* |
| Uniformiser feuille + arbre (menus, catégories) | Composite |
| Ajouter comportement sans modifier la classe | Decorator |
| Simplifier un sous-système complexe | Facade |
| Partager état immuable massivement dupliqué | Flyweight |
| Contrôler l’accès / lazy load / cache | Proxy |
| Pipeline de handlers (auth, validation, filtres) | Chain of Responsibility |
| Encapsuler une action (undo, jobs, audit) | Command |
| Parcourir une collection sans exposer la structure | Iterator |
| Médier le dialogue entre objets UI/domaine | Mediator |
| Snapshot / undo d’état | Memento |
| Notifier des abonnés d’un changement | Observer |
| Comportement selon état métier | State |
| Squelette d’algo avec étapes surchargeables | Template Method |
| Opérations sur une structure d’objets sans la modifier | Visitor |

En cas d’hésitation entre 2 patterns : implémenter le plus simple qui résout le besoin ; mentionner l’alternative en une ligne.

### 2–3. Lire les fiches

- Fiche pattern : [patterns/](patterns/) — fichier `patterns/<kebab-name>.md`
- Conventions Rails : [rails-conventions.md](rails-conventions.md)

### 4. Explorer le projet

Avant d’écrire :

1. Repérer où l’équipe place déjà services / adapters / jobs / forms
2. Réutiliser les conventions locales (naming, `call` / `perform`, Result objects, etc.)
3. Ne pas inventer une arborescence parallèle si une existe

### 5. Implémenter (style prod)

- Noms **métier** (`Pricing::VolumeStrategy`, pas `ConcreteStrategyA`)
- Interfaces via duck typing + doc/`# @abstract`, ou module partagé — pas de hiérarchie GoF verbeuse sauf besoin réel
- Une responsabilité claire par classe ; fichiers courts
- Injectable / testable (pas de globals cachés)
- Suivre [rails-conventions.md](rails-conventions.md)

### 6. Note GoF (obligatoire, courte)

Dans le commit message, la description PR, **ou** un commentaire de module d’1–3 lignes :

```ruby
# GoF: Strategy — algorithmes de pricing interchangeables via une interface commune.
# Le Context est Pricing::Calculator ; les strategies vivent sous Pricing::Strategies.
```

Pas de pavé pédagogique EN/RU dans le code prod.

### 7. Tests

- Specs unitaires sur chaque stratégie / adapter / commande
- Spec du context / facade qui orchestre
- Mocker les IO (HTTP, mailers, APIs) ; pas les PORO purs sans raison

## Anti-patterns à éviter

- Copier le style `conceptual/main.rb` du repo pédagogique tel quel dans `app/`
- Introduire un pattern « pour le pattern » (YAGNI)
- Singleton quand un objet injecté ou `Rails.application.config` suffit
- God Facade qui cache tout le domaine
- Decorator / Observer ActiveRecord en chaîne incontrôlable (callbacks)

## Fiches patterns

| Pattern | Fichier |
|---------|---------|
| Abstract Factory | [patterns/abstract-factory.md](patterns/abstract-factory.md) |
| Adapter | [patterns/adapter.md](patterns/adapter.md) |
| Bridge | [patterns/bridge.md](patterns/bridge.md) |
| Builder | [patterns/builder.md](patterns/builder.md) |
| Chain of Responsibility | [patterns/chain-of-responsibility.md](patterns/chain-of-responsibility.md) |
| Command | [patterns/command.md](patterns/command.md) |
| Composite | [patterns/composite.md](patterns/composite.md) |
| Decorator | [patterns/decorator.md](patterns/decorator.md) |
| Facade | [patterns/facade.md](patterns/facade.md) |
| Factory Method | [patterns/factory-method.md](patterns/factory-method.md) |
| Flyweight | [patterns/flyweight.md](patterns/flyweight.md) |
| Iterator | [patterns/iterator.md](patterns/iterator.md) |
| Mediator | [patterns/mediator.md](patterns/mediator.md) |
| Memento | [patterns/memento.md](patterns/memento.md) |
| Observer | [patterns/observer.md](patterns/observer.md) |
| Prototype | [patterns/prototype.md](patterns/prototype.md) |
| Proxy | [patterns/proxy.md](patterns/proxy.md) |
| Singleton | [patterns/singleton.md](patterns/singleton.md) |
| State | [patterns/state.md](patterns/state.md) |
| Strategy | [patterns/strategy.md](patterns/strategy.md) |
| Template Method | [patterns/template-method.md](patterns/template-method.md) |
| Visitor | [patterns/visitor.md](patterns/visitor.md) |
