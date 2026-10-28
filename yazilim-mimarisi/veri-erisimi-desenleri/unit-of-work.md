# Unit of Work

> Bir iş birimi içindeki tüm değişiklikleri tek transaction olarak toplar. Hepsi birlikte kaydedilir veya hiçbiri kaydedilmez.

## Problem

Bir iş akışı birden fazla repository'de değişiklik yaparsa ve aralarında bir hata çıkarsa veri yarım kalır (sipariş oluşur ama stok düşmez).

## Çözüm ve çalışma mantığı

Unit of Work, değişen nesneleri takip eder ve işin sonunda tek bir transaction ile veritabanına yazar.

{% stepper %}
{% step %}
## İş birimi başlatılır
{% endstep %}

{% step %}
## Repository'ler değişiklikleri iş birimine kaydeder
{% endstep %}

{% step %}
## İş bittiğinde `commit` çağrılır

Değişikliklerin hepsi tek transaction'da yazılır.
{% endstep %}

{% step %}
## Hata olursa `rollback` çağrılır

Hiçbir değişiklik kalıcı olmaz.
{% endstep %}
{% endstepper %}

Birçok ORM (Entity Framework, Prisma `$transaction`, TypeORM `QueryRunner`) bu deseni zaten içerir.

## Kod örneği

Kod örnekleri varsayılan olarak gizlidir. Görmek için başlığa tıklayın.

<details>

<summary>Kodu göster: TypeScript</summary>

```ts
class UnitOfWork {
  private constructor(private client: PoolClient) {}

  static async run<T>(pool: Pool, work: (client: PoolClient) => Promise<T>): Promise<T> {
    const client = await pool.connect();
    try {
      await client.query("BEGIN");
      const result = await work(client);
      await client.query("COMMIT");
      return result;
    } catch (e) {
      await client.query("ROLLBACK");
      throw e;
    } finally {
      client.release();
    }
  }
}

await UnitOfWork.run(pool, async (tx) => {
  await tx.query("INSERT INTO orders ...");
  await tx.query("UPDATE stock SET qty = qty - $1 WHERE id = $2", [qty, productId]);
});
```

</details>

## Ne zaman kullanılır

* Birden fazla aggregate veya tabloya yazan işlemlerde
* Atomik işlem gereken iş akışlarında

## Ne zaman kullanılmamalı

* ORM zaten transaction yönetimi sağlıyorsa ek bir sarmalama gereksizdir

## Artılar

* Atomik işlem
* Tek commit noktası
* Veritabanı yazma sayısı azalır

## Eksiler

* ORM'nin sunduğu özelliği tekrar sarmalamak gereksiz karmaşıklık yaratabilir
* Uzun süren iş birimleri kilitlere neden olabilir

## Sık yapılan hatalar

* İş birimini çok uzun tutmak
* Dış servis çağrılarını (HTTP) transaction içinde yapmak

## Gerçek hayattan örnekler

Entity Framework DbContext, Hibernate Session, Prisma transaction'ları.

## İlişkili desenler

* [Repository Pattern](repository.md): Birlikte çalışır
* [Outbox Pattern](../mikroservis-ve-dagitik-sistem-desenleri/outbox.md): Transaction içinde mesaj kaydetme
* [Saga Pattern](../mikroservis-ve-dagitik-sistem-desenleri/saga.md): Dağıtık işlemler için alternatif
