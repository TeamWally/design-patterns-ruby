# Abstract Factory

## GoF (rappel)

Familles de produits liés (ex. UI Mac/Win) créées sans coupler le client aux classes concrètes.

## Quand l’utiliser (Rails)

Plusieurs **providers** cohérents (paiement + webhook + refund Stripe vs PayPal ; storage S3 vs GCS avec la même API métier).

## Forme Rails

```
app/adapters/payments/
  factory.rb          # choisit la famille
  stripe/
    charge.rb
    refund.rb
  paypal/
    charge.rb
    refund.rb
```

```ruby
module Payments
  class Factory
    def self.for(provider)
      case provider.to_sym
      when :stripe then Stripe
      when :paypal then Paypal
      else raise ArgumentError, "unknown provider: #{provider}"
      end
    end
  end

  module Stripe
    module_function
    def charge(...) = Charge.new(...).call
    def refund(...) = Refund.new(...).call
  end
end

# Usage
Payments::Factory.for(:stripe).charge(amount_cents: 1000, customer_id: "cus_1")
```

## Pièges

- Ne pas créer une Abstract Factory pour un seul provider
- Le `case` provider peut vivre dans la Factory uniquement ; le reste du domaine parle l’API commune
