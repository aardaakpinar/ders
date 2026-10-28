# MVC

> Kullanıcı girişini Controller karşılar, iş mantığını Model üstlenir, sonucu View gösterir. Sunucu taraflı web uygulamalarının en yaygın arayüz desenidir.

## Problem

Arayüz üretimi, veri erişimi ve iş kuralları tek bir yerde toplandığında (örneğin HTML üreten bir fonksiyonun içinde SQL sorgusu çalıştırmak) kod hem okunmaz hem test edilemez hale gelir. Bir ekranın görünümünü değiştirmek iş kuralını bozabilir, bir kuralı değiştirmek ekranı bozabilir.

## Çözüm ve çalışma mantığı

Sorumluluk üç role ayrılır:

* **Model:** Veriyi ve iş kurallarını taşır. Arayüzden habersizdir.
* **View:** Model'den gelen veriyi kullanıcıya gösterir. Karar vermez.
* **Controller:** Kullanıcı girdisini alır, Model'e iletir, sonucu uygun View'a yönlendirir.

Tipik web akışı:

{% stepper %}
{% step %}
## Kullanıcı bir istek gönderir

Kullanıcı bir istek gönderir (URL, form).
{% endstep %}

{% step %}
## Router isteği yönlendirir

Router isteği ilgili Controller eylemine yönlendirir.
{% endstep %}

{% step %}
## Controller girdiyi işler

Controller girdiyi doğrular ve Model'den veri ister veya durum değiştirir.
{% endstep %}

{% step %}
## Controller sonucu View'a verir

Controller sonucu View'a verir.
{% endstep %}

{% step %}
## View yanıtı üretir

View HTML üretir ve yanıt olarak döner.
{% endstep %}
{% endstepper %}

Masaüstü MVC'sinde View, Model'i doğrudan gözlemleyebilir. Web MVC'sinde ise View yalnızca Controller'ın verdiği veriyi çizer.

## Yapı

```
Kullanıcı --> Controller --> Model
                 |             |
                 v             |
               View <----------+
```

## Kod örneği

Kod örnekleri varsayılan olarak gizlidir. Görmek için başlığa tıklayın.

<details>

<summary>Kodu göster: JavaScript</summary>

```js
// model.js
const products = [{ id: 1, name: "Klavye", price: 750 }];
export const findProduct = (id) => products.find((p) => p.id === id);

// controller.js
import { findProduct } from "./model.js";

export function showProduct(req, res) {
  const product = findProduct(Number(req.params.id));
  if (!product) return res.status(404).send("Bulunamadı");
  res.render("product", { product }); // View
}

// app.js
app.get("/products/:id", showProduct);
```

</details>

## Ne zaman kullanılır

* Sunucu tarafında sayfa üreten uygulamalar
* CRUD ağırlıklı yönetim panelleri
* Ekibin şablon motoru (EJS, Razor, Thymeleaf vb.) deneyimi varsa

## Ne zaman kullanılmamalı

* Durumu yoğun ve etkileşimli tek sayfalı uygulamalarda (bileşen tabanlı yaklaşım daha uygundur)
* İş kuralları çok karmaşıksa. MVC yalnızca arayüz katmanını düzenler, iş mantığı için ayrıca bir yapı gerekir.

## Artılar

* Basit, öğrenmesi kolay ve çok yaygın
* Çoğu çerçeve hazır destek verir
* Görünüm ile mantık ayrıldığı için paralel çalışmak kolaylaşır

## Eksiler

* Controller zamanla şişebilir
* "Model" kavramı belirsiz kalırsa iş mantığı her yere dağılır
* Karmaşık arayüz durumunu yönetmek için yeterli değildir

## Sık yapılan hatalar

* İş mantığını Controller'a yazmak (Fat Controller)
* Model'i yalnızca veritabanı tablosu sanmak
* View içinde sorgu veya iş kuralı çalıştırmak

## Gerçek hayattan örnekler

Ruby on Rails, Spring MVC, ASP.NET Core MVC, Laravel, Express ile şablon motoru kullanan uygulamalar.

## İlişkili desenler

* [MVP (Model-View-Presenter)](mvp.md): Presenter ile test edilebilirliği artıran türev
* [MVVM (Model-View-ViewModel)](mvvm.md): Veri bağlama ile çalışan türev
* [Layered (N-Tier) Architecture](../uygulama-ici-mimari-stiller/layered.md): MVC'nin arkasında genelde katmanlı yapı bulunur
