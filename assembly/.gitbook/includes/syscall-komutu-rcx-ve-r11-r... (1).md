---
title: syscall komutu rcx ve r11 r...
---

{% hint style="info" %}
`syscall` komutu **`rcx` ve `r11` register'larını bozar** (çekirdek bunları kendi işi için kullanır). Bu register'lardaki değerlere sonra ihtiyacın varsa onları başka yerde sakla. Genelde `r12`–`r15` güvenli seçimdir.
{% endhint %}
