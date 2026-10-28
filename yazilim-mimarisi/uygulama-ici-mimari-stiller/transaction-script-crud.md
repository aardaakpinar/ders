# Transaction Script / CRUD

> Her iş senaryosu tek bir prosedür olarak yazılır. Nesne modeli yoktur veya çok incedir.

## Problem

Basit bir iş için zengin bir domain modeli, katmanlar ve soyutlamalar kurmak, kazandırdığından fazla maliyet getirir.

## Çözüm ve çalışma mantığı

Her senaryo (örneğin "para transferi") bir fonksiyondur. Fonksiyon girdiyi alır, veritabanı işlemlerini doğrudan yapar ve sonucu döner. Transaction yönetimi genellikle aynı fonksiyonun içindedir.

{% stepper %}
{% step %}
## Giriş doğrulanır
{% endstep %}

{% step %}
## Transaction başlatılır
{% endstep %}

{% step %}
## Gerekli sorgular sırayla çalıştırılır
{% endstep %}

{% step %}
## Transaction onaylanır veya geri alınır
{% endstep %}

{% step %}
## Sonuç döner
{% endstep %}
{% endstepper %}

CRUD, bu yaklaşımın en yalın halidir: oluştur, oku, güncelle, sil.

## Kod örneği

Kod örnekleri varsayılan olarak gizlidir. Görmek için başlığa tıklayın.

<details>

<summary>Kodu göster: TypeScript</summary>

```ts
async function transfer(pool: Pool, fromId: number, toId: number, amount: number) {
  const client = await pool.connect();
  try {
    await client.query("BEGIN");
    await client.query("UPDATE accounts SET balance = balance - $1 WHERE id = $2", [amount, fromId]);
    await client.query("UPDATE accounts SET balance = balance + $1 WHERE id = $2", [amount, toId]);
    await client.query("COMMIT");
  } catch (e) {
    await client.query("ROLLBACK");
    throw e;
  } finally {
    client.release();
  }
}
```

</details>

## Ne zaman kullanılır

* İş mantığı basit uygulamalar
* Yönetim panelleri, iç araçlar, hızlı prototipler

## Ne zaman kullanılmamalı

* İş kuralları karmaşıklaşıp aynı mantık birçok yerde tekrarlanıyorsa
* Kurallar sık değişiyorsa

## Artılar

* En basit ve hızlı yöntem
* Ek soyutlama yok, okuması kolay
* Küçük ekipler için verimli

## Eksiler

* Kurallar büyüdükçe tekrar ve dağınıklık artar
* Domain bilgisi kodda görünmez
* Test için veritabanı gerekebilir

## Sık yapılan hatalar

* Karmaşıklık artmasına rağmen yaklaşımı değiştirmemek
* Aynı iş kuralını birçok script'e kopyalamak

## Gerçek hayattan örnekler

Birçok yönetim paneli, Rails ve Django tabanlı klasik CRUD uygulamaları.

## İlişkili desenler

* [DDD (Domain-Driven Design)](../veri-ve-alan-modelleme/ddd.md): Karmaşıklık büyüyünce geçilecek yaklaşım
* [Active Record](../veri-erisimi-desenleri/active-record.md): CRUD için pratik veri erişimi
* [Layered (N-Tier) Architecture](layered.md): Genelde katmanlı yapı ile kullanılır
