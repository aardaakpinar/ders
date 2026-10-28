# Event-Driven Architecture

> Bileşenler birbirini doğrudan çağırmak yerine olay yayınlar ve dinler. Yayıncı, dinleyiciyi bilmez.

## Problem

Bir servis, bir işlem sonrası birçok başka servisi doğrudan çağırdığında sıkı bağlanır. Yeni bir dinleyici eklemek, yayıncıyı değiştirmeyi gerektirir.

## Çözüm ve çalışma mantığı

* **Yayıncı (producer):** Bir şey olduğunda olay yayınlar.
* **Broker:** Olayları taşır ve dağıtır (Kafka, RabbitMQ, SNS/SQS).
* **Tüketici (consumer):** İlgilendiği olaylara abone olur ve tepki verir.

{% stepper %}
{% step %}
## Sipariş olayını yayınlama

Sipariş servisi `OrderCreated` olayını yayınlar.
{% endstep %}

{% step %}
## Olayın iletilmesi

Broker olayı abonelere iletir.
{% endstep %}

{% step %}
## Olayın işlenmesi

Stok, e-posta ve analitik servisleri olayı bağımsız işler.
{% endstep %}
{% endstepper %}

Olaylar geçmiş zamanla adlandırılır (`OrderCreated`), çünkü olmuş bir gerçeği bildirir.

## Yapı

```
Sipariş Servisi --> [OrderCreated] --> Message Broker
                                          +--> Stok Servisi
                                          +--> E-posta Servisi
                                          +--> Analitik
```

## Mesaj türleri

| Tür     | Anlamı                  | Örnek            |
| ------- | ----------------------- | ---------------- |
| Event   | Bir şey oldu (geçmiş)   | `OrderCreated`   |
| Command | Şunu yap (gelecek)      | `ReserveStock`   |
| Query   | Şunu söyle (bilgi iste) | `GetOrderStatus` |

## Dikkat edilecek noktalar

* **Nihai tutarlılık:** Tüketiciler olayı bir süre sonra işler.
* **Idempotency:** Aynı olay tekrar gelebilir, tüketici bunu güvenle işlemelidir.
* **Sıralama:** Olayların sırası bazı senaryolarda önemlidir.
* **Şema sürümleme:** Olay yapısı değiştiğinde eski tüketiciler bozulmamalıdır.

## Kod örneği

{% code title="Kodu göster: TypeScript" expandable="true" %}
```ts
type OrderCreated = { type: "OrderCreated"; orderId: string; total: number };

class EventBus {
  private handlers = new Map<string, Array<(e: any) => Promise<void>>>();
  on(type: string, h: (e: any) => Promise<void>) {
    this.handlers.set(type, [...(this.handlers.get(type) ?? []), h]);
  }
  async publish(event: { type: string }) {
    for (const h of this.handlers.get(event.type) ?? []) await h(event);
  }
}

bus.on("OrderCreated", async (e: OrderCreated) => sendMail(e.orderId));
bus.on("OrderCreated", async (e: OrderCreated) => reduceStock(e.orderId));
await bus.publish({ type: "OrderCreated", orderId: "o1", total: 250 } as OrderCreated);
```
{% endcode %}

## Ne zaman kullanılır

* Bileşenlerin gevşek bağlı olması gereken sistemler
* Bir olaya birden fazla tepki verilecekse
* Asenkron ve ölçeklenebilir işlemler

## Ne zaman kullanılmamalı

* Anında tutarlı yanıt gereken basit akışlarda
* Ekip dağıtık sistemlerde hata ayıklama deneyimine sahip değilse

## Artılar

* Gevşek bağlılık
* Yeni tüketici eklemek kolay
* Asenkron ölçeklenme
* Yayıncı arızalara karşı daha dayanıklı

## Eksiler

* Akışı baştan sona takip etmek zordur
* Nihai tutarlılık kabul edilmelidir
* Mesaj tekrarı ve sıralama sorunları
* Test ve hata ayıklama karmaşıktır

## Sık yapılan hatalar

* Olayı komut gibi kullanmak
* Olaya çok fazla veya çok az veri koymak
* Tüketicileri idempotent yazmamak

## Gerçek hayattan örnekler

Apache Kafka, RabbitMQ, AWS SNS/SQS, Azure Service Bus tabanlı sistemler.

## İlişkili desenler

* [Event Sourcing](../veri-ve-alan-modelleme/event-sourcing.md): Olayların kalıcı kayıt olarak saklanması
* [Saga Pattern](../mikroservis-ve-dagitik-sistem-desenleri/saga.md): Olaylarla dağıtık iş akışı
* [Outbox Pattern](../mikroservis-ve-dagitik-sistem-desenleri/outbox.md): Olayı güvenle yayınlama
