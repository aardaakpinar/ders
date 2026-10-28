---
description: >-
  Sıfırdan başlayıp Linux üzerinde x86-64 assembly yazmayı öğreten, uygulamalı
  ve Türkçe bir ders. Her bölümde açıklama, çalıştırılabilir kod ve alıştırmalar
  var. Kolaydan zora doğru ilerler.
---

# Türkçe x64 Assembly Dersi (Linux / NASM)

### Bu ders kimler için?

* Programlamayı (C, Python vb.) az da olsa bilenler
* Bilgisayarın alt katmanını, derleyicinin ne ürettiğini merak edenler
* Tersine mühendislik, güvenlik veya işletim sistemi geliştirmeye hazırlananlar

Assembly bilmek zorunda değilsin ama temel programlama kavramlarını (değişken, döngü, fonksiyon) bilmen işini çok kolaylaştırır.

### Gereksinimler

* Linux (Ubuntu, Debian, Fedora, Arch vb.), **x86-64** işlemci
* Windows'ta: WSL2 (Ubuntu) kullanabilirsin
* Apple Silicon (M1/M2/...) Mac'te: Docker ile `--platform linux/amd64` veya bir x86-64 Linux sanal makine/sunucu
* Terminal bilgisi (cd, ls, çalıştırma) yeterli

### Nasıl ilerlemeli?

1. Bölümleri sırayla oku. Her bölüm öncekine dayanır.
2. **Kodları kopyalama, elle yaz.** Hata yapmak öğrenmenin parçası.
3. Her bölümün sonundaki alıştırmaları yap.
4. Takıldığında GDB kullan ([Bölüm 7](baslangic/gdb-ile-hata-ayiklama.md)). Programı adım adım izlemek en iyi öğretmen.

**Ders akışı:** Önce register'ları ve temel komutları (`mov`, `add`, `mul`...) öğrenip sonuçları çıkış kodu ile (`echo $?`) göreceksin. Sonra sistem çağrılarını öğrenecek (Bölüm 8) ve ancak ardından ekrana yazı yazdıracaksın (Bölüm 9: Merhaba Dünya).
