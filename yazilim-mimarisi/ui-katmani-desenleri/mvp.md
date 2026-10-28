# MVP

> View pasif tutulur, tüm sunum mantığı Presenter'a taşınır. View ile Presenter arayüzler üzerinden konuşur.

## Problem

MVC'de View ile Model arasındaki bağ ve Controller'ın arayüze sızması, sunum mantığını test etmeyi zorlaştırır. Özellikle masaüstü ve mobil arayüzlerde "buton tıklanınca ne olmalı" mantığı, ekranı çalıştırmadan test edilemez.

## Çözüm ve çalışma mantığı

View, yalnızca iki iş yapar: kullanıcı olaylarını Presenter'a iletmek ve Presenter'ın verdiği veriyi göstermek. Presenter, View'ı somut sınıfıyla değil **arayüzüyle** tanır.

{% stepper %}
{% step %}
## Kullanıcı bir butona tıklar
{% endstep %}

{% step %}
## View bu olayı Presenter'a iletir
{% endstep %}

{% step %}
## Presenter girdiyi View arayüzünden okur, Model'i çağırır
{% endstep %}

{% step %}
## Presenter sonucu View arayüzünün metotlarıyla (showError, navigate vb.) ekrana yansıtır
{% endstep %}
{% endstepper %}

Testte gerçek View yerine sahte (mock) bir View verilir, böylece ekran olmadan tüm sunum mantığı sınanır.

## Yapı

```
View <---- arayüz ----> Presenter <----> Model
(pasif)                 (mantık)
```

## Kod örneği

Kod örnekleri varsayılan olarak gizlidir. Görmek için başlığa tıklayın.

<details>

<summary>Kodu göster: TypeScript</summary>

```ts
interface LoginView {
  getUsername(): string;
  getPassword(): string;
  showError(message: string): void;
  navigateToHome(): void;
}

class LoginPresenter {
  constructor(
    private view: LoginView,
    private auth: { login(u: string, p: string): Promise<boolean> }
  ) {}

  async onLoginClick(): Promise<void> {
    const ok = await this.auth.login(this.view.getUsername(), this.view.getPassword());
    ok ? this.view.navigateToHome() : this.view.showError("Hatalı kullanıcı adı veya parola");
  }
}
```

</details>

## Ne zaman kullanılır

* Sunum mantığının yoğun test edilmesi gereken masaüstü ve mobil uygulamalar
* Arayüz çerçevesi veri bağlamayı desteklemiyorsa

## Ne zaman kullanılmamalı

* Veri bağlamayı güçlü destekleyen çerçevelerde (MVVM daha az kod gerektirir)
* Çok basit ekranlarda

## Artılar

* Sunum mantığı kolay test edilir
* View ile mantık net ayrılır
* Arayüz çerçevesi değiştirilebilir

## Eksiler

* Her ekran için arayüz ve Presenter yazmak gerekir
* Presenter ile View arasında sık çağrı trafiği oluşur
* Presenter büyüyebilir

## Sık yapılan hatalar

* View arayüzünü çok geniş tutup her küçük işlemi metoda dönüştürmek
* Presenter içinde arayüz çerçevesine ait sınıfları kullanmak

## Gerçek hayattan örnekler

Eski Android uygulamaları, WinForms projeleri, GWT tabanlı arayüzler.

## İlişkili desenler

* [MVC (Model-View-Controller)](mvc.md): Temel MVC yaklaşımı
* [MVVM (Model-View-ViewModel)](mvvm.md): Veri bağlama ile aynı hedefe daha az kodla ulaşan türev
