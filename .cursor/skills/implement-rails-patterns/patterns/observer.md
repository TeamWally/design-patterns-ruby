# Observer

## GoF (rappel)

Notification automatique des dépendants quand l’état d’un sujet change.

## Quand l’utiliser (Rails)

Effets de bord découplés : analytics, cache bust, webhooks sortants, emails après action métier.

## Forme Rails

```ruby
# Option 1 — ActiveSupport::Notifications
ActiveSupport::Notifications.instrument("order.paid", order_id: order.id)

ActiveSupport::Notifications.subscribe("order.paid") do |*args|
  event = ActiveSupport::Notifications::Event.new(*args)
  Analytics.track("order_paid", event.payload)
end
```

```ruby
# Option 2 — callbacks AR (avec parcimonie)
after_commit :notify_paid, on: :update, if: :paid?

# Option 3 — pub/sub explicite
module Orders
  class Publisher
    def self.paid(order)
      [AnalyticsListener, WebhookListener].each { |l| l.order_paid(order) }
    end
  end
end
```

## Pièges

- Trop de `after_commit` = couplage invisible ; préférer notifications / listeners explicites
- Listeners idempotents si jobs async
