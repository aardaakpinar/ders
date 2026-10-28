# Mediator

> Nesneler birbirini doğrudan çağırmaz, bir aracı (mediator) üzerinden konuşur. Controller handler'ı tanımaz, yalnızca mesaj gönderir.

## Problem

Çok sayıda nesne birbirini doğrudan çağırdığında bağlantı ağı karmaşıklaşır. Controller'lar da birçok servise bağımlı olur ve şişer.

## Çözüm ve çalışma mantığı

* **Mesaj (request):** Yapılmak istenen işi tanımlar (`CreateOrder`).
* **Handler:** Mesajı işleyen sınıf.
* **Mediator:** Mesajı doğru handler'a ulaştırır.
* **Pipeline behavior:** Handler'dan önce veya sonra çalışan ortak adımlar (loglama, doğrulama, transaction).

1. Controller bir komut oluşturur ve mediator'a gönderir.
2. Mediator pipeline adımlarından geçirir.
3. İlgili handler komutu işler.
4. Sonuç controller'a döner.

Controller handler'ı tanımadığı için ince kalır.

## Yapı

```
Controller --> Mediator.send(command) --> Handler
                    |
         Pipeline: Logging -> Validation -> Transaction -> Handler
```

## Kod örneği

Kod örnekleri varsayılan olarak gizlidir. Görmek için başlığa tıklayın.

<details>

<summary>Kodu göster: TypeScript</summary>

```ts
interface Command<TResult> { readonly __result?: TResult }
type Handler<C extends Command<R>, R> = (cmd: C) => Promise<R>;

class Mediator {
  private handlers = new Map<string, Handler<any, any>>();
  private behaviors: Array<(next: () => Promise<any>, cmd: any) => Promise<any>> = [];

  register<C extends Command<R>, R>(name: string, h: Handler<C, R>) { this.handlers.set(name, h); }
  use(b: (next: () => Promise<any>, cmd: any) => Promise<any>) { this.behaviors.push(b); }

  async send<R>(name: string, cmd: Command<R>): Promise<R> {
    const handler = this.handlers.get(name);
    if (!handler) throw new Error(`Handler yok: ${name}`);
    const run = this.behaviors.reduceRight(
      (next, b) => () => b(next, cmd),
      () => handler(cmd)
    );
    return run();
  }
}

// Kullanım
mediator.use(async (next, cmd) => { console.log("istek:", cmd); return next(); }); // logging
mediator.register("CreateOrder", async (cmd: { customerId: string }) => "order-1");
const id = await mediator.send("CreateOrder", { customerId: "c1" });
```

</details>

## Ne zaman kullanılır

* CQRS ve Vertical Slice ile birlikte
* Ortak kaygıların (log, doğrulama, yetki) tek yerde toplanması istenen projelerde

## Ne zaman kullanılmamalı

* Küçük uygulamalarda (gereksiz dolaylılık yaratır)
* Basit fonksiyon çağrısı yeterliyse

## Artılar

* İnce controller'lar
* Ortak kaygılar tek yerde
* Her use case ayrı ve test edilebilir

## Eksiler

* Akışı IDE'de takip etmek zorlaşır
* Her şeye uygulanırsa gereksiz dolaylılık
* Hata ayıklama zorlaşabilir

## Sık yapılan hatalar

* Handler'ların birbirini mediator ile çağırması (gizli bağımlılık zinciri)
* Handler içine çok fazla iş koymak

## Gerçek hayattan örnekler

.NET dünyasında MediatR kütüphanesi ile yaygındır. NestJS CQRS modülü benzer bir yapı sunar. Kütüphane lisans şartları değişebildiği için kullanmadan önce güncel lisansı kontrol edin.

## İlişkili desenler

* [CQRS](../veri-ve-alan-modelleme/cqrs.md): Komut ve sorgu yönlendirmesi
* [Vertical Slice Architecture](../uygulama-ici-mimari-stiller/vertical-slice.md): Doğal eşleşme
* [Dependency Injection (DI) / IoC](dependency-injection.md): Handler'ları kaydetmek için
