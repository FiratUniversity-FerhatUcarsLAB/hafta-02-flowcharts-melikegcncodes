BAŞLA
    GÜNLÜK_LIMIT ← 5000
    HAK ← 3

    TEKRAR:
        YAZ "Lütfen PIN'inizi giriniz:"
        OKU PIN

        EĞER PIN DOĞRU İSE
            YAZ "PIN doğrulandı."
            OKU BAKİYE

            DÖNGÜ
                YAZ "Çekmek istediğiniz tutarı giriniz (20 TL katları):"
                OKU TUTAR

                EĞER TUTAR MOD 20 ≠ 0 İSE
                    YAZ "Tutar 20 TL katı olmalıdır."
                DEĞİLSE EĞER TUTAR > GÜNLÜK_LIMIT İSE
                    YAZ "Tutar günlük limiti aşıyor!"
                DEĞİLSE EĞER TUTAR > BAKİYE İSE
                    YAZ "Yetersiz bakiye!"
                DEĞİLSE
                    BAKİYE ← BAKİYE - TUTAR
                    GÜNLÜK_LIMIT ← GÜNLÜK_LIMIT - TUTAR
                    YAZ "İşlem başarılı. Kalan bakiye: ", BAKİYE
                SON

                YAZ "Başka işlem yapmak ister misiniz? (E/H)"
                OKU CEVAP
            DÖNGÜ CEVAP = "E" İKEN

            YAZ "İyi günler!"
            BİTİR
        DEĞİLSE
            HAK ← HAK - 1
            YAZ "Hatalı PIN. Kalan hakkınız: ", HAK
        SON

    EĞER HAK > 0 İSE
        GİT TEKRAR
    DEĞİLSE
        YAZ "3 defa hatalı PIN girdiniz. Kartınız bloke edildi."
    SON
BİTİR
