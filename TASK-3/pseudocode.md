Modül1 - Randevu Alma:
// Kontrol noktaları: [A1]=Kimlik, [A2]=Poliklinik, [A3]=Doktor, [A4]=Saat, [A5]=Onay/SMS
BAŞLA
  // [A1] Kimlik doğrulama
  EĞER kimlik_doğrula() = HAYIR İSE HATA "Kimlik doğrulama başarısız"; BİTİR SON

  // [A2] Poliklinik seçimi
  poliklinik = poliklinik_seç()
  EĞER poliklinik = BOŞ İSE UYAR "Poliklinik seçiniz"; BİTİR SON

  // [A3] Doktor listesi ve seçim
  doktorlar = doktor_listesi_getir(poliklinik)
  EĞER doktorlar = BOŞ İSE UYAR "Uygun doktor yok"; BİTİR SON
  doktor = doktor_seç(doktorlar)

  // [A4] Uygun saatler ve seçim
  saatler = uygun_saatleri_getir(doktor)
  EĞER saatler = BOŞ İSE UYAR "Uygun saat bulunamadı"; BİTİR SON
  saat = saat_seç(saatler)

  // [A5] Randevu onayı
  EĞER kullanıcı_onayı() = EVET İSE
      randevu = randevu_oluştur(kimlik, poliklinik, doktor, saat)
      sms_gönder(randevu)
      BİLGİ "Randevu oluşturuldu ve SMS gönderildi"
  DEĞİLSE
      BİLGİ "Randevu iptal edildi"
  SON
BİTİR

Modül2 - Tahlil Sonuçları:

// Kontrol noktaları: [T1]=Kimlik, [T2]=Tahlil var mı, [T3]=Hazır mı, [T4]=Göster/İndir
BAŞLA
  // [T1] Kimlik doğrulama
  EĞER kimlik_doğrula() = HAYIR İSE HATA "Kimlik doğrulama başarısız"; BİTİR SON

  // [T2] Tahlil kaydı var mı?
  tahlil = tahlil_kayıt_sorgula(kimlik)
  EĞER tahlil = YOK İSE BİLGİ "Tahlil kaydı bulunamadı"; BİTİR SON

  // [T3] Sonuç hazır mı?
  EĞER tahlil.hazır_mı = HAYIR İSE
      BİLGİ "Sonuçlar hazırlanıyor. Lütfen daha sonra tekrar deneyin."
      BİTİR
  SON

  // [T4] Görüntüle veya PDF indir
  seçim = kullanıcı_seçimi("Görüntüle", "PDF İndir")
  EĞER seçim = "Görüntüle" İSE
      sonuç_görüntüle(tahlil)
  DEĞİLSE
      pdf_oluştur_ve_indir(tahlil)
  SON
BİTİR

Ana Menü - Birleştirme:
BAŞLA
  DÖNGÜ çıkış = HAYIR OLDUĞU SÜRECE
      seçim = menü(["Randevu Alma", "Tahlil Sonuçları", "Çıkış"])
      EĞER seçim = "Randevu Alma" İSE modül_randevu_alma()
      EĞER seçim = "Tahlil Sonuçları" İSE modül_tahlil_sonuçları()
      EĞER seçim = "Çıkış" İSE çıkış = EVET
  SON_DÖNGÜ
BİTİR
