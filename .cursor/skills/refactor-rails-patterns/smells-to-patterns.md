# Smells → Patterns (Rails)

Diagnostic rapide pour le skill **refactor-rails-patterns**. Un smell → **un** pattern principal par passe.

| Smell / symptôme dans le code Rails | Pattern cible | Indices |
|-------------------------------------|---------------|---------|
| Gros `if/elsif/case` sur un « type » (format, provider, canal) avec la même signature | **Strategy** | Même entrées/sorties, algos différents |
| `case status` / booléens d’état qui changent les transitions autorisées | **State** | Transitions + comportements liés à l’état |
| Appels HTTP/gem collés dans un modèle ou un service métier | **Adapter** | Port métier vs API tierce |
| Plusieurs providers (Stripe/PayPal) avec charge+refund+webhook cohérents | **Abstract Factory** | Familles de produits |
| `case format` répété pour instancier Csv/Xlsx/Pdf | **Factory Method** | Création polymorphique |
| Constructeur / méthode avec 10+ kwargs optionnels ordonnés | **Builder** | Objet complexe pas à pas |
| Controller/service qui orchestre 5+ collaborateurs (créer, payer, mailer, audit) | **Facade** | Point d’entrée unique |
| Logique de présentation (labels, URLs) dans le modèle AR | **Decorator** / Presenter | Enrichir sans gonfler le modèle |
| Même pipeline fetch→transform→persist recopié dans plusieurs jobs | **Template Method** | Squelette + hooks |
| Suite de filtres (coupon, loyalty, tax) appliqués dans l’ordre | **Chain of Responsibility** | Pipeline de handlers |
| Action métier mélangée au controller ; besoin undo/audit/async | **Command** (+ Job) | Encapsuler l’action |
| `after_commit` en chaîne opaque / effets de bord croisés | **Observer** | Notifications / listeners explicites |
| Duplication d’une ressource « modèle » (campagne, config) à la main | **Prototype** | Clone métier |
| Arbre (catégories, menus) traité différemment feuille vs nœud | **Composite** | API uniforme |
| Accès direct coûteux sans cache/lazy/contrôle | **Proxy** | Même interface, contrôle d’accès |
| Objets UI/domaine qui s’appellent tous entre eux | **Mediator** | Coordinateur central |
| Besoin d’undo / draft / snapshot ad hoc | **Memento** | Capture d’état (ou PaperTrail) |
| Opérations multiples (pdf, tax, print) sur une famille de documents | **Visitor** | Opérations stables, types stables |
| Abstraction × implémentation (type notif × canal) en explosion de classes | **Bridge** | Deux axes indépendants |
| Pagination API / curseur réinventé partout | **Iterator** | `Enumerable` / cursor |
| Constantes globales / classe « unique » difficile à tester | **Singleton** | Souvent → config / DI à la place |
| Milliers d’objets quasi identiques en mémoire | **Flyweight** | Rare en Rails classique |

## Heuristiques

1. **Comportement interchangeable** sans changer l’état de l’objet → Strategy  
2. **Comportement qui change avec l’état persistant** → State  
3. **Frontière IO** → Adapter (éventuellement Abstract Factory si familles)  
4. **Trop de collaborateurs au même endroit** → Facade ou Mediator  
5. Si deux patterns marchent → le plus petit changement qui améliore la lisibilité

## Hors scope d’un refactor « pattern »

- Extraire une méthode privée locale (suffit parfois — YAGNI)
- Changer le schéma DB sans besoin
- Réécrire tout un bounded context d’un coup
