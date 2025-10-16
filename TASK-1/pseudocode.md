PROGRAM ATM_ParaCekme

// Sabitler / Konfigürasyon
MAX_PIN_ATTEMPTS = 3
DAILY_WITHDRAWAL_LIMIT = 5000.00   // örnek: TL
CASH_DENOMINATIONS = [200, 100, 50, 20, 10, 5]  // ATM'deki banknot tipi ve stok kontrolü yapılır

// Fonksiyonlar
FUNCTION AuthenticateCard(cardData):
    IF cardData.isExpired OR cardData.status == "blocked":
        RETURN (false, "Kart geçersiz veya bloke")
    userAccount = ReadAccountFromCard(cardData)
    RETURN (true, userAccount)

FUNCTION VerifyPIN(userAccount, enteredPIN):
    attempts = 0
    WHILE attempts < MAX_PIN_ATTEMPTS:
        IF enteredPIN == userAccount.PIN:
            RETURN (true, "")
        ELSE:
            attempts = attempts + 1
            IF attempts >= MAX_PIN_ATTEMPTS:
                LockCard(userAccount)
                RETURN (false, "Çok fazla yanlış PIN. Kart bloke edildi.")
            // (UI) PIN tekrar sorulur (bu pseudocode seviyesinde temsil)
            enteredPIN = PromptPIN()
    END WHILE

FUNCTION CheckBalanceAndLimits(userAccount, amount):
    IF amount <= 0:
        RETURN (false, "Geçersiz tutar")
    IF amount > userAccount.balance:
        RETURN (false, "Yetersiz bakiye")
    IF (userAccount.todayWithdrawn + amount) > DAILY_WITHDRAWAL_LIMIT:
        RETURN (false, "Günlük çekim limiti aşılıyor")
    RETURN (true, "")

FUNCTION CheckATMCashAvailability(amount):
    // Greedy veya benzeri algoritma ile banknot kombinasyonu bulunur
    availableNotes = GetATMNNoteCounts() // örn: {200:2,100:5,...}
    combination = ComputeNoteCombination(amount, availableNotes)
    IF combination == null:
        RETURN (false, "ATM'de uygun nakit yok")
    RETURN (true, combination)

FUNCTION DispenseCash(noteCombination):
    // Donanım komutları ile banknotları ver
    success = ATMHardware.Dispense(noteCombination)
    RETURN success

FUNCTION PrintReceipt(userAccount, amount, newBalance):
    // Opsiyonel: fiş bastır
    Receipt.Print(...)

FUNCTION UpdateAccountAfterWithdrawal(userAccount, amount, noteCombination):
    userAccount.balance = userAccount.balance - amount
    userAccount.todayWithdrawn = userAccount.todayWithdrawn + amount
    SaveAccount(userAccount)
    UpdateATMNNoteCounts(noteCombination)
    LogTransaction(userAccount.id, amount, noteCombination, timestamp=Now())

// Ana akış
MAIN:
    Display("Kartınızı takınız")
    cardData = WaitForCardInsert()
    (ok, userAccountOrMsg) = AuthenticateCard(cardData)
    IF NOT ok:
        Display(userAccountOrMsg)
        EjectCard()
        END

    userAccount = userAccountOrMsg

    enteredPIN = PromptPIN()
    (pinOk, pinMsg) = VerifyPIN(userAccount, enteredPIN)
    IF NOT pinOk:
        Display(pinMsg)
        EjectCard()
        END

    DisplayMenu(["Para Çekme", "Bakiye Sorgulama", "Diğer İşlemler", "İptal"])
    choice = GetUserChoice()
    IF choice != "Para Çekme":
        HandleOtherChoice(choice)
        EjectCard()
        END

    amount = PromptAmount()  // kullanıcıdan çekmek istediği tutarı al
    (valid, msg) = CheckBalanceAndLimits(userAccount, amount)
    IF NOT valid:
        Display(msg)
        AskIfAnotherTransaction()
        EjectCard()
        END

    (cashOk, noteCombinationOrMsg) = CheckATMCashAvailability(amount)
    IF NOT cashOk:
        Display(noteCombinationOrMsg)
        AskIfAnotherTransaction()
        EjectCard()
        END

    // Onay ekranı
    Display("Çekilecek tutar: " + amount)
    Display("Devam etmek istiyor musunuz? E/H")
    confirm = GetUserConfirmation()
    IF confirm != "E":
        Display("İşlem iptal edildi")
        EjectCard()
        END

    // Nakit verme ve güncelleme
    dispenseSuccess = DispenseCash(noteCombinationOrMsg)
    IF NOT dispenseSuccess:
        Display("Nakit verilemedi. Lütfen bankayla iletişime geçin.")
        LogError(...)
        EjectCard()
        END

    UpdateAccountAfterWithdrawal(userAccount, amount, noteCombinationOrMsg)
    newBalance = userAccount.balance
    PrintReceipt(userAccount, amount, newBalance)

    Display("Lütfen paranızı ve kartınızı alın.")
    EjectCard()
    END
