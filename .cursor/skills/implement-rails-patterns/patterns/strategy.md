# Strategy

## GoF (rappel)

Famille d’algorithmes encapsulés et interchangeables au runtime.

## Quand l’utiliser (Rails)

Pricing, scoring, export, authentification multi-mode, règles de commission — même entrée/sortie, implémentations différentes.

## Forme Rails

```ruby
# app/services/pricing/calculator.rb
module Pricing
  class Calculator
    def initialize(strategy:)
      @strategy = strategy
    end

    def call(cart) = @strategy.call(cart)
  end

  module Strategies
    class Flat
      def call(cart) = cart.subtotal_cents
    end

    class Volume
      def call(cart)
        discount = cart.item_count >= 10 ? 0.9 : 1.0
        (cart.subtotal_cents * discount).to_i
      end
    end
  end
end

strategy = user.vip? ? Pricing::Strategies::Volume.new : Pricing::Strategies::Flat.new
Pricing::Calculator.new(strategy:).call(cart)
```

## Pièges

- Strategy vs State : Strategy choisie de l’extérieur ; State change avec l’objet
- Éviter un `case` géant dans le calculator — la sélection peut vivre dans une factory légère
