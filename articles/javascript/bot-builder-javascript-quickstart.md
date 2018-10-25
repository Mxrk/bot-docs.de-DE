---
title: Erstellen eines Bots mit dem Bot Builder SDK für JavaScript | Microsoft Docs
description: Erstellen Sie schnell einen Bot mithilfe des Bot Builder SDK für JavaScript.
keywords: Schnellstart, Bot Builder SDK, erste Schritte
author: jonathanfingold
ms.author: jonathanfingold
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 10/15/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: aa13889cea2a26bf094a919f5d05905d65f7661f
ms.sourcegitcommit: b78fe3d8dd604c4f7233740658a229e85b8535dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2018
ms.locfileid: "49998854"
---
# <a name="create-a-bot-with-the-bot-builder-sdk-for-javascript"></a>Erstellen eines Bots mit dem Bot Builder SDK für JavaScript

[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

Dieser Schnellstart führt Sie durch die Erstellung eines Bots, wobei Sie den Yeoman Bot Builder-Generator und das Bot Builder SDK für JavaScript verwenden und dann den Bot mit dem Bot Framework-Emulator testen. 

## <a name="prerequisites"></a>Voraussetzungen

- [Visual Studio Code](https://www.visualstudio.com/downloads)
- [Node.js](https://nodejs.org/)
- [Yeoman](http://yeoman.io/): Kann einen Generator verwenden, um einen Bot für Sie zu erstellen.
- [git](https://git-scm.com/)
- [Bot Framework-Emulator](https://github.com/Microsoft/BotFramework-Emulator)
- Kenntnisse von [restify](http://restify.com/) und asynchroner Programmierung in JavaScript

> [!NOTE]
> Bei einigen Installationen gibt der Installationsschritt für restify einen Fehler in Bezug auf node-gyp aus.
> Wenn dies der Fall ist, versuchen Sie, `npm install -g windows-build-tools` auszuführen.

## <a name="create-a-bot"></a>Erstellen eines Bots

Öffnen Sie eine Eingabeaufforderung mit erhöhten Rechten, erstellen Sie ein Verzeichnis, und initialisieren Sie das Paket für Ihren Bot.

```bash
md myJsBots
cd myJsBots
```

Stellen Sie sicher, dass Ihre Version von npm aktuell ist.
```bash
npm i npm
```

Dann installieren Sie Yeoman und den Generator für JavaScript.

```bash
npm install -g yo
npm install -g generator-botbuilder
```

Verwenden Sie anschließend den Generator, um einen Echobot zu erstellen.

```bash
yo botbuilder
```

Yeoman fordert Sie zur Eingabe einiger Informationen auf, um Ihren Bot zu erstellen.

- Geben Sie einen Namen für Ihren Bot ein.
- Geben Sie eine Beschreibung ein.
- Wählen Sie die Sprache für Ihren Bot aus: `JavaScript` oder `TypeScript`.
- Wählen Sie die Vorlage `Echo` aus.

Dank der Vorlage enthält Ihr Projekt sämtlichen Code, der zum Erstellen des Bots in dieser Schnellstartanleitung erforderlich ist. Sie müssen tatsächlich keinen zusätzlichen Code schreiben.

> [!NOTE]
> Für einen Basisbot benötigen Sie ein LUIS-Sprachmodell. Sie können dieses Modell unter [luis.ai](https://www.luis.ai) erstellen. Aktualisieren Sie die BOT-Datei, nachdem Sie das Modell erstellt haben. Ihre BOT-Datei sollte [dieser Datei](../v4sdk/bot-builder-service-file.md) ähneln. 

## <a name="start-your-bot"></a>Starten Ihres Bots

Wechseln Sie in PowerShell/Bash in das Verzeichnis, das Sie für Ihren Bot erstellt haben, und starten Sie ihn mit `npm start`. Zu diesem Zeitpunkt wird Ihr Bot lokal ausgeführt.

## <a name="start-the-emulator-and-connect-your-bot"></a>Starten des Emulators und Herstellen einer Verbindung mit Ihrem Bot
1. Starten Sie den Emulator.
2. Klicken Sie im Emulator auf der Registerkarte „Willkommen“ auf den Link **Bot öffnen**.
3. Wählen Sie in dem Verzeichnis, in dem Sie das Projekt erstellt haben, die BOT-Datei aus.

Senden Sie eine Nachricht an den Bot, und der Bot antwortet mit einer Nachricht.
![Ausgeführter Emulator](../media/emulator-v4/emulator-running.png)

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Funktionsweise von Bots](../v4sdk/bot-builder-basics.md) 
