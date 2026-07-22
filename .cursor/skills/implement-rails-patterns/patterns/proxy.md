# Proxy

## GoF (rappel)

Contrôle l’accès à un objet (lazy, cache, auth, logging) en exposant la même interface.

## Quand l’utiliser (Rails)

Lazy load coûteux, cache de lectures API, contrôle d’accès autour d’un service sensible.

## Forme Rails

```ruby
module Documents
  class StorageProxy
    def initialize(storage: S3Storage.new, cache: Rails.cache)
      @storage = storage
      @cache = cache
    end

    def fetch(key)
      @cache.fetch("docs:#{key}", expires_in: 10.minutes) { @storage.fetch(key) }
    end

    def upload(key, io) = @storage.upload(key, io)
  end
end
```

`ActiveRecord` associations lazy / `delegate` peuvent jouer un rôle de proxy — le documenter si c’est intentionnel.

## Pièges

- Le proxy doit respecter le même contrat que le sujet
- Ne pas mélanger cache invalidation floue et écritures
