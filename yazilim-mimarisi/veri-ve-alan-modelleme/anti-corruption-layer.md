# Anti-Corruption Layer

> Eski veya dış sistemin modelinin kendi domain'inizi bozmaması için araya konan çeviri katmanıdır.

## Problem

Yeni sistem, eski bir sistemle veya üçüncü taraf bir API ile konuşmak zorundadır. Eski sistemin garip alan adları ve mantığı yeni kodun içine sızarsa, yeni sistem de aynı dağınıklığı miras alır.

## Çözüm ve çalışma mantığı

Araya bir katman konur. Bu katman:

* Dış sistemi çağırır (**adapter**).
* Dış modeli kendi domain modelinize çevirir (**translator**).
* Yeni domain'e yalnızca temiz, kendi dilinizde veri sunar (**facade**).

{% stepper %}
{% step %}
## Domain bir arayüz üzerinden veri ister
{% endstep %}

{% step %}
## ACL, eski sistemi çağırır
{% endstep %}

{% step %}
## Gelen veriyi kendi modele çevirir
{% endstep %}

{% step %}
## Domain yalnızca kendi modelini görür

Eski sistem değişirse yalnızca ACL güncellenir.
{% endstep %}
{% endstepper %}

## Yapı

```
Yeni Domain <--> [ ACL: Adapter + Translator ] <--> Eski / 3. Parti Sistem
```

## Kod örneği

Kod örnekleri varsayılan olarak gizlidir. Görmek için başlığa tıklayın.

<details>

<summary>Kodu göster: TypeScript</summary>

```ts
// Eski sistemin biçimi
type LegacyCustomer = { CUST_NO: string; FNAME: string; LNAME: string; STAT: "A" | "P" };

// Kendi domain modelimiz
class Customer {
  constructor(readonly id: string, readonly fullName: string, readonly active: boolean) {}
}

// ACL: çeviriyi tek bir yerde yapar
class LegacyCustomerTranslator {
  toDomain(l: LegacyCustomer): Customer {
    return new Customer(l.CUST_NO, `${l.FNAME} ${l.LNAME}`, l.STAT === "A");
  }
}
```

</details>

## Ne zaman kullanılır

* Legacy sistem entegrasyonlarında
* Üçüncü taraf API'lerle çalışırken
* Strangler Fig ile kademeli geçiş yaparken

## Ne zaman kullanılmamalı

* Dış sistemin modeli zaten sizinkiyle uyumluysa (Conformist ilişkisi tercih edilebilir)

## Artılar

* Domain temiz kalır
* Dış değişikliklerin etkisi tek katmanla sınırlıdır
* Eski sistem yavaş yavaş devreden çıkarılabilir

## Eksiler

* Ek kod ve bakım yükü
* Çeviri hataları gizli bug üretebilir
* Performans için ek dönüşüm maliyeti

## Sık yapılan hatalar

* Çeviri katmanını atlayıp dış modeli doğrudan kullanmak
* ACL'ye iş mantığı koymak

## Gerçek hayattan örnekler

Eski bir ERP'den müşteri verisi alan yeni sipariş servisinin araya koyduğu çeviri katmanı.

## İlişkili desenler

* [Bounded Context & Context Mapping](bounded-context-and-context-mapping.md): Context map ilişki türlerinden biri
* [Strangler Fig](../mikroservis-ve-dagitik-sistem-desenleri/strangler-fig.md): Kademeli geçişte sık kullanılır
* [Hexagonal Architecture (Ports and Adapters)](../uygulama-ici-mimari-stiller/hexagonal.md): Adapter fikrinin domain düzeyi
