---
title: Anfordern von Zahlungen | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie mit dem Bot Builder SDK für .NET eine Zahlungsanforderung senden.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/17
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: ce58c210123219c14f580d7164c759307ac2237b
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39304085"
---
# <a name="request-payment"></a>Anfordern von Zahlungen
> [!div class="op_single_selector"]
> - [.NET](../dotnet/bot-builder-dotnet-request-payment.md)
> - [Node.js](../nodejs/bot-builder-nodejs-request-payment.md)

Wenn Ihr Bot Benutzern den Kauf von Artikeln ermöglicht, kann er durch Einbeziehen eines besonderen Schaltflächentyps in eine [Rich Card](bot-builder-dotnet-add-rich-card-attachments.md) eine Zahlung anfordern. In diesem Artikel wird beschrieben, wie Sie mit dem Bot Builder SDK für .NET eine Zahlungsanforderung senden.

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie mit dem Bot Builder SDK für .NET eine Zahlungsanforderung senden können, müssen Sie die folgenden erforderlichen Aufgaben ausführen.

### <a name="update-webconfig"></a>Aktualisieren der Datei „Web.config“

Aktualisieren Sie die Datei **Web.config** Ihres Bots, indem Sie `MicrosoftAppId` und `MicrosoftAppPassword` auf die Werte für App-ID und App-Kennwort festlegen, die beim [Registrierungsvorgang](~/bot-service-quickstart-registration.md) für Ihren Bot generiert wurden. 

> [!NOTE]
> Unter [MicrosoftAppID und MicrosoftAppPassword](~/bot-service-manage-overview.md#microsoftappid-and-microsoftapppassword) wird beschrieben, wie Sie die Werte für **AppID** und **AppPassword** für Ihren Bot finden.

### <a name="create-and-configure-merchant-account"></a>Erstellen und Konfigurieren eines Händlerkontos

1. <a href="https://dashboard.stripe.com/register" target="_blank">Erstellen und aktivieren Sie ein Stripe-Konto, falls Sie noch keins besitzen.</a>

2. <a href="https://seller.microsoft.com/en-us/dashboard/registration/seller/?accountprogram=botframework" target="_blank">Melden Sie sich mit Ihrem Microsoft-Konto beim Seller Center an.</a>

3. Verbinden Sie im Seller Center Ihr Konto mit Stripe.

4. Navigieren Sie im Seller Center zum Dashboard, und kopieren Sie den Wert von **MerchantID**.

5. Aktualisieren Sie die Datei **Web.config** Ihres Bots, indem Sie `MerchantId` auf den Wert festlegen, den Sie aus dem Seller Center-Dashboard kopiert haben. 

[!INCLUDE [Payment process overview](../includes/snippet-payment-process-overview.md)]

## <a name="payment-bot-sample"></a>Beispiel für einen Zahlungsbot

Das <a href="https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/sample-payments" target="_blank">Zahlungsbot</a>-Beispiel enthält einen Beispielbot, der mithilfe von .NET eine Zahlungsanforderung sendet. Um diesen Beispielbot in Aktion zu sehen, können Sie ihn <a href="https://webchat.botframework.com/embed/paymentsample?s=d39Bk7JOMzQ.cwA.Rig.dumLki9bs3uqfWFMjXPn5PFnQVmT2VAVR1Zl1iPi07k" target="_blank">in einem Webchat ausprobieren</a>, <a href="https://join.skype.com/bot/9fbc0f17-43eb-40fe-bf3b-af151e6ce45e" target="_blank">als Skype-Kontakt hinzufügen</a> oder das Zahlungsbot-Beispiel herunterladen und lokal mit dem Bot Framework-Emulator ausführen. 

> [!NOTE]
> Um den End-to-End-Zahlungsvorgang mit dem **Zahlungsbot**-Beispiel im Webchat oder in Skype abschließen zu können, müssen Sie in Ihrem Microsoft-Konto eine gültige Kredit- oder Debitkarte (z. B. eine gültige Karte von einem Aussteller in den USA) angeben. Ihre Kreditkarte wird nicht belastet, und die Kreditkartenprüfnummer wird nicht überprüft, weil das **Zahlungsbot**-Beispiel im Testmodus ausgeführt wird (d. h. `LiveMode` ist in der Datei **Web.config** auf `false` festgelegt).

In den nächsten Abschnitten dieses Artikels werden die drei Teile des Zahlungsvorgangs im Kontext des **Zahlungsbot**-Beispiels beschrieben.

## <a id="request-payment"></a> Anfordern von Zahlungen

Ihr Bot kann von einem Benutzer eine Zahlung anfordern, indem er eine Nachricht mit einer [Rich Card-Anlage](bot-builder-dotnet-add-rich-card-attachments.md) mit einer Schaltfläche sendet, die eine Zahlung des Typs `CardAction.Type` angibt. In diesem Codeausschnitt aus dem **Zahlungsbot**-Beispiel wird eine Nachricht erstellt, die eine Hero Card mit der Schaltfläche **Buy** (Kaufen) enthält, auf die der Benutzer klicken (oder tippen) kann, um den Zahlungsvorgang zu initiieren. 

[!code-csharp[Request payment](../includes/code/dotnet-request-payment.cs#requestPayment)]

In diesem Beispiel ist `PaymentRequest.PaymentActionType` als Schaltflächentyp angegeben, der in der Bot Builder-Bibliothek als „Zahlung“ definiert ist. Der Wert der Schaltfläche wird von der Methode `BuildPaymentRequest` ausgefüllt, die ein `PaymentRequest`-Objekt zurückgibt, das Informationen zu den unterstützten Zahlungsmethoden, Details und Optionen enthält. Weitere Informationen zu den Implementierungsdetails finden Sie unter **Dialogs/RootDialog.cs** im <a href="https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/sample-payments" target="_blank">Zahlungsbot</a>-Beispiel.

Dieser Screenshot zeigt die Hero Card (mit der Schaltfläche **Kaufen**), die im obigen Codeausschnitt generiert wird. 
 
![Bot mit Zahlungsbeispiel](../media/payments-bot-buy.png) 

> [!IMPORTANT]
> Alle Benutzer mit Zugriff auf die Schaltfläche **Kaufen** können damit den Zahlungsvorgang initiieren. Im Kontext einer Gruppenunterhaltung ist es nicht möglich, eine Schaltfläche nur für die Verwendung durch einen bestimmten Benutzer festzulegen. 

## <a id="user-experience"></a> Benutzeroberfläche

Wenn ein Benutzer auf die Schaltfläche **Kaufen** klickt, wird er zu einer Zahlungsweboberfläche weitergeleitet, damit er dort alle erforderlichen Zahlungs-, Versand- und Kontaktinformationen über sein Microsoft-Konto angeben kann. 

![Zahlung über Microsoft](../media/microsoft-payment.png)

### <a name="http-callbacks"></a>HTTP-Rückrufe

HTTP-Rückrufe werden an Ihren Bot gesendet, um anzugeben, dass bestimmte Vorgänge ausgeführt werden müssen. Jeder Rückruf ist eine [Aktivität](bot-builder-dotnet-activities.md), die die folgenden Eigenschaftswerte enthält: 

| Eigenschaft | Wert |
|----|----|
| `Activity.Type` | invoke | 
| `Activity.Name` | Gibt den Typ des Vorgangs an, den der Bot ausführen soll (z. B. Lieferanschrift aktualisieren, Versandoption aktualisieren, Zahlung vornehmen). | 
| `Activity.Value` | Die Anforderungsnutzlast im JSON-Format. | 
| `Activity.RelatesTo` |  Beschreibt den der Zahlungsanforderung zugeordneten Kanal und Benutzer. | 

> [!NOTE]
> `invoke` ist ein spezieller Aktivitätstyp, der für die Verwendung durch das Microsoft Bot Framework reserviert ist. Der Absender einer `invoke`-Aktivität erwartet, dass Ihr Bot den Rückruf durch Senden einer HTTP-Antwort bestätigt.

## <a id="process-callbacks"></a> Verarbeiten von Rückrufen

[!INCLUDE [Process callbacks overview](../includes/snippet-payment-process-callbacks-overview.md)]

### <a name="shipping-address-update-and-shipping-option-update-callbacks"></a>Rückrufe zum Aktualisieren von Lieferanschrift und Versandoption

[!INCLUDE [Process shipping address and shipping option callbacks](../includes/snippet-payment-process-callbacks-1.md)]

### <a name="payment-complete-callbacks"></a>Rückrufe des Typs „Zahlung vornehmen“

Beim Empfang eines Rückrufs des Typs „Zahlung vornehmen“ werden Ihrem Bot in der `Activity.Value`-Eigenschaft eine Kopie der ersten, unveränderten Zahlungsanforderung sowie die Zahlungsantwortobjekte zur Verfügung gestellt. Das Zahlungsantwortobjekt enthält die endgültige Auswahl des Kunden sowie ein Zahlungstoken. Ihr Bot sollte die letzte Zahlungsanforderung anhand der ersten Zahlungsanforderung und der endgültigen Auswahl des Kunden neu berechnen. Unter der Voraussetzung, dass die Auswahl des Kunden als gültig ermittelt wurde, sollte der Bot den Betrag und die Währung in der Kopfzeile des Zahlungstokens überprüfen, um sicherzustellen, dass die Werte mit der letzten Zahlungsanforderung übereinstimmen.  Wenn der Bot entscheidet, den Kunden zu belasten, sollte nur der Betrag in der Kopfzeile des Zahlungstokens in Rechnung gestellt werden, da dies der vom Kunden bestätigte Preis ist. Wenn es zwischen den Werten, die der Bot erwartet und die er im Rückruf „Zahlung vornehmen“ empfangen hat, Abweichungen gibt, kann er durch Senden des HTTP-Statuscodes `200 OK` und Festlegen des Ergebnisfelds auf `failure` die Zahlungsanforderung als fehlerhaft kennzeichnen.   

Neben der Überprüfung der Zahlungsdetails sollte der Bot auch prüfen, ob der Auftrag ausgeführt werden kann, bevor er die Zahlungsverarbeitung initiiert. Beispielsweise sollte er sicherstellen, dass die Artikel, die gekauft werden, auch noch vorrätig sind. Wenn die Werte korrekt sind und Ihr Zahlungsabwickler das Zahlungstoken erfolgreich belastet hat, sollte der Bot mit dem HTTP-Statuscode `200 OK` antworten und das Ergebnisfeld auf `success` festlegen, damit auf der Zahlungsweboberfläche die Zahlungsbestätigung angezeigt werden kann. Das Zahlungstoken, das der Bot empfängt, kann nur einmal von dem anfordernden Händler verwendet werden und muss an Stripe übermittelt werden. (Stripe ist der einzige Zahlungsabwickler, der aktuell vom Bot Framework unterstützt wird.) Das Senden eines HTTP-Statuscodes im Bereich `400` oder `500` führt zu einem allgemeinen Fehler für den Kunden.

Im **Zahlungsbot**-Beispiel verarbeitet die Methode `OnInvoke` die Rückrufe, die der Bot empfängt. 

[!code-csharp[Request payment](../includes/code/dotnet-request-payment.cs#processCallback)]

In diesem Beispiel untersucht der Bot die `Name`-Eigenschaft der eingehenden Aktivität, um den Typ des auszuführenden Vorgangs zu bestimmen, und ruft dann die entsprechende Methode auf, um den Rückruf zu verarbeiten. Weitere Informationen zu den Implementierungsdetails finden Sie unter **Controllers/MessagesControllers.cs** im <a href="https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/sample-payments" target="_blank">Zahlungsbot</a>-Beispiel.

## <a name="testing-a-payment-bot"></a>Testen eines Zahlungsbots

[!INCLUDE [Test a payment bot](../includes/snippet-payment-test-bot.md)]

Im <a href="https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/sample-payments" target="_blank">Zahlungsbot</a>-Beispiel bestimmt die Konfigurationseinstellung `LiveMode` in der Datei **Web.config**, ob Rückrufe des Typs „Zahlung vornehmen“ emulierte oder echte Zahlungstoken enthalten. Wenn `LiveMode` auf `false` festgelegt ist, wird der ausgehenden Zahlungsanforderung des Bots eine Kopfzeile hinzugefügt, die angibt, dass sich der Bot im Testmodus befindet, und der Rückruf „Zahlung vornehmen“ enthält ein emuliertes Zahlungstoken, das nicht belastet werden kann. Wenn `LiveMode` auf `true` festgelegt ist, fehlt die Kopfzeile mit dem Hinweis auf den Testmodus in der ausgehenden Zahlungsanforderung des Bots, und der Rückruf „Zahlung vornehmen“ enthält ein echtes Zahlungstoken, das der Bot an Stripe zur Zahlungsabwicklung sendet. Dies ist dann eine echte Transaktion, die zu einer Belastung des angegebenen Zahlungsinstruments führt. 

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- <a href="https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/sample-payments" target="_blank">Zahlungsbot-Beispiel</a>
- [Übersicht über Aktivitäten](bot-builder-dotnet-activities.md)
- [Hinzufügen von Rich Cards zu Nachrichten](bot-builder-dotnet-add-rich-card-attachments.md)
- <a href="http://www.w3.org/Payments/" target="_blank">Webzahlungen bei W3C</a> 
- <a href="/dotnet/api/?view=botbuilder-3.11.0" target="_blank">Referenz zum Bot Builder SDK für .NET</a>