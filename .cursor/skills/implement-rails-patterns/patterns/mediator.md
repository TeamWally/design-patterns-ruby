# Mediator

## GoF (rappel)

Objet central qui coordonne les interactions entre collègues, pour éviter un graphe de dépendances dense.

## Quand l’utiliser (Rails)

Wizards multi-étapes, chat/moderation, coordination entre plusieurs sous-services sans qu’ils se connaissent.

## Forme Rails

```ruby
module Support
  class TicketMediator
    def initialize(assigner:, notifier:, sla:)
      @assigner = assigner
      @notifier = notifier
      @sla = sla
    end

    def open(ticket)
      @assigner.assign(ticket)
      @sla.schedule(ticket)
      @notifier.created(ticket)
    end

    def resolve(ticket)
      @sla.cancel(ticket)
      @notifier.resolved(ticket)
    end
  end
end
```

Proche d’une Facade ; Mediator insiste sur le **dialogue** entre collègues.

## Pièges

- Mediator god-object : extraire quand trop de responsabilités
- Les collègues ne doivent pas s’appeler entre eux directement
