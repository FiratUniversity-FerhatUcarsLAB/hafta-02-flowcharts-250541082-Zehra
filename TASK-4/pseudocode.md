// Öğrenci Bilgi Sistemi (OBS) Temel Algoritması

BAŞLA:

// Veri Yapıları (Basitleştirilmiş)
YAPILANMA Ogrenci:
    KimlikNo: TamSayı
    Sifre: Metin
    Ad: Metin
    Soyad: Metin
    KayitliDersler: Liste<Ders>
    Notlar: Sözlük<DersKodu, Not>

YAPILANMA Ders:
    DersKodu: Metin
    DersAdi: Metin
    Kredi: TamSayı
    Kontenjan: TamSayı

// Veritabanı Benzetimi
TANIMLA Ogrenciler: Liste<Ogrenci> // Öğrenci verilerinin bulunduğu liste
TANIMLA Dersler: Liste<Ders> // Tüm mevcut derslerin bulunduğu liste

TANIMLA OturumAcik: Mantıksal = YANLIŞ
TANIMLA MevcutOgrenci: Ogrenci = BOŞ

FONKSİYON KullaniciDogrula(KimlikNo, Sifre): Ogrenci
    HER Ogrenci OGR IN Ogrenciler İÇİN YAP:
        EĞER OGR.KimlikNo EŞİT KimlikNo VE OGR.Sifre EŞİT Sifre İSE:
            GERİ DÖN OGR
        SON
    SON
    GERİ DÖN BOŞ // Doğrulama başarısız

FONKSİYON AnaMenu():
    YAZDIR "=== Öğrenci Bilgi Sistemi ==="
    YAZDIR "1. Ders Kayıt İşlemleri"
    YAZDIR "2. Not Görüntüleme"
    YAZDIR "3. Çıkış Yap"
    OKU Secim

    GERİ DÖN Secim

FONKSİYON DersKayitIslemleri(Ogrenci OGR):
    YAZDIR "=== Ders Kayıt İşlemleri ==="
    YAZDIR "1. Mevcut Dersleri Görüntüle"
    YAZDIR "2. Ders Ekle"
    YAZDIR "3. Ders Bırak"
    YAZDIR "4. Ana Menüye Dön"
    OKU Secim_Ders

    DURUM Secim_Ders:
        DURUM "1":
            YAZDIR "--- Mevcut Dersler ---"
            HER Ders D IN Dersler İÇİN YAP:
                YAZDIR "Kod:", D.DersKodu, " | Adı:", D.DersAdi, " | Kredi:", D.Kredi, " | Kalan Kontenjan:", D.Kontenjan
            SON
            DersKayitIslemleri(OGR) // Tekrar Ders Kayıt Menüsüne dön
        DURUM "2":
            YAZDIR "Eklemek istediğiniz dersin kodunu girin:"
            OKU DersKodu_Ekle
            BULUNAN_DERS = Dersler'de DersKodu_Ekle'ye sahip Ders'i BUL

            EĞER BULUNAN_DERS BOŞ DEĞİL İSE VE BULUNAN_DERS.Kontenjan > 0 İSE:
                EĞER BULUNAN_DERS OGR.KayitliDersler'DE YOK İSE:
                    OGR.KayitliDersler'E BULUNAN_DERS'İ EKLE
                    BULUNAN_DERS.Kontenjan = BULUNAN_DERS.Kontenjan - 1
                    YAZDIR DersKodu_Ekle, " dersi başarıyla eklendi."
                DEĞİLSE:
                    YAZDIR "Bu derse zaten kayıtlısınız."
                SON
            DEĞİLSE:
                YAZDIR "Ders bulunamadı veya kontenjan dolu."
            SON
            DersKayitIslemleri(OGR)
        DURUM "3":
            YAZDIR "Bırakmak istediğiniz dersin kodunu girin:"
            OKU DersKodu_Bırak
            BULUNAN_DERS = OGR.KayitliDersler'DE DersKodu_Bırak'a sahip Ders'i BUL

            EĞER BULUNAN_DERS BOŞ DEĞİL İSE:
                OGR.KayitliDersler'DEN BULUNAN_DERS'İ SİL
                BULUNAN_DERS_TÜM = Dersler'DE DersKodu_Bırak'a sahip Ders'i BUL // Genel listede de kontenjanı artır
                EĞER BULUNAN_DERS_TÜM BOŞ DEĞİL İSE:
                    BULUNAN_DERS_TÜM.Kontenjan = BULUNAN_DERS_TÜM.Kontenjan + 1
                SON
                YAZDIR DersKodu_Bırak, " dersi başarıyla bırakıldı."
            DEĞİLSE:
                YAZDIR "Bu derse kayıtlı değilsiniz."
            SON
            DersKayitIslemleri(OGR)
        DURUM "4":
            GERİ DÖN // Ana Menüye döner
        VARSAYILAN:
            YAZDIR "Geçersiz seçim."
            DersKayitIslemleri(OGR)
    SON

FONKSİYON NotGoruntule(Ogrenci OGR):
    YAZDIR "=== Not Görüntüleme ==="
    EĞER OGR.Notlar BOŞ İSE:
        YAZDIR "Henüz girilmiş bir notunuz bulunmamaktadır."
    DEĞİLSE:
        HER DersKodu, Not IN OGR.Notlar İÇİN YAP:
            YAZDIR DersKodu, ": ", Not
        SON
    SON

// --- Ana Program Akışı ---

DÖNGÜ OturumAcik EŞİT YANLIŞ İKEN YAP: // Giriş DÖNGÜSÜ
    YAZDIR "Lütfen Kimlik Numaranızı girin:"
    OKU GirilenKimlikNo
    YAZDIR "Lütfen Şifrenizi girin:"
    OKU GirilenSifre

    MevcutOgrenci = KullaniciDogrula(GirilenKimlikNo, GirilenSifre)

    EĞER MevcutOgrenci BOŞ DEĞİL İSE:
        OturumAcik = DOĞRU
        YAZDIR "Hoş geldiniz, ", MevcutOgrenci.Ad, " ", MevcutOgrenci.Soyad
    DEĞİLSE:
        YAZDIR "Hatalı Kimlik No veya Şifre. Lütfen tekrar deneyin."
    SON
SON // Giriş DÖNGÜSÜ Sonu

DÖNGÜ OturumAcik EŞİT DOĞRU İKEN YAP: // Ana İşlem DÖNGÜSÜ
    Secim_Ana = AnaMenu()

    DURUM Secim_Ana:
        DURUM "1":
            DersKayitIslemleri(MevcutOgrenci)
        DURUM "2":
            NotGoruntule(MevcutOgrenci)
        DURUM "3":
            OturumAcik = YANLIŞ
            YAZDIR "Oturum kapatıldı. İyi günler dileriz."
        VARSAYILAN:
            YAZDIR "Geçersiz seçim. Lütfen tekrar deneyin."
    SON
SON // Ana İşlem DÖNGÜSÜ Sonu

SON:
