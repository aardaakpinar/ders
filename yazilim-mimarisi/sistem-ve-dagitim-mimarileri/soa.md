# SOA

> İş fonksiyonları yeniden kullanılabilir servisler olarak sunulur. Servisler genellikle merkezi bir ESB üzerinden haberleşir.

## Problem

Kurumda birçok bağımsız sistem aynı iş fonksiyonlarına (müşteri sorgulama, fatura) ihtiyaç duyar ve her biri bunu kendi içinde tekrar yazar.

## Çözüm ve çalışma mantığı

* **Servis:** Belirli bir iş yeteneğini açık bir sözleşmeyle sunar (SOAP/WSDL veya REST).
* **ESB (Enterprise Service Bus):** Yönlendirme, dönüşüm ve protokol çevirisi yapan merkezi aracı.
* **Servis kayıt defteri:** Servislerin keşfedilmesini sağlar.

{% stepper %}
{% step %}
## Servisler sözleşmeleriyle yayınlanır
{% endstep %}

{% step %}
## İstemci servisi ESB aracılığıyla çağırır
{% endstep %}

{% step %}
## ESB mesajı yönlendirir, gerekirse biçimini dönüştürür
{% endstep %}

{% step %}
## Servis yanıt verir
{% endstep %}
{% endstepper %}

SOA kurumsal yeniden kullanıma, mikroservisler ise bağımsız dağıtıma odaklanır.

## Yapı

```
Servis A --+
Servis B --+--> ESB <-- Servis D
Servis C --+
```

## Kod örneği

Kod örnekleri varsayılan olarak gizlidir. Görmek için başlığa tıklayın.

<details>

<summary>Kodu göster: TypeScript</summary>

```ts
type Handler = (payload: unknown) => Promise<unknown>;

class ServiceBus {
  private services = new Map<string, Handler>();
  register(name: string, handler: Handler) { this.services.set(name, handler); }
  async call(name: string, payload: unknown) {
    const handler = this.services.get(name);
    if (!handler) throw new Error(`Servis yok: ${name}`);
    return handler(payload);
  }
}

bus.register("customer.get", async (p) => customerService.get(p as string));
const customer = await bus.call("customer.get", "42");
```

</details>

## Ne zaman kullanılır

* Farklı teknolojilerdeki büyük kurumsal sistemlerin entegrasyonu
* Ortak iş servislerinin çok sayıda uygulama tarafından kullanılması

## Ne zaman kullanılmamalı

* Yeni, bağımsız ürünlerde (mikroservis veya modüler monolit daha hafiftir)

## Artılar

* Kurumsal ölçekte yeniden kullanım
* Heterojen sistemlerin entegrasyonu
* Standartlaştırılmış sözleşmeler

## Eksiler

* ESB tek hata noktası ve darboğaz olabilir
* Ağır standartlar ve yönetişim
* Merkezi yapı ekip özerkliğini azaltır

## Sık yapılan hatalar

* ESB'nin içine iş mantığı koymak
* Servisleri çok büyük ve genel tasarlamak

## Gerçek hayattan örnekler

Bankacılık ve telekom sektöründe yaygın. Örnek ürünler: MuleSoft, IBM Integration Bus, WSO2.

## İlişkili desenler

* [Microservices](microservices.md): Daha hafif ve bağımsız evrim
* [EDA (Event-Driven Architecture)](eda.md): Olay tabanlı entegrasyon
