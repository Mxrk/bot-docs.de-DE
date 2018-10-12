---
title: Aktivitätsverarbeitung | Microsoft-Dokumentation
description: Verstehen Sie die Aktivitätsverarbeitung im Bot SDK.
keywords: botadapter, benutzerdefinierte middleware, kurzschluss, fallback, ereignishandler
author: jonathanfingold
ms.author: jonathanfingold
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 09/13/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: fbcc9532e39fab96b6794c80c29dfc349ad552b1
ms.sourcegitcommit: 3bf3dbb1a440b3d83e58499c6a2ac116fe04b2f6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/23/2018
ms.locfileid: "46707706"
---
# <a name="activity-processing"></a>Aktivitätsverarbeitung

[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

Der Bot und der Benutzer interagieren und tauschen Informationen über Aktivitäten aus. Jede von Ihrer Botanwendung empfangene Aktivität wird an einen Botadapter weitergeleitet, der die Aktivitätsinformationen an Ihre Botlogik weiterleitet und schließlich alle Antworten an den Benutzer sendet. Das Empfangen einer Aktivität und ihre Verarbeitung durch den Bot wird als „Durchlauf“ bezeichnet. Ein Durchlauf stellt einen kompletten Zyklus des Bots dar. Ein Durchlauf endet, wenn alle Ausführungen und Botebenen abgeschlossen sind und die Aktivität vollständig verarbeitet wurde.

Aktivitäten, insbesondere die, die während eines Botdurchlaufs von [einem Bot gesendet werden](#generating-responses), werden asynchron verarbeitet. Es ist ein notwendiger Teil der Erstellung eines Bot; wenn Sie Ihre Kenntnisse über die Funktionsweise auffrischen möchten, lesen Sie [async für .NET](https://docs.microsoft.com/en-us/dotnet/csharp/async) oder [async für JavaScript](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/async_function) je nach Ihrer Sprachwahl.

## <a name="the-bot-adapter"></a>Der Botadapter

Der Botadapter kapselt Authentifizierungsprozesse ein und sendet Aktivitäten an bzw. empfängt Aktivitäten vom Bot Connector Service. Wenn Ihr Bot eine Aktivität empfängt, wickelt der Adapter alles über diese Aktivität ab, erstellt ein [Kontextobjekt](#turn-context) für den Durchlauf, leitet es an die Anwendungslogik Ihres Bot weiter und sendet die von Ihrem Bot generierten Antworten an den Kanal des Benutzers zurück.

## <a name="authentication"></a>Authentifizierung

Der Adapter authentifiziert jede eingehende Aktivität, die die Anwendung erhält, anhand von Informationen aus der Aktivität und dem `Authentication`-Header aus der REST-Anforderung. Der Adapter verwendet ein Verbindungsobjekt und die Anmeldeinformationen Ihrer Anwendung, um die ausgehenden Aktivitäten für den Benutzer zu authentifizieren.

Bot Connector Service-Authentifizierung verwendet JWT (JSON Web Token)-`Bearer`-Token und die **Microsoft-App-ID** und das **Microsoft-App-Kennwort**, die Azure für Sie erstellt, wenn Sie einen Botdienst erstellen oder Ihren Bot registrieren. Ihre Anwendung benötigt diese Anmeldeinformationen bei der Initialisierung, damit der Adapter den Datenverkehr authentifizieren kann.

> [!NOTE]
> Wenn Sie Ihren Bot lokal ausführen oder testen, z.B. mit dem Bot Framework Emulator, ist dies möglich, ohne den Adapter für die Authentifizierung des Datenverkehrs von und zu Ihrem Bot zu konfigurieren.

## <a name="turn-context"></a>Durchlaufkontext

Wenn ein Adapter eine Aktivität empfängt, erzeugt er ein **Durchlaufkontextobjekt**, das Informationen über die eingehende Aktivität, den Sender und Empfänger, den Kanal, die Konversation und andere Daten liefert, die zur Verarbeitung der Aktivität benötigt werden. Der Adapter übergibt dieses Kontextobjekt dann an den Bot. Das Kontextobjekt wird für die Dauer eines Durchlaufs beibehalten und stellt folgende Informationen bereit:

* Konversation – Identifiziert die Konversation und enthält Informationen über den Bot und den Benutzer, der an der Konversation teilnimmt.
* Aktivität – Die Anforderungen und Antworten in einer Konversation sind alles Arten von Aktivitäten. Dieser Kontext liefert Informationen über die eingehende Aktivität, einschließlich Routinginformationen, Informationen über den Kanal, die Konversation, den Sender und den Empfänger.
* Benutzerdefinierte Informationen – Wenn Sie Ihren Bot entweder durch die Implementierung von Middleware oder innerhalb Ihrer Botlogik erweitern, können Sie in jedem Durchlauf zusätzliche Informationen zur Verfügung stellen.

Das Kontextobjekt kann auch verwendet werden, um eine Antwort an den Benutzer zu senden und einen Verweis auf den Adapter zu erhalten<!-- to create a new conversation or continue an existing one-->.

> [!NOTE]
> Ihre Anwendung und der Adapter behandeln Anforderungen asynchron; Ihre Geschäftslogik muss jedoch nicht von Anforderung/Antwort gesteuert werden.

## <a name="middleware"></a>Middleware

Sie können Middleware zum Adapter hinzufügen. Die Middleware und die Botlogik nutzen das Kontextobjekt, um Informationen über die Aktivität abzurufen und entsprechend zu handeln. Die Middleware und der Bot können zudem Informationen zum Kontextobjekt aktualisieren oder hinzufügen, z.B. um den Zustand eines Durchlaufs, einer Konversation oder eines anderen Bereichs zu verfolgen. Weitere ausführliche Informationen zu Middleware finden Sie im [Middleware-Artikel](~/v4sdk/bot-builder-concept-middleware.md).

## <a name="generating-responses"></a>Generieren von Antworten

Das Kontextobjekt stellt Methoden zur Verfügung, mit denen der Code auf eine Aktivität reagieren kann:

* Die Methoden _send activity_ und _send activities_ senden eine oder mehrere Aktivitäten an die Konversation.
* Die _update activity_-Methode aktualisiert eine Aktivität innerhalb der Konversation, wenn dies vom Kanal unterstützt wird.
* Die _delete activity_-Methode entfernt eine Aktivität aus der Konversation, wenn dies vom Kanal unterstützt wird.

Jede Antwortmethode wird in einem asynchronen Prozess ausgeführt. Beim Aufruf klont die Aktivitätsantwortmethode die zugeordnete Liste mit [Ereignishandlern](#response-event-handlers), bevor sie mit dem Aufrufen des Handlers beginnt. Sie enthält also alle bis zu diesem Zeitpunkt hinzugefügten Handler, ist nach dem Start des Prozesses aber leer.

Dies bedeutet auch, dass die Reihenfolge Ihrer Antworten nicht garantiert ist, insbesondere wenn eine Aufgabe komplexer ist als die andere. Wenn Ihr Bot mehrere Antworten auf eine eingehende Aktivität generieren kann, stellen Sie sicher, dass diese in der Reihenfolge sinnvoll sind, in der sie vom Benutzer empfangen werden.

> [!IMPORTANT]
> Der Thread, der den primären Botdurchlauf verarbeitet, löscht am Ende das Kontextobjekt. Wenn eine Antwort (einschließlich ihrer Handler) eine gewisse Zeit in Anspruch nimmt und versucht, auf das Kontextobjekt einzuwirken, kann es zu einem `Context was disposed`-Fehler kommen. **Stellen Sie sicher, dass Sie auf alle Aktivitätsaufrufe`await`**, sodass der primäre Thread auf die generierte Aktivität wartet, bevor er die Verarbeitung beendet und den Durchlaufkontext löscht.

## <a name="response-event-handlers"></a>Antwortereignishandler

Zusätzlich zur Bot- und Middlewarelogik können Antworthandler (manchmal auch als Ereignishandler oder Aktivitätsereignishandler bezeichnet) zum Kontextobjekt hinzugefügt werden. Diese Ereignishandler werden aufgerufen, wenn die dazugehörige [Antwort](#generating-responses) im aktuellen Kontextobjekt vor der Ausführung der tatsächlichen Antwort stattfindet. Diese Handler sind nützlich, wenn Sie wissen, dass Sie vor oder nach dem eigentlichen Ereignis etwas für jede Aktivität dieses Typs für den Rest der aktuellen Antwort tun wollen.

> [!WARNING]
> Achten Sie darauf, eine Aktivitätsantwortmethode nicht aus ihrem jeweiligen Antwortereignishandler heraus aufzurufen, z.B. indem Sie die send activity-Methode aus einem _on send activity_-Handler heraus aufrufen. Anderenfalls wird eine Endlosschleife generiert.

Jede neue Aktivität erhält einen neuen Thread für die Ausführung. Wenn der Thread zur Verarbeitung der Aktivität erstellt wird, wird die Liste der Handler für diese Aktivität in den neuen Thread kopiert. Handler, die nach diesem Punkt hinzugefügt wurden, werden für dieses spezielle Aktivitätsereignis nicht ausgeführt.

Die unter einem Kontextobjekt registrierten Handler werden auf eine Weise verarbeitet, die der Verwaltung der [Middlewarepipeline](~/v4sdk/bot-builder-concept-middleware.md#the-bot-middleware-pipeline) durch den Adapter ähnelt. Das heißt, dass Handler in der Reihenfolge aufgerufen werden, in der sie hinzugefügt wurden, und der Aufruf des _nächsten_ Delegaten übergibt die Kontrolle an den nächsten registrierten Ereignishandler. Wenn ein Handler den nächsten Delegaten nicht aufruft, werden keine nachfolgenden Ereignishandler aufgerufen. Das Ereignis wird [kurzgeschlossen](~/v4sdk/bot-builder-concept-middleware.md#short-circuiting), und der Adapter sendet die Antwort nicht an den Kanal.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Middleware](~/v4sdk/bot-builder-concept-middleware.md)
