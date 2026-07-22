# Factory Method

## GoF (rappel)

Délègue la création d’objets aux sous-classes / variantes, le flux restant dans le créateur.

## Quand l’utiliser (Rails)

Créer le bon type selon un discriminant (`export_format`, `report_type`, STI) sans `case` partout dans les controllers.

## Forme Rails

```ruby
module Exports
  class Generator
    def self.for(format)
      case format.to_sym
      when :csv  then Csv
      when :xlsx then Xlsx
      when :pdf  then Pdf
      else raise ArgumentError, format
      end
    end

    def call(scope) = raise NotImplementedError
  end

  class Csv < Generator
    def call(scope) = CsvRenderer.new.render(scope)
  end

  class Xlsx < Generator
    def call(scope) = XlsxRenderer.new.render(scope)
  end
end

Exports::Generator.for(params[:format]).new.call(scope)
```

Variante sans héritage : hash de classes / registry.

## Pièges

- Un seul `case` dans la factory, zéro duplication ailleurs
- Préférer registry si les formats sont pluggables par gems
