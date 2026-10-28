# Projeler

Öğrendiklerini pekiştirmek için projeler. Zorluk sırasına göre:

#### Kolay

1. **Selamlayıcı:** Adı sor, "Merhaba, X!" yaz. (Bölüm 9)
2. **Toplama makinesi:** İki tek haneli sayıyı klavyeden oku, toplamı yazdır. (`atoi` + `yazdir_sayi`)
3. **Geri sayım:** 10'dan 1'e kadar say, sonra "Basla!" yaz.
4. **Tek/çift:** Girilen sayı tek mi çift mi söylesin.

#### Orta

5. **Faktöriyel hesaplayıcı:** Klavyeden sayı al, faktöriyelini yazdır. (20!'in 64 bit'e sığdığını, 21!'in sığmadığını gözle.)
6. **Asal sayı testi:** Sayıyı 2'den karekökü civarına kadar bölerek test et.
7. **Palindrom kontrolü:** String ileri-geri aynı mı?
8. **Basit hesap makinesi:** `./hesap 12 + 30` gibi komut satırı argümanlarını işle.
9. **wc benzeri:** Dosyadaki satır sayısını say.
10. **Caesar şifreleme:** Metni belirli kaydırma ile şifrele.

#### Zor

11. **Mini `cat`/`hexdump`:** Dosyayı hex olarak yazdır.
12. **Basit shell:** Satır oku, `fork` + `execve` ile çalıştır. (Syscall'lar: 57, 59, 61)
13. **TCP echo sunucusu:** `socket`, `bind`, `listen`, `accept`, `read`, `write` (syscall 41, 49, 50, 43...). Saf assembly ile ağ programı!
14. **Sıralama algoritması:** Bubble sort, sonra quick sort.
15. **Oyun:** Terminalde "sayı tahmin" oyunu (rastgele sayı için `getrandom` syscall 318 veya `rdrand` komutu).

#### Sonraki adımlar

Bu dersi bitirdiysen temel x64 assembly'yi biliyorsun. Buradan sonra:

* **Tersine mühendislik:** Ghidra, radare2, IDA ile gerçek programları incele; CTF'lerde `crackme` ve `pwn` kategorileri.
* **Güvenlik:** Stack overflow, ROP (return-oriented programming), shellcode yazımı.
* **İşletim sistemi geliştirme:** 16 bit real mode → 32 bit protected mode → 64 bit long mode geçişi, bootloader yazmak.
* **Optimizasyon:** Intel/AMD işlemci mimarisi, pipeline, cache, AVX ile vektörleştirme.
* **Diğer mimariler:** ARM64 (Apple Silicon, Raspberry Pi) ve RISC-V, benzer mantık, farklı komutlar.

