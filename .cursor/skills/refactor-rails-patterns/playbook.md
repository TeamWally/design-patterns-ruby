# Playbook — refactor sûr vers un pattern

Checklist opérationnelle pour **refactor-rails-patterns**. Objectif : même comportement, meilleure structure.

## Avant toute modification

```
Préflight:
- [ ] Fichiers / classes concernés listés
- [ ] Smell nommé + pattern choisi (smells-to-patterns.md)
- [ ] Fiche lue : ../implement-rails-patterns/patterns/<pattern>.md
- [ ] Conventions locales repérées (app/services, Result, etc.)
- [ ] (Optionnel) src/<pattern>/conceptual/main.rb parcouru pour les rôles GoF
- [ ] Specs existantes identifiées OU characterization prévue
```

### Characterization tests

Si peu ou pas de tests sur le comportement à préserver :

1. Écrire des specs qui figent le **comportement actuel** (y compris bizarreries)
2. Couvrir le chemin nominal + 1–2 edge cases visibles dans le code
3. Mocker HTTP / mailers / clock
4. Ne refactorer qu’après vert

## Séquence incrémentale (recommandée)

### Étape A — Extraire sans inverser les dépendances

- Extract Method / Extract Class vers un PORO encore appelé comme avant
- Aucun changement d’API publique (controller params, job args)
- Vert → continuer

### Étape B — Introduire le pattern

- Créer la structure cible (Strategy classes, Adapter, Facade, …) selon la fiche implement
- Noms **métier**, dossiers alignés sur [rails-conventions.md](../implement-rails-patterns/rails-conventions.md)
- Brancher derrière l’ancien point d’entrée (feature flag optionnel si risque élevé)

### Étape C — Migrer les appels

- Remplacer les `case` / duplications un site à la fois
- Garder un shim déprécié si d’autres gemmes/engines appellent encore l’ancienne API

### Étape D — Nettoyer

- Supprimer code mort, commentaires obsolètes
- Ajouter la note GoF courte (module ou PR)
- Specs unitaires sur les nouvelles classes + spec d’intégration légère sur le point d’entrée

## Garde-fous

| Faire | Ne pas faire |
|-------|----------------|
| Petits diffs relisables | Rewrite big-bang |
| Un pattern par passe | Cascader Strategy+Facade+Observer d’un coup |
| Garder les tests verts entre étapes | Casser puis « on fixera plus tard » |
| Réutiliser conventions du repo app | Inventer une arborescence parallèle |
| Référencer `src/...` pour comprendre | Copier le style pédagogique dans `app/` |

## Rollback

- Chaque étape = commit logique (si l’utilisateur le demande) pour revert facile
- Si characterization rouge après extract → revert l’étape, ne pas empiler

## Definition of Done

- [ ] Smell initial réduit ou éliminé au point d’entrée
- [ ] Specs vertes (characterization + nouvelles)
- [ ] Note GoF présente
- [ ] Résumé smell → pattern → fichiers pour l’utilisateur
