# Flyweight

## GoF (rappel)

Partage des états intrinsèques immuables pour réduire l’usage mémoire.

## Quand l’utiliser (Rails)

Rare en apps web classiques. Utile pour caches d’objets de référence immuables (règles tarifaires versionnées, templates, icônes metadata) chargés massivement.

## Forme Rails

```ruby
module Catalog
  class RuleBook
    @cache = {}

    def self.fetch(version)
      @cache[version] ||= JSON.parse(Rails.root.join("config/rules/#{version}.json").read).freeze
    end
  end

  class PricedItem
    def initialize(sku, rule_version:)
      @sku = sku
      @rules = RuleBook.fetch(rule_version) # partagé
    end
  end
end
```

## Pièges

- Ne pas « flyweightiser » des AR ActiveRecord (déjà en DB)
- État partagé = immuable ; l’état extrinsèque reste sur l’instance
