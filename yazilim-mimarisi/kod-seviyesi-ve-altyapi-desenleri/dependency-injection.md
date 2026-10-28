# Dependency Injection

> Bir sınıf bağımlılıklarını kendisi oluşturmaz, dışarıdan alır. Nesne oluşturma kontrolü dışarıya devredilir (Inversion of Control).

## Problem

Bir sınıf bağımlılıklarını `new` ile kendisi oluşturduğunda onlara sıkı bağlanır. Test sırasında sahte nesne vermek veya teknolojiyi değiştirmek mümkün olmaz.

## Çözüm ve çalışma mantığı

Sınıf, ihtiyaç duyduğu nesneleri **arayüz** olarak tanımlar ve constructor aracılığıyla dışarıdan alır.

1. Sınıf bağımlılığını arayüz olarak constructor'da bildirir.
2. Uygulamanın başlangıç noktasında (**composition root**) gerçek nesneler oluşturulur ve birleştirilir.
3. Testte aynı sınıfa sahte nesneler verilir.

Bu, **Dependency Inversion Principle** (SOLID'in "D"si) ile birlikte çalışır: üst seviye modül, alt seviye modülün somut sınıfına değil, arayüzüne bağlıdır.

## Yaşam süreleri

| Yaşam süresi | Anlamı                       | Örnek                     |
| ------------ | ---------------------------- | ------------------------- |
| Transient    | Her istekte yeni örnek       | Hafif, durumsuz servisler |
| Scoped       | HTTP isteği başına bir örnek | Veritabanı bağlantısı     |
| Singleton    | Uygulama boyunca tek örnek   | Ayar, önbellek            |

## Enjeksiyon türleri

* **Constructor injection:** Önerilen yöntem. Bağımlılıklar zorunlu ve açıkça görülür.
* **Property injection:** İsteğe bağlı bağımlılıklar için.
* **Method injection:** Yalnızca belirli bir çağrıda gereken bağımlılıklar için.

## Kod örneği

Kod örnekleri varsayılan olarak gizlidir. Görmek için başlığa tıklayın.

<details>

<summary>Kodu göster: TypeScript</summary>

```ts
// Kötü: sınıf bağımlılığını kendisi üretiyor
class BadService {
  private repo = new PgUserRepository();
}

// İyi: bağımlılık dışarıdan geliyor (constructor injection)
class UserService {
  constructor(private repo: UserRepository, private mailer: Mailer) {}
}

// Composition root: tüm nesneler tek yerde birleştirilir
const repo = new PgUserRepository(pool);
const mailer = new SmtpMailer();
const service = new UserService(repo, mailer);

// Test: sahte bağımlılıklarla
const testService = new UserService(new InMemoryUserRepository(), new FakeMailer());
```

</details>

## Ne zaman kullanılır

* Neredeyse her ölçekte uygulama
* Test edilebilir kod isteniyorsa

## Ne zaman kullanılmamalı

* Çok küçük betiklerde (gerekirse elle bağımlılık vermek yeterlidir)

## Artılar

* Gevşek bağlılık
* Kolay test
* Teknoloji değişimi kolaylaşır

## Eksiler

* Container'ın gizli çalışması hata ayıklamayı zorlaştırabilir
* Çalışma zamanı kayıt hataları (kaydı unutulan servis)
* Çok sayıda parametreli constructor kod kokusudur

## Sık yapılan hatalar

* Service Locator kullanmak (bağımlılığı gizlice container'dan çekmek)
* Her sınıf için arayüz yazmak
* Singleton içine scoped bağımlılık koymak

## Gerçek hayattan örnekler

NestJS, Angular DI, Spring, ASP.NET Core, tsyringe, InversifyJS.

## İlişkili desenler

* [Onion Architecture](../uygulama-ici-mimari-stiller/onion.md): Bağımlılık yönünü ters çevirir
* [Hexagonal Architecture (Ports and Adapters)](../uygulama-ici-mimari-stiller/hexagonal.md): Adapter'ları çekirdeğe bağlar
* [Clean Architecture](../uygulama-ici-mimari-stiller/clean.md): Clean Architecture'ın temel aracı
