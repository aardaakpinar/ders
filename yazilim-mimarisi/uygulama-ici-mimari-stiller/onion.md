# Onion

> Domain en ortada bulunur, bağımlılıklar her zaman içe doğru akar. Veritabanı ve arayüz dış halkadadır.

## Problem

Katmanlı mimaride iş mantığı veri katmanına bağımlıdır. Veritabanı veya çerçeve değiştiğinde iş kuralları da etkilenir ve test için gerçek veritabanı gerekir.

## Çözüm ve çalışma mantığı

Halkalar içten dışa: **Domain Model**, **Domain Services**, **Application Services**, **Infrastructure / UI / Tests**.

{% stepper %}
{% step %}
## İç halkalar dış halkaları bilmez
{% endstep %}

{% step %}
## İç halka, ihtiyaç duyduğu dış işlevi bir arayüz olarak tanımlar

Örneğin `InvoiceRepository`.
{% endstep %}

{% step %}
## Dış halka bu arayüzü uygular

Örneğin PostgreSQL ile.
{% endstep %}

{% step %}
## Çalışma anında bağımlılık enjeksiyonu ile ikisi birleştirilir
{% endstep %}
{% endstepper %}

Bağımlılık yönü bu sayede tersine çevrilir: veritabanı domain'e bağımlıdır, domain veritabanına değil.

## Yapı

```
+-----------------------------------+
| UI / Infrastructure / Tests       |
|  +-----------------------------+  |
|  | Application Services        |  |
|  |  +-----------------------+  |  |
|  |  | Domain Model          |  |  |
|  |  +-----------------------+  |  |
|  +-----------------------------+  |
+-----------------------------------+
   Bağımlılıklar merkeze doğru akar
```

## Kod örneği

<details>

<summary>Kodu göster: TypeScript</summary>

```ts
// domain (en iç halka): hiçbir şeye bağımlı değil
export class Invoice {
  constructor(readonly id: string, readonly total: number, private paid = false) {}
  pay() { if (this.paid) throw new Error("Zaten ödendi"); this.paid = true; }
}
export interface InvoiceRepository { get(id: string): Promise<Invoice>; save(i: Invoice): Promise<void>; }

// application
export class PayInvoice {
  constructor(private repo: InvoiceRepository) {}
  async execute(id: string) { const i = await this.repo.get(id); i.pay(); await this.repo.save(i); }
}

// infrastructure (dış halka): arayüzü uygular
export class PgInvoiceRepository implements InvoiceRepository { /* ... */ }
```

</details>

## Ne zaman kullanılır

* İş kuralları önemli ve uzun ömürlü uygulamalar
* Altyapı teknolojisinin değişme ihtimali olduğunda

## Ne zaman kullanılmamalı

* Basit CRUD uygulamalarında

## Artılar

* Domain altyapıdan bağımsız
* İş kuralları gerçek veritabanı olmadan test edilir
* Teknoloji değişimi kolaylaşır

## Eksiler

* Başlangıçta daha fazla soyutlama
* Küçük projeler için ağır kalır

## Sık yapılan hatalar

* Domain'e ORM veya çerçeve sınıflarını sokmak
* İç halkada dış halkaya ait türleri kullanmak

## Gerçek hayattan örnekler

Jeffrey Palermo'nun tanımladığı desen. .NET dünyasında yaygın, diğer dillerde de kullanılır.

## İlişkili desenler

* [Hexagonal Architecture (Ports and Adapters)](hexagonal.md): Aynı fikrin port ve adapter ile ifadesi
* [Clean Architecture](clean.md): İki fikrin birleşimi
* [Dependency Injection (DI) / IoC](../kod-seviyesi-ve-altyapi-desenleri/dependency-injection.md): Bağımlılık tersine çevirmenin aracı
