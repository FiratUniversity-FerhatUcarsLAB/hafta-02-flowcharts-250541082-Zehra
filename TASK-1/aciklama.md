İsim - Soy isim : Zehra ÖZDEMİR 
Öğrenci No:250541082
   POUSEDOCOD:
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
 Dot kodu :digraph ATM_ParaCekme_Akisi {
    // Grafiğin genel ayarları
    rankdir=TB; // Yukarıdan aşağıya akış (Top to Bottom)
    node [shape=box]; // Varsayılan düğüm şekli kutu
    
    // Şekil Tanımlamaları:
    node [shape=oval, style="filled", fillcolor="palegreen"]; Baslangic [label="BAŞLANGIÇ"];
    node [shape=box, style="filled", fillcolor="lightblue"]; İşlem [label="İşlem Adımı"];
    node [shape=diamond, style="filled", fillcolor="yellow"]; Karar [label="Karar Noktası"];
    node [shape=oval, style="filled", fillcolor="salmon"]; BitisHata [label="HATA/SON"];
    node [shape=oval, style="filled", fillcolor="lightgreen"]; BitisBasarili [label="BAŞARILI SON"];

    // 1. PIN Doğrulama
    Baslangic -> Karar_PIN [label="PIN ve Miktar Girildi"];
    Karar_PIN [label="PIN Doğru mu?"];
    Karar_PIN -> BitisHata_PIN [label="Hayır"];
    BitisHata_PIN [label="Hatalı PIN"];

    // 2. Miktar Doğrulama
    Karar_PIN -> Karar_MiktarPozitif [label="Evet"];
    Karar_MiktarPozitif [label="İstenen Miktar > 0?"];
    Karar_MiktarPozitif -> BitisHata_MiktarNegatif [label="Hayır"];
    BitisHata_MiktarNegatif [label="Geçersiz Miktar"];
    
    // 3. Limit Kontrolü
    Karar_MiktarPozitif -> Karar_Limit [label="Evet"];
    Karar_Limit [label="Miktar Limitler İçinde mi?"];
    Karar_Limit -> BitisHata_Limit [label="Hayır"];
    BitisHata_Limit [label="Limit Dışı Miktar"];

    // 4. ATM Yeterliliği Kontrolü
    Karar_Limit -> Karar_ATMYeter [label="Evet"];
    Karar_ATMYeter [label="ATM'de Yeterli Para Var mı?"];
    Karar_ATMYeter -> BitisHata_ATMYetersiz [label="Hayır"];
    BitisHata_ATMYetersiz [label="ATM Yetersiz Bakiye"];

    // 5. Hesap Bakiye Kontrolü
    Karar_ATMYeter -> Karar_HesapBakiye [label="Evet"];
    Karar_HesapBakiye [label="Hesap Bakiyesi Yeterli mi? (Miktar <= Bakiye)"];
    Karar_HesapBakiye -> BitisHata_HesapYetersiz [label="Hayır"];
    BitisHata_HesapYetersiz [label="Hesap Yetersiz Bakiye"];
    
    // 6. İşlem Gerçekleştirme
    Karar_HesapBakiye -> Islem_Guncelle [label="Evet"];
    Islem_Guncelle [label="Yeni Bakiye Hesapla"];
    
    // 7. Veritabanı Güncelleme
    Islem_Guncelle -> Karar_DB [label="Bakiye Güncelle"];
    Karar_DB [label="Veritabanı Güncelleme Başarılı mı?"];
    Karar_DB -> BitisHata_DB [label="Hayır"];
    BitisHata_DB [label="DB Güncelleme Hatası"];

    // 8. Başarılı İşlem Akışı
    Karar_DB -> Islem_ParaTeslim [label="Evet"];
    Islem_ParaTeslim [label="Parayı Teslim Et (Fiziksel)"];
    Islem_ParaTeslim -> Islem_ATM_Sayim_Azalt;
    Islem_ATM_Sayim_Azalt [label="ATM Para Sayımını Azalt"];
    Islem_ATM_Sayim_Azalt -> Islem_Makbuz;
    Islem_Makbuz [label="Makbuzu Bas"];
    
    // 9. Sonuç
    Islem_Makbuz -> BitisBasarili [label="Sonuç Ekrana Yazdırılır"];

    // Hata düğümlerini tek bir Bitiş düğümüne yönlendirme (isteğe bağlı)
    BitisHata_PIN -> BitisHata;
    BitisHata_MiktarNegatif -> BitisHata;
    BitisHata_Limit -> BitisHata;
    BitisHata_ATMYetersiz -> BitisHata;
    BitisHata_HesapYetersiz -> BitisHata;
    BitisHata_DB -> BitisHata;
}
 Graphviz:<img width="1386" height="1808" alt="graphviz (1)" src="https://github.com/user-attachments/assets/fd52c41f-ca19-4307-ac0e-7c06917be2ca" />
Gemini konuşmaları:
Atm de para çekme algoritmasını psoduocod ile oluştur.
Bu kodu graphviz dot koduna çevir.
sistemin kısa açıklaması (maks. 5-6 satır):
Oluşturulan dot kodunu Graphviz Online sayfasına  yukarda linki bulunduğu şekilde yapıştırıp diyagramını oluşturdum.



