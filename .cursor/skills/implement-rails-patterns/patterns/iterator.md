# Iterator

## GoF (rappel)

Parcourt une collection sans exposer sa représentation.

## Quand l’utiliser (Rails)

Souvent déjà fourni par `Enumerable` / `ActiveRecord::Relation`. Custom iterator pour APIs paginées, curseurs, graphes.

## Forme Rails

```ruby
module Crm
  class ContactCursor
    include Enumerable

    def initialize(client:, page_size: 100)
      @client = client
      @page_size = page_size
    end

    def each
      return enum_for(__method__) unless block_given?
      cursor = nil
      loop do
        page = @client.list_contacts(cursor:, limit: @page_size)
        page.items.each { |c| yield c }
        break unless page.next_cursor
        cursor = page.next_cursor
      end
    end
  end
end

Crm::ContactCursor.new(client:).find { |c| c.email == email }
```

## Pièges

- Préférer `find_each` / `in_batches` pour les Relations AR
- Ne pas charger toute la collection en mémoire dans `#each`
