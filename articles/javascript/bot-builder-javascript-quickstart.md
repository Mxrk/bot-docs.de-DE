---
title: Erstellen eines Bots mit dem Bot Builder SDK für JavaScript | Microsoft Docs
description: Erstellen Sie schnell einen Bot mithilfe des Bot Builder SDK für JavaScript.
keywords: Schnellstart, Bot Builder SDK, erste Schritte
author: jonathanfingold
ms.author: v-jofing
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 10/30/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 1804f7bb6a19fbe0cfced49cffa4e9d6d77005eb
ms.sourcegitcommit: f7a8f05fc05ff4a7212a437d540485bf68831604
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/21/2018
ms.locfileid: "53735930"
---
# <a name="create-a-bot-with-the-bot-builder-sdk-for-javascript"></a>Erstellen eines Bots mit dem Bot Builder SDK für JavaScript

[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

Dieser Schnellstart führt Sie durch die Erstellung eines einzelnen Bots mit dem Yeoman Bot Builder-Generator und dem Bot Builder SDK für JavaScript sowie das anschließende Testen des Bots mit dem Bot Framework-Emulator.

## <a name="prerequisites"></a>Voraussetzungen

- [Visual Studio Code](https://www.visualstudio.com/downloads)
- [Node.js](https://nodejs.org/)
- [Yeoman](http://yeoman.io/) (verwendet einen Generator, um einen Bot für Sie zu erstellen)
- [git](https://git-scm.com/)
- [Bot Framework-Emulator](https://github.com/Microsoft/BotFramework-Emulator)
- Kenntnisse von [restify](http://restify.com/) und asynchroner Programmierung in JavaScript

> [!NOTE]
> Bei einigen Installationen gibt der Installationsschritt für restify einen Fehler in Bezug auf node-gyp aus.
> Wenn dies der Fall ist, versuchen Sie den folgenden Befehl mit erhöhten Berechtigungen auszuführen:
> ```bash
> npm install -g windows-build-tools
> ```

## <a name="create-a-bot"></a>Erstellen eines Bots

So erstellen Sie Ihren Bot und initialisieren Sie die zugehörigen Pakete

1. Öffnen Sie ein Terminalfenster oder eine Eingabeaufforderung mit erhöhten Rechten.
1. Falls Sie noch kein Verzeichnis für Ihre JavaScript-Bots haben, erstellen Sie eines, und wechseln Sie in dieses Verzeichnis. (Wir erstellen ein allgemeines Verzeichnis für Ihre JavaScript-Bots, obwohl wir in diesem Tutorial nur einen Bot erstellen.)

   ```bash
   md myJsBots
   cd myJsBots
   ```

1. Stellen Sie sicher, dass Ihre Version von npm aktuell ist.

   ```bash
   npm install -g npm
   ```

1. Dann installieren Sie Yeoman und den Generator für JavaScript.

   ```bash
   npm install -g yo generator-botbuilder
   ```

1. Verwenden Sie anschließend den Generator, um einen Echobot zu erstellen.

   ```bash
   yo botbuilder
   ```

Yeoman fordert Sie zur Eingabe einiger Informationen auf, um Ihren Bot zu erstellen. Verwenden Sie für dieses Tutorial die Standardwerte.

- Geben Sie einen Namen für Ihren Bot ein. (myChatBot)
- Geben Sie eine Beschreibung ein. (Zeigen Sie die Kernfunktionen von Microsoft Bot Framework auf.)
- Wählen Sie die Sprache für Ihren Bot aus. (JavaScript)
- Wählen Sie die zu verwendende Vorlage aus. (Echo)

Dank der Vorlage enthält Ihr Projekt sämtlichen Code, der zum Erstellen des Bots in dieser Schnellstartanleitung erforderlich ist. Sie müssen tatsächlich keinen zusätzlichen Code schreiben.

> [!NOTE]
> Wenn Sie einen `Basic`-Bot erstellen, benötigen Sie ein LUIS-Sprachmodell (Language Understanding). Sie können dieses Modell unter [luis.ai](https://www.luis.ai) erstellen. Aktualisieren Sie die BOT-Datei, nachdem Sie das Modell erstellt haben. Ihre BOT-Datei sollte [dieser Datei](../v4sdk/bot-builder-service-file.md) ähneln.

## <a name="start-your-bot"></a>Starten Ihres Bots

Wechseln Sie in einem Terminalfenster oder einer Eingabeaufforderung in das Verzeichnis, das Sie für Ihren Bot erstellt haben, und starten Sie ihn mit `npm start`. Zu diesem Zeitpunkt wird Ihr Bot lokal ausgeführt.

## <a name="start-the-emulator-and-connect-your-bot"></a>Starten des Emulators und Herstellen einer Verbindung mit Ihrem Bot

1. Starten Sie den Bot Framework-Emulator.
2. Klicken Sie im Emulator auf der Registerkarte „Willkommen“ auf den Link **Bot öffnen**.
3. Wählen Sie in dem Verzeichnis, in dem Sie das Projekt erstellt haben, die BOT-Datei aus.

Senden Sie eine Nachricht an den Bot, und der Bot antwortet mit einer Nachricht.
![Ausgeführter Emulator](../media/emulator-v4/js-quickstart.png)

## <a name="additional-resources"></a>Zusätzliche Ressourcen

Informationen zum Herstellen einer Verbindung mit einem remote gehosteten Bot finden Sie unter [Tunneling (ngrok)](https://github.com/Microsoft/BotFramework-Emulator/wiki/Tunneling-(ngrok)).

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Funktionsweise von Bots](../v4sdk/bot-builder-basics.md)
