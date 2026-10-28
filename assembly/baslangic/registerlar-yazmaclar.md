# Registerlar (Yazmaçlar)

Register, işlemcinin içindeki çok hızlı küçük bellek hücresidir. İşlemci bellekle doğrudan çok işlem yapmaz. Veriyi register'a alır, orada işler, sonucu geri yazar. x64'te register'lar 64 bit genişliğindedir.

#### Genel amaçlı registerlar

16 adet var:

`rax, rbx, rcx, rdx, rsi, rdi, rbp, rsp, r8, r9, r10, r11, r12, r13, r14, r15`

#### Alt registerlar (parçalar)

Her 64 bit register'ın alt parçalarına ayrı isimle erişebilirsin:

<table data-search="false"><thead><tr><th>64 bit</th><th>32 bit</th><th>16 bit</th><th>8 bit (alt)</th><th>8 bit (üst)</th></tr></thead><tbody><tr><td><code>rax</code></td><td><code>eax</code></td><td><code>ax</code></td><td><code>al</code></td><td><code>ah</code></td></tr><tr><td><code>rbx</code></td><td><code>ebx</code></td><td><code>bx</code></td><td><code>bl</code></td><td><code>bh</code></td></tr><tr><td><code>rcx</code></td><td><code>ecx</code></td><td><code>cx</code></td><td><code>cl</code></td><td><code>ch</code></td></tr><tr><td><code>rdx</code></td><td><code>edx</code></td><td><code>dx</code></td><td><code>dl</code></td><td><code>dh</code></td></tr><tr><td><code>rsi</code></td><td><code>esi</code></td><td><code>si</code></td><td><code>sil</code></td><td>–</td></tr><tr><td><code>rdi</code></td><td><code>edi</code></td><td><code>di</code></td><td><code>dil</code></td><td>–</td></tr><tr><td><code>rbp</code></td><td><code>ebp</code></td><td><code>bp</code></td><td><code>bpl</code></td><td>–</td></tr><tr><td><code>rsp</code></td><td><code>esp</code></td><td><code>sp</code></td><td><code>spl</code></td><td>–</td></tr><tr><td><code>r8</code></td><td><code>r8d</code></td><td><code>r8w</code></td><td><code>r8b</code></td><td>–</td></tr><tr><td>...</td><td>...</td><td>...</td><td>...</td><td>–</td></tr><tr><td><code>r15</code></td><td><code>r15d</code></td><td><code>r15w</code></td><td><code>r15b</code></td><td>–</td></tr></tbody></table>

Görsel olarak `rax`:

```
 63                              32 31             16 15      8 7       0
┌──────────────────────────────────┬─────────────────┬─────────┬─────────┐
│                                  │                 │   ah    │   al    │
└──────────────────────────────────┴─────────────────┴─────────┴─────────┘
                                   │<-------- eax ----------------------->│
                                                     │<------- ax -------->│
│<---------------------------------- rax ---------------------------------->│
```

#### Çok önemli kural: 32 bit yazma üst yarıyı sıfırlar

```nasm
mov rax, 0xFFFFFFFFFFFFFFFF
mov eax, 5            ; 32 bit yazma → rax = 0x0000000000000005 (üst 32 bit SIFIRLANIR)
```

Ama 8 ve 16 bit yazmalar üst kısma **dokunmaz**:

```nasm
mov rax, 0xFFFFFFFFFFFFFFFF
mov al, 5             ; rax = 0xFFFFFFFFFFFFFF05
mov ax, 5             ; rax = 0xFFFFFFFFFFFF0005
```

Bu yüzden derleyiciler `xor eax, eax` (2 bayt) komutunu `xor rax, rax` yerine tercih eder. Sonuç aynı, makine kodu daha kısa.

#### Registerların geleneksel rolleri

Teknik olarak çoğu register istediğin gibi kullanılabilir ama bazı komutlar ve kurallar belirli register'ları bekler:

<table data-search="false"><thead><tr><th>Register</th><th>Rol</th></tr></thead><tbody><tr><td><code>rax</code></td><td>Dönüş değeri, syscall numarası, <code>mul</code>/<code>div</code> için operand</td></tr><tr><td><code>rcx</code></td><td>Sayaç (kaydırma <code>cl</code>, <code>rep</code> önekleri, <code>loop</code>)</td></tr><tr><td><code>rdx</code></td><td><code>mul</code>/<code>div</code> için üst yarı, syscall 3. argüman</td></tr><tr><td><code>rsi</code> / <code>rdi</code></td><td>String komutlarında kaynak / hedef, fonksiyon argümanları</td></tr><tr><td><code>rsp</code></td><td><strong>Stack pointer</strong>: stack'in tepesini gösterir (Bölüm 13)</td></tr><tr><td><code>rbp</code></td><td>Base pointer: stack frame tabanı (Bölüm 14)</td></tr><tr><td><code>rip</code></td><td><strong>Instruction pointer</strong>: çalıştırılacak sonraki komutun adresi. Doğrudan <code>mov</code> ile yazılamaz</td></tr></tbody></table>

#### RFLAGS: bayrak register'ı

İşlem sonuçları hakkında bilgi tutan bit'lerdir. Koşullu atlamalar bunlara bakar (Bölüm 9'da ayrıntılı).

| Bayrak | Ad        | Ne zaman 1 olur?                         |
| ------ | --------- | ---------------------------------------- |
| `ZF`   | Zero      | Sonuç sıfır                              |
| `SF`   | Sign      | Sonuç negatif (en üst bit 1)             |
| `CF`   | Carry     | İşaretsiz taşma (elde)                   |
| `OF`   | Overflow  | İşaretli taşma                           |
| `PF`   | Parity    | Sonucun alt baytında 1'lerin sayısı çift |
| `DF`   | Direction | String komutları yönü (0 = ileri)        |

#### Diğer registerlar (ileride)

* **xmm0–xmm15:** Kayan noktalı sayı ve SIMD için 128 bit (Bölüm 18)
* **Segment register'ları:** `cs`, `ds`, `fs`, `gs` vb. 64 bit modunda büyük ölçüde etkisiz (`fs`/`gs` iş parçacığı verisi için kullanılır)

#### Alıştırmalar

1. `mov rax, -1` sonrası `mov ax, 0` yapılırsa `rax` ne olur?
2. Aynı başlangıçla `mov eax, 0` yapılırsa `rax` ne olur?
3. `r9`'un alt 8 bit parçasının adı nedir?

<details>

<summary>Cevaplar</summary>

1. `0xFFFFFFFFFFFF0000`
2. `0x0000000000000000`
3. `r9b`

</details>

