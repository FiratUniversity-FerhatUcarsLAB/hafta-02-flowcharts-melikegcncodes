// Akıllı Ev Güvenlik – Sürekli Çalışan Ana Döngü
// Seviyeler: 0=Normal, 1=Bilgilendirme, 2=Uyarı, 3=Kritik (Tam Alarm)

BAŞLA
  sistemi_init_et()
  alarm_aktif_mi ← HAYIR

  DÖNGÜ DOĞRU OLDUĞU SÜRECE     // Sonsuz döngü
    veriler = sensörleri_oku({
      hareket, kapı_pencere, cam_kırılma, duman, gaz, sıcaklık, kamera_yapay_zeka
    })

    seviye = tehdit_seviyesi_hesapla(veriler)   // ağırlıklı skor + eşikler
    olayı_kaydet(veriler, seviye, zaman=ŞİMDİ)

    EĞER seviye = 0 İSE
        EĞER alarm_aktif_mi = EVET İSE uyarı_göstergelerini_kapat()
        alarm_aktif_mi ← HAYIR
    DEĞİLSE EĞER seviye = 1 İSE
        mobil_bildirim_gönder("Düşük risk", veriler)
    DEĞİLSE EĞER seviye = 2 İSE
        uyarı_isiklarını_yak(); buzzer_kısa_çalıştır()
        mobil_bildirim_gönder("Orta risk – kontrol edin", veriler)
        alarm_aktif_mi ← EVET
    DEĞİLSE      // seviye = 3
        siren_aç(); kapı_kilitlerini_aktif_et(); kamera_kaydı_başlat()
        acil_merkez_bildir(veriler)
        alarm_aktif_mi ← EVET
    SON

    // Kullanıcıdan manuel sıfırlama/iptal
    EĞER alarm_aktif_mi = EVET VE alarm_sıfırlama_komutu_geldi_mi() = EVET İSE
        siren_kapat(); uyarı_isiklarını_kapat(); alarm_aktif_mi ← HAYIR
        mobil_bildirim_gönder("Alarm sıfırlandı", {})
    SON

    uyku_ms(250)   // örn. 4 Hz tarama; gerçek zamanda ayarlanır
  SON_DÖNGÜ
BİTİR
