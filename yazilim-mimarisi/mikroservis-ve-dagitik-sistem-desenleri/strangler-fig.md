# Strangler Fig

> Eski sistemi bir anda yıkmak yerine, yeni sistemi etrafında büyütüp özellikleri parça parça yeni sisteme yönlendirmek.

## Problem

Büyük bir eski sistemi baştan yazmak (big bang rewrite) uzun sürer, riskli olur ve çoğu zaman başarısız biter. Yazım sürerken eski sistem de değişmeye devam eder.

## Çözüm ve çalışma mantığı

Adı, ağaçları sarıp zamanla yerini alan incir türünden gelir.

{% stepper %}
{% step %}
## Eski sistemin önüne bir proxy / facade koyun
{% endstep %}

{% step %}
## Taşınacak bir özellik seçin ve yeni sistemde yeniden yazın
{% endstep %}

{% step %}
## Proxy ile bu özelliğin trafiğini yeni sisteme yönlendirin
{% endstep %}

{% step %}
## Süreci özellik özellik tekrarlayın
{% endstep %}

{% step %}
## Eski sistemde hiçbir şey kalmayınca kapatın
{% endstep %}
{% endstepper %}

Her adımda sorun çıkarsa trafik tekrar eski sisteme çevrilebilir. Veri ve model farkları için sıklıkla bir ACL gerekir.

## Yapı

```
 İstemci --> [ Proxy / Facade ]
                  +--> Yeni Servisler  (taşınan özellikler)
                  +--> Eski Sistem     (henüz taşınmayanlar)
```

## Kod örneği

<details>

<summary>Kodu göster: JavaScript</summary>

```js
import express from "express";
import { createProxyMiddleware } from "http-proxy-middleware";

const app = express();

// Taşınmış özellikler yeni sisteme gider
app.use("/invoices", createProxyMiddleware({ target: "http://new-billing:4000", changeOrigin: true }));

// Geri kalan her şey hâlâ eski sisteme gider
app.use("/", createProxyMiddleware({ target: "http://legacy-app:8000", changeOrigin: true }));

app.listen(80);
```

</details>

## Ne zaman kullanılır

* Legacy sistemlerin modernizasyonu
* Monolitten mikroservise geçiş
* Riskin düşük tutulması gereken geçişler

## Ne zaman kullanılmamalı

* Eski sistem çok küçükse (doğrudan yeniden yazmak daha hızlı olabilir)
* Önüne proxy konulamayan sistemlerde

## Artılar

* Düşük riskli, kademeli geçiş
* Her adımda geri dönüş mümkün
* Kullanıcıya erken değer sunulur

## Eksiler

* Uzun süre iki sistem birlikte yaşar
* Veri senkronizasyonu zordur
* Proxy ek karmaşıklık ve gecikme getirir

## Sık yapılan hatalar

* Geçişi bitirmeden yarıda bırakmak (iki sistem sonsuza kadar kalır)
* Veri sahipliğini net belirlememek

## Gerçek hayattan örnekler

Martin Fowler tarafından adlandırıldı. Amazon, Netflix gibi şirketlerin monolitten geçiş hikâyelerinde kullanıldı.

## İlişkili desenler

* [Anti-Corruption Layer](../veri-ve-alan-modelleme/anti-corruption-layer.md): Eski model ile yeni model arasındaki çeviri
* [Monolithic Architecture](../sistem-ve-dagitim-mimarileri/monolith.md): Taşınacak tipik kaynak
* [Microservices](../sistem-ve-dagitim-mimarileri/microservices.md): Tipik hedef
* [API Gateway](api-gateway.md): Proxy olarak gateway kullanılabilir
