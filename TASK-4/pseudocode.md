// Üniversite Ders Kayıt Sistemi – Pseudocode
// Kontrol noktaları: [K1]=Ön koşul, [K2]=Kontenjan, [K3]=Çakışma, [K4]=Kredi limiti, [K5]=Danışman onayı

BAŞLA
  EĞER kimlik_doğrula(ogrenci) = HAYIR İSE
      HATA "Giriş başarısız"; BİTİR
  SON

  seçili_dersler = []
  DÖNGÜ kullanıcı_bitir_dedi_mi() = HAYIR OLDUĞU SÜRECE
      ders = ders_listesinden_seç()
      EĞER ders = YOK İSE DEVAM ET

      // [K1] Ön koşul
      EĞER on_kosul_saglandi_mi(ogrenci, ders) = HAYIR İSE
          bildir(ders, "Ön koşul sağlanmadı"); DEVAM ET
      SON

      // [K2] Kontenjan
      EĞER kapasite_var_mi(ders) = HAYIR İSE
          yedek_listeye_ekle(ogrenci, ders)
          bildir(ders, "Kontenjan dolu (yedek liste)"); DEVAM ET
      SON

      // [K3] Zaman çakışması
      EĞER zaman_cakismasi_var_mi(seçili_dersler, ders) = EVET İSE
          bildir(ders, "Zaman çakışması"); DEVAM ET
      SON

      // [K4] Kredi limiti
      yeni_toplam_kredi = toplam_kredi(seçili_dersler) + ders.kredi
      EĞER yeni_toplam_kredi > ogrenci.kredi_ust_siniri İSE
          bildir(ders, "Kredi üst sınırı aşılıyor"); DEVAM ET
      SON

      // Tüm kontroller geçti: geçici ekle
      seçili_dersler ← ders
      kapasite_rezerv_et(ders, ogrenci)
      bildir(ders, "Ders geçici olarak eklendi")
  SON_DÖNGÜ

  // Alt sınır kontrolü (opsiyonel)
  EĞER toplam_kredi(seçili_dersler) < ogrenci.kredi_alt_siniri İSE
      UYAR "Alt kredi sınırı karşılanmıyor"; // öğrenci geri dönebilir
  SON

  // [K5] Danışman onayı
  EĞER danisman_onayi(seçili_dersler, ogrenci) = EVET İSE
      kayıtları_kesinleştir(ogrenci, seçili_dersler)
      bildirim_gönder(ogrenci, "Kayıt onaylandı")
  DEĞİLSE
      gerekçe = danisman_red_gerekcesi()
      rezervasyonları_geri_al(seçili_dersler)
      bildirim_gönder(ogrenci, "Kayıt reddedildi: " + gerekçe)
  SON

BİTİR
