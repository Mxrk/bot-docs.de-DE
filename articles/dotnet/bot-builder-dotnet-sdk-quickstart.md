---
title: Erstellen eines Bots mit dem Bot Builder SDK für .NET | Microsoft-Dokumentation
description: Erstellen Sie einen Bot mit dem Bot Builder SDK für .NET, einem leistungsstarken Konstruktionsframework für Bots.
keywords: Bot Builder SDK, Erstellen eines Bots, Schnellstart, .NET, erste Schritte
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: get-started-article
ms.prod: bot-framework
ms.date: 04/27/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 98c6103f0cd38bf6d3f477735dafcf01952304d5
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39304149"
---
# <a name="create-a-bot-with-the-bot-builder-sdk-v4-for-net"></a>Erstellen eines Bots mit dem Bot Builder SDK v4 für .NET
[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

Dieser Schnellstart führt Sie durch die Erstellung eines Bots, wobei Sie die Botanwendungsvorlage und das Bot Builder SDK für .NET verwenden und dann den Bot mit dem Bot Framework-Emulator testen. Dies basiert auf dem [Microsoft Bot Builder SDK v4](https://github.com/Microsoft/botbuilder-dotnet).

## <a name="prerequisites"></a>Voraussetzungen
- Visual Studio [2017](https://www.visualstudio.com/downloads)
- Bot Builder SDK v4-Vorlage für [C#](https://marketplace.visualstudio.com/items?itemName=BotBuilder.botbuilderv4)
- [Bot Framework-Emulator](https://github.com/Microsoft/BotFramework-Emulator/releases)
- Kenntnisse von [ASP.Net Core](https://docs.microsoft.com/aspnet/core/) und asynchroner Programmierung in [C#](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/concepts/async/index)

## <a name="create-a-bot"></a>Erstellen eines Bots
Installieren Sie die Vorlage „BotBuilderVSIX.vsix“, die Sie im Abschnitt „Voraussetzungen“ heruntergeladen haben. 

Erstellen Sie in Visual Studio ein neues Botprojekt.

![Visual Studio-Projekt](../media/azure-bot-quickstarts/bot-builder-dotnet-project.png)

> [!TIP] 
> Aktualisieren Sie ggf. die [NuGet-Pakete](https://docs.microsoft.com/en-us/nuget/quickstart/install-and-use-a-package-in-visual-studio).

## <a name="explore-code"></a>Untersuchen von Code
Öffnen Sie die Datei „Startup.cs“, um den Code in der Methode `ConfigureServices(IServiceCollection services)` zu überprüfen. Die Middleware `CatchExceptionMiddleware` wird der Messagingpipeline hinzugefügt. Sie verarbeitet alle Ausnahmen, die von anderer Middleware oder der OnTurn-Methode ausgelöst werden. 

```cs
options.Middleware.Add(new CatchExceptionMiddleware<Exception>(async (context, exception) =>
{
    await context.TraceActivity("EchoBot Exception", exception);
    await context.SendActivity("Sorry, it looks like something went wrong!");
}));
```

Die Unterhaltungsstatusmiddleware verwendet In-Memory-Speicher. Sie liest und schreibt den EchoState in den Speicher.  Der Rundenzähler in der EchoState-Klasse verfolgt die Anzahl der an den Bot gesendeten Nachrichten. Sie können ein ähnliches Verfahren verwenden, um den Zustand zwischen den Runden beizubehalten.

```cs
 IStorage dataStore = new MemoryStorage();
 options.Middleware.Add(new ConversationState<EchoState>(dataStore));
```

Die Methode `Configure(IApplicationBuilder app, IHostingEnvironment env)` ruft `.UseBotFramework` auf, um eingehende Aktivitäten an den Botadapter weiterzuleiten. 

Die Methode `OnTurn(ITurnContext context)` in der Datei „EchoBot.cs“ wird verwendet, um den Typ der eingehenden Aktivität zu überprüfen und eine Antwort an den Benutzer zu senden. 

```cs
public async Task OnTurn(ITurnContext context)
{
    // This bot is only handling Messages
    if (context.Activity.Type == ActivityTypes.Message)
    {
        // Get the conversation state from the turn context
        var state = context.GetConversationState<EchoState>();

        // Bump the turn count. 
        state.TurnCount++;

        // Echo back to the user whatever they typed.
        await context.SendActivity($"Turn {state.TurnCount}: You sent '{context.Activity.Text}'");
    }
}
```
## <a name="start-your-bot"></a>Starten Ihres Bots

- Ihre `default.html`-Seite wird in einem Browser angezeigt.
- Notieren Sie sich die Localhost-Portnummer der Seite. Sie benötigen diese Information für die Interaktion mit Ihrem Bot.

### <a name="start-the-emulator-and-connect-your-bot"></a>Starten des Emulators und Herstellen einer Verbindung mit Ihrem Bot

Zu diesem Zeitpunkt wird Ihr Bot lokal ausgeführt.
Starten Sie als Nächstes den Emulator, und verbinden Sie dann Ihren Bot im Emulator:

1. Klicken Sie im Emulator auf der Registerkarte „Willkommen“ auf den Link **Neue Botkonfiguration erstellen**. 

2. Geben Sie einen **Botnamen** ein, und geben Sie dann den Verzeichnispfad zu Ihrem Botcode ein. Die Botkonfigurationsdatei wird unter diesem Pfad gespeichert.

3. Geben Sie im Feld **Endpunkt-URL** die Zeichenfolge `http://localhost:port-number/api/messages` ein, wobei *Portnummer* der Portnummer entspricht, die in dem Browser angezeigt wird, in dem Ihre Anwendung ausgeführt wird.

4. Klicken Sie auf **Verbinden**, um eine Verbindung mit Ihrem Bot herzustellen. Die **Microsoft-App-ID** und das **Microsoft-App-Kennwort** müssen nicht angegeben werden. Sie können diese Felder vorerst leer lassen. Sie erhalten diese Informationen später, wenn Sie Ihren Bot registrieren.

## <a name="interact-with-your-bot"></a>Interagieren mit Ihrem Bot

Senden Sie „Hi“ an Ihren Bot. Der Bot antwortet dann mit „Runde 1: Sie haben Hi gesendet“ auf die Nachricht.

## <a name="next-steps"></a>Nächste Schritte

Als Nächstes können Sie [Ihren Bot in Azure bereitstellen](../bot-builder-howto-deploy-azure.md) oder zu den Konzepten wechseln, die einen Bot und dessen Funktionsweise erläutern.

> [!div class="nextstepaction"]
> [Grundlegende Botkonzepte](../v4sdk/bot-builder-basics.md)
