---
title: Anfordern von Zahlungen | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie mit dem Bot Builder SDK für Node.js eine Zahlungsanforderung senden.
author: v-ducvo
ms.author: v-ducvo
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 1e0c262c3a8dbef6430d51dab79a2d7c3cda938c
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39304197"
---
# <a name="request-payment"></a>Anfordern von Zahlungen
> [!div class="op_single_selector"]
> - [.NET](../dotnet/bot-builder-dotnet-request-payment.md)
> - [Node.js](../nodejs/bot-builder-nodejs-request-payment.md)

Wenn Ihr Bot Benutzern den Kauf von Artikeln ermöglicht, kann er durch Einbeziehen eines besonderen Schaltflächentyps in eine [Rich Card](bot-builder-nodejs-send-rich-cards.md) eine Zahlung anfordern. In diesem Artikel wird beschrieben, wie Sie mit dem Bot Builder SDK für Node.js eine Zahlungsanforderung senden.

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie mit dem Bot Builder SDK für Node.js eine Zahlungsanforderung senden können, müssen Sie die folgenden erforderlichen Aufgaben abschließen.

### <a name="register-and-configure-your-bot"></a>Registrieren und Konfigurieren des Bots

Aktualisieren Sie bei Ihrem Bot die Umgebungsvariablen für `MicrosoftAppId` und `MicrosoftAppPassword` auf die Werte für App-ID und Kennwort, die beim [Registrierungsvorgang](~/bot-service-quickstart-registration.md) für Ihren Bot generiert wurden. 

> [!NOTE]
> Wie Sie **AppID** und **AppPassword** für Ihren Bot finden, ist unter [MicrosoftAppID und MicrosoftAppPassword](~/bot-service-manage-overview.md#microsoftappid-and-microsoftapppassword) beschrieben.

### <a name="create-and-configure-merchant-account"></a>Erstellen und Konfigurieren eines Händlerkontos

1. <a href="https://dashboard.stripe.com/register" target="_blank">Erstellen und aktivieren Sie ein Stripe-Konto, wenn Sie noch keins haben.</a>

2. <a href="https://seller.microsoft.com/en-us/dashboard/registration/seller/?accountprogram=botframework" target="_blank">Melden Sie sich mit Ihrem Microsoft-Konto in Microsoft Seller Center an.</a>

3. Verbinden Sie in Seller Center Ihr Konto mit Stripe.

4. Navigieren Sie in Seller Center zum Dashboard, und kopieren Sie den Wert von **MerchantID**.

5. Aktualisieren Sie die Umgebungsvariable `PAYMENTS_MERCHANT_ID` auf den Wert, den Sie aus dem Dashboard in Seller Center kopiert haben. 

[!INCLUDE [Payment process overview](../includes/snippet-payment-process-overview.md)]

## <a name="payment-bot-sample"></a>Beispiel für einen Zahlungs-Bot

Das <a href="https://github.com/Microsoft/BotBuilder-Samples/tree/master/Node/sample-payments" target="_blank">Zahlungs-Bot</a>-Beispiel enthält einen Beispiel-Bot, der mithilfe von Node.js eine Zahlungsanforderung sendet. Um diesen Beispiel-Bot in Aktion zu sehen, können Sie ihn <a href="https://webchat.botframework.com/embed/paymentsample?s=d39Bk7JOMzQ.cwA.Rig.dumLki9bs3uqfWFMjXPn5PFnQVmT2VAVR1Zl1iPi07k" target="_blank">in einem Webchat ausprobieren</a>, <a href="https://join.skype.com/bot/9fbc0f17-43eb-40fe-bf3b-af151e6ce45e" target="_blank">als Skype-Kontakt hinzufügen</a> oder das Zahlungs-Bot-Beispiel herunterladen und lokal mit dem Bot Framework-Emulator ausführen. 

> [!NOTE]
> Um den End-to-End-Zahlungsvorgang mit dem **Zahlungs-Bot**-Beispiel im Webchat oder in Skype abschließen zu können, müssen Sie in Ihrem Microsoft-Konto eine gültige Kredit- oder Debitkarte (z. B. eine gültige Karte von einem Aussteller in den USA) angeben. Ihre Kreditkarte wird nicht belastet, und die Kreditkartenprüfnummer wird nicht überprüft, da das **Zahlungs-Bot**-Beispiel im Testmodus ausgeführt wird (d. h. `PAYMENTS_LIVEMODE` ist in **.env** auf `false` eingestellt).

In den nächsten Abschnitten dieses Artikels werden die drei Teile des Zahlungsvorgangs beschrieben (im Zusammenhang mit dem **Zahlungs-Bot**-Beispiel).

## <a id="request-payment"></a> Anfordern von Zahlungen

Ihr Bot kann von einem Benutzer eine Zahlung anfordern, indem er eine Nachricht mit einer [Rich Card](bot-builder-nodejs-send-rich-cards.md) mit einer Schaltfläche sendet, die eine Zahlung des Typs `type` angibt. Mit diesem Codeausschnitt aus dem **Zahlungs-Bot**-Beispiel wird eine Nachricht erstellt, die eine Hero Card mit der Schaltfläche **Kaufen** enthält, auf die der Benutzer klicken (oder tippen) kann, um den Zahlungsvorgang zu initiieren. 

[!code-javascript[Request payment](../includes/code/node-request-payment.js#requestPayment)]

In diesem Beispiel ist `payments.PaymentActionType` als Schaltflächentyp angegeben, den die App als „Zahlung“ definiert. Der Wert der Schaltfläche wird durch die Funktion `createPaymentRequest` ausgefüllt, die ein `PaymentRequest`-Objekt zurückgibt, das Informationen zu den unterstützten Zahlungsmethoden, Details und Optionen enthält. Weitere Informationen zu den Implementierungsdetails finden Sie unter **app.js** im <a href="https://github.com/Microsoft/BotBuilder-Samples/tree/master/Node/sample-payments" target="_blank">Zahlungs-Bot</a>-Beispiel.

Dieser Screenshot zeigt die Hero Card (mit der Schaltfläche **Kaufen**), die von dem oben genannten Codeausschnitt generiert wird. 
 
![Bot mit Zahlungsbeispiel](../media/payments-bot-buy.png) 

> [!IMPORTANT]
> Alle Benutzer mit Zugriff auf die Schaltfläche **Kaufen** können damit den Zahlungsvorgang initiieren. Im Kontext einer Gruppenunterhaltung ist es nicht möglich, eine Schaltfläche nur für die Verwendung durch einen bestimmten Benutzer festzulegen. 

## <a id="user-experience"></a> Benutzeroberfläche

Wenn ein Benutzer auf die Schaltfläche **Kaufen** klickt, wird er oder sie zur Zahlungsweboberfläche weitergeleitet, damit er dort alle erforderlichen Zahlungs-, Versand- und Kontaktinformationen über sein Microsoft-Konto bereitstellen kann. 

![Zahlung über Microsoft](../media/microsoft-payment.png)

### <a name="http-callbacks"></a>HTTP-Rückrufe

HTTP-Rückrufe werden an Ihren Bot gesendet, um anzugeben, dass bestimmte Vorgänge ausgeführt werden müssen. Jeder Rückruf ist ein Ereignis, das die folgenden Eigenschaftswerte enthält: 

| Eigenschaft | Wert |
|----|----|
| `type` | Aufrufen | 
| `name` | Gibt den Typ des Vorgangs an, den der Bot durchführen soll (z. B. Versandadresse aktualisieren, Versandoption aktualisieren, Zahlung vornehmen). | 
| `value` | Die Anforderungsnutzlast im JSON-Format. | 
| `relatesTo` |  Beschreibt den Kanal und den Benutzer, welche der Zahlungsanforderung zugeordnet sind. | 

> [!NOTE]
> `invoke` ist ein spezieller Ereignistyp, der für die Verwendung durch das Microsoft Bot Framework reserviert ist. Der Sender eines `invoke`-Ereignisses erwartet, dass Ihr Bot den Rückruf durch Senden einer HTTP-Antwort bestätigt.

## <a id="process-callbacks"></a> Verarbeiten von Rückrufen

[!INCLUDE [Process callbacks overview](../includes/snippet-payment-process-callbacks-overview.md)]

### <a name="shipping-address-update-and-shipping-option-update-callbacks"></a>Rückrufe zum Aktualisieren von Versandadresse und Versandoption

Beim Erhalt eines Rückrufs zum Aktualisieren von Versandadresse oder Versandoption werden Ihrem Bot in der `value`-Eigenschaft des Ereignisses die aktuellen Zahlungsdetails vom Kunden bereitgestellt.
Als Händler sollten Sie diese Rückrufe als statische, gegebene Zahlungseingabedetails behandeln, anhand der Sie einige Zahlungsausgabedetails berechnen, und die fehlschlagen, wenn der durch den Kunden bereitgestellte Eingabestatus aus irgendeinem Grund ungültig ist. Wenn der Bot ermittelt, dass die angegebenen Informationen im aktuellen Status gültig sind, sendet er zusammen mit den unveränderten Zahlungsdetails einfach den HTTP-Statuscode `200 OK`. Alternativ kann der Bot den HTTP-Statuscode `200 OK` mit aktualisierten Zahlungsdetails senden, die angewendet werden müssen, bevor der Auftrag verarbeitet werden kann. In einigen Fällen kann Ihr Bot bestimmen, dass die aktualisierten Informationen ungültig sind und der Auftrag im aktuellen Status nicht verarbeitet werden kann. Beispiel: Die Versandadresse eines Benutzers enthält ein Land, in das der Produktanbieter nicht versendet. In diesem Fall sendet der Bot wahrscheinlich den HTTP-Statuscode `200 OK` und eine Nachricht, mit der die Fehlereigenschaft des Objekts mit den Zahlungsdetails aufgefüllt wird. Das Senden eines HTTP-Statuscodes im Bereich `400` oder `500` führt zu einem allgemeinen Fehler für den Kunden.

### <a name="payment-complete-callbacks"></a>Rückrufe des Typs „Zahlung vornehmen“

Beim Empfang eines Rückrufs „Zahlung vornehmen“ werden Ihrem Bot in der `value`-Eigenschaft des Ereignisses eine Kopie der ersten, unveränderten Zahlungsanforderung sowie die Zahlungsantwortobjekte bereitgestellt. Ein Zahlungsantwortobjekt enthält die endgültige Auswahl durch den Kunden sowie ein Zahlungstoken. Ihr Bot sollte die Gelegenheit wahrnehmen, anhand der ersten Zahlungsanforderung und der Endauswahl des Kunden die endgültige Zahlungsanforderung neu zu berechnen. Vorausgesetzt, dass die Auswahl des Kunden als gültig ermittelt wurde, sollte der Bot den Betrag und die Währung in der Kopfzeile des Zahlungstokens überprüfen, um sicherzustellen, dass die Werte mit der letzten Zahlungsanforderung übereinstimmen.  Wenn der Bot entscheidet, den Kunden zu belasten, sollte nur der Betrag in der Kopfzeile des Zahlungstokens belastet werden, da dies der durch den Kunden bestätigte Preis ist. Wenn es zwischen den Werten, die der Bot erwartet und die er im Rückruf „Zahlung vornehmen“ empfangen hat, Abweichungen gibt, kann er durch Senden des HTTP-Statuscodes `200 OK` und Festlegen des Ergebnisfelds auf `failure` die Zahlungsanforderung als fehlerhaft kennzeichnen.   

Neben der Überprüfung der Zahlungsdetails sollte der Bot auch prüfen, ob der Auftrag ausgeführt werden kann, bevor er die Zahlungsverarbeitung initiiert. Beispielsweise sollte er sicherstellen, dass die Artikel, die gekauft werden, auch noch vorrätig sind. Wenn die Werte korrekt sind und Ihr Zahlungsabwickler das Zahlungstoken erfolgreich belastet hat, sollte der Bot mit dem HTTP-Statuscode `200 OK` antworten und das Ergebnisfeld auf `success` festlegen, damit auf der Zahlungsweboberfläche die Zahlungsbestätigung angezeigt werden kann. Das Zahlungstoken, das der Bot empfängt, kann nur einmal von dem anfordernden Händler verwendet werden und muss an Stripe (der einzige Zahlungsabwickler, der das Bot Framework derzeit unterstützt) übermittelt werden. Das Senden eines HTTP-Statuscodes im Bereich `400` oder `500` führt zu einem allgemeinen Fehler für den Kunden.

Im folgenden Codeausschnitt aus dem **Zahlungs-Bot**-Beispiel werden die Rückrufe verarbeitet, die der Bot empfängt. 

[!code-javascript[Request payment](../includes/code/node-request-payment.js#processCallback)]

In diesem Beispiel untersucht der Bot die `name`-Eigenschaft des eingehenden Ereignisses, um den Typ des auszuführenden Vorgangs zu bestimmen, und ruft dann die entsprechende(n) Methode(n) auf, um den Rückruf zu verarbeiten. Weitere Informationen zu den Implementierungsdetails finden Sie unter **app.js** im <a href="https://github.com/Microsoft/BotBuilder-Samples/tree/master/Node/sample-payments" target="_blank">Zahlungs-Bot</a>-Beispiel.

## <a name="testing-a-payment-bot"></a>Testen eines Zahlungs-Bots

[!INCLUDE [Test a payment bot](../includes/snippet-payment-test-bot.md)]

Im <a href="https://github.com/Microsoft/BotBuilder-Samples/tree/master/Node/sample-payments" target="_blank">Zahlungs-Bot</a>-Beispiel bestimmt die Umgebungsvariable `PAYMENTS_LIVEMODE` in **.env**, ob Rückrufe des Typs „Zahlung vornehmen“ emulierte oder echte Zahlungstoken enthalten. Wenn `PAYMENTS_LIVEMODE` auf `false` festgelegt ist, wird der ausgehenden Zahlungsanforderung des Bots eine Kopfzeile hinzugefügt, die angibt, dass sich der Bot im Testmodus befindet, und der Rückruf „Zahlung vornehmen“ enthält ein emuliertes Zahlungstoken, das nicht belastet werden kann. Wenn `PAYMENTS_LIVEMODE` auf `true` festgelegt ist, fehlt die Kopfzeile mit dem Hinweis auf den Testmodus in der ausgehenden Zahlungsanforderung des Bots, und der Rückruf „Zahlung vornehmen“ enthält ein echtes Zahlungstoken, das der Bot an Stripe zur Zahlungsabwicklung sendet. Dies ist dann eine echte Transaktion, die in einer Belastung des angegebenen Zahlungsinstruments resultiert. 

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- <a href="https://github.com/Microsoft/BotBuilder-Samples/tree/master/Node/sample-payments" target="_blank">Beispiel für einen Zahlungs-Bot</a>
- [Hinzufügen von Rich Cards-Anlagen zu Nachrichten](bot-builder-nodejs-send-rich-cards.md)
- <a href="http://www.w3.org/Payments/" target="_blank">Webzahlungen bei W3C</a> 
