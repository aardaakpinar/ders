# Specification

> Sorgu ve iş kuralı koşullarını yeniden kullanılabilir, birleştirilebilir nesnelere paketler.

## Problem

Aynı koşul ("aktif müşteri", "İstanbul'daki müşteri") birçok yerde tekrar tekrar yazılır. Kural değiştiğinde her yer ayrı ayrı güncellenir.

## Çözüm ve çalışma mantığı

Her koşul bir **specification** nesnesidir. Nesne, bir öğenin koşulu sağlayıp sağlamadığını söyler (`isSatisfiedBy`) veya bir sorgu koşuluna çevrilir.

{% stepper %}
{% step %}
## Basit koşulları ayrı specification olarak yazın
{% endstep %}

{% step %}
## Karmaşık koşulları birleştirin

`and`, `or`, `not` ile birleştirilerek karmaşık koşullar üretilir.
{% endstep %}

{% step %}
## Aynı specification'ı farklı yerlerde kullanın

Aynı specification hem bellek içi doğrulamada hem repository sorgusunda kullanılır.
{% endstep %}
{% endstepper %}

Böylece iş kuralları tek yerde ve adlandırılmış biçimde durur.

## Kod örneği

Kod örnekleri varsayılan olarak gizlidir. Görmek için başlığa tıklayın.

<details>

<summary>Kodu göster: TypeScript</summary>

```ts
interface Specification<T> {
  isSatisfiedBy(item: T): boolean;
}

class And<T> implements Specification<T> {
  constructor(private a: Specification<T>, private b: Specification<T>) {}
  isSatisfiedBy(item: T) { return this.a.isSatisfiedBy(item) && this.b.isSatisfiedBy(item); }
}

class ActiveCustomer implements Specification<Customer> {
  isSatisfiedBy(c: Customer) { return c.active; }
}
class InCity implements Specification<Customer> {
  constructor(private city: string) {}
  isSatisfiedBy(c: Customer) { return c.city === this.city; }
}

const spec = new And(new ActiveCustomer(), new InCity("İstanbul"));
const result = customers.filter((c) => spec.isSatisfiedBy(c));
```

</details>

## Ne zaman kullanılır

* Aynı koşul birçok yerde tekrarlanıyorsa
* Sorgu koşullarının dinamik birleştirilmesi gerekiyorsa

## Ne zaman kullanılmamalı

* Basit ve az sayıda sorgu olan projelerde

## Artılar

* Tekrar azalır, iş kuralı tek yerde
* Birleştirilebilir ve ayrı test edilebilir
* Kod, iş diline daha yakın olur

## Eksiler

* Basit sorgular için aşırı mühendislik
* Veritabanı sorgusuna çevirmek ek çaba gerektirir

## Sık yapılan hatalar

* Specification içinde veritabanına özel kod yazmak
* Çok küçük koşullar için bile specification oluşturmak

## Gerçek hayattan örnekler

Eric Evans ve Martin Fowler tarafından tanımlandı. Dinamik filtreleme gerektiren listeleme ekranlarında yaygındır.

## İlişkili desenler

* [Repository Pattern](repository.md): Repository ile birlikte kullanılır
* [DDD (Domain-Driven Design)](../veri-ve-alan-modelleme/ddd.md): Domain kurallarını ifade etmek için
