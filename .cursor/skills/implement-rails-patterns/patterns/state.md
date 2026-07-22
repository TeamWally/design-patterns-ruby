# State

## GoF (rappel)

Alterne le comportement d’un objet quand son état interne change.

## Quand l’utiliser (Rails)

Machines à états métier (`draft` → `published` → `archived`, commandes, tickets). Souvent `aasm`, `statesman`, ou enum + service transitions.

## Forme Rails

```ruby
module Orders
  class OrderState
    def ship!(order) = raise TransitionError
    def cancel!(order) = raise TransitionError
  end

  class Pending < OrderState
    def ship!(order) = order.transition_to!(:shipped)
    def cancel!(order) = order.transition_to!(:cancelled)
  end

  class Shipped < OrderState
    def cancel!(order) = raise TransitionError, "already shipped"
  end
end

class Order < ApplicationRecord
  def state_object
    { pending: Orders::Pending, shipped: Orders::Shipped }.fetch(status.to_sym).new
  end

  def ship! = state_object.ship!(self)
end
```

Si le projet a déjà AASM : **l’utiliser**, et annoter GoF: State.

## Pièges

- Transitions invalides = erreurs explicites
- Persister l’état ; ne pas garder l’état seulement en mémoire process
