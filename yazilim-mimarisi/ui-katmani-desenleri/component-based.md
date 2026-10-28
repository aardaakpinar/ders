# Component Based

> Arayüz, yeniden kullanılabilir ve kendi içinde tamamlanmış bileşenlere bölünür. Bileşenler girdi olarak `props` alır, olay olarak geri bildirim yapar.

## Problem

Büyük bir arayüzü tek parça yazmak, aynı görünümün farklı yerlerde tekrar yazılmasına ve bir değişikliğin her yerde ayrı ayrı yapılmasına yol açar.

## Çözüm ve çalışma mantığı

Her bileşen kendi görünümünü, stilini ve yerel durumunu taşır. Bileşenler ağaç yapısında iç içe geçer.

{% stepper %}
{% step %}
## Arayüzü bölün

Arayüz küçük, tek sorumluluklu bileşenlere bölünür (Button, Price, ProductCard).
{% endstep %}

{% step %}
## Veriyi aktarın

Veri yukarıdan aşağıya `props` ile akar.
{% endstep %}

{% step %}
## Olayları bildirin

Olaylar aşağıdan yukarıya geri çağrılarla (callback) bildirilir.
{% endstep %}

{% step %}
## Ortak durumu yönetin

Ortak durum ortak üst bileşende veya bir durum yönetim aracında tutulur.
{% endstep %}
{% endstepper %}

İyi bileşenler "sunum" (nasıl görünür) ve "kapsayıcı" (veriyi nereden alır) sorumluluklarını ayırır.

## Kod örneği

Kod örnekleri varsayılan olarak gizlidir. Görmek için başlığa tıklayın.

<details>

<summary>Kodu göster: TypeScript (React)</summary>

```tsx
type PriceProps = { value: number };
const Price = ({ value }: PriceProps) => <span>{value.toFixed(2)} TL</span>;

type CardProps = { name: string; price: number; onAdd: () => void };
const ProductCard = ({ name, price, onAdd }: CardProps) => (
  <div className="card">
    <h3>{name}</h3>
    <Price value={price} />
    <button onClick={onAdd}>Sepete ekle</button>
  </div>
);
```

</details>

## Ne zaman kullanılır

* Neredeyse tüm modern web arayüzleri
* Tasarım sistemi veya bileşen kütüphanesi oluştururken

## Ne zaman kullanılmamalı

* Tamamen statik, birkaç sayfalık sitelerde (düz HTML yeterli olabilir)

## Artılar

* Yeniden kullanım
* Bileşenler izole test edilir
* Ekipler bileşen bazında paralel çalışır

## Eksiler

* Bileşen sınırları yanlış çizilirse prop taşıma zinciri oluşur
* Çok küçük veya çok büyük bileşenler bakımı zorlaştırır

## Sık yapılan hatalar

* Tek bileşende hem veri çekme hem görünüm hem iş kuralı toplamak
* Gereksiz yere her şeyi global durumda tutmak

## Gerçek hayattan örnekler

React, Angular, Vue, Svelte, Web Components.

## İlişkili desenler

* [Flux / Redux](flux-redux.md): Ortak durum yönetimi için
* [MVVM (Model-View-ViewModel)](mvvm.md): Bileşen çerçevelerinin altındaki bağlama fikri
