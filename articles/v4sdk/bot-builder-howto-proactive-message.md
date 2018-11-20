---
title: Abrufen von Benachrichtigungen von einem Bot | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Benachrichtigungsnachrichten senden.
keywords: proaktive Nachricht, Benachrichtigungsnachricht, Botbenachrichtigung
author: jonathanfingold
ms.author: jonathanfingold
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 11/08/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: fac1e026ac92fcbe1b5c5bb9363c29e1d9e9b02a
ms.sourcegitcommit: b6327fa0b4547556d2d45d8910796e0c02948e43
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2018
ms.locfileid: "51681587"
---
# <a name="get-notification-from-a-bot"></a>Abrufen von Benachrichtigungen von einem Bot

[!INCLUDE [pre-release-label](~/includes/pre-release-label.md)]

In der Regel steht jede Nachricht, die ein Bot an den Benutzer sendet, im direkten Zusammenhang mit der vorherigen Eingabe des Benutzers.
In einigen Fällen muss ein Bot dem Benutzer eine Nachricht senden, die nicht im direkten Zusammenhang mit dem aktuellen Thema der Unterhaltung oder mit der letzten vom Benutzer gesendeten Nachricht steht. Diese Art von Nachrichten werden als _proaktive Nachrichten_ bezeichnet.

## <a name="uses"></a>Verwendung

Proaktive Nachrichten können in einer Vielzahl von Szenarien nützlich sein. Wenn ein Bot einen Timer oder eine Erinnerung festlegt, muss er den Benutzer benachrichtigen, wenn dieser Zeitpunkt erreicht ist. Auch in dem Fall, dass ein Bot eine Benachrichtigung von einem externen System empfängt, muss er möglicherweise diese Informationen umgehend an den Benutzer weitergeben. Wenn der Benutzer den Bot vorher aufgefordert hat, den Preis eines Produkts zu überwachen, kann der Bot den Benutzer warnen, wenn der Preis für das Produkt um 20 % gesunken ist. Ein anderes Beispiel: Wenn ein Bot einen gewissen Zeitraum zum Kompilieren einer Antwort auf die Frage des Benutzers benötigt, kann er den Benutzer über die Verzögerung informieren und die Unterhaltung in der Zwischenzeit fortfahren lassen. Wenn der Bot die Kompilierung der Antwort auf die Frage abgeschlossen hat, teilt er dies dem Benutzer mit.

Beachten Sie bei der Implementierung proaktiver Nachrichten in Ihrem Bot Folgendes:

- Senden Sie nicht mehrere proaktive Nachrichten innerhalb kurzer Zeit. Einige Kanäle erzwingen Einschränkungen darüber, wie oft ein Bot Nachrichten an den Benutzer senden kann, und deaktivieren den Bot, wenn er gegen diese Einschränkungen verstößt.
- Senden Sie keine proaktiven Nachrichten an Benutzer, die vorher nicht mit dem Bot interagiert haben oder auf andere Weise, z.B. per E-Mail oder SMS, Kontakt mit dem Bot angefordert haben.

Eine **proaktive Ad-hoc-Nachricht** ist der einfachste Typ von proaktiven Nachrichten.
Der Bot streut die Nachricht einfach immer dann in die Konversation ein, wenn diese ausgelöst wird, ohne Rücksicht darauf, ob der Benutzer derzeit in einer anderen Konversation mit dem Bot aktiv ist und wird nicht versuchen, die Unterhaltung zu verändern.

Ziehen Sie zur reibungsloseren Verarbeitung von Benachrichtigungen andere Möglichkeiten zum Integrieren der Benachrichtigung in den Konversationsablauf in Betracht. Legen Sie beispielsweise ein Flag im Konversationsstatus fest, oder fügen Sie die Benachrichtigung zu einer Warteschlange hinzu.

### <a name="prerequisites"></a>Voraussetzungen
- Eine Kopie des **Beispiels für proaktive Nachrichten** in [C#](https://aka.ms/proactive-sample-cs) oder [JS](https://aka.ms/proactive-sample-js).
- Installieren Sie für JS [Bot Builder](https://www.npmjs.com/package/botbuilder) für Node.js.


### <a name="about-the-sample-code"></a>Informationen zum Beispielcode

Im Beispiel für proaktive Nachrichten werden Benutzeraufgaben modelliert, die eine unbestimmte Dauer aufweisen können. Der Bot speichert Informationen zur Aufgabe, teilt dem Benutzer mit, dass der Bot sich nach Beendigung der Aufgabe wieder bei ihm meldet, und setzt die Konversation fort. Nachdem die Aufgabe abgeschlossen ist, sendet der Bot die Bestätigungsnachricht proaktiv in der Originalkonversation.

#### <a name="define-job-data-and-state"></a>Definieren der Auftragsdaten und des Zustands

In diesem Szenario verfolgen wir einige Aufträge nach, die von verschiedenen Benutzern in verschiedenen Konversationen erstellt werden können. Wir müssen Informationen zu den einzelnen Aufträgen speichern, z.B. den Konversationsverweis und eine Auftrags-ID. Dazu benötigen wir Folgendes:
- Den Konversationsverweis, damit wir die proaktive Nachricht an die richtige Konversation senden können.
- Eine Möglichkeit zum Identifizieren von Aufträgen. In diesem Beispiel verwenden wir einen einfachen Zeitstempel.
- Eine Möglichkeit, den Auftragszustand unabhängig vom Konversations- oder Benutzerzustand zu speichern.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Wir müssen Klassen für die Auftragsdaten und den Auftragszustand definieren. Außerdem müssen wir unseren Bot registrieren und einen Zustandseigenschaftenaccessor für das Auftragsprotokoll einrichten.

#### <a name="define-a-class-for-job-data"></a>Definieren einer Klasse für Auftragsdaten

Die `JobLog`-Klasse verfolgt Auftragsdaten nach, die anhand der Auftragsnummer indiziert sind (Zeitstempel). Die `JobLog`-Klasse verfolgt alle ausstehenden Aufträge nach.  Jeder Auftrag wird durch einen eindeutigen Schlüssel identifiziert. `Job data` beschreibt den Zustand eines Auftrags und ist als eine innere Klasse eines Wörterbuchs definiert.

```csharp
public class JobLog : Dictionary<long, JobLog.JobData>
{
    public class JobData
    {
        // Gets or sets the time-stamp for the job.
        public long TimeStamp { get; set; } = 0;

        // Gets or sets a value indicating whether indicates whether the job has completed.
        public bool Completed { get; set; } = false;

        // Gets or sets the conversation reference to which to send status updates.
        public ConversationReference Conversation { get; set; }
    }
}
```

#### <a name="define-a-state-middleware-class"></a>Definieren einer Middleware-Klasse für den Zustand

Mit der **JobState**-Klasse wird der Auftragszustand unabhängig vom Konversations- oder Benutzerzustand verwaltet.

```csharp
using Microsoft.Bot.Builder;

/// A BotState for managing bot state for "bot jobs".
public class JobState : BotState
{
    // The key used to cache the state information in the turn context.
    private const string StorageKey = "ProactiveBot.JobState";

    // Initializes a new instance of the JobState class.
    public JobState(IStorage storage)
        : base(storage, StorageKey)
    {
    }

    // Gets the storage key for caching state information.
    protected override string GetStorageKey(ITurnContext turnContext) => StorageKey;
}
```

### <a name="register-the-bot-and-required-services"></a>Registrieren des Bots und der erforderlichen Dienste

Die Datei **Startup.cs** registriert den Bot und die zugeordneten Dienste.

1. Die `ConfigureServices`-Methode registriert den Bot, einschließlich Fehlerbehandlung und Zustandsverwaltung. Darüber hinaus registriert sie den Endpunktdienst des Bots und den Auftragszustandsaccessor.

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        // The Memory Storage used here is for local bot debugging only. When the bot
        // is restarted, everything stored in memory will be gone.
        IStorage dataStore = new MemoryStorage();

        // ...

        // Create Job State object.
        // The Job State object is where we persist anything at the job-scope.
        // Note: It's independent of any user or conversation.
        var jobState = new JobState(dataStore);

        // Make it available to our bot
        services.AddSingleton(sp => jobState);

        // Register the proactive bot.
        services.AddBot<ProactiveBot>(options =>
        {
            var secretKey = Configuration.GetSection("botFileSecret")?.Value;
            var botFilePath = Configuration.GetSection("botFilePath")?.Value;

            // Loads .bot configuration file and adds a singleton that your Bot can access through dependency injection.
            var botConfig = BotConfiguration.Load(botFilePath ?? @".\BotConfiguration.bot", secretKey);
            services.AddSingleton(sp => botConfig ?? throw new InvalidOperationException($"The .bot config file could not be loaded. ({botConfig})"));

            // Retrieve current endpoint.
            var environment = _isProduction ? "production" : "development";
            var service = botConfig.Services.Where(s => s.Type == "endpoint" && s.Name == environment).FirstOrDefault();
            if (!(service is EndpointService endpointService))
            {
                throw new InvalidOperationException($"The .bot file does not contain an endpoint with name '{environment}'.");
            }

            options.CredentialProvider = new SimpleCredentialProvider(endpointService.AppId, endpointService.AppPassword);

            // Creates a logger for the application to use.
            ILogger logger = _loggerFactory.CreateLogger<ProactiveBot>();

            // Catches any errors that occur during a conversation turn and logs them.
            options.OnTurnError = async (context, exception) =>
            {
                logger.LogError($"Exception caught : {exception}");
                await context.SendActivityAsync("Sorry, it looks like something went wrong.");
            };

        });

        services.AddSingleton(sp =>
        {
            var config = BotConfiguration.Load(@".\BotConfiguration.bot");
            var endpointService = (EndpointService)config.Services.First(s => s.Type == "endpoint")
                                    ?? throw new InvalidOperationException(".bot file 'endpoint' must be configured prior to running.");

            return endpointService;
        });
    }
    ```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Der Code in der Datei **index.js** führt folgende Schritte aus:
- Verweist auf die Botklasse und die **BOT**-Datei
- Erstellt den HTTP-Server, den Botadapter und die Speicherobjekte
- Erstellt den Bot und startet den Server, übergibt Aktivitäten an den Bot

```javascript
const restify = require('restify');
const path = require('path');

// Import required bot services. See https://aka.ms/bot-services to learn more about the different part of a bot.
const { BotFrameworkAdapter, BotState, MemoryStorage } = require('botbuilder');
const { BotConfiguration } = require('botframework-config');

const { ProactiveBot } = require('./bot');

// Read botFilePath and botFileSecret from .env file.
// Note: Ensure you have a .env file and include botFilePath and botFileSecret.
const ENV_FILE = path.join(__dirname, '.env');
require('dotenv').config({ path: ENV_FILE });

// Create HTTP server.
let server = restify.createServer();
server.listen(process.env.port || process.env.PORT || 3978, function() {
    console.log(`\n${ server.name } listening to ${ server.url }.`);
    console.log(`\nGet Bot Framework Emulator: https://aka.ms/botframework-emulator.`);
    console.log(`\nTo talk to your bot, open proactive-messages.bot file in the Emulator.`);
});

// .bot file path
const BOT_FILE = path.join(__dirname, (process.env.botFilePath || ''));

// Read the bot's configuration from a .bot file identified by BOT_FILE.
// This includes information about the bot's endpoints and configuration.
let botConfig;
try {
    botConfig = BotConfiguration.loadSync(BOT_FILE, process.env.botFileSecret);
} catch (err) {
    console.error(`\nError reading bot file. Please ensure you have valid botFilePath and botFileSecret set for your environment.`);
    console.error(`\n - The botFileSecret is available under appsettings for your Azure Bot Service bot.`);
    console.error(`\n - If you are running this bot locally, consider adding a .env file with botFilePath and botFileSecret.\n\n`);
    process.exit();
}

const DEV_ENVIRONMENT = 'development';

// Define the name of the bot, as specified in .bot file.
// See https://aka.ms/about-bot-file to learn more about .bot files.
const BOT_CONFIGURATION = (process.env.NODE_ENV || DEV_ENVIRONMENT);

// Load the configuration profile specific to this bot identity.
const endpointConfig = botConfig.findServiceByNameOrId(BOT_CONFIGURATION);

// Create the adapter. See https://aka.ms/about-bot-adapter to learn more about using information from
// the .bot file when configuring your adapter.
const adapter = new BotFrameworkAdapter({
    appId: endpointConfig.appId || process.env.MicrosoftAppId,
    appPassword: endpointConfig.appPassword || process.env.MicrosoftAppPassword
});

// Define the state store for your bot. See https://aka.ms/about-bot-state to learn more about using MemoryStorage.
// A bot requires a state storage system to persist the dialog and user state between messages.
const memoryStorage = new MemoryStorage();

// Create state manager with in-memory storage provider.
const botState = new BotState(memoryStorage, () => 'proactiveBot.botState');

// Create the main dialog, which serves as the bot's main handler.
const bot = new ProactiveBot(botState, adapter);

// Listen for incoming requests.
server.post('/api/messages', (req, res) => {
    adapter.processActivity(req, res, async (turnContext) => {
        // Route the message to the bot's main handler.
        await bot.onTurn(turnContext);
    });
});

// Catch-all for errors.
adapter.onTurnError = async (context, error) => {
    // This check writes out errors to console log .vs. app insights.
    console.error(`\n [onTurnError]: ${ error }`);
    // Send a message to the user
    context.sendActivity(`Oops. Something went wrong!`);
};
```

---

### <a name="define-the-bot"></a>Definieren des Bots

Der Benutzer kann den Bot bitten, für ihn einen Auftrag zu erstellen und auszuführen. Ein separater Auftragsdienst kann den Bot benachrichtigen, wenn ein Auftrag abgeschlossen wurde. Der Bot ist für die folgenden Aufgaben konzipiert:

- Erstellen eines Auftrags als Antwort auf die Nachricht `run` oder `run job` vom Benutzer
- Anzeigen aller registrierten Aufträge als Antwort auf die Nachricht `show` oder `show jobs` vom Benutzer
- Ausführen eines Auftrags als Antwort auf das Ereignis _Auftrag abgeschlossen_, mit dem der abgeschlossene Auftrag identifiziert wird
- Simulieren des Ereignisses „Auftrag abgeschlossen“ als Antwort auf die Nachricht `done <jobIdentifier>`
- Senden einer proaktiven Nachricht an den Benutzer über die ursprüngliche Konversation nach Abschluss des Auftrags

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Für den Bot müssen einige Aspekte berücksichtigt werden:

- Initialisierungscode
- Turn-Handler
- Methoden zum Erstellen und Ausführen der Aufträge

#### <a name="declare-the-class"></a>Deklarieren der Klasse

```csharp
namespace Microsoft.BotBuilderSamples
{
    // For each interaction from the user, an instance of this class is called.
    // This is a Transient lifetime service.  Transient lifetime services are created
    // each time they're requested. For each Activity received, a new instance of this
    // class is created. Objects that are expensive to construct, or have a lifetime
    // beyond the single Turn, should be carefully managed.
    public class ProactiveBot : IBot
    {
        // The name of events that signal that a job has completed.
        public const string JobCompleteEventName = "jobComplete";

        public const string WelcomeText = "Type 'run' or 'run job' to start a new job.\r\n" +
                                          "Type 'show' or 'show jobs' to display the job log.\r\n" +
                                          "Type 'done <jobNumber>' to complete a job.";
    }
}
```

#### <a name="add-initialization-code"></a>Hinzufügen von Initialisierungscode

```csharp
private readonly JobState _jobState;
private readonly IStatePropertyAccessor<JobLog> _jobLogPropertyAccessor;

public ProactiveBot(JobState jobState, EndpointService endpointService)
{
    _jobState = jobState ?? throw new ArgumentNullException(nameof(jobState));
    _jobLogPropertyAccessor = _jobState.CreateProperty<JobLog>(nameof(JobLog));

    // Validate AppId.
    // Note: For local testing, .bot AppId is empty for the Bot Framework Emulator.
    AppId = string.IsNullOrWhiteSpace(endpointService.AppId) ? "1" : endpointService.AppId;
}

private string AppId { get; }
```

#### <a name="add-a-turn-handler"></a>Hinzufügen eines Turn-Handlers

Jeder Bot muss einen Turn-Handler implementieren. Der Adapter leitet Aktivitäten an diese Methode weiter.

```csharp
public async Task OnTurnAsync(ITurnContext turnContext, CancellationToken cancellationToken = default(CancellationToken))
{
    if (turnContext.Activity.Type != ActivityTypes.Message)
    {
        // Handle non-message activities.
        await OnSystemActivityAsync(turnContext);
    }
    else
    {
        // Get the job log.
        // The job log is a dictionary of all outstanding jobs in the system.
        JobLog jobLog = await _jobLogPropertyAccessor.GetAsync(turnContext, () => new JobLog());

        // Get the user's text input for the message.
        var text = turnContext.Activity.Text.Trim().ToLowerInvariant();
        switch (text)
        {
            case "run":
            case "run job":

                // Start a virtual job for the user.
                JobLog.JobData job = CreateJob(turnContext, jobLog);

                // Set the new property
                await _jobLogPropertyAccessor.SetAsync(turnContext, jobLog);

                // Now save it into the JobState
                await _jobState.SaveChangesAsync(turnContext);

                await turnContext.SendActivityAsync(
                    $"We're starting job {job.TimeStamp} for you. We'll notify you when it's complete.");

                break;

            case "show":
            case "show jobs":

                // Display information for all jobs in the log.
                if (jobLog.Count > 0)
                {
                    await turnContext.SendActivityAsync(
                        "| Job number &nbsp; | Conversation ID &nbsp; | Completed |<br>" +
                        "| :--- | :---: | :---: |<br>" +
                        string.Join("<br>", jobLog.Values.Select(j =>
                            $"| {j.TimeStamp} &nbsp; | {j.Conversation.Conversation.Id.Split('|')[0]} &nbsp; | {j.Completed} |")));
                }
                else
                {
                    await turnContext.SendActivityAsync("The job log is empty.");
                }

                break;

            default:
                // Check whether this is simulating a job completed event.
                string[] parts = text?.Split(' ', StringSplitOptions.RemoveEmptyEntries);
                if (parts != null && parts.Length == 2
                    && parts[0].Equals("done", StringComparison.InvariantCultureIgnoreCase)
                    && long.TryParse(parts[1], out long jobNumber))
                {
                    if (!jobLog.TryGetValue(jobNumber, out JobLog.JobData jobInfo))
                    {
                        await turnContext.SendActivityAsync($"The log does not contain a job {jobInfo.TimeStamp}.");
                    }
                    else if (jobInfo.Completed)
                    {
                        await turnContext.SendActivityAsync($"Job {jobInfo.TimeStamp} is already complete.");
                    }
                    else
                    {
                        await turnContext.SendActivityAsync($"Completing job {jobInfo.TimeStamp}.");

                        // Send the proactive message.
                        await CompleteJobAsync(turnContext.Adapter, AppId, jobInfo);
                    }
                }

                break;
        }

        if (!turnContext.Responded)
        {
            await turnContext.SendActivityAsync(WelcomeText);
        }
    }
}

private static async Task SendWelcomeMessageAsync(ITurnContext turnContext)
{
    foreach (var member in turnContext.Activity.MembersAdded)
    {
        if (member.Id != turnContext.Activity.Recipient.Id)
        {
            await turnContext.SendActivityAsync($"Welcome to SuggestedActionsBot {member.Name}.\r\n{WelcomeText}");
        }
    }
}

// Handles non-message activities.
private async Task OnSystemActivityAsync(ITurnContext turnContext)
{
    // On a job completed event, mark the job as complete and notify the user.
    if (turnContext.Activity.Type is ActivityTypes.Event)
    {
        var jobLog = await _jobLogPropertyAccessor.GetAsync(turnContext, () => new JobLog());
        var activity = turnContext.Activity.AsEventActivity();
        if (activity.Name == JobCompleteEventName
            && activity.Value is long timestamp
            && jobLog.ContainsKey(timestamp)
            && !jobLog[timestamp].Completed)
        {
            await CompleteJobAsync(turnContext.Adapter, AppId, jobLog[timestamp]);
        }
    }
    else if (turnContext.Activity.Type is ActivityTypes.ConversationUpdate)
    {
        if (turnContext.Activity.MembersAdded.Any())
        {
            await SendWelcomeMessageAsync(turnContext);
        }
    }
}
```

#### <a name="add-job-creation-and-completion-methods"></a>Hinzufügen von Methoden für die Auftragserstellung und -ausführung

Zum Starten eines Auftrags erstellt der Bot den Auftrag und zeichnet Informationen dazu – und die aktuelle Konversation – im Auftragsprotokoll auf. Wenn der Bot in einer Konversation das Ereignis „Auftrag abgeschlossen“ empfängt, überprüft er die Auftrags-ID, bevor der Code zum Ausführen des Auftrags aufgerufen wird.

Der Code zum Ausführen des Auftrags ruft das Auftragsprotokoll aus dem Zustand ab und kennzeichnet den Auftrag dann als abgeschlossen und sendet eine proaktive Nachricht, indem die Methode zum Fortsetzen der Konversation (_continue conversation_) des Adapters verwendet wird.

- Mit dem Aufruf „continue conversation“ wird der Kanal aufgerufen, um unabhängig vom Benutzer einen Turn zu initiieren.
- Der Adapter führt den zugeordneten Rückruf anstelle des normalen OnTurn-Handlers des Bots aus. Dieser Turn verfügt über seinen eigenen Kontext, aus dem wir die Zustandsinformationen abrufen und die proaktive Nachricht an den Benutzer senden.

```csharp
// Creates and "starts" a new job.
private JobLog.JobData CreateJob(ITurnContext turnContext, JobLog jobLog)
{
    JobLog.JobData jobInfo = new JobLog.JobData
    {
        TimeStamp = DateTime.Now.ToBinary(),
        Conversation = turnContext.Activity.GetConversationReference(),
    };

    jobLog[jobInfo.TimeStamp] = jobInfo;

    return jobInfo;
}

// Sends a proactive message to the user.
private async Task CompleteJobAsync(
    BotAdapter adapter,
    string botId,
    JobLog.JobData jobInfo,
    CancellationToken cancellationToken = default(CancellationToken))
{
    await adapter.ContinueConversationAsync(botId, jobInfo.Conversation, CreateCallback(jobInfo), cancellationToken);
}

// Creates the turn logic to use for the proactive message.
private BotCallbackHandler CreateCallback(JobLog.JobData jobInfo)
{
    return async (turnContext, token) =>
    {
        // Get the job log from state, and retrieve the job.
        JobLog jobLog = await _jobLogPropertyAccessor.GetAsync(turnContext, () => new JobLog());

        // Perform bookkeeping.
        jobLog[jobInfo.TimeStamp].Completed = true;

        // Set the new property
        await _jobLogPropertyAccessor.SetAsync(turnContext, jobLog);

        // Now save it into the JobState
        await _jobState.SaveChangesAsync(turnContext);

        // Send the user a proactive confirmation message.
        await turnContext.SendActivityAsync($"Job {jobInfo.TimeStamp} is complete.");
    };
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Der Bot wird in der Datei **bot.js** definiert, und folgende Aspekte müssen berücksichtigt werden:

- Initialisierungscode
- Turn-Handler
- Methoden zum Erstellen und Ausführen der Aufträge

#### <a name="declare-the-class-and-add-initialization-code"></a>Deklarieren der Klasse und Hinzufügen des Initialisierungscodes

```javascript
const { ActivityTypes, TurnContext } = require('botbuilder');

const JOBS_LIST = 'jobs';

class ProactiveBot {
    constructor(botState, adapter) {
        this.botState = botState;
        this.adapter = adapter;

        this.jobsList = this.botState.createProperty(JOBS_LIST);
    }

    // ...
};

// Helper function to check if object is empty.
function isEmpty(obj) {
    for (var key in obj) {
        if (obj.hasOwnProperty(key)) {
            return false;
        }
    }
    return true;
};

module.exports.ProactiveBot = ProactiveBot;
```

#### <a name="the-turn-handler"></a>Turn-Handler

Die Methoden `onTurn` und `showJobs` werden in der `ProactiveBot`-Klasse definiert. Mit `onTurn` wird die Eingabe der Benutzer verarbeitet. Hiermit werden ggf. auch Ereignisaktivitäten aus dem hypothetischen System zur Auftragserfüllung empfangen. Mit `showJobs` wird das Auftragsprotokoll formatiert und gesendet.

```javascript
/**
    *
    * @param {TurnContext} turnContext A TurnContext object representing an incoming message to be handled by the bot.
    */
async onTurn(turnContext) {
    // See https://aka.ms/about-bot-activity-message to learn more about the message and other activity types.
    if (turnContext.activity.type === ActivityTypes.Message) {
        const utterance = (turnContext.activity.text || '').trim().toLowerCase();
        var jobIdNumber;

        // If user types in run, create a new job.
        if (utterance === 'run') {
            await this.createJob(turnContext);
        } else if (utterance === 'show') {
            await this.showJobs(turnContext);
        } else {
            const words = utterance.split(' ');

            // If the user types done and a Job Id Number,
            // we check if the second word input is a number.
            if (words[0] === 'done' && !isNaN(parseInt(words[1]))) {
                jobIdNumber = words[1];
                await this.completeJob(turnContext, jobIdNumber);
            } else if (words[0] === 'done' && (words.length < 2 || isNaN(parseInt(words[1])))) {
                await turnContext.sendActivity('Enter the job ID number after "done".');
            }
        }

        if (!turnContext.responded) {
            await turnContext.sendActivity(`Say "run" to start a job, or "done <job>" to complete one.`);
        }
    } else if (turnContext.activity.type === ActivityTypes.Event && turnContext.activity.name === 'jobCompleted') {
        jobIdNumber = turnContext.activity.value;
        if (!isNaN(parseInt(jobIdNumber))) {
            await this.completeJob(turnContext, jobIdNumber);
        }
    }

    await this.botState.saveChanges(turnContext);
}

// Show a list of the pending jobs
async showJobs(turnContext) {
    const jobs = await this.jobsList.get(turnContext, {});
    if (Object.keys(jobs).length) {
        await turnContext.sendActivity(
            '| Job number &nbsp; | Conversation ID &nbsp; | Completed |<br>' +
            '| :--- | :---: | :---: |<br>' +
            Object.keys(jobs).map((key) => {
                return `${ key } &nbsp; | ${ jobs[key].reference.conversation.id.split('|')[0] } &nbsp; | ${ jobs[key].completed }`;
            }).join('<br>'));
    } else {
        await turnContext.sendActivity('The job log is empty.');
    }
}
```

#### <a name="logic-to-start-a-job"></a>Logik zum Starten eines Auftrags

Die `createJob`-Methode wird in der `ProactiveBot`-Klasse definiert. Sie erstellt und protokolliert neue Aufträge für den Benutzer. Theoretisch können diese Informationen auch an das System zur Auftragserfüllung weitergeleitet werden.

```javascript
// Save job ID and conversation reference.
async createJob(turnContext) {
    // Create a unique job ID.
    var date = new Date();
    var jobIdNumber = date.getTime();

    // Get the conversation reference.
    const reference = TurnContext.getConversationReference(turnContext.activity);

    // Get the list of jobs. Default it to {} if it is empty.
    const jobs = await this.jobsList.get(turnContext, {});

    // Try to find previous information about the saved job.
    const jobInfo = jobs[jobIdNumber];

    try {
        if (isEmpty(jobInfo)) {
            // Job object is empty so we have to create it
            await turnContext.sendActivity(`Need to create new job ID: ${ jobIdNumber }`);

            // Update jobInfo with new info
            jobs[jobIdNumber] = { completed: false, reference: reference };

            try {
                // Save to storage
                await this.jobsList.set(turnContext, jobs);
                // Notify the user that the job has been processed
                await turnContext.sendActivity('Successful write to log.');
            } catch (err) {
                await turnContext.sendActivity(`Write failed: ${ err.message }`);
            }
        }
    } catch (err) {
        await turnContext.sendActivity(`Read rejected. ${ err.message }`);
    }
}
```

#### <a name="logic-to-complete-a-job"></a>Logik zum Ausführen eines Auftrags

Die `completeJob`-Methode wird in der `ProactiveBot`-Klasse definiert. Sie führt einige Buchhaltungsaufgaben aus und sendet die proaktive Nachricht mit dem Hinweis, dass der Auftrag abgeschlossen ist, an den Benutzer (in der Originalkonversation des Benutzers).

```javascript
async completeJob(turnContext, jobIdNumber) {
    // Get the list of jobs from the bot's state property accessor.
    const jobs = await this.jobsList.get(turnContext, {});

    // Find the appropriate job in the list of jobs.
    let jobInfo = jobs[jobIdNumber];

    // If no job was found, notify the user of this error state.
    if (isEmpty(jobInfo)) {
        await turnContext.sendActivity(`Sorry no job with ID ${ jobIdNumber }.`);
    } else {
        // Found a job with the ID passed in.
        const reference = jobInfo.reference;
        const completed = jobInfo.completed;

        // If the job is not yet completed and conversation reference exists,
        // use the adapter to continue the conversation with the job's original creator.
        if (reference && !completed) {
            // Since we are going to proactively send a message to the user who started the job,
            // we need to create the turnContext based on the stored reference value.
            await this.adapter.continueConversation(reference, async (proactiveTurnContext) => {
                // Complete the job.
                jobInfo.completed = true;
                // Save the updated job.
                await this.jobsList.set(turnContext, jobs);
                // Notify the user that the job is complete.
                await proactiveTurnContext.sendActivity(`Your queued job ${ jobIdNumber } just completed.`);
            });

            // Send a message to the person who completed the job.
            await turnContext.sendActivity('Job completed. Notification sent.');
        } else if (completed) { // The job has already been completed.
            await turnContext.sendActivity('This job is already completed, please start a new job.');
        };
    };
};
```

---

### <a name="test-your-bot"></a>Testen Ihres Bots

Erstellen Sie Ihren Bot lokal, und führen Sie ihn aus. Öffnen Sie anschließend zwei Emulator-Fenster.

1. Beachten Sie, dass sich die Konversations-ID in den beiden Fenstern unterscheidet.
1. Geben Sie im ersten Fenster einige Male `run` ein, um mehrere Aufträge zu starten.
1. Geben Sie im zweiten Fenster `show` ein, um eine Liste mit den Aufträgen im Protokoll anzuzeigen.
1. Geben Sie im zweiten Fenster `done <jobNumber>` ein, wobei `<jobNumber>` eine der Auftragsnummern aus dem Protokoll ist (ohne spitze Klammern). (Der Botcode ist so konzipiert, dass dies wie ein jobComplete-Ereignis interpretiert wird.)
1. Beachten Sie, dass der Bot im ersten Fenster eine proaktive Nachricht an den Benutzer sendet.

<!--TODO: Recreate the screen shots once we're happy with both the C# and JS versions of the code.-->

Ihre Konversation kann aus Sicht des Benutzers unter Umständen wie folgt aussehen:

![Emulator-Sitzung des Benutzers](~/v4sdk/media/how-to-proactive/user.png)

Aus Sicht des simulierten Auftragssystems:

![Emulator-Sitzung des Auftragssystems](~/v4sdk/media/how-to-proactive/job-system.png)

<!-- Add a next steps section. -->
