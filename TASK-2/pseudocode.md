// Adım 2: Pseudocode (BAŞLA/BİTİR, DÖNGÜ ve EĞER-İSE kullanımına uygun)
// Kontrol noktaları: [K1]...[K6]

BAŞLA
  // K1: Kullanıcı girişi
  EĞER kullanıcı_oturumu_yok_ise
      kullanıcı_girişi_yap()
      EĞER giriş_başarısız_ise
          HATA "Giriş başarısız"; BİTİR
      SON
  SON

  // Ürün ekleme döngüsü
  DÖNGÜ ürün_seçimi_bitti_mi = HAYIR OLDUĞU SÜRECE
      ürün = ürün_seç()
      adet = adet_belirle()
      sepete_ekle(ürün, adet)
      ürün_seçimi_bitti_mi = kullanıcı_devam_etmek_istiyor_mu() == HAYIR ? EVET : HAYIR
  SON_DÖNGÜ

  // K2: Stok kontrolü
  TÜM sepet_satırları İÇİN
      EĞER stok_yeterli_mi(satır) = HAYIR İSE
          satırı_güncelle_veya_kaldır(satır)
      SON
  SON_TÜM

  // K3: İndirim kodu
  EĞER kullanıcı_kupon_girdi_mi = EVET İSE
      EĞER kupon_geçerli_mi() = EVET İSE indirimi_uygula()
      DEĞİLSE kuponu_reddet_ve_uyar()
      SON
  SON

  // K4: Kargo hesaplama
  adres = adres_seç()
  kargo_seçeneği = kargo_opsiyonunu_hesapla(adres, sepet)
  kargo_ücreti = kargo_seçeneği.ücret

  // K5: Ödeme
  ödeme_yöntemi = ödeme_yöntemi_seç()
  ödeme_sonuç = ödemeyi_gerçekleştir(ödeme_yöntemi, toplam + kargo_ücreti)
  EĞER ödeme_sonuç = BAŞARISIZ İSE
      alternatif_yöntem_sun()
      BİTİR
  SON

  // K6: Sipariş ve bildirim
  sipariş = sipariş_oluştur(sepet, adres, kargo_seçeneği, ödeme_yöntemi)
  stok_düş(sipariş)
  bildirim_gönder(sipariş)

BİTİR
