# Saga

> Birden fazla servise yayılan bir iş akışını, dağıtık transaction kullanmadan yerel transaction'lar zinciriyle ve telafi işlemleriyle yönetir.

## Problem

Mikroservislerde her servisin kendi veritabanı vardır. Tek bir ACID transaction ile birden fazla servisi güncellemek mümkün değildir. Bir adım başarısız olursa önceki adımlar nasıl geri alınacak?

## Çözüm ve çalışma mantığı

Her adım, kendi servisinde yerel bir transaction'dır. Her adımın bir **telafi (compensating) işlemi** vardır.

{% stepper %}
{% step %}
## Sipariş oluşturulur
{% endstep %}

{% step %}
## Ödeme alınır
{% endstep %}

{% step %}
## Stok rezerve edilir
{% endstep %}

{% step %}
## Kargo planlanır

Bir adım başarısız olursa, tamamlanmış adımların telafi işlemleri ters sırayla çalışır (stok iadesi, ödeme iadesi, siparişin iptali).
{% endstep %}
{% endstepper %}

## Yapı

```
Sipariş -> Ödeme -> Stok -> Kargo
              | hata
              v
 Stok iade <- Ödeme iade <- Sipariş iptal   (telafi adımları)
```

## Saga türleri

| Tür           | Nasıl çalışır                                           | Avantaj                | Dezavantaj              |
| ------------- | ------------------------------------------------------- | ---------------------- | ----------------------- |
| Choreography  | Servisler olaylara tepki verir, merkezi yönetici yoktur | Gevşek bağlılık        | Akışı takip etmek zor   |
| Orchestration | Merkezi bir orkestratör adımları sırayla yönetir        | Akış tek yerde görünür | Orkestratör bağımlılığı |

## Dikkat edilecek noktalar

* Telafi işlemleri **idempotent** olmalıdır.
* Her adım başarısızlıkta ne yapacağını bilmelidir.
* Ara durumlar (örneğin "ödeme alındı ama stok henüz ayrılmadı") dışarıdan görülebilir.

## Kod örneği

<details>

<summary>Kodu göster: TypeScript</summary>

```ts
type Step = { name: string; run: () => Promise<void>; compensate: () => Promise<void> };

async function runSaga(steps: Step[]) {
  const done: Step[] = [];
  try {
    for (const step of steps) {
      await step.run();
      done.push(step);
    }
  } catch (err) {
    for (const step of done.reverse()) {
      await step.compensate();   // yapılanları ters sırayla geri al
    }
    throw err;
  }
}

await runSaga([
  { name: "payment", run: () => payment.charge(orderId), compensate: () => payment.refund(orderId) },
  { name: "stock",   run: () => stock.reserve(orderId),  compensate: () => stock.release(orderId) },
  { name: "shipping",run: () => shipping.schedule(orderId), compensate: () => shipping.cancel(orderId) },
]);
```

</details>

## Ne zaman kullanılır

* Mikroservislerde birden fazla servisi kapsayan iş akışları
* Dağıtık transaction (2PC) kullanılamayan ortamlar

## Ne zaman kullanılmamalı

* Tek veritabanında çözülebilen işlerde (normal transaction yeterlidir)

## Artılar

* 2PC olmadan tutarlılık sağlar
* Servisler bağımsız kalır
* Ölçeklenebilir

## Eksiler

* Telafi mantığı yazılmalıdır
* Ara durumlar görünür (nihai tutarlılık)
* Hata ayıklama ve test zordur

## Sık yapılan hatalar

* Telafi işlemi tasarlamamak
* Her adımı idempotent yazmamak
* Süresi uzun akışlarda durumu kalıcı saklamamak

## Gerçek hayattan örnekler

Temporal, Camunda, MassTransit Saga, NServiceBus, AWS Step Functions.

## İlişkili desenler

* [EDA (Event-Driven Architecture)](../sistem-ve-dagitim-mimarileri/eda.md): Choreography olaylarla çalışır
* [Outbox Pattern](outbox.md): Olayları güvenle yayınlamak için
* [Microservices](../sistem-ve-dagitim-mimarileri/microservices.md): Kullanıldığı mimari
