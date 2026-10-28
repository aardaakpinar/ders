# Outbox

> Mesaj, iş verisiyle aynı transaction içinde bir `outbox` tablosuna yazılır. Ayrı bir süreç bu tabloyu okuyup mesajları broker'a iletir.

## Problem

Veritabanına yazıp aynı anda broker'a mesaj göndermek atomik değildir. Biri başarılı, diğeri başarısız olursa tutarsızlık oluşur. Buna **dual write** problemi denir.

## Çözüm ve çalışma mantığı

{% stepper %}
{% step %}
### İş verisi ve olay yazılır

İş verisi ve olay, aynı transaction içinde yazılır (`orders` ve `outbox` tabloları).
{% endstep %}

{% step %}
### Transaction onaylanır

İkisi birlikte kaydedilir veya hiçbiri kaydedilmez.
{% endstep %}

{% step %}
### Relay süreci mesajları okur

**Relay** süreci `outbox` tablosunu periyodik okur (veya değişiklik akışını CDC ile izler).
{% endstep %}

{% step %}
### Mesaj broker'a iletilir

Mesajı broker'a iletir ve kaydı "yayınlandı" olarak işaretler.
{% endstep %}
{% endstepper %}

Mesajlar **en az bir kez** (at-least-once) teslim edilir, yani tekrarlanabilir. Bu nedenle tüketiciler idempotent olmalıdır. Gelen mesaj kimliklerini kaydederek tekrarı engelleyen yöntem **Inbox Pattern** olarak bilinir.

## Yapı

```
+------------- Tek Transaction -------------+
| INSERT orders       INSERT outbox(event)   |
+--------------------------------------------+
                    |
        Relay (periyodik okuma veya CDC)
                    |
                    v
             Message Broker
```

## Kod örneği

<details>

<summary>Kodu göster: TypeScript</summary>

```ts
// 1) İş verisi ve olay aynı transaction'da yazılır
await UnitOfWork.run(pool, async (tx) => {
  await tx.query("INSERT INTO orders (id, total) VALUES ($1, $2)", [order.id, order.total]);
  await tx.query(
    "INSERT INTO outbox (id, type, payload, published) VALUES ($1, $2, $3, false)",
    [crypto.randomUUID(), "OrderCreated", JSON.stringify({ orderId: order.id })]
  );
});

// 2) Relay: yayınlanmamış mesajları broker'a iletir
setInterval(async () => {
  const { rows } = await pool.query("SELECT * FROM outbox WHERE published = false LIMIT 50");
  for (const msg of rows) {
    await broker.publish(msg.type, msg.payload);
    await pool.query("UPDATE outbox SET published = true WHERE id = $1", [msg.id]);
  }
}, 1000);
```

</details>

## Ne zaman kullanılır

* Veritabanı değişikliği ile olay yayınının tutarlı olması gereken tüm sistemler
* Saga ve olay tabanlı mimarilerde

## Ne zaman kullanılmamalı

* Mesajlaşma olmayan sistemlerde
* Mesaj kaybının önemsiz olduğu durumlarda

## Artılar

* Mesaj kaybı olmaz
* Veri ve olay tutarlı kalır
* Broker geçici olarak erişilemezse sistem çalışmaya devam eder

## Eksiler

* Ek tablo ve relay süreci
* Mesajlar tekrarlanabilir
* Gecikme (relay aralığı kadar)

## Sık yapılan hatalar

* Tüketiciyi idempotent yazmamak
* Outbox tablosunu hiç temizlememek
* Relay sürecini tek kopya çalıştırıp hata toleransını düşünmemek

## Gerçek hayattan örnekler

Debezium ile CDC tabanlı outbox, MassTransit ve NServiceBus'ın outbox özellikleri.

## İlişkili desenler

* [EDA (Event-Driven Architecture)](../sistem-ve-dagitim-mimarileri/eda.md): Yayınlanan olaylar
* [Saga Pattern](saga.md): Saga adımlarında güvenilir mesajlaşma
* [Unit of Work Pattern](../veri-erisimi-desenleri/unit-of-work.md): Aynı transaction'ı paylaşır
