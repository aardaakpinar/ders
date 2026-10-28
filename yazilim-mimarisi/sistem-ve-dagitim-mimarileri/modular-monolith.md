# Modular Monolith

> Tek dağıtım birimidir ama içi net sınırlı modüllere bölünmüştür. Modüller yalnızca açık arayüzlerle konuşur.

## Problem

Sınırsız monolit zamanla karışır. Mikroservis ise erken aşamada gereksiz operasyon ve dağıtık sistem karmaşıklığı getirir.

## Çözüm ve çalışma mantığı

Her modül bir iş yeteneğine karşılık gelir (Sipariş, Stok, Ödeme). Her modülün bir **genel API'si** (public API) ve gizli **iç kısmı** vardır.

{% stepper %}
{% step %}
## Modül sınırlarını iş alanına göre çizin
{% endstep %}

{% step %}
## Modüllerin yalnızca birbirinin genel API'sini çağırmasını sağlayın
{% endstep %}

{% step %}
## Her modülün kendi veri şemasına sahip olmasını sağlayın

Modüller arası doğrudan tablo erişimi yapılmaz.
{% endstep %}

{% step %}
## Modüller arası iletişimi arayüz veya olay ile yapın
{% endstep %}

{% step %}
## Sınır ihlallerini araçlarla denetleyin

Mimari testler ve import kuralları kullanın.
{% endstep %}
{% endstepper %}

Sınırlar net olduğu için ileride bir modülü ayrı servise çıkarmak kolaydır.

## Yapı

```
+---------------- Tek Uygulama ----------------+
| [Sipariş]   [Stok]   [Ödeme]   [Kullanıcı]   |
|    yalnızca public API üzerinden konuşurlar  |
+----------------------------------------------+
```

## Kod örneği

Kod örnekleri varsayılan olarak gizlidir. Görmek için başlığa tıklayın.

<details>

<summary>Kodu göster: TypeScript</summary>

```ts
// modules/stock/index.ts  -> modülün tek giriş kapısı
export interface StockApi {
  reserve(productId: string, qty: number): Promise<boolean>;
}
export { createStockModule } from "./internal/stockModule";
// internal/ klasörü dışarıdan import edilmez

// modules/orders/placeOrder.ts
import type { StockApi } from "../stock";

export async function placeOrder(stock: StockApi, productId: string, qty: number) {
  const ok = await stock.reserve(productId, qty);
  if (!ok) throw new Error("Stok yetersiz");
}
```

</details>

## Ne zaman kullanılır

* Büyüyen ekip ve genişleyen kod tabanı
* Mikroservise hazırlık aşamasında
* Operasyonel karmaşıklık istenmiyorsa

## Ne zaman kullanılmamalı

* Çok küçük ve basit uygulamalarda
* Modüllerin gerçekten bağımsız ölçeklenmesi gerekiyorsa

## Artılar

* Monolitin sadeliği ve net sınırlar bir arada
* Mikroservise geçiş yolu açık
* Tek transaction ve tek dağıtım

## Eksiler

* Sınırları korumak disiplin ve araç gerektirir
* Hâlâ tek dağıtım birimi, tüm uygulama birlikte ölçeklenir

## Sık yapılan hatalar

* Modüller arası ortak veritabanı tablolarına erişmek
* Sınırı sadece klasörle belirleyip kuralı denetlememek

## Gerçek hayattan örnekler

Shopify'ın modüler monolit yaklaşımı ve birçok kurumsal .NET ve Java projesi.

## İlişkili desenler

* [Monolithic Architecture](monolith.md): Başlangıç noktası
* [Microservices](microservices.md): Gerekirse gidilecek hedef
* [Bounded Context ve Context Mapping](../veri-ve-alan-modelleme/bounded-context-and-context-mapping.md): Sınırları belirlemek için
