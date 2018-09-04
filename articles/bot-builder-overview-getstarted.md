---
title: Einstieg in die Erstellung von Bots mit Bot Builder | Microsoft-Dokumentation
description: Erste Schritte beim Erstellen von leistungsstarken Bots mit Bot Builder.
author: robstand
ms.author: kamrani
manager: kamrani
ms.topic: get-started-article
ms.prod: bot-framework
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 3ee7843e64dfa95427ebcb132740eab3db281ffc
ms.sourcegitcommit: 2dc75701b169d822c9499e393439161bc87639d2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/24/2018
ms.locfileid: "42904013"
---
# <a name="develop-bots-with-bot-builder"></a>Entwickeln von Bots mit Bot Builder



Bot Builder bietet ein SDK, Bibliotheken, Beispiele und Tools, die Sie beim Erstellen und Debuggen von Bots unterstützen. Wenn Sie einen Bot mit Bot Service erstellen, wird Ihr Bot durch das Bot Builder SDK unterstützt. Sie können das Bot Builder SDK auch verwenden, um mit C# oder Node.js einen komplett neuen Bot zu erstellen. Bot Builder enthält den Bot Framework Emulator zum Testen Ihrer Bots und den Channel Inspector, mit dem Sie eine Vorschau der Benutzeroberfläche Ihres Bots auf verschiedenen Kanälen anzeigen können.

Wenn Sie einen Bot mit der von Ihnen bevorzugten Programmiersprache erstellen möchten, können Sie die REST-API verwenden.

## <a name="bot-builder-sdk-for-net"></a>Bot Builder SDK für .NET
Das Bot Builder SDK für .NET nutzt C#, um .NET-Entwicklern eine vertraute Umgebung zum Schreiben von Bots zur Verfügung zu stellen. Es ist ein leistungsstarkes Framework für die Erstellung von Bots, die sowohl Freiforminteraktionen als auch strukturiertere Konversationen verarbeiten können, bei denen Benutzer mögliche Werte auswählen. 

Der [Schnellstart für .NET](~/dotnet/bot-builder-dotnet-quickstart.md) führt Sie durch die Erstellung eines Bots mit dem Bot Builder SDK für .NET.

Das Bot Builder SDK für .NET ist als [NuGet-Paket](https://www.nuget.org/packages/Microsoft.Bot.Builder/) verfügbar.

> [!IMPORTANT]
> Für das Bot Builder SDK für .NET ist .NET Framework 4.6 oder höher erforderlich. Wenn Sie das SDK einem vorhandenen Projekt für eine niedrigere Version von .NET Framework hinzufügen, müssen Sie zuerst Ihr Projekt aktualisieren und .NET Framework 4.6 als Ziel festlegen.

Führen Sie die folgenden Schritte aus, um das SDK in einem vorhandenen Visual Studio-Projekt zu installieren:

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf den Projektnamen, und wählen Sie **NuGet-Pakete verwalten** aus.
2. Geben Sie auf der Registerkarte **Durchsuchen** im Suchfeld „Microsoft.Bot.Builder“ ein.
3. Wählen Sie in der Ergebnisliste **Microsoft.Bot.Builder** aus, klicken Sie auf **Installieren**, und akzeptieren Sie die Änderungen.

Sie können den [Quellcode](https://github.com/Microsoft/BotBuilder/tree/master/CSharp) für das Bot Builder SDK für .NET auch von GitHub herunterladen.

### <a name="visual-studio-project-templates"></a>Visual Studio-Projektvorlagen
Laden Sie Visual Studio-Projektvorlagen herunter, um die Bot-Entwicklung zu beschleunigen.

* [Bot-Vorlage für Visual Studio][bot-template] für die Entwicklung von Bots mit C#
* [Cortana-Funktionsvorlage für Visual Studio][cortana-template] für die Entwicklung von Cortana-Funktionen mit C#

> [!TIP]
> <a href="/visualstudio/ide/how-to-locate-and-organize-project-and-item-templates" target="_blank">Erfahren Sie mehr</a> über die Installation von Visual Studio 2017-Projektvorlagen.

## <a name="bot-builder-sdk-for-nodejs"></a>Bot Builder SDK für Node.js
Mit dem Bot Builder SDK für Node.js können Node.js-Entwickler auf gewohnte Weise Bots schreiben. Sie können damit eine Vielzahl von Benutzeroberflächen für Konversationen erstellen, von einfachen Eingabeaufforderungen bis hin zu Freiformkonversationen. Das Bot Builder SDK für Node.js verwendet „restify“, ein beliebtes Framework zum Erstellen von Webdiensten, um den Webserver des Bots zu erstellen. Das SDK ist auch mit Express kompatibel. 

Der [Schnellstart für Node.js](~/nodejs/bot-builder-nodejs-quickstart.md) führt Sie durch die Erstellung eines Bots mit dem Bot Builder SDK für Node.js. 

Das Bot Builder SDK für Node.js ist als npm-Paket verfügbar. Um das Bot Builder SDK für Node.js und die zugehörigen Abhängigkeiten zu installieren, erstellen Sie zunächst einen Ordner für Ihren Bot, navigieren Sie dann zu diesem Ordner, und führen Sie den folgenden **npm**-Befehl aus:

```nodejs
npm init
```

Installieren Sie anschließend das Bot Builder SDK für Node.js und <a href="http://restify.com/" target="_blank">Restify</a>-Module, indem Sie die folgenden **npm**-Befehle ausführen:

```nodejs
npm install --save botbuilder
npm install --save restify
```

Sie können den [Quellcode](https://github.com/Microsoft/BotBuilder/tree/master/Node) für das Bot Builder SDK für Node.js auch von GitHub herunterladen.

## <a name="rest-api"></a>REST-API

Mit der Bot Framework-REST-API können Sie einen Bot mit einer beliebigen Programmiersprache erstellen. Der [Schnellstart für REST](rest-api/bot-framework-rest-connector-quickstart.md) führt Sie durch die Erstellung eines Bots mit REST.

Das Bot Framework enthält drei REST-APIs:

 - Die [Bot Connector-REST-API][connectorAPI] ermöglicht es Ihrem Bot, Nachrichten mit Kanälen auszutauschen, die im [Bot Framework-Portal](https://dev.botframework.com/) konfiguriert sind. 

- Die [Bot State-REST-API][stateAPI] ermöglicht Ihrem Bot das Speichern und Abrufen des Zustands im Zusammenhang mit den Konversationen, die über die Bot Connector-REST-API durchgeführt werden.

- Die [Direct Line-REST-API][directLineAPI] ermöglicht es Ihnen, Ihre eigene Anwendung (beispielsweise eine Clientanwendung, ein Webchat-Steuerelement oder eine mobile App) direkt mit einem einzelnen Bot zu verbinden.

## <a name="bot-framework-emulator"></a>Bot Framework Emulator
Der Bot Framework Emulator ist eine Desktopanwendung, mit der Sie Ihre Bots lokal oder remote testen und debuggen können. Mit dem Emulator können Sie mit Ihrem Bot chatten und die Nachrichten überprüfen, die Ihr Bot sendet und empfängt. [Erfahren Sie mehr über den Bot Framework Emulator](~/bot-service-debug-emulator.md), und [laden Sie den Emulator für Ihre Plattform herunter](http://emulator.botframework.com).

## <a name="bot-framework-channel-inspector"></a>Bot Framework Channel Inspector
Mit dem Bot Framework [Channel Inspector](bot-service-channel-inspector.md) können Sie schnell überprüfen, wie Bot-Funktionen auf verschiedenen Kanälen aussehen.

[bot-template]: http://aka.ms/bf-bc-vstemplate
[cortana-template]: https://aka.ms/bf-cortanaskill-template


[connectorAPI]: https://docs.botframework.com/en-us/restapi/connector/#navtitle
 
[stateAPI]: https://docs.botframework.com/en-us/restapi/state/#navtitle

[directLineAPI]: https://docs.botframework.com/en-us/restapi/directline3/#navtitle
