# CQRS

> Yazma (Command) ve okuma (Query) modelleri birbirinden ayrılır.

## Problem

Aynı model hem karmaşık yazma kurallarını hem de çok sayıda farklı okuma ihtiyacını karşılamaya çalıştığında ikisi de kötü olur. Okuma için gereken birleştirmeler, yazma modelini kirletir.

## Çözüm ve çalışma mantığı

* **Command:** Durumu değiştirir (`PlaceOrder`). Genellikle veri döndürmez.
* **Query:** Durumu değiştirmez, veri döndürür (`GetOrderSummary`).

{% stepper %}
{% step %}
## İstemci bir komut gönderir
{% endstep %}

{% step %}
## Yazma modeli kuralları uygular ve durumu kaydeder
{% endstep %}

{% step %}
## Gerekirse okuma modeli güncellenir

Okuma modeli ayrı bir depoda olabilir.
{% endstep %}

{% step %}
## Sorgular okumaya optimize edilmiş modelden cevap alır
{% endstep %}
{% endstepper %}

CQRS ayrı veritabanı **gerektirmez**. Aynı veritabanında yalnızca kod ayrımı da CQRS'tir. Okuma tarafı ayrı depoya taşınırsa nihai tutarlılık devreye girer.

## Yapı

```
Command --> Yazma Modeli --> Yazma DB --(senkronizasyon)--> Okuma DB --> Query
```

## Command ve Query karşılaştırması

|           | Command                 | Query                           |
| --------- | ----------------------- | ------------------------------- |
| Amaç      | Durumu değiştirir       | Veriyi okur                     |
| Dönüş     | Genelde yok veya kimlik | DTO veya okuma modeli           |
| Model     | Zengin domain modeli    | Düz, hızlı, denormalize görünüm |
| Ölçekleme | Yazma yüküne göre       | Okuma yüküne göre               |

## Kod örneği

Kod örnekleri varsayılan olarak gizlidir. Görmek için başlığa tıklayın.

<details>

<summary>Kodu göster: TypeScript</summary>

```ts
// Command: durum değiştirir
type PlaceOrder = { customerId: string; items: string[] };
async function placeOrderHandler(cmd: PlaceOrder, repo: OrderRepository): Promise<string> {
  const order = Order.create(cmd.customerId, cmd.items);
  await repo.save(order);
  return order.id;
}

// Query: yalnızca okur, doğrudan düz bir görünüm döner
type OrderSummary = { id: string; customerName: string; total: number };
async function getOrderSummary(id: string, readDb: ReadDb): Promise<OrderSummary> {
  return readDb.queryOne("SELECT id, customer_name AS customerName, total FROM order_summaries WHERE id = $1", [id]);
}
```

</details>

## Ne zaman kullanılır

* Okuma ve yazma ihtiyaçları çok farklıysa
* Okuma yükü yazmadan çok fazlaysa
* Karmaşık domain kuralları ile hızlı raporlama bir arada isteniyorsa

## Ne zaman kullanılmamalı

* Basit CRUD uygulamalarında
* Okuma ve yazma modelleri zaten benzerse

## Artılar

* Okuma ve yazma bağımsız optimize edilir ve ölçeklenir
* Modeller sade kalır
* Güvenlik ve yetkilendirme komut ve sorgu bazında yönetilir

## Eksiler

* Kod miktarı ve karmaşıklık artar
* Ayrı depo kullanılırsa nihai tutarlılık
* Ekip için öğrenme eğrisi

## Sık yapılan hatalar

* Her projeye varsayılan olarak uygulamak
* Sorgu içinde durum değiştirmek
* Okuma deposunu eşitleme sürecini (hata, gecikme) düşünmemek

## Gerçek hayattan örnekler

Greg Young ve Udi Dahan tarafından yaygınlaştırıldı. E-ticaret ve finans sistemlerinde yaygın.

## İlişkili desenler

* [Event Sourcing](event-sourcing.md): Sık birlikte kullanılır
* [Mediator Pattern](../kod-seviyesi-ve-altyapi-desenleri/mediator.md): Komut ve sorgu yönlendirmesi için
* [Vertical Slice Architecture](../uygulama-ici-mimari-stiller/vertical-slice.md): Doğal eşleşme
