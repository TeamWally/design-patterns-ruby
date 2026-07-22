# Adapter

## GoF (rappel)

Convertit l’interface d’une classe existante en celle attendue par le client.

## Quand l’utiliser (Rails)

Gem, API HTTP, ou legacy dont l’API ne match pas le port métier (`PaymentGateway`, `Geocoder`, `Mailer`).

## Forme Rails

```
app/adapters/geo/nominatim_adapter.rb
app/adapters/geo/google_adapter.rb
# port implicite: #geocode(address) -> Result
```

```ruby
module Geo
  class NominatimAdapter
    def initialize(http: HttpClient)
      @http = http
    end

    def geocode(address)
      body = @http.get("/search", q: address)
      Coordinates.new(lat: body.fetch("lat"), lng: body.fetch("lon"))
    end
  end
end

# Domaine
class PlaceResolver
  def initialize(geocoder: Geo::NominatimAdapter.new)
    @geocoder = geocoder
  end

  def call(address) = @geocoder.geocode(address)
end
```

## Pièges

- L’adapter traduit ; la logique métier reste hors de l’adapter
- Normaliser erreurs / timeouts ici pour exposer des erreurs domaine stables
