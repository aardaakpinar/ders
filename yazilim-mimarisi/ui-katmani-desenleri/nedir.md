# Nedir?

Bu bölümdeki desenler, arayüz ile iş mantığı arasındaki sorumlulukları ayırır ve arayüz durumunun nasıl yönetileceğini belirler.

Her desenin kendi klasörü vardır. Klasörün içindeki `README.md` dosyasında problem, çözüm, yapı, gizli (açılır) kod örneği, kullanım ve kaçınma durumları, artılar, eksiler, sık yapılan hatalar ve ilişkili desenler yer alır.

| Desen                                              | Özet                                                                                       |
| -------------------------------------------------- | ------------------------------------------------------------------------------------------ |
| [MVC (Model-View-Controller)](mvc.md)              | Kullanıcı girişini Controller karşılar, iş mantığını Model üstlenir, sonucu View gösterir. |
| [MVP (Model-View-Presenter)](mvp.md)               | View pasif tutulur, tüm sunum mantığı Presenter'a taşınır.                                 |
| [MVVM (Model-View-ViewModel)](mvvm.md)             | View, ViewModel'e veri bağlama ile bağlanır.                                               |
| [MVI (Model-View-Intent)](mvi.md)                  | Kullanıcı niyeti (Intent) bir durum (State) üretir, View yalnızca bu durumu çizer.         |
| [Flux / Redux](flux-redux.md)                      | Uygulamanın tek bir global durumu (store) vardır.                                          |
| [Component-Based Architecture](component-based.md) | Arayüz, yeniden kullanılabilir ve kendi içinde tamamlanmış bileşenlere bölünür.            |
