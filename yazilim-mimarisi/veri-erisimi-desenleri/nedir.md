# Nedir?

Bu bölümdeki desenler, domain katmanı ile veri depolama arasındaki ilişkiyi düzenler.

Her desenin kendi klasörü vardır. Klasörün içindeki `README.md` dosyasında problem, çözüm, yapı, gizli (açılır) kod örneği, kullanım ve kaçınma durumları, artılar, eksiler, sık yapılan hatalar ve ilişkili desenler yer alır.

| Desen                                     | Özet                                                                                          |
| ----------------------------------------- | --------------------------------------------------------------------------------------------- |
| [Repository Pattern](repository.md)       | Veri erişimini, domain'e bellek içi bir koleksiyon gibi görünen bir arayüzün arkasına saklar. |
| [Unit of Work Pattern](unit-of-work.md)   | Bir iş birimi içindeki tüm değişiklikleri tek transaction olarak toplar.                      |
| [Specification Pattern](specification.md) | Sorgu ve iş kuralı koşullarını yeniden kullanılabilir, birleştirilebilir nesnelere paketler.  |
| [Data Mapper](data-mapper.md)             | Domain nesneleri ile veritabanı arasındaki dönüşümü ayrı bir katman yapar.                    |
| [Active Record](active-record.md)         | Nesne hem veriyi hem de kendi kaydetme ve yükleme işlemlerini taşır.                          |
| [DTO (Data Transfer Object)](dto.md)      | Katmanlar veya servisler arasında veri taşıyan, davranışı olmayan basit nesnedir.             |
