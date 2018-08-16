---
title: Verbinden eines Bots mit Twilio | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie die Verbindung eines Bots mit Twilio konfigurieren.
keywords: Twilio, Botkanäle, SMS, App, Telefon, Twilio konfigurieren, Cloudkommunikation, Text
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
ms.openlocfilehash: 7e09126d50cfbebfc0aad0ee7fcb71b4e7551a7d
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39301217"
---
# <a name="connect-a-bot-to-twilio"></a>Verbinden eines Bots mit Twilio

Sie können Ihren Bot so konfigurieren, dass er mit Personen kommuniziert, die als Plattform für die Cloudkommunikation Twilio verwenden.

## <a name="log-in-to-or-create-a-twilio-account-for-sending-and-receiving-sms-messages"></a>Anmelden bei einem Twilio-Konto oder Erstellen eines Kontos für das Senden und Empfangen von SMS-Nachrichten

Wenn Sie kein Twilio-Konto besitzen, <a href="https://www.twilio.com/try-twilio" target="_blank">erstellen Sie ein neues Konto</a>.

## <a name="create-a-twiml-application"></a>Erstellen einer TwiML-Anwendung

<a href="https://www.twilio.com/user/account/messaging/dev-tools/twiml-apps/add" target="_blank">Erstellen einer TwiML-Anwendung</a>

![Erstellen einer App](~/media/channels/twi-StepTwiml.png)

 Der Anforderungs-URL unter „Messaging“ sollte **https://sms.botframework.com/api/sms** lauten.

## <a name="select-or-add-a-phone-number"></a>Auswählen oder Hinzufügen einer Telefonnummer

<a href="https://www.twilio.com/user/account/phone-numbers/incoming" target="_blank">Wählen Sie eine Telefonnummer aus, oder fügen Sie eine hinzu.</a> Klicken Sie auf die Nummer, um sie der erstellten TwiML-Anwendung hinzuzufügen.

![Festlegen einer Telefonnummer](~/media/channels/twi-StepPhone.png)

## <a name="specify-application-to-use-for-messaging"></a>Festlegen einer Anwendung für das Messaging
Legen Sie im Abschnitt **Messaging** die **TwiML-App** auf den Namen der TwiML-App fest, die Sie gerade erstellt haben.
Kopieren Sie die **Telefonnummer** zur späteren Verwendung.

![Angeben einer App](~/media/channels/twi-StepPhone2.png)

## <a name="gather-credentials"></a>Erfassen von Anmeldeinformationen

<a href="https://www.twilio.com/user/account/settings" target="_blank">Geben Sie die Anmeldeinformationen an</a>, und klicken Sie dann auf das Augensymbol, um das Authentifizierungstoken anzuzeigen.

![Erfassen von App-Anmeldeinformationen](~/media/channels/twi-StepAuth.png)

## <a name="submit-credentials"></a>Senden von Anmeldeinformationen

Geben Sie die zuvor kopierten Werte für Telefonnummer, accountSID und Authentifizierungstoken ein, und klicken Sie auf **Submit Twilio Credentials** (Twilio-Anmeldeinformationen senden).

## <a name="enable-the-bot"></a>Aktivieren des Bots
Aktivieren Sie **Enable this bot on SMS** (Diesen Bot für SMS aktivieren). Klicken Sie dann auf die Option zum **Beenden der SMS-Konfiguration**.

Sobald Sie diese Schritte ausgeführt haben, ist Ihr Bot erfolgreich für die Kommunikation mit Benutzern, die Twilio verwenden, konfiguriert.

