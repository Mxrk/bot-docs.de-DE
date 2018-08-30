---
title: Aktivitätsverarbeitung | Microsoft-Dokumentation
description: Verstehen Sie die Aktivitätsverarbeitung im Bot SDK.
keywords: botadapter, benutzerdefinierte middleware, kurzschluss, fallback, ereignishandler
author: jonathanfingold
ms.author: jonathanfingold
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 03/22/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 278005c25e98c7c5b7d523030846909aed830224
ms.sourcegitcommit: 2dc75701b169d822c9499e393439161bc87639d2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/24/2018
ms.locfileid: "42905353"
---
# <a name="activity-processing"></a>Aktivitätsverarbeitung

[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

Der Bot und der Benutzer tauschen Informationen über Aktivitäten aus. Jede von Ihrer Botanwendung empfangene Aktivität wird an einen Botadapter weitergeleitet, der die Aktivitätsinformationen an Ihre Botlogik weiterleitet und schließlich alle Antworten an den Benutzer sendet.

[!INCLUDE [Define a turn](~/includes/snippet-definition-turn.md)]

> [!IMPORTANT]
> Aktivitäten, insbesondere die, die während eines Botdurchlaufs von [uns generiert werden](#generating-responses), werden asynchron verarbeitet. Es ist ein notwendiger Teil der Erstellung eines Bot; wenn Sie Ihre Kenntnisse über die Funktionsweise auffrischen möchten, lesen Sie [async für .NET](https://docs.microsoft.com/en-us/dotnet/csharp/async) oder [async für JavaScript](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/async_function) je nach Ihrer Sprachwahl.

## <a name="the-bot-adapter"></a>Der Botadapter

Ein Bot wird von seinem **Adapter** gesteuert. Das ist sozusagen der Leiter Ihres Bots. Der Adapter ist verantwortlich für die Authentifizierung, das Routing von ein- und ausgehender Kommunikation usw. Der Adapter unterscheidet sich je nach Umgebung (der Adapter arbeitet lokal anders als auf Azure), erreicht aber in jeder Instanz das gleiche Ziel. In den meisten Situationen arbeiten wir nicht direkt mit dem Adapter, z.B. wenn wir einen Bot aus einer Vorlage erstellen, aber es ist gut zu wissen, dass er da ist und wie er funktioniert.

Der Botadapter kapselt Authentifizierungsprozesse ein und sendet Aktivitäten an bzw. empfängt Aktivitäten vom Bot Connector Service. Wenn Ihr Bot eine Aktivität empfängt, wickelt der Adapter alles über diese Aktivität ab, erstellt ein [Kontextobjekt](#turn-context) für den Durchlauf, leitet es an die Anwendungslogik Ihres Bot weiter und sendet die von Ihrem Bot generierten Antworten an den Kanal des Benutzers zurück.

## <a name="authentication"></a>Authentifizierung

Der Adapter authentifiziert jede eingehende Aktivität, die die Anwendung erhält, anhand von Informationen aus der Aktivität und dem `Authentication`-Header aus der REST-Anforderung.

Der Adapter verwendet ein Verbindungsobjekt und die Anmeldeinformationen Ihrer Anwendung, um die ausgehenden Aktivitäten für den Benutzer zu authentifizieren.

Bot Connector Service-Authentifizierung verwendet JWT (JSON Web Token)-`Bearer`-Token und die **Microsoft-App-ID** und das **Microsoft-App-Kennwort**, die Azure für Sie erstellt, wenn Sie einen Botdienst erstellen oder Ihren Bot registrieren. Ihre Anwendung benötigt diese Anmeldeinformationen bei der Initialisierung, damit der Adapter den Datenverkehr authentifizieren kann.

> [!NOTE]
> Wenn Sie Ihren Bot lokal ausführen oder testen, z.B. mit dem Bot Framework Emulator, ist dies möglich, ohne den Adapter für die Authentifizierung des Datenverkehrs von und zu Ihrem Bot zu konfigurieren.

## <a name="turn-context"></a>Durchlaufkontext

Wenn ein Adapter eine Aktivität empfängt, erzeugt er ein **Durchlaufkontextobjekt**, das Informationen über die eingehende Aktivität, den Sender und Empfänger, den Kanal, die Konversation und andere Daten liefert, die zur Verarbeitung der Aktivität benötigt werden. Der Adapter übergibt dieses Kontextobjekt dann an den Bot. Das Kontextobjekt wird für die Dauer eines Durchlaufs beibehalten und stellt folgende Informationen bereit:

* Konversation – Identifiziert die Konversation und enthält Informationen über den Bot und den Benutzer, der an der Konversation teilnimmt.
* Aktivität – Die Anforderungen und Antworten in einer Konversation sind alles Arten von Aktivitäten. Dieser Kontext liefert Informationen über die eingehende Aktivität, einschließlich Routinginformationen, Informationen über den Kanal, die Konversation, den Sender und den Empfänger.
* Zustand – Eigenschaften, die verwendet werden, um seinen [Zustand](~/v4sdk/bot-builder-storage-concept.md) zu verfolgen, z.B. an welcher Stelle er sich in einer Konversation mit einem Benutzer befindet, Informationen über diesen Benutzer oder andere Geschäftslogikinformationen.
* Benutzerdefinierte Informationen – Wenn Sie Ihren Bot entweder durch die Implementierung von Middleware oder innerhalb Ihrer Botlogik erweitern, können Sie in jedem Durchlauf zusätzliche Informationen zur Verfügung stellen.

Das Kontextobjekt kann auch verwendet werden, um eine Antwort an den Benutzer zu senden und eine Referenz auf den Adapter zu erhalten, um eine neue Konversation anzulegen oder eine bestehende fortzusetzen.

> [!NOTE]
> Ihre Anwendung und der Adapter behandeln Anforderungen asynchron; Ihre Geschäftslogik muss jedoch nicht von Anforderung/Antwort gesteuert werden.

## <a name="middleware"></a>Middleware

Sie können Middleware zum Adapter hinzufügen. Middleware sind Ebenen von Plug-Ins, die zu Ihrem Bot und Ihrer Botkernlogik hinzugefügt werden. Weitere ausführliche Informationen zu Middleware finden Sie in diesem unabhängigen [Middleware-Artikel](~/v4sdk/bot-builder-concept-middleware.md).

Die Middleware und die Botlogik nutzen das Kontextobjekt, um Informationen über die Aktivität abzurufen und entsprechend zu handeln. Die Middleware und der Bot können zudem Informationen zum Kontextobjekt aktualisieren oder hinzufügen, z.B. um den Zustand eines Durchlaufs, einer Konversation oder eines anderen Bereichs zu verfolgen. Das SDK enthält einige _Zustandsmiddleware_, die Sie zum Hinzufügen von Zustandspersistenz zu Ihrem Bot verwenden können.

## <a name="generating-responses"></a>Generieren von Antworten

Das Kontextobjekt stellt Methoden zur Verfügung, mit denen der Code auf eine Aktivität reagieren kann:

* Die _send activity_- und _send activities_-Methoden senden eine oder mehrere Aktivitäten an die Konversation.
* Die _update activity_-Methode aktualisiert eine Aktivität innerhalb der Konversation, wenn dies vom Kanal unterstützt wird.
* Die _delete activity_-Methode entfernt eine Aktivität aus der Konversation, wenn dies vom Kanal unterstützt wird.

Jede Antwortmethode wird in einem asynchronen Prozess ausgeführt. Beim Aufruf klont die Aktivitätsantwortmethode die zugehörige Ereignisbehandlerliste, bevor sie mit dem Aufrufen des Handlers beginnt, d.h. sie enthält alle bis zu diesem Zeitpunkt hinzugefügten Handler, ist aber nach dem Start des Prozesses leer.

Dies bedeutet auch, dass die Reihenfolge Ihrer Antworten nicht garantiert ist, insbesondere wenn eine Aufgabe komplexer ist als die andere. Wenn Ihr Bot mehrere Antworten auf eine eingehende Aktivität generieren kann, stellen Sie sicher, dass diese in der Reihenfolge sinnvoll sind, in der sie vom Benutzer empfangen werden.

> [!IMPORTANT]
> Der Thread, der den primären Botdurchlauf verarbeitet, löscht am Ende das Kontextobjekt. Wenn eine Antwort (einschließlich ihrer Handler) eine gewisse Zeit in Anspruch nimmt und versucht, auf das Kontextobjekt einzuwirken, kann es zu einem `Context was disposed`-Fehler kommen. **Stellen Sie sicher, dass Sie auf alle Aktivitätsaufrufe`await`**, sodass der primäre Thread auf die generierte Aktivität wartet, bevor er die Verarbeitung beendet und den Durchlaufkontext löscht.

## <a name="response-event-handlers"></a>Antwortereignishandler

Zusätzlich zur Bot- und Middlewarelogik können Antworthandler (manchmal auch als Ereignishandler oder Aktivitätsereignishandler bezeichnet) zum Kontextobjekt hinzugefügt werden. Diese Ereignishandler werden aufgerufen, wenn die dazugehörige Antwort im aktuellen Kontextobjekt vor der Ausführung der tatsächlichen Antwort stattfindet. Diese Handler sind nützlich, wenn Sie wissen, dass Sie vor oder nach dem eigentlichen Ereignis etwas für jede Aktivität dieses Typs für den Rest der aktuellen Antwort tun wollen.

> [!WARNING]
> Achten Sie darauf, eine Aktivitätsantwortmethode nicht aus ihrem jeweiligen Antwortereignishandler heraus aufzurufen, z.B. indem Sie die send activity-Methode aus einem _on send activity_-Handler heraus aufrufen. Anderenfalls wird eine Endlosschleife generiert.

Jede neue Aktivität erhält einen neuen Thread für die Ausführung. Wenn der Thread zur Verarbeitung der Aktivität erstellt wird, wird die Liste der Handler für diese Aktivität in den neuen Thread kopiert. Handler, die nach diesem Punkt hinzugefügt wurden, werden für dieses spezielle Aktivitätsereignis nicht ausgeführt.

Der Adapter verwaltet die auf einem Kontextobjekt registrierten Handler ähnlich wie die [Middlewarepipeline](~/v4sdk/bot-builder-concept-middleware.md#the-bot-middleware-pipeline).

Das heißt, dass Handler in der Reihenfolge aufgerufen werden, in der sie hinzugefügt wurden, und der Aufruf des _nächsten_ Delegaten übergibt die Kontrolle an den nächsten registrierten Ereignishandler. Wenn ein Handler den nächsten Delegaten nicht aufruft, ruft der Adapter keinen der nachfolgenden Ereignishandler auf; das Ereignis wird [kurzgeschlossen](~/v4sdk/bot-builder-concept-middleware.md#short-circuiting), und der Adapter sendet die Antwort nicht an den Kanal.

## <a name="next-steps"></a>Nächste Schritte

Da Sie nun mit einigen grundlegenden Konzepten von Bots vertraut sind, sehen Sie sich an, wie ein Bot proaktive Nachrichten senden kann.

> [!div class="nextstepaction"]
> [Middleware](~/v4sdk/bot-builder-concept-middleware.md)
