---
title: Testen eines Bots | Microsoft-Dokumentation
description: Testen und Debuggen Ihres Conversation Designer-Bot.
author: vkannan
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
ROBOTS: NoIndex, NoFollow
ms.openlocfilehash: b08bd96bc7c413ff7cb6db4899c8c0d3ad613ab8
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39304216"
---
# <a name="test-your-conversation-designer-bot"></a>Testen Ihres Conversation Designer-Bots
> [!IMPORTANT]
> Conversation Designer ist noch nicht für alle Kunden verfügbar. Weitere Informationen zur Verfügbarkeit von Conversation Designer folgen später in diesem Jahr.

Der Conversation Designer bietet nützliche Debugtools in Form mehrerer verschiedener Ausgabefenster (die sich unten auf dem Bildschirm befinden). Sie können das Testen und Debuggen starten, indem Sie auf die Schaltfläche **Testen** in der oberen rechten Bildschirmecke klicken. 

## <a name="validation-errors"></a>Überprüfungsfehler
Es gibt eine Reihe von Bedingungen, unter denen Überprüfungsfehler auftreten. Beispiel:  
- fehlende Reaktion auf den Trigger 
- unvollständige Informationen zum Ablauf
- fehlende Trigger und CodeBehind für bedingte Antwortvorlage, Entscheidungs- und Prozesszustände
- eine erkannte rekursive oder Endlosschleife in den Vorlagenverweisen 
- ein verwaister Zustand des Dialogs, der nicht mit dem Ablauf verbunden ist
- eine hinzugefügte Aufgabe, deren Trigger oder Aktion fehlt 


## <a name="javascript-output"></a>JavaScript-Ausgabe
Sie können ein Skript an die Ausführung einer Antwort anfügen, um zusätzliche Funktionalität bereitzustellen. Wenn das Skript einen Fehler enthält, wird er hier angezeigt. Sie können der Geschäftslogik Ihres Bots `console.log()`, `error.log()` oder `debug.log()` hinzufügen, wodurch Nachrichten im Ausgabefenster aufgelistet werden. Beispiel: 

``` javascript
module.exports.Respond_beforeResponse = function(context) {
    console.log(JSON.stringify(context));
}
```

## <a name="runtime-output"></a>Runtime-Ausgaben
Von der Runtime generierte Fehler oder Ausnahmen werden hier angezeigt. Wenn Ihr Bot beispielsweise nicht reagiert, sollten Sie sich die Ausgabe ansehen, um zu untersuchen, wodurch der Fehler oder die Ausnahme hervorgerufen wurde. Bei zu vielen Nachrichten im Ausgabefenster können Sie auf **Clear all** (Alles löschen) klicken und Ihren Bot dann erneut testen, indem Sie dem Bot eine Nachricht senden, um die Fehlerdetails anzuzeigen. 

## <a name="next-step"></a>Nächster Schritt
> [!div class="nextstepaction"]
> [Importieren und Exportieren von Daten](conversation-designer-export-import-bot.md)
