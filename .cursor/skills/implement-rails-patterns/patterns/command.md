# Command

## GoF (rappel)

Encapsule une requête comme objet (exécuter, annuler, journaliser, mettre en file).

## Quand l’utiliser (Rails)

Actions métier explicites, undo, audit, exécution différée → **ActiveJob** est souvent la Command async.

## Forme Rails

```ruby
# Sync command / service
module Billing
  class RefundOrder
    def initialize(order:, gateway: Payments.default)
      @order = order
      @gateway = gateway
    end

    def call
      @gateway.refund(@order.charge_id)
      @order.update!(status: :refunded)
      Audit.log!(action: :refund, order_id: @order.id)
    end
  end
end

# Async
class RefundOrderJob < ApplicationJob
  def perform(order_id)
    Billing::RefundOrder.new(order: Order.find(order_id)).call
  end
end
```

## Pièges

- Ne pas dupliquer Command sync + Job sans factoriser le cœur métier
- Undo : stocker un memento / événement inverse si vraiment requis
