# Data Mapper

> Domain nesneleri ile veritabanı arasındaki dönüşümü ayrı bir katman yapar. Domain nesnesi veritabanını bilmez.

## Problem

Domain modeli ile veritabanı şeması farklı şekillerde olabilir. Nesneyi doğrudan tabloya bağlarsak her iki taraf da diğerinin kısıtlarına takılır.

## Çözüm ve çalışma mantığı

Bir **mapper**, domain nesnesini veritabanı satırına ve satırı domain nesnesine çevirir.

{% stepper %}
{% step %}
## Repository veriyi veritabanından satır olarak alır.
{% endstep %}

{% step %}
## Mapper satırı domain nesnesine çevirir.
{% endstep %}

{% step %}
## Domain nesnesi iş kuralını çalıştırır.
{% endstep %}

{% step %}
## Kaydederken mapper nesneyi satıra çevirir.

Domain nesnesi veritabanı kolonlarını, ID üretimini veya kaydetme mantığını bilmez.
{% endstep %}
{% endstepper %}

## Yapı

```
Domain Nesnesi <-- Mapper --> Veritabanı Satırı
```

## Active Record ile karşılaştırma

|                      | Data Mapper                 | Active Record              |
| -------------------- | --------------------------- | -------------------------- |
| Kaydetme sorumluluğu | Ayrı mapper veya repository | Nesnenin kendisi           |
| Domain saflığı       | Yüksek                      | Düşük                      |
| Geliştirme hızı      | Orta                        | Yüksek                     |
| Uygun senaryo        | Karmaşık domain             | CRUD ağırlıklı uygulamalar |

## Kod örneği

Kod örnekleri varsayılan olarak gizlidir. Görmek için başlığa tıklayın.

<details>

<summary>Kodu göster: TypeScript</summary>

```ts
class User {
  constructor(readonly id: string, readonly email: string, readonly createdAt: Date) {}
  isCorporate() { return this.email.endsWith("@sirket.com"); }
}

type UserRow = { id: string; email: string; created_at: string };

const UserMapper = {
  toDomain: (r: UserRow) => new User(r.id, r.email, new Date(r.created_at)),
  toRow: (u: User): UserRow => ({ id: u.id, email: u.email, created_at: u.createdAt.toISOString() }),
};
```

</details>

## Ne zaman kullanılır

* Domain modeli veritabanı şemasından farklı olduğunda
* Domain mantığının veritabanından bağımsız test edilmesi gerektiğinde

## Ne zaman kullanılmamalı

* Basit CRUD uygulamalarında (Active Record daha hızlıdır)

## Artılar

* Domain saf kalır
* Model ve şema birbirinden bağımsız evrilir
* Test kolay

## Eksiler

* Ek kod ve yapılandırma
* Öğrenme eğrisi
* Basit işler için gereğinden fazla

## Sık yapılan hatalar

* Mapper'a iş mantığı koymak
* Domain nesnesine veritabanı ile ilgili bilgi eklemek

## Gerçek hayattan örnekler

Hibernate, Entity Framework, Prisma, TypeORM (Data Mapper modu), MikroORM.

## İlişkili desenler

* [Active Record](active-record.md): Alternatif yaklaşım
* [Repository Pattern](repository.md): Mapper'ı repository kullanır
* [DTO (Data Transfer Object)](dto.md): Dışarıya açılan veri taşıyıcısı
