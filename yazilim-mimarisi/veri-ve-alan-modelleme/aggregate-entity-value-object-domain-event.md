# Aggregate, Entity, Value Object, Domain Event

> Bu dört yapı taşı, domain modelini kurmak için kullanılır: Entity, Value Object, Aggregate ve Domain Event.

## Problem

Domain kuralları dağınık veri sınıflarında ve servislerde yaşarsa tutarsızlık oluşur. Hangi nesne neyi değiştirebilir, hangi kural nerede korunur belli olmaz.

## Çözüm ve çalışma mantığı

* **Entity:** Kimliği (ID) olan, zaman içinde değişse de aynı kalan nesne (Müşteri, Sipariş).
* **Value Object:** Kimliği olmayan, değerleriyle tanımlanan ve değişmez nesne (Para, Adres, Tarih aralığı).
* **Aggregate:** Birlikte tutarlı olması gereken nesne kümesi. **Aggregate Root** tek giriş noktasıdır, dışarıdan iç nesnelere doğrudan erişilmez.
* **Domain Event:** Domain'de gerçekleşmiş önemli bir olayın kaydı (`OrderPlaced`).

Aggregate Root, kendi iç kurallarını (invariant) korur. Dışarıdan yalnızca root'un metotlarıyla değişiklik yapılır.

## Aggregate kuralları

1. Bir işlemde (transaction) yalnızca bir aggregate değiştirilir.
2. Aggregate'ler birbirine nesneyle değil kimlikle (ID) referans verir.
3. Aggregate'ler küçük tutulur.
4. Dış dünya yalnızca Aggregate Root ile konuşur.

## Entity ve Value Object farkı

|                | Entity      | Value Object         |
| -------------- | ----------- | -------------------- |
| Kimlik         | Var         | Yok                  |
| Eşitlik        | ID ile      | Değerlerle           |
| Değişebilirlik | Değişebilir | Değişmez (immutable) |
| Örnek          | Müşteri     | Para (100 TRY)       |

## Kod örneği

Kod örnekleri varsayılan olarak gizlidir. Görmek için başlığa tıklayın.

<details>

<summary>Kodu göster: TypeScript</summary>

```ts
// Value Object
class Money {
  constructor(readonly amount: number, readonly currency: string) {
    Object.freeze(this);
  }
  add(other: Money) {
    if (other.currency !== this.currency) throw new Error("Para birimi farklı");
    return new Money(this.amount + other.amount, this.currency);
  }
  equals(o: Money) { return o.amount === this.amount && o.currency === this.currency; }
}

// Domain Event
type OrderPlaced = { type: "OrderPlaced"; orderId: string; at: Date };

// Entity + Aggregate Root
class OrderLine {
  constructor(readonly productId: string, readonly qty: number, readonly price: Money) {}
}

class Order {
  private lines: OrderLine[] = [];
  private events: OrderPlaced[] = [];

  constructor(readonly id: string) {}

  addLine(productId: string, qty: number, price: Money) {
    if (qty <= 0) throw new Error("Miktar pozitif olmalı");
    this.lines.push(new OrderLine(productId, qty, price));
  }

  place() {
    if (this.lines.length === 0) throw new Error("Boş sipariş verilemez");
    this.events.push({ type: "OrderPlaced", orderId: this.id, at: new Date() });
  }

  pullEvents() { return this.events.splice(0); }
}
```

</details>

## Ne zaman kullanılır

* Zengin iş kurallarına sahip domain'lerde
* DDD taktik tasarımı uygulanan projelerde

## Ne zaman kullanılmamalı

* Basit CRUD veri akışlarında

## Artılar

* İş kuralları tek yerde ve korunmuş
* Geçersiz durumların oluşması zorlaşır
* Domain olayları ile gevşek bağlı akışlar kurulur

## Eksiler

* Doğru aggregate sınırını bulmak zordur
* Büyük aggregate'ler performans ve eşzamanlılık sorunları yaratır

## Sık yapılan hatalar

* Çok büyük aggregate tasarlamak
* Aggregate'ler arasında nesne referansı tutmak
* Value Object yerine ilkel tip (string, number) kullanmak

## Gerçek hayattan örnekler

Para birimi bir Value Object, Sipariş bir Aggregate Root, "Sipariş verildi" bir Domain Event'tir.

## İlişkili desenler

* [DDD (Domain-Driven Design)](ddd.md): Üst yaklaşım
* [Repository Pattern](../veri-erisimi-desenleri/repository.md): Aggregate'i saklayan desen
* [Event Sourcing](event-sourcing.md): Domain event'lerin kalıcı hali
