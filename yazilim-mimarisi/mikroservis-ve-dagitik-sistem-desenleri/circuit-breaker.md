# Circuit Breaker

> Sürekli hata veren bir servise istek göndermeye devam etmek yerine devreyi açar ve hızlıca hata döner. Böylece sistem zincirleme çöküşten korunur.

## Problem

Bir servis yavaşladığında veya çöktüğünde, onu çağıran servisler de beklemeye başlar. Bekleyen istekler kaynakları (iş parçacığı, bağlantı) tüketir ve hata zincirleme olarak yayılır.

## Çözüm ve çalışma mantığı

Devre üç durumdan birinde olur:

{% stepper %}
{% step %}
## Closed (kapalı)

İstekler normal geçer, hatalar sayılır.
{% endstep %}

{% step %}
## Open (açık)

Hata eşiği aşıldığında devre açılır. İstekler çağrılmadan hemen reddedilir veya fallback döner.
{% endstep %}

{% step %}
## Half-Open (yarı açık)

Bekleme süresi dolunca birkaç deneme isteği geçirilir. Başarılıysa devre kapanır, değilse tekrar açılır.
{% endstep %}
{% endstepper %}

Devre açıkken sistem, bozuk servise yük bindirmez ve toparlanması için zaman tanır.

## Yapı

```
          hata eşiği aşıldı
 CLOSED ------------------> OPEN
    ^                         | bekleme süresi doldu
    | başarılı                v
    +------------------- HALF-OPEN (deneme isteği)
```

## Durumlar

| Durum     | Davranış                                              |
| --------- | ----------------------------------------------------- |
| Closed    | İstekler normal geçer, hatalar sayılır                |
| Open      | İstekler hemen reddedilir                             |
| Half-Open | Birkaç deneme isteği geçer. Başarılıysa devre kapanır |

## Birlikte kullanılan dayanıklılık desenleri

* **Retry:** Geçici hatalarda tekrar deneme (üstel geri çekilme ile).
* **Timeout:** Beklemeyi sınırlama.
* **Bulkhead:** Kaynakları izole ederek bir bileşenin diğerlerini tüketmesini önleme.
* **Fallback:** Hata durumunda alternatif yanıt (önbellek, varsayılan değer).

## Kod örneği

Kod örnekleri varsayılan olarak gizlidir. Görmek için başlığa tıklayın.

{% code title="Kodu göster: TypeScript" expandable="true" %}
```ts
class CircuitBreaker {
  private failures = 0;
  private state: "CLOSED" | "OPEN" | "HALF_OPEN" = "CLOSED";
  private openedAt = 0;

  constructor(private threshold = 5, private cooldownMs = 30_000) {}

  async exec<T>(fn: () => Promise<T>, fallback?: () => T): Promise<T> {
    if (this.state === "OPEN") {
      if (Date.now() - this.openedAt < this.cooldownMs) {
        if (fallback) return fallback();
        throw new Error("Devre açık");
      }
      this.state = "HALF_OPEN";
    }
    try {
      const result = await fn();
      this.failures = 0;
      this.state = "CLOSED";
      return result;
    } catch (e) {
      this.failures++;
      if (this.state === "HALF_OPEN" || this.failures >= this.threshold) {
        this.state = "OPEN";
        this.openedAt = Date.now();
      }
      throw e;
    }
  }
}

const breaker = new CircuitBreaker();
const rate = await breaker.exec(() => fetchRate(), () => cachedRate);
```
{% endcode %}

## Ne zaman kullanılır

* Başka servislere veya dış API'lere bağımlı her sistem
* Zincirleme hata riski olan mikroservis mimarileri

## Ne zaman kullanılmamalı

* Tek monolit ve harici bağımlılığı olmayan basit uygulamalarda

## Artılar

* Zincirleme hataları önler
* Kaynak tükenmesini engeller
* Bozuk servise toparlanma zamanı tanır

## Eksiler

* Eşik ve süre değerlerini ayarlamak zordur
* Fallback stratejisi tasarlanmalıdır
* Yanlış ayarlarla gereksiz yere açılabilir

## Sık yapılan hatalar

* Fallback tasarlamamak
* Tüm hata türlerini aynı sayıp (örneğin 404'ü de hata saymak) gereksiz yere devreyi açmak
* Devre durumunu izlememek

## Gerçek hayattan örnekler

Netflix Hystrix ile yaygınlaştı. Güncel araçlar: Resilience4j, Polly, opossum (Node.js), Envoy ve Istio.

## İlişkili desenler

* [Microservices](../sistem-ve-dagitim-mimarileri/microservices.md): Kullanıldığı mimari
* [API Gateway](api-gateway.md): Gateway'de sık uygulanır
* [Sidecar / Service Mesh](sidecar-service-mesh.md): Service mesh bu işi altyapı düzeyinde yapabilir
