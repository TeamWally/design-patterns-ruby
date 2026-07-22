# Bridge

## GoF (rappel)

Sépare abstraction et implémentation pour les faire varier indépendamment.

## Quand l’utiliser (Rails)

Deux axes de variation (ex. **type de notification** × **canal** email/SMS/push) sans explosion de sous-classes.

## Forme Rails

```ruby
module Notifications
  class OrderShipped # abstraction
    def initialize(channel:)
      @channel = channel
    end

    def deliver(order)
      @channel.deliver(to: order.user, body: render(order))
    end

    private
    def render(order) = "Order #{order.id} shipped"
  end

  module Channels
    class Email
      def deliver(to:, body:) = OrderMailer.notice(to, body).deliver_later
    end

    class Sms
      def deliver(to:, body:) = SmsAdapter.new.send(to.phone, body)
    end
  end
end

Notifications::OrderShipped.new(channel: Notifications::Channels::Email.new).deliver(order)
```

## Pièges

- Si un seul axe varie → Strategy suffit
- Bridge ≠ Adapter (Adapter rattrape une API existante ; Bridge structure deux hiérarchies prévues)
