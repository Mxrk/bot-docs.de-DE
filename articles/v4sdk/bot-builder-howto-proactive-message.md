---
title: Senden von proaktiven Nachrichten | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie proaktives Messaging mit Ihrem Bot ausführen können.
keywords: Proaktive Nachricht
author: jonathanfingold
ms.author: jonathanfingold
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 05/01/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: c22ce6a35d4d49506360a78a439f15137c429d9d
ms.sourcegitcommit: 2dc75701b169d822c9499e393439161bc87639d2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/24/2018
ms.locfileid: "42905134"
---
# <a name="send-proactive-messages"></a>Senden von proaktiven Nachrichten 

[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]


Bots senden häufig _reaktive Nachrichten_, aber es gibt auch Situationen, in denen wir in der Lage sein müssen, eine [proaktive Nachricht](bot-builder-proactive-messages.md) zu senden. 

Ein häufiger Fall für proaktives Messaging tritt ein, wenn der Bot eine Aufgabe ausführt, die eine unbestimmte Zeit in Anspruch nehmen kann. In diesem Fall können Sie Informationen zur Aufgabe speichern, dem Benutzer mitteilen, dass der Bot sich wieder bei ihm meldet, wenn die Aufgabe beendet ist, und die Unterhaltung fortsetzen. Wenn die Aufgabe abgeschlossen ist, kann der Bot die Unterhaltung fortsetzen, indem er die Bestätigungsmeldung proaktiv sendet.

# <a name="ctabcs"></a>[C#](#tab/cs)

## <a name="notes-about-this-sample"></a>Anmerkungen zu diesem Beispiel

Wir ändern das grundlegende EchoBot-Beispiel.
- Wir verwenden `Microsoft.Samples.Proactive` als Namespace.
- Wir ersetzen die Zustandsdatei durch eine Datei `JobData.cs`.
- Wir ersetzen die Botdatei durch eine Datei `ProactiveBot.cs`.

> [!NOTE]
> Proaktives Messaging setzt zurzeit voraus, dass Ihr Bot über eine gültige ApplicationID und ein gültiges Kennwort verfügt.


## <a name="define-task-data"></a>Definieren von Aufgabendaten

In diesem Szenario verfolgen wir beliebige Aufgaben nach, die von verschiedenen Benutzern in verschiedenen Unterhaltungen erstellt werden können. Wir verwenden daher allgemeine Botzustandsmiddleware anstelle von Benutzer- oder Unterhaltungszustandsmiddleware.

Die folgende Klasse definiert die Datenstruktur, die wir für einzelne Aufgaben verwenden.


```csharp
using Microsoft.Bot.Schema;
using System.Collections.Generic;

namespace Microsoft.Samples.Proactive
{
    /// <summary>
    /// Class for storing job state. 
    /// </summary>
    public class JobData
    {
        /// <summary>
        /// The name to use to read and write this bot state object to storage.
        /// </summary>
        public readonly static string PropertyName = $"BotState:{typeof(Dictionary<int, JobData>).FullName}";

        public int JobNumber { get; set; } = 0;
        public bool Completed { get; set; } = false;

        /// <summary>
        /// The conversation reference to which to send status updates.
        /// </summary>
        public ConversationReference Conversation { get; set; }
    }
}
```


Wir müssen unserem Startcode außerdem Zustandsmiddleware hinzufügen.


Aktualisieren Sie in der Datei `StartUp.cs` die `ConfigureServices`-Methode, um unserem Botzustand ein Wörterbuch mit Aufträgen hinzuzufügen. Im folgenden Code ist es der letzte Aufruf von `options.Middleware.Add`.
```csharp
// This method gets called by the runtime. Use this method to add services to the container.
public void ConfigureServices(IServiceCollection services)
{
    services.AddBot<ProactiveBot>(options =>
    {
        options.CredentialProvider = new ConfigurationCredentialProvider(Configuration);

        // The CatchExceptionMiddleware provides a top-level exception handler for your bot. 
        // Any exceptions thrown by other Middleware, or by your OnTurn method, will be 
        // caught here. To facillitate debugging, the exception is sent out, via Trace, 
        // to the emulator. Trace activities are NOT displayed to users, so in addition
        // an "Ooops" message is sent. 
        options.Middleware.Add(new CatchExceptionMiddleware<Exception>(async (context, exception) =>
        {
            await context.TraceActivity($"{nameof(ProactiveBot)} Exception", exception);
            await context.SendActivity("Sorry, it looks like something went wrong!");
        }));

        // The Memory Storage used here is for local bot debugging only. When the bot
        // is restarted, anything stored in memory will be gone. 
        IStorage dataStore = new MemoryStorage();

        // Using the base BotState here, since the job log is not necessarily tied to a
        // specific user or conversation.
        options.Middleware.Add(
            new BotState<Dictionary<int, JobData>>(
                dataStore, JobData.PropertyName, (context) => $"jobs/{typeof(Dictionary<int, JobData>)}"));
    });
}
```


## <a name="update-your-bot-to-create-and-run-jobs"></a>Aktualisieren Ihres Bots zum Erstellen und Ausführen von Aufträgen

In jedem Durchgang lassen wir einen Benutzer einen Auftrag erstellen, indem `run` oder `run job` eingegeben wird.

Als Reaktion führt der Bot in diesem Durchgang die folgenden Schritte aus:
- Erstellen des Auftrags.
- Aufzeichnen von Informationen zur aktuellen Unterhaltung, damit wir die proaktive Nachricht später senden können.
- Informieren des Benutzers, dass wir seinen Auftrag starten und ihn später informieren, wenn er abgeschlossen ist.
- Starten des asynchronen Auftrags.
- Beenden des Durchgangs.

Der Auftrag, den wir starten, ist ein einfacher 5-Sekunden-Timer, der dann mit dem Senden der proaktiven Nachricht abgeschlossen wird.
- Der Aufruf der Methode zum Fortsetzen der Unterhaltung des Adapters erstellt einen neuen Durchgang, der vom Bot initiiert wird.
- Dieser Turn bzw. Durchgang weist einen eigenen [Turn-Kontext](bot-builder-concept-activity-processing.md#turn-context) auf, aus dem wir die Zustandsinformationen abrufen.
- Wir verwenden diesen Kontext, um die proaktive Nachricht an den Benutzer zu senden.



> [!NOTE]
> Die `GetAppId`-Methode ist eine Problemumgehung zum Aktivieren von proaktivem Messaging im .NET SDK.

```csharp
using Microsoft.Bot;
using Microsoft.Bot.Builder;
using Microsoft.Bot.Connector.Authentication;
using Microsoft.Bot.Schema;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Security.Claims;
using System.Security.Principal;
using System.Threading;
using System.Threading.Tasks;

namespace Microsoft.Samples.Proactive
{
    public class ProactiveBot : IBot
    {
        /// <summary>
        /// Random number generator for job numbers.
        /// </summary>
        private static Random NumberGenerator = new Random();

        /// <summary>
        /// Gets the job log from the bot state.
        /// </summary>
        /// <param name="context">The current turn context.</param>
        /// <returns>The job log.</returns>
        private static Dictionary<int, JobData> GetJobLog(ITurnContext context)
        {
            return context.Services.Get<Dictionary<int, JobData>>(JobData.PropertyName);
        }

        /// <summary>
        /// Workaround to get the bot's app ID.
        /// </summary>
        /// <param name="context">The current turn context.</param>
        /// <returns>The application ID for the bot.</returns>
        private static string GetAppId(ITurnContext context)
        {
            // The BotFrameworkAdapter sets the identity provider on the context object.
            var claimsIdentity = context.Services.Get<IIdentity>("BotIdentity") as ClaimsIdentity;

            // For requests from a channel, the app ID is in the Audience claim of the JWT token.
            // For requests from the emulator, it is in the AppId claim.
            // For unauthenticated requests, we have anonymouse identity provided auth is disabled.
            // For Activities coming from Emulator AppId claim contains the Bot's AAD AppId.
            var botAppIdClaim =
                (claimsIdentity.Claims?.SingleOrDefault(claim => claim.Type == AuthenticationConstants.AudienceClaim)
                ?? claimsIdentity.Claims?.SingleOrDefault(claim => claim.Type == AuthenticationConstants.AppIdClaim));

            return botAppIdClaim?.Value;
        }

        /// <summary>
        /// Every Conversation turn calls this method.
        /// When the user types "run" or "run job", the bot starts a "job".
        /// When the job finishes, the bot proactively notifies the user.
        /// </summary>
        /// <param name="context">The turn context.</param>
        /// <remarks>When our virtual job finishes, it sends a proactive message
        /// to notify the user that the job completed.</remarks>
        public async Task OnTurn(ITurnContext context)
        {
            // This bot is only handling Messages
            if (context.Activity.Type is ActivityTypes.Message)
            {
                var text = context.Activity.AsMessageActivity()?.Text?.Trim().ToLower();
                switch (text)
                {
                    case "run":
                    case "run job":

                        var jobLog = GetJobLog(context);
                        var job = CreateJob(context, jobLog);
                        var appId = GetAppId(context);
                        var conversation = TurnContext.GetConversationReference(context.Activity);

                        await context.SendActivity($"We're starting job {job.JobNumber} for you. We'll notify you when it's complete.");

                        // Since the context is disposed at the end of the turn, extract and send the
                        // information we need to send the proactive message later.
                        var adapter = context.Adapter;
                        Task.Run(() =>
                        {
                            // Simulate a separate process to complete the user's job.
                            Thread.Sleep(5000);

                            // Perform bookkeeping and send the proactive message.
                            CompleteJob(adapter, appId, conversation, job.JobNumber);
                        });

                        break;

                    default:

                        await context.SendActivity("Type 'run' or 'run job' to start a new job.");

                        break;
                }
            }
        }

        /// <summary>
        /// Creates a simulated job and updates the job log.
        /// </summary>
        /// <param name="context">The current turn context.</param>
        /// <param name="jobLog">The job log.</param>
        /// <returns>A new job.</returns>
        private JobData CreateJob(ITurnContext context, Dictionary<int, JobData> jobLog)
        {
            // Generate a non-duplicate job number;
            int number;
            while (jobLog.ContainsKey(number = NumberGenerator.Next())) { }

            // Simulate creaing the job and logging it.
            var job = new JobData
            {
                JobNumber = number,
                Conversation = TurnContext.GetConversationReference(context.Activity)
            };
            jobLog.Add(job.JobNumber, job);

            // Return the created job.
            return job;
        }

        /// <summary>
        /// Performs bookkeeping and proactively notifies the user that their job completed.
        /// </summary>
        /// <param name="adapter">The bot adapter with which to send the message.</param>
        /// <param name="appId">The app ID of the bot to send the message from.</param>
        /// <param name="conversation">The conversation in which to put the message.</param>
        /// <param name="jobNumber">The number of the job that completed.</param>
        private async void CompleteJob(BotAdapter adapter, string appId, ConversationReference conversation, int jobNumber)
        {
            await adapter.ContinueConversation(appId, conversation, async context =>
            {
                // Get the job log from state, and retrieve the job.
                var jobLog = GetJobLog(context);
                var job = jobLog[jobNumber];

                // Perform bookkeeping.
                job.Completed = true;

                // Send the user a proactive confirmation message.
                await context.SendActivity($"Job {job.JobNumber} is complete.");
            });
        }
    }
}
```

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)

Bevor Sie eine proaktive Nachricht an einen Benutzer senden können, muss der Benutzer mindestens eine reaktive Nachricht an Ihren Bot gesendet haben. 

Sie müssen eine Nachricht an den Bot senden, da er einen Verweis auf das Aktivitätsobjekt erhalten und es für die zukünftige Verwendung speichern muss. Sie können sich das Aktivitätsobjekt als die Adresse des Benutzers vorstellen, da es Informationen zum Kanal enthält, über den es empfangen wurde. Außerdem ist die Benutzer-ID, die Unterhaltungs-ID und sogar der Server enthalten, der alle zukünftigen Nachrichten empfangen soll. Dieses Objekt ist einfacher JSON-Code und sollte als Ganzes ohne Manipulationen gespeichert werden.

Beginnen wir mit einem kurzen Codeausschnitt, der zeigt, wie der Unterhaltungsverweis jedes Mal gespeichert wird, wenn der Benutzer „Abonnieren“ sagt:
```javascript
const { MemoryStorage } = require('botbuilder');

const storage = new MemoryStorage();

// Listen for incoming activity 
server.post('/api/messages', (req, res) => {
    // Route received activity to adapter for processing
    adapter.processActivity(req, res, async (context) => {
        if (context.activity.type === 'message') {
            const utterances = (context.activity.text || '').trim().toLowerCase()
            if (utterances === 'subscribe') {
                var userId = await saveReference(TurnContext.getConversationReference(context.activity));
                await subscribeUser(userId)
                await context.sendActivity(`Thank You! We will message you shortly.`);
               
            } else{
                await context.sendActivity("Say 'subscribe' to start proactive message");
            }
    
        }
    });
});
```
Der Codeausschnitte oben ruft die `saveReference()`-Funktion auf, die den Verweis des Benutzers mit `MemoryStorage` speichert, und gibt `userId` zurück. Sobald der Verweis erfolgreich gespeichert wurde, rufen wir `subscribeUser()` auf, um den Benutzer darüber zu informieren, dass er abonniert wurde. 

Die `subscribeUser()`-Funktion richtet das eigentliche Abonnement ein. Werfen wir einen Blick auf eine einfache Implementierung, die einen 2-Sekunden-Timer startet und dem Benutzer nach Ablauf des Timers proaktiv eine Nachricht sendet:

```javascript
// Persist info to storage
async function saveReference(reference){
    const userId = reference.activityId
    const changes = {};
    changes['reference/' + userId] = reference;
    await storage.write(changes); // Write reference info to persisted storage
    return userId;
}

// Subscribe user to a proactive call. In this case, we are using a setTimeOut() to trigger the proactive call
async function subscribeUser(userId) {
    setTimeout(async () => {
        const reference = await findReference(userId);
        if (reference) {
            await adapter.continueConversation(reference, async (context) => {
                await context.sendActivity("You have been notified");
            });
            
        }
    }, 2000); // Trigger after 2 secs
}

// Read the stored reference info from storage
async function findReference(userId){
    const referenceKey = 'reference/' + userId;
    var rows = await storage.read([referenceKey])
    var reference = await rows[referenceKey]

    return reference;
}
```

Die `subscribeUser()`-Funktion richtet einen Timer ein, der das Verweisobjekt ermittelt, indem er es aus dem Speicher liest. Wenn das Verweisobjekt gefunden wurde, können wir die Unterhaltung mit dem Benutzer fortsetzen. Die `continueConversation`-Methode ermöglicht dem Bot das proaktive Senden von Nachrichten an eine Unterhaltung oder einen Benutzer, mit der bzw. dem er bereits kommuniziert hat.

---

## <a name="test-your-bot"></a>Testen Ihres Bots

Um Ihren Bot zu testen, stellen Sie ihn in Azure als reinen Registrierungsbot bereit, und testen Sie ihn in Webchat, oder testen Sie ihn lokal mit dem Emulator.
