---
title: Middleware | Microsoft-Dokumentation
description: Grundlagen zu Middleware und deren Verwendung im Bot SDK.
keywords: middleware, middlewarepipeline, kurzschluss, middleware verwendung
author: ivorb
ms.author: v-ivorb
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 11/8/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 231ed330faf9ce777a5acc5f4e6272b747a6f7fc
ms.sourcegitcommit: d385ec5fe61c469ab17e6f21b4a0d50e5110d0fd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/15/2019
ms.locfileid: "54298277"
---
# <a name="middleware"></a>Middleware

[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

Middleware ist lediglich eine Klasse zwischen dem Adapter und der Bot-Logik, die während der Initialisierung der Middlewaresammlung Ihres Adapters hinzugefügt wird. Das SDK ermöglicht Ihnen das Schreiben eigener Middleware sowie das Hinzufügen von Middleware, die von anderen Entwicklern erstellt wurde. Jede ein- oder ausgehende Aktivität für Ihren Bot durchläuft Ihre Middleware.

Der Adapter verarbeitet und leitet eingehende Aktivitäten durch die Bot-Middlewarepipeline zur Logik Ihres Bots und wieder zurück. Während jede Aktivität den Bot durchläuft, kann jede Middleware die Aktivität überprüfen und beeinflussen, sowohl bevor als auch nachdem die Bot-Logik ausgeführt wurde.

Bevor Sie mit Middleware arbeiten, ist es wichtig, dass Sie [Bots im Allgemeinen](~/v4sdk/bot-builder-basics.md) und [wie sie Aktivitäten verarbeiten](~/v4sdk/bot-builder-basics.md#the-activity-processing-stack) verstehen.

## <a name="uses-for-middleware"></a>Verwendungsmöglichkeiten für Middleware
Oft stellt sich folgende Frage: „Wann sollte ich Aktionen als Middleware implementieren und nicht meine normale Botlogik verwenden?“ Mit Middleware haben Sie zusätzliche Möglichkeiten zum Interagieren mit dem Konversationsfluss Ihrer Benutzer – sowohl vor als auch nach der Verarbeitung jedes _Durchlaufs_ der Konversation. Darüber hinaus können Sie mit Middleware auch Informationen zur Konversation speichern und abrufen und bei Bedarf weitere Verarbeitungslogik aufrufen. Unten sind einige häufige Szenarien angegeben, die zeigen, wann Middleware hilfreich sein kann.

### <a name="looking-at-or-acting-on-every-activity"></a>Ansehen oder Beeinflussen jeder Aktivität
Es gibt viele Situationen, in denen Ihr Bot jede Aktivität bzw. jede Aktivität eines bestimmten Typs behandeln sollte. Angenommen, Sie möchten jedes MessageActivity-Objekt protokollieren, das der Bot empfängt, oder eine Fallbackantwort bereitstellen, wenn der Bot keine andere Antwort für diesen Durchlauf generiert hat. Die Middleware ist ein guter Ausgangspunkt hierfür, da sie sowohl vor als auch nach der Ausführung der restlichen Bot-Logik agieren kann.

### <a name="modifying-or-enhancing-the-turn-context"></a>Ändern und Erweitern des Turn-Kontexts
Bestimmte Konversationen können erfolgreicher sein, wenn der Bot über mehr Informationen verfügt, als in der Aktivität bereitgestellt werden. In diesem Fall kann Middleware auf die bisher verfügbaren Informationen zum Konversationszustand zugreifen, eine externe Datenquelle abfragen und diese an das [Turn](~/v4sdk/bot-builder-basics.md#defining-a-turn)-Kontextobjekt anfügen, bevor die Ausführung an die Bot-Logik übergeben wird. 

Im SDK ist das Protokollieren der Middleware definiert, mit der ein- und ausgehende Aktivitäten aufgezeichnet werden können, aber Sie können auch Ihre eigene Middleware definieren.

## <a name="the-bot-middleware-pipeline"></a>Die Bot-Middlewarepipeline
Der Adapter ruft für jede Aktivität Middleware in der Reihenfolge auf, in der Sie sie hinzugefügt haben. Der Adapter übergibt das Kontextobjekt für den Turn und einen _next_-Delegaten, und die Middleware ruft den Delegaten auf, um die Steuerung an die nächste Middleware in der Pipeline zu übergeben. Middleware hat auch die Möglichkeit, Aktionen durchzuführen, nachdem der _next_-Delegat zurückgegeben wird, bevor die Methode abgeschlossen wird. Sie können es sich so vorstellen, dass jedes Middleware-Objekt die Möglichkeit hat, in Bezug auf Middleware-Objekte, die in der Pipeline folgen, als erstes und als letztes zu agieren.

Beispiel: 

- Der Turn-Handler des ersten Middleware-Objekts führt Code aus, bevor _Weiter_ aufgerufen wird.
  - Der Turn-Handler des zweiten Middleware-Objekts führt Code aus, bevor _Weiter_ aufgerufen wird.
    - Der Turn-Handler des Bots wird ausgeführt, und die Rückgabe wird gesendet.
  - Der Turn-Handler des zweiten Middleware-Objekts führt restlichen Code vor der Rückgabe aus.
- Der Turn-Handler des ersten Middleware-Objekts führt restlichen Code vor der Rückgabe aus.

Wenn die Middleware nicht den next-Delegaten aufruft, ruft der Adapter keine nachfolgende Middleware oder Bot-Turn-Handler auf und bei der Pipeline tritt ein Kurzschluss auf.

Nach Abschluss der Bot-Middlewarepipeline endet der Turn und der Gültigkeitsbereich des Turn-Kontexts.

Die Middleware oder der Bot kann Antworten generieren und Antwortereignishandler registrieren. Beachten Sie, dass Antworten in separaten Prozessen verarbeitet werden.

## <a name="order-of-middleware"></a>Reihenfolge der Middleware
Die Reihenfolge, in der die Middleware hinzugefügt wird, ist eine wichtige Entscheidung, da sie bestimmt, in welcher Reihenfolge die Middleware eine Aktivität verarbeitet.

> [!NOTE]
> Damit soll ein allgemeines Muster bereitgestellt werden, das für die meisten Bots funktioniert. Beachten Sie jedoch, wie jeder Teil der Middleware mit den Anderen in Ihrer Situation interagiert.

Die ersten Schritte in Ihrer Pipeline sollten wahrscheinlich die sein, die sich um die allgemeinsten Aufgaben kümmern und jedes Mal verwendet werden. Hierzu gehören beispielsweise die Protokollierung, die Ausnahmebehandlung und die Übersetzung. Die Festlegung der Reihenfolge hierfür kann je nach Ihren Anforderungen variieren, z.B. ob eingehende Nachrichten zuerst übersetzt werden sollen, bevor Nachrichten gespeichert werden, oder ob zuerst das Speichern der Nachrichten erfolgen soll. Dies kann bedeuten, dass die gespeicherten Nachrichten nicht übersetzt sind.

Die letzten Schritte in Ihrer Middleware-Pipeline sollten sich auf Bot-spezifische Middleware beziehen. Also Middleware, die Sie implementieren, um jede Nachricht an den Bot zu verarbeiten. Wenn Ihre Middleware Zustandsinformationen oder andere Informationen im Kontext des Bots verwendet, fügen Sie sie nach der Middleware, die den Zustand oder Kontext ändert, der Middleware-Pipeline hinzu.

## <a name="short-circuiting"></a>Kurzschlüsse
_Kurzschlüsse_ sind ein wichtiges Konzept bei der Middleware (und bei Antworthandlern). Wenn die Ausführung durch die nachfolgenden Ebenen fortgesetzt werden soll, ist eine Middleware (oder ein Antworthandler) erforderlich, um die Ausführung durch Aufrufen des _next_-Delegaten zu übergeben.  Wenn der next-Delegat in der Middleware (oder dem Antworthandler) aufgerufen wird, tritt für die zugeordnete Pipeline ein Kurzschluss auf, und die nachfolgenden Ebenen werden nicht ausgeführt. Dies bedeutet, dass die gesamte Botlogik sowie jegliche Middleware im weiteren Verlauf der Pipeline übersprungen wird. Es besteht ein feiner Unterschied dazwischen, ob der Kurzschluss eines Durchlaufs von Ihrer Middleware oder von Ihrem Antworthandler ausgeht.

Wenn der Kurschluss eines Durchlaufs von der Middleware ausgeht, wird Ihr Handler für den Botdurchlauf nicht aufgerufen, aber der gesamte Middleware-Code, der in der Pipeline vor diesem Punkt ausgeführt wurde, wird trotzdem bis zum Ende ausgeführt. 

Wenn _next_ bei Ereignishandlern nicht aufgerufen wird, wird das Ereignis abgebrochen, was ein deutlich anderes Ergebnis als das Überspringen der Logik durch die Middleware ist. Wenn der Rest des Ereignisses nicht verarbeitet wird, sendet der Adapter es nicht.

> [!TIP]
> Stellen Sie sicher, dass es dem gewünschten Verhalten entspricht, wenn Sie für ein Antwortereignis wie `SendActivities` einen Kurzschluss durchführen. Andernfalls kann es schwierig sein, Fehler zu beheben.

## <a name="response-event-handlers"></a>Antwortereignishandler
Zusätzlich zur Anwendungs- und Middlewarelogik können Antworthandler (manchmal auch als Ereignishandler oder Aktivitätsereignishandler bezeichnet) dem Kontextobjekt hinzugefügt werden. Diese Ereignishandler werden aufgerufen, wenn die dazugehörige Antwort im aktuellen Kontextobjekt vor der Ausführung der tatsächlichen Antwort stattfindet. Diese Handler sind nützlich, wenn Sie wissen, dass Sie vor oder nach dem eigentlichen Ereignis etwas für jede Aktivität dieses Typs für den Rest der aktuellen Antwort tun wollen.

> [!WARNING]
> Achten Sie darauf, eine Aktivitätsantwortmethode nicht in ihrem jeweiligen Antwortereignishandler aufzurufen, z. B. indem Sie die SendActivity-Methode in einem onSendActivity-Handler aufrufen. Anderenfalls wird eine Endlosschleife generiert.

Beachten Sie, dass jede neue Aktivität einen neuen Thread für die Ausführung erhält. Wenn der Thread zur Verarbeitung der Aktivität erstellt wird, wird die Liste der Handler für diese Aktivität in den neuen Thread kopiert. Handler, die nach diesem Punkt hinzugefügt wurden, werden für dieses spezielle Aktivitätsereignis nicht ausgeführt.
Die unter einem Kontextobjekt registrierten Handler werden auf eine Weise verarbeitet, die der Verwaltung der Middlewarepipeline durch den Adapter sehr ähnelt. Handler werden daher in der Reihenfolge aufgerufen, in der sie hinzugefügt wurden, und der Aufruf des nächsten Delegaten übergibt die Steuerung an den nächsten registrierten Ereignishandler. Wenn ein Handler den nächsten Delegaten nicht aufruft, werden keine nachfolgenden Ereignishandler aufgerufen. Das Ereignis wird kurzgeschlossen, und der Adapter sendet die Antwort nicht an den Kanal.

## <a name="handling-state-in-middleware"></a>Verarbeiten des Zustands in Middleware

Eine gängige Methode zum Speichern des Zustands ist das Aufrufen der „save changes“-Methode am Ende des Turn-Handlers. Hier ist ein Diagramm angegeben, in dem der Schwerpunkt auf dem Aufruf liegt.

![Zustand der Middleware – Probleme](media/bot-builder-dialog-state-problem.png)

Das Problem bei diesem Ansatz ist, dass alle Zustandsaktualisierungen, die über die benutzerdefinierte Middleware nach der Rückgabe des Turn-Handlers des Bots durchgeführt werden, nicht im beständigen Speicher gespeichert werden. Die Lösung besteht darin, den Aufruf der SaveChanges-Methode zu verschieben und nach dem Abschluss der benutzerdefinierten Middleware auszuführen. Dazu wird eine Instanz der _auto-save changes_-Middleware am Anfang des Middlewarestapels oder spätestens vor der Middleware, die den Zustand möglicherweise aktualisiert, hinzugefügt. Die Ausführung ist unten dargestellt.

![Zustand der Middleware – Lösung](media/bot-builder-dialog-state-solution.png)

Fügen Sie die Objekte für die Zustandsverwaltung hinzu, die eine Aktualisierung eines _BotStateSet_-Objekts erfordern, und verwenden Sie diese dann, wenn Sie Ihre „auto-save changes“-Middleware erstellen.


## <a name="additional-resources"></a>Zusätzliche Ressourcen
Sie können sich die Middleware für die Transkriptprotokollierung ansehen, die im Bot Framework SDK implementiert ist [[C#](https://github.com/Microsoft/botbuilder-dotnet/blob/master/libraries/Microsoft.Bot.Builder/TranscriptLoggerMiddleware.cs) | [JS](https://github.com/Microsoft/botbuilder-js/blob/master/libraries/botbuilder-core/src/transcriptLogger.ts)].
