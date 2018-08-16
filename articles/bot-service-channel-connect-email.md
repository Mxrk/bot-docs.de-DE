---
title: Verbinden eines Bots mit Office 365-E-Mails | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie einen Bot zum Senden und Empfangen von E-Mails mit Office 365 konfigurieren.
keywords: Office 365, Botkanäle, E-Mail, E-Mail-Anmeldeinformationen, Azure-Portal, benutzerdefinierte E-Mail
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
ms.openlocfilehash: a8c3d4fb469ce7c52dfffbcfc3a17e08c167ea66
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39303493"
---
# <a name="connect-a-bot-to-office-365-email"></a>Verbinden eines Bots mit Office 365-E-Mails

Zusätzlich zu anderen [Kanälen](~/bot-service-manage-channels.md) können Bots mit Benutzern über Office 365-E-Mails kommunizieren. Wenn ein Bot für den Zugriff auf ein E-Mail-Konto konfiguriert ist, empfängt er eine Nachricht, sobald eine neue E-Mail eintrifft. Der Bot kann dann auf die Weise antworten, die durch die Geschäftslogik festgelegt ist. Beispielsweise kann der Bot eine E-Mail-Antwort senden, in der der Empfang einer E-Mail mit folgender Nachricht bestätigt wird: „Hallo, vielen Dank für Ihren Auftrag! Wir werden sofort mit der Bearbeitung beginnen.“

> [!WARNING]
> Es verstößt gegen den Bot Framework-[Verhaltenskodex](https://www.botframework.com/Content/Microsoft-Bot-Framework-Preview-Online-Services-Agreement.htm), „Spambots“ zu erstellen. Dazu gehören Bots, die unerwünschte oder nicht angeforderte Massen-E-Mails senden.

## <a name="configure-email-credentials"></a>Konfigurieren von E-Mail-Anmeldeinformationen

Sie können einen Bot mit dem E-Mail-Kanal verbinden, indem Sie Office 365-Anmeldeinformationen in der Konfiguration des E-Mail-Kanals eingeben.
Eine Verbundauthentifizierung mithilfe eines beliebigen Anbieters, die AAD ersetzt, wird nicht unterstützt.

> [!NOTE]
> Sie sollten nicht Ihre eigenen persönlichen E-Mail-Konten für Bots verwenden, da jede an dieses E-Mail-Konto gesendete Nachricht an den Bot weitergeleitet wird. Dies kann dazu führen, dass der Bot eine unangemessene Antwort an einen Absender sendet. Aus diesem Grund sollten Bots nur dedizierte Office 365-E-Mail-Konten verwenden.

Zum Hinzufügen des E-Mail-Kanals öffnen Sie den Bot im [Azure-Portal](https://portal.azure.com/), klicken Sie auf das Blatt **Kanäle**, und klicken Sie dann auf **E-Mail**. Geben Sie Ihre gültigen E-Mail-Anmeldeinformationen ein, und klicken Sie auf **Speichern**.

![Eingeben von E-Mail-Anmeldeinformationen](~/media/bot-service-channel-connect-email/bot-service-channel-connect-email-credentials.png)

Der E-Mail-Kanal funktioniert zurzeit nur mit Office 365. Andere E-Mail-Dienste werden derzeit nicht unterstützt.

## <a name="customize-emails"></a>Anpassen von E-Mails

Der E-Mail-Kanal unterstützt das Senden benutzerdefinierter Eigenschaften, um erweiterte, angepasste E-Mails mithilfe der `channelData`-Eigenschaft zu erstellen.

[!INCLUDE [Email channelData table](~/includes/snippet-channelData-email.md)]

Die folgende Beispielnachricht zeigt eine JSON-Datei, die diese `channelData`-Eigenschaften enthält.

```json
{
    "type": "message",
    "locale": "en-Us",
    "channelID": "email",
    "from": { "id": "mybot@mydomain.com", "name": "My bot"},
    "recipient": { "id": "joe@otherdomain.com", "name": "Joe Doe"},
    "conversation": { "id": "123123123123", "topic": "awesome chat" },
    "channelData":
    {
        "htmlBody": "<html><body style = /"font-family: Calibri; font-size: 11pt;/" >This is more than awesome.</body></html>",
        "subject": "Super awesome message subject",
        "importance": "high",
        "ccRecipients": "Yasemin@adatum.com;Temel@adventure-works.com"
    }
}
```

::: moniker range="azure-bot-service-3.0"
Weitere Informationen zur Verwendung von `channelData` finden Sie im [Node.js](https://github.com/Microsoft/BotBuilder-Samples/tree/master/Node/core-ChannelData)-Beispiel oder in der [.NET](~/dotnet/bot-builder-dotnet-channeldata.md)-Dokumentation.
::: moniker-end

::: moniker range="azure-bot-service-4.0"
Weitere Informationen zur Verwendung von `channelData` finden Sie im [Node.js](https://github.com/Microsoft/BotBuilder-Samples/tree/master/Node/core-ChannelData)-Beispiel oder in der [.NET](~/v4sdk/bot-builder-channeldata.md)-Dokumentation.
::: moniker-end

## <a name="additional-resources"></a>Zusätzliche Ressourcen

<!-- Put whole list in monikers, even though it's just the second item that needs to be different. -->
::: moniker range="azure-bot-service-3.0"
* Verbinden eines Bots mit [Kanälen](~/bot-service-manage-channels.md)
* [Implementieren von kanalspezifischer Funktionalität](dotnet/bot-builder-dotnet-channeldata.md) mit dem Bot Builder SDK für .NET
* Verwenden des [Kanalinspektors](bot-service-channel-inspector.md) zur Feststellung, wie ein Kanal eine bestimmte Funktion Ihrer Botanwendung rendert
::: moniker-end
::: moniker range="azure-bot-service-4.0"
* Verbinden eines Bots mit [Kanälen](~/bot-service-manage-channels.md)
* [Implementieren von kanalspezifischer Funktionalität](~/v4sdk/bot-builder-channeldata.md) mit dem Bot Builder SDK für .NET
* Verwenden des [Kanalinspektors](bot-service-channel-inspector.md) zur Feststellung, wie ein Kanal eine bestimmte Funktion Ihrer Botanwendung rendert
::: moniker-end
