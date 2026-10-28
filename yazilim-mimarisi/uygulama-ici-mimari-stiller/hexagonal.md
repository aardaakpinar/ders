# Hexagonal

> Uygulama çekirdeği port adı verilen arayüzleri tanımlar. Dış dünya, adapter'lar aracılığıyla bu portlara bağlanır.

## Problem

Uygulama mantığı veritabanına, mesaj kuyruğuna veya arayüze doğrudan bağlandığında bu bağımlılıkları değiştirmek veya test etmek zorlaşır.

## Çözüm ve çalışma mantığı

* **Port:** Çekirdeğin dış dünya ile konuşma sözleşmesi (arayüz).
* **Adapter:** Bu sözleşmeyi gerçek bir teknolojiyle uygulayan sınıf.
* **Sürücü (driving) taraf:** Çekirdeği çağıranlar (REST, CLI, test).
* **Sürülen (driven) taraf:** Çekirdeğin çağırdıkları (veritabanı, e-posta, kuyruk).

{% stepper %}
{% step %}
## Dış dünyadan gelen çağrı

Dış dünyadan gelen çağrı bir giriş adapter'ı ile çekirdeğe iletilir.
{% endstep %}

{% step %}
## İş mantığını çalıştırma

Çekirdek, kendi iş mantığını çalıştırır.
{% endstep %}

{% step %}
## Çıkış portunu çağırma

Dış kaynak gerektiğinde bir çıkış portunu çağırır.
{% endstep %}

{% step %}
## Gerçek teknolojiyle iletişim

Çıkış adapter'ı gerçek teknolojiyle iletişimi yapar.
{% endstep %}
{% endstepper %}

Test sırasında gerçek adapter yerine sahte adapter takılır.

## Yapı

```
REST  --+                          +-- SQL Adapter
CLI   --+--> [Port IN]  CORE  [Port OUT] --+-- Mail Adapter
Test  --+                          +-- Queue Adapter
```

## Kod örneği

Kod örnekleri varsayılan olarak gizlidir. Görmek için başlığa tıklayın.

<details>

<summary>Kodu göster: TypeScript</summary>

```ts
// Port: çekirdek tanımlar
interface NotificationPort { send(to: string, message: string): Promise<void>; }

// Çekirdek
class OrderService {
  constructor(private notifier: NotificationPort) {}
  async complete(email: string) { await this.notifier.send(email, "Siparişiniz tamamlandı"); }
}

// Adapter: dış dünya uygular
class SmtpNotifier implements NotificationPort {
  async send(to: string, message: string) { /* SMTP çağrısı */ }
}

// Test adapter'ı
class FakeNotifier implements NotificationPort {
  sent: string[] = [];
  async send(to: string, message: string) { this.sent.push(`${to}:${message}`); }
}
```

</details>

## Ne zaman kullanılır

* Çok sayıda dış sistemle çalışan uygulamalar
* Teknoloji bağımsızlığı ve kolay test istenen projeler

## Ne zaman kullanılmamalı

* Küçük ve tek entegrasyonlu projeler

## Artılar

* Teknoloji değişimi çekirdeği etkilemez
* Kolay sahte (fake) adapter ile test
* Giriş ve çıkış noktaları net

## Eksiler

* Küçük projeler için gereksiz yapı
* Port sayısı artabilir

## Sık yapılan hatalar

* Port'u teknolojiye özel tanımlamak (SQL sorgusu sızdırmak)
* Adapter içine iş kuralı yazmak

## Gerçek hayattan örnekler

Alistair Cockburn tarafından tanımlanmıştır. Modern API ve mikroservis projelerinde yaygın kullanılır.

## İlişkili desenler

* [Onion Architecture](onion.md): Aynı ailedeki halka tabanlı yaklaşım
* [Clean Architecture](clean.md): Hexagonal fikrini kapsayan mimari
* [Dependency Injection (DI) / IoC](../kod-seviyesi-ve-altyapi-desenleri/dependency-injection.md): Adapter'ları çekirdeğe bağlamak için
