---
title: Verbinden eines Bots mit Twilio | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie die Verbindung eines Bots mit Twilio konfigurieren.
keywords: Twilio, Botkanäle, SMS, App, Telefon, Twilio konfigurieren, Cloudkommunikation, Text
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 10/9/2018
ms.openlocfilehash: 7d7416940ccad4e62c98f4a386dac43189301b56
ms.sourcegitcommit: b78fe3d8dd604c4f7233740658a229e85b8535dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2018
ms.locfileid: "49998305"
---
# <a name="connect-a-bot-to-twilio"></a>Verbinden eines Bots mit Twilio

Sie können Ihren Bot so konfigurieren, dass er mit Personen kommuniziert, die als Plattform für die Cloudkommunikation Twilio verwenden.

## <a name="log-in-to-or-create-a-twilio-account-for-sending-and-receiving-sms-messages"></a>Anmelden bei einem Twilio-Konto oder Erstellen eines Kontos für das Senden und Empfangen von SMS-Nachrichten

Wenn Sie kein Twilio-Konto besitzen, können Sie <a href="https://www.twilio.com/try-twilio" target="_blank">ein neues Konto erstellen</a>.

## <a name="create-a-twiml-application"></a>Erstellen einer TwiML-Anwendung

<a href="https://support.twilio.com/hc/en-us/articles/223180928-How-Do-I-Create-a-TwiML-App-" target="_blank">Erstellen Sie eine TwiML-Anwendung</a>, indem Sie die folgende Anleitung befolgen.

![Erstellen einer App](~/media/channels/twi-StepTwiml.png)

Geben Sie unter **Eigenschaften** einen **ANZEIGENAMEN** ein. In diesem Tutorial verwenden wir „My TwiML app“ als Beispiel. Die **ANFORDERUNGS-URL** unter „Voice“ (Sprache) kann leer gelassen werden. Unter **Messaging** sollte die **Anforderungs-URL** auf `https://sms.botframework.com/api/sms` festgelegt werden.

## <a name="select-or-add-a-phone-number"></a>Auswählen oder Hinzufügen einer Telefonnummer

Befolgen Sie <a href = "https://support.twilio.com/hc/en-us/articles/223180048-Adding-a-Verified-Phone-Number-or-Caller-ID-with-Twilio" target="_blank">diese Anleitung</a>, um über die Konsolenwebsite eine verifizierte Anrufer-ID hinzuzufügen. Wenn Sie fertig sind, wird Ihre verifizierte Nummer im Abschnitt **Active Numbers** (Aktive Nummern) unter **Manage Numbers** (Nummern verwalten) angezeigt.

![Festlegen einer Telefonnummer](~/media/channels/twi-StepPhone.png)

## <a name="specify-application-to-use-for-voice-and-messaging"></a>Angeben der Anwendung für Sprachfunktion (Voice) und Messaging

Klicken Sie auf die Nummer, und navigieren Sie zu **Konfigurieren**. Legen Sie unter „Voice“ und „Messaging“ jeweils die Option **CONFIGURE WITH** (KONFIGURIEREN MIT) auf „TwiML App“ und **TWIML APP** auf „My TwiML app“ fest. Klicken Sie auf **Speichern**, wenn Sie fertig sind.

![Angeben der Anwendung](~/media/channels/twi-StepPhone2.png)

Navigieren Sie zurück zu **Manage Numbers** (Nummern verwalten). Sie sehen, dass die Konfiguration für „Voice“ und „Messaging“ in „TwiML App“ geändert wurde.

![Angegebene Nummer](~/media/channels/twi-StepPhone3.png)


## <a name="gather-credentials"></a>Erfassen von Anmeldeinformationen

Navigieren Sie zurück zur [Startseite der Konsole](https://www.twilio.com/console/). Sie sehen, dass Ihre Konto-SID (Account SID) und das Authentifizierungstoken (Auth Token) im Projektdashboard angezeigt werden (unten dargestellt).

![Erfassen von App-Anmeldeinformationen](~/media/channels/twi-StepAuth.png)

## <a name="submit-credentials"></a>Senden von Anmeldeinformationen

Rufen Sie in einem separaten Fenster die Bot Framework-Website unter https://dev.botframework.com/ auf. 

- Klicken Sie auf **My bots** (Meine Bots), und wählen Sie den Bot aus, für den eine Verbindung mit Twilio hergestellt werden soll. Sie werden zum Azure-Portal geleitet.
- Wählen Sie unter **Bot Management** (Botverwaltung) die Option **Channels** (Kanäle). Klicken Sie auf das Symbol „Twilio (SMS)“.
- Geben Sie die Telefonnummer, die Konto-SID und das Authentifizierungstoken ein, die Sie sich zuvor notiert haben. Klicken Sie auf **Speichern**, wenn Sie fertig sind.

![Senden von Anmeldeinformationen](~/media/channels/twi-StepSubmit.png)

Sobald Sie diese Schritte ausgeführt haben, ist Ihr Bot erfolgreich für die Kommunikation mit Benutzern, die Twilio verwenden, konfiguriert.