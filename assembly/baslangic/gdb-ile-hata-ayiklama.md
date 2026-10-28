# GDB ile hata ayıklama

Assembly'de en değerli beceri: **programı komut komut izleyip register'ları ve belleği görmek.** Bunu erken öğrenmek seni çok hızlandırır.

#### Hata ayıklama bilgisiyle derle

```
nasm -f elf64 -g -F dwarf program.asm -o program.o
ld program.o -o program
```

`-g -F dwarf` GDB'ye kaynak satırı bilgisi ekler.

#### GDB'yi Intel sözdizimine ayarla

GDB varsayılan olarak AT\&T gösterir. Kalıcı olarak Intel yapmak için:

```
echo "set disassembly-flavor intel" >> ~/.gdbinit
```

#### Temel komutlar

```
gdb ./program
```

<table data-search="false"><thead><tr><th>Komut</th><th>Kısaltma</th><th>Ne yapar</th></tr></thead><tbody><tr><td><code>break _start</code></td><td><code>b _start</code></td><td><code>_start</code>'a durak noktası koy</td></tr><tr><td><code>break program.asm:12</code></td><td></td><td>12. satıra durak koy</td></tr><tr><td><code>run</code></td><td><code>r</code></td><td>Programı çalıştır</td></tr><tr><td><code>starti</code></td><td></td><td>İlk komutta dur</td></tr><tr><td><code>stepi</code></td><td><code>si</code></td><td><strong>Tek komut</strong> ilerle (<code>call</code>'un içine girer)</td></tr><tr><td><code>nexti</code></td><td><code>ni</code></td><td>Tek komut ilerle (<code>call</code>'u atlar)</td></tr><tr><td><code>continue</code></td><td><code>c</code></td><td>Sonraki durak noktasına kadar çalış</td></tr><tr><td><code>info registers</code></td><td><code>i r</code></td><td>Tüm register'ları göster</td></tr><tr><td><code>info registers rax</code></td><td></td><td>Sadece <code>rax</code></td></tr><tr><td><code>print $rax</code></td><td><code>p $rax</code></td><td><code>rax</code>'ı onluk göster</td></tr><tr><td><code>print/x $rax</code></td><td><code>p/x $rax</code></td><td>Hex göster</td></tr><tr><td><code>print/t $al</code></td><td></td><td>İkilik göster</td></tr><tr><td><code>x/8xb &#x26;mesaj</code></td><td></td><td>Bellekten 8 bayt, hex</td></tr><tr><td><code>x/4xg $rsp</code></td><td></td><td>Stack'ten 4 quadword, hex</td></tr><tr><td><code>x/s &#x26;mesaj</code></td><td></td><td>String olarak göster</td></tr><tr><td><code>x/5i $rip</code></td><td></td><td>Sıradaki 5 komutu göster</td></tr><tr><td><code>disassemble</code></td><td><code>disas</code></td><td>Geçerli bölgeyi göster</td></tr><tr><td><code>layout asm</code></td><td></td><td>Üstte kaynak/asm penceresi (TUI)</td></tr><tr><td><code>layout regs</code></td><td></td><td>Register penceresi</td></tr><tr><td><code>quit</code></td><td><code>q</code></td><td>Çık</td></tr></tbody></table>

`x` komutunun biçimi: `x/<adet><biçim><boyut> <adres>`

* biçim: `x` hex, `d` onluk, `u` işaretsiz, `t` ikilik, `c` karakter, `s` string, `i` komut
* boyut: `b` bayt, `h` 2 bayt, `w` 4 bayt, `g` 8 bayt

#### Uygulamalı oturum

Şu programı alalım (`gdb_deneme.asm`):

```
section .data
    sayi dd 0x12345678

section .text
    global _start

_start:
    mov eax, [sayi]
    mov rbx, 10
    add rax, rbx
    mov rdi, 0
    mov rax, 60
    syscall
```

GDB oturumu:

```
$ gdb ./gdb_deneme
(gdb) break _start
(gdb) run
(gdb) x/4xb &sayi            # little-endian'ı gör: 0x78 0x56 0x34 0x12
(gdb) stepi
(gdb) p/x $rax               # 0x12345678
(gdb) stepi
(gdb) stepi
(gdb) p/x $rax               # 0x12345682
(gdb) info registers rflags  # bayrakları gör
```

#### Faydalı püf noktaları

* `layout regs` ile sürekli görünen register penceresi aç. `si` ile adım at, değişen register'ları izle.
* Ekran bozulursa `Ctrl+L` ile yenile.
* **GEF** veya **pwndbg** gibi eklentiler çok güzel bir arayüz sunar (isteğe bağlı).
* Bölüm 9'da `strace` ile programın sistem çağrılarını izlemeyi de göreceksin.

#### Alıştırmalar

1. Bölüm 6'daki `(a + b) * c - d` programını GDB'de adım adım izle. Her adımda `rax` ne oluyor?
2. Bölüm 5'teki toplama programında `x/gd &sayi1` ile bellekteki değeri oku.
3. `stepi` ile ilerleyip `syscall` komutuna gel. Tam o noktada `info registers rax rdi` ile hangi sistem çağrısının, hangi çıkış koduyla yapılacağını gör.
