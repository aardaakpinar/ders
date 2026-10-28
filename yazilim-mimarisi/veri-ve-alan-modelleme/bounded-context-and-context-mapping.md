# Bounded Context & Context Mapping

> Bounded Context, belirli bir modelin ve dilin geçerli olduğu sınırdır. Context Mapping ise bağlamlar arasındaki ilişkileri tanımlar.

## Problem

Büyük bir sistemde tek bir "Müşteri" modeli herkesi memnun etmeye çalışır ve herkes için karışık olur. Satış için müşteri, kredi limitidir. Kargo için müşteri, teslimat adresidir.

## Çözüm ve çalışma mantığı

Her bounded context kendi modelini ve kendi dilini taşır. Aynı kavram farklı bağlamlarda farklı modellenir.

{% stepper %}
{% step %}
## İş alanı konuşmaları ve olay fırtınası

İş alanı konuşmaları ve olay fırtınası (event storming) ile dil değişimlerinin olduğu yerler bulunur.
{% endstep %}

{% step %}
## Bağlam sınırını çizme

Her tutarlı dil alanı için bir bağlam sınırı çizilir.
{% endstep %}

{% step %}
## Context map oluşturma

Bağlamlar arası ilişkiler bir context map ile belgelenir.
{% endstep %}

{% step %}
## Açık sözleşmelerle iletişim

Bağlamlar arası iletişim açık sözleşmelerle (API, olay) yapılır.
{% endstep %}
{% endstepper %}

Bounded context çoğu zaman bir mikroservis veya modüler monolit modülü için en iyi sınır adayıdır.

## Yapı

```
+----- Satış -----+      +----- Kargo -----+
| Customer:        |      | Customer:        |
|  ad, kredi limiti|      |  teslimat adresi |
+------------------+      +------------------+
```

## Context Map ilişki türleri

| İlişki                | Anlamı                                                        |
| --------------------- | ------------------------------------------------------------- |
| Shared Kernel         | İki bağlam modelin bir kısmını ortak paylaşır                 |
| Customer-Supplier     | Alt akış (downstream), üst akıştan (upstream) talepte bulunur |
| Conformist            | Alt akış, üst akışın modeline uyar                            |
| Anti-Corruption Layer | Alt akış, çeviri katmanıyla kendini korur                     |
| Open Host Service     | Üst akış, herkese açık tutarlı bir API sunar                  |
| Published Language    | Ortak ve belgelenmiş mesaj formatı                            |
| Partnership           | İki ekip birlikte başarır veya başarısız olur                 |
| Separate Ways         | Entegrasyon yoktur, bağlamlar ayrı yürür                      |

## Kod örneği

Kod örnekleri varsayılan olarak gizlidir. Görmek için başlığa tıklayın.

<details>

<summary>Kodu göster: TypeScript</summary>

```ts
// sales/Customer.ts
export class Customer { constructor(readonly id: string, readonly creditLimit: number) {} }

// shipping/Recipient.ts  -> aynı kişi, farklı bağlamda farklı model
export class Recipient { constructor(readonly id: string, readonly address: string) {} }
```

</details>

## Ne zaman kullanılır

* Büyük sistemlerde modelleri ayırmak için
* Mikroservis veya modül sınırlarını belirlerken
* Birden fazla ekibin çalıştığı alanlarda

## Ne zaman kullanılmamalı

* Çok küçük, tek ekipli ve tek modelli uygulamalarda

## Artılar

* Her modelin net ve tutarlı olması
* Ekipler arası sorumluluk netliği
* Mimari sınırlar iş alanına dayanır

## Eksiler

* Sınırları doğru çizmek zor ve zaman alır
* Bağlamlar arası veri dönüşümü ek iş getirir

## Sık yapılan hatalar

* Sınırları teknik katmanlara göre çizmek
* Tüm sistemi tek bir bağlam yapmak
* Context map'i hiç güncellememek

## Gerçek hayattan örnekler

Bir e-ticaret sisteminde Satış, Envanter, Kargo ve Fatura ayrı bağlamlardır ve her birinde "Ürün" farklı anlama gelir.

## İlişkili desenler

* [DDD (Domain-Driven Design)](ddd.md): Üst yaklaşım
* [Anti-Corruption Layer](anti-corruption-layer.md): Bağlamlar arası koruma
* [Microservices](../sistem-ve-dagitim-mimarileri/microservices.md): Servis sınırlarının kaynağı
* [Modular Monolith](../sistem-ve-dagitim-mimarileri/modular-monolith.md): Modül sınırlarının kaynağı
