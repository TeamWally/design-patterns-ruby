# Conventions Rails (style prod)

Adapter ces emplacements au codebase réel s’ils existent déjà sous d’autres noms.

## Emplacements par défaut

| Rôle | Emplacement typique |
|------|---------------------|
| Orchestration métier | `app/services/` |
| Stratégies / politiques | `app/services/<domaine>/strategies/` ou `app/domains/<domaine>/` |
| Adapters IO (HTTP, PSP, S3) | `app/adapters/` ou `app/clients/` |
| Construction complexe | `app/builders/` ou service Builder |
| Form / input validation | `app/forms/` ou `app/models/` (ActiveModel) |
| Requêtes lecture | `app/queries/` |
| Presenters / décorateurs UI | `app/presenters/` ou `app/decorators/` |
| Commandes async | `app/jobs/` (ActiveJob) |
| Événements domaine | `app/events/` + subscribers, ou `ActiveSupport::Notifications` |
| PORO partagés | `app/models/concerns/` (avec parcimonie) ou `lib/` |

Autoload Zeitwerk : respecter les constantes (`Pricing::Strategies::Volume` → `app/services/pricing/strategies/volume.rb`).

## API des objets

Préférer une API explicite et uniforme dans le projet :

```ruby
# Service / Command sync
Result = MyService.call(user:, params:)
# ou
MyService.new(user:).call(params)

# Job
MyJob.perform_later(id)

# Adapter
Payments::StripeAdapter.new.charge(amount_cents:, customer_id:)
```

Si le projet a déjà `Dry::Monads`, `Result`, Interactor, Trailblazer : **réutiliser** ce pattern, ne pas en inventer un second.

## Injection & testabilité

- Passer les dépendances au `initialize` (adapter, clock, logger)
- Éviter `MyAdapter.new` en dur au milieu d’un service si on doit le stubber
- Config via credentials / `ENV` / `Rails.application.config`, pas de constantes magiques éparpillées

## Naming

- Métier d’abord : `Billing::InvoicePdfExporter`, pas `ConcreteExporter1`
- Suffixes utiles : `Adapter`, `Strategy`, `Builder`, `Facade`, `Proxy`, `Handler` (chain), `Policy` (si proche Pundit)
- Eviter les préfixes `Abstract` / `Concrete` en prod

## Note GoF

Une fois par feature, pas sur chaque classe :

```ruby
module Billing
  # GoF: Facade — point d'entrée unique pour export facture (PDF + storage + notify).
  class InvoiceExportFacade
```

## Tests (RSpec)

```ruby
RSpec.describe Pricing::Calculator do
  it "délègue à la strategy injectée" do
    strategy = instance_double(Pricing::Strategies::Base, call: 42)
    expect(described_class.new(strategy:).call(cart)).to eq(42)
  end
end
```

- `instance_double` pour les ports (adapters)
- Factories FactoryBot pour les modèles AR
- Pas de hit réseau réel

## Ce qu’on ne copie pas du repo pédagogique

- Commentaires bilingues EN/RU
- Tout le pattern dans un seul `main.rb`
- Classes nommées `Creator`, `ProductA1`, `ConcreteStrategyB`
- `raise NotImplementedError` partout si un module/doc + duck typing suffit
