# DTO

> Katmanlar veya servisler arasında veri taşıyan, davranışı olmayan basit nesnedir.

## Problem

Entity'yi doğrudan dışarı açarsak iç yapı ve hassas alanlar (parola özeti, iç kimlikler) sızar. Domain değiştiğinde API sözleşmesi de bozulur.

## Çözüm ve çalışma mantığı

DTO yalnızca veri taşır. İş mantığı içermez. Dönüştürme (mapping) sınırda yapılır:

{% stepper %}
{% step %}
## Domain nesnesi alınır
{% endstep %}

{% step %}
## Gerekli alanlar DTO'ya kopyalanır
{% endstep %}

{% step %}
## DTO dışarıya döner

API yanıtı olarak döner.
{% endstep %}

{% step %}
## Gelen istek DTO olarak alınır

Önce DTO alınır, doğrulanır ve domain nesnesine çevrilir.
{% endstep %}
{% endstepper %}

Her kullanım senaryosu için ayrı DTO tasarlamak (liste görünümü, detay görünümü) gereksiz veri taşımayı önler.

## Kod örneği

Kod örnekleri varsayılan olarak gizlidir. Görmek için başlığa tıklayın.

<details>

<summary>Kodu göster: TypeScript</summary>

```ts
class User {
  constructor(
    readonly id: string,
    readonly email: string,
    readonly passwordHash: string,   // dışarı çıkmamalı
    readonly createdAt: Date
  ) {}
}

type UserDto = { id: string; email: string };

const toDto = (u: User): UserDto => ({ id: u.id, email: u.email });

app.get("/users/:id", async (req, res) => {
  const user = await repo.getById(req.params.id);
  res.json(toDto(user!));   // Entity doğrudan döndürülmez
});
```

</details>

## Ne zaman kullanılır

* API giriş ve çıkış sözleşmeleri
* Katmanlar ve servisler arası veri aktarımı

## Ne zaman kullanılmamalı

* Katmanlar zaten aynı basit modeli paylaşıyorsa küçük projelerde dönüşüm yükü gereksiz olabilir

## Artılar

* İç yapıyı ve hassas alanları gizler
* API sözleşmesi domain'den bağımsız sürümlenir
* Gereksiz veri taşınmaz

## Eksiler

* Ek dönüşüm (mapping) kodu
* Çok sayıda benzer sınıf

## Sık yapılan hatalar

* Entity'yi doğrudan API'den döndürmek
* DTO'ya iş mantığı koymak
* Tek DTO'yu her senaryoda kullanmak

## Gerçek hayattan örnekler

REST API istek ve yanıt modelleri, GraphQL türleri, mesaj yükleri (payload).

## İlişkili desenler

* [Data Mapper](data-mapper.md): Domain ve veritabanı dönüşümü
* [CQRS](../veri-ve-alan-modelleme/cqrs.md): Okuma modeli sıklıkla DTO olarak döner
* [API Gateway](../mikroservis-ve-dagitik-sistem-desenleri/api-gateway.md): API sınırında kullanılır
