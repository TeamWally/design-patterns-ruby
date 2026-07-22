# Visitor

## GoF (rappel)

Sépare des opérations d’une structure d’objets ; nouvelles opérations sans modifier les éléments.

## Quand l’utiliser (Rails)

Rare. Utile pour AST, règles composites, rapports multi-types (`Invoice`, `CreditNote`, `Proforma`) avec opérations export/tax/print.

## Forme Rails

```ruby
module Billing
  module Visitable
    def accept(visitor) = visitor.visit(self)
  end

  class Invoice
    include Visitable
  end

  class CreditNote
    include Visitable
  end

  class PdfVisitor
    def visit(node)
      case node
      when Invoice then render_invoice(node)
      when CreditNote then render_credit_note(node)
      else raise ArgumentError, node.class.name
      end
    end
  end
end

documents.each { |d| d.accept(Billing::PdfVisitor.new) }
```

En Ruby, un `case` / pattern matching sur types remplace souvent le double dispatch classique.

## Pièges

- Si les types changent souvent et les opérations peu → méthodes sur les modèles
- Si les opérations changent souvent et les types stables → Visitor (ou serializers dédiés)
