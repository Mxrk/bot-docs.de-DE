---
title: Verbinden eines Bots mit GroupMe | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie die Verbindung eines Bots mit GroupMe konfigurieren.
keywords: bot channel, GroupMe, create GroupMe, credentials
author: RobStand
ms.author: RobStand
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 12/13/2017
ms.openlocfilehash: a2004293ff10cfbc7132f58b7c0c834a2012cfd1
ms.sourcegitcommit: b78fe3d8dd604c4f7233740658a229e85b8535dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2018
ms.locfileid: "49998567"
---
# <a name="connect-a-bot-to-groupme"></a>Verbinden eines Bots mit GroupMe

Sie können einen Bot so konfigurieren, dass er mit Personen kommuniziert, die die GroupMe-Messaging-App verwenden.

[!INCLUDE [Channel Inspector intro](~/includes/snippet-channel-inspector.md)]

## <a name="sign-up-for-a-groupme-account"></a>Registrieren für ein GroupMe-Konto

Falls Sie noch kein GroupMe-Konto besitzen, [registrieren Sie sich für ein neues Konto](https://web.groupme.com/signup).

## <a name="create-a-groupme-application"></a>Erstellen einer GroupMe-Anwendung

[Erstellen Sie eine GroupMe-Anwendung](https://dev.groupme.com/applications/new) für Ihren Bot.

Verwenden Sie die folgende Rückruf-URL: `https://groupme.botframework.com/Home/Login`

![App erstellen](~/media/channels/GM-StepApp.png)

## <a name="gather-credentials"></a>Erfassen von Anmeldeinformationen

1. Kopieren Sie im Feld **Umleitungs-URL** den Wert nach **client_id=**.
2. Kopieren Sie den Wert von **Zugriffstoken**.

![Kopieren der Client-ID und des Zugriffstokens](~/media/channels/GM-StepClientId.png)


## <a name="submit-credentials"></a>Senden von Anmeldeinformationen

1. Fügen Sie unter „dev.botframework.com“ den Wert von **client_id**, den Sie gerade kopiert haben, in das Feld **Client-ID** ein.
2. Fügen Sie den Wert von **Zugriffstoken** in das Feld **Zugriffstoken** ein.
2. Klicken Sie auf **Speichern**.

![Eingeben von Anmeldeinformationen](~/media/channels/GM-StepClientIDToken.png)
