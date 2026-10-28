# Pipe and Filter

> Veri, art arda dizilmiş dönüşüm adımlarından (filtre) geçer. Her adımın çıktısı bir sonrakinin girdisidir.

## Problem

Karmaşık bir veri işleme akışı tek bir büyük fonksiyonda yazıldığında yeniden kullanılamaz, sıralaması değiştirilemez ve parça parça test edilemez.

## Çözüm ve çalışma mantığı

* **Filtre:** Tek bir iş yapan bağımsız adım.
* **Boru (pipe):** Filtreler arasındaki veri akışı.

{% stepper %}
{% step %}
## Kaynak veriyi üretir
{% endstep %}

{% step %}
## İlk filtre veriyi dönüştürüp çıktıyı sonraki adıma verir
{% endstep %}

{% step %}
## Zincir sonuna kadar devam eder
{% endstep %}

{% step %}
## Hedef nihai çıktıyı alır
{% endstep %}
{% endstepper %}

Filtreler birbirinden habersiz ve aynı arayüzdedir; bu nedenle sırası değiştirilebilir, biri eklenip çıkarılabilir.

## Yapı

```
Kaynak --> [Filtre 1] --> [Filtre 2] --> [Filtre 3] --> Hedef
```

## Kod örneği

Kod örnekleri varsayılan olarak gizlidir. Görmek için başlığa tıklayın.

<details>

<summary>Kodu göster: JavaScript</summary>

```js
const trim = (s) => s.trim();
const lower = (s) => s.toLowerCase();
const slugify = (s) => s.replace(/\s+/g, "-");

const pipe = (...fns) => (input) => fns.reduce((acc, fn) => fn(acc), input);

const toSlug = pipe(trim, lower, slugify);
toSlug("  Yazılım Mimarisi  "); // "yazılım-mimarisi"
```

</details>

## Ne zaman kullanılır

* ETL ve veri işleme hatları
* HTTP middleware zincirleri
* Derleme, görsel işleme, metin işleme akışları

## Ne zaman kullanılmamalı

* Adımlar birbirinden çok fazla veri paylaşıyorsa veya akış dallanıyorsa

## Artılar

* Her adım tek iş yapar ve ayrı test edilir
* Yeniden kullanılabilir ve sıralanabilir
* Paralel çalıştırmaya uygundur

## Eksiler

* Adımlar arası veri formatı uyumu gerekir
* Hata yönetimi ve geri alma zor olabilir
* Çok uzun zincirler hata ayıklamayı güçleştirir

## Sık yapılan hatalar

* Filtreler arasında gizli paylaşılan durum kullanmak
* Bir filtreye birden fazla sorumluluk yüklemek

## Gerçek hayattan örnekler

Unix komut satırı boruları, Express middleware zinciri, Gulp, ETL araçları.

## İlişkili desenler

* [Microkernel (Plugin) Architecture](microkernel.md): Eklenti tabanlı benzer yapı
* [EDA (Event-Driven Architecture)](../sistem-ve-dagitim-mimarileri/eda.md): Olay akışlarında benzer işleme zincirleri
