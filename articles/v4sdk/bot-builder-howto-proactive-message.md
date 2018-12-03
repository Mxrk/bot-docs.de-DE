---
title: Abrufen von Benachrichtigungen aus Bots | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Benachrichtigungsnachrichten senden.
keywords: proaktive Nachricht, Benachrichtigungsnachricht, Botbenachrichtigung
author: jonathanfingold
ms.author: jonathanfingold
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 11/15/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 82811d202e0e20169ae2ebb348949366009d2421
ms.sourcegitcommit: 4661b9bb31d74731dbbb16e625be088b44ba5899
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2018
ms.locfileid: "51826927"
---
# <a name="get-notification-from-bots"></a>Abrufen von Benachrichtigungen aus Bots

[!INCLUDE [pre-release-label](~/includes/pre-release-label.md)]

In der Regel steht jede Nachricht, die ein Bot an den Benutzer sendet, im direkten Zusammenhang mit der vorherigen Eingabe des Benutzers.
In einigen Fällen muss ein Bot dem Benutzer eine Nachricht senden, die nicht im direkten Zusammenhang mit dem aktuellen Thema der Unterhaltung oder mit der letzten vom Benutzer gesendeten Nachricht steht. Diese Art von Nachrichten werden als _proaktive Nachrichten_ bezeichnet.

## <a name="proactive-messages"></a>Proaktive Nachrichten

Proaktive Nachrichten können in einer Vielzahl von Szenarien nützlich sein. Wenn ein Bot einen Timer oder eine Erinnerung festlegt, muss er den Benutzer benachrichtigen, wenn dieser Zeitpunkt erreicht ist. Auch in dem Fall, dass ein Bot eine Benachrichtigung von einem externen System empfängt, muss er möglicherweise diese Informationen umgehend an den Benutzer weitergeben. Wenn der Benutzer den Bot vorher aufgefordert hat, den Preis eines Produkts zu überwachen, kann der Bot den Benutzer warnen, wenn der Preis für das Produkt um 20 % gesunken ist. Ein anderes Beispiel: Wenn ein Bot einen gewissen Zeitraum zum Kompilieren einer Antwort auf die Frage des Benutzers benötigt, kann er den Benutzer über die Verzögerung informieren und die Unterhaltung in der Zwischenzeit fortfahren lassen. Wenn der Bot die Kompilierung der Antwort auf die Frage abgeschlossen hat, teilt er dies dem Benutzer mit.

Beachten Sie bei der Implementierung proaktiver Nachrichten in Ihrem Bot Folgendes:

- Senden Sie nicht mehrere proaktive Nachrichten innerhalb kurzer Zeit. Einige Kanäle erzwingen Einschränkungen darüber, wie oft ein Bot Nachrichten an den Benutzer senden kann, und deaktivieren den Bot, wenn er gegen diese Einschränkungen verstößt.
- Senden Sie keine proaktiven Nachrichten an Benutzer, die vorher nicht mit dem Bot interagiert haben oder auf andere Weise, z.B. per E-Mail oder SMS, Kontakt mit dem Bot angefordert haben.

Eine proaktive Ad-hoc-Nachricht ist der einfachste Typ von proaktiven Nachrichten. Der Bot streut die Nachricht einfach immer dann in die Konversation ein, wenn diese ausgelöst wird, ohne Rücksicht darauf, ob der Benutzer derzeit in einer anderen Konversation mit dem Bot aktiv ist und wird nicht versuchen, die Unterhaltung zu verändern.

Ziehen Sie zur reibungsloseren Verarbeitung von Benachrichtigungen andere Möglichkeiten zum Integrieren der Benachrichtigung in den Konversationsablauf in Betracht. Legen Sie beispielsweise ein Flag im Konversationsstatus fest, oder fügen Sie die Benachrichtigung zu einer Warteschlange hinzu.

## <a name="prerequisites"></a>Voraussetzungen
- Sie müssen mit den [Bot-Grundlagen](bot-builder-basics.md) vertraut sein. 
- Eine Kopie des **Beispiels für proaktive Nachrichten** in [C#](https://aka.ms/proactive-sample-cs) oder [JS](https://aka.ms/proactive-sample-js). Anhand dieses Beispiels werden in diesem Artikel proaktive Nachrichten beschrieben. 

## <a name="about-the-sample-code"></a>Informationen zum Beispielcode

Im Beispiel für proaktive Nachrichten werden Benutzeraufgaben modelliert, die eine unbestimmte Dauer aufweisen können. Der Bot speichert Informationen zur Aufgabe, teilt dem Benutzer mit, dass der Bot sich nach Beendigung der Aufgabe wieder bei ihm meldet, und setzt die Konversation fort. Nachdem die Aufgabe abgeschlossen ist, sendet der Bot die Bestätigungsnachricht proaktiv in der Originalkonversation.

## <a name="define-job-data-and-state"></a>Definieren der Auftragsdaten und des Zustands

In diesem Szenario verfolgen wir einige Aufträge nach, die von verschiedenen Benutzern in verschiedenen Konversationen erstellt werden können. Wir müssen Informationen zu den einzelnen Aufträgen speichern, z.B. den Konversationsverweis und eine Auftrags-ID. Dazu benötigen wir Folgendes:
- Den Konversationsverweis, damit wir die proaktive Nachricht an die richtige Konversation senden können.
- Eine Möglichkeit zum Identifizieren von Aufträgen. In diesem Beispiel verwenden wir einen einfachen Zeitstempel.
- Eine Möglichkeit, den Auftragszustand unabhängig vom Konversations- oder Benutzerzustand zu speichern.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Wir müssen Klassen für die Auftragsdaten und den Auftragszustand definieren. Außerdem müssen wir unseren Bot registrieren und einen Zustandseigenschaftenaccessor für das Auftragsprotokoll einrichten.

### <a name="define-a-class-for-job-data"></a>Definieren einer Klasse für Auftragsdaten

Die `JobLog`-Klasse verfolgt Auftragsdaten nach, die anhand der Auftragsnummer indiziert sind (Zeitstempel). Die `JobLog`-Klasse verfolgt alle ausstehenden Aufträge nach.  Jeder Auftrag wird durch einen eindeutigen Schlüssel identifiziert. `JobData` beschreibt den Zustand eines Auftrags und ist als eine innere Klasse eines Wörterbuchs definiert.

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

### <a name="define-a-state-middleware-class"></a>Definieren einer Middleware-Klasse für den Zustand

Mit der `JobState`-Klasse wird der Auftragszustand unabhängig vom Konversations- oder Benutzerzustand verwaltet.

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

Die `ConfigureServices`-Methode registriert den Bot und den Endpunktdienst, einschließlich Fehlerbehandlung und Zustandsverwaltung. Außerdem wird hiermit der Auftragszustandsaccessor registriert.

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

    // ...      
    }
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Für einen Bot wird ein System zum Speichern des Zustands benötigt, um den Dialog- und Benutzerzustand zwischen Nachrichten beibehalten zu können. Hier wird dies mit einem In-Memory-Speicheranbieter definiert. 

```javascript
// index.js 


const memoryStorage = new MemoryStorage();
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

// ...
```

---

## <a name="define-the-bot"></a>Definieren des Bots

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

### <a name="declare-the-class"></a>Deklarieren der Klasse

Für jede Interaktion des Benutzers wird eine Instanz der `ProactiveBot`-Klasse erstellt. Der Prozess zur jeweiligen Erstellung eines Diensts, sofern dies erforderlich ist, wird als Dienst für vorübergehende Lebensdauer bezeichnet. Objekte, deren Erstellung aufwändig ist oder deren Lebensdauer über einen einzelnen Durchlauf hinausreicht, sollten sorgfältig verwaltet werden.

Für jede Interaktion des Benutzers wird eine Instanz der `ProactiveBot`-Klasse erstellt. Der Prozess zur jeweiligen Erstellung eines Diensts, sofern dies erforderlich ist, wird als Dienst für vorübergehende Lebensdauer bezeichnet. Objekte, deren Erstellung aufwändig ist oder deren Lebensdauer über einen einzelnen Durchlauf hinausreicht, sollten sorgfältig verwaltet werden.

```csharp
namespace Microsoft.BotBuilderSamples
{
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

### <a name="add-initialization-code"></a>Hinzufügen von Initialisierungscode

```csharp
private readonly JobState _jobState;
private readonly IStatePropertyAccessor<JobLog> _jobLogPropertyAccessor;

public ProactiveBot(JobState jobState, EndpointService endpointService)
{
    _jobState = jobState ?? throw new ArgumentNullException(nameof(jobState));
    _jobLogPropertyAccessor = _jobState.CreateProperty<JobLog>(nameof(JobLog));

    //...
}

```

### <a name="add-a-turn-handler"></a>Hinzufügen eines Turn-Handlers

Der Adapter leitet Aktivitäten an den Turn-Handler weiter, der dann den `Activity`-Typ untersucht und die richtige Methode aufruft. Jeder Bot muss einen Turn-Handler implementieren.

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
                // ...
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
```

### <a name="handle-non-message-activities"></a>Behandeln von Aktivitäten ohne Nachrichten

Kennzeichnen Sie den Auftrag beim Ereignis „Auftrag abgeschlossen“ als abgeschlossen, und benachrichtigen Sie den Benutzer.

```csharp
private async Task OnSystemActivityAsync(ITurnContext turnContext)
{
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

### <a name="add-job-creation-and-completion-methods"></a>Hinzufügen von Methoden für die Auftragserstellung und -ausführung

Zum Starten eines Auftrags erstellt der Bot den Auftrag und zeichnet Informationen dazu – und die aktuelle Konversation – im Auftragsprotokoll auf. Wenn der Bot in einer Konversation das Ereignis „Auftrag abgeschlossen“ empfängt, überprüft er die Auftrags-ID, bevor der Code zum Ausführen des Auftrags aufgerufen wird.

Der Code zum Ausführen des Auftrags ruft das Auftragsprotokoll aus dem Zustand ab und kennzeichnet den Auftrag dann als abgeschlossen und sendet eine proaktive Nachricht, indem die Methode zum Fortsetzen der Konversation (`ContinueConversationAsync`) des Adapters verwendet wird.

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
```

### <a name="sends-a-proactive-message-to-the-user"></a>Senden einer proaktiven Nachricht an den Benutzer

```csharp
private async Task CompleteJobAsync(
    BotAdapter adapter,
    string botId,
    JobLog.JobData jobInfo,
    CancellationToken cancellationToken = default(CancellationToken))
{
    await adapter.ContinueConversationAsync(botId, jobInfo.Conversation, CreateCallback(jobInfo), cancellationToken);
}
```

### <a name="creates-the-turn-logic-to-use-for-the-proactive-message"></a>Erstellen der Turn-Logik für die proaktive Nachricht

```csharp
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

### <a name="declare-the-class-and-add-initialization-code"></a>Deklarieren der Klasse und Hinzufügen des Initialisierungscodes

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

### <a name="the-turn-handler"></a>Turn-Handler

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

### <a name="logic-to-start-a-job"></a>Logik zum Starten eines Auftrags

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

### <a name="logic-to-complete-a-job"></a>Logik zum Ausführen eines Auftrags

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

## <a name="test-your-bot"></a>Testen Ihres Bots

Erstellen Sie Ihren Bot lokal, und führen Sie ihn aus. Öffnen Sie anschließend zwei Emulator-Fenster. Eine Schritt-für-Schritt-Anleitung finden Sie in der [INFODATEI](https://github.com/Microsoft/BotBuilder-Samples/blob/master/samples/csharp_dotnetcore/16.proactive-messages/README.md).

1. Beachten Sie, dass sich die Konversations-ID in den beiden Fenstern unterscheidet.
1. Geben Sie im ersten Fenster einige Male `run` ein, um mehrere Aufträge zu starten.
1. Geben Sie im zweiten Fenster `show` ein, um eine Liste mit den Aufträgen im Protokoll anzuzeigen.
1. Geben Sie im zweiten Fenster `done <jobNumber>` ein, wobei `<jobNumber>` eine der Auftragsnummern aus dem Protokoll ist (ohne spitze Klammern). (Der Botcode ist so konzipiert, dass dies wie ein jobComplete-Ereignis interpretiert wird.)
1. Beachten Sie, dass der Bot im ersten Fenster eine proaktive Nachricht an den Benutzer sendet.

Ihre Konversation kann aus Sicht des Benutzers unter Umständen wie folgt aussehen:

![Emulator-Sitzung des Benutzers](~/v4sdk/media/how-to-proactive/user.png)

Aus Sicht des simulierten Auftragssystems:

![Emulator-Sitzung des Auftragssystems](~/v4sdk/media/how-to-proactive/job-system.png)

## <a name="additional-resources"></a>Zusätzliche Ressourcen
Sehen Sie sich weitere Beispiele für das C#- und JS-Format auf [GitHub](https://github.com/Microsoft/BotBuilder-Samples/blob/master/readme.md) an.
