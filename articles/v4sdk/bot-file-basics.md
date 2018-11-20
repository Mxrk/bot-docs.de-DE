---
title: Verwalten von Botressourcen mit einer BOT-Datei | Microsoft-Dokumentation
description: Beschreibt den Zweck und die Verwendung einer BOT-Datei.
keywords: BOT-Datei, .bot, .bot-Datei, MsBot, Botressourcen, Botressourcen verwalten
author: ivorb
ms.author: v-ivorb
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 11/13/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 0ff3f0e68d58a8768bb785a88ee7664ab430e453
ms.sourcegitcommit: 8b7bdbcbb01054f6aeb80d4a65b29177b30e1c20
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/14/2018
ms.locfileid: "51645630"
---
# <a name="manage-bot-resources-with-a-bot-file"></a>Verwalten von Botressourcen mit einer BOT-Datei

Bots nutzen in der Regel viele verschiedene Dienste, beispielsweise [LUIS.ai](https://luis.ai) oder [QnaMaker.ai](https://qnamaker.ai). Beim Entwickeln eines Bots ist kein einheitlicher Speicherort zum Speichern der Metadaten der verwendeten Dienste verfügbar.  Daher können wir keine Tools erstellen, die einen Bot in seiner Gesamtheit erfassen und darstellen.

Zur Lösung dieses Problems haben wir eine **BOT-Datei** erstellt. In dieser Datei können alle Dienstverweise an einem zentralen Ort zusammengeführt werden, um die Bereitstellung von Tools zu ermöglichen.  Der Bot Framework-Emulator ([V4](https://aka.ms/Emulator-wiki-getting-started)) verwendet beispielsweise eine BOT-Datei, um eine einheitliche Ansicht der von Ihrem Bot genutzten verbundenen Dienste zu erstellen.  

Mit einer BOT-Datei können Sie unter anderem die folgenden Dienste registrieren:

* **Localhost** stellt lokale Debuggerendpunkte bereit.
* [**Azure Bot Service**](https://azure.microsoft.com/en-us/services/bot-service/) wird für Azure Bot Service-Registrierungen verwendet.
* [**LUIS.AI**](https://www.luis.ai/) (Language Understanding, LUIS) ermöglicht es Ihrem Bot, in natürlicher Sprache mit Benutzern zu kommunizieren. 
* [**QnA Maker**](https://qnamaker.ai/) ermöglicht es Ihnen, in Minutenschnelle einen einfachen Frage-und-Antwort-Bot auf der Grundlage von FAQ-URLs, strukturierten Dokumenten oder redaktionellen Inhalten zu erstellen, zu trainieren und zu veröffentlichen.
* [**Dispatch**](https://github.com/Microsoft/botbuilder-tools/tree/master/Dispatch)-Modelle können zur dienstübergreifenden Verteilung verwendet werden.
* [**Azure Application Insights**](https://azure.microsoft.com/en-us/services/application-insights/) bietet Erkenntnisse und Botanalysen.
* [**Azure Blob Storage**](https://azure.microsoft.com/en-us/services/storage/blobs/) kann zum Speichern des Botzustands verwendet werden. 
* [**Azure Cosmos DB**](https://azure.microsoft.com/en-us/services/cosmos-db/) ist ein global verteilter Multimodell-Datenbankdienst zum Speichern des Botzustands.

Abgesehen von diesen Diensten kann Ihr Bot von anderen benutzerdefinierten Diensten abhängig sein. Sie können die Funktion für [allgemeine Dienste](https://github.com/Microsoft/botbuilder-tools/blob/master/packages/MSBot/docs/add-services.md) nutzen, um eine generische Dienstkonfiguration zu verbinden.

## <a name="when-is-a-bot-file-created"></a>Wann wird eine BOT-Datei erstellt? 
- Wenn Sie einen Bot mit [Azure Bot Service](https://ms.portal.azure.com/#blade/Microsoft_Azure_Marketplace/GalleryResultsListBlade/selectedSubMenuItemId/%7B%22menuItemId%22%3A%22gallery%2FCognitiveServices_MP%2FBotService%22%2C%22resourceGroupId%22%3A%22%22%2C%22resourceGroupLocation%22%3A%22%22%2C%22dontDiscardJourney%22%3Afalse%2C%22launchingContext%22%3A%7B%22source%22%3A%5B%22GalleryFeaturedMenuItemPart%22%5D%2C%22menuItemId%22%3A%22CognitiveServices_MP%22%2C%22subMenuItemId%22%3A%22BotService%22%7D%7D) erstellen, wird automatisch eine BOT-Datei für Sie erstellt, die eine Liste der bereitgestellten verbundenen Dienste enthält. Die BOT-Datei wird standardmäßig verschlüsselt.
- Wenn Sie einen Bot mithilfe einer [Vorlage](https://marketplace.visualstudio.com/items?itemName=BotBuilder.botbuilderv4) des Bot Builder V4 SDK für Visual Studio oder mit dem [Yeoman-Generator](https://www.npmjs.com/package/generator-botbuilder) von Bot Builder erstellen, wird automatisch eine BOT-Datei erstellt. In diesem Flow werden keine verbundenen Dienste bereitgestellt, und die BOT-Datei wird nicht verschlüsselt.
- Wenn Sie [Bot Builder-Beispiele](https://github.com/Microsoft/botbuilder-samples) nutzen, um mit der Erstellung zu beginnen, enthält jedes Beispiel für das Bot Builder V4 SDK eine BOT-Datei, und die BOT-Datei wird nicht verschlüsselt. 
- Sie können eine BOT-Datei auch mithilfe des [MSBot](https://github.com/Microsoft/botbuilder-tools/blob/master/packages/MSBot/README.md)-Tools erstellen.

## <a name="what-does-a-bot-file-look-like"></a>Wie sieht eine BOT-Datei aus? 
Sehen Sie sich die folgende [BOT](https://github.com/Microsoft/botbuilder-tools/blob/master/packages/MSBot/docs/sample-bot-file.json)-Beispieldatei an.
Informationen zur Verschlüsselung und Entschlüsselung der BOT-Datei finden Sie unter [Bot Secrets](https://github.com/Microsoft/botbuilder-tools/blob/master/packages/MSBot/docs/bot-file-encryption.md) (Botgeheimnisse).
## <a name="why-do-i-need-a-bot-file"></a>Wozu benötige ich eine BOT-Datei?

Eine BOT-Datei ist zum Erstellen von Bots mit dem Bot Builder SDK **nicht** erforderlich. Sie können zum Nachverfolgen der Dienstverweise und Schlüssel, von denen Ihr Bot abhängt, weiterhin „appsettings.json“, „web.config“, ENV, Key Vault oder andere geeignete Mechanismen verwenden. Um den Bot mit dem Emulator zu testen, benötigen Sie jedoch eine BOT-Datei. Die gute Nachricht ist, dass der Emulator eine BOT-Datei zum Testen erstellen kann. Starten Sie hierzu den Emulator, und klicken Sie auf der Begrüßungsseite auf den Link **Neue Botkonfiguration erstellen**. Geben Sie im angezeigten Dialogfeld einen **Botnamen** und eine **Endpunkt-URL** ein. Stellen Sie anschließend eine Verbindung her.

Die Verwendung einer BOT-Datei bietet die folgenden Vorteile:
- Sie stellt unabhängig von der verwendeten Programmiersprache/Plattform eine Standardmethode zum Speichern von Ressourcen bereit.   
- Der Bot Framework-Emulator und die CLI-Tools beruhen auf verbundenen Überwachungsdiensten in einem konsistenten Format (in einer BOT-Datei) und funktionieren einwandfrei mit solchen Diensten. 
- Elegante Toollösungen für die Erstellung und Verwaltung von Diensten sind ohne ein gut definiertes Schema (BOT-Datei) schwieriger zu realisieren.  


## <a name="using-bot-file-in-your-bot-builder-sdk-bot"></a>Verwenden der BOT-Datei in Ihrem Bot Builder SDK-Bot
Sie können die BOT-Datei verwenden, um Dienstkonfigurationsinformationen im Code Ihres Bots abzurufen. Die für [C#](https://www.nuget.org/packages/Microsoft.Bot.Configuration) und [JS](https://www.npmjs.com/package/botframework-config) verfügbare Bibliothek „BotFramework-Configuration“ ermöglicht Ihnen das Laden einer BOT-Datei und unterstützt mehrere Methoden zum Abfragen und Abrufen der entsprechenden Dienstkonfigurationsinformationen.

## <a name="additional-resources"></a>Zusätzliche Ressourcen
Weitere Informationen zur Verwendung einer BOT-Datei finden Sie in der Infodatei von [MSBot](https://github.com/Microsoft/botbuilder-tools/blob/master/packages/MSBot/README.md).
