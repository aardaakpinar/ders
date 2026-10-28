# Flux / Redux

> Uygulamanın tek bir global durumu (store) vardır. Durum yalnızca action ve reducer hattıyla değişir.

## Problem

Birçok bileşen aynı veriyi paylaştığında, bu veriyi bileşenler arasında aktarmak (prop drilling) ve kimin ne zaman değiştirdiğini takip etmek zorlaşır.

## Çözüm ve çalışma mantığı

Dört parça vardır: **Action** (ne oldu), **Reducer** (durumu nasıl değiştirir), **Store** (durumun kendisi), **View** (durumu okur ve action gönderir).

{% stepper %}
{% step %}
## Kullanıcı eylemi

Kullanıcı bir eylem yapar, bileşen `dispatch(action)` çağırır.
{% endstep %}

{% step %}
## Action reducer'a iletilir

Store, action'ı reducer'a verir.
{% endstep %}

{% step %}
## Yeni durum üretilir

Reducer, eski durumdan yeni bir durum üretir (saf fonksiyon).
{% endstep %}

{% step %}
## Store güncellenir

Store yeni durumu saklar ve abonelere bildirir.
{% endstep %}

{% step %}
## Bileşenler yeniden çizilir

Bileşenler yeni durumu okuyup yeniden çizilir.
{% endstep %}
{% endstepper %}

Yan etkiler (ağ çağrısı) reducer içinde değil, ayrı bir katmanda (thunk, saga, middleware) yönetilir.

## Yapı

```
View --> Action --> Reducer --> Store --> View
```

## Temel ilkeler

| İlke                 | Anlamı                                                   |
| -------------------- | -------------------------------------------------------- |
| Tek doğruluk kaynağı | Uygulama durumu tek bir store'da tutulur                 |
| Durum salt okunurdur | Yalnızca action ile değişir                              |
| Saf reducer'lar      | Aynı girdi her zaman aynı çıktıyı verir, yan etki yoktur |

## Kod örneği

{% code title="Kodu göster: JavaScript" expandable="true" %}
```js
import { configureStore, createSlice } from "@reduxjs/toolkit";

const cartSlice = createSlice({
  name: "cart",
  initialState: { items: [] },
  reducers: {
    added(state, action) { state.items.push(action.payload); },
    removed(state, action) {
      state.items = state.items.filter((i) => i.id !== action.payload);
    },
  },
});

export const { added, removed } = cartSlice.actions;
export const store = configureStore({ reducer: { cart: cartSlice.reducer } });

store.dispatch(added({ id: 1, name: "Kalem" }));
```
{% endcode %}

## Ne zaman kullanılır

* Çok sayıda bileşenin ortak durum paylaştığı büyük arayüzler
* Durum geçişlerinin kaydedilmesi ve geri alınması gereken uygulamalar

## Ne zaman kullanılmamalı

* Küçük uygulamalar (yerel durum veya Context yeterlidir)
* Sunucu verisini önbelleklemek için (React Query, SWR gibi araçlar daha uygundur)

## Artılar

* Öngörülebilir durum yönetimi
* Güçlü hata ayıklama araçları (zaman yolculuğu)
* Ekip için ortak bir kural seti

## Eksiler

* Küçük işler için fazla yapı
* Her değişiklik için action ve reducer yazmak gerekir (Redux Toolkit bunu azaltır)

## Sık yapılan hatalar

* Her küçük durumu global store'a koymak
* Reducer içinde yan etki çalıştırmak
* Durumu doğrudan mutasyona uğratmak

## Gerçek hayattan örnekler

Redux, Redux Toolkit, Zustand, Pinia, NgRx.

## İlişkili desenler

* [MVI (Model-View-Intent)](mvi.md): Aynı fikrin bileşen düzeyindeki versiyonu
* [Component-Based Architecture](component-based.md): Bileşenler bu store'u okur
