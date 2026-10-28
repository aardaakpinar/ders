# BFF

> Her istemci türü (web, mobil, TV) için ayrı ve o istemciye özel bir backend katmanıdır.

## Problem

Tek bir genel API, web, mobil ve diğer istemcilerin farklı ihtiyaçlarını aynı anda karşılamaya çalıştığında herkes için gereğinden fazla veya eksik veri döner.

## Çözüm ve çalışma mantığı

Her istemci kendi BFF'ine konuşur. BFF, ilgili istemciye özel veriyi mikroservislerden toplar, biçimlendirir ve döner.

{% stepper %}
{% step %}
## Mobil uygulama mobil BFF'e tek bir istek gönderir
{% endstep %}

{% step %}
## BFF birkaç servisi paralel çağırır
{% endstep %}

{% step %}
## Yanıtları birleştirir ve mobil için küçültülmüş tek yanıt döner
{% endstep %}
{% endstepper %}

BFF çoğunlukla **istemciyi geliştiren ekip** tarafından yönetilir, böylece istemci ihtiyaçları hızla karşılanır.

## Yapı

```
Web App   --> Web BFF   --+
Mobil App --> Mobil BFF --+--> Mikroservisler
Akıllı TV --> TV BFF    --+
```

## Kod örneği

<details>

<summary>Kodu göster: JavaScript</summary>

```js
// Mobil için tek çağrıda, küçültülmüş veri döner
app.get("/mobile/home", async (req, res) => {
  const [user, orders] = await Promise.all([
    fetch(`${USER_SVC}/users/${req.userId}`).then((r) => r.json()),
    fetch(`${ORDER_SVC}/orders?user=${req.userId}&limit=3`).then((r) => r.json()),
  ]);

  res.json({
    name: user.name,
    lastOrders: orders.map((o) => ({ id: o.id, total: o.total })), // yalnızca gerekli alanlar
  });
});
```

</details>

## Ne zaman kullanılır

* Web ve mobil gibi çok farklı ihtiyaçlı istemciler
* İstemci ekiplerinin kendi backend'ini yönetebildiği organizasyonlar

## Ne zaman kullanılmamalı

* Tek istemcili basit sistemlerde
* İstemciler aynı veriyi kullanıyorsa

## Artılar

* Her istemciye en uygun veri biçimi ve yük
* İstemci ekibi hızlı ilerler
* Genel API'yi istemci özelliklerinden arındırır

## Eksiler

* Kod tekrarı (BFF'ler arasında)
* Servis sayısı artar
* Ek operasyon yükü

## Sık yapılan hatalar

* BFF'e iş mantığı koymak
* Tüm istemciler için tek BFF yazmak (gateway'e dönüşür)

## Gerçek hayattan örnekler

Netflix, SoundCloud ve Spotify benzeri platformlarda web ve mobil için ayrı backend katmanları kullanılır.

## İlişkili desenler

* [API Gateway](api-gateway.md): API Gateway'den farkı: gateway ortaktır, BFF istemciye özeldir
* [Microservices](../sistem-ve-dagitim-mimarileri/microservices.md): Mikroservis mimarisinin parçası
