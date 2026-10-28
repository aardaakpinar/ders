# Yazılım mimarisi

Bu doküman, yazılım mimarisinde sık kullanılan 44 desen ve mimari stili sade bir dille anlatır. Her desenin kendi klasörü vardır. Klasörün içindeki `README.md` dosyasında şu başlıklar yer alır:

* **Problem**
* **Çözüm ve çalışma mantığı**
* **Yapı**
* **Kod örneği**
* **Ne zaman kullanılır**
* **Ne zaman kullanılmamalı**
* **Artılar**
* **Eksiler**
* **Sık yapılan hatalar**
* **Gerçek hayattan örnekler**
* **İlişkili desenler**

Kod örnekleri TypeScript ve JavaScript ile yazılmıştır. Tip sözleşmesinin önemli olduğu yerlerde TypeScript, dinamik ve kısa örneklerde JavaScript kullanılmıştır.

{% hint style="info" %}
Kod örnekleri varsayılan olarak gizlidir. Açmak için **“Kodu göster”** başlığına tıklayın. Örnekler fikri göstermek için sadeleştirilmiştir; üretim koduna olduğu gibi kopyalanmamalıdır.
{% endhint %}

***

## 0. Başlamadan Önce

### Mimari nedir?

Mimari, bir sistemin önemli kararlarının toplamıdır: Hangi bileşenler var, birbirleriyle nasıl konuşur, bağımlılıklar hangi yöne akar, veri nerede tutulur.

### Mimari stil, mimari desen ve tasarım deseni

| Kavram         | Ölçek                          | Örnek                         |
| -------------- | ------------------------------ | ----------------------------- |
| Mimari stil    | Tüm sistem veya büyük parçalar | Microservices, Layered, Clean |
| Mimari desen   | Belirli bir yapısal problem    | CQRS, Saga, API Gateway       |
| Tasarım deseni | Sınıf ve kod seviyesi          | Repository, Mediator, DI      |

### Temel ilkeler

1. Her desenin bir bedeli vardır. Bedelini bilmeden desen seçme.
2. Önce problemi tanımla, sonra deseni seç.
3. Gereksiz karmaşıklık, en pahalı teknik borçtur.
4. Bağımlılık yönü ve değişim nedenleri, mimarinin ana eksenidir.
5. Desenler birlikte kullanılır. Örneğin Clean Architecture, CQRS, Mediator ve Repository bir arada çalışır.

## İçindekiler

<table data-search="false"><thead><tr><th>Klasör</th><th>İçerik</th></tr></thead><tbody><tr><td><a href="ui-katmani-desenleri/nedir.md">UI Katmanı Desenleri</a></td><td>MVC, MVP, MVVM, MVI, Flux / Redux, Component-Based</td></tr><tr><td><a href="uygulama-ici-mimari-stiller/nedir.md">Uygulama İçi Mimari Stiller</a></td><td>Layered, Onion, Hexagonal, Clean, Vertical Slice, Microkernel, Pipe and Filter, Transaction Script / CRUD</td></tr><tr><td><a href="sistem-ve-dagitim-mimarileri/nedir.md">Sistem ve Dağıtım Mimarileri</a></td><td>Monolith, Modular Monolith, SOA, Microservices, Serverless, EDA, Client-Server, P2P, Space-Based</td></tr><tr><td><a href="veri-ve-alan-modelleme/nedir.md">Veri ve Alan Modelleme</a></td><td>CQRS, Event Sourcing, DDD, Bounded Context, Anti-Corruption Layer, Aggregate / Entity / Value Object / Domain Event</td></tr><tr><td><a href="veri-erisimi-desenleri/nedir.md">Veri Erişimi Desenleri</a></td><td>Repository, Unit of Work, Specification, Data Mapper, Active Record, DTO</td></tr><tr><td><a href="kod-seviyesi-ve-altyapi-desenleri/nedir.md">Kod Seviyesi ve Altyapı Desenleri</a></td><td>Dependency Injection / IoC, Mediator</td></tr><tr><td><a href="mikroservis-ve-dagitik-sistem-desenleri/nedir.md">Mikroservis ve Dağıtık Sistem Desenleri</a></td><td>API Gateway, BFF, Saga, Outbox, Circuit Breaker, Strangler Fig, Sidecar / Service Mesh</td></tr><tr><td><a href="karsilastirma-ve-karar.md">Karşılaştırma ve Karar Rehberi</a></td><td>Karşılaştırma tabloları, karar akış şeması, sık yapılan hatalar, kontrol listesi</td></tr></tbody></table>

Önerilen okuma sırası: önce bu sayfadaki **“Başlamadan Önce”** bölümü, ardından ihtiyacınıza göre bölümler. Yeni başlayanlar için 1, 2 ve 3. bölümler ile 8. bölümdeki karar rehberi iyi bir başlangıçtır.
