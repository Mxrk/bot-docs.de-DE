---
title: Beispiel-Bots für das Bot Builder SDK für .NET | Microsoft-Dokumentation
description: Beispiel Bots für einen schnellen Einstieg in die Bot-Entwicklung mit dem Bot Builder SDK für .NET.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 05/03/2018
ms.openlocfilehash: 7aff56dfc60d9d5cce42a5b6a2624c1364ff1b72
ms.sourcegitcommit: 44f100a588ffda19c275b118f4f97029f12d1449
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/26/2018
ms.locfileid: "42928378"
---
# <a name="bot-builder-sdk-for-net-samples"></a>Beispiele zum Bot Builder SDK für .NET

::: moniker range="azure-bot-service-3.0"

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

In diesen Beispielen werden aufgabenorientierte Bots veranschaulicht, die zeigen, wie Sie die Features im Bot Builder SDK für .NET nutzen. Sie können die Beispiele dazu verwenden, schnell mit der Erstellung großartiger Bots mit umfassenden Funktionen einzusteigen.

## <a name="get-the-samples"></a>Abrufen der Beispiele
Klonen Sie mit mithilfe von Git das GitHub-Repository [BotBuilder-Samples](https://github.com/Microsoft/BotBuilder-Samples), um die Beispiele abzurufen.

```cmd
git clone https://github.com/Microsoft/BotBuilder-Samples.git
cd BotBuilder-Samples
```

Die mit dem Bot Builder SDK erstellten Beispiel-Bots befinden sich im **CSharp**-Verzeichnis.

Sie können die Beispiele auch auf GitHub anzeigen und direkt in Azure bereitstellen.

## <a name="core"></a>Core
In diesen Beispielen werden grundlegende Techniken zum Erstellen von umfangreichen und fähigen Bots veranschaulicht.

Beispiel | BESCHREIBUNG
------------ | ------------- 
[Send Attachment](https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/core-SendAttachment) | Ein Beispiel-Bot, der einfache Medienanlagen (Bilder) an den Benutzer sendet. 
[Receive Attachment](https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/core-ReceiveAttachment) | Ein Beispiel-Bot, der vom Benutzer gesendete Anlagen empfängt und herunterlädt. 
[Create New Conversation](https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/core-CreateNewConversation)  | Ein Beispiel-Bot, der mithilfe einer zuvor gespeicherten Benutzeradresse eine neue Unterhaltung erstellt.
[Get Members of a Conversation](https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/core-GetConversationMembers) | Ein Beispiel-Bot, der die Teilnehmerliste einer Unterhaltung abruft und erkennt, wenn sie sich ändert. 
[Direct Line](https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/core-DirectLine) | Ein Beispiel-Bot und ein benutzerdefinierter Client, die über die Direct Line-API miteinander kommunizieren. 
[Direct Line (WebSockets)](https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/core-DirectLineWebSockets) | Ein Beispiel-Bot und ein benutzerdefinierter Client, die über die Direct Line-API und WebSockets miteinander kommunizieren. 
[Multi Dialogs](https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/core-MultiDialogs) | Ein Beispiel-Bot, der verschiedene Arten von Dialogen anzeigt.
[State API](https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/core-State) | Ein statusloser Beispiel-Bot, der den Kontext einer Unterhaltung nachverfolgt.
[Custom State API](https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/core-CustomState) | Ein statusloser Beispiel-Bot, der den Kontext einer Unterhaltung mithilfe eines benutzerdefinierten Speicheranbieters nachverfolgt.
[ChannelData](https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/core-ChannelData) | Ein Beispiel-Bot, der mithilfe von ChannelData native Metadaten an Facebook sendet.
[AppInsights](https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/core-AppInsights) | Ein Beispiel-Bot, der Telemetriedaten, die an eine Application Insights-Instanz gesendet werden, protokolliert.

## <a name="search"></a>Suchen,
In diesem Beispiel wird veranschaulicht, wie Azure Search in datengesteuerten Bots genutzt wird.

Beispiel | BESCHREIBUNG
------------ | -------------
[Azure Search](https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/demo-Search) | Zwei Beispiel-Bots, die dem Benutzer beim Navigieren durch große Inhaltsmengen helfen.


## <a name="cards"></a>Cards
Diese Beispiele veranschaulichen das Senden von Rich Cards im Bot Framework.

Beispiel | BESCHREIBUNG
------------ | -------------
[Rich Cards](https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/cards-RichCards) | Ein Beispiel-Bot, der verschiedene Arten von Rich Cards sendet.
[Carousel of Cards](https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/cards-CarouselCards) | Ein Beispiel-Bot, der mithilfe des Karussell-Layouts mehrere Rich Cards in einer einzelnen Nachricht sendet.

## <a name="intelligence"></a>Intelligence
In diesen Beispielen wird veranschaulicht, wie Sie einem Bot mithilfe der Bing- und Microsoft Cognitive Services-APIs Funktionen der künstlichen Intelligenz hinzufügen.

Beispiel | BESCHREIBUNG
------------ | -------------
[LUIS](https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/intelligence-LUIS) | Ein Beispiel-Bot, der LuisDialog verwendet, um sich in eine LUIS.ai-Anwendung zu integrieren.
[Image Caption](https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/intelligence-ImageCaption) | Ein Beispiel-Bot, der mithilfe der Microsoft Cognitive Services Vision-API eine Bildbeschriftung abruft.
[Speech To Text](https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/intelligence-SpeechToText)  | Ein Beispiel-Bot, der mithilfe der Bing-Spracheingabe-API Text aus Audio abruft.
[Similar Products](https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/intelligence-SimilarProducts) | Ein Beispiel-Bot, der mithilfe der Bing-Bildersuche-API visuell ähnliche Produkte ermittelt. 
[Zummer](https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/intelligence-Zummer) | Ein Beispiel-Bot, der mithilfe der Bing-Suche-API nach Wikipedia-Artikeln sucht.

## <a name="reference-implementation"></a>Referenzimplementierung
Dieses Beispiel soll ein End-to-End-Szenario veranschaulichen. Es ist eine hervorragende Quelle für Codefragmente, wenn Sie komplexere Features in Ihren Bot implementieren möchten.


Beispiel | BESCHREIBUNG
------------ | -------------
[Contoso Flowers](https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/demo-ContosoFlowers) | Ein Beispiel-Bot, der viele Features des Bot Framework implementiert.

::: moniker-end

::: moniker range="azure-bot-service-4.0"

[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

In diesen Beispielen werden aufgabenorientierte Bots veranschaulicht, die zeigen, wie Sie die Features im Bot Builder SDK v4 für .NET nutzen. Sie können die Beispiele dazu verwenden, schnell in die Erstellung großartiger Bots mit umfangreichen Funktionen einzusteigen. 

Hinweis: Das SDK V4 wird aktiv entwickelt und sollte daher nur zum Experimentieren verwendet werden. 

Klonen Sie mit mithilfe von Git das GitHub-Repository [botbuilder-dotnet](https://github.com/Microsoft/botbuilder-dotnet), um die Beispiele abzurufen.
```cmd
git clone https://github.com/Microsoft/botbuilder-dotnet.git
cd botbuilder-dotnet\samples-final
```
Die mit dem Bot Builder SDK erstellten Beispiel-Bots befinden sich im **samples-final**-Verzeichnis.


::: moniker-end

