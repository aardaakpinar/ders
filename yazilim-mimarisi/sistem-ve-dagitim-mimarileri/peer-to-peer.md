# Peer to Peer

> Her düğüm (peer) hem istemci hem sunucu görevi görür. Merkezi bir sunucu yoktur veya çok sınırlıdır.

## Problem

Merkezi sunucu hem maliyetlidir hem de tek hata noktasıdır. Çok büyük dosya dağıtımında veya sansüre dayanıklı sistemlerde tek merkez yetersiz kalır.

## Çözüm ve çalışma mantığı

Her düğüm kaynaklarını (depolama, bant genişliği, işlem gücü) ağa sunar ve diğer düğümlerin kaynaklarını kullanır.

{% stepper %}
{% step %}
## Düğüm ağa katılır ve diğer düğümleri keşfeder

DHT, tracker veya sinyalleşme sunucusu kullanılır.
{% endstep %}

{% step %}
## Düğümler doğrudan birbirleriyle bağlantı kurar
{% endstep %}

{% step %}
## Veri düğümler arasında paylaşılır veya çoğaltılır
{% endstep %}

{% step %}
## Düğüm eklendikçe ağın toplam kapasitesi artar
{% endstep %}
{% endstepper %}

Tam merkezsiz (saf) ve melez (keşif için merkezi bir bileşen olan) türleri vardır.

## Yapı

```
 A <----> B
 ^ \    / ^
 |  \  /  |
 v   \/   v
 C <----> D
```

## Kod örneği

Kod örnekleri varsayılan olarak gizlidir. Görmek için başlığa tıklayın.

<details>

<summary>Kodu göster: JavaScript</summary>

```js
const peer = new RTCPeerConnection();
const channel = peer.createDataChannel("chat");

channel.onopen = () => channel.send("Merhaba");
channel.onmessage = (e) => console.log("Gelen:", e.data);

// offer/answer değişimi için bir sinyalleşme sunucusu gerekir,
// veri akışı başladıktan sonra doğrudan eşler arasında gider.
```

</details>

## Ne zaman kullanılır

* Dosya paylaşımı ve dağıtımı
* Blockchain ve merkeziyetsiz uygulamalar
* Gerçek zamanlı iletişim (WebRTC)

## Ne zaman kullanılmamalı

* Güçlü tutarlılık ve merkezi denetim gereken sistemlerde

## Artılar

* Tek hata noktası yok
* Düğüm sayısıyla artan kapasite
* Sansüre dayanıklılık

## Eksiler

* Güvenlik ve güven sorunları
* Tutarlılık ve düğüm keşfi zor
* NAT ve güvenlik duvarı sorunları

## Sık yapılan hatalar

* Düğümlere güvenmek (veriyi doğrulamamak)
* Keşif mekanizmasını hesaba katmamak

## Gerçek hayattan örnekler

BitTorrent, IPFS, Bitcoin ve Ethereum ağları, WebRTC görüntülü görüşmeler.

## İlişkili desenler

* [Client-Server](client-server.md): Merkezi alternatif
