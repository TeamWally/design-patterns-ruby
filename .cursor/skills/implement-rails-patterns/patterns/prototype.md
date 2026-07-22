# Prototype

## GoF (rappel)

Crée de nouveaux objets en clonant une instance prototype.

## Quand l’utiliser (Rails)

Dupliquer une ressource métier (facture modèle, campagne, config) avec ajustements.

## Forme Rails

```ruby
class Campaign < ApplicationRecord
  has_many :steps

  def duplicate!(attrs = {})
    proto = deep_clone include: :steps
    proto.assign_attributes(attrs.merge(status: :draft, name: "#{name} (copy)"))
    proto.save!
    proto
  end
end

# Ou service
module Campaigns
  class Cloner
    def initialize(campaign) = @campaign = campaign
    def call(**overrides) = @campaign.duplicate!(overrides)
  end
end
```

Gems : `amoeba`, `deep_cloneable` si graphe riche.

## Pièges

- Décider quoi cloner (associations, fichiers, IDs externes)
- Réinitialiser états (`paid`, tokens) explicitement
