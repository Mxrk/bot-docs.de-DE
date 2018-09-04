---
title: Erstellen eines Bots mit dem Bot Builder SDK für JavaScript | Microsoft Docs
description: Erstellen Sie schnell einen Bot mithilfe des Bot Builder SDK für JavaScript.
keywords: Schnellstart, Bot Builder SDK, erste Schritte
author: jonathanfingold
ms.author: jonathanfingold
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 07/12/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 21a9aa5b1d108b5d03b108278a81229e16b5bc99
ms.sourcegitcommit: a2f3d87c0f252e876b3e63d75047ad1e7e110b47
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2018
ms.locfileid: "42928126"
---
# <a name="create-a-bot-with-the-bot-builder-sdk-v4-preview-for-javascript"></a>Erstellen eines Bots mit dem Bot Builder SDK v4 (Vorschau) für JavaScript

[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

Dieser Schnellstart führt Sie durch die Erstellung eines Bots, wobei Sie den Yeoman Bot Builder-Generator und das Bot Builder SDK für JavaScript verwenden und dann den Bot mit dem Bot Framework-Emulator testen. Dieser Schnellstart basiert auf dem [Microsoft Bot Builder SDK v4](https://github.com/Microsoft/botbuilder-js).

## <a name="prerequisites"></a>Voraussetzungen

- [Visual Studio Code](https://www.visualstudio.com/downloads)
- [Node.js](https://nodejs.org/en/)
- [Yeoman](http://yeoman.io/): Kann einen Generator verwenden, um einen Bot für Sie zu erstellen.
- [Bot-Emulator](https://github.com/Microsoft/BotFramework-Emulator)
- Kenntnisse von [restify](http://restify.com/) und asynchroner Programmierung in JavaScript

> [!NOTE]
> Bei einigen Installationen gibt der Installationsschritt für restify einen Fehler in Bezug auf node-gyp aus.
> Wenn dies der Fall ist, versuchen Sie, `npm install -g windows-build-tools` auszuführen.

Das Bot Builder SDK für JavaScript besteht aus einer Reihe von [Paketen](https://github.com/Microsoft/botbuilder-js/tree/master/libraries), die von npm mit einem speziellen `@preview`-Tag installiert werden können.

## <a name="create-a-bot"></a>Erstellen eines Bots

Öffnen Sie eine Eingabeaufforderung mit erhöhten Rechten, erstellen Sie ein Verzeichnis, und initialisieren Sie das Paket für Ihren Bot.

```bash
md myJsBots
cd myJsBots
```

Dann installieren Sie Yeoman und den Generator für JavaScript.

```bash
npm install -g yo
npm install -g generator-botbuilder@preview
```

Verwenden Sie anschließend den Generator, um einen Echobot zu erstellen.

```bash
yo botbuilder
```

Yeoman fordert Sie zur Eingabe einiger Informationen auf, um Ihren Bot zu erstellen.

- Geben Sie einen Namen für Ihren Bot ein.
- Geben Sie eine Beschreibung ein.
- Wählen Sie die Sprache für Ihren Bot aus: `JavaScript` oder `TypeScript`.
- Wählen Sie die zu verwendende Vorlage aus. Zurzeit ist `Echo` die einzige Vorlage. Weitere Vorlagen werden jedoch bald hinzugefügt.

Yeoman erstellt Ihren Bot in einem neuen Ordner.

## <a name="explore-code"></a>Untersuchen des Codes

Wenn Sie Ihren neu erstellten Botordner öffnen, sehen Sie eine Datei `app.js`. Diese Datei `app.js` enthält den gesamten erforderlichen Code zum Ausführen einer Bot-App. Diese Datei enthält einen Echobot, der alles zurückgibt, was Sie eingeben, und einen Zähler inkrementiert.

Im folgenden Code verwendet die Unterhaltungszustandsmiddleware In-Memory-Speicher. Sie liest und schreibt das Zustandsobjekt in den Speicher. Die count-Variable verfolgt die Anzahl der an den Bot gesendeten Nachrichten nach. Sie können ein ähnliches Verfahren verwenden, um den Zustand zwischen den Durchgängen beizubehalten.

**app.js**
```javascript
// Packages are installed for you
const { BotFrameworkAdapter, MemoryStorage, ConversationState } = require('botbuilder');
const restify = require('restify');

// Create server
let server = restify.createServer();
server.listen(process.env.port || process.env.PORT || 3978, function () {
    console.log(`${server.name} listening to ${server.url}`);
});

// Create adapter
const adapter = new BotFrameworkAdapter({
    appId: process.env.MICROSOFT_APP_ID,
    appPassword: process.env.MICROSOFT_APP_PASSWORD
});

// Add conversation state middleware
const conversationState = new ConversationState(new MemoryStorage());
adapter.use(conversationState);
```

Der folgende Code lauscht auf eingehende Anforderungen und überprüft den Typ der eingehenden Aktivität, bevor er eine Antwort an den Benutzer sendet.

```javascript
// Listen for incoming requests
server.post('/api/messages', (req, res) => {
    // Route received request to adapter for processing
    adapter.processActivity(req, res, (context) => {
        // This bot is only handling Messages
        if (context.activity.type === 'message') {

            // Get the conversation state
            const state = conversationState.get(context);

            // If state.count is undefined set it to 0, otherwise increment it by 1
            const count = state.count === undefined ? state.count = 0 : ++state.count;

            // Echo back to the user whatever they typed.
            return context.sendActivity(`${count}: You said "${context.activity.text}"`);
        } else {
            // Echo back the type of activity the bot detected if not of type message
            return context.sendActivity(`[${context.activity.type} event detected]`);
        }
    });
});
```

## <a name="start-your-bot"></a>Starten Ihres Bots

Wechseln Sie in das Verzeichnis, das Sie für Ihren Bot erstellt haben, und starten Sie ihn.

```bash
cd <bot directory>
node app.js
```

## <a name="start-the-emulator-and-connect-your-bot"></a>Starten des Emulators und Herstellen einer Verbindung mit Ihrem Bot

Zu diesem Zeitpunkt wird Ihr Bot lokal ausgeführt. Starten Sie im nächste Schritt den Emulator, und stellen Sie dann im Emulator eine Verbindung mit Ihrem Bot her:

1. Klicken Sie im Emulator auf der Registerkarte „Willkommen“ auf den Link **Neue Botkonfiguration erstellen**.
1. Geben Sie einen **Botnamen** ein, und geben Sie dann den Verzeichnispfad zu Ihrem Botcode ein. Die Botkonfigurationsdatei wird unter diesem Pfad gespeichert.
1. Geben Sie im Feld **Endpunkt-URL** die Zeichenfolge `http://localhost:port-number/api/messages` ein, wobei *Portnummer* der Portnummer entspricht, die in dem Browser angezeigt wird, in dem Ihre Anwendung ausgeführt wird.
1. Klicken Sie auf **Verbinden**, um eine Verbindung mit Ihrem Bot herzustellen. Die **Microsoft-App-ID** und das **Microsoft-App-Kennwort** müssen nicht angegeben werden. Sie können diese Felder vorerst leer lassen. Sie erhalten diese Informationen später, wenn Sie Ihren Bot registrieren.

Senden Sie „Hi“ an Ihren Bot. Der Bot antwortet dann mit „0: You said ‚Hi‘“ auf die Nachricht.

## <a name="next-steps"></a>Nächste Schritte

Als Nächstes können Sie [Ihren Bot in Azure bereitstellen](../bot-builder-howto-deploy-azure.md) oder zu den Konzepten wechseln, die einen Bot und dessen Funktionsweise erläutern.

> [!div class="nextstepaction"]
> [Grundlegende Botkonzepte](../v4sdk/bot-builder-basics.md)
