# Event Sourcing

> Durumun son hali yerine, onu oluşturan olayların tamamı saklanır. Güncel durum olayların yeniden oynatılmasıyla bulunur.

## Problem

Geleneksel veritabanları yalnızca son durumu tutar. "Bu bakiye nasıl oluştu?", "Geçen ay bu sipariş nasıldı?" gibi sorulara cevap vermek için ayrı denetim kaydı yazmak gerekir.

## Çözüm ve çalışma mantığı

Her durum değişikliği **değiştirilemez bir olay** olarak olay deposuna eklenir.

{% stepper %}
{% step %}
## Komut gelir

Mevcut olaylar yüklenir ve durum yeniden oluşturulur.
{% endstep %}

{% step %}
## İş kuralları kontrol edilir
{% endstep %}

{% step %}
## Yeni olaylar eklenir

Yeni olay(lar) olay deposuna eklenir (yalnızca ekleme, güncelleme veya silme yoktur).
{% endstep %}

{% step %}
## Olaylar yayınlanır

Olaylar yayınlanır ve projeksiyonlar (okuma modelleri) güncellenir.
{% endstep %}
{% endstepper %}

Uzun olay akışlarını her seferinde baştan oynatmamak için belirli aralıklarla **snapshot** alınır.

## Yapı

```
OrderCreated -> ItemAdded -> ItemAdded -> OrderPaid
                      | (yeniden oynatma)
                Siparişin güncel durumu
```

## Kod örneği

{% code title="TypeScript" expandable="true" collapsedlinecount="1" %}
```ts
type Event =
  | { type: "AccountOpened"; id: string }
  | { type: "Deposited"; amount: number }
  | { type: "Withdrawn"; amount: number };

class Account {
  balance = 0;
  static replay(events: Event[]): Account {
    const acc = new Account();
    events.forEach((e) => acc.apply(e));
    return acc;
  }
  private apply(e: Event) {
    if (e.type === "Deposited") this.balance += e.amount;
    if (e.type === "Withdrawn") this.balance -= e.amount;
  }
}

const events: Event[] = [
  { type: "AccountOpened", id: "a1" },
  { type: "Deposited", amount: 500 },
  { type: "Withdrawn", amount: 120 },
];
Account.replay(events).balance; // 380
```
{% endcode %}

## Ne zaman kullanılır

* Denetim izi gereken sistemler (finans, sağlık, hukuk)
* Geçmişe dönük analiz ve zaman yolculuğu gereken uygulamalar
* Domain olayları zaten önemli olan sistemler

## Ne zaman kullanılmamalı

* Basit CRUD uygulamalarında
* Verinin sık silinmesi veya düzeltilmesi gerekiyorsa (KVKK, GDPR zorlukları)

## Artılar

* Tam denetim izi
* Geçmişi yeniden oynatma ve hata ayıklama
* Yeni okuma modelleri geçmiş olaylardan üretilebilir

## Eksiler

* Öğrenme eğrisi yüksektir
* Olay şeması sürümleme zordur
* Sorgulama için projeksiyon gerekir
* Kişisel veri silme zorlaşır

## Sık yapılan hatalar

* Her projede varsayılan olarak kullanmak
* Olayları, değişen bir veri tablosu gibi tasarlamak
* Olay sürümlemeyi baştan düşünmemek

## Gerçek hayattan örnekler

EventStoreDB, Axon Framework, Marten. Banka hesap hareketleri benzer bir mantıkla çalışır.

## İlişkili desenler

* [CQRS](cqrs.md): Sıklıkla birlikte kullanılır
* [EDA (Event-Driven Architecture)](../sistem-ve-dagitim-mimarileri/eda.md): Olay tabanlı mimari ile örtüşür
* [Aggregate, Entity, Value Object, Domain Event](aggregate-entity-value-object-domain-event.md): Aggregate olay üretir
