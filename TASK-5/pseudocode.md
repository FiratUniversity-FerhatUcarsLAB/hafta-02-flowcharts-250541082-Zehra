// Sabitler ve Değişkenler
SABIT Durum_Kapalı = "KAPALI"
SABIT Durum_Kurulu = "KURULU"
SABIT Eşik_Hareket = 100         // Hareket sensörü hassasiyet eşiği (örneğin, bir değer)
SABIT Eşik_Kapı_Açık = 1         // Kapı/Pencere sensörü (0=Kapalı, 1=Açık)

DEĞİŞKEN Sistem_Durumu = Durum_Kapalı
DEĞİŞKEN Alarm_Aktif = YANLIŞ
DEĞİŞKEN Kullanıcı_Bildirildi = YANLIŞ

// FONKSİYONLAR
FONKSIYON Sesli_Alarm_Etkinleştir()
    YAZDIR "!!! GÜVENLİK İHLALİ ALGILANDI - SESLİ ALARM AKTİF !!!"
    Alarm_Aktif = DOĞRU
    DÖN

FONKSIYON Kullaniciya_Bildirim_Gonder(mesaj)
    YAZDIR "Kullanıcıya bildirim gönderiliyor: " + mesaj
    Kullanıcı_Bildirildi = DOĞRU
    DÖN

FONKSIYON Sensor_Verisi_Oku(sensor_adi)
    // Gerçek bir sistemde, bu, sensörden fiziksel okuma yapardı.
    // Simülasyon için rastgele bir değer döndürelim (veya belirli bir senaryoyu test edelim).
    EĞER sensor_adi EŞİTTİR "HAREKET" İSE
        GERİ DÖN RASTGELE_SAYI(50, 150) // Örn: 50 ile 150 arasında bir değer
    EĞER sensor_adi EŞİTTİR "KAPI" İSE
        GERİ DÖN RASTGELE_SAYI(0, 1)    // Örn: 0 veya 1
    GERİ DÖN 0
    DÖN

FONKSIYON Sistemi_Kur(yeni_durum)
    Sistem_Durumu = yeni_durum
    Alarm_Aktif = YANLIŞ
    Kullanıcı_Bildirildi = YANLIŞ
    YAZDIR "Sistem durumu: " + Sistem_Durumu
    DÖN

// ANA ALGORİTMA DÖNGÜSÜ
ANA_PROGRAM_BAŞLA

    Sistemi_Kur(Durum_Kapalı) // Başlangıçta kapalı
    
    // Kullanıcının sistemi kurduğunu varsayalım
    Sistemi_Kur(Durum_Kurulu)

    DÖNGÜ: SÜREKLİ_ÇALIŞ (Her saniye)

        EĞER Sistem_Durumu EŞİTTİR Durum_Kurulu İSE

            // 1. Hareket Sensörü Kontrolü
            hareket_değeri = Sensor_Verisi_Oku("HAREKET")
            
            EĞER hareket_değeri BÜYÜKTÜR Eşik_Hareket İSE
                YAZDIR "Hareket Algılandı: " + hareket_değeri
                Sesli_Alarm_Etkinleştir()
            
            // 2. Kapı/Pencere Sensörü Kontrolü
            kapi_durumu = Sensor_Verisi_Oku("KAPI")

            EĞER kapi_durumu EŞİTTİR Eşik_Kapı_Açık İSE
                YAZDIR "Kapı/Pencere Açık Algılandı: " + kapi_durumu
                Sesli_Alarm_Etkinleştir()
            
            // 3. Alarm ve Bildirim Yönetimi
            EĞER Alarm_Aktif EŞİTTİR DOĞRU İSE
                EĞER Kullanıcı_Bildirildi EŞİTTİR YANLIŞ İSE
                    Kullaniciya_Bildirim_Gonder("Evde bir güvenlik ihlali algılandı!")
                
                // Alarm çalmaya devam eder...
                BEKLE(1) // Alarmın çalmaya devam ettiğini simüle et
            
        BAŞKA // Sistem Kapalı Durumda
            YAZDIR "Sistem Kapalı. Sensörler izlenmiyor."
            Alarm_Aktif = YANLIŞ // Kapalıyken alarmı kapat
            Kullanıcı_Bildirildi = YANLIŞ
        
        BEKLE(1) // Bir sonraki kontrol için 1 saniye bekle

    DÖNGÜ_SONU

ANA_PROGRAM_SONU
