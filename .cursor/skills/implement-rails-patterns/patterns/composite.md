# Composite

## GoF (rappel)

Compose des objets en arbre ; traite uniformément feuilles et nœuds.

## Quand l’utiliser (Rails)

Catégories imbriquées, menus, permissions arborescentes, bundles de produits.

## Forme Rails

Souvent un modèle AR auto-référent (`parent_id`) + API uniforme :

```ruby
class Category < ApplicationRecord
  belongs_to :parent, class_name: "Category", optional: true
  has_many :children, class_name: "Category", foreign_key: :parent_id

  def leaf? = children.none?

  def all_product_ids
    return product_ids if leaf?
    children.flat_map(&:all_product_ids)
  end
end
```

PORO si l’arbre n’est pas persisté :

```ruby
MenuItem = Data.define(:label, :children) do
  def render = children.empty? ? label : "#{label}: #{children.map(&:render).join(", ")}"
end
```

## Pièges

- N+1 : préférer CTE / `ancestry` / `closure_tree` pour grands arbres
- Ne pas exposer la structure interne aux controllers
