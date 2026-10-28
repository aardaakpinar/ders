# MVVM

> View, ViewModel'e veri bağlama ile bağlanır. ViewModel arayüzü tanımaz, yalnızca durumu ve komutları sunar.

## Problem

Presenter yaklaşımında her durum değişikliğinde View'a elle komut vermek gerekir. Bu, çok sayıda tekrarlayan "şunu göster, bunu gizle" kodu üretir ve View ile Presenter arasındaki bağı sıklaştırır.

## Çözüm ve çalışma mantığı

ViewModel, ekranın **durumunu** (metinler, liste, yüklenme bilgisi) ve **komutlarını** (kaydet, sil) dışarı açar. View bunlara bağlanır. Durum değişince çerçeve View'ı otomatik günceller.

{% stepper %}
{% step %}
## ViewModel gözlemlenebilir özellikler tanımlar
{% endstep %}

{% step %}
## View bu özelliklere bağlanır

Binding kullanılır.
{% endstep %}

{% step %}
## Kullanıcı bir işlem yapınca View komutu çağırır
{% endstep %}

{% step %}
## ViewModel Model'i çağırır ve durumunu günceller
{% endstep %}

{% step %}
## Çerçeve değişikliği fark edip View'ı yeniler
{% endstep %}
{% endstepper %}

ViewModel View'ı hiç bilmez, bu nedenle arayüz olmadan test edilebilir.

## Yapı

```
View <== binding ==> ViewModel --> Model
```

## Kod örneği

{% code title="Kodu göster: TypeScript" expandable="true" %}
```ts
class Observable<T> {
  private listeners: Array<(v: T) => void> = [];
  constructor(private value: T) {}
  get(): T { return this.value; }
  set(v: T): void { this.value = v; this.listeners.forEach((l) => l(v)); }
  subscribe(l: (v: T) => void): void { this.listeners.push(l); }
}

class CounterViewModel {
  count = new Observable(0);
  increment = () => this.count.set(this.count.get() + 1);
}

// View
const vm = new CounterViewModel();
vm.count.subscribe((v) => (document.querySelector("#label")!.textContent = String(v)));
document.querySelector("#btn")!.addEventListener("click", vm.increment);
```
{% endcode %}

## Ne zaman kullanılır

* Veri bağlamayı destekleyen çerçeveler: Vue, Angular, WPF, SwiftUI, Jetpack Compose
* Form ağırlıklı ve durumu çok olan ekranlar

## Ne zaman kullanılmamalı

* Veri bağlama olmayan ortamlarda
* Çok basit, tek seferlik ekranlarda

## Artılar

* Az sayıda tekrarlayan kod
* ViewModel kolay test edilir
* Tasarımcı ve geliştirici bağımsız çalışabilir

## Eksiler

* Bağlama hatalarını ayıklamak zordur
* Büyük ViewModel'ler karmaşıklaşabilir
* Bellek sızıntısı riski (abonelikleri temizlememek)

## Sık yapılan hatalar

* ViewModel içinde View'a ait sınıflara (DOM, Context) başvurmak
* Tüm uygulama durumunu tek ViewModel'e doldurmak

## Gerçek hayattan örnekler

WPF, Xamarin/MAUI, Android Jetpack ViewModel, Vue ve Angular uygulamaları.

## İlişkili desenler

* [MVP (Model-View-Presenter)](mvp.md): Presenter tabanlı alternatif
* [MVI (Model-View-Intent)](mvi.md): Tek yönlü ve değişmez durum yaklaşımı
* [Component-Based Architecture](component-based.md): Modern bileşen çerçeveleri bu fikri içerir
