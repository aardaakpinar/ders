# Layered

> Kod yatay katmanlara ayrılır. Her katman yalnızca bir alt katmanı kullanır.

## Problem

Sunum, iş mantığı ve veri erişimi aynı kodda karıştığında değişiklikler birbirini etkiler ve hiçbir parça ayrı test edilemez.

## Çözüm ve çalışma mantığı

Tipik dört katman: **Presentation** (arayüz, API), **Business** (iş kuralları), **Data Access** (veritabanı işlemleri), **Database**.

{% stepper %}
{% step %}
## İstek en üst katmana gelir
{% endstep %}

{% step %}
## Üst katman, iş kuralları için alttaki katmanı çağırır
{% endstep %}

{% step %}
## İş katmanı veriye ihtiyaç duyunca veri erişim katmanını çağırır
{% endstep %}

{% step %}
## Yanıt aynı yoldan yukarı döner
{% endstep %}
{% endstepper %}

Katmanlar "kapalı" (yalnızca bir alt katmanı çağırır) veya "açık" (alttaki birkaç katmanı atlayabilir) olabilir. Kapalı katmanlar daha temiz ama daha fazla geçiş kodu üretir.

N-Layer mantıksal ayrımı, N-Tier fiziksel (ayrı sunucular) ayrımı ifade eder.

## Yapı

```
+--------------------+
| Presentation       |
+--------------------+
| Business           |
+--------------------+
| Data Access        |
+--------------------+
| Database           |
+--------------------+
```

## Kod örneği

Kod örnekleri varsayılan olarak gizlidir. Görmek için başlığa tıklayın.

<details>

<summary>Kodu göster: TypeScript</summary>

```ts
// data access
class UserRepository {
  async findById(id: number) { /* SQL sorgusu */ return { id, name: "Ayşe" }; }
}

// business
class UserService {
  constructor(private repo: UserRepository) {}
  async getProfile(id: number) {
    const user = await this.repo.findById(id);
    if (!user) throw new Error("Kullanıcı yok");
    return user;
  }
}

// presentation
app.get("/users/:id", async (req, res) => res.json(await service.getProfile(+req.params.id)));
```

</details>

## Ne zaman kullanılır

* Basit ve orta karmaşıklıkta uygulamalar
* Hızlı başlangıç gereken ve ekibin tanıdığı bir yapı istenen projeler

## Ne zaman kullanılmamalı

* İş kuralları çok karmaşıksa ve veritabanından bağımsız olması gerekiyorsa (Onion, Clean tercih edilir)

## Artılar

* Basit ve herkesin bildiği bir yapı
* Katmanlar ayrı geliştirilebilir
* Küçük projelerde hızlı ilerlenir

## Eksiler

* İş mantığı veri katmanına bağımlı kalır
* Büyüdükçe katmanlar sadece çağrıyı aktaran geçişlere dönüşür
* Bir özellik için birçok katmanda değişiklik gerekir

## Sık yapılan hatalar

* Katmanları atlayıp doğrudan veritabanına erişmek
* İş mantığını controller'a veya veri katmanına yazmak

## Gerçek hayattan örnekler

Klasik kurumsal web uygulamaları, Spring ve ASP.NET tabanlı çoğu iş uygulaması.

## İlişkili desenler

* [Onion Architecture](onion.md): Bağımlılığı tersine çeviren evrim
* [Clean Architecture](clean.md): Kurumsal büyük uygulamalar için gelişmiş sürüm
* [Vertical Slice Architecture](vertical-slice.md): Katman yerine özellik bazlı dilimleme
