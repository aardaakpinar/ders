# Active Record

> Nesne hem veriyi hem de kendi kaydetme ve yükleme işlemlerini taşır.

## Problem

Basit bir uygulamada nesne ile veritabanı arasında ayrı mapper, repository ve soyutlama katmanları kurmak gereğinden fazla iş getirir.

## Çözüm ve çalışma mantığı

Her sınıf bir tabloya karşılık gelir. Nesne, kendi `find`, `save` ve `delete` metotlarına sahiptir.

{% stepper %}
{% step %}
## `User.find(1)`

Veritabanından kaydı alıp nesneyi oluşturur.
{% endstep %}

{% step %}
## Alanları değiştirin
{% endstep %}

{% step %}
## `user.save()`

Değişikliği veritabanına yazar.
{% endstep %}
{% endstepper %}

Veri erişimi ve iş mantığı aynı sınıfta olduğu için hızlı geliştirilir. Ancak iş kuralı arttıkça sınıf iki sorumluluğu birden taşır.

## Data Mapper ile karşılaştırma

|                      | Active Record    | Data Mapper                 |
| -------------------- | ---------------- | --------------------------- |
| Kaydetme sorumluluğu | Nesnenin kendisi | Ayrı mapper veya repository |
| Domain saflığı       | Düşük            | Yüksek                      |
| Geliştirme hızı      | Yüksek           | Orta                        |
| Uygun senaryo        | CRUD ağırlıklı   | Karmaşık domain             |

## Kod örneği

<details>

<summary>Kodu göster: JavaScript</summary>

```js
class User {
  constructor({ id, email }) { this.id = id; this.email = email; }

  static async find(id) {
    const { rows } = await db.query("SELECT * FROM users WHERE id = $1", [id]);
    return rows[0] ? new User(rows[0]) : null;
  }

  async save() {
    await db.query(
      "INSERT INTO users (id, email) VALUES ($1, $2) ON CONFLICT (id) DO UPDATE SET email = $2",
      [this.id, this.email]
    );
  }
}

const user = await User.find(1);
user.email = "yeni@mail.com";
await user.save();
```

</details>

## Ne zaman kullanılır

* CRUD ağırlıklı uygulamalar
* Hızlı geliştirme ve prototip
* Küçük ekipler

## Ne zaman kullanılmamalı

* Karmaşık domain kuralları olan sistemlerde
* Domain'in veritabanından bağımsız test edilmesi gereken projelerde

## Artılar

* Çok hızlı geliştirme
* Basit ve anlaşılır
* Az kod

## Eksiler

* İş mantığı ve veri erişimi iç içe
* Test için veritabanı gerekebilir
* Karmaşık domain'de zor ölçeklenir

## Sık yapılan hatalar

* Karmaşıklık artmasına rağmen deseni değiştirmemek
* Active Record sınıflarını doğrudan API'den döndürmek

## Gerçek hayattan örnekler

Ruby on Rails ActiveRecord, Laravel Eloquent, Django ORM, Sequelize.

## İlişkili desenler

* [Data Mapper](data-mapper.md): Daha ayrık alternatif
* [Transaction Script / CRUD](../uygulama-ici-mimari-stiller/transaction-script-crud.md): Basit CRUD yaklaşımı
