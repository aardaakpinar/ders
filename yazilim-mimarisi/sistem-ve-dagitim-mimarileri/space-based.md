# Space Based

> Veritabanı darboğazını kaldırmak için durum bellek içi bir veri ızgarasında tutulur ve işlem birimleri çoğaltılarak ölçeklenir.

## Problem

Çok yüksek ve ani trafik (bilet satışı, açık artırma) altında veritabanı ilk tıkanan bileşendir. Veritabanını büyütmek pahalı ve sınırlıdır.

## Çözüm ve çalışma mantığı

* **İşlem birimi (processing unit):** Uygulama kodu ve bellek içi veriyi birlikte taşır.
* **Sanal ara yazılım (virtualized middleware):** Mesajlaşma, veri çoğaltma ve işlem dağıtımını yönetir.
* **Veri yazıcı:** Bellekteki değişiklikleri veritabanına asenkron yazar.

{% stepper %}
{% step %}
## İsteği yönlendirin

İstek yük dengeleyici ile bir işlem birimine gider.
{% endstep %}

{% step %}
## Veriyi bellekten güncelleyin

İşlem birimi veriyi bellekten okur ve günceller.
{% endstep %}

{% step %}
## Değişiklikleri çoğaltın

Değişiklik diğer birimlere çoğaltılır.
{% endstep %}

{% step %}
## Veritabanına asenkron yazın

Veritabanına yazma arka planda, asenkron yapılır.
{% endstep %}
{% endstepper %}

Yük arttıkça işlem birimi sayısı artırılır.

## Yapı

```
Client --> Load Balancer --> [İşlem Birimi + Bellek Izgarası] x N
                                       |
                              asenkron yazma
                                       v
                                  Veritabanı
```

## Kod örneği

<details>

<summary>Kodu göster: TypeScript</summary>

```ts
class ProcessingUnit {
  private grid = new Map<string, number>();   // bellek içi veri
  private writeQueue: Array<[string, number]> = [];

  constructor(private db: { save(k: string, v: number): Promise<void> }) {
    setInterval(() => this.flush(), 1000);    // veritabanına toplu, asenkron yazım
  }

  decrementTicket(eventId: string): boolean {
    const left = this.grid.get(eventId) ?? 0;
    if (left <= 0) return false;
    this.grid.set(eventId, left - 1);
    this.writeQueue.push([eventId, left - 1]);
    return true;
  }

  private async flush() {
    const batch = this.writeQueue.splice(0);
    for (const [k, v] of batch) await this.db.save(k, v);
  }
}
```

</details>

## Ne zaman kullanılır

* Çok yüksek ve öngörülemeyen yük (bilet satışı, oyun, finans)
* Okuma ve yazma gecikmesinin çok düşük olması gereken sistemler

## Ne zaman kullanılmamalı

* Normal yüklü uygulamalarda (maliyet ve karmaşıklık gereksiz)
* Güçlü anlık tutarlılık şart olan işlemlerde

## Artılar

* Çok yüksek ölçeklenebilirlik
* Düşük gecikme
* Veritabanı darboğazı ortadan kalkar

## Eksiler

* Karmaşık ve pahalı
* Veri çoğaltma ve tutarlılık yönetimi zor
* Bellek boyutu sınırlıdır

## Sık yapılan hatalar

* Gerekmediği halde kullanmak
* Veri kaybı senaryolarını (düğüm çökmesi) tasarlamamak

## Gerçek hayattan örnekler

Hazelcast, Apache Ignite, Oracle Coherence tabanlı sistemler.

## İlişkili desenler

* [Microservices](microservices.md): Dağıtık alternatif
* [CQRS](../veri-ve-alan-modelleme/cqrs.md): Okuma ve yazma ayrımı fikri
