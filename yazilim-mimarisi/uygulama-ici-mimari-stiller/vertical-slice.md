# Vertical Slice

> Kod teknik katmanlara değil özelliğe (use case) göre dilimlenir. Her dilim isteği baştan sona kendi içinde karşılar.

## Problem

Katmanlı yapıda tek bir özelliği değiştirmek için controller, service, repository ve DTO klasörlerinde ayrı ayrı gezmek gerekir. Alakasız özellikler aynı katmanda iç içe geçer.

## Çözüm ve çalışma mantığı

Her özellik kendi klasöründe yaşar: istek modeli, doğrulama, iş mantığı, veri erişimi ve uç nokta.

{% stepper %}
{% step %}
## Yeni bir özellik için yeni bir klasör açılır
{% endstep %}

{% step %}
## Dilim, kendi ihtiyacına en uygun tekniği seçer

Basit özellik için doğrudan SQL, karmaşık için domain modeli.
{% endstep %}

{% step %}
## Dilimler birbirine doğrudan bağımlı olmaz
{% endstep %}

{% step %}
## Gerçekten ortak olan kod, ayrı bir paylaşılan modüle alınır
{% endstep %}
{% endstepper %}

Kural: aynı sebeple değişen şeyler bir arada durur.

## Yapı

```
features/
  create-order/
    handler.ts
    validator.ts
    route.ts
  get-order/
    handler.ts
    route.ts
```

## Kod örneği

<details>

<summary>Kodu göster: TypeScript</summary>

```ts
// features/create-order/handler.ts
import { z } from "zod";

const schema = z.object({ customerId: z.string(), items: z.array(z.string()).min(1) });

export async function createOrder(body: unknown, db: Db) {
  const input = schema.parse(body);
  const id = crypto.randomUUID();
  await db.insert("orders", { id, ...input });
  return { id };
}
```

</details>

## Ne zaman kullanılır

* Özellik odaklı API'ler
* CQRS ve Mediator ile çalışan projeler
* Ekiplerin farklı özelliklerde paralel çalıştığı durumlar

## Ne zaman kullanılmamalı

* Ekip dilimler arasında ortak kodu disiplinli yönetemiyorsa

## Artılar

* Değişiklik tek klasörde kalır
* Dilimler arası bağlılık düşüktür
* Yeni özellik eklemek hızlıdır

## Eksiler

* Dilimler arasında kod tekrarı olabilir
* Disiplin gerekir, yoksa tutarsız yapılar çıkar

## Sık yapılan hatalar

* Dilimler arasında doğrudan çağrı yapmak
* Her şeyi "ortak" klasörüne taşımak

## Gerçek hayattan örnekler

Jimmy Bogard tarafından yaygınlaştırıldı. Feature klasörleri kullanan Next.js, NestJS ve Express projelerinde karşılaşılır.

## İlişkili desenler

* [CQRS](../veri-ve-alan-modelleme/cqrs.md): Doğal eşleşme
* [Mediator Pattern](../kod-seviyesi-ve-altyapi-desenleri/mediator.md): Dilimleri çağırma aracı
* [Layered (N-Tier) Architecture](layered.md): Alternatif olarak katmanlı yapı
