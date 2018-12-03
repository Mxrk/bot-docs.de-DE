---
title: Erstellen eines Direct Line-Bots und -Clients | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie einen Direct Line-Bot und -Client mit Version 4 des Bot Builder SDK für .NET erstellen.
keywords: Direct Line-Bot, Direct Line-Client, benutzerdefinierter Kanal, konsolenbasiert, veröffentlichen
author: v-royhar
ms.author: v-royhar
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 4/16/18
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: c13733af0f6b26654952a0aab190f6d8cb06d059
ms.sourcegitcommit: b78fe3d8dd604c4f7233740658a229e85b8535dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2018
ms.locfileid: "49999977"
---
# <a name="create-a-direct-line-bot-and-client"></a>Erstellen eines Direct Line-Bots und -Clients

[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

Microsoft Bot Framework-Direct Line-Bots sind Bots, die mit einem benutzerdefinierten Client nach Ihrem eigenen Entwurf funktionieren. Direct Line-Bots sind normalen Bots überraschend ähnlich, nur dass sie nicht die bereitgestellten Kanäle verwenden müssen.

Direct Line-Clients können ganz nach Ihren Vorstellungen geschrieben werden. Sie können einen Android-Client, einen iOS-Client oder sogar eine konsolenbasierte Clientanwendung schreiben.

In diesem Thema erfahren Sie, wie Sie einen Direct Line-Bot erstellen und bereitstellen und wie Sie eine konsolenbasierte Direct Line-Client-App erstellen und ausführen.

## <a name="create-your-bot"></a>Erstellen Ihres Bots

Jede Sprache folgt zum Erstellen des Bots einem anderen Pfad. In JavaScript wird der Bot in Azure erstellt und der Code dann geändert, während in C# der Bot lokal erstellt und dann in Azure veröffentlicht wird. Beides sind jedoch gültige Methoden, die mit beiden Sprachen verwendet werden können. Wenn Sie Details zum Veröffentlichen Ihres Bots in Azure erhalten möchten, lesen Sie [Bereitstellen Ihres Bot in Azure](../bot-builder-howto-deploy-azure.md).

# <a name="ctabcscreatebot"></a>[C#](#tab/cscreatebot)

### <a name="create-the-solution-in-visual-studio"></a>Erstellen der Projektmappe in Visual Studio

So erstellen Sie die Projektmappe für den Direct Line-Bot in Visual Studio 2015 oder höher

1. Erstellen Sie eine neue **ASP.NET Core-Webanwendung** in **Visual C#** > **.NET Core**.

1. Geben Sie den Namen **DirectLineBotSample** ein, und klicken Sie auf **OK**.

1. Stellen Sie sicher, dass **.NET Core** und **ASP.NET Core 2.0** ausgewählt sind, wählen Sie die Projektvorlage **Leer** aus, und klicken Sie dann auf **OK**.

#### <a name="add-dependencies"></a>Hinzufügen von Abhängigkeiten

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf **Abhängigkeiten**, und wählen Sie dann **NuGet-Pakete verwalten** aus.

1. Klicken Sie auf **Durchsuchen**, und stellen Sie sicher, dass das Kontrollkästchen **Vorabversion einbeziehen** aktiviert ist.

1. Suchen und installieren Sie die folgenden NuGet-Pakete:
    - Microsoft.Bot.Builder
    - Microsoft.Bot.Builder.Core.Extensions
    - Microsoft.Bot.Builder.Integration.AspNet.Core
    - Microsoft.Rest.ClientRuntime
    - Newtonsoft.Json

### <a name="create-the-appsettingsjson-file"></a>Erstellen der Datei „appsettings.json“

Die Datei „appsettings.json“ enthält die Microsoft-App-ID, das App-Kennwort und die Datenverbindungszeichenfolge. Da dieser Bot keine Zustandsinformationen speichert, bleibt die Datenverbindungszeichenfolge leer. Wenn Sie nur den Bot Framework-Emulator verwenden, können sogar alle Angaben leer bleiben.

So erstellen Sie die Datei **appsettings.json**

1. Klicken Sie mit der rechten Maustaste auf das Projekt **DirectLineBotSample**, und wählen Sie **Hinzufügen** > **Neues Element** aus.

1. Klicken Sie in **ASP.NET Core** auf **ASP.NET-Konfigurationsdatei**.

1. Geben Sie als Namen **appsettings.json** ein, und klicken Sie auf **Hinzufügen**.

Ersetzen Sie den Inhalt der Datei „appsettings.json“ durch den folgenden:

```json
{
    "Logging": {
        "IncludeScopes": false,
        "Debug": {
            "LogLevel": {
                "Default": "Warning"
            }
        },
        "Console": {
            "LogLevel": {
                "Default": "Warning"
            }
        }
    },

    "MicrosoftAppId": "",
    "MicrosoftAppPassword": "",
    "DataConnectionString": ""
}
```

### <a name="edit-startupcs"></a>Bearbeiten von „Startup.cs“

Ersetzen Sie den Inhalt der Datei „Startup.cs“ durch den folgenden:

**Hinweis:** **DirectBot** wird im nächsten Schritt definiert, daher können Sie die rote Unterstreichung ignorieren.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Hosting;
using Microsoft.AspNetCore.Http;
using Microsoft.Bot.Builder.BotFramework;
using Microsoft.Bot.Builder.Integration.AspNet.Core;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;

namespace DirectLineBotSample
{
    public class Startup
    {
        public IConfiguration Configuration { get; }

        public Startup(IHostingEnvironment env)
        {
            var builder = new ConfigurationBuilder()
                .SetBasePath(env.ContentRootPath)
                .AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
                .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true)
                .AddEnvironmentVariables();
            Configuration = builder.Build();
        }

        // This method gets called by the runtime. Use this method to add services to the container.
        public void ConfigureServices(IServiceCollection services)
        {
            services.AddSingleton(_ => Configuration);
            services.AddBot<DirectBot>(options =>
            {
                options.CredentialProvider = new ConfigurationCredentialProvider(Configuration);
            });
        }

        // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
        public void Configure(IApplicationBuilder app, IHostingEnvironment env)
        {
            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }

            app.UseDefaultFiles();
            app.UseStaticFiles();
            app.UseBotFramework();
        }
    }
}
```

### <a name="create-the-directbot-class"></a>Erstellen der DirectBot-Klasse

Die DirectBot-Klasse enthält den größten Teil der Logik für diesen Bot.

1. Klicken Sie mit der rechten Maustaste auf **DirectLineBotSample** > **Hinzufügen** > **Neues Element**.

1. Klicken Sie in **ASP.NET Core** auf **Klasse**.

1. Geben Sie für den Namen **DirectBot.cs** ein, und klicken Sie auf **Hinzufügen**.

Ersetzen Sie den Inhalt der Datei „DirectBot.cs“ durch den folgenden:

```csharp
using System.Threading.Tasks;
using Microsoft.Bot;
using Microsoft.Bot.Builder;
using Microsoft.Bot.Builder.Core.Extensions;
using Microsoft.Bot.Schema;

namespace DirectLineBotSample
{
    public class DirectBot : IBot
    {
        public Task OnTurn(ITurnContext context)
        {
            // Respond to the various activity types.
            switch (context.Activity.Type)
            {
                case ActivityTypes.Message:
                    // Respond to the incoming text message.
                    RespondToMessage(context);
                    break;

                case ActivityTypes.ConversationUpdate:
                    break;

                case ActivityTypes.ContactRelationUpdate:
                    break;

                case ActivityTypes.Typing:
                    break;

                case ActivityTypes.DeleteUserData:
                    break;
            }

            return Task.CompletedTask;
        }

        /// <summary>
        /// Responds to the incoming message by either sending a hero card, an image, 
        /// or echoing the user's message.
        /// </summary>
        /// <param name="context">The context of this conversation.</param>
        private void RespondToMessage(ITurnContext context)
        {
            switch (context.Activity.Text.Trim().ToLower())
            {
                case "hi":
                case "hello":
                case "help":
                    // Send the user an instruction message.
                    context.SendActivity("Welcome to the Bot to showcase the DirectLine API. " +
                    "Send \"Show me a hero card\" or \"Send me a BotFramework image\" to see how the " +
                    "DirectLine client supports custom channel data. Any other message will be echoed.");
                    break;

                case "show me a hero card":
                    // Create the hero card.
                    HeroCard heroCard = new HeroCard()
                    {
                        Title = "Sample Hero Card",
                        Text = "Displayed in the DirectLine client"
                    };

                    // Attach the hero card to a new activity.
                    context.SendActivity(MessageFactory.Attachment(heroCard.ToAttachment()));
                    break;

                case "send me a botframework image":
                    // Create the image attachment.
                    Attachment imageAttachment = new Attachment()
                    {
                        ContentType = "image/png",
                        ContentUrl = "https://docs.microsoft.com/en-us/bot-framework/media/how-it-works/architecture-resize.png",
                    };

                    // Attach the image attachment to a new activity.
                    context.SendActivity(MessageFactory.Attachment(imageAttachment));
                    break;

                default:
                    // No command was encountered. Echo the user's message.
                    context.SendActivity($"You said \"{context.Activity.Text}\"");
                    break;
            }
        }
    }
}
```

**Hinweis:** Die vorhandene Datei „Program.cs“ muss nicht geändert werden.

Um zu überprüfen, ob alles ordnungsgemäß eingerichtet wurde, drücken Sie **F6**, um das Projekt zu erstellen. Es sollten keine Warnungen oder Fehler auftreten.

### <a name="publish-the-bot-from-visual-studio"></a>Veröffentlichen des Bots aus Visual Studio

1. Klicken Sie in Visual Studio im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt **DirectLineBotSample**und dann auf **Als Startprojekt festlegen**.

    ![Seite „Veröffentlichen“ in Visual Studio](media/bot-builder-howto-direct-line/visual-studio-publish-page.png)

1. Klicken Sie erneut mit der rechten Maustaste auf **DirectLineBotSample**, und klicken Sie auf **Veröffentlichen**. Die Seite **Veröffentlichen** von Visual Studio wird geöffnet.

1. Wenn Sie bereits über ein Veröffentlichungsprofil für diesen Bot verfügen, wählen Sie es aus, und klicken Sie auf Schaltfläche **Veröffentlichen**. Fahren Sie dann mit dem nächsten Abschnitt fort.

1. Um ein neues Veröffentlichungsprofil zu erstellen, wählen Sie das Symbol **Microsoft Azure App Service** aus.

1. Wählen Sie **Neu erstellen**.

1. Klicken Sie auf die Schaltfläche **Veröffentlichen** . Das Dialogfeld **App Service erstellen** wird angezeigt.

    ![Dialogfeld „App Service erstellen“](media/bot-builder-howto-direct-line/create-app-service-dialog.png)

    - Geben Sie für den App-Namen einen Namen ein, den Sie später wiederfinden können. Beispielsweise können Sie Ihren E-Mail-Namen am Anfang des App-Namens hinzufügen.

    - Vergewissern Sie sich, dass Sie das richtige Abonnement verwenden.

    - Vergewissern Sie sich außerdem, dass Sie die richtige Ressourcengruppe verwenden. Die Ressourcengruppe ist auf dem Blatt **Übersicht** des Bots zu finden. **Hinweis:** Eine falsche Ressourcengruppe ist schwer zu korrigieren.

    - Vergewissern Sie sich, dass Sie den richtigen App Service-Plan verwenden.
    
1. Klicken Sie auf die Schaltfläche **Erstellen** . Visual Studio beginnt mit der Bereitstellung Ihres Bots.

Nachdem Ihr Bot veröffentlicht wurde, wird ein Browser mit dem URL-Endpunkt Ihres Bots geöffnet.

### <a name="create-bot-channels-registration-bot-on-microsoft-azure"></a>Erstellen eines Bots zum Registrieren von Botkanälen in Microsoft Azure

Der Direct Line-Bot kann auf einer beliebigen Plattform gehostet werden. In diesem Beispiel wird der Bot in Microsoft Azure gehostet. 

So erstellen Sie den Bot in Microsoft Azure

1. Klicken Sie im Microsoft Azure-Portal auf **Ressource erstellen**, und suchen Sie nach „Bot Channels Registration“ (Botkanalregistrierung).

1. Klicken Sie auf **Create**. Das Blatt „Bot Channels Registration“ (Botkanalregistrierung) wird angezeigt.

    ![Das Blatt „Bot Channels Registration“ (Botkanalregistrierung) mit Feldern für den Botnamen, das Abonnement, die Ressourcengruppe, den Speicherort, den Tarif, den Messagingendpunkt und anderen Feldern.](media/bot-builder-howto-direct-line/bot-service-registration-blade.png)

1. Geben Sie auf dem Blatt „Bot Channels Registration“ (Botkanalregistrierung) Werte für **Botname**, **Abonnement**, **Ressourcengruppe**, **Standort** und **Tarif** ein.

1. Lassen Sie das Feld **Messaging endpoint** (Messagingendpunkt) leer. Dieser Wert wird später ausgefüllt.

1. Klicken Sie auf **Microsoft App ID and password** (Microsoft-App-ID und Kennwort), und klicken Sie dann auf **Auto create App ID and password** (App-ID und Kennwort automatisch erstellen).

1. Aktivieren Sie das Kontrollkästchen **An Dashboard anheften**.

1. Klicken Sie auf die Schaltfläche **Erstellen** .

Die Bereitstellung dauert einige Minuten, dann sollte sie jedoch in Ihrem Dashboard angezeigt werden.

### <a name="update-the-appsettingsjson-file"></a>Aktualisieren der Datei „appsettings.json“

1. Klicken Sie auf dem Dashboard auf den Bot, oder wechseln Sie zu der neuen Ressource, indem Sie auf **Alle Ressourcen** klicken und nach dem Namen Ihrer Botkanalregistrierung suchen.

1. Klicken Sie auf **Übersicht**.

1. Kopieren Sie den Namen der Ressourcengruppe in die Zeichenfolge **botId** von **Program.cs** im Projekt **DirectLineClientSample**.

1. Klicken Sie auf **Einstellungen** und dann auf **Verwalten** neben dem Feld **Microsoft-App-ID**.

1. Kopieren Sie die Anwendungs-ID, und fügen Sie sie in das Feld **MicrosoftAppId** der Datei **appsettings.json** ein.

1. Klicken Sie auf die Schaltfläche **Neues Kennwort generieren**.

1. Kopieren Sie das neue Kennwort, und fügen Sie es in das Feld **MicrosoftAppPassword** der Datei **appsettings.json** ein.

### <a name="set-the-messaging-endpoint"></a>Festlegen des Messagingendpunkts

So fügen Sie Ihrem Bot den Endpunkt hinzu

1. Kopieren Sie die URL-Adresse aus dem Browser, der nach dem Veröffentlichen Ihres Bots eingeblendet wurde.

    ![Das Blatt „Einstellungen“ der Botkanalregistrierung mit hervorgehobenem Messagingendpunkt](media/bot-builder-howto-direct-line/bot-channels-registration-settings.png)

1. Rufen Sie das Blatt **Einstellungen** Ihres Bots auf.

1. Fügen Sie die Adresse unter **Messaging endpoint** (Messagingendpunkt) ein.

1. Bearbeiten Sie die Adresse, sodass sie mit „https://“ beginnt und mit „/api/messages“ endet. Wenn beispielsweise die aus dem Browser kopierte Adresse http://v-royhar-dlbot-directlinebotsample20180329044602.azurewebsites.net lautet, bearbeiten Sie sie so, dass sie https://v-royhar-dlbot-directlinebotsample20180329044602.azurewebsites.net/api/messages lautet.

1. Klicken Sie auf dem Blatt „Einstellungen“ auf die Schaltfläche **Speichern**.

**HINWEIS:** Wenn bei der Veröffentlichung ein Fehler auftritt, müssen Sie Ihren Bot möglicherweise in Azure beenden, bevor Sie Änderungen am Bot veröffentlichen können.

1. Suchen Sie Ihren App Service-Namen (er befindet sich zwischen „https://“ und „.azurewebsites.net“.

1. Suchen Sie im Azure-Portal unter „Alle Ressourcen“ nach dieser Ressource.

1. Klicken Sie auf die App Service-Ressource. Das Blatt „App Service“ wird angezeigt.

1. Klicken Sie auf dem Blatt „App Service“ auf die Schaltfläche **Beenden**.

1. Veröffentlichen Sie Ihren Bot in Visual Studio erneut.

1. Klicken Sie auf dem Blatt „App Service“ auf die Schaltfläche **Starten**.

### <a name="test-your-bot-in-webchat"></a>Testen Ihres Bots im Webchat

Um sicherzustellen, dass der Bot funktioniert, überprüfen Sie ihn im Webchat:

1. Klicken Sie auf dem Blatt „Bot Channels Registration“ (Botkanalregistrierung) für Ihren Bot auf **Einstellungen** und dann auf **Test in Web Chat** (Im Webchat testen).

1. Geben Sie „Hi“ ein. Der Bot sollte mit einer Begrüßungsnachricht antworten.

1. Geben Sie „show me a hero card“ ein. Der Bot sollte eine Herokarte anzeigen.

1. Geben Sie „send me a botframework image“ ein. Der Bot sollte ein Bild aus der Bot Framework-Dokumentation anzeigen.

1. Wenn Sie einen beliebigen anderen Text eingeben, sollte der Bot mit „You said“, gefolgt von Ihrer Nachricht in Anführungszeichen antworten.

### <a name="troubleshooting"></a>Problembehandlung

Wenn Ihr Bot nicht funktioniert, überprüfen Sie Ihre Microsoft-App-ID und Ihr Kennwort, oder geben Sie diese Angaben erneut ein. Auch wenn Sie sie zuvor kopiert haben, überprüfen Sie die Microsoft-App-ID auf dem Blatt „Einstellungen“ Ihrer Botkanalregistrierung anhand des Werts im Feld **MicrosoftAppId** der Datei „appsettings.json“.

Überprüfen Ihres Kennworts oder Erstellen und Verwenden eines neuen Kennworts: 

1. Klicken Sie auf dem Blatt „Bot Channels Registration“ (Botkanalregistrierung) neben dem Feld **Microsoft-App-ID** auf **Verwalten**.

1. Melden Sie sich beim App-Registrierungsportal an.

1. Vergewissern Sie sich, dass die ersten drei Buchstaben Ihres Kennworts mit dem Feld **MicrosoftAppPassword** in der Datei **appsettings.json** übereinstimmen. 

1. Wenn die Werte nicht übereinstimmen, generieren Sie ein neues Kennwort, und speichern Sie diesen Wert im Feld **MicrosoftAppPassword** in der Datei **appsettings.json**.

# <a name="javascripttabjscreatebot"></a>[JavaScript](#tab/jscreatebot)

### <a name="create-web-app-bot-on-microsoft-azure"></a>Erstellen eines Web-App-Bots in Microsoft Azure

So erstellen Sie den Bot in Microsoft Azure 

1. Klicken Sie im Microsoft Azure-Portal auf **Ressource erstellen**, und suchen Sie nach „Web-App-Bot“.

1. Klicken Sie auf **Create**. Das Blatt „Web-App-Bot“ wird angezeigt.

![Web-App-Bot-Registrierung](media/bot-builder-howto-direct-line/web-app-bot-registration.png)

1. Geben Sie auf dem Blatt „Web-App-Bot“ Werte für **Botname**, **Abonnement**, **Ressourcengruppe**, **Standort**, **Tarif** und **App-Name** ein.

1. Wählen Sie die Botvorlage aus, die Sie verwenden möchten. **SDK-Version: SDK v4** und **SDK-Sprache: Node.js** 

1. Wählen Sie die Vorlage für grundlegende v4-Vorschau aus. 

1. Wählen Sie Werte für **App Service-Plan/Standort**, **Azure Storage** und **Application Insights-Standort** aus.

1. Aktivieren Sie das Kontrollkästchen „An Dashboard anheften“.

1. Klicken Sie auf die Schaltfläche „Erstellen“.

1. Warten Sie, bis der Bot bereitgestellt wurde. Da Sie das Kontrollkästchen „An Dashboard anheften“ aktiviert haben, wird der Bot auf dem Dashboard angezeigt.

### <a name="edit-your-direct-line-bot"></a>Bearbeiten des Direct Line-Bots

Nun fügen Sie dem Echobot einige weitere Features hinzu.

1. Klicken Sie auf dem Blatt „Web-App-Bot“ auf „Erstellen“. 

1. Klicken Sie auf „ZIP-Datei herunterladen“. 

1. Extrahieren Sie die ZIP-Datei in ein lokales Verzeichnis.

1. Navigieren Sie zum extrahierten Ordner, und öffnen Sie die Quelldateien in Ihrer bevorzugten IDE.

1. Kopieren Sie den unten stehenden Code, und fügen Sie ihn in die Datei „app.js“ ein.

```javascript
// Copyright (c) Microsoft Corporation. All rights reserved.
// Licensed under the MIT License.

const { BotFrameworkAdapter, MessageFactory, CardFactory } = require('botbuilder');
const restify = require('restify');

// Create server
let server = restify.createServer();
server.listen(process.env.port || process.env.PORT || 3978, function () {
    console.log(`${server.name} listening to ${server.url}`);
});

// Create adapter
const adapter = new BotFrameworkAdapter({
    appId: process.env.MicrosoftAppId,
    appPassword: process.env.MicrosoftAppPassword
});

// Responds to the incoming message by either sending a hero card, an image, 
// or echoing the user's message.

// Listen for incoming requests 
server.post('/api/messages', (req, res) => {
    // Route received request to adapter for processing
    adapter.processActivity(req, res, (context) => {
        if (context.activity.type === 'message') {
            const text = (context.activity.text || '').trim().toLowerCase()

            switch(text){
                case 'hi':
                case "hello":
                case "help":
                    // Send the user an instruction message.
                    return context.sendActivity("Welcome to the Bot to showcase the DirectLine API. " +
                    "Send \"Show me a hero card\" or \"Send me a BotFramework image\" to see how the " +
                    "DirectLine client supports custom channel data. Any other message will be echoed.");
                    break;

                case 'show me a hero card':
                    // Create the hero card.
                    const message = MessageFactory.attachment(
                        CardFactory.heroCard(   
                        'Sample Hero Card', //cards title
                        'Displayed in the DirectLine client' //cards text
                        )
                    );
                    return context.sendActivity(message);
                    break;
                    
                case 'send me a botframework image':
                    // Create the image attachment.
                    const imageOrVideoMessage = MessageFactory.contentUrl('https://docs.microsoft.com/en-us/azure/bot-service/media/how-it-works/architecture-resize.png', 'image/png')
                    return context.sendActivity(imageOrVideoMessage);
                    break;
                
                default:
                    // No command was encountered. Echo the user's message.
                    return context.sendActivity(`You said ${context.activity.text}`);
                    break;
                    
            }
        }
    });
});

```

Wenn Sie bereit sind, können Sie die Quellen zurück in Azure veröffentlichen. Führen Sie diese Schritte aus, um zu erfahren, wie Sie den Bot zurück in [Azure](../bot-service-build-download-source-code.md) veröffentlichen.

---


## <a name="create-the-console-client-app"></a>Erstellen der Konsolenclientanwendung

# <a name="ctabcsclientapp"></a>[C#](#tab/csclientapp)

Die Konsolenclientanwendung wird in zwei Threads ausgeführt. Der primäre Thread akzeptiert Benutzereingaben und sendet Nachrichten an den Bot. Der sekundäre Thread fragt den Bot einmal pro Sekunde ab, um Nachrichten von ihm abzurufen, und zeigt die empfangenen Nachrichten dann an.

So erstellen Sie ein Konsolenprojekt

1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf die Projektmappe **DirectLineBotSample**.

1. Wählen Sie **Hinzufügen** > **Neues Projekt** aus.

1. Wählen Sie in **Visual C#** > **Klassischer Windows-Desktop* die Option **Konsolen-App (.NET Framework)** aus.
 
    **Hinweis:** Wählen Sie nicht **Konsolen-App (.NET Core)** aus.

1. Geben Sie als Namen **DirectLineClientSample** ein.

1. Klicken Sie auf **OK**.

### <a name="add-the-nuget-packages-to-the-console-app"></a>Fügen Sie der Konsolenanwendung die NuGet-Pakete hinzu.

1. Klicken Sie mit der rechten Maustaste auf **References**.

1. Klicken Sie auf **Manage NuGet Packages**.

1. Klicken Sie auf **Durchsuchen**. Stellen Sie sicher, dass das Kontrollkästchen **Vorabversion einbeziehen** aktiviert ist.

1. Suchen und installieren Sie die folgenden NuGet-Pakete:
    - Microsoft.Bot.Connector.DirectLine (v3.0.2)
    - Newtonsoft.Json

### <a name="edit-the-programcs-file"></a>Bearbeiten der Datei „Program.cs“

Ersetzen Sie den Inhalt der Datei **Program.cs** in DirectLineClientSample durch den folgenden:

```csharp
using System;
using System.Diagnostics;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.Bot.Connector.DirectLine;
using Newtonsoft.Json;

namespace DirectLineClientSample
{
    class Program
    {
        // ************
        // Replace the following values with your Direct Line secret and the name of your bot resource ID.
        //*************
        private static string directLineSecret = "*** Replace with Direct Line secret ***";
        private static string botId = "*** Replace with the resource name of your bot ***";

        // This gives a name to the bot user.
        private static string fromUser = "DirectLineClientSampleUser";

        static void Main(string[] args)
        {
            StartBotConversation().Wait();
        }


        /// <summary>
        /// Drives the user's conversation with the bot.
        /// </summary>
        /// <returns></returns>
        private static async Task StartBotConversation()
        {
            // Create a new Direct Line client.
            DirectLineClient client = new DirectLineClient(directLineSecret);

            // Start the conversation.
            var conversation = await client.Conversations.StartConversationAsync();

            // Start the bot message reader in a separate thread.
            new System.Threading.Thread(async () => await ReadBotMessagesAsync(client, conversation.ConversationId)).Start();

            // Prompt the user to start talking to the bot.
            Console.Write("Type your message (or \"exit\" to end): ");

            // Loop until the user chooses to exit this loop.
            while (true)
            {
                // Accept the input from the user.
                string input = Console.ReadLine().Trim();

                // Check to see if the user wants to exit.
                if (input.ToLower() == "exit")
                {
                    // Exit the app if the user requests it.
                    break;
                }
                else
                {
                    if (input.Length > 0)
                    {
                        // Create a message activity with the text the user entered.
                        Activity userMessage = new Activity
                        {
                            From = new ChannelAccount(fromUser),
                            Text = input,
                            Type = ActivityTypes.Message
                        };

                        // Send the message activity to the bot.
                        await client.Conversations.PostActivityAsync(conversation.ConversationId, userMessage);
                    }
                }
            }
        }


        /// <summary>
        /// Polls the bot continuously and retrieves messages sent by the bot to the client.
        /// </summary>
        /// <param name="client">The Direct Line client.</param>
        /// <param name="conversationId">The conversation ID.</param>
        /// <returns></returns>
        private static async Task ReadBotMessagesAsync(DirectLineClient client, string conversationId)
        {
            string watermark = null;

            // Poll the bot for replies once per second.
            while (true)
            {
                // Retrieve the activity set from the bot.
                var activitySet = await client.Conversations.GetActivitiesAsync(conversationId, watermark);
                watermark = activitySet?.Watermark;

                // Extract the activies sent from our bot.
                var activities = from x in activitySet.Activities
                                 where x.From.Id == botId
                                 select x;

                // Analyze each activity in the activity set.
                foreach (Activity activity in activities)
                {
                    // Display the text of the activity.
                    Console.WriteLine(activity.Text);

                    // Are there any attachments?
                    if (activity.Attachments != null)
                    {
                        // Extract each attachment from the activity.
                        foreach (Attachment attachment in activity.Attachments)
                        {
                            switch (attachment.ContentType)
                            {
                                // Display a hero card.
                                case "application/vnd.microsoft.card.hero":
                                    RenderHeroCard(attachment);
                                    break;

                                // Display the image in a browser.
                                case "image/png":
                                    Console.WriteLine($"Opening the requested image '{attachment.ContentUrl}'");
                                    Process.Start(attachment.ContentUrl);
                                    break;
                            }
                        }
                    }

                    // Redisplay the user prompt.
                    Console.Write("\nType your message (\"exit\" to end): ");
                }

                // Wait for one second before polling the bot again.
                await Task.Delay(TimeSpan.FromSeconds(1)).ConfigureAwait(false);
            }
        }


        /// <summary>
        /// Displays the hero card on the console.
        /// </summary>
        /// <param name="attachment">The attachment that contains the hero card.</param>
        private static void RenderHeroCard(Attachment attachment)
        {
            const int Width = 70;
            // Function to center a string between asterisks.
            Func<string, string> contentLine = (content) => string.Format($"{{0, -{Width}}}", string.Format("{0," + ((Width + content.Length) / 2).ToString() + "}", content));

            // Extract the hero card data.
            var heroCard = JsonConvert.DeserializeObject<HeroCard>(attachment.Content.ToString());

            // Display the hero card.
            if (heroCard != null)
            {
                Console.WriteLine("/{0}", new string('*', Width + 1));
                Console.WriteLine("*{0}*", contentLine(heroCard.Title));
                Console.WriteLine("*{0}*", new string(' ', Width));
                Console.WriteLine("*{0}*", contentLine(heroCard.Text));
                Console.WriteLine("{0}/", new string('*', Width + 1));
            }
        }
    }
}
```

Um zu überprüfen, ob alles ordnungsgemäß eingerichtet wurde, drücken Sie **F6**, um das Projekt zu erstellen. Es sollten keine Warnungen oder Fehler auftreten.

# <a name="javascripttabjsclientapp"></a>[JavaScript](#tab/jsclientapp)

### <a name="create-a-direct-line-client"></a>Erstellen eines Direct Line-Clients 

Nachdem Sie Ihren Web-App-Bot bereitgestellt haben, können Sie einen Direct Line-Client erstellen.

```
    md DirectLineClient
    cd DirectLineClient
    npm init
```

Installieren Sie als Nächstes diese Pakete von npm:

```
    npm install --save open
    npm install --save request
    npm install --save request-promise
    npm install --save swagger-client
```

Erstellen Sie die Datei **app.js**. Kopieren Sie den unten stehenden Code, und fügen Sie ihn in diese Datei ein.

Wir erhalten das directLineSecret im nächsten Schritt.
```javascript
var Swagger = require('swagger-client');
var open = require('open');
var rp = require('request-promise');

// config items
var pollInterval = 1000;
// Change the Direct Line Secret to your own 
var directLineSecret = 'your secret here';
var directLineClientName = 'DirectLineClient';
var directLineSpecUrl = 'https://docs.botframework.com/en-us/restapi/directline3/swagger.json';

var directLineClient = rp(directLineSpecUrl)
    .then(function (spec) {
        // client
        return new Swagger({
            spec: JSON.parse(spec.trim()),
            usePromise: true
        });
    })
    .then(function (client) {
        // add authorization header to client
        client.clientAuthorizations.add('AuthorizationBotConnector', new Swagger.ApiKeyAuthorization('Authorization', 'Bearer ' + directLineSecret, 'header'));
        return client;
    })
    .catch(function (err) {
        console.error('Error initializing DirectLine client', err);
    });

// once the client is ready, create a new conversation
directLineClient.then(function (client) {
    client.Conversations.Conversations_StartConversation()                          // create conversation
        .then(function (response) {
            return response.obj.conversationId;
        })                            // obtain id
        .then(function (conversationId) {
            sendMessagesFromConsole(client, conversationId);                        // start watching console input for sending new messages to bot
            pollMessages(client, conversationId);                                   // start polling messages from bot
        })
        .catch(function (err) {
            console.error('Error starting conversation', err);
        });
});

// Read from console (stdin) and send input to conversation using DirectLine client
function sendMessagesFromConsole(client, conversationId) {
    var stdin = process.openStdin();
    process.stdout.write('Command> ');
    stdin.addListener('data', function (e) {
        var input = e.toString().trim();
        if (input) {
            // exit
            if (input.toLowerCase() === 'exit') {
                return process.exit();
            }

            // send message
            client.Conversations.Conversations_PostActivity(
                {
                    conversationId: conversationId,
                    activity: {
                        textFormat: 'plain',
                        text: input,
                        type: 'message',
                        from: {
                            id: directLineClientName,
                            name: directLineClientName
                        }
                    }
                }).catch(function (err) {
                    console.error('Error sending message:', err);
                });

            process.stdout.write('Command> ');
        }
    });
}

// Poll Messages from conversation using DirectLine client
function pollMessages(client, conversationId) {
    console.log('Starting polling message for conversationId: ' + conversationId);
    var watermark = null;
    setInterval(function () {
        client.Conversations.Conversations_GetActivities({ conversationId: conversationId, watermark: watermark })
            .then(function (response) {
                watermark = response.obj.watermark;                                 // use watermark so subsequent requests skip old messages
                return response.obj.activities;
            })
            .then(printMessages);
    }, pollInterval);
}

// Helpers methods
function printMessages(activities) {
    if (activities && activities.length) {
        // ignore own messages
        activities = activities.filter(function (m) { return m.from.id !== directLineClientName });

        if (activities.length) {
            process.stdout.clearLine();
            process.stdout.cursorTo(0);

            // print other messages
            activities.forEach(printMessage);

            process.stdout.write('Command> ');
        }
    }
}

function printMessage(activity) {
    if (activity.text) {
        console.log(activity.text);
    }

    if (activity.attachments) {
        activity.attachments.forEach(function (attachment) {
            switch (attachment.contentType) {
                case "application/vnd.microsoft.card.hero":
                    renderHeroCard(attachment);
                    break;

                case "image/png":
                    console.log('Opening the requested image ' + attachment.contentUrl);
                    open(attachment.contentUrl);
                    break;
            }
        });
    }
}

function renderHeroCard(attachment) {
    var width = 70;
    var contentLine = function (content) {
        return ' '.repeat((width - content.length) / 2) +
            content +
            ' '.repeat((width - content.length) / 2);
    }

    console.log('/' + '*'.repeat(width + 1));
    console.log('*' + contentLine(attachment.content.title) + '*');
    console.log('*' + ' '.repeat(width) + '*');
    console.log('*' + contentLine(attachment.content.text) + '*');
    console.log('*'.repeat(width + 1) + '/');
}
```

---

## <a name="configure-the-direct-line-channel"></a>Konfigurieren des Direct Line-Kanals

Durch Hinzufügen des Direct Line-Kanals wird dieser Bot zu einem Direct Line-Bot.

So konfigurieren Sie den Direct Line-Kanal

![Blatt „Connect to channels“ (Mit Kanälen verbinden) mit hervorgehobener Schaltfläche „Direct Line channel“ (Direct Line-Kanal)](media/bot-builder-howto-direct-line/direct-line-channel-button.png)

1. Klicken Sie auf dem Blatt **Bot Channels Registration** (Botkanalregistrierung) auf **Kanäle**. Das Blatt **Connect to Channels** (Mit Kanälen verbinden) wird angezeigt.

    ![Blatt „Configure Direct Line“ (Direct Line konfigurieren) mit beiden geheimen Schlüsseln](media/bot-builder-howto-direct-line/configure-direct-line.png)

1. Klicken Sie auf dem Blatt **Connect to Channels** (Mit Kanälen verbinden) auf die Schaltfläche **Configure Direct Line channel** (Direct Line-Kanal konfigurieren). 

1. Wenn **3.0** nicht aktiviert ist, aktivieren Sie dieses Kontrollkästchen.

1. Klicken Sie für mindestens einen geheimen Schlüssel auf **Anzeigen**.

1. Kopieren Sie einen der geheimen Schlüssel, und fügen Sie ihn in der Client-App in die Zeichenfolge **directLineSecret** ein.

1. Klicken Sie auf Done.

## <a name="run-the-client-app"></a>Ausführen der Client-App

# <a name="ctabcsrunclient"></a>[C#](#tab/csrunclient)

Ihr Bot kann jetzt mit der Direct Line-Konsolenclientanwendung kommunizieren. Gehen Sie folgendermaßen vor, um die Konsolen-App auszuführen:

1. Klicken Sie in Visual Studio mit der rechten Maustaste auf das Projekt **DirectLineClientSample**, und wählen Sie **Als Startprojekt festlegen** aus.

1. Überprüfen Sie die Datei **Program.cs** im Projekt **DirectLineClientSample**.

    - Stellen Sie sicher, dass sich der geheime Direct Line-Code in der Zeichenfolge **directLineSecret** befindet.

1. Wenn **directLineSecret** nicht richtig ist, wechseln Sie zum Azure-Portal, und klicken Sie nacheinander auf Ihren Direct Line-Bot, auf **Kanäle** und auf **Configure Direct Line channel** (Direct Line-Kanal konfigurieren) (oder auf **Bearbeiten**), zeigen Sie einen Schlüssel an, und kopieren Sie diesen Schlüssel dann in die Zeichenfolge **directLineSecret**.

    - Vergewissern Sie sich, dass sich die Ressourcengruppe in der Zeichenfolge **botId** befindet.

1. Ist das nicht der Fall, kopieren Sie auf dem Blatt **Übersicht** die **Ressourcengruppe**, und fügen Sie sie in die Zeichenfolge **botId** ein.

1. Drücken Sie F5, um das Debuggen zu starten.

Die Konsolenclientanwendung wird gestartet. So testen Sie die App 

1. Geben Sie „Hi“ ein. Der Bot sollte anzeigen: „You said ‚Hi‘“.

1. Geben Sie „show me a hero card“ ein. Der Bot sollte eine Herokarte anzeigen.

1. Geben Sie „send me a botframework image“ ein. Der Bot startet einen Browser, um ein Bild aus der Bot Framework-Dokumentation anzuzeigen.

1. Wenn Sie einen beliebigen anderen Text eingeben, sollte der Bot mit „You said“, gefolgt von Ihrer Nachricht in Anführungszeichen antworten.

# <a name="javascripttabjsrunclient"></a>[JavaScript](#tab/jsrunclient)

Zum Ausführen des Beispiels müssen Sie Ihre DirectLineClient-App ausführen.

1. Öffnen Sie eine Befehlskonsole, und wechseln Sie in das Verzeichnis „DirectLineClient“.

1. Führen Sie `node app.js` aus.

Um die benutzerdefinierten Nachrichten zu testen, geben Sie in der Konsole des Clients „show me a hero card“ oder „send me a botframework image“ ein. Es sollte das folgende Ergebnis angezeigt werden.

![Ergebnis](media/bot-builder-howto-direct-line/outcome.png)

---


### <a name="next-steps"></a>Nächste Schritte
