## <a name="payment-process-overview"></a>Übersicht über den Zahlungsvorgang

Der Zahlungsvorgang besteht aus drei Teilen:

1. Der Bot sendet eine Zahlungsanforderung.

2. Der Benutzer meldet sich mit einem Microsoft-Konto an, um Zahlungs-, Versand- und Kontaktinformationen bereitzustellen. Es werden Rückrufe an den Bot gesendet, um mitzuteilen, wann er bestimmte Vorgänge durchführen muss (Lieferadresse aktualisieren, Versandoption aktualisieren, Zahlung abschließen).

3. Der Bot verarbeitet die empfangenen Rückrufe, einschließlich der Aktualisierung der Lieferadresse, der Versandoption und der vollständigen Zahlung. 

Ihr Bot muss Sie nur Schritt 1 und Schritt 3 dieses Prozesses Ihr Bot muss nur Schritt eins und Schritt drei dieses Vorgangs implementieren; Schritt zwei findet außerhalb des Kontexts Ihres Bot statt. 
