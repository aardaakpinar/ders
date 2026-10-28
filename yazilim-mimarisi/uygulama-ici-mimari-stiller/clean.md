# Clean

> Onion ve Hexagonal fikirlerinin birleşimi. Temel kural: kaynak kod bağımlılıkları yalnızca içe doğru olur.

## Problem

İş mantığı çerçeveye, veritabanına ve arayüze bağımlı olduğunda uygulama bunlarla birlikte eskir. Teknoloji değişiminde iş kuralları da yeniden yazılır.

## Çözüm ve çalışma mantığı

Dört ana halka:

* **Entities:** En genel iş kuralları ve domain nesneleri.
* **Use Cases:** Uygulamaya özel iş akışları (örneğin "kullanıcı oluştur").
* **Interface Adapters:** Controller, presenter, repository uygulamaları.
* **Frameworks and Drivers:** Web çerçevesi, veritabanı, dış servisler.

**Bağımlılık kuralı:** Bir halka yalnızca kendinden içeridekileri bilir. Dışarıya ihtiyaç olunca arayüz tanımlanır ve dış halka uygular.

Pratikte klasörler `domain`, `application`, `infrastructure` ve `presentation` olarak ayrılır.

## Yapı

```
Frameworks   -->  Interface Adapters  -->  Use Cases  -->  Entities
(Web, DB)         (Controller, Repo)       (Application)    (Domain)
```

```
src/
  domain/            Entity, Value Object
  application/       Use case, port arayüzleri
  infrastructure/    Veritabanı, dış servisler
  presentation/      HTTP controller, DI kaydı
```

## Kod örneği

Kod örnekleri varsayılan olarak gizlidir. Görmek için başlığa tıklayın.

<details>

<summary>Kodu göster: TypeScript</summary>

```ts
// application/CreateUser.ts
export interface UserGateway { exists(email: string): Promise<boolean>; add(u: User): Promise<void>; }

export class CreateUser {
  constructor(private users: UserGateway) {}
  async execute(input: { email: string; name: string }) {
    if (await this.users.exists(input.email)) throw new Error("E-posta kayıtlı");
    await this.users.add(new User(input.email, input.name));
  }
}
```

</details>

## Ne zaman kullanılır

* Uzun ömürlü kurumsal uygulamalar
* İş kuralları zengin olan ve çerçeve bağımsızlığı istenen projeler

## Ne zaman kullanılmamalı

* Basit CRUD ve kısa ömürlü prototipler

## Artılar

* Çerçeve ve veritabanından bağımsız iş mantığı
* Yüksek test edilebilirlik
* Net sorumluluklar

## Eksiler

* Dosya ve klasör sayısı artar
* Basit işler için fazla tören
* Yeni gelen geliştirici için öğrenme eğrisi

## Sık yapılan hatalar

* Tüm katmanlar için aynı sınıfı paylaşmak (entity'yi API'den döndürmek)
* Use case'leri anlamsız sarmalayıcılara dönüştürmek
* Kuralı sadece klasörde değil, derleme düzeyinde de (paket sınırlarıyla) zorlamamak

## Gerçek hayattan örnekler

Robert C. Martin (Uncle Bob) tarafından yaygınlaştırıldı. NestJS ve Node.js kurumsal projelerinde sık görülür.

## İlişkili desenler

* [Onion Architecture](onion.md): Kardeş mimari
* [Hexagonal Architecture (Ports and Adapters)](hexagonal.md): Port ve adapter fikri
* [CQRS](../veri-ve-alan-modelleme/cqrs.md): Use case'leri komut ve sorgu olarak yazma
* [Mediator Pattern](../kod-seviyesi-ve-altyapi-desenleri/mediator.md): Use case'leri mesajlarla çağırma
