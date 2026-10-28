# Repository

> Veri erişimini, domain'e bellek içi bir koleksiyon gibi görünen bir arayüzün arkasına saklar.

## Problem

Domain kodu SQL, ORM sorguları ve veritabanı detaylarıyla doluysa iş kuralları teknolojiye bağlanır ve test için gerçek veritabanı gerekir.

## Çözüm ve çalışma mantığı

Domain katmanı bir `Repository` arayüzü tanımlar (`getById`, `add`, `remove`). Altyapı katmanı bu arayüzü gerçek bir veritabanıyla uygular.

{% stepper %}
{% step %}
## Use case repository arayüzünü çağırır
{% endstep %}

{% step %}
## Repository uygulaması veritabanından veriyi alıp domain nesnesine çevirir
{% endstep %}

{% step %}
## Domain mantığı çalışır
{% endstep %}

{% step %}
## Değişiklik repository ile kaydedilir
{% endstep %}
{% endstepper %}

Genel kural: tablo başına değil, **aggregate başına** bir repository tanımlanır. ORM zaten benzer bir soyutlama sunuyorsa üzerine anlamsız bir sarmalama eklenmez.

## Kod örneği

{% code title="TypeScript" expandable="true" collapsedlinecount="1" %}
```ts
interface OrderRepository {
  getById(id: string): Promise<Order | null>;
  add(order: Order): Promise<void>;
  remove(id: string): Promise<void>;
}

class PgOrderRepository implements OrderRepository {
  constructor(private db: Pool) {}

  async getById(id: string) {
    const { rows } = await this.db.query("SELECT * FROM orders WHERE id = $1", [id]);
    return rows[0] ? OrderMapper.toDomain(rows[0]) : null;
  }
  async add(order: Order) { /* INSERT */ }
  async remove(id: string) { /* DELETE */ }
}

// Test için bellek içi sürüm
class InMemoryOrderRepository implements OrderRepository {
  private store = new Map<string, Order>();
  async getById(id: string) { return this.store.get(id) ?? null; }
  async add(o: Order) { this.store.set(o.id, o); }
  async remove(id: string) { this.store.delete(id); }
}
```
{% endcode %}

## Ne zaman kullanılır

* Domain mantığını veri teknolojisinden ayırmak istenen projeler
* Domain testlerinin bellek içi sahte depo ile yazılması hedefleniyorsa

## Ne zaman kullanılmamalı

* Basit CRUD uygulamalarında (ORM doğrudan kullanılabilir)
* Karmaşık raporlama sorgularında (CQRS okuma modeli veya doğrudan sorgu daha uygundur)

## Artılar

* Domain veri teknolojisinden bağımsız
* Test için sahte repository kolay yazılır
* Sorgu mantığı tek yerde toplanır

## Eksiler

* Basit durumlarda gereksiz soyutlama
* Her sorgu için metot eklemek arayüzü şişirir
* Yanlış kullanılırsa ORM'nin gücünü gizler

## Sık yapılan hatalar

* Her tablo için generic repository yazmak
* Repository içinde iş mantığı çalıştırmak
* Arayüzden sorgu oluşturucu nesneleri döndürmek (sızdıran soyutlama)

## Gerçek hayattan örnekler

Prisma, TypeORM, Entity Framework ve Spring Data üzerine kurulan domain repository'leri.

## İlişkili desenler

* [Unit of Work Pattern](unit-of-work.md): Değişiklikleri tek transaction'da toplar
* [Specification Pattern](specification.md): Karmaşık sorgu koşulları için
* [Data Mapper](data-mapper.md): Domain ve veritabanı arasındaki eşleme
* [Aggregate, Entity, Value Object, Domain Event](../veri-ve-alan-modelleme/aggregate-entity-value-object-domain-event.md): Aggregate başına repository
