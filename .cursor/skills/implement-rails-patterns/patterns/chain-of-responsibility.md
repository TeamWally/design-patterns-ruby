# Chain of Responsibility

## GoF (rappel)

Passe une requête le long d’une chaîne de handlers jusqu’à ce qu’un la traite (ou tous contribuent).

## Quand l’utiliser (Rails)

Pipelines : authentification → authorization → rate-limit → validation ; moderation de contenu ; calcul de remise empilable.

## Forme Rails

```ruby
module Checkout
  class DiscountPipeline
    def initialize(handlers)
      @handlers = handlers
    end

    def call(cart)
      @handlers.reduce(cart) { |memo, handler| handler.call(memo) }
    end
  end

  module Handlers
    class Coupon
      def call(cart)
        return cart unless cart.coupon
        cart.apply_coupon!
      end
    end

    class Loyalty
      def call(cart) = cart.apply_loyalty_points!; cart
    end
  end
end
```

Ou middleware Rack / `ActiveSupport::Callbacks` si le cadre existe déjà.

## Pièges

- Ordre des handlers = contrat ; le documenter / tester
- Éviter chaînes mutables globales difficiles à raisonner
