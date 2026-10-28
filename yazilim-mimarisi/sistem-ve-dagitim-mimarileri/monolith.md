# Monolith

> Tüm uygulama tek bir birim olarak derlenir ve dağıtılır.

## Problem

Yeni bir ürünün ilk aşamasında en büyük risk, ürün fikrinin doğru olup olmadığıdır. Erken aşamada dağıtık sistem kurmak, hızı düşürür ve gereksiz operasyon yükü getirir.

## Çözüm ve çalışma mantığı

Arayüz, iş mantığı ve veri erişimi aynı kod tabanında ve aynı süreçte çalışır. Modüller birbirini doğrudan fonksiyon çağrısıyla kullanır. Tek bir veritabanı vardır.

{% stepper %}
{% step %}
## Tek kod tabanı geliştirilir
{% endstep %}

{% step %}
## Tek derleme çıktısı üretilir
{% endstep %}

{% step %}
## Tek birim olarak sunucuya dağıtılır
{% endstep %}

{% step %}
## Ölçekleme, birimin birden fazla kopyasını çalıştırarak yapılır
{% endstep %}
{% endstepper %}

Monolit "kötü mimari" demek değildir. İyi organize edilmiş bir monolit, çoğu proje için en doğru başlangıçtır.

## Yapı

```
+-----------------------------+
|  UI + İş Mantığı + Veri     |   tek süreç, tek dağıtım
+-----------------------------+
```

## Kod örneği

Kod örnekleri varsayılan olarak gizlidir. Görmek için başlığa tıklayın.

<details>

<summary>Kodu göster: JavaScript</summary>

```js
import express from "express";
const app = express();

app.use("/orders", ordersRouter);     // aynı kod tabanı
app.use("/products", productsRouter); // aynı süreç
app.use("/users", usersRouter);       // aynı veritabanı

app.listen(3000);
```

</details>

## Ne zaman kullanılır

* Yeni projeler ve küçük ekipler
* Ürün alanı henüz netleşmemişse
* Operasyon kapasitesi sınırlıysa

## Ne zaman kullanılmamalı

* Farklı parçaların çok farklı ölçekleme ve dağıtım ihtiyacı varsa
* Çok sayıda ekip aynı kod tabanında sürekli çakışıyorsa

## Artılar

* Geliştirme, test, dağıtım ve hata ayıklama basit
* Düşük operasyon yükü
* Fonksiyon çağrıları hızlı, ağ hatası yok
* Tek transaction ile tutarlılık kolay

## Eksiler

* Büyüdükçe derleme ve dağıtım yavaşlar
* Tüm uygulama birlikte ölçeklenir
* Teknoloji seçimi tek tip kalır
* Dikkat edilmezse kod karışır (spagetti)

## Sık yapılan hatalar

* Modül sınırları olmadan büyütmek
* Her şeyin her şeye doğrudan bağımlı olması

## Gerçek hayattan örnekler

Birçok başarılı ürün (örneğin ilk dönem e-ticaret ve SaaS ürünleri) monolit olarak başlar.

## İlişkili desenler

* [Modular Monolith](modular-monolith.md): Monolitin düzenli sürümü
* [Microservices](microservices.md): Büyüme sonrası olası hedef
* [Strangler Fig](../mikroservis-ve-dagitik-sistem-desenleri/strangler-fig.md): Monolitten kademeli geçiş yöntemi
