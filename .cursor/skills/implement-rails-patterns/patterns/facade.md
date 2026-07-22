# Facade

## GoF (rappel)

Interface simple face à un sous-système complexe.

## Quand l’utiliser (Rails)

Orchestrer plusieurs services (créer compte + Stripe customer + mail + audit) derrière un seul point d’entrée application/API.

## Forme Rails

```ruby
module Onboarding
  class SignupFacade
    def initialize(payments: Payments::Factory.for(:stripe), mailer: UserMailer)
      @payments = payments
      @mailer = mailer
    end

    def call(params)
      user = User.create!(params)
      @payments.create_customer(user)
      @mailer.welcome(user).deliver_later
      Audit.log!(:signup, user_id: user.id)
      user
    end
  end
end
```

Souvent un service dans `app/services/` nommé sans suffixe `Facade` si l’équipe préfère — le suffixe aide à documenter le rôle GoF.

## Pièges

- Facade ≠ fourre-tout : déléguer, ne pas absorber toute la logique
- Garder des services sous-jacents testables isolément
