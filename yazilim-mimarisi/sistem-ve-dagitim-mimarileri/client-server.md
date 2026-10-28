# Client-Server

> İstemci istek yapar, sunucu yanıt verir. Web'in temel modelidir.

## Problem

Veriyi ve iş mantığını her kullanıcının cihazına dağıtmak güvenlik, güncelleme ve tutarlılık sorunları doğurur.

## Çözüm ve çalışma mantığı

Sunucu, kaynakları ve iş mantığını merkezi olarak tutar. İstemci arayüzü sunar ve sunucuya istek gönderir.

{% stepper %}
{% step %}
## İstemci istek gönderir

İstemci istek gönderir (HTTP, gRPC, WebSocket).
{% endstep %}

{% step %}
## Sunucu isteği işler

Sunucu isteği doğrular, işler ve yanıt üretir.
{% endstep %}

{% step %}
## İstemci yanıtı gösterir

İstemci yanıtı kullanıcıya gösterir.
{% endstep %}
{% endstepper %}

Sunucu durumsuz (stateless) tasarlanırsa birden fazla kopya çalıştırılarak kolayca ölçeklenir.

## Yapı

```
Client ---- istek ---->  Server
Client <--- yanıt -----  Server
```

## Kod örneği

<details>

<summary>Kodu göster: JavaScript</summary>

```js
// server
app.get("/api/time", (req, res) => res.json({ now: new Date().toISOString() }));

// client
const res = await fetch("/api/time");
const { now } = await res.json();
```

</details>

## Ne zaman kullanılır

* Neredeyse tüm web ve mobil uygulamalar
* Merkezi veri ve güvenlik gereken sistemler

## Ne zaman kullanılmamalı

* Sunucusuz çalışması gereken çevrimdışı öncelikli uygulamalar (P2P veya yerel öncelikli yaklaşım)

## Artılar

* Merkezi yönetim, güvenlik ve güncelleme
* Basit ve iyi anlaşılan model
* İstemciler hafif kalır

## Eksiler

* Sunucu tek hata noktası ve darboğaz olabilir
* Ağ bağlantısı gerektirir
* Yoğun yükte sunucu ölçeklenmelidir

## Sık yapılan hatalar

* Güvenlik kontrollerini yalnızca istemcide yapmak
* Sunucuda oturum durumunu bellekte tutarak ölçeklemeyi zorlaştırmak

## Gerçek hayattan örnekler

Tarayıcı ve web sunucusu, mobil uygulama ve REST API, e-posta istemcisi ve sunucusu.

## İlişkili desenler

* [Peer-to-Peer (P2P)](peer-to-peer.md): Merkezi sunucusuz alternatif
* [Microservices](microservices.md): İstemci-sunucunun dağıtık hali
