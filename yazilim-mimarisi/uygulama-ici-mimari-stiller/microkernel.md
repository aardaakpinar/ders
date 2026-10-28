# Microkernel

> Küçük bir çekirdek ve takılıp çıkarılabilen eklentilerden oluşur.

## Problem

Her yeni özelliğin çekirdeğe eklenmesi ürünü şişirir, çekirdeği kırılganlaştırır ve üçüncü tarafların ürünü genişletmesini engeller.

## Çözüm ve çalışma mantığı

* **Çekirdek:** Yalnızca temel işlevi ve eklenti yönetimini içerir.
* **Eklenti:** Belirli bir özelliği bağımsız sağlar.
* **Sözleşme:** Eklentilerin çekirdekle nasıl konuşacağını tanımlayan arayüz.

{% stepper %}
{% step %}
## Çekirdek başlar

Çekirdek başlar ve eklenti kayıt defterini hazırlar.
{% endstep %}

{% step %}
## Eklentiler kaydolur

Eklentiler kendilerini kaydeder.
{% endstep %}

{% step %}
## Eklentiler çağrılır

Çekirdek belirli noktalarda (hook) eklentileri çağırır.
{% endstep %}

{% step %}
## Eklentiler yönetilir

Eklentiler bağımsız eklenir, kaldırılır veya güncellenir.
{% endstep %}
{% endstepper %}

## Yapı

```
          +-- Plugin A
Çekirdek -+-- Plugin B
          +-- Plugin C
```

## Kod örneği

<details>

<summary>Kodu göster: TypeScript</summary>

```ts
interface Plugin {
  name: string;
  apply(context: AppContext): void;
}

class Core {
  private plugins: Plugin[] = [];
  use(plugin: Plugin) { this.plugins.push(plugin); return this; }
  start(ctx: AppContext) { this.plugins.forEach((p) => p.apply(ctx)); }
}

new Core().use(loggerPlugin).use(authPlugin).start(context);
```

</details>

## Ne zaman kullanılır

* Genişletilebilir ürünler (IDE, tarayıcı, derleyici araçları, CMS)
* Müşteriye özel özelleştirme gereken yazılımlar

## Ne zaman kullanılmamalı

* Özellik seti sabit ve küçük olan uygulamalarda

## Artılar

* Genişletme çekirdeği değiştirmeden yapılır
* Özellikler birbirinden izole
* Üçüncü taraf geliştirici ekosistemi oluşur

## Eksiler

* Eklenti sözleşmesi yayınlanınca değiştirmek zordur
* Eklentiler arası bağımlılık ve sürüm uyumu sorun çıkarabilir
* Güvenlik: kötü niyetli eklenti riski

## Sık yapılan hatalar

* Çekirdeğin eklenti detaylarını bilmesi
* Sözleşmeyi çok geniş veya çok dar tasarlamak

## Gerçek hayattan örnekler

VS Code, Eclipse, Webpack, ESLint, WordPress, Chrome eklentileri.

## İlişkili desenler

* [Pipe and Filter](pipe-and-filter.md): Eklentilerin bir zincirde çalışması
* [Modular Monolith](../sistem-ve-dagitim-mimarileri/modular-monolith.md): Modüller arası sıkı sınırlar
