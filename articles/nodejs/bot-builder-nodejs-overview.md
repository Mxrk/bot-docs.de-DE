---
title: Bot Framework SDK für Node.js | Microsoft-Dokumentation
description: Erfahren Sie mehr über das Bot Framework SDK für Node.js, ein leistungsstarkes und leicht zu bedienendes Framework zum Erstellen von Bots.
author: v-ducvo
ms.author: v-ducvo
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 2e25237b616810f5ef10442fec41834568afcb59
ms.sourcegitcommit: b15cf37afc4f57d13ca6636d4227433809562f8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/11/2019
ms.locfileid: "54224738"
---
# <a name="bot-framework-sdk-for-nodejs"></a>Bot Framework SDK für Node.js

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

> [!div class="op_single_selector"]
> - [.NET](../dotnet/bot-builder-dotnet-overview.md)
> - [Node.js](../nodejs/bot-builder-nodejs-overview.md)
> - [REST](../rest-api/bot-framework-rest-overview.md)

Das Bot Framework SDK für Node.js ist ein leistungsstarkes und leicht zu bedienendes Framework, mit dem Node.js-Entwickler auf gewohnte Art und Weise Bots schreiben können.
Sie können damit eine Vielzahl von Benutzeroberflächen für Konversationen erstellen, von einfachen Eingabeaufforderungen bis hin zu Freiformkonversationen.

Die Unterhaltungslogik für Ihren Bot wird als Webdienst gehostet. Das Bot Framework SDK verwendet <a href="http://restify.com">restify</a>, ein beliebtes Framework zum Erstellen von Webdiensten, um den Webserver des Bots zu erstellen. Das SDK ist auch mit <a href="http://expressjs.com/">Express</a> kompatibel, und die Verwendung anderer Web-App-Frameworks ist mit einigen Anpassungen möglich. 

Mit dem SDK können Sie die folgenden SDK-Funktionen nutzen: 

- Leistungsfähiges System zum Erstellen von Dialogen zum Kapseln der Unterhaltungslogik
- Integrierte Eingabeaufforderungen für einfache Eingaben wie Ja/Nein, Zeichenfolgen, Zahlen und Enumerationen, Unterstützung für Nachrichten mit Bildern und Anlagen sowie Rich Cards mit Schaltflächen
- Integrierte Unterstützung für KI-Frameworks wie <a href="http://luis.ai" target="_blank">LUIS</a>
- Integrierte Erkennungen und Ereignishandler, die den Benutzer durch die Unterhaltung führen und nach Bedarf Hilfe, Navigation, Erläuterungen und Bestätigungen bereitstellen

## <a name="get-started"></a>Erste Schritte

Wenn Sie noch nicht mit dem Schreiben von Bots vertraut sind, [erstellen Sie Ihren ersten Bot mit Node.js](bot-builder-nodejs-quickstart.md) mit Schritt-für-Schritt-Anleitungen, die Ihnen helfen, Ihr Projekt einzurichten, das SDK zu installieren und Ihren ersten Bot auszuführen. 

Wenn Sie noch nicht mit dem Bot Framework SDK für Node.js vertraut sind, beginnen Sie mit den [wichtigen Begriffen](bot-builder-nodejs-concepts.md), um die Hauptkomponenten des Bot Framework SDK zu verstehen.

Um sicherzustellen, dass Ihr Bot die wichtigsten Benutzerszenarien adressiert, betrachten Sie zur Orientierung die [Entwurfsprinzipien](../bot-service-design-principles.md) und die [Muster](../bot-service-design-pattern-task-automation.md).

## <a name="get-samples"></a>Herunterladen von Beispielen

Die [Beispiele für das Bot Framework SDK für Node.js](bot-builder-nodejs-samples.md) veranschaulichen aufgabenorientierte Bots, die zeigen, wie Sie die Funktionen im Bot Framework SDK für Node.js nutzen. Sie können die Beispiele dazu verwenden, schnell in die Erstellung großartiger Bots mit umfangreichen Funktionen einzusteigen.

## <a name="next-steps"></a>Nächste Schritte
> [!div class="nextstepaction"]
> [Wichtige Begriffe](bot-builder-nodejs-concepts.md)

## <a name="additional-resources"></a>Zusätzliche Ressourcen

Die folgenden aufgabenorientierten Anleitungen zeigen verschiedene Bot Framework SDK-Funktionen für Node.js:

* [Antworten auf Nachrichten](bot-builder-nodejs-use-default-message-handler.md)
* [Behandeln von Benutzeraktionen](bot-builder-nodejs-dialog-actions.md)
* [Erkennen der Benutzerabsicht](bot-builder-nodejs-recognize-intent-messages.md)
* [Senden einer Rich Card](bot-builder-nodejs-send-rich-cards.md)
* [Senden von Anlagen](bot-builder-nodejs-send-receive-attachments.md)
* [Speichern von Benutzerdaten](bot-builder-nodejs-save-user-data.md)


Wenn Sie Probleme oder Vorschläge in Bezug auf das Bot Framework SDK für Node.js haben, finden Sie unter [Support](../bot-service-resources-links-help.md) eine Liste der verfügbaren Ressourcen. 


[DesignGuide]: ../bot-service-design-principles.md 
[DesignPatterns]: ../bot-service-design-pattern-task-automation.md 
[HowTo]: bot-builder-nodejs-use-default-message-handler.md 
