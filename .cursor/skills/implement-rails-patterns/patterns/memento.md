# Memento

## GoF (rappel)

Capture et restaure l’état interne d’un objet sans violer l’encapsulation.

## Quand l’utiliser (Rails)

Drafts, undo d’éditeur, versioning ponctuel. Souvent remplacé par `paper_trail`, JSON `draft` column, ou event sourcing.

## Forme Rails

```ruby
module Editor
  class Document
    attr_accessor :title, :body

    def save_memento = Memento.new(title:, body:)

    def restore(memento)
      @title = memento.title
      @body = memento.body
    end
  end

  Memento = Data.define(:title, :body)

  class History
    def initialize = @stack = []
    def push(m) = @stack << m
    def pop = @stack.pop
  end
end
```

Persistance : table `document_versions` ou colonne `snapshots`.

## Pièges

- Ne pas exposer les champs internes du memento aux clients
- Pour l’audit long terme, préférer PaperTrail / événements
