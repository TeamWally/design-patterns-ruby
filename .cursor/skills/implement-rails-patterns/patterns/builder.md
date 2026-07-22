# Builder

## GoF (rappel)

Construit un objet complexe étape par étape ; le même process peut produire des représentations différentes.

## Quand l’utiliser (Rails)

Payloads API, requêtes Elasticsearch, emails multi-parts, objets avec beaucoup d’attributs optionnels cohérents.

## Forme Rails

```ruby
module Reports
  class ExportBuilder
    def initialize
      @filters = {}
      @columns = []
    end

    def for_account(account) = tap { @account = account }
    def between(from, to) = tap { @filters[:range] = from..to }
    def with_columns(*cols) = tap { @columns = cols }
    def format(fmt) = tap { @format = fmt }

    def build
      raise ArgumentError, "account required" unless @account
      ExportRequest.new(account: @account, filters: @filters, columns: @columns, format: @format || :csv)
    end
  end
end

request = Reports::ExportBuilder.new.for_account(account).between(from, to).with_columns(:id, :total).build
```

## Pièges

- Prefer keyword args / form object si la construction est simple
- Valider à `build`, pas silencieusement
