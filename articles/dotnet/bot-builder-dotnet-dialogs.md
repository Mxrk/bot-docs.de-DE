---
title: Übersicht über Dialoge | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Dialoge im Bot Builder SDK für .NET verwenden, um Unterhaltungen zu modellieren und den Konversationsfluss zu verwalten.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: aa56a91d2246b5d81462dbf3483d04252e0ec45b
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39302293"
---
# <a name="dialogs-in-the-bot-builder-sdk-for-net"></a>Dialoge im Bot Builder SDK für .NET
> [!div class="op_single_selector"]
> - [.NET](../dotnet/bot-builder-dotnet-dialogs.md)
> - [Node.js](../nodejs/bot-builder-nodejs-dialog-overview.md)

Wenn Sie einen Bot mithilfe des Bot Builder SDK für .NET erstellen, können Sie Dialoge verwenden, um eine Unterhaltung zu modellieren und den [Konversationsfluss](../bot-service-design-conversation-flow.md) zu verwalten. Jeder Dialog ist eine Abstraktion, die ihren eigenen Status in einer C#-Klasse mit `IDialog`-Implementierung kapselt. Ein Dialog kann sich aus anderen Dialogen zusammensetzen, um die Wiederverwendung zu maximieren. In einem Dialogkontext wird der [Stapel von Dialogen](../bot-service-design-conversation-flow.md#dialog-stack), die zu einem bestimmten Zeitpunkt in der Unterhaltung aktiv sind, verwaltet. 

Eine Unterhaltung, die sich aus Dialogen zusammensetzt, ist auf andere Computer übertragbar, sodass es möglich ist, Ihre Bot-Implementierung zu skalieren. Wenn Sie Dialoge im Bot Builder SDK für .NET verwenden, wird der Unterhaltungsstatus (im Dialogstapel und der Status der einzelnen Dialoge im Stapel) automatisch im Speicher für [Statusdaten](bot-builder-dotnet-state.md) Ihrer Wahl gespeichert. So kann der Dienstcode Ihres Bots zustandslos sein, ähnlich wie bei einer Webanwendung, die nicht den Sitzungszustand im Arbeitsspeicher des Webservers speichern muss. 

## <a name="echo-bot-example"></a>Beispiel zum Echo-Bot

Sehen Sie sich das folgende Beispiel zum Echo-Bot an. Darin wird beschrieben, wie Sie den im [Schnellstarttutorial](bot-builder-dotnet-quickstart.md) erstellten Bot ändern, sodass er über Dialoge Nachrichten mit dem Benutzer austauscht.

> [!TIP]
> Um dieses Beispiel nachvollziehen zu können, verwenden Sie die Anweisungen im Tutorial [Schnellstart](bot-builder-dotnet-quickstart.md), um einen Bot zu erstellen, und aktualisieren Sie dann die Datei **MessagesController.cs**, wie nachfolgend beschrieben wird.

### <a name="messagescontrollercs"></a>MessagesController.cs 

Im Bot Builder SDK für .NET können Sie mit der [Builder][builderLibrary]-Bibliothek Dialoge implementieren. Importieren Sie den `Dialogs`-Namespace, um auf die relevanten Klassen zuzugreifen.

[!code-csharp[Using statement](../includes/code/dotnet-dialogs.cs#usingStatement)]

Fügen Sie zur Darstellung der Unterhaltung als Nächstes diese `EchoDialog`-Klasse zu **MessagesController.cs** hinzu. 

[!code-csharp[EchoDialog class](../includes/code/dotnet-dialogs.cs#echobot1)]

Verknüpfen Sie anschließend die `EchoDialog`-Klasse mit der `Post`-Methode, indem Sie die `Conversation.SendAsync`-Methode aufrufen.

[!code-csharp[Post method](../includes/code/dotnet-dialogs.cs#echobot2)]

### <a name="implementation-details"></a>Details zur Implementierung 

Die `Post`-Methode ist als `async` markiert, da Bot Builder die C#-Funktionen für die Behandlung der asynchronen Kommunikation verwendet. Sie gibt ein `Task`-Objekt zurück, das die Aufgabe darstellt, die für das Senden von Antworten an die übergebene Nachricht verantwortlich ist. Wenn eine Ausnahme auftritt, enthält die `Task`, die von der Methode zurückgegeben wird, die Ausnahmeinformationen. 

Die `Conversation.SendAsync`-Methode spielt für die Implementierung von Dialogen mit dem Bot Builder SDK für .NET eine entscheidende Rolle. Sie entspricht dem <a href="https://en.wikipedia.org/wiki/Dependency_inversion_principle" target="_blank">Dependency-Inversion-Prinzip</a> und führt folgende Schritte aus:

1. Die erforderlichen Komponenten werden instanziiert.  
2. Der Status der Unterhaltung (im Dialogstapel und der Status der einzelnen Dialoge im Stapel) wird über `IBotDataStore` deserialisiert.
3. Der Unterhaltungsprozess, bei dem der Bot angehalten wurde und auf eine Nachricht wartet, wird fortgesetzt.
4. Die Antworten werden gesendet.
5. Der aktualisierte Status der Unterhaltung wird serialisiert und wieder unter `IBotDataStore` gespeichert.

Zu Beginn der Unterhaltung enthält der Dialog keinen Status, `Conversation.SendAsync` erstellt also `EchoDialog` und ruft die `StartAsync`-Methode auf. Die `StartAsync`-Methode ruft `IDialogContext.Wait` mit dem Fortsetzungsdelegaten auf, um die Methode anzugeben, die beim Empfang einer neuen Nachricht aufgerufen werden soll (`MessageReceivedAsync`). 

Die `MessageReceivedAsync`-Methode wartet auf eine Meldung, veröffentlicht eine Antwort und wartet auf die nächste Nachricht. Jedes Mal, wenn `IDialogContext.Wait` aufgerufen wird, wechselt der Bot in einen angehaltenen Status und kann auf einem beliebigen Computer neu gestartet werden, der die Nachricht empfängt. 

Ein Bot, der mithilfe der oben aufgeführten Codebeispiele erstellt wird, antwortet auf jede Nachricht, die der Benutzer sendet, indem er einfach die Nachricht des Benutzers erneut zurückgibt und dabei den Text „You said:“ voranstellt. Da der Bot mithilfe von Dialogen erstellt wird, kann er dahingehend weiterentwickelt werden, komplexere Unterhaltungen zu unterstützen, ohne dass der Status explizit verwaltet werden muss.

## <a name="echo-bot-with-state-example"></a>Echo-Bot mit Statusbeispiel

Das nächste Beispiel baut auf dem Beispiel auf, bei dem die Möglichkeit zur Nachverfolgung des Dialogstatus hinzugefügt wird. Wenn die `EchoDialog`-Klasse wie im folgenden Codebeispiel gezeigt aktualisiert wird, antwortet der Bot auf jede Nachricht, die der Benutzer sendet, durch erneute Rückgabe der Benutzernachricht, der eine Zahl (`count`) gefolgt von „You said:“ vorangestellt wird. Der Bot erhöht mit jeder Antwort weiterhin schrittweise `count`, bis der Benutzer die Anzahl zurücksetzt.

### <a name="messagescontrollercs"></a>MessagesController.cs 

[!code-csharp[EchoDialog class](../includes/code/dotnet-dialogs.cs#echobot3)]

### <a name="implementation-details"></a>Details zur Implementierung

Wie im ersten Beispiel wird die `MessageReceivedAsync`-Methode aufgerufen, wenn eine neue Nachricht empfangen wird. Dieses Mal jedoch wertet die `MessageReceivedAsync`-Methode die Nachricht des Benutzers vor der Antwort aus. Wenn die Benutzernachricht „zurückgesetzt“ wird, zeigt die integrierte Eingabeaufforderung `PromptDialog.Confirm` einen untergeordneten Dialog an, der den Benutzer auffordert, die Zurücksetzung der Anzahl zu bestätigen. Der untergeordnete Dialog weist einen eigenen privaten Status auf, der nicht den Status des übergeordneten Dialogs beeinträchtigt. Wenn der Benutzer auf die Eingabeaufforderung antwortet, wird das Ergebnis des untergeordneten Dialogs an die `AfterResetAsync`-Methode übergeben, die eine Nachricht an den Benutzer sendet, um anzugeben, ob die Anzahl zurückgesetzt wurde, und dann `IDialogContext.Wait` mit einer Fortsetzung zurück an `MessageReceivedAsync` in der nächste Meldung aufruft.

## <a name="dialog-context"></a>Dialogkontext

Die Schnittstelle `IDialogContext`, die an jede Dialogmethode übergeben wird, bietet Zugriff auf die Dienste, die ein Dialog benötigt, um den Status zu speichern und mit dem Kanal zu kommunizieren. Die Schnittstelle `IDialogContext` besteht aus drei Schnittstellen: [Internals.IBotData][iBotData], [Internals.IBotToUser][iBotToUser] und [Internals.IDialogStack][iDialogStack]. 

### <a name="internalsibotdata"></a>Internals.IBotData

Die Schnittstelle `Internals.IBotData` bietet Zugriff auf benutzerspezifische, unterhaltungsspezifische und private Statusdaten zu Unterhaltungen, die vom Connector verwaltet werden. Benutzerspezifische Statusdaten eignen sich zum Speichern von Benutzerdaten, die nicht mit einer bestimmten Unterhaltung verknüpft sind, während unterhaltungsspezifische Daten zum Speichern von allgemeinen Daten zu einer Unterhaltung und private Unterhaltungsdaten zum Speichern von Benutzerdaten verwendet werden, die mit einer bestimmten Unterhaltung im Zusammenhang stehen. 

### <a name="internalsibottouser"></a>Internals.IBotToUser

`Internals.IBotToUser` stellt Methoden zum Senden einer Nachricht vom Bot an den Benutzer bereit. Nachrichten können inline mit der Antwort an den Web-API-Methodenaufruf oder direkt mit dem [Connectorclient](bot-builder-dotnet-connector.md#create-a-connector-client) gesendet werden. Durch das Senden und Empfangen von Nachrichten über den Dialogkontext wird sichergestellt, dass der Status `Internals.IBotData` über den Connector übergeben wird.

### <a name="internalsidialogstack"></a>Internals.IDialogStack

`Internals.IDialogStack` stellt Methoden zum Verwalten des [Dialogstapels](../bot-service-design-conversation-flow.md#dialog-stack) bereit. In den meisten Fällen wird der Dialogstapel automatisch für Sie verwaltet. Allerdings gibt es möglicherweise Fälle, in denen der Stapel explizit verwaltet werden soll. Beispielsweise sollten Sie einen untergeordneten Dialog aufrufen und am Anfang des Dialogstapels einfügen, den aktuellen Dialog als vollständig markieren (wobei dieser aus dem Dialogstapel entfernt und das Ergebnis an den vorherigen Dialog im Stapel zurückgegeben wird), den aktuellen Dialog anhalten, bis eine Nachricht vom Benutzer eingeht, oder den Dialogstapel sogar vollständig zurücksetzen.

## <a name="serialization"></a>Serialisierung

Der Dialogstapel und -status aller aktiven Dialoge werden den benutzer- und unterhaltungsspezifischen [IBotDataBag][iBotDataBag] serialisiert. Das serialisierte Blob wird in den Nachrichten gespeichert, die der Bot an den [Connector](bot-builder-dotnet-concepts.md#connector) sendet und von diesem empfängt. Damit die `Dialog`-Klasse serialisiert wird, muss sie das `[Serializable]`-Attribut enthalten. Alle `IDialog`-Implementierungen in der [Builder][builderLibrary]-Bibliothek werden als serialisierbar markiert. 

Die [Methodenketten](#dialog-chains) bieten eine Fluent-Oberfläche für Dialoge, die in der LINQ-Abfragesyntax verwendet werden können. Bei der kompilierten Form der LINQ-Abfragesyntax kommen häufig anonyme Methoden zum Einsatz. Wenn diese anonymen Methoden nicht auf die Umgebung von lokalen Variablen verweisen, dann weisen diese anonymen Methoden keinen Status auf, und sie sind unerheblicherweise serialisierbar. Wenn die anonyme Methode allerdings beliebige lokale Variablen in der Umgebung erfasst, wird das resultierende Closure-Objekt (vom Compiler generiert) nicht als serialisierbar markiert. In diesem Fall löst Bot Builder eine `ClosureCaptureException` aus, um das Problem zu identifizieren.

Um mithilfe von Reflektion Klassen zu serialisieren, die nicht als serialisierbar markiert sind, enthält die Builder-Bibliothek einen Serialisierungsersatz basierend auf Reflexion, mit dem Sie sich bei [Autofac][autofac] registrieren können.

[!code-csharp[Serialization](../includes/code/dotnet-dialogs.cs#serialization)]

## <a id="dialog-chains"></a> Dialogketten

Mit `IDialogStack.Call<R>` und `IDialogStack.Done<R>` können Sie zwar explizit den Stapel der aktiven Dialoge verwalten, doch Sie können den Stapel der aktiven Dialoge auch implizit mit diesen Fluent-[Methodenketten][chain] verwalten.


|           Methode            |  Typ   |                                 Notizen                                  |
|-----------------------------|---------|------------------------------------------------------------------------|
|     Chain.Select<T, R>      |  LINQ   |           Unterstützt „select“ und „let“ in der LINQ-Abfragesyntax.            |
|  Chain.SelectMany<T, C, R>  |  LINQ   |            Unterstützt aufeinanderfolgende Vorkommen von „from“ in der LINQ-Abfragesyntax.            |
|       Chain.Where<T>        |  LINQ   |                 Unterstützt „where“ in der LINQ-Abfragesyntax.                 |
|        Chain.From<T>        | Ketten  |                Instanziiert eine neue Instanz eines Dialogs.                |
|       Chain.Return<T>       | Ketten  |                Gibt einen konstanten Wert in der Kette zurück.                |
|         Chain.Do<T>         | Ketten  |               Berücksichtigt Nebenwirkungen innerhalb der Kette.                |
|  Chain.ContinueWith<T, R>   | Ketten  |                      Einfache Verkettung von Dialogen.                       |
|       Chain.Unwrap<T>       | Ketten  |                  Entpackt einen in einem Dialog geschachtelten Dialog.                   |
| Chain.DefaultIfException<T> | Ketten  | Fängt eine Ausnahme aus dem vorherigen Ergebnis ab und gibt „default(T)“ zurück. |
|        Chain.Loop<T>        | Verzweigung  |                   Führt Schleifen für die gesamte Kette von Dialogen aus.                   |
|        Chain.Fold<T>        | Verzweigung  |   Fasst die Ergebnisse aus einer Enumeration von Dialogen in einem einzigen Ergebnis zusammen.   |
|     Chain.Switch<T, R>      | Verzweigung  |            Unterstützt die Verzweigung in verschiedene Dialogketten.            |
|     Chain.PostToUser<T>     | Message |                      Veröffentlicht eine Meldung für den Benutzer.                      |
|     Chain.WaitToBot<T>      | Message |                    Wartet auf eine Nachricht an den Bot.                     |
|    Chain.PostToChain<T>     | Message |              Startet eine Kette mit einer Meldung vom Benutzer.              |

### <a name="examples"></a>Beispiele 

Bei der LINQ-Abfragesyntax wird die `Chain.Select<T, R>`-Methode verwendet.

[!code-csharp[Chain.Select](../includes/code/dotnet-dialogs.cs#chain1)]

Alternativ wird die Methode `Chain.SelectMany<T, C, R>` verwendet.

[!code-csharp[Chain.SelectMany](../includes/code/dotnet-dialogs.cs#chain2)]

Die Methoden `Chain.PostToUser<T>` und `Chain.WaitToBot<T>` veröffentlichen Nachrichten vom Bot an den Benutzer und umgekehrt.

[!code-csharp[Chain.PostToUser](../includes/code/dotnet-dialogs.cs#chain3)]

Die Methode `Chain.Switch<T, R>` verzweigt den Dialogdatenfluss einer Unterhaltung.

[!code-csharp[Chain.Switch](../includes/code/dotnet-dialogs.cs#chain4)]

Wenn `Chain.Switch<T, R>` ein geschachteltes `IDialog<IDialog<T>>` zurückgibt, kann das innere `IDialog<T>` mit `Chain.Unwrap<T>` entpackt werden. Dadurch können Unterhaltungen mit unterschiedlichen Pfaden verketteter Dialoge womöglich mit ungleicher Länge verzweigt werden. Dieses Beispiel zeigt ein umfassenderes Beispiel von verzweigten Dialogen, die im Stil von Fluent-Ketten mit impliziter Stapelverwaltung geschrieben sind.

[!code-csharp[Chain.Switch](../includes/code/dotnet-dialogs.cs#chain5)]

## <a name="next-steps"></a>Nächste Schritte

Dialoge verwalten den Konversationsfluss zwischen einem Bot und einem Benutzer. Ein Dialog definiert, wie die Interaktion mit einem Benutzer erfolgt. Ein Bot kann zahlreiche Dialoge aufweisen, die in Stapeln organisiert sind, um die Unterhaltung mit dem Benutzer zu steuern. Im nächsten Abschnitt erfahren Sie, wie der Dialogstapel wächst oder schrumpft, während Sie Dialoge im Stapel erstellen und verwerfen.

> [!div class="nextstepaction"]
> [Verwalten des Konversationsflusses mit Dialogen](bot-builder-dotnet-manage-conversation-flow.md)


[builderLibrary]: /dotnet/api/microsoft.bot.builder.dialogs

[iBotData]: /dotnet/api/microsoft.bot.builder.dialogs.internals.ibotdata

[iBotToUser]: /dotnet/api/microsoft.bot.builder.dialogs.internals.ibottouser

[iDialogStack]: /dotnet/api/microsoft.bot.builder.dialogs.internals.idialogstack

[iBotDataBag]: /dotnet/api/microsoft.bot.builder.dialogs.ibotdatabag

[autofac]: /dotnet/api/microsoft.bot.builder.autofac.base

[chain]: /dotnet/api/microsoft.bot.builder.dialogs.chain
