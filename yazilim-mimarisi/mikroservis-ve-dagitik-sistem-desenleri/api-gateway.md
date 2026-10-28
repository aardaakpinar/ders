# Api Gateway

> İstemciler için tek giriş noktasıdır. İstekleri uygun servislere yönlendirir ve ortak işleri üstlenir.

## Problem

Mikroservis mimarisinde istemci onlarca servisin adresini, kimlik doğrulama yöntemini ve yanıt biçimini bilmek zorunda kalır. Ortak işler (yetkilendirme, hız sınırlama) her serviste tekrar yazılır.

## Çözüm ve çalışma mantığı

Gateway, istemci ile servisler arasına konur.

{% stepper %}
{% step %}
## İstekleri gateway'e gönderme

İstemci tüm istekleri gateway'e gönderir.
{% endstep %}

{% step %}
## Ortak işleri gerçekleştirme

Gateway kimlik doğrulama, hız sınırlama ve loglama gibi ortak işleri yapar.
{% endstep %}

{% step %}
## İsteği yönlendirme

İsteği ilgili servise yönlendirir.
{% endstep %}

{% step %}
## Yanıtları birleştirme

Gerekirse birden fazla servisin yanıtını birleştirip tek yanıt döner.
{% endstep %}
{% endstepper %}

Tipik görevleri: yönlendirme, kimlik doğrulama, hız sınırlama (rate limiting), önbellekleme, yanıt birleştirme, SSL sonlandırma, loglama.

## Yapı

```
Mobil   --+                        +--> Kullanıcı Servisi
Web     --+--> [ API Gateway ] ----+--> Sipariş Servisi
3. Parti --+    Auth, Rate limit   +--> Ürün Servisi
                Routing, Cache
```

## Kod örneği

Kod örnekleri varsayılan olarak gizlidir. Görmek için başlığa tıklayın.

<details>

<summary>Kodu göster: JavaScript</summary>

```js
import express from "express";
import { createProxyMiddleware } from "http-proxy-middleware";
import rateLimit from "express-rate-limit";

const app = express();
app.use(rateLimit({ windowMs: 60_000, max: 100 }));
app.use(verifyJwt); // kimlik doğrulama tek yerde

app.use("/users",  createProxyMiddleware({ target: "http://user-service:3001", changeOrigin: true }));
app.use("/orders", createProxyMiddleware({ target: "http://order-service:3002", changeOrigin: true }));

app.listen(8080);
```

</details>

## Ne zaman kullanılır

* Mikroservis mimarilerinde
* Birden fazla istemcinin aynı servislere eriştiği sistemlerde

## Ne zaman kullanılmamalı

* Tek monolit uygulamada
* Gateway'in içine iş mantığı koymak istenirse (yanlış kullanım)

## Artılar

* İstemci karmaşıklığı azalır
* Ortak kaygılar merkezileşir
* Servis adresleri ve yapısı istemciden gizlenir

## Eksiler

* Tek hata noktası ve darboğaz olabilir
* Ek ağ atlaması (gecikme)
* İçine iş mantığı konulursa mini monolit olur

## Sık yapılan hatalar

* Gateway'e iş mantığı yazmak
* Gateway'i tek kopya ile çalıştırmak
* Tüm istemciler için tek ve genel API'ye zorlamak (BFF düşünülmeli)

## Gerçek hayattan örnekler

Kong, NGINX, YARP, AWS API Gateway, Azure API Management, Envoy.

## İlişkili desenler

* [BFF (Backend for Frontend)](bff.md): İstemciye özel evrimi
* [Microservices](../sistem-ve-dagitim-mimarileri/microservices.md): Kullanıldığı mimari
* [Circuit Breaker](circuit-breaker.md): Dayanıklılık için birlikte kullanılır
