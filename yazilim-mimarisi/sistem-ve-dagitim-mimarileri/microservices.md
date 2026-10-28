# Microservices

> Uygulama, bağımsız dağıtılabilen ve tek bir iş yeteneğine odaklanan küçük servislerden oluşur. Her servis kendi verisine sahiptir.

## Problem

Büyük bir monolitte her değişiklik tüm uygulamanın yeniden dağıtılmasını gerektirir. Ekipler birbirini bekler ve parçalar ayrı ayrı ölçeklenemez.

## Çözüm ve çalışma mantığı

Her servis bir iş yeteneğine odaklanır ve kendi veritabanını yönetir. Servisler ağ üzerinden HTTP/gRPC ile veya olaylarla haberleşir.

{% stepper %}
{% step %}
## Sınırları belirleyin

Sınırlar iş alanına göre belirlenir (bounded context).
{% endstep %}

{% step %}
## Servisleri bağımsız yönetin

Her servis bağımsız geliştirilir, test edilir ve dağıtılır.
{% endstep %}

{% step %}
## Sözleşmeler kullanın

Servisler sözleşmeler (API şeması) ile konuşur.
{% endstep %}

{% step %}
## Ortak kaygıları çözün

Ortak kaygılar (giriş noktası, gözlemlenebilirlik, direnç) altyapı desenleriyle çözülür.
{% endstep %}
{% endstepper %}

Başlamadan önce kendinize şunları sorun: Birden fazla bağımsız ekip var mı? Parçalar farklı hızda ölçekleniyor mu? CI/CD, izleme ve loglama olgun mu?

## Yapı

```
Client --> API Gateway --+--> Sipariş Servisi --> [DB-1]
                         +--> Stok Servisi    --> [DB-2]
                         +--> Ödeme Servisi   --> [DB-3]
```

## Kod örneği

Kod örnekleri varsayılan olarak gizlidir. Görmek için başlığa tıklayın.

<details>

<summary>Kodu göster: JavaScript</summary>

```js
// stock-service
app.get("/stock/:id", (req, res) => res.json({ id: req.params.id, qty: 12 }));

// order-service
app.post("/orders", async (req, res) => {
  const r = await fetch(`http://stock-service/stock/${req.body.productId}`);
  const stock = await r.json();
  if (stock.qty < req.body.qty) return res.status(409).json({ error: "Stok yetersiz" });
  res.status(201).json({ id: crypto.randomUUID() });
});
```

</details>

## Ne zaman kullanılır

* Birden fazla ekip, bağımsız dağıtım ihtiyacı
* Parçaların farklı ölçeklenme gereksinimleri
* Olgun DevOps kültürü

## Ne zaman kullanılmamalı

* Küçük ekip, belirsiz ürün alanı
* Operasyon kapasitesi sınırlıysa

## Artılar

* Bağımsız dağıtım ve ölçekleme
* Ekip özerkliği
* Hata izolasyonu
* Teknoloji çeşitliliği

## Eksiler

* Dağıtık sistem karmaşıklığı (ağ, gecikme, kısmi hata)
* Dağıtık tutarlılık zorluğu
* Operasyon ve gözlemlenebilirlik yükü
* Test etmek zorlaşır

## Sık yapılan hatalar

* Dağıtık monolit: servisler birbirine sıkı bağlı ve birlikte dağıtılıyor
* Paylaşılan veritabanı kullanmak
* Servisleri çok küçük bölmek (nano servis)

## Gerçek hayattan örnekler

Netflix, Amazon, Uber gibi büyük platformların mimarileri.

## İlişkili desenler

* [Modular Monolith](modular-monolith.md): Daha hafif alternatif
* [API Gateway](../mikroservis-ve-dagitik-sistem-desenleri/api-gateway.md): Giriş noktası
* [Saga Pattern](../mikroservis-ve-dagitik-sistem-desenleri/saga.md): Dağıtık iş akışı
* [Circuit Breaker](../mikroservis-ve-dagitik-sistem-desenleri/circuit-breaker.md): Hata dayanıklılığı
