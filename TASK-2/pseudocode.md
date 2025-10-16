ALGORITMA AlisverisSistemi

  // **VERİ YAPILARI (DEĞİŞKENLER)**
  DEKLARE SEPET DIZISI (urun_adi, urun_fiyati, miktar)
  DEKLARE TOPLAM_TUTAR = 0
  DEKLARE SECIM TAM SAYI
  DEKLARE DONGU_DEVAM EKRANI MANTIKSAL = DOGRU

  // **ANA DÖNGÜ**
  DÖNGÜ DONGU_DEVAM_EKRANI = DOGRU İKEN

    // **KULLANICI MENÜSÜ**
    EKRANA_YAZ "--- ALİŞVERİŞ SİSTEMİ ---"
    EKRANA_YAZ "1. Ürün Ekle"
    EKRANA_YAZ "2. Sepeti Görüntüle ve Toplamı Hesapla"
    EKRANA_YAZ "3. Ödeme Yap"
    EKRANA_YAZ "4. Çıkış"
    EKRANA_YAZ "Seçiminizi Girin (1-4): "
    KULLANICIDAN_AL SECIM

    // **SEÇİMİ İŞLEME**
    EĞER SECIM = 1 İSE
      // Ürün Ekleme İşlemi
      CAGIR Fonksiyon_UrunEkle(SEPET)

    YOKSA EĞER SECIM = 2 İSE
      // Sepeti Görüntüleme ve Hesaplama İşlemi
      CAGIR Fonksiyon_SepetGoruntuleVeHesapla(SEPET, TOPLAM_TUTAR)

    YOKSA EĞER SECIM = 3 İSE
      // Ödeme İşlemi
      CAGIR Fonksiyon_Odeme(TOPLAM_TUTAR)
      // Ödeme başarılıysa döngüyü sonlandır
      EĞER TOPLAM_TUTAR > 0 İSE // Basit bir kontrol
        EKRANA_YAZ "Alışveriş Tamamlandı. Teşekkür ederiz!"
        DONGU_DEVAM_EKRANI = YANLIS
      YOKSA
        EKRANA_YAZ "Sepetiniz boş olduğu için ödeme yapılamaz."
      SON

    YOKSA EĞER SECIM = 4 İSE
      // Çıkış İşlemi
      EKRANA_YAZ "Sistemden Çıkılıyor..."
      DONGU_DEVAM_EKRANI = YANLIS

    YOKSA
      // Geçersiz Seçim
      EKRANA_YAZ "Geçersiz seçim. Lütfen 1 ile 4 arasında bir değer girin."

    SON

  DÖNGÜ_SONU

ALGORITMA_SONU

// **YARDIMCI FONKSİYONLAR**

FONKSIYON Fonksiyon_UrunEkle(SEPET)
  DEKLARE URUN_ADI METIN
  DEKLARE FIYAT ONDALIK
  DEKLARE MIKTAR TAM SAYI

  EKRANA_YAZ "Eklemek istediğiniz ürünün adını girin: "
  KULLANICIDAN_AL URUN_ADI
  EKRANA_YAZ "Ürünün birim fiyatını girin: "
  KULLANICIDAN_AL FIYAT
  EKRANA_YAZ "Miktarı girin: "
  KULLANICIDAN_AL MIKTAR

  // SEPET'e yeni bir ürün kaydı ekle
  SEPET'E_EKLE (URUN_ADI, FIYAT, MIKTAR)
  EKRANA_YAZ URUN_ADI & " sepete eklendi."
FONKSIYON_SONU

FONKSIYON Fonksiyon_SepetGoruntuleVeHesapla(SEPET, TOPLAM_TUTAR REFERANS)
  TOPLAM_TUTAR = 0 // Her çağrıda sıfırla

  EKRANA_YAZ "--- SEPETİNİZDEKİ ÜRÜNLER ---"
  
  EĞER SEPET BOŞ İSE
    EKRANA_YAZ "Sepetinizde ürün bulunmamaktadır."
  YOKSA
    HER ÜRÜN İÇİN SEPET İÇİNDE DÖNGÜ
      URUN_TOPLAM = ÜRÜN.urun_fiyati * ÜRÜN.miktar
      EKRANA_YAZ ÜRÜN.urun_adi & " - Miktar: " & ÜRÜN.miktar & " - Birim Fiyat: " & ÜRÜN.urun_fiyati & " TL - Ara Toplam: " & URUN_TOPLAM & " TL"
      TOPLAM_TUTAR = TOPLAM_TUTAR + URUN_TOPLAM
    DÖNGÜ_SONU
    
    EKRANA_YAZ "---------------------------------"
    EKRANA_YAZ "GENEL TOPLAM TUTAR: " & TOPLAM_TUTAR & " TL"

  SON
FONKSIYON_SONU

FONKSIYON Fonksiyon_Odeme(TOPLAM_TUTAR)
  EĞER TOPLAM_TUTAR > 0 İSE
    EKRANA_YAZ "Ödenecek Tutar: " & TOPLAM_TUTAR & " TL"
    EKRANA_YAZ "Ödeme işlemi simüle ediliyor..."
    // Buraya banka/ödeme entegrasyonu mantığı eklenebilir.
    EKRANA_YAZ "Ödeme Başarılı!"
  YOKSA
    EKRANA_YAZ "Ödeme Yapılacak Ürün Yok."
  SON
FONKSIYON_SONU
