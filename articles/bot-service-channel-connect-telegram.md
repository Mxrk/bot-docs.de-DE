---
title: Erstellen eines Bots für Telegram | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie die Verbindung eines Bots mit Telegram konfigurieren.
keywords: Bot konfigurieren, Telegram, Bot-Kanal, Telegram-Bot, Zugriffstoken
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
ms.openlocfilehash: ccd6bbbf30a9af83d6c687ee68f94d2f31c1c9cd
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39301888"
---
# <a name="connect-a-bot-to-telegram"></a>Herstellen der Verbindung eines Bots mit Telegram

Sie können Ihren Bot so konfigurieren, dass er mit Personen kommuniziert, die die Telegram-Messaging-App verwenden.

[!INCLUDE [Channel Inspector intro](~/includes/snippet-channel-inspector.md)]

## <a name="visit-the-bot-father-to-create-a-new-telegram-bot"></a>Besuchen des Bot Father, um einen neuen Telegram-Bot zu erstellen

<a href="https://telegram.me/botfather" target="_blank">Erstellen Sie einen neuen Telegram-Bot</a> mithilfe des Bot Father.

![Besuchen des Bot Father](~/media/channels/tg-StepVisitBotFather.png)

## <a name="create-a-new-telegram-bot"></a>Erstellen eines neuen Telegram-Bots
Um einen neuen Telegram-Bot zu erstellen, senden Sie den Befehl `/newbot`.

![Erstellen eines neuen Bots](~/media/channels/tg-StepNewBot.png)

### <a name="specify-a-friendly-name"></a>Eingeben eines Anzeigenamens

Geben Sie dem Telegram-Bot einen Anzeigenamen.

![Vergabe eines Anzeigenamens an den Bot](~/media/channels/tg-StepNameBot.png)

### <a name="specify-a-username"></a>Angeben eines Benutzernamens

Geben Sie dem Telegram-Bot einen eindeutigen Benutzernamen.

![Vergabe eines eindeutigen Namens an den Bot](~/media/channels/tg-StepUsername.png)

### <a name="copy-the-access-token"></a>Kopieren des Zugriffstokens

Kopieren Sie das Zugriffstoken des Telegram-Bots.

![Kopieren des Zugriffstokens](~/media/channels/tg-StepBotCreated.png)

## <a name="enter-the-telegram-bots-access-token"></a>Eingeben des Zugriffstokens des Telegram-Bots

Fügen Sie das Token, das Sie zuvor kopiert haben, in das Feld **Zugriffstoken** ein, und klicken Sie auf **Senden**.

## <a name="enable-the-bot"></a>Aktivieren des Bots
Aktivieren Sie **Diesen Bot für Telegram aktivieren**. Klicken Sie dann auf die Option zum **Beenden der Telegram-Konfiguration**.

Sobald Sie diese Schritte ausgeführt haben, ist Ihr Bot erfolgreich für die Kommunikation mit Benutzern in Telegram konfiguriert.
