# MVI

> Kullanıcı niyeti (Intent) bir durum (State) üretir, View yalnızca bu durumu çizer. Durum değişmezdir ve akış tek yönlüdür.

## Problem

Arayüzde birden fazla durum parçası (yükleniyor, hata, veri) birbirinden bağımsız değiştiğinde tutarsız ekranlar oluşur. Örneğin hem "yükleniyor" hem "hata" aynı anda görünebilir.

## Çözüm ve çalışma mantığı

Ekranın tamamı **tek bir durum nesnesiyle** ifade edilir.

{% stepper %}
{% step %}
## Kullanıcı eylemi

Kullanıcı bir eylem yapar ve bu bir Intent'e dönüşür.
{% endstep %}

{% step %}
## Yeni durum üretimi

Reducer, mevcut durum ile Intent'i alıp **yeni** bir durum üretir.
{% endstep %}

{% step %}
## Görünümün çizilmesi

View yeni durumu çizer.
{% endstep %}

{% step %}
## Yan etkiler

Yan etkiler (ağ çağrısı) ayrı bir katmanda yürür ve sonuçları yeni bir Intent olarak geri gelir.
{% endstep %}
{% endstepper %}

Durum her seferinde yeniden üretildiği için geçmiş kaydedilebilir, hata ayıklama kolaylaşır.

## Yapı

```
Intent --> Reducer --> State --> View
  ^                                |
  +----------- kullanıcı ----------+
```

## Kod örneği

Kod örnekleri varsayılan olarak gizlidir. Görmek için başlığa tıklayın.

{% code title="Kodu göster: TypeScript" expandable="true" %}
```ts
type State = { count: number; loading: boolean };
type Intent = { type: "INC" } | { type: "DEC" } | { type: "RESET" };

const initial: State = { count: 0, loading: false };

function reduce(state: State, intent: Intent): State {
  switch (intent.type) {
    case "INC":   return { ...state, count: state.count + 1 };
    case "DEC":   return { ...state, count: state.count - 1 };
    case "RESET": return initial;
  }
}
```
{% endcode %}

## Ne zaman kullanılır

* Karmaşık ve birbirine bağlı durumlar içeren ekranlar
* Durum geçişlerinin izlenmesi gereken uygulamalar

## Ne zaman kullanılmamalı

* Basit formlar ve statik ekranlar

## Artılar

* Durum öngörülebilir, aynı girdi aynı çıktıyı verir
* Hata ayıklama ve test kolaydır
* Geçersiz durum kombinasyonları engellenebilir

## Eksiler

* Basit işler için fazla tören
* Çok sayıda Intent ve durum sınıfı oluşur
* Durum çok büyürse performans için dikkat gerekir

## Sık yapılan hatalar

* Durumu yerinde değiştirmek (mutasyon)
* Birden fazla bağımsız boolean yerine tek bir durum modeli kurmamak

## Gerçek hayattan örnekler

Android Compose ile yazılan uygulamalar, Kotlin tabanlı MVI kütüphaneleri, reducer tabanlı React bileşenleri (useReducer).

## İlişkili desenler

* [Flux / Redux](flux-redux.md): Aynı fikrin global durum versiyonu
* [MVVM (Model-View-ViewModel)](mvvm.md): Veri bağlama ile çalışan alternatif
