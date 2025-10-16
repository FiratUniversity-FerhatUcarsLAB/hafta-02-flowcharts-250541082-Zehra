// Veri Yapıları Tanımlamaları

YAPI Hasta:
  ID: TamSayı
  Ad: Metin
  Soyad: Metin
  TC_Kimlik: Metin

YAPI Doktor:
  ID: TamSayı
  Ad: Metin
  Uzmanlık: Metin
  Çalışma_Saatleri: Dizi<SaatDilimi>

YAPI Randevu:
  ID: TamSayı
  Hasta_ID: TamSayı
  Doktor_ID: TamSayı
  Tarih_Saat: ZamanDamgası
  Durum: Metin (Örn: "Planlandı", "Tamamlandı", "İptal Edildi")

YAPI TahlilSonucu:
  ID: TamSayı
  Hasta_ID: TamSayı
  Randevu_ID: TamSayı (İsteğe Bağlı)
  Tahlil_Türü: Metin
  Sonuç_Değeri: Metin/Sayı
  Referans_Aralık: Metin
  Rapor_Tarihi: ZamanDamgası
  Durum: Metin (Örn: "Beklemede", "Hazır", "Onaylandı")

// Veritabanı Benzetimi (Koleksiyonlar)
VERİTABANI:
  Hastalar: Liste<Hasta>
  Doktorlar: Liste<Doktor>
  Randevular: Liste<Randevu>
  Tahlil_Sonuçları: Liste<TahlilSonucu>

// **********************************************
// BÖLÜM 1: RANDEVU ALMA ALGORİTMASI
// **********************************************

FONKSİYON Randevu_Al(Hasta_TC, Uzmanlık_Alanı, İstenen_Tarih): Mantıksal
  // 1. Hasta Doğrulama
  Hasta_Nesnesi = Hastalar.TC_Kimlik_ile_Bul(Hasta_TC)
  EĞER Hasta_Nesnesi BOŞSA:
    Geri_Dön: YANLIŞ ("Hasta kaydı bulunamadı.")

  // 2. Uygun Doktorları Filtreleme
  Uygun_Doktorlar = Doktorlar.Uzmanlık_ile_Filtrele(Uzmanlık_Alanı)
  EĞER Uygun_Doktorlar BOŞSA:
    Geri_Dön: YANLIŞ ("Bu uzmanlık alanında doktor bulunamadı.")

  // 3. Uygun Zaman Dilimi Bulma
  Uygun_Randevu_Saati = BOŞ
  SEÇİLEN_DOKTOR = BOŞ
  HER_BİR Doktor_Objesi İÇİN Uygun_Doktorlar:
    HER_BİR Saat_Dilimi İÇİN Doktor_Objesi.Çalışma_Saatleri:
      // Randevu çakışması kontrolü
      Çakışan_Randevu = Randevular.Filtrele(
        Doktor_ID = Doktor_Objesi.ID VE
        Tarih_Saat = Saat_Dilimi VE
        Durum != "İptal Edildi"
      )
      
      EĞER Çakışan_Randevu BOŞSA VE Saat_Dilimi.Tarih = İstenen_Tarih:
        Uygun_Randevu_Saati = Saat_Dilimi
        SEÇİLEN_DOKTOR = Doktor_Objesi
        DÖNGÜYÜ_SONLANDIR (İçteki Döngü)
    EĞER Uygun_Randevu_Saati BOŞ DEĞİLSE:
      DÖNGÜYÜ_SONLANDIR (Dıştaki Döngü)

  EĞER Uygun_Randevu_Saati BOŞSA:
    Geri_Dön: YANLIŞ ("Seçilen tarih için uygun randevu saati bulunamadı.")

  // 4. Randevuyu Kaydetme
  Yeni_Randevu = YENİ Randevu(
    ID: Randevular.Yeni_ID_Oluştur(),
    Hasta_ID: Hasta_Nesnesi.ID,
    Doktor_ID: SEÇİLEN_DOKTOR.ID,
    Tarih_Saat: Uygun_Randevu_Saati,
    Durum: "Planlandı"
  )
  
  Randevular.Ekle(Yeni_Randevu)
  Randevu_Onay_Mesajı_Gönder(Yeni_Randevu)
  Geri_Dön: DOĞRU ("Randevunuz başarıyla oluşturuldu.")

// **********************************************
// BÖLÜM 2: TAHLİL SONUCU GÖRÜNTÜLEME ALGORİTMASI
// **********************************************

FONKSİYON Tahlil_Sonuçlarını_Görüntüle(Hasta_TC): Liste<TahlilSonucu>
  // 1. Hasta Doğrulama
  Hasta_Nesnesi = Hastalar.TC_Kimlik_ile_Bul(Hasta_TC)
  EĞER Hasta_Nesnesi BOŞSA:
    Geri_Dön: BOŞ ("Hasta kaydı bulunamadı.")

  // 2. Tahlil Sonuçlarını Sorgulama
  Hasta_Tahlilleri = Tahlil_Sonuçları.Filtrele(Hasta_ID = Hasta_Nesnesi.ID)
  
  EĞER Hasta_Tahlilleri BOŞSA:
    Geri_Dön: BOŞ ("Hasta adına kayıtlı tahlil sonucu bulunamadı.")

  // 3. Sonuçları Hazırlama ve Görüntüleme Yetkisi Kontrolü
  Görüntülenecek_Sonuçlar = Yeni Liste<TahlilSonucu>
  HER_BİR Sonuç İÇİN Hasta_Tahlilleri:
    EĞER Sonuç.Durum = "Hazır" VEYA Sonuç.Durum = "Onaylandı":
      Görüntülenecek_Sonuçlar.Ekle(Sonuç)
    // Diğer durumlar (Örn: 'Beklemede') gösterilmez

  // 4. Sonuçları Geri Döndürme
  Geri_Dön: Görüntülenecek_Sonuçlar

// **********************************************
// BÖLÜM 3: TAHLİL SONUCU GİRİŞİ (LABORATUVAR İŞLEMİ)
// **********************************************

FONKSİYON Tahlil_Sonucu_Giris(Hasta_ID, Tahlil_Türü, Sonuç_Değeri): Mantıksal
  // 1. Hasta Kontrolü (Opsiyonel: İlgili Randevunun veya Talebin Kontrolü)
  EĞER Hastalar.ID_ile_Bul(Hasta_ID) BOŞSA:
    Geri_Dön: YANLIŞ ("Geçersiz Hasta ID.")

  // 2. Yeni Tahlil Sonucu Kaydı Oluşturma
  Yeni_Sonuç = YENİ TahlilSonucu(
    ID: Tahlil_Sonuçları.Yeni_ID_Oluştur(),
    Hasta_ID: Hasta_ID,
    Tahlil_Türü: Tahlil_Türü,
    Sonuç_Değeri: Sonuç_Değeri,
    Rapor_Tarihi: ŞİMDİKİ_ZAMAN,
    Durum: "Hazır" // İlk girişte 'Hazır' olabilir, doktor onayı sonrası 'Onaylandı'
  )
  
  Tahlil_Sonuçları.Ekle(Yeni_Sonuç)
  
  // 3. Bildirim Gönderme (Hasta ve/veya İlgili Doktor)
  Bildirim_Gönder(Hasta_ID, "Yeni tahlil sonucunuz görüntülenebilir.")

  Geri_Dön: DOĞRU
