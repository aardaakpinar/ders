# Nedir?

Bu bölümdeki desenler, iş alanını kodda nasıl modelleyeceğinizi ve verinin okuma ile yazma yollarını nasıl düzenleyeceğinizi anlatır.

Her desenin kendi klasörü vardır. Klasörün içindeki `README.md` dosyasında problem, çözüm, yapı, gizli (açılır) kod örneği, kullanım ve kaçınma durumları, artılar, eksiler, sık yapılan hatalar ve ilişkili desenler yer alır.

| Desen                                                                                          | Özet                                                                                                        |
| ---------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------- |
| [CQRS](cqrs.md)                                                                                | Yazma (Command) ve okuma (Query) modelleri birbirinden ayrılır.                                             |
| [Event Sourcing](event-sourcing.md)                                                            | Durumun son hali yerine, onu oluşturan olayların tamamı saklanır.                                           |
| [DDD (Domain-Driven Design)](ddd.md)                                                           | Yazılımın merkezine iş alanı (domain) konur.                                                                |
| [Bounded Context ve Context Mapping](bounded-context-and-context-mapping.md)                   | Bounded Context, belirli bir modelin ve dilin geçerli olduğu sınırdır.                                      |
| [Anti-Corruption Layer](anti-corruption-layer.md)                                              | Eski veya dış sistemin modelinin kendi domain'inizi bozmaması için araya konan çeviri katmanıdır.           |
| [Aggregate, Entity, Value Object, Domain Event](aggregate-entity-value-object-domain-event.md) | Bu dört yapı taşı, domain modelini kurmak için kullanılır: Entity, Value Object, Aggregate ve Domain Event. |
