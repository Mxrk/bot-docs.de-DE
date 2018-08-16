---
title: Erstellen eines Bots mit dem Bot Builder SDK für Node.js | Microsoft-Dokumentation
description: Erstellen Sie einen Bot mit dem Bot Builder SDK für Node.js, einem leistungsstarken Framework für die Bot-Konstruktion.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: get-started-article
ms.prod: bot-framework
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 6159997ec5ea3dbd3188ba2ea4b6207b5d9db08f
ms.sourcegitcommit: 97bb24f15041caccef4ca5736aa14f144881e0c6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/06/2018
ms.locfileid: "39567549"
---
# <a name="create-a-bot-with-the-bot-builder-sdk-for-nodejs"></a>Erstellen eines Bots mit dem Bot Builder SDK für Node.js

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

> [!div class="op_single_selector"]
> - [.NET](../dotnet/bot-builder-dotnet-quickstart.md)
> - [Node.js](../nodejs/bot-builder-nodejs-quickstart.md)
> - [Bot Service](../bot-service-quickstart.md)
> - [REST](../rest-api/bot-framework-rest-connector-quickstart.md)

Das Bot Builder SDK für Node.js ist ein Framework für die Entwicklung von Bots. Es ist einfach zu verwenden und ermöglicht die Modellierung von Frameworks wie Express und Restify, um JavaScript-Entwicklern eine vertraute Umgebung zum Schreiben von Bots zu bieten.

In diesem Tutorial wird die Erstellung eines Bots mithilfe des Bot Builder SDK für Node.js veranschaulicht. Sie können den Bot in einem Konsolenfenster mit dem Bot Framework Channel Emulator testen.

## <a name="prerequisites"></a>Voraussetzungen
Führen Sie zunächst die folgenden erforderlichen Aufgaben aus:

1. Installieren Sie [Node.js](https://nodejs.org).
2. Erstellen Sie einen Ordner für Ihren Bot.
3. Navigieren Sie an einer Eingabeaufforderung oder einem Terminal zu dem Ordner, den Sie gerade erstellt haben.
4. Führen Sie den folgenden **npm**-Befehl aus:

```nodejs
npm init
```

Befolgen Sie die Aufforderungen auf dem Bildschirm, um Informationen zu Ihrem Bot einzugeben. npm erstellt dann eine Datei namens **package.json**, die die von Ihnen angegebenen Informationen enthält. 

## <a name="install-the-sdk"></a>Installieren des SDKs
Installieren Sie als Nächstes das Bot Builder SDK für Node.js, indem Sie den folgenden **npm**-Befehl ausführen:

```nodejs
npm install --save botbuilder
```

Nachdem Sie das SDK installiert haben, können Sie Ihren ersten Bot schreiben.

Erstellen Sie für Ihren ersten Bot einen Bot, der Benutzereingaben einfach wiederholt. Gehen Sie zum Erstellen Ihres Bots wie folgt vor:

1. Erstellen Sie in dem Ordner, den Sie zuvor für Ihren Bot erstellt haben, eine neue Datei namens **app.js**.
2. Öffnen Sie **app.js** in einem Text-Editor oder in einer IDE Ihrer Wahl. Fügen Sie der Datei den folgenden Code hinzu: 

   [!code-javascript[consolebot code sample Node.js](../includes/code/node-getstarted.js#consolebot)]

3. Speichern Sie die Datei . Jetzt können Sie Ihren Bot ausführen und testen.

### <a name="start-your-bot"></a>Starten Ihres Bots

Navigieren Sie in einem Konsolenfenster zum Verzeichnis Ihres Bots, und starten Sie Ihren Bot:

```nodejs
node app.js
```

Ihr Bot wird jetzt lokal ausgeführt. Testen Sie Ihren Bot, indem Sie einige Nachrichten in das Konsolenfenster eingeben.
Sie sollten erkennen können, dass der Bot auf jede Nachricht antwortet, die Sie senden, indem er Ihre Nachricht wiederholt und den Text *„You said:“* voranstellt.

## <a name="install-restify"></a>Installieren von Restify

Konsolen-Bots sind gute textbasierte Clients, Ihr Bot muss jedoch auf einem API-Endpunkt ausgeführt werden, damit er einen beliebigen Bot Framework-Kanal verwenden (oder Ihren Bot im Emulator ausführen) kann. Installieren Sie <a href="http://restify.com/" target="_blank">restify</a> mit dem folgenden **npm**-Befehl:

```nodejs
npm install --save restify
```

Nachdem Sie Restify eingerichtet haben, können Sie einige Änderungen an Ihrem Bot vornehmen.

## <a name="edit-your-bot"></a>Bearbeiten Ihres Bots

Sie müssen einige Änderungen an Ihrer Datei **app.js** vornehmen. 

1. Fügen Sie eine Zeile hinzu, um das Modul `restify` zu erzwingen.
2. Ändern Sie den `ConsoleConnector` in einen `ChatConnector`.
3. Schließen Sie Ihre Microsoft-App-ID und Ihr Microsoft-App-Kennwort ein.
4. Stellen Sie sicher, dass der Connector auf einen API-Endpunkt lauscht.

   [!code-javascript[echobot code sample Node.js](../includes/code/node-getstarted.js#echobot)]

5. Speichern Sie die Datei . Jetzt können Sie Ihren Bot im Emulator ausführen und testen.

> [!NOTE] 
> Sie benötigen keine **Microsoft-App-ID** oder ein **Microsoft-App-Kennwort**, um Ihren Bot im Bot Framework Channel Emulator auszuführen.

## <a name="test-your-bot"></a>Testen Ihres Bots
Testen Sie als Nächstes Ihren Bot mit dem [Bot Framework Channel Emulator](../bot-service-debug-emulator.md), um ihn in Aktion zu erleben. Der Emulator ist eine Desktopanwendung, mit der Sie Ihren Bot auf dem Localhost testen und debuggen oder remote durch einen Tunnel ausführen können.

Laden Sie zunächst den Emulator [herunter](https://emulator.botframework.com), und installieren Sie diesen. Starten Sie nach Abschluss des Downloads die ausführbare Datei, und schließen Sie den Installationsvorgang ab.

### <a name="start-your-bot"></a>Starten Ihres Bots

Navigieren Sie nach der Installation des Emulators in einem Konsolenfenster zum Verzeichnis Ihres Bots, und starten Sie Ihren Bot:

```nodejs
node app.js
```
   
Ihr Bot wird jetzt lokal ausgeführt.

### <a name="start-the-emulator-and-connect-your-bot"></a>Starten des Emulators und Herstellen einer Verbindung mit Ihrem Bot
Nachdem Sie Ihren Bot gestartet haben, starten Sie als Nächstes den Emulator, und verbinden Sie dann Ihren Bot:

1. Klicken Sie im Emulatorfenster auf den Link **Neue Bot-Konfiguration erstellen**. 

2. Geben Sie in die Adressleiste die Zeichenfolge `http://localhost:port-number/api/messages` ein, wobei *port-number* der Portnummer entspricht, die in dem Browser angezeigt wird, in dem Ihre Anwendung ausgeführt wird.

3. Klicken Sie auf „Speichern und verbinden“. Die Microsoft-App-ID und das Microsoft-App-Kennwort müssen nicht angegeben werden. Sie können diese Felder vorerst leer lassen. Sie erhalten diese Informationen später, wenn Sie Ihren Bot registrieren.

### <a name="try-out-your-bot"></a>Testen Ihres Bots

Da Ihr Bot nun lokal ausgeführt wird und mit dem Emulator verbunden ist, können Sie Ihren Bot testen, indem Sie einige Nachrichten im Emulator eingeben.
Sie sollten erkennen können, dass der Bot auf jede Nachricht antwortet, die Sie senden, indem er Ihre Nachricht wiederholt und den Text *„You said:“* voranstellt.

Sie haben erfolgreich Ihren ersten Bot mithilfe des Bot Builder SDK für Node.js erstellt!

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Bot Builder SDK für Node.js](bot-builder-nodejs-overview.md)
