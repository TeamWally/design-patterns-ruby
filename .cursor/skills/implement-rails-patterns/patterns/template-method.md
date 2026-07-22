# Template Method

## GoF (rappel)

Squelette d’algorithme dans une classe de base ; les sous-classes redéfinissent des étapes.

## Quand l’utiliser (Rails)

Pipelines d’import/export, sync multi-sources, jobs avec les mêmes étapes (fetch → transform → persist → notify).

## Forme Rails

```ruby
module Imports
  class BaseJob < ApplicationJob
    def perform
      rows = fetch
      records = transform(rows)
      persist(records)
      notify
    end

    private

    def fetch = raise NotImplementedError
    def transform(rows) = rows
    def persist(records) = raise NotImplementedError
    def notify = nil
  end

  class UsersJob < BaseJob
    def fetch = CsvSource.load("users.csv")
    def persist(records) = User.upsert_all(records)
    def notify = Slack.notify("users import done")
  end
end
```

Composition (hooks injectés) possible si l’héritage dérange.

## Pièges

- Trop d’hooks abstraits = rigidité ; limiter les étapes
- Préférer composition si les variantes divergent fortement
