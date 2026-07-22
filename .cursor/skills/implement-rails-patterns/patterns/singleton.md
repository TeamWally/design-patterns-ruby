# Singleton

## GoF (rappel)

Garantit une unique instance et un point d’accès global.

## Quand l’utiliser (Rails)

**Rarement.** Préférer :

- `Rails.application.config`
- credentials / ENV
- injection de dépendances
- `ActiveSupport::CurrentAttributes` pour l’état par requête (pas un vrai Singleton app-wide)

## Forme Rails (si vraiment nécessaire)

```ruby
module FeatureFlags
  class Client
    include Singleton

    def initialize
      @sdk = LaunchDarkly::Client.new(Rails.application.credentials.ld_key)
    end

    def enabled?(flag, user) = @sdk.variation(flag, user, false)
  end
end

# Préférer souvent :
# FeatureFlags::Client.new dans un initializer assigné à config.x.flags
```

## Pièges

- Testabilité : singletons globaux = douleur (reset, ordre des specs)
- Thread-safety en Puma : vérifier le client sous-jacent
- Documenter pourquoi Singleton plutôt que config injectée
