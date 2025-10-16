FONKSIYON ParaCekme(istenenMiktar, mevcutBakiye, PIN)

    // 1. PIN Doğrulaması
    EĞER PIN_DOGRULANDI(PIN) EŞİT DEĞİLSE DOĞRU
        EKRANA_YAZ("Hatalı PIN. Lütfen tekrar deneyiniz.")
        GERI_DON("Hata: PIN Doğrulanamadı")
    SON

    // 2. İşlem Miktarı Doğrulaması (Pozitif ve Geçerli Miktar)
    EĞER istenenMiktar <= 0
        EKRANA_YAZ("Geçersiz miktar. Lütfen pozitif bir değer girin.")
        GERI_DON("Hata: Geçersiz Miktar")
    SON

    // 3. ATM Minimum/Maksimum Limit Kontrolü
    EĞER istenenMiktar < MIN_CEKIM_LIMITI VEYA istenenMiktar > MAX_CEKIM_LIMITI
        EKRANA_YAZ("Çekmek istediğiniz miktar ATM limitlerinin dışında.")
        EKRANA_YAZ("Minimum: " + MIN_CEKIM_LIMITI + ", Maksimum: " + MAX_CEKIM_LIMITI)
        GERI_DON("Hata: Limit Dışı Miktar")
    SON
    
    // 4. ATM Para Yeterliliği Kontrolü (ATM'de yeterli banknot var mı?)
    EĞER ATM_YETERLI_PARAYA_SAHIP_DEGIL(istenenMiktar)
        EKRANA_YAZ("ATM'de istenen miktarda para bulunmamaktadır. Daha küçük bir miktar deneyiniz.")
        GERI_DON("Hata: ATM Yetersiz Bakiye")
    SON
    
    // 5. Hesap Bakiyesi Kontrolü
    EĞER istenenMiktar > mevcutBakiye
        EKRANA_YAZ("Hesabınızda yeterli bakiye bulunmamaktadır.")
        EKRANA_YAZ("Mevcut Bakiye: " + mevcutBakiye)
        GERI_DON("Hata: Yetersiz Bakiye")
    SON

    // 6. İşlemi Gerçekleştirme
    yeniBakiye = mevcutBakiye - istenenMiktar

    // 7. Veritabanı Güncelleme
    EĞER HESAP_BAKIYESINI_GUNCELLE(yeniBakiye) EŞİTSE BAŞARILI
        
        // 8. Para Çekme
        PARAYI_TESLIM_ET(istenenMiktar)
        ATM_PARASINI_AZALT(istenenMiktar) // ATM içindeki para sayısını güncelle
        
        // 9. Makbuz Basma
        MAKBUZ_BAS("Çekilen Miktar: " + istenenMiktar, "Yeni Bakiye: " + yeniBakiye)
        
        // 10. Sonuç
        EKRANA_YAZ("İşlem başarılı. Lütfen paranızı ve kartınızı alınız.")
        GERI_DON("Başarılı")
        
    DIĞER
        EKRANA_YAZ("İşlem sırasında bir hata oluştu. Lütfen bankanızla iletişime geçiniz.")
        GERI_DON("Hata: Veritabanı Güncelleme Başarısız")
    SON
    
SON_FONKSIYON
