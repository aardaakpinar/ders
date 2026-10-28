# Serverless FaaS



> Kod fonksiyon olarak yazılır, sunucu yönetimini bulut sağlayıcı üstlenir. Fonksiyon bir olayla tetiklenir ve kullanım kadar ücretlendirilir.

## Problem

Düzensiz veya seyrek çalışan işler için sürekli açık sunucu tutmak, kullanılmayan kapasiteye para ödemek ve sunucu yönetmek anlamına gelir.

## Çözüm ve çalışma mantığı

Fonksiyon, bir tetikleyiciyle (HTTP isteği, kuyruk mesajı, dosya yükleme, zamanlayıcı) çalışır.

{% stepper %}
{% step %}
## Bir olay gerçekleşir
{% endstep %}

{% step %}
## Platform fonksiyonu gerekirse yeni bir örnekle başlatır
{% endstep %}

{% step %}
## Fonksiyon işini yapar ve sonuç döner
{% endstep %}

{% step %}
## Kullanılmayan örnekler kapatılır

Ücret çalışma süresine göre hesaplanır.
{% endstep %}
{% endstepper %}

Fonksiyonlar durumsuz (stateless) olmalıdır. Kalıcı veri harici bir depoya yazılır.

## Kod örneği

Kod örnekleri varsayılan olarak gizlidir. Görmek için başlığa tıklayın.

<details>

<summary>Kodu göster: JavaScript</summary>

```js
export const handler = async (event) => {
  const body = JSON.parse(event.body ?? "{}");
  if (!body.email) return { statusCode: 400, body: "email gerekli" };

  await sendWelcomeMail(body.email);
  return { statusCode: 200, body: JSON.stringify({ ok: true }) };
};
```

</details>

## Ne zaman kullanılır

* Düzensiz yük, webhook'lar, zamanlanmış görevler
* Dosya ve görsel işleme
* Hızlı prototip ve olay tabanlı iş akışları

## Ne zaman kullanılmamalı

* Uzun süren işlemler ve çok düşük gecikme gerektiren senaryolar
* Sabit ve yüksek trafikte maliyet avantajı kalkabilir

## Artılar

* Sunucu yönetimi yok
* Otomatik ölçekleme
* Kullandığın kadar ödersin

## Eksiler

* Soğuk başlangıç (cold start) gecikmesi
* Çalışma süresi ve kaynak sınırları
* Sağlayıcıya bağımlılık
* Yerelde test ve hata ayıklama zorluğu

## Sık yapılan hatalar

* Fonksiyonda durum tutmak
* Çok büyük ve çok işlevli fonksiyonlar yazmak
* Zincirleme fonksiyon çağrılarıyla hata ayıklanması zor akış kurmak

## Gerçek hayattan örnekler

AWS Lambda, Azure Functions, Google Cloud Functions, Cloudflare Workers.

## İlişkili desenler

* [EDA (Event-Driven Architecture)](eda.md): Olay tabanlı tetiklemeyle sık birlikte kullanılır
* [Microservices](microservices.md): Mikroservislere alternatif veya tamamlayıcı
