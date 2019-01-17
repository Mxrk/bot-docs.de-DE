---
ms.openlocfilehash: 04b015963b8ea991b87f085dd5d6aa0110c50a18
ms.sourcegitcommit: bdb981c0b11ee99d128e30ae0462705b2dae8572
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/17/2019
ms.locfileid: "54360810"
---
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
   mkdir myJsBots
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
