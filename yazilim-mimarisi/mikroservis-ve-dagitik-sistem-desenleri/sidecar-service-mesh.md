# Sidecar / Service Mesh

> Sidecar, ana servisin yanında ayrı bir süreç olarak çalışıp ortak altyapı işlerini üstlenir. Service Mesh, tüm sidecar'ların ve onları yöneten kontrol düzleminin bütünüdür.

## Problem

Mikroservislerde her servis, aynı ortak işleri (şifreleme, yeniden deneme, loglama, metrik) kendi diliyle yeniden yazmak zorunda kalır. Farklı dillerdeki servislerde bu tutarlı yönetilemez.

## Çözüm ve çalışma mantığı

* **Sidecar:** Uygulamayla aynı pod veya makinede çalışan yardımcı proxy. Uygulamanın tüm ağ trafiği buradan geçer.
* **Data plane:** Tüm sidecar proxy'lerinin oluşturduğu trafik katmanı.
* **Control plane:** Proxy'lere politika ve yapılandırmayı dağıtan yönetim katmanı.

{% stepper %}
{% step %}
## Uygulama bir servise istek gönderir
{% endstep %}

{% step %}
## İstek önce kendi sidecar'ına gider
{% endstep %}

{% step %}
## Sidecar isteği işler

Sidecar şifreler (mTLS), yönlendirir, yeniden dener, metrik ve iz toplar.
{% endstep %}

{% step %}
## Karşı servisin sidecar'ı isteği iletir

Karşı servisin sidecar'ı isteği alıp uygulamaya iletir.
{% endstep %}
{% endstepper %}

Uygulama kodu değişmez, ortak kaygılar altyapı katmanında çözülür.

## Yapı

```
+------------- Pod --------------+
| [ Uygulama ] <--> [ Sidecar ]   |
|                   log, mTLS,    |
|                   metrik, proxy |
+---------------------------------+
```

```
Servis A <--> [Proxy] ==== mTLS ==== [Proxy] <--> Servis B
                  ^                     ^
                  +---- Control Plane ---+
```

## Service Mesh'in sağladıkları

* Servisler arası şifreleme (mTLS)
* Trafik yönlendirme (canary, blue-green)
* Yeniden deneme, zaman aşımı ve devre kesici
* Gözlemlenebilirlik (metrik, dağıtık izleme)
* Politika ve erişim kontrolü

## Kod örneği

Kod örnekleri varsayılan olarak gizlidir. Görmek için başlığa tıklayın.

<details>

<summary>Kodu göster: JavaScript</summary>

```js
// Uygulama yalnızca localhost:3000 ile konuşur, sidecar dış dünyaya çıkışı yönetir.
import http from "node:http";

http.createServer((req, res) => {
  const started = Date.now();
  const upstream = http.request(
    { host: "order-service", port: 3002, path: req.url, method: req.method, headers: req.headers },
    (up) => {
      res.writeHead(up.statusCode, up.headers);
      up.pipe(res);
      console.log(JSON.stringify({ path: req.url, status: up.statusCode, ms: Date.now() - started }));
    }
  );
  req.pipe(upstream);
}).listen(3000);
```

</details>

## Ne zaman kullanılır

* Çok sayıda ve farklı dillerde yazılmış servis olduğunda
* Servisler arası güvenlik ve gözlemlenebilirlik standartlaştırılmak istendiğinde

## Ne zaman kullanılmamalı

* Az sayıda servis olduğunda (operasyonel yük kazanımı aşar)
* Kubernetes ve container deneyimi yoksa

## Artılar

* Ortak kaygılar uygulama kodundan ayrılır
* Dil bağımsızdır
* Tutarlı güvenlik ve izleme

## Eksiler

* Operasyonel karmaşıklık
* Ek kaynak tüketimi ve gecikme
* Hata ayıklama zorlaşabilir

## Sık yapılan hatalar

* Az servis için service mesh kurmak
* Mesh ile uygulama içindeki retry ve timeout ayarlarını çakıştırmak

## Gerçek hayattan örnekler

Istio, Linkerd, Consul Connect, Envoy proxy, Dapr.

## İlişkili desenler

* [Circuit Breaker](circuit-breaker.md): Mesh bu deseni altyapıda sağlar
* [Microservices](../sistem-ve-dagitim-mimarileri/microservices.md): Kullanıldığı mimari
* [API Gateway](api-gateway.md): Gateway ile tamamlayıcı
