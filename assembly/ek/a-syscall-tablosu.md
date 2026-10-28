---
description: Linux x86-64 sık kullanılan sistem çağrıları
---

# A: Syscall tablosu

| Numara | Ad            | Açıklama                |
| ------ | ------------- | ----------------------- |
| 0      | `read`        | fd'den oku              |
| 1      | `write`       | fd'ye yaz               |
| 2      | `open`        | dosya aç                |
| 3      | `close`       | fd'yi kapat             |
| 4      | `stat`        | dosya bilgisi           |
| 8      | `lseek`       | dosya konumunu değiştir |
| 9      | `mmap`        | bellek eşle / ayır      |
| 11     | `munmap`      | belleği bırak           |
| 12     | `brk`         | heap sınırını değiştir  |
| 24     | `sched_yield` | işlemciyi bırak         |
| 35     | `nanosleep`   | belirli süre uyu        |
| 39     | `getpid`      | süreç kimliği           |
| 41     | `socket`      | soket oluştur           |
| 42     | `connect`     | bağlan                  |
| 43     | `accept`      | bağlantı kabul et       |
| 49     | `bind`        | adrese bağla            |
| 50     | `listen`      | dinlemeye başla         |
| 57     | `fork`        | süreci çatalla          |
| 59     | `execve`      | program çalıştır        |
| 60     | `exit`        | süreci sonlandır        |
| 61     | `wait4`       | çocuk sürecini bekle    |
| 62     | `kill`        | sinyal gönder           |
| 79     | `getcwd`      | çalışma dizini          |
| 83     | `mkdir`       | dizin oluştur           |
| 87     | `unlink`      | dosya sil               |
| 318    | `getrandom`   | rastgele bayt           |

Tam liste için: [`man 2 syscalls`](https://man7.org/linux/man-pages/man2/syscalls.2.html) veya sisteminde `/usr/include/asm/unistd_64.h`.

Her syscall'ın ayrıntıları için `man 2 write`, `man 2 open` gibi kılavuz sayfalarına bak.

