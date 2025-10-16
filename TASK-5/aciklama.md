ZEHRA ÖZDEMİR 
250541082

1-BAŞLA: Sistemi KAPALI durumdan KURULU duruma geçir.

2-DÖNGÜ SÜREKLİ: Sistem KURULU ise (Aşama 3'e geç), değilse (Sistem KAPALI, döngü tekrar et).

3-SENSÖR OKU: Hareket ve Kapı/Pencere sensörlerinden veri al.

4-İHLAL KONTROLÜ: Okunan veriler önceden belirlenmiş eşikleri (Eşik_Hareket, Eşik_Kapı_Açık) aşıyor mu?

5-ALARM AKTİVASYONU: Eşiği aşan bir durum varsa, alarmı etkinleştir (Alarm_Aktif = DOĞRU).

6-BİLDİRİM GÖNDER: Alarm aktif ise ve kullanıcı daha önce bilgilendirilmediyse, kullanıcıya uyarı mesajı gönder.

7-DÖNGÜ TEKRAR: Bir sonraki kontrole geçmek için bekle (1 sn) ve döngüyü tekrarla.
