# Decorator

## GoF (rappel)

Ajoute des responsabilités à un objet dynamiquement via composition.

## Quand l’utiliser (Rails)

Présentation (labels, URLs, formatage) sans gonfler le modèle AR ; wrapping d’IO (retry, logging) autour d’un adapter.

## Forme Rails

```ruby
# Présentation
class UserPresenter < SimpleDelegator
  def display_name
    name.presence || email
  end
end

# Ou app/presenters/user_presenter.rb avec composition explicite
class UserPresenter
  def initialize(user) = @user = user
  def display_name = @user.name.presence || @user.email
  def method_missing(...) = @user.send(...)
  def respond_to_missing?(...) = @user.respond_to?(...)
end
```

```ruby
# Décorateur technique
class RetryingGateway
  def initialize(gateway, attempts: 3)
    @gateway = gateway
    @attempts = attempts
  end

  def charge(...)
    attempts = @attempts
    begin
      @gateway.charge(...)
    rescue TransientError
      retry if (attempts -= 1).positive?
      raise
    end
  end
end
```

## Pièges

- Éviter Draper/callbacks si un presenter simple suffit
- Ne pas mettre de logique métier lourde dans un décorateur de vue
