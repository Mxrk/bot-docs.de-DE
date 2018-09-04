---
title: Richtlinien für das Testen und Debuggen | Microsoft-Dokumentation
description: Verstehen Sie, wie Sie Ihren Bot testen und debuggen.
keywords: Testprinzipien, Modellelemente, häufig gestellte Fragen, Testebenen
author: ivorb
ms.author: v-ivorb
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 04/09/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: caa424ed0ea0944805836739ed48a7a61f78d21c
ms.sourcegitcommit: 2dc75701b169d822c9499e393439161bc87639d2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/24/2018
ms.locfileid: "42905259"
---
# <a name="testing-and-debugging-guidelines"></a>Richtlinien für das Testen und Debuggen

[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

Bots sind komplexe Apps, bei denen viele verschiedene Komponenten zusammenarbeiten. Wie bei jeder komplexen App kann dies zu einigen interessanten Fehlern führen oder bei Ihrem Bot ein anderes Verhalten als das erwartete verursachen.

Das Testen und anschließende Debuggen Ihres Bots kann eine schwierige Aufgabe sein. Jeder Entwickler hat eine eigene bevorzugte Methode für diese Aufgabe, und die unten angegebenen Richtlinien sind Vorschläge zu Ihrer Verwendung, die für die überwiegende Mehrheit aller Bots gelten.

## <a name="testing-your-bot"></a>Testen des Bots

Die nachstehenden Richtlinien werden auf drei verschiedenen **Ebenen** präsentiert.  Auf jeder Ebene kommen zu den Tests weitere Komplexität und Features hinzu, daher wird empfohlen, dass Sie mit einer Ebene zufrieden sind, bevor Sie mit der nächsten fortfahren. So können Sie zuerst die Probleme auf der niedrigsten Ebene beheben, bevor Sie weitere Komplexität hinzunehmen.

Die bewährten Testmethoden behandeln nach Möglichkeit verschiedene Blickwinkel. Dies kann Sicherheit, Integration, fehlgeformte URLs, Sicherheitslücken bei der Validierung, HTTP-Statuscodes, JSON-Nutzlasten, NULL-Werte usw. umfassen. Wenn Ihr Bot Informationen verarbeitet, die Auswirkungen auf die Privatsphäre der Benutzer haben, ist dies besonders wichtig.

### <a name="level-1-use-mock-elements"></a>Ebene 1: Verwenden von Modellelementen

Die erste Ebene des Testens besteht darin, sicherzustellen, dass jeder kleine Teil der App (oder in diesem Fall des Bots) so funktioniert, wie er sollte. Dazu können Sie Modellelemente für die Dinge verwenden, die Sie derzeit nicht testen. Zu Referenzzwecken kann diese Ebene grundsätzlich als Einheiten- und Integrationstest betrachtet werden.

**Verwenden von Modellelementen zum Testen einzelner Abschnitte**

Wenn Sie so viele Elemente wie möglich modellieren, erhalten Sie eine bessere Isolierung des zu testenden Elements. Zu den Kandidaten für Modellelemente gehören Speicher, Adapter, Middleware, Aktivitätspipeline, Kanäle und alle weitere Elemente, die nicht direkt zu Ihrem Bot gehören. Dabei können auch bestimmte Aspekte vorübergehend entfernt werden, um jedes Element zu isolieren, etwa Middleware, die nicht an den zu testenden Teilen Ihres Bots beteiligt ist. Wenn Sie Ihre Middleware testen, sollten Sie jedoch möglicherweise stattdessen Ihren Bot modellieren.

Das Modellieren von Elementen kann eine Reihe von Formen annehmen, vom Ersetzen eines Elements durch ein anderes bekanntes Objekt bis hin zum Implementieren einer grundlegenden Hallo Welt-Funktionalität. Dabei kann das Element auch einfach entfernt werden, wenn es nicht erforderlich ist, oder es kann Passivität erzwungen werden. 

Auf dieser Ebene sollten einzelne Methoden und Funktionen in Ihrem Bot verwendet werden. Das Testen einzelner Methoden kann über integrierte Einheitentests mit Ihrer eigenen Test-App oder Testsuite erfolgen (empfohlen) oder manuell in Ihrer IDE. 

**Verwenden von Modellelementen für Tests größerer Features**

Sobald Sie mit dem Verhalten jeder Methode zufrieden sind, verwenden Sie diese Modellelemente, um umfassendere Features in Ihrem Bot zu testen. Hier wird veranschaulicht, wie einige Schichten zusammenarbeiten, um eine Konversation mit Ihrem Benutzer zu führen. 

Zur Unterstützung bei dieser Herangehensweise wird eine Reihe von Tools bereitgestellt. Beispielsweise bietet der [Azure Bot Framework-Emulator](https://github.com/Microsoft/BotFramework-Emulator) einen emulierten Kanal für die Kommunikation mit Ihrem Bot. Der Emulator erzeugt eine komplexere Situation als nur Einheitentests und Integrationstests und überschneidet sich daher auch bereits mit der nächsten Ebene von Tests.

### <a name="level-2-use-a-direct-line-client"></a>Ebene 2: Verwenden eines Direct Line-Clients

Nachdem Sie sich vergewissert haben, dass Ihr Bot wunschgemäß funktioniert, besteht der nächste Schritt darin, ihn mit einem Kanal zu verbinden. Zu diesem Zweck können Sie Ihren Bot auf einem Stagingserver bereitstellen und einen eigenen [Direct Line-Client](bot-builder-howto-direct-line.md) für die Verbindung mit Ihrem Bot erstellen.

Durch das Erstellen eines eigenen Clients können Sie die interne Funktionsweise des Kanals definieren und spezifisch testen, wie Ihr Bot auf den Austausch bestimmter Aktivitäten reagiert. Sobald die Verbindung mit Ihrem Client besteht, führen Sie die Tests aus, um den Botzustand einzurichten und Ihre Features zu überprüfen. Wenn Ihr Bot ein Feature wie Spracherkennung nutzt, können Sie diese Funktionalität mithilfe dieser Kanäle überprüfen.

Wenn Sie hier den Emulator und Webchat über das Azure-Portal verwenden, können Sie weiter Einblicke in die Leistung Ihres Bots bei der Interaktion mit verschiedenen Kanälen erhalten.

### <a name="level-3-channel-tests"></a>Ebene 3: Kanaltests

Sobald Sie mit der unabhängigen Leistung Ihres Bots zufrieden sind, müssen Sie überprüfen, wie er mit verschiedenen Kanälen, in denen er verfügbar sein soll, funktioniert. 

Dazu gibt es sehr unterschiedliche Möglichkeiten, von der einzelnen Verwendung verschiedener Kanäle und Browser bis hin zur Verwendung eines Drittanbietertools wie [Selenium](https://docs.seleniumhq.org/) für die Interaktion über einen Kanal und das Extrahieren der Antworten von Ihrem Bot.

### <a name="other-testing"></a>Andere Tests

Verschiedene Arten von Tests können zusätzlich zu den oben genannten Ebenen oder aus verschiedenen Blickwinkeln durchgeführt werden, z.B. Belastungstests, Leistungstests oder eine Profilerstellung für die Aktivität Ihres Bots. Visual Studio bietet dazu lokale Methoden sowie eine [Suite von Tools](https://www.visualstudio.com/team-services/testing-tools/) zum Testen Ihrer App, und das [Azure-Portal](https://portal.azure.com) bietet einen Einblick in die Leistung Ihres Bots.

## <a name="debugging"></a>Debuggen

Das Debuggen des Bots funktioniert ähnlich wie bei anderen Apps mit mehreren Threads: Sie können Haltepunkte festlegen oder Features wie das Direktfenster verwenden. 

Bots folgen einem ereignisgesteuerten Programmierparadigma, das möglicherweise schwierig zu rationalisieren ist, wenn Sie nicht mit ihm vertraut sind. Die Idee, dass der Bot zustandslos ist, mehrere Threads aufweist und asynchrone oder Warte-Aufrufe behandeln muss, kann zu unerwarteten Fehlern führen. Auch wenn das Debuggen Ihres Bots ähnlich wie bei anderen Apps mit mehreren Threads funktioniert, finden Sie hier einige Vorschläge, Tools und Ressourcen zur Hilfestellung.

**Grundlegendes zu Botaktivitäten mit dem Emulator**

Der Bot behandelt unterschiedliche Typen von [Aktivitäten](bot-builder-concept-activity-processing.md) neben der normalen _Nachrichtenaktivität_. Über den [Emulator](../bot-service-debug-emulator.md) erfahren Sie, um welche Aktivitäten es sich handelt, wann sie stattfinden und welche Informationen sie enthalten. Ein Verständnis dieser Aktivitäten hilft Ihnen dabei, Ihren Bot effizienter zu schreiben, und ermöglicht Ihnen, zu überprüfen, ob die gesendeten und empfangenen Aktivitäten des Bots Ihren Erwartungen entsprechen.

**Funktionsweise von Middleware**

[Middleware](bot-builder-concept-middleware.md) ist bei der ersten Verwendung möglicherweise nicht intuitiv, insbesondere beim Fortsetzen oder Kurzschließen der Ausführung. Middleware kann zu Beginn oder zum Ende eines Durchlaufs ausgeführt werden. Dabei legt der Aufruf des Delegats `next()` fest, wann die Ausführung an die Logik des Bots übergeben wird. 

Wenn Sie mehrere Middlewareelemente verwenden, kann der Delegat die Ausführung an eine andere Middleware übergeben, wenn Ihre Pipeline entsprechend ausgerichtet ist. Details zur [Middleware-Pipeline des Bots](bot-builder-concept-middleware.md#the-bot-middleware-pipeline) können dazu beitragen, diese Idee zu verdeutlichen.

Das Nichtaufrufen des Delegats `next()` wird als [Kurzschlussweiterleitung](bot-builder-concept-middleware.md#short-circuiting) bezeichnet. Dies geschieht, wenn die Middleware die aktuelle Aktivität erfüllt und festlegt, dass es nicht erforderlich ist, die Ausführung zu übergeben. 

Das Verständnis, wann und warum Middleware einen Kurzschluss ausführt, kann einen Hinweis darauf bieten, welche Middleware in Ihrer Pipeline zuerst eingesetzt werden soll. Außerdem ist eine klare Erwartung besonders wichtig für integrierte Middleware, die durch das SDK oder andere Entwickler bereitgestellt wird. Es kann hilfreich sein, zuerst [eigene Middleware zu erstellen](bot-builder-create-middleware.md), um vor dem Einstieg in die integrierte Middleware ein wenig zu experimentieren.

[QnA Maker](bot-builder-howto-qna.md) behandelt beispielweise bestimmte Interaktionen und schließt die Pipeline dann kurz. Dies kann beim Erlernen seiner Verwendung verwirrend sein.

**Grundlegendes zum Zustand**

Die Überwachung des Zustands ist ein wichtiger Bestandteil Ihres Bots, insbesondere für komplexe Aufgaben. Im Allgemeinen hat es sich bewährt, Aktivitäten möglichst schnell zu verarbeiten und die Verarbeitung abzuschließen, sodass der Zustand beibehalten wird. Aktivitäten können aufgrund der asynchronen Architektur zur nahezu selben Zeit an Ihren Bot gesendet werden, und dies kann zu sehr verwirrenden Fehlern führen.

Stellen Sie vor allem sicher, dass der Status ihren Erwartungen entsprechend beibehalten wird. Abhängig davon, wo sich Ihr persistenter Zustand befindet, können Sie ihn mithilfe von Speicheremulatoren für [Cosmos DB](https://docs.microsoft.com/en-us/azure/cosmos-db/local-emulator) und [Azure Table Storage](https://docs.microsoft.com/en-us/azure/storage/common/storage-use-emulator) überprüfen, bevor Sie Produktionsspeicher verwenden.

**Verwenden von Aktivitätshandlern**

Aktivitätshandler können eine weitere Ebene der Komplexität einführen, in erster Linie dadurch, dass jede Aktivität in einem unabhängigen Thread (oder abhängig von Ihrer Sprache in Webworkern) ausgeführt wird. Je nach den Aktionen Ihrer Handler kann dies Probleme verursachen, bei denen der aktuelle Zustand nicht Ihren Erwartungen entspricht.

Der integrierte Zustand wird am Ende eines Durchlaufs geschrieben, während dieses Durchlaufs generierte Aktivitäten werden jedoch unabhängig von der Durchlaufpipeline ausgeführt. Häufig hat dies keine Auswirkung, aber wenn ein Aktivitätshandler den Zustand ändert, muss der Zustand geschrieben werden, damit er diese Änderung enthält. In diesem Fall kann die Durchlaufpipeline vor dem Abschluss warten, bis die Aktivität abgeschlossen ist, um sicherzustellen, dass für diesen Durchlauf der richtige Zustand aufgezeichnet wird.

Die Methoden zum _Senden einer Aktivität_ und der zugehörige Handler stellen ein besonderes Problem dar, wenn Sie etwas an den Benutzer ausgeben möchten, da das einfache _Senden der Aktivität_ innerhalb dieser Methode eine unendliche Aufspaltung von Threads bewirkt. Sie können dieses Problem auf verschiedene Weise umgehen, z.B. durch Anfügen der Debugmeldung an die ausgehenden Informationen oder das Schreiben an einen anderen Speicherort wie die Konsole oder eine Datei, um einen Absturz Ihres Bots zu vermeiden.


## <a name="additional-resources"></a>Zusätzliche Ressourcen
* [Debuggen in Visual Studio](https://docs.microsoft.com/en-us/visualstudio/debugger/index)
* [Debugging, Ablaufverfolgung und Profilerstellung](https://docs.microsoft.com/en-us/dotnet/framework/debug-trace-profile/) für Bot Framework
* Verwenden von [ConditionalAttribute](https://docs.microsoft.com/en-us/dotnet/api/system.diagnostics.conditionalattribute?view=netcore-2.0) für Methoden, die nicht im Produktionscode enthalten sein sollen 
* Verwenden von Tools wie [Fiddler](https://www.telerik.com/fiddler) zum Einsehen des Netzwerkverkehrs 
* [Bottoolrepository](https://github.com/Microsoft/botbuilder-tools)
* Frameworks können beim Testen helfen, z.B. [Moq](https://github.com/moq/moq4).
