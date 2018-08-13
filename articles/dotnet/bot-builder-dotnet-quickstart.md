---
title: Erstellen eines Bots mit dem Bot Builder SDK für .NET | Microsoft-Dokumentation
description: Erstellen Sie einen Bot mit dem Bot Builder SDK für .NET, einem leistungsstarken Konstruktionsframework für Bots.
keywords: Bot Builder SDK, Erstellen eines Bots, Schnellstart, erste Schritte
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: get-started-article
ms.prod: bot-framework
ms.date: 04/27/2018
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: fcb21fa38750c09f110a3c71f763a941c4437979
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39304072"
---
# <a name="create-a-bot-with-the-bot-builder-sdk-for-net"></a>Erstellen eines Bots mit dem Bot Builder SDK für .NET
> [!div class="op_single_selector"]
> - [.NET](../dotnet/bot-builder-dotnet-quickstart.md)
> - [Node.js](../nodejs/bot-builder-nodejs-quickstart.md)
> - [Bot Service](../bot-service-quickstart.md)
> - [REST](../rest-api/bot-framework-rest-connector-quickstart.md)

Das <a href="https://github.com/Microsoft/BotBuilder" target="_blank">Bot Builder SDK für .NET</a> ist ein benutzerfreundliches Framework für das Entwickeln von Bots mit Visual Studio und Windows. Das SDK nutzt C#, um .NET-Entwicklern eine vertraute Möglichkeit zum Erstellen von leistungsstarken Bots zu bieten.


Dieses Tutorial führt Sie durch die Erstellung eines Bots, wobei Sie die Botanwendungsvorlage und das Bot Builder SDK für .NET verwenden und dann den Bot mit dem Bot Framework-Emulator testen.

## <a name="prerequisites"></a>Voraussetzungen
1. Visual Studio [2017](https://www.visualstudio.com/).

2. Aktualisieren Sie in Visual Studio alle [Erweiterungen](https://docs.microsoft.com/en-us/visualstudio/extensibility/how-to-update-a-visual-studio-extension) auf die jeweils neueste Version.

3. Botvorlage für [C#](https://marketplace.visualstudio.com/items?itemName=BotBuilder.BotBuilderV3).

> [!TIP]
> Das Visual Studio 2017-Projektvorlagenverzeichnis befindet sich in der Regel unter `%USERPROFILE%\Documents\Visual Studio 2017\Templates\ProjectTemplates\Visual C#\`, und das Verzeichnis für Elementvorlagen befindet sich unter `%USERPROFILE%\Documents\Visual Studio 2017\Templates\ItemTemplates\Visual C#\`.

## <a name="create-your-bot"></a>Erstellen Ihres Bots

Öffnen Sie Visual Studio, und erstellen Sie ein neues C#-Projekt. Wählen Sie die Vorlage **Simple Echo Bot Application** für Ihr neues Projekt aus.

![Projekt in Visual Studio erstellen](../media/connector-getstarted-create-project.png)

> [!NOTE]
> Möglicherweise werden Sie in Visual Studio zum Herunterladen und Installieren von [IIS Express](https://www.microsoft.com/en-us/download/details.aspx?id=48264) aufgefordert. 

Dank der Botanwendungsvorlage enthält Ihr Projekt sämtlichen Code, der zum Erstellen des Bots in diesem Tutorial erforderlich ist. Sie müssen tatsächlich keinen zusätzlichen Code schreiben. Bevor wir jedoch mit dem Testen Ihres Bots fortfahren, werfen wir noch einen kurzen Blick auf einen Teil des von der Botanwendungsvorlage bereitgestellten Codes.

> [!TIP] 
> Aktualisieren Sie ggf. die [NuGet-Pakete](https://docs.microsoft.com/en-us/nuget/quickstart/install-and-use-a-package-in-visual-studio).

## <a name="explore-the-code"></a>Untersuchen des Codes

Zuerst empfängt die `Post`-Methode in **Controllers\MessagesController.cs** die Nachricht des Benutzers und ruft den Stammdialog auf.

```csharp
public async Task<HttpResponseMessage> Post([FromBody]Activity activity)
{
    if (activity.GetActivityType() == ActivityTypes.Message)
    {
        await Conversation.SendAsync(activity, () => new Dialogs.RootDialog());
    }
    else
    {
        HandleSystemMessage(activity);
    }
    var response = Request.CreateResponse(HttpStatusCode.OK);
    return response;
}

```

Der Stammdialog verarbeitet die Nachricht und generiert eine Antwort. Die `MessageReceivedAsync`-Methode in **Dialogs\RootDialog.cs** sendet eine Antwort, die die Nachricht des Benutzers mit dem vorangestellten Text „You sent“ wiederholt und mit dem Text „which was *##* characters“ endet, wobei *##* die Anzahl von Zeichen in der Nachricht des Benutzers darstellt.

```csharp
public class RootDialog : IDialog<object>
{
    public Task StartAsync(IDialogContext context)
    {
        context.Wait(MessageReceivedAsync);

        return Task.CompletedTask;
    }

    private async Task MessageReceivedAsync(IDialogContext context, IAwaitable<object> result)
    {
        var activity = await result as Activity;

        // Calculate something for us to return
        int length = (activity.Text ?? string.Empty).Length;

        // Return our reply to the user
        await context.PostAsync($"You sent {activity.Text} which was {length} characters");

        context.Wait(MessageReceivedAsync);
    }
}
```

## <a name="test-your-bot"></a>Testen Ihres Bots

[!INCLUDE [Get started test your bot](../includes/snippet-getstarted-test-bot.md)]

### <a name="start-your-bot"></a>Starten Ihres Bots

Starten Sie nach der Installation des Emulators Ihren Bot in Visual Studio, und verwenden Sie dabei einen Browser als Anwendungshost.
Aus dem folgenden Visual Studio-Screenshot geht hervor, dass der Bot in Microsoft Edge gestartet wird, wenn der Benutzer auf die Schaltfläche „Ausführen“ klickt.

![Projekt in Visual Studio ausführen](../media/connector-getstarted-start-bot-locally.png)

Wenn Sie auf die Schaltfläche „Ausführen“ klicken, geschieht Folgendes: Visual Studio erstellt die Anwendung, stellt sie für „localhost“ bereit und startet den Webbrowser zum Anzeigen der Seite **default.htm** der Anwendung.
Nachfolgend wird beispielsweise die Seite **default.htm** der Anwendung in Microsoft Edge angezeigt:

![Visual Studio-Bot auf „localhost“](../media/connector-getstarted-bot-running-localhost.png)

> [!NOTE]
> Sie können die Datei **default.htm** in Ihrem Projekt ändern, um den Namen und die Beschreibung Ihrer Botanwendung anzugeben.

### <a name="start-the-emulator-and-connect-your-bot"></a>Starten des Emulators und Herstellen einer Verbindung mit Ihrem Bot

Zu diesem Zeitpunkt wird Ihr Bot lokal ausgeführt.
Starten Sie als Nächstes den Emulator, und verbinden Sie dann Ihren Bot im Emulator:

1. Erstellen Sie eine neue Botkonfiguration. Geben Sie in die Adressleiste die Zeichenfolge `http://localhost:port-number/api/messages` ein, wobei *port-number* der Portnummer entspricht, die in dem Browser angezeigt wird, in dem Ihre Anwendung ausgeführt wird.

2. Klicken Sie auf **Speichern und verbinden**. Die **Microsoft-App-ID** und das **Microsoft-App-Kennwort** müssen nicht angegeben werden. Sie können diese Felder vorerst leer lassen. Sie erhalten diese Informationen später, wenn Sie [Ihren Bot registrieren](~/bot-service-quickstart-registration.md).

### <a name="test-your-bot"></a>Testen Ihres Bots

Da Ihr Bot nun lokal ausgeführt wird und mit dem Emulator verbunden ist, können Sie Ihren Bot testen, indem Sie einige Nachrichten im Emulator eingeben.
Sie sollten sehen, dass der Bot auf jede von Ihnen gesendete Nachricht reagiert, indem Ihre Nachricht mit dem vorangestellten Text „You sent“ wiederholt wird und mit dem Text „which was *##* characters“ endet, wobei *##* die Gesamtzahl von Zeichen in der von Ihnen gesendet Nachricht darstellt.


> [!TIP]
> Klicken Sie im Emulator auf eine beliebige Sprechblase in Ihrer Konversation. Im Detailbereich werden Details zur Nachricht im JSON-Format angezeigt.

Sie haben mithilfe der Botanwendungsvorlage und des Bot Builder SDK für .NET erfolgreich einen Bot erstellt!

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie mithilfe der Botanwendungsvorlage und des Bot Builder SDK für .NET einen einfachen Bot erstellt und die Funktionalität des Bots mit dem Bot Framework-Emulator überprüft.

Lernen Sie als Nächstes die wichtigen Konzepte des Bot Builder SDK für .NET kennen.

> [!div class="nextstepaction"]
> [Wichtige Konzepte im Bot Builder SDK für .NET](bot-builder-dotnet-concepts.md)
