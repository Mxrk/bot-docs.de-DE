---
title: Middleware | Microsoft-Dokumentation
description: Grundlagen zu Middleware und deren Verwendung im Bot SDK.
keywords: middleware, middlewarepipeline, kurzschluss, middleware verwendung
author: ivorb
ms.author: v-ivorb
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 05/24/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 9986ac7d46acfa94694456d653b91dd66c1f55f0
ms.sourcegitcommit: f95702d27abbd242c902eeb218d55a72df56ce56
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/19/2018
ms.locfileid: "39304653"
---
# <a name="middleware"></a>Middleware

[!INCLUDE [pre-release-label](~/includes/pre-release-label.md)]

Middleware ist lediglich eine Klasse zwischen dem Adapter und der Bot-Logik, die während der Initialisierung der Middlewaresammlung Ihres Adapters hinzugefügt wird. Das SDK ermöglicht Ihnen das Schreiben eigener Middleware sowie das Hinzufügen wiederverwendbarer Komponenten von Middleware, die von anderen erstellt wurde. Was können Sie in der Middleware tun? So ziemlich alles. Jede Aktivität, die in oder aus Ihrem Bot kommt, durchläuft die Middleware.

Der Adapter verarbeitet und leitet eingehende Aktivitäten durch die Bot-Middlewarepipeline zur Logik Ihres Bots und wieder zurück. Während jede Aktivität den Bot durchläuft, kann jede Middleware die Aktivität überprüfen und beeinflussen, sowohl bevor als auch nachdem die Bot-Logik ausgeführt wurde.

Bevor Sie mit Middleware arbeiten, ist es wichtig, dass Sie [Bots im Allgemeinen](~/v4sdk/bot-builder-basics.md) und [wie sie Aktivitäten verarbeiten](~/v4sdk/bot-builder-concept-activity-processing.md) verstehen.

## <a name="uses-for-middleware"></a>Verwendungsmöglichkeiten für Middleware

Oft kommt die Frage auf: was gehört im Vergleich zur Bot-Logik in die Middleware? Die Middleware ist sehr flexibel, sodass es letztendlich Ihnen überlassen ist. Im Folgenden lernen Sie gute Verwendungsmöglichkeiten für Middleware kennen.

### <a name="looking-at-or-acting-on-every-activity"></a>Ansehen oder Beeinflussen jeder Aktivität

Es gibt viele Situationen, in denen der Bot jede Aktivität oder jede Aktivität eines bestimmten Typs behandeln sollte. Angenommen, Sie möchten jedes MessageActivity-Objekt protokollieren, das der Bot empfängt, oder eine Fallbackantwort bereitstellen, wenn der Bot keine andere Antwort für diesen Turn generiert hat. Die Middleware ist ein guter Ausgangspunkt hierfür, da sie sowohl vor als auch nach der Ausführung der restlichen Bot-Logik agieren kann.

### <a name="modifying-or-enhancing-the-turn-context"></a>Ändern und Erweitern des Turn-Kontexts

Bestimmte Konversationen können erfolgreicher sein, wenn der Bot über mehr Informationen verfügt, als in der Aktivität bereitgestellt werden. In diesem Fall kann Middleware auf die bisher verfügbaren Informationen zum Konversationszustand zugreifen, eine externe Datenquelle abfragen und diese an das Kontextobjekt anfügen, bevor die Ausführung an die Bot-Logik übergeben wird.
Middleware kann beispielsweise Konversationsdetails wie die Konversations-ID und -Zustand identifizieren und anschließend einen Verzeichnisdienst für Informationen abfragen. Die Middleware kann das von der externen Abfrage empfangene Benutzerobjekt dem Kontextobjekt hinzufügen und übergeben, wodurch mehr Daten über den Benutzer bereitgestellt werden und dem Bot ermöglicht wird, die Anforderung besser zu verarbeiten.

Die Middleware kann auf beide der oben beschriebenen Weisen oder auf ganz andere Weise verwendet werden. Es hängt voll und ganz davon ab, wie Sie Ihren Bot strukturieren möchten, und was die Aufgabe des Bots ist.
Middleware kann Zustand einrichten oder beibehalten, auf eingehende Anforderungen reagieren oder die Pipeline kurzschließen.
Folgendes gehört zu den Kandidaten für die Middleware:

- **Speicher oder Persistenz:** Verwenden Sie Middleware, um Werte nach einem Turn zu speichern oder beizubehalten, oder um Werte basierend auf den Aktivitäten dieses Turns zu aktualisieren.
- **Fehlerbehandlung oder ordnungsgemäß abgebrochen:** Wenn eine Ausnahme ausgelöst wird, kann die Middleware eine benutzerfreundliche Nachricht für diese Ausnahme senden.
- **Übersetzung:** Diese Middleware kann eingehende Nachrichten erkennen und übersetzen sowie ausgehende Nachrichten zurück in die erkannte Sprache übersetzen.

Sie können die Middleware selbst definieren, oder das SDK definiert Middleware, die anwendungsunabhängige Komponenten darstellt, zum Beispiel:

- Zustandsmiddleware, die Zustandsinformationen zum Kontextobjekt beibehält und wiederherstellt. Das SDK stellt Konversations- und Benutzerzustandsmiddleware bereit.
- Übersetzungsmiddleware, die Eingabesprache erkennen und in eine andere Sprache übersetzen kann, die Ihre Anwendung versteht.
- Protokollierungsmiddleware, die eingehende und ausgehende Aktivitäten protokollieren kann.

## <a name="the-bot-middleware-pipeline"></a>Die Bot-Middlewarepipeline

Der Adapter ruft für jede Aktivität Middleware in der Reihenfolge auf, **in der Sie sie hinzugefügt haben**. Der Adapter übergibt das Kontextobjekt für den Turn und einen _next_-Delegaten, und die Middleware ruft den Delegaten auf, um die Steuerung an die nächste Middleware in der Pipeline zu übergeben. Middleware hat auch die Möglichkeit, Aktionen durchzuführen, nachdem der _next_-Delegat zurückgegeben wird, bevor die Methode abgeschlossen wird. Sie können es sich so vorstellen, dass jedes Middleware-Objekt die Möglichkeit hat, in Bezug auf Middleware-Objekte, die in der Pipeline folgen, als erstes und als letztes zu agieren.

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

Die ersten Schritte in Ihrer Pipeline sollten wahrscheinlich die sein, die sich um die allgemeinsten Aufgaben kümmern und jedes Mal verwendet werden. Dazu gehören beispielsweise die Protokollierung, die Ausnahmebehandlung, die Zustandsverwaltung und die Übersetzung. Die Reihenfolge kann je nach Bedarf variieren, wenn die eingehende Nachricht beispielsweise zuerst übersetzt werden soll, bevor Ausnahmen behandelt werden, oder wenn die Ausnahmebehandlung zuerst stattfinden soll, was bedeuten kann, dass Ausnahmemeldungen nicht übersetzt werden.

Die letzten Schritte in Ihrer Middleware-Pipeline sollten sich auf Bot-spezifische Middleware beziehen. Also Middleware, die Sie implementieren, um jede Nachricht an den Bot zu verarbeiten. Wenn Ihre Middleware Zustandsinformationen oder andere Informationen im Kontext des Bots verwendet, fügen Sie sie nach der Middleware, die den Zustand oder Kontext ändert, der Middleware-Pipeline hinzu.

## <a name="short-circuiting"></a>Kurzschlüsse

_Kurzschlüsse_ sind ein wichtiges Konzept bei der Middleware (und bei [Ereignishandlern](~/v4sdk/bot-builder-concept-activity-processing.md#response-event-handlers)). Wenn die Ausführung durch die nachfolgenden Ebenen fortgesetzt werden soll, ist eine Middleware (oder ein Handler) erforderlich, um die Ausführung durch Aufrufen des _next_-Delegaten zu übergeben.  Wenn der next-Delegat in der Middleware (oder dem Handler) aufgerufen wird, wird die aktuelle Pipeline kurzgeschlossen, und die nachfolgenden Ebenen werden nicht ausgeführt. Das bedeutet, dass die gesamte Bot-Logik sowie jegliche Middleware im späteren Verlauf der Pipeline übersprungen werden.

Wenn _next_ bei Ereignishandlern nicht aufgerufen wird, wird das Ereignis abgebrochen, was ein deutlich anderes Ergebnis als das Überspringen der Logik durch die Middleware ist. Wenn der Rest des Ereignisses nicht verarbeitet wird, sendet der Adapter es nicht.

> [!TIP]
> Stellen Sie sicher, dass es dem gewünschten Verhalten entspricht, wenn Sie ein Ereignis wie `SendActivities` kurzschließen. Andernfalls kann dies zu verwirrenden Fehlern führen.

## <a name="next-steps"></a>Nächste Schritte

Da Sie nun mit einigen grundlegenden Konzepten von Bots vertraut sind, sehen Sie sich an, wie ein Bot proaktive Nachrichten senden kann.

> [!div class="nextstepaction"]
> [Proactive Messaging (Proaktive Nachrichten)](~/v4sdk/bot-builder-proactive-messages.md)
