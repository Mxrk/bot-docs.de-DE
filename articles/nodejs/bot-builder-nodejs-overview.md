---
title: Bot Builder SDK für Node.js | Microsoft-Dokumentation
description: Erfahren Sie mehr über das Bot Builder SDK für Node.js, ein leistungsstarkes und leicht zu bedienendes Framework zum Erstellen von Bots.
author: v-ducvo
ms.author: v-ducvo
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 24da2cc90908a1f22bee9d2cd007607b116ac2cc
ms.sourcegitcommit: 2dc75701b169d822c9499e393439161bc87639d2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/24/2018
ms.locfileid: "42904203"
---
# <a name="bot-builder-sdk-for-nodejs"></a>Bot Builder SDK für Node.js

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

> [!div class="op_single_selector"]
> - [.NET](../dotnet/bot-builder-dotnet-overview.md)
> - [Node.js](../nodejs/bot-builder-nodejs-overview.md)
> - [REST](../rest-api/bot-framework-rest-overview.md)

Das Bot Builder SDK für Node.js ist ein leistungsstarkes und leicht zu bedienendes Framework, mit dem Node.js-Entwickler auf gewohnte Art und Weise Bots schreiben können.
Sie können damit eine Vielzahl von Benutzeroberflächen für Unterhaltungen erstellen, von einfachen Eingabeaufforderungen bis hin zu Freiformunterhaltungen.

Die Unterhaltungslogik für Ihren Bot wird als Webdienst gehostet. Das Bot Builder SDK verwendet <a href="http://restify.com">restify</a>, ein beliebtes Framework zum Erstellen von Webdiensten, um den Webserver des Bots zu erstellen. Das SDK ist auch mit <a href="http://expressjs.com/">Express</a> kompatibel, und die Verwendung anderer Web-App-Frameworks ist mit einigen Anpassungen möglich. 

Mit dem SDK können Sie die folgenden SDK-Funktionen nutzen: 

- Leistungsfähiges System zum Erstellen von Dialogen zum Kapseln der Unterhaltungslogik
- Integrierte Eingabeaufforderungen für einfache Eingaben wie Ja/Nein, Zeichenfolgen, Zahlen und Enumerationen, Unterstützung für Nachrichten mit Bildern und Anlagen sowie Rich Cards mit Schaltflächen
- Integrierte Unterstützung für KI-Frameworks wie <a href="http://luis.ai" target="_blank">LUIS</a>
- Integrierte Erkennungen und Ereignishandler, die den Benutzer durch die Unterhaltung führen und nach Bedarf Hilfe, Navigation, Erläuterungen und Bestätigungen bereitstellen

## <a name="get-started"></a>Erste Schritte

Wenn Sie noch nicht mit dem Schreiben von Bots vertraut sind, [erstellen Sie Ihren ersten Bot mit Node.js](bot-builder-nodejs-quickstart.md) mit Schritt-für-Schritt-Anleitungen, die Ihnen helfen, Ihr Projekt einzurichten, das SDK zu installieren und Ihren ersten Bot auszuführen. 

Wenn Sie noch nicht mit dem Bot Builder SDK für Node.js vertraut sind, beginnen Sie mit den [wichtigen Begriffen](bot-builder-nodejs-concepts.md), um die Hauptkomponenten des Bot Builder SDKs zu verstehen.

Um sicherzustellen, dass Ihr Bot die wichtigsten Benutzerszenarien adressiert, betrachten Sie zur Orientierung die [Entwurfsprinzipien](../bot-service-design-principles.md) und die [Muster](../bot-service-design-pattern-task-automation.md).

## <a name="get-samples"></a>Herunterladen von Beispielen

Die [Beispiele für das Bot Builder SDK für Node.js](bot-builder-nodejs-samples.md) veranschaulichen aufgabenorientierte Bots, die zeigen, wie Sie die Funktionen im Bot Builder SDK für Node.js nutzen. Sie können die Beispiele dazu verwenden, schnell in die Erstellung großartiger Bots mit umfangreichen Funktionen einzusteigen.

## <a name="next-steps"></a>Nächste Schritte
> [!div class="nextstepaction"]
> [Wichtige Begriffe](bot-builder-nodejs-concepts.md)

## <a name="additional-resources"></a>Zusätzliche Ressourcen

Die folgenden aufgabenorientierten Anleitungen zeigen verschiedene Bot Builder SDK-Funktionen für Node.js.

* [Antworten auf Nachrichten](bot-builder-nodejs-use-default-message-handler.md)
* [Behandeln von Benutzeraktionen](bot-builder-nodejs-dialog-actions.md)
* [Erkennen der Benutzerabsicht](bot-builder-nodejs-recognize-intent-messages.md)
* [Senden einer Rich Card](bot-builder-nodejs-send-rich-cards.md)
* [Senden von Anlagen](bot-builder-nodejs-send-receive-attachments.md)
* [Speichern von Benutzerdaten](bot-builder-nodejs-save-user-data.md)


Wenn Sie Probleme oder Vorschläge in Bezug auf das Bot Builder SDK für Node.js haben, finden Sie unter [Support](../bot-service-resources-links-help.md) eine Liste der verfügbaren Ressourcen. 


[DesignGuide]: ../bot-service-design-principles.md 
[DesignPatterns]: ../bot-service-design-pattern-task-automation.md 
[HowTo]: bot-builder-nodejs-use-default-message-handler.md 
