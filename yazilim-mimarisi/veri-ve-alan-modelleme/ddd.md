# DDD

> Yazılımın merkezine iş alanı (domain) konur. Kod, iş uzmanlarının kullandığı dili yansıtır.

## Problem

Yazılımcılar ile iş birimi farklı kelimeler kullandığında yanlış anlamalar oluşur. Karmaşık iş kuralları kodun içinde dağılır ve kimse tamamını bilmez.

## Çözüm ve çalışma mantığı

DDD iki düzeyde çalışır:

* **Stratejik tasarım:** Büyük resmi çizer: alt alanlar, bounded context'ler ve aralarındaki ilişkiler.
* **Taktik tasarım:** Bir bounded context'in içini modeller: entity, value object, aggregate, repository, domain event.

Başlangıç noktası **ortak dildir (Ubiquitous Language)**: iş birimi "siparişi onaylamak" diyorsa kodda da `order.confirm()` olur.

## Alt alan türleri

| Tür        | Anlamı                                                  | Yaklaşım                     |
| ---------- | ------------------------------------------------------- | ---------------------------- |
| Core       | Rekabet avantajı sağlayan alan                          | En iyi ekip, özel geliştirme |
| Supporting | Gerekli ama farklılaştırıcı olmayan alan                | Basit çözümler               |
| Generic    | Herkesin ihtiyaç duyduğu alan (kimlik doğrulama, ödeme) | Hazır ürün veya hizmet       |

## Taktik yapı taşları

| Yapı taşı      | Kısa açıklama                              |
| -------------- | ------------------------------------------ |
| Entity         | Kimliği olan nesne                         |
| Value Object   | Değeriyle tanımlanan, değişmez nesne       |
| Aggregate      | Birlikte tutarlı olan nesne kümesi         |
| Repository     | Aggregate'leri saklama ve yükleme          |
| Domain Event   | Olmuş önemli bir olay                      |
| Domain Service | Tek bir nesneye ait olmayan domain mantığı |

## Kod örneği

Kod örnekleri varsayılan olarak gizlidir. Görmek için başlığa tıklayın.

<details>

<summary>Kodu göster: TypeScript</summary>

```ts
// "Bir sipariş onaylanır, ardından sevk edilir" ifadesi birebir koddadır.
class Order {
  private status: "Draft" | "Confirmed" | "Shipped" = "Draft";

  confirm() {
    if (this.status !== "Draft") throw new Error("Yalnızca taslak sipariş onaylanabilir");
    this.status = "Confirmed";
  }
  ship() {
    if (this.status !== "Confirmed") throw new Error("Onaylanmamış sipariş sevk edilemez");
    this.status = "Shipped";
  }
}
```

</details>

## Ne zaman kullanılır

* İş kuralları karmaşık ve sık değişen sistemler
* İş uzmanlarıyla yakın çalışılabilen projeler

## Ne zaman kullanılmamalı

* Basit CRUD uygulamaları
* İş uzmanına erişimin olmadığı projeler

## Artılar

* Karmaşık iş kurallarını yönetilebilir kılar
* İş ve yazılım aynı dili konuşur
* Doğru sınırlar sayesinde mimari (ve mikroservis) kararları kolaylaşır

## Eksiler

* Yüksek öğrenme ve zaman yatırımı
* Basit problemlerde fazla yük
* İş birimiyle sürekli iş birliği gerekir

## Sık yapılan hatalar

* Yalnızca taktik desenleri kullanıp stratejik tasarımı atlamak
* Anemic domain model: nesneler sadece veri taşır, kurallar servislerdedir
* Ortak dili geliştirici tarafında tek başına belirlemek

## Gerçek hayattan örnekler

Eric Evans'ın 2003 tarihli kitabıyla yaygınlaştı. Bankacılık, sigorta ve lojistik gibi karmaşık alanlarda yaygındır.

## İlişkili desenler

* [Bounded Context ve Context Mapping](bounded-context-and-context-mapping.md): Stratejik tasarımın temel parçası
* [Aggregate, Entity, Value Object, Domain Event](aggregate-entity-value-object-domain-event.md): Taktik yapı taşları
* [Clean Architecture](../uygulama-ici-mimari-stiller/clean.md): DDD'yi barındıran mimari
