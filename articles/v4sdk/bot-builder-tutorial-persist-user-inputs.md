---
title: Speichern von Benutzerdaten | Microsoft-Dokumentation
description: Erfahren Sie mehr zum Speichern von Benutzerstatusdaten im Speicher des Bot Builder SDK.
keywords: speichern von benutzerdaten, speicher, konversationsdaten
author: v-ducvo
ms.author: v-ducvo
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 09/19/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 61e86ce9536bc5d77dc7bd411054b2f65bce8dd9
ms.sourcegitcommit: b8bd66fa955217cc00b6650f5d591b2b73c3254b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2018
ms.locfileid: "49326557"
---
# <a name="persist-user-data"></a>Speichern von Benutzerdaten

[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

Wenn der Bot eine Benutzereingabe anfordert, möchten Sie wahrscheinlich, dass einige der Informationen in einem Speicher beibehalten werden. Mit dem Bot Builder SDK können Sie Benutzereingaben in *In-Memory-Speicher* oder in Datenbankspeicher wie *CosmosDB* speichern. Lokale Speicherarten werden hauptsächlich während der Test- oder Prototyp-Phase Ihres Bots verwendet. Für Bots in Produktionsumgebungen sollten beständige Speicherarten wie etwa Datenbankspeicher verwendet werden.

In diesem Tutorial erfahren Sie, wie Sie Ihr Speicherobjekt definieren und Benutzereingaben dauerhaft in dem Speicherobjekt speichern. Wir verwenden einen Dialog, um den Benutzer nach seinem Namen zu fragen, sofern dieser nicht bereits vorliegt. Unabhängig davon, für welchen Speichertyp Sie sich entscheiden, ist das Verfahren für die Einbindung und die Speicherung der Daten gleich. Im Code dieses Themas wird `CosmosDB` als beständiger Datenspeicher verwendet.

## <a name="prerequisites"></a>Voraussetzungen

Je nach verwendeter Entwicklungsumgebung sind bestimmte Ressourcen erforderlich.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

* [Installieren Sie Visual Studio 2015 oder eine höhere Version.](https://www.visualstudio.com/downloads/)
* [Installieren Sie die Bot Builder V4-Vorlage.](https://marketplace.visualstudio.com/items?itemName=BotBuilder.botbuilderv4)

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

* [Installieren Sie Visual Studio Code.](https://www.visualstudio.com/downloads/)
* [Installieren Sie Node.js v8.5 oder eine höhere Version.](https://nodejs.org/en/)
* [Installieren Sie Yeoman.](http://yeoman.io/)
* Installieren Sie den Node.js v4-Bot Builder-Vorlagengenerator.

    ```shell
    npm install generator-botbuilder
    ```

---

In diesem Tutorial werden folgende Pakete verwendet:

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Wir beginnen mit einer einfachen EchoBot-Vorlage. Entsprechende Anweisungen finden Sie in der [Schnellstartanleitung für .NET](~/dotnet/bot-builder-dotnet-quickstart.md).

Installieren Sie die folgenden zusätzlichen Pakete über den NuGet-Paket-Manager:

* **Microsoft.Bot.Builder.Azure**
* **Microsoft.Bot.Builder.Dialogs**

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Wir beginnen mit einer einfachen EchoBot-Vorlage. Entsprechende Anweisungen finden Sie in der [Schnellstartanleitung für JavaScript](~/javascript/bot-builder-javascript-quickstart.md).

Installieren Sie diese zusätzlichen npm-Pakete.

```cmd
npm install --save botbuilder-dialogs
npm install --save botbuilder-azure
```

---

Um den in diesem Tutorial erstellten Bot testen zu können, muss der [BotFramework-Emulator](https://github.com/Microsoft/BotFramework-Emulator) installiert werden.

## <a name="create-a-cosmosdb-service-and-update-your-application-settings"></a>Erstellen eines Cosmos DB-Diensts und Aktualisieren Ihrer Anwendungseinstellungen

Gehen Sie gemäß der Anleitung zum [Verwenden von Cosmos DB](bot-builder-howto-v4-storage.md#using-cosmos-db) vor, um einen Cosmos DB-Dienst und eine entsprechende Datenbank einzurichten. Die erforderlichen Schritte sind hier noch einmal zusammengefasst:

1. Melden Sie sich in einem neuen Browserfenster beim <a href="http://portal.azure.com/" target="_blank">Azure-Portal</a> an.
1. Klicken Sie auf **Ressource erstellen > Datenbanken > Azure Cosmos DB**.
1. Geben Sie auf der Seite **Neues Konto** im Feld **ID** einen eindeutigen Namen an. Wählen Sie für **API** die Option **SQL** aus, und geben Sie Informationen für **Abonnement**, **Standort** und **Ressourcengruppe** an.
1. Klicken Sie auf **Create**.

Fügen Sie dem Dienst anschließend eine Sammlung für die Verwendung mit diesem Bot hinzu.

Erfassen Sie die Datenbank-ID und die Sammlungs-ID, die Sie beim Hinzufügen der Sammlung verwendet haben, sowie den URI und den Primärschlüssel aus den Schlüsseleinstellungen der Sammlung. Diese werden benötigt, um eine Verbindung zwischen Bot und Dienst herzustellen.

### <a name="update-your-application-settings"></a>Aktualisieren Ihrer Anwendungseinstellungen

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Aktualisieren Sie die Datei **appsettings.json** mit den Verbindungsinformationen für Cosmos DB.

```csharp
{
  // Settings for CosmosDB.
  "CosmosDB": {
    "DatabaseID": "<your-database-identifier>",
    "CollectionID": "<your-collection-identifier>",
    "EndpointUri": "<your-CosmosDB-endpoint>",
    "AuthenticationKey": "<your-primary-key>"
  }
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Suchen Sie in Ihrem Projektordner nach der Datei mit der Erweiterung **.env**, und fügen Sie ihr die folgenden Einträge mit Ihren Cosmos-spezifischen Daten hinzu:

**.env**

```text
DB_SERVICE_ENDPOINT=<your-CosmosDB-endpoint>
AUTH_KEY=<authentication key>
DATABASE=<your-primary-key>
COLLECTION=<your-collection-identifier>
```

Ändern Sie anschließend `storage` in der Hauptindexdatei (**index.js**) Ihres Bots, um `CosmosDbStorage` anstelle von `MemoryStorage` zu verwenden. Während der Laufzeit werden die Umgebungsvariablen abgerufen, um diese Felder aufzufüllen.

```javascript
const storage = new CosmosDbStorage({
    serviceEndpoint: process.env.DB_SERVICE_ENDPOINT,
    authKey: process.env.AUTH_KEY, 
    databaseId: process.env.DATABASE,
    collectionId: process.env.COLLECTION
});
```

---

## <a name="create-storage-state-manager-and-state-property-accessor-objects"></a>Erstellen von Objekten für Speicher, Zustands-Manager und Zustandseigenschaftenaccessor

Bots verwenden Zustandsverwaltungs- und Speicherobjekte, um den Zustand zu verwalten und zu speichern. Der Manager bietet eine Abstraktionsebene, dank der Sie unabhängig von der Art des zugrunde liegenden Speichers mithilfe eines Zustandseigenschaftenaccessors auf Zustandseigenschaften zugreifen können. Verwenden Sie den Zustands-Manager, um Daten in den Speicher zu schreiben.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

### <a name="define-a-class-for-your-user-data"></a>Definieren einer Klasse für Ihre Benutzerdaten

Benennen Sie die Datei **CounterState.cs** in **UserData.cs** und die Klasse **CounterState** in **UserData** um.

Aktualisieren Sie diese Klasse für die Speicherung der Daten, die Sie erfassen.

```csharp
/// <summary>
/// Class for storing persistent user data.
/// </summary>
public class UserData
{
    public string Name { get; set; }
}
```

### <a name="define-a-class-for-your-state-and-state-property-accessor-objects"></a>Definieren einer Klasse für Ihre Zustands- und Zustandseigenschaftenaccessor-Objekte

Benennen Sie die Datei **EchoBotAccessors.cs** in **BotAccessors.cs** und die Klasse **EchoBotAccessors** in **BotAccessors** um.

Aktualisieren Sie diese Klasse für die Speicherung der Zustandsobjekte und Zustandseigenschaftenaccessoren, die Ihr Bot benötigt.

```csharp
using Microsoft.Bot.Builder;
using Microsoft.Bot.Builder.Dialogs;
using System;

public class BotAccessors
{
    public UserState UserState { get; }

    public ConversationState ConversationState { get; }

    public IStatePropertyAccessor<DialogState> DialogStateAccessor { get; set; }

    public IStatePropertyAccessor<UserData> UserDataAccessor { get; set; }

    public BotAccessors(UserState userState, ConversationState conversationState)
    {
        this.UserState = userState
            ?? throw new ArgumentNullException(nameof(userState));

        this.ConversationState = conversationState
            ?? throw new ArgumentNullException(nameof(conversationState));
    }
}
```

### <a name="update-the-startup-code-for-your-bot"></a>Aktualisieren des Startcodes für Ihren Bot

Aktualisieren Sie die using-Anweisungen in der Datei **Startup.cs**.

```csharp
using System;
using System.Linq;
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Bot.Builder;
using Microsoft.Bot.Builder.Azure;
using Microsoft.Bot.Builder.Dialogs;
using Microsoft.Bot.Builder.Integration;
using Microsoft.Bot.Builder.Integration.AspNet.Core;
using Microsoft.Bot.Configuration;
using Microsoft.Bot.Connector.Authentication;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Logging;
using Microsoft.Extensions.Options;
```

Aktualisieren Sie in der Methode `ConfigureServices` den Aufruf zum Hinzufügen des Bots ab der Erstellung des zugrunde liegenden Speicherobjekts, und registrieren Sie anschließend Ihr Botaccessorenobjekt.

Wir benötigen den Konversationszustand für das Objekt `DialogState`, um den Dialogzustand nachzuverfolgen. Wir registrieren Singletons für den Dialog-Zustandseigenschaftenaccessor und den Dialogsatz, den unser Bot verwendet. Der Bot erstellt einen eigenen Zustandseigenschaftenaccessor für den Benutzerzustand.

Der Accessor `BotAccessors` ermöglicht eine effiziente Speicherverwaltung für mehrere Zustandsobjekte Ihres Bots.

```cs
public void ConfigureServices(IServiceCollection services)
{
    // Register your bot.
    services.AddBot<UserDataBot>(options =>
    {
        // ...

        // Use persistent storage and create state management objects.
        var cosmosSettings = Configuration.GetSection("CosmosDB");
        IStorage storage = new CosmosDbStorage(
            new CosmosDbStorageOptions
            {
                DatabaseId = cosmosSettings["DatabaseID"],
                CollectionId = cosmosSettings["CollectionID"],
                CosmosDBEndpoint = new Uri(cosmosSettings["EndpointUri"]),
                AuthKey = cosmosSettings["AuthenticationKey"],
            });
        options.State.Add(new ConversationState(storage));
        options.State.Add(new UserState(storage));
    });

    // Register the bot's state and state property accessor objects.
    services.AddSingleton<BotAccessors>(sp =>
    {
        var options = sp.GetRequiredService<IOptions<BotFrameworkOptions>>().Value;
        var userState = options.State.OfType<UserState>().FirstOrDefault();
        var conversationState = options.State.OfType<ConversationState>().FirstOrDefault();

        return new BotAccessors(userState, conversationState)
        {
            UserDataAccessor = userState.CreateProperty<UserData>("UserDataBot.UserData"),
            DialogStateAccessor = conversationState.CreateProperty<DialogState>("UserDataBot.DialogState"),
        };
    });
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

### <a name="update-your-server-code"></a>Aktualisieren des Servercodes

Aktualisieren Sie in der Datei **index.js** Ihres Projekts die folgenden require-Anweisungen:

```javascript
// Import required bot services.
const { BotFrameworkAdapter, ConversationState, UserState } = require('botbuilder');
const { CosmosDbStorage } = require('botbuilder-azure');
```

In diesem Tutorial verwenden wir `UserState` zum Speichern von Daten. Wir müssen ein neues Objekt vom Typ `userState` erstellen und diese Codezeile aktualisieren, um einen zweiten Parameter an die Klasse `MainDialog` zu übergeben:

```javascript
// Create conversation state with in-memory storage provider.
const conversationState = new ConversationState(storage);
const userState = new UserState(storage);

// Create the main dialog.
const bot = new MyBot(conversationState, userState);
```

Wenn ein allgemeiner Fehler auftritt, sollten Sie sowohl die Konversation als auch den Benutzerzustand löschen.

```javascript
// Catch-all for errors.
adapter.onTurnError = async (context, error) => {
    // This check writes out errors to console log .vs. app insights.
    console.error(`\n [onTurnError]: ${error}`);
    // Send a message to the user
    context.sendActivity(`Oops. Something went wrong!`);
    // Clear out state
    await conversationState.load(context);
    await conversationState.clear(context);
    await userState.load(context);
    await userState.clear(context);
    // Save state changes.
    await conversationState.saveChanges(context);
    await userState.saveChanges(context);
};
```

Aktualisieren Sie außerdem die HTTP-Serverschleife, um unser Botobjekt aufzurufen.

```javascript
// Listen for incoming requests.
server.post('/api/messages', (req, res) => {
    adapter.processActivity(req, res, async (context) => {
        // Route to main dialog.
        await bot.onTurn(context);
    });
});
```

### <a name="update-your-bot-logic"></a>Aktualisieren der Botlogik

Geben Sie in der Klasse `MyBot` mithilfe einer require-Anweisung die erforderlichen Bibliotheken für den Betrieb Ihres Bots an. In diesem Tutorial verwenden wir die Bibliothek **Dialogs**.

```javascript
// Required packages for this bot
const { ActivityTypes } = require('botbuilder');
const { DialogSet, WaterfallDialog, TextPrompt, NumberPrompt } = require('botbuilder-dialogs');

```

Aktualisieren Sie den Konstruktor der `MyBot`-Klasse, um `userState` als zweiten Parameter zu akzeptieren. Aktualisieren Sie außerdem den Konstruktor, um die Zustände, Dialoge und Eingabeaufforderungen zu definieren, die wir für dieses Tutorial benötigen. In diesem Szenario definieren wir einen Wasserfall mit zwei Schritten: In _Schritt 1_ wird der Benutzer nach seinem Namen gefragt. In _Schritt 2_ wird die Antwort des Benutzers zurückgegeben. Die Angabe muss von der Hauptlogik des Bots gespeichert werden.

```javascript
constructor(conversationState, userState) {

    // creates a new state accessor property.
    this.conversationState = conversationState;
    this.userState = userState;

    this.dialogState = this.conversationState.createProperty('dialogState');
    this.userDataAccessor = this.userState.createProperty('userData');

    this.dialogs = new DialogSet(this.dialogState);

    // Add prompts
    this.dialogs.add(new TextPrompt('textPrompt'));

    // Add a waterfall dialog to collect and return the user's name.
    this.dialogs.add(new WaterfallDialog('greetings', [
        async function (step) {
            return await step.prompt('textPrompt', "What is your name?");
        },
        async function (step) {
            return await step.endDialog(step.result);
        }
    ]));
}
```

---

Beim Speichern von Benutzerdaten haben Sie mehrere Möglichkeiten. Das SDK bietet einige Zustandsobjekte mit unterschiedlichen Bereichen, aus denen Sie wählen können. Im vorliegenden Fall verwenden wir den Konversationszustand zur Verwaltung des Dialogzustandsobjekts und den Benutzerzustand zur Verwaltung von Benutzerdaten.

Weitere allgemeine Informationen zu Speicher und Zustand finden Sie unter [Direktes Schreiben in den Speicher](bot-builder-howto-v4-storage.md) sowie unter [Verwalten des Konversations- und Benutzerzustands](bot-builder-howto-v4-state.md).

## <a name="create-a-greeting-dialog"></a>Erstellen eines Begrüßungsdialogs

Wir verwenden einen Dialog, um den Namen des Benutzers zu erfassen. Der Einfachheit halber gibt der Dialog den Namen des Benutzers zurück, und der Bot verwaltet das Benutzerdatenobjekt und den Zustand.

Erstellen Sie eine Klasse vom Typ **GreetingsDialog**, und fügen Sie den folgenden Code ein.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```cs
using Microsoft.Bot.Builder;
using Microsoft.Bot.Builder.Dialogs;

/// <summary>Defines a dialog for collecting a user's name.</summary>
public class GreetingsDialog : DialogSet
{
    /// <summary>The ID of the main dialog.</summary>
    public const string MainDialog = "main";

    /// <summary>The ID of the text prompt to use in the dialog.</summary>
    private const string TextPrompt = "textPrompt";

    /// <summary>Creates a new instance of this dialog set.</summary>
    /// <param name="dialogState">The dialog state property accessor to use for dialog state.</param>
    public GreetingsDialog(IStatePropertyAccessor<DialogState> dialogState)
        : base(dialogState)
    {
        // Add the text prompt to the dialog set.
        Add(new TextPrompt(TextPrompt));

        // Define the main dialog and add it to the set.
        Add(new WaterfallDialog(MainDialog, new WaterfallStep[]
        {
            async (stepContext, cancellationToken) =>
            {
                // Ask the user for their name.
                return await stepContext.PromptAsync(TextPrompt, new PromptOptions
                {
                    Prompt = MessageFactory.Text("What is your name?"),
                }, cancellationToken);
            },
            async (stepContext, cancellationToken) =>
            {
                // Assume that they entered their name, and return the value.
                return await stepContext.EndDialogAsync(stepContext.Result, cancellationToken);
            },
        }));
    }
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Informationen dazu, wo der Dialog innerhalb des Konstruktors von `MainDialog` erstellt werden muss, finden Sie im Abschnitt weiter oben.

---

Weitere Informationen zu Dialogen finden Sie unter [Auffordern von Benutzern zur Eingabe mithilfe der Dialogbibliothek](bot-builder-prompts.md) sowie unter [Verwalten eines einfachen Konversationsflusses mit Dialogen](bot-builder-dialog-manage-conversation-flow.md).

## <a name="update-your-bot-to-use-the-dialog-and-user-state"></a>Aktualisieren Ihres Bots für die Verwendung des Dialog- und Benutzerzustands

Boterstellung und die Verwaltung von Benutzereingaben werden separat behandelt.

### <a name="add-the-dialog-and-a-user-state-accessor"></a>Hinzufügen des Dialogs und eines Benutzerzustandsaccessors

Wir müssen die Dialoginstanz und den Zustandseigenschaftenaccessor für Benutzerdaten nachverfolgen.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Fügen Sie Code für die Botinitialisierung hinzu.

```cs
using System.Linq;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.Bot.Builder;
using Microsoft.Bot.Builder.Dialogs;
using Microsoft.Bot.Schema;

/// <summary>Defines the bot for the persisting user data tutorial.</summary>
public class UserDataBot : IBot
{
    /// <summary>The bot's state and state property accessor objects.</summary>
    private BotAccessors Accessors { get; }

    /// <summary>The dialog set that has the dialog to use.</summary>
    private GreetingsDialog GreetingsDialog { get; }

    /// <summary>Create a new instance of the bot.</summary>
    /// <param name="options">The options to use for our app.</param>
    /// <param name="greetingsDialog">An instance of the dialog set.</param>
    public UserDataBot(BotAccessors botAccessors)
    {
        // Retrieve the bot's state and accessors.
        Accessors = botAccessors;

        // Create the greetings dialog.
        GreetingsDialog = new GreetingsDialog(Accessors.DialogStateAccessor);
    }
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Informationen dazu, wo diese Zustandsaccessoren innerhalb des Konstruktors von `MainDialog` definiert werden müssen, finden Sie im Abschnitt weiter oben.

---

### <a name="update-the-turn-handler"></a>Aktualisieren des Turn-Handlers

Der Turn-Handler begrüßt den Benutzer zu Beginn der Konversation und antwortet auf Nachrichten, die der Benutzer an den Bot sendet. Sollte dem Bot der Name des Benutzers noch nicht bekannt sein, wird der Benutzer zu Beginn des Begrüßungsdialogs nach seinem Namen gefragt. Nach Abschluss des Dialogs wird der Name mithilfe eines Zustandsobjekts, das von unserem Zustandseigenschaftenaccessor generiert wurde, im Benutzerzustand gespeichert. Am Ende der Runde schreiben der Accessor und der Zustands-Manager Änderungen am Objekt in den Speicher.

Darüber hinaus unterstützen wir die Aktivität _DeleteUserData_.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Aktualisieren Sie die Methode `OnTurnAsync` des Bots.

```cs
/// <summary>Handles incoming activities to the bot.</summary>
/// <param name="turnContext">The context object for the current turn.</param>
/// <param name="cancellationToken">A cancellation token that can be used by other objects
/// or threads to receive notice of cancellation.</param>
/// <returns>A task that represents the work queued to execute.</returns>
public async Task OnTurnAsync(ITurnContext turnContext, CancellationToken cancellationToken = default(CancellationToken))
{
    // Retrieve user data from state.
    UserData userData = await Accessors.UserDataAccessor.GetAsync(turnContext, () => new UserData());

    // Establish context for our dialog from the turn context.
    DialogContext dc = await GreetingsDialog.CreateContextAsync(turnContext);

    // Handle conversation update, message, and delete user data activities.
    switch (turnContext.Activity.Type)
    {
        case ActivityTypes.ConversationUpdate:

            // Greet any user that is added to the conversation.
            IConversationUpdateActivity activity = turnContext.Activity.AsConversationUpdateActivity();
            if (activity.MembersAdded.Any(member => member.Id != activity.Recipient.Id))
            {
                if (userData.Name is null)
                {
                    // If we don't already have their name, start a dialog to collect it.
                    await turnContext.SendActivityAsync("Welcome to the User Data bot.");
                    await dc.BeginDialogAsync(GreetingsDialog.MainDialog);
                }
                else
                {
                    // Otherwise, greet them by name.
                    await turnContext.SendActivityAsync($"Hi {userData.Name}! Welcome back to the User Data bot.");
                }
            }

            break;

        case ActivityTypes.Message:

            // If there's a dialog running, continue it.
            if (dc.ActiveDialog != null)
            {
                var dialogTurnResult = await dc.ContinueDialogAsync();
                if (dialogTurnResult.Status == DialogTurnStatus.Complete
                    && dialogTurnResult.Result is string name
                    && !string.IsNullOrWhiteSpace(name))
                {
                    // If it completes successfully and returns a valid name, save the name and greet the user.
                    userData.Name = name;
                    await turnContext.SendActivityAsync($"Pleased to meet you {userData.Name}.");
                }
            }
            else if (userData.Name is null)
            {
                // Else, if we don't have the user's name yet, ask for it.
                await dc.BeginDialogAsync(GreetingsDialog.MainDialog);
            }
            else
            {
                // Else, echo the user's message text.
                await turnContext.SendActivityAsync($"{userData.Name} said, '{turnContext.Activity.Text}'.");
            }

            break;

        case ActivityTypes.DeleteUserData:

            // Delete the user's data.
            userData.Name = null;
            await turnContext.SendActivityAsync("I have deleted your user data.");

            break;
    }

    // Update the user data in the turn's state cache.
    await Accessors.UserDataAccessor.SetAsync(turnContext, userData, cancellationToken);

    // Persist any changes to storage.
    await Accessors.UserState.SaveChangesAsync(turnContext, false, cancellationToken);
    await Accessors.ConversationState.SaveChangesAsync(turnContext, false, cancellationToken);
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Aktualisieren Sie den `onTurn`-Handler des Bots.

```javascript
async onTurn(turnContext) {
    const dc = await this.dialogs.createContext(turnContext); // Create dialog context
    const userData = await this.userDataAccessor.get(turnContext, {});

    switch (turnContext.activity.type) {
        case ActivityTypes.ConversationUpdate:
            if (turnContext.activity.membersAdded[0].name !== 'Bot') {
                if (userData.name) {
                    await turnContext.sendActivity(`Hi ${userData.name}! Welcome back to the User Data bot.`);
                }
                else {
                    // send a "this is what the bot does" message
                    await turnContext.sendActivity('Welcome to the User Data bot.');
                    await dc.beginDialog('greetings');
                }
            }
            break;
        case ActivityTypes.Message:
            // If there is an active dialog running, continue it
            if (dc.activeDialog) {
                var turnResult = await dc.continueDialog();
                if (turnResult.status == "complete" && turnResult.result) {
                    // If it completes successfully and returns a value, save the name and greet the user.
                    userData.name = turnResult.result;
                    await this.userDataAccessor.set(turnContext, userData);
                    await turnContext.sendActivity(`Pleased to meet you ${userData.name}.`);
                }
            }
            // Else, if we don't have the user's name yet, ask for it.
            else if (!userData.name) {
                await dc.beginDialog('greetings');
            }
            // Else, echo the user's message text.
            else {
                await turnContext.sendActivity(`${userData.name} said, ${turnContext.activity.text}.`);
            }
            break;
        case ActivityTypes.DeleteUserData:
            // Delete the user's data.
            // Note: You can use the Emulator to send this activity.
            userData.name = null;
            await this.userDataAccessor.set(turnContext, userData);
            await turnContext.sendActivity("I have deleted your user data.");
            break;
    }

    // Save changes to the conversation and user states.
    await this.conversationState.saveChanges(turnContext);
    await this.userState.saveChanges(turnContext);
}
```

---

## <a name="start-your-bot-in-visual-studio"></a>Starten Ihres Bots in Visual Studio

Erstellen Sie Ihre Anwendung, und führen Sie sie aus.

## <a name="start-the-emulator-and-connect-your-bot"></a>Starten des Emulators und Herstellen einer Verbindung mit Ihrem Bot

Starten Sie im nächste Schritt den Emulator, und stellen Sie dann im Emulator eine Verbindung mit Ihrem Bot her:

1. Klicken Sie im Emulator auf der Registerkarte „Willkommen“ auf den Link **Bot öffnen**.
2. Wählen Sie in dem Verzeichnis, in dem Sie die Visual Studio-Projektmappe erstellt haben, die BOT-Datei aus.

## <a name="interact-with-your-bot"></a>Interagieren mit Ihrem Bot

Senden Sie eine Nachricht an Ihren Bot. Dieser antwortet daraufhin mit einer Nachricht.
![Ausgeführter Emulator](../media/emulator-v4/emulator-running.png)

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Verwalten des Konversations- und Benutzerzustands](bot-builder-howto-v4-state.md)
