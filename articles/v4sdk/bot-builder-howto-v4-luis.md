---
title: Verwenden von LUIS für Language Understanding | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie LUIS zum Verstehen natürlicher Sprache mit dem Bot Builder SDK verwenden können.
keywords: Language Understanding, LUIS, absicht, erkennung, entitäten, middleware
author: ivorb
ms.author: v-demak
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 4/30/17
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 88651a2c86698d55e3a429d7ea62662976d2115f
ms.sourcegitcommit: 2dc75701b169d822c9499e393439161bc87639d2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/24/2018
ms.locfileid: "42906164"
---
# <a name="using-luis-for-language-understanding"></a>Verwenden von LUIS für Language Understanding

[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

Die Fähigkeit zu verstehen, was Ihr Benutzer in einer Konversation und im Kontext meint, kann eine schwierige Aufgabe sein, kann Ihrem Bot aber ein natürlicheres Sprachgefühl geben. Language Understanding, genannt LUIS, ermöglicht es Ihnen, genau das zu tun, sodass Ihr Bot die Absicht von Benutzernachrichten bestimmen, mehr natürliche Sprache von Ihrem Benutzer erkennen und den Konversationsablauf besser steuern kann. Weitere Hintergrundinformationen zur Integration von LUIS in einen Bot finden Sie unter [Language Understanding für Bots](./bot-builder-concept-LUIS.md). 

Dieses Thema behandelt die Einrichtung einfacher Bots, die LUIS verwenden, um verschiedene Absichten zu erkennen.

## <a name="installing-packages"></a>Installieren von Paketen

Stellen Sie zunächst sicher, dass Sie über die erforderlichen Pakete für LUIS verfügen.

# <a name="ctabcs"></a>[C#](#tab/cs)

[Fügen Sie einen Verweis](https://docs.microsoft.com/en-us/nuget/tools/package-manager-ui) auf die v4-Vorabversion der folgenden NuGet-Pakete hinzu:


* `Microsoft.Bot.Builder.Ai.LUIS`

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)

Sie können einen Verweis auf den Bot Builder und das Botbuilder-ai-Paket in Ihrem Projekt über npm hinzufügen:

* `npm install --save botbuilder@preview`
* `npm install --save botbuilder-ai@preview`

---


## <a name="set-up-middleware-to-use-your-luis-app"></a>Einrichten der Middleware zur Verwendung Ihrer LUIS-App

Richten Sie zuerst eine _LUIS-App_ ein. Dies ist ein Dienst, den Sie auf [www.luis.ai](https://www.luis.ai) erstellen. Diese LUIS-App kann hinsichtlich bestimmter Absichten trainiert werden, die sie erkennen soll. Informationen zum Erstellen einer LUIS-App finden Sie auf der [LUIS-Website](https://www.luis.ai).

Für dieses Beispiel verwenden Sie einfach eine Demo-LUIS-App, die die Absichten „Help“ (Hilfe), „Cancel“ (Abbrechen) und „Weather“ (Wetter) erkennen kann; die App-ID ist bereits im Beispielcode enthalten. Sie benötigen einen Cognitive Services-Schlüssel. Diesen erhalten Sie, indem Sie sich auf [www.luis.ai](https://www.luis.ai) anmelden und den Schlüssel unter **Benutzereinstellungen** > **Schlüssel erstellen** kopieren.

> [!NOTE] 
> Um eine eigene Kopie der in diesem Beispiel verwendeten öffentlichen LUIS-App zu erstellen, kopieren Sie die [FirstSimpleBotExample.json](https://github.com/Microsoft/LUIS-Samples/blob/master/examples/simple-bot-example/FirstSimpleBotExample.json)-LUIS-Datei. Dann [importieren Sie die LUIS-App](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/create-new-app#import-new-app), [trainieren](https://docs.microsoft.com/en-us/azure/cognitive-services/LUIS/luis-how-to-train) und [veröffentlichen](https://docs.microsoft.com/en-us/azure/cognitive-services/LUIS/publishapp) sie. Ersetzen Sie die öffentlichen App-ID im Beispielcode durch die App-ID Ihrer neue LUIS-App.

Konfigurieren Sie Ihren Bot so, dass er Ihre LUIS-App für jede von einem Benutzer empfangene Nachricht aufruft, indem Sie sie einfach zum Middlewarestapel Ihres Bot hinzufügen. Die Middleware speichert die Ergebnisse der Erkennung im Kontextobjekt und kann dann von Ihrer Botlogik angesprochen werden.

# <a name="ctabcs"></a>[C#](#tab/cs)
Starten Sie mit der Echo-Bot-Vorlage, und öffnen Sie **Startup.cs**. 

Fügen Sie eine `using` -Anweisung für `Microsoft.Bot.Builder.Ai.LUIS` hinzu.

```csharp
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Bot.Builder.BotFramework;
using Microsoft.Bot.Builder.Integration.AspNet.Core;
// add this
using Microsoft.Bot.Builder.Ai.LUIS;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Logging;
using System;
```

Aktualisieren Sie die `ConfigureServices`-Methode in Ihre `Startup.cs`-Datei, um ein `LuisRecognizerMiddleware`-Objekt hinzuzufügen, das sich mit Ihrer LUIS-App verbindet. 

```csharp
// This method gets called by the runtime. Use this method to add services to the container.
public void ConfigureServices(IServiceCollection services)
{
    services.AddBot<EchoBot>(options =>
    {
        options.CredentialProvider = new ConfigurationCredentialProvider(Configuration);
. 
        options.Middleware.Add(new CatchExceptionMiddleware<Exception>(async (context, exception) =>
        {
            await context.TraceActivity("EchoBot Exception", exception);
            await context.SendActivity("Sorry, it looks like something went wrong!");
        }));

        // The Memory Storage used here is for local bot debugging only. When the bot
        // is restarted, anything stored in memory will be gone. 
        IStorage dataStore = new MemoryStorage();

        options.Middleware.Add(new ConversationState<EchoState>(dataStore));

        // Add LUIS recognizer as middleware
        options.Middleware.Add(
            new LuisRecognizerMiddleware(
                new LuisModel(
                    // This appID is for a public app that's made available for demo purposes
                    "eb0bf5e0-b468-421b-9375-fdfb644c512e",
                    // You can use it by replacing <subscriptionKey> with your Authoring Key
                    // which you can find at https://www.luis.ai under User settings > Authoring Key
                    "<subscriptionKey>",
                    // The location-based URL begins with "https://<region>.api.cognitive.microsoft.com", where region is the region associated with the key you are using. Some examples of regions are `westus`, `westcentralus`, `eastus2`, and `southeastasia`.
                    new Uri("https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/"))));
    });
}
```

<!--TODO: DOES PUBLIC APP WORK WITH KEYS IN DIFFERENT REGIIONS? --> Fügen Sie den Abonnementschlüssel von [https://www.luis.ai](https://www.luis.ai) anstelle von `<subscriptionKey>` ein.

> [!NOTE] 
> Wenn Sie Ihre eigene LUIS-App anstelle der öffentlichen verwenden, können Sie die ID, den Abonnementschlüssel und die URL für Ihre LUIS-App von [https://www.luis.ai](https://www.luis.ai) beziehen. 
>
>Sie können die zu verwendende Basis-URL in Ihrem `LuisModel` finden, indem Sie sich auf der [LUIS-Website](https://www.luis.ai) anmelden, zur Registerkarte **Veröffentlichen** navigieren und sich die Spalte **Endpunkt** unter **Ressourcen und Schlüssel** ansehen. Die Basis-URL ist der Teil der **Endpunkt-URL** vor der Abonnement-ID und anderen Parametern.

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)

Zuerst muss der Import in die [LuisRecognizer](https://github.com/Microsoft/botbuilder-js/tree/master/doc/botbuilder-ai/classes/botbuilder_ai.luisrecognizer.md)-Klasse erfolgen; anschließend erstellen Sie eine Instanz für das LUIS-Modell:

```javascript
const { LuisRecognizer } = require('botbuilder-ai');

const model = new LuisRecognizer({
    // This appID is for a public app that's made available for demo purposes
    // You can use it by providing your LUIS subscription key
     appId: 'eb0bf5e0-b468-421b-9375-fdfb644c512e',
    // replace subscriptionKey with your Authoring Key
    // your key is at https://www.luis.ai under User settings > Authoring Key 
    subscriptionKey: '<your subscription key>',
    // The serviceEndpoint URL begins with "https://<region>.api.cognitive.microsoft.com", where region is the region associated with the key you are using. Some examples of regions are `westus`, `westcentralus`, `eastus2`, and `southeastasia`.
    serviceEndpoint: 'https://westus.api.cognitive.microsoft.com'
});
```

Fügen Sie das Modell zum Middlewarestapel hinzu:

```javascript
adapter.use(model);
```


> [!NOTE] 
> Wenn Sie Ihre eigene LUIS-App anstelle der öffentlichen verwenden, können Sie die ID, die Abonnement-ID und die URL für Ihre LUIS-App von [https://www.luis.ai](https://www.luis.ai) beziehen. 

---



LUIS Language Understanding ist jetzt mit Ihrem Bot verbunden. Als nächstes schauen wir uns an, wie wir die Absicht aus dem LUIS-Modell abrufen, das auf dem Kontextobjekt gespeichert ist.


## <a name="get-the-intent-from-the-turn-context"></a>Abrufen der Absicht aus dem Durchlaufkontext

Die Ergebnisse von LUIS sind dann von Ihrem Bot aus anhand des Kontextes bei jedem Konversationsdurchlauf zugänglich.

# <a name="ctabcs"></a>[C#](#tab/cs)

Damit Ihr Bot basierend auf der von der LUIS-App erkannten Absicht einfach eine Antwort sendet, ersetzen Sie den Code in `OnTurn` durch:

```cs
using System.Threading.Tasks;
using Microsoft.Bot;
using Microsoft.Bot.Builder;
using Microsoft.Bot.Builder.Ai.LUIS;
using Microsoft.Bot.Builder.Core.Extensions;
using Microsoft.Bot.Schema;

namespace Bot_Builder_Echo_Bot1
{
    public class EchoBot : IBot
    {
        /// <summary>
        /// Every Conversation turn for our EchoBot will call this method. In here
        /// the bot checks the Activty type to verify it's a message, checks the /// intent from the LUIS recognizer, and sends a reply based on the recognized intent
        /// </summary>
        /// <param name="context">Turn-scoped context containing all the data needed
        /// for processing this conversation turn. </param>        
        public async Task OnTurn(ITurnContext context)
        {
            // This bot is only handling Messages
            if (context.Activity.Type == ActivityTypes.Message)
            {

                var result = context.Services.Get<RecognizerResult>
                    (LuisRecognizerMiddleware.LuisRecognizerResultKey);
                var topIntent = result?.GetTopScoringIntent();
                switch ((topIntent != null) ? topIntent.Value.intent : null)
                {
                    case null:
                        await context.SendActivity("Failed to get results from LUIS.");
                        break;
                    case "None":
                        await context.SendActivity("Sorry, I don't understand.");
                        break;
                    case "Help":
                        await context.SendActivity("<here's some help>");
                        break;
                    case "Cancel":
                        // Cancel the process.
                        await context.SendActivity("<cancelling the process>");
                        break;
                    case "Weather":
                        // Cancel the process.
                        await context.SendActivity("The weather today is sunny.");
                        break;
                    default:
                        // Received an intent we didn't expect, so send its name and score.
                        await context.SendActivity($"Intent: {topIntent.Value.intent} ({topIntent.Value.score}).");
                        break;
                }
            }
        }
    }    
}

```

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)

```javascript
// Listen for incoming activity 
server.post('/api/messages', (req, res) => {
    // Route received activity to adapter for processing
    adapter.processActivity(req, res, async (context) => {
        if (context.activity.type === 'message') {
            const results = model.get(context);
            const topIntent = LuisRecognizer.topIntent(results);
            switch (topIntent) {

                case 'Cancel':
                    await context.sendActivity("<cancelling the process>")
                    break;
                case 'Help':
                    await context.sendActivity("<here's some help>");
                    break;
                case 'Weather':
                    await context.sendActivity("The weather today is sunny.");
                    break;
                case 'None':                    
                    await context.sendActivity("Sorry, I don't understand.")
                    break;
                case 'null':                    
                    await context.sendActivity("Failed to get results from LUIS.")
                    break;
                default:
                    // Received an intent we didn't expect, so send its name and score.
                    await context.sendActivity(`The top intent was ${topIntent}`);
            }
        }
    });
});
```

Alle in der Äußerung erkannten Absichten werden als Zuordnung von Absichtsbezeichnungen zu den Ergebnissen zurückgegeben und können von `results.intents` aus aufgerufen werden. Es wird eine statische `LuisRecognizer.topIntent()`-Methode wird zur Verfügung, um die Suche nach der Absicht mit der besten Bewertung für eine Ergebnismenge zu vereinfachen.
Alle erkannten Entitäten werden als Zuordnung von Entitätsbezeichnungen an Werte zurückgegeben und über `results.entities` aufgerufen. Zusätzliche Entitätsmetadaten können durch Übergabe einer `verbose=true`-Einstellung beim Erstellen des LuisRecognizers zurückgegeben werden. Die hinzugefügte Metadaten können über `results.entities.$instance` aufgerufen werden.

---

<!-- TODO: SHOW RUNNING THE FIRST BOT --> Versuchen Sie, den Bot im Bot Framework-Emulator auszuführen, und sagen Sie etwas wie „Weather“ (Wetter), „Help“ (Hilfe) oder „Abbrechen“.

![Ausführen des Bots](./media/how-to-luis/run-luis-bot.png)


## <a name="using-a-luis-recognizer-with-conversation-state"></a>Verwenden einer LUIS-Erkennung mit Konversationszustand
<!-- TBD, complete example --> Wenn Ihre Antwort an den Benutzer mehr als einen Durchlauf hat, können Sie entscheiden zu verfolgen, an welcher Stelle Sie sich in der Konversation befinden, indem Sie den Konversationszustand speichern. Sie können die Absicht aus LUIS verwenden, um Konversationszustandsdaten zu bestimmen, z.B. ob ein Konversationsthema gestartet oder beendet wurde.

Die Middleware der LUIS-Erkennung wird für jeden Durchlauf Ihres Bots ausgeführt, sodass Sie für jede von Ihrem Benutzer empfangene Nachricht eine Absicht abrufen können. Wenn Sie einen Konversationsablauf mit mehreren Durchläufen basierend auf einer Absicht starten möchten, besteht eine Möglichkeit darin, die Logik für das Ändern von Themen zu überspringen, bis Sie das aktuelle Thema abgeschlossen haben.

# <a name="ctabcs"></a>[C#](#tab/cs)

Ändern Sie EchoState in der EchoState.cs-Datei wie folgt:

```csharp
public class ConversationStateInfo 
{
    public bool WeatherTopicStarted  { get; set; }
    public bool HelpTopicStarted  { get; set; }
    public bool CancelTopicStarted  { get; set; }
}
```

Ändern Sie in der Startup.cs-Datei die Initialisierung von ConversationState, um `ConversationStateInfo` zu verwenden.
```cs
    options.Middleware.Add(new ConversationState<ConversationStateInfo>(dataStore));
```

Bearbeiten Sie in der EchoBot.cs-Datei `OnTurn`:
```cs
public async Task OnTurn(ITurnContext context)
{
    // This bot is only handling Messages
    if (context.Activity.Type == ActivityTypes.Message)
    {
        var text = context.Activity.Text;
        var conversationState = context.GetConversationState<ConversationStateInfo>() ?? new ConversationStateInfo();

        // Here, you can add some other logic based on the topic flags in conversation state
        // For example, if you know that a particular topic was started in a previous turn,
        // you can send the reply for that topic and bypass getting the intent from LUIS 
        if (conversationState.WeatherTopicStarted)
        {
            // Set this flag to false since this reply concludes the topic.
            conversationState.WeatherTopicStarted = false;
            // Assume that they responded to the prompt with a location.
            await context.SendActivity($"The weather in {text} is sunny.");
        }
        else
        {
            var result = context.Services.Get<RecognizerResult>
            (LuisRecognizerMiddleware.LuisRecognizerResultKey);
            var topIntent = result?.GetTopScoringIntent();
            switch ((topIntent != null) ? topIntent.Value.intent : null)
            {
                case null:
                    await context.SendActivity("Failed to get results from LUIS.");
                    break;
                case "None":
                    await context.SendActivity("Sorry, I don't understand.");
                    break;
                case "Weather":
                    conversationState.WeatherTopicStarted = true;
                    await context.SendActivity($"Looks like you want a weather forecast. What city do you want the forecast for?");

                    break;
                case "Help":
                    conversationState.HelpTopicStarted = true;
                    await context.SendActivity("<here's some help>");
                    break;
                case "Cancel":
                    // Cancel the process.
                    conversationState.CancelTopicStarted = true;
                    await context.SendActivity("<cancelling the process>");
                    break;
                default:
                    // Received an intent we didn't expect, so send its name and score.
                    await context.SendActivity($"Intent: {topIntent.Value.intent} ({topIntent.Value.score}).");
                    break;
            }
        }
    }
}
```

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)

Middleware zum Konversationszustand können Sie nach dem Hinzufügen von `LuisRecognizer` hinzufügen.

```javascript
const model = new LuisRecognizer({
    // This appID is for a public app that's made available for demo purposes
    // You can use it by providing your LUIS subscription key
     appId: 'eb0bf5e0-b468-421b-9375-fdfb644c512e',
    // replace subscriptionKey with your Authoring Key
    // your key is at https://www.luis.ai under User settings > Authoring Key 
    subscriptionKey: '<your subscription>',
    // The serviceEndpoint URL begins with "https://<region>.api.cognitive.microsoft.com", where region is the region associated with the key you are using. Some examples of regions are `westus`, `westcentralus`, `eastus2`, and `southeastasia`.
    serviceEndpoint: 'https://westus.api.cognitive.microsoft.com'
});
adapter.use(model)

// Add conversation state middleware
const conversationState = new ConversationState(new MemoryStorage());
adapter.use(conversationState);
```

Anschließend können Sie den Zustand speichern und so angeben, welches Thema gestartet wurde.

```javascript
// Listen for incoming activities 
server.post('/api/messages', (req, res) => {
    // Route received activity to adapter for processing
    adapter.processActivity(req, res, async(context) => {
        if (context.activity.type === 'message') {
            var utterance = context.activity.text;
            
            // Check topic flags in conversation state 
            if (conversationState.weatherTopicStarted) 
            {
                // Assume the user's message is a reply to the bot's prompt for a location
                await context.sendActivity(`The weather in ${utterance} is sunny.`);
                // This conversation flow is now finished. Set flag to false,
                // so that on the next turn the user can ask for another weather forecast.
                conversationState.WeatherTopicStarted = false;
            }
            // To add more steps to the other topics
            // you could check the topic flags here
            else 
            {
                const results = model.get(context);
                const topIntent = LuisRecognizer.topIntent(results);
                switch (topIntent) {
                    case 'None':
                        //Add app logic when there is no result
                        await context.sendActivity("<null case>")
                        break;
                    case 'Cancel':
                        conversationState.cancelTopicStarted = true;
                        await context.sendActivity("<cancelling the process>")
                        break;
                    case 'Help':
                        conversationState.helpTopicStarted = true;
                        await context.sendActivity("<here's some help>");
                        break;
                    case 'Weather':
                        conversationState.weatherTopicStarted = true;
                        await context.sendActivity("Looks like you want a weather forecast. What city do you want the forecast for?");
                        break;
                    default:
                        // Add app logic for the recognition results.
                        await context.sendActivity(`Received this intent: ${topIntent}`);
                }
            }
        }
    });
});
```

---

Versuchen Sie, den Bot im Bot Framework-Emulator auszuführen und beachten Sie, dass „get weather“ jetzt ein Konversationsablauf mit zwei Durchläufen ist.

![Ausführen des Bots](./media/how-to-luis/run-luis-bot-2step-weather.png)


## <a name="using-luis-with-dialogs"></a>Verwenden von LUIS mit Dialogen

Wenn Sie die Absicht von LUIS verwenden, einen Konversationsablauf mit mehreren Durchläufen auszulösen, kann es hilfreich sein, Dialoge zu verwenden, um diesen Ablauf zu kapseln. Dieser Beispielbot arbeitet mit einer LUIS-App, die Absichten erkennt, die entweder einen „home automation“-Dialog oder einen „weather“-Dialog auslösen.

> [!NOTE] 
> Um eine eigene Kopie der in diesem Beispiel verwendeten öffentlichen LUIS-App zu erstellen, kopieren Sie die [WeatherOrHomeAutomation.json](https://github.com/Microsoft/LUIS-Samples/blob/master/examples/simple-bot-example/WeatherOrHomeAutomation.json)-LUIS-Datei. Dann [importieren Sie die LUIS-App](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/create-new-app#import-new-app), [trainieren](https://docs.microsoft.com/en-us/azure/cognitive-services/LUIS/luis-how-to-train) und [veröffentlichen](https://docs.microsoft.com/en-us/azure/cognitive-services/LUIS/publishapp) sie. Ersetzen Sie die öffentlichen App-ID im Beispielcode durch die App-ID Ihrer neue LUIS-App.

# <a name="ctabcs"></a>[C#](#tab/cs)

Ändern Sie zunächst `ConfigureServices` in der Startup.cs, um Middleware für Ihre LUIS-App hinzuzufügen. Benennen Sie `EchoBot` in `LuisDialogBot` um.
In diesem Beispiel erkennt die als `LuisRecognizerMiddleware` hinzugefügte LUIS-App die Absichten `homeautomation` oder `weather`, um Dialoge mit diesem Bezeichnungen auszulösen. 

```csharp
public void ConfigureServices(IServiceCollection services)
{  
    // Rename EchoBot to LuisDialogBot
    services.AddBot<LuisDialogBot>(options =>
    {
        options.CredentialProvider = new ConfigurationCredentialProvider(Configuration); 
        options.Middleware.Add(new CatchExceptionMiddleware<Exception>(async (context, exception) =>
        {
            await context.TraceActivity("EchoBot Exception", exception);
            await context.SendActivity("Sorry, it looks like something went wrong!");
        }));

        // The Memory Storage used here is for local bot debugging only. When the bot
        // is restarted, anything stored in memory will be gone. 
        IStorage dataStore = new MemoryStorage();

        // Use Dictionary<string, object> for the conversation state type
        options.Middleware.Add(new ConversationState<Dictionary<string, object>>(dataStore));

        // Add LUIS recognizer as middleware
        options.Middleware.Add(
            new LuisRecognizerMiddleware(
                new LuisModel(
                    // This appID is for a public app that's made available for demo purposes
                    "428affb6-7650-46ac-9184-68c00a4f1729",
                    // You can use it by replacing <subscriptionKey> with your Authoring Key
                    // which you can find at https://www.luis.ai under User settings > Authoring Key
                    "<subscriptionKey>",
                    // The location-based URL begins with "https://<region>.api.cognitive.microsoft.com", where region is the region associated with the key you are using. Some examples of regions are `westus`, `westcentralus`, `eastus2`, and `southeastasia`.
                    new Uri("https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/"))));
    });
}
```

Benennen Sie **EchoBot.cs** in **LuisDialogBot.cs** und die `EchoBot`-Klasse in `LuisDialogBot` um. Fügen Sie anschließend in der **LuisDialogBot.cs** die folgenden `using`-Anweisungen hinzu.

```csharp
using System;
using System.Collections.Generic;
using System.Threading.Tasks;
using Microsoft.Bot.Builder;
using Microsoft.Bot.Builder.Ai.LUIS;
using Microsoft.Bot.Builder.Core.Extensions;
using Microsoft.Bot.Builder.Dialogs;
using Microsoft.Bot.Builder.Prompts;
using Microsoft.Bot.Schema;
```

Fügen Sie in der **LuisDialogBot.cs** den folgenden Code zur `LuisDialogBot`-Klasse hinzu.

```csharp
    public class LuisDialogBot : IBot
    {
        private DialogSet _dialogs;

        public LuisDialogBot()
        {
            _dialogs = new DialogSet();

            _dialogs.Add("homeautomation", CreateHomeAutomationWaterfall());
            _dialogs.Add("weather", CreateWeatherWaterfall());
            _dialogs.Add("weather_city", new Builder.Dialogs.TextPrompt());
        }

        // App ID for a separate LUIS app used to tell if the user wants to turn the lights on or off
        // The `homeautomation` dialogs uses results from this app to determine the "on/off" argument. 
        private static LuisModel luisHomeAutomation =
            new LuisModel("76feb726-515b-44c4-acc9-adb216965a58", "SUBSCRIPTION-KEY", new System.Uri("https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/"));

        public async Task OnTurn(ITurnContext context)
        {
            // This bot is only handling Messages
            if (context.Activity.Type == ActivityTypes.Message)
            {

                // Create a dialog context
                var state = ConversationState<Dictionary<string, object>>.Get(context);
                var dc = _dialogs.CreateContext(context, state);

                // Run the next dialog step
                await dc.Continue();
                
                // Check if any dialog has responded on this turn
                if (!context.Responded)
                {

                    var luisResult = context.Services.Get<RecognizerResult>(LuisRecognizerMiddleware.LuisRecognizerResultKey);
                    var topIntent = luisResult?.GetTopScoringIntent();
                    var utterance = context.Activity.Text;
                    var dialogArgs = new Dictionary<string, object>();

                    switch ((topIntent != null) ? topIntent.Value.intent.ToLowerInvariant() : null)
                    {
                        case "homeautomation":
                            // The Homeautomation_TurnOn and Homeautomation_TurnOff dialogs 
                            // use results from a separate LUIS app.
                            // The results determine the "on/off" argument to pass to the dialog. 
                            var recognizerHomeAutomation = new LuisRecognizer(luisHomeAutomation);
                            RecognizerResult recognizerResult = await recognizerHomeAutomation.Recognize(utterance, System.Threading.CancellationToken.None);
                            var topHomeAutoIntent = recognizerResult.GetTopScoringIntent().intent;


                            dialogArgs.Add("IntentFromHomeAuto", "");
                            switch ((topHomeAutoIntent != null) ? topHomeAutoIntent.ToLowerInvariant() : null)
                            {
                                case "homeautomation_turnon":
                                    dialogArgs["Intent_HomeAutomation"] = "on";
                                    await dc.Begin("homeautomation", dialogArgs);
                                    break;
                                case "homeautomation_turnoff":
                                    dialogArgs["Intent_HomeAutomation"] = "off";
                                    await dc.Begin("homeautomation", dialogArgs);
                                    break;
                                case null:
                                    await dc.Begin("homeautomation", null);
                                    break;
                                default:
                                    dialogArgs["Intent_HomeAutomation"] = topHomeAutoIntent;
                                    await dc.Begin("homeautomation", dialogArgs);
                                    break;
                            }
                            break;
                        case "weather":
                            dialogArgs.Add("LuisResult", luisResult);
                            await dc.Begin("weather", dialogArgs);
                            break;
                        case null:
                            await context.SendActivity($"Couldn't get a result from LUIS. You said: {utterance}");
                            break;
                        default:
                            // The intent didn't match any case, so just display the recognition results.
                            await context.SendActivity($"you said: {utterance}");
                            await context.SendActivity($"Recognized intent: {topIntent.Value.intent}.");

                            break;

                    }

                }
            }
        }

    }


```

Fügen Sie nach `OnTurn` Code in der `LuisDialogBot`-Klasse zum Implementieren der Dialoge hinzu.

```csharp
        // The home automation waterfall has one step
        private WaterfallStep[] CreateHomeAutomationWaterfall()
        {
            return new WaterfallStep[] {
                TurnLightsOnOrOff
            };
        }

        // The weather waterfall has two steps
        private WaterfallStep[] CreateWeatherWaterfall()
        {
            return new WaterfallStep[] {
                AskWeatherLocation,
                SendWeatherReport
            };
        }

        /// <summary>
        /// This is the first step and only of the home automation dialog.
        /// </summary>
        /// <param name="dc"></param>
        /// <param name="args">Can be "on", "off", another string for an intent name, or null.
        /// null indicates that no result was received from which to get an intent name.</param>
        /// <param name="next"></param>
        /// <returns></returns>
        private async Task TurnLightsOnOrOff(DialogContext dc, IDictionary<string, object> args, SkipStepFunction next)
        {
            var intentFromHomeAutomation = args["Intent_HomeAutomation"];
            if (args != null)
            {
                switch (intentFromHomeAutomation)
                {
                    case "on":
                    case "off":
                        await dc.Context.SendActivity($"Turning {intentFromHomeAutomation} your lights!");
                        break;
                    default:
                        await dc.Context.SendActivity($"Intent detected by homeautomation was: {intentFromHomeAutomation}, but the home automation system doesn't support that yet.");
                        break;
                }

            }
            else
            {
                await dc.Context.SendActivity($"You said {dc.Context.Activity.AsMessageActivity().Text}. Unable to get a result from which to determine on/off/other operation");
            }

            await dc.End();

        }

        // This is the first step of the weather dialog
        private async Task AskWeatherLocation(DialogContext dc, IDictionary<string, object> args, SkipStepFunction next)
        {
            var dialogState = dc.ActiveDialog.State as IDictionary<string, object>;
            
            if (args["LuisResult"] is RecognizerResult luisResult)
            {
                var location = GetEntity<string>(luisResult, "Weather_Location");
                if (!string.IsNullOrEmpty(location))
                {
                    dialogState.Add("Location", location);
                }
            }

            // Save info back to the dialog instance
            dc.ActiveDialog.State = dialogState;

            if (!dialogState.ContainsKey("Location"))
            {
                await dc.Prompt("weather_city", "What city do you want the weather for?");
            }
            else
            {
                // We've set the location parameter for the weather report,
                // so go to the next step in the waterfall
                await dc.Continue();
            }
        }

        // This the second step of the weather dialog
        private async Task SendWeatherReport(DialogContext dc, IDictionary<string, object> args, SkipStepFunction next)
        {
            var dialogState = dc.ActiveDialog.State as IDictionary<string, object>;
            if (args != null)
            {
                if (!dialogState.ContainsKey("Location"))
                {   // Interpret args as a reply to prompt only if they didn't give location
                    TextResult locationResult = (TextResult)args;
                    dialogState.Add("Location", locationResult.Text);
                }

            }

            // You can add some logic that uses the location 
            // to get the weather from a weather service instead of hard-coding it
            await dc.Context.SendActivity($"The weather forecast for '{dialogState["Location"]}' is sunny and 70 degrees F.");

            dc.ActiveDialog.State = new Dictionary<string, object>(); // clear the dialog state 
            await dc.End();
        }
```

Fügen Sie diese Hilfsfunktion hinzu.

```cs
private T GetEntity<T>(RecognizerResult luisResult, string entityKey)
{
    var data = luisResult.Entities as IDictionary<string, JToken>;
    if (data.TryGetValue(entityKey, out JToken value))
    {
        return value.First.Value<T>();
    }
    return default(T);
}
```

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)

Möglicherweise müssen Sie die Dialogbibliothek installieren, wenn Sie dies noch nicht getan haben. 

```cmd
npm install --save botbuilder-dialogs
```

Erstellen Sie zunächst Ihre LUIS-App und fügen Sie sie zu Ihrem Bot hinzu mithilfe von `adapter.use`:

```javascript
const { BotFrameworkAdapter, ConversationState, MemoryStorage, TurnContext } = require('botbuilder');
const { LuisRecognizer } = require('botbuilder-ai');
const { DialogSet } = require('botbuilder-dialogs');
const restify = require('restify');

// Create server
let server = restify.createServer();
server.listen(process.env.port || process.env.PORT || 3978, function () {
    console.log(`${server.name} listening to ${server.url}`);
});

// Create adapter
const adapter = new BotFrameworkAdapter({
    appId: process.env.MICROSOFT_APP_ID,
    appPassword: process.env.MICROSOFT_APP_PASSWORD
});

// Create LuisRecognizer 
// The LUIS application is public, meaning you can use your own subscription key to test the applications.
const luisRecognizer = new LuisRecognizer({
    appId: '1fefd4a7-ae5b-4e63-99f7-0cf499a1423b',
    subscriptionKey: process.env.LUIS_SUBSCRIPTION_KEY,
    serviceEndpoint: 'https://westus.api.cognitive.microsoft.com/',
    verbose: true
});

// Add the recognizer to your bot
adapter.use(luisRecognizer);

// create conversation state
const conversationState = new ConversationState(new MemoryStorage());
adapter.use(conversationState);

// register some dialogs for usage with the intents detected by the LUIS app
const dialogs = new DialogSet();
```

Fügen Sie anschließend Dialoge hinzu:

```javascript

dialogs.add('HomeAutomation_TurnOn', [
    async (dialogContext) => {
        const state = conversationState.get(dialogContext.context);
        // state.homeAutomationTurnOn counts how many times this dialog was called 
        state.homeAutomationTurnOn = state.homeAutomationTurnOn ? state.homeAutomationTurnOn + 1 : 1;
        await dialogContext.context.sendActivity(`${state.homeAutomationTurnOn}: You reached the "HomeAutomation_TurnOn" dialog.`);

        await dialogContext.end();
    }
]);

dialogs.add('Weather_GetForecast', [
    async (dialogContext) => {
        const state = conversationState.get(dialogContext.context);
        // state.weatherGetForecast counts how many times this dialog was called  
        state.weatherGetForecast = state.weatherGetForecast ? state.weatherGetForecast + 1 : 1;
        await dialogContext.context.sendActivity(`${state.weatherGetForecast}: You reached the "Weather_GetForecast" dialog.`);

        await dialogContext.end();
    }
]);

dialogs.add('None', [
    async (dialogContext) => {
        const state = conversationState.get(dialogContext.context);
        // state.None counts how many times this dialog was called        
        state.None = state.None ? state.None + 1 : 1;
        await dialogContext.context.sendActivity(`${state.None}: You reached the "None" dialog.`);

        await dialogContext.end();
    }
]);
```

Rufen Sie in Ihrem Bot jeden Dialog basierend auf der erkannten Absicht auf:
```javascript
server.post('/api/messages', (req, res) => {
    adapter.processActivity(req, res, async (context) => {
        if (context.activity.type === 'message') {
            const state = conversationState.get(context);
            const dc = dialogs.createContext(context, state);

            // Retrieve the LUIS results from our LUIS application
            const luisResults = luisRecognizer.get(context);

            // Extract the top intent from LUIS and use it to select which dialog to start
            // "NotFound" is the intent name for when no top intent can be found.
            const topIntent = LuisRecognizer.topIntent(luisResults, "NotFound");

            const isMessage = context.activity.type === 'message';
            if (isMessage) {
                switch (topIntent) {
                    case 'homeautomation':                    
                        await dc.begin("HomeAutomation_TurnOn", luisResults);
                        break;
                    case 'weather':                    
                        await dc.begin("Weather_GetForecast", luisResults);
                        break;
                    case 'None':
                        await dc.begin("None");
                        break;
                    case 'NotFound':
                        await context.sendActivity(`Sorry, I didn't get any results from LUIS.`);
                        break;
                    default:
                        // handle intents for which we have no dialog
                        await context.sendActivity(`You reached the ${topIntent} intent.`);
                        break;
                }
            }
            
            if (!context.responded) {
                await dc.continue();
                if (!context.responded && isMessage) {
                    await dc.context.sendActivity(`Hi! I'm the LUIS dialog bot. Say something and LUIS will decide how the message should be routed.`);
                }
            }
        }
    });
});
```

Versuchen Sie, den Bot im Bot Framework-Emulator auszuführen, und sagen Sie etwas wie „Turn on the lights“ (Licht einschalten), „Get weather in Seattle“ (Wetter in Seattle abrufen).

![Ausführen des Bots](./media/how-to-luis/run-luis-bot-js-single-step-dialog.png)

---

## <a name="extract-entities"></a>Extrahieren von Entitäten

Neben dem Erkennen von Absichten können mit einer LUIS-App auch Entitäten extrahiert werden, bei denen es sich um wichtige Wörter für die Erfüllung der Anforderung eines Benutzers handelt. Im Beispiel des „weather“-Dialogs kann die LUIS-App beispielsweise den Ort für den Wetterbericht aus der Nachricht des Benutzers extrahieren. 

Eine gängige Methode zur Verwendung von Dialogen ist die Identifizierung von Entitäten in der Nachricht des Benutzers und die Abfrage nach den benötigten Entitäten, die nicht gefunden werden. Der nachfolgende Dialogschritt verarbeitet dann die Antwort auf die Eingabeaufforderung.

# <a name="ctabcs"></a>[C#](#tab/cs)

Die Nachricht des Benutzers lautete beispielsweise „What's the weather in Seattle?“ (Wie ist das Wetter in Seattle?). Für den Bot in diesem Beispiel gibt der [LuisRecognizer](https://docs.microsoft.com/en-us/dotnet/api/microsoft.bot.builder.ai.luis.luisrecognizer) ein [RecognizerResult](https://docs.microsoft.com/en-us/dotnet/api/microsoft.bot.builder.core.extensions.recognizerresult) mit einer [`Entities`-Eigenschaft](https://docs.microsoft.com/en-us/dotnet/api/microsoft.bot.builder.core.extensions.recognizerresult#properties-) mit folgender Struktur aus:

```json
{
"$instance": {
    "Weather_Location": [
        {
            "startIndex": 22,
            "endIndex": 29,
            "text": "seattle",
            "score": 0.8073087
        }
    ]
},
"Weather_Location": [
        "seattle"
    ]
}
```

Die folgende Hilfsfunktion, die Sie zu Ihrer `LuisDialogBot`-Klasse hinzugefügt haben, ruft Entitäten aus `RecognizerResult` von LUIS ab.

```cs
// Get entities from LUIS result
private T GetEntity<T>(RecognizerResult luisResult, string entityKey)
{
    var data = luisResult.Entities as IDictionary<string, JToken>;
    if (data.TryGetValue(entityKey, out JToken value))
    {
        return value.First.Value<T>();
    }
    return default(T);
}
```

Wenn Sie Informationen, wie. z.B. Entitäten, aus mehreren Schritten in einem Dialog erfassen, kann es hilfreich sein, die benötigten Informationen im dialogspezifischen Zustand zu speichern. Beispielsweise werden die Entitäten im `AskWeatherLocation`-Dialog aus den an den Dialog übergebenen LUIS-Ergebnissen extrahiert. Wenn eine `Weather_Location`-Entität gefunden wird, wird sie zum Dialogzustand hinzugefügt.

```cs

        // This is the first step of the weather dialog
        private async Task AskWeatherLocation(DialogContext dc, IDictionary<string, object> args, SkipStepFunction next)
        {
            var dialogState = dc.ActiveDialog.State as IDictionary<string, object>;
            
            if (args["LuisResult"] is RecognizerResult luisResult)
            {
                var location = GetEntity<string>(luisResult, "Weather_Location");
                if (!string.IsNullOrEmpty(location))
                {
                    dialogState.Add("Location", location);
                }
            }

            // Save info back to the dialog instance
            dc.ActiveDialog.State = dialogState;

            if (!dialogState.ContainsKey("Location"))
            {
                await dc.Prompt("weather_city", "What city do you want the weather for?");
            }
            else
            {
                // We've set the location parameter for the weather report,
                // so go to the next step in the waterfall
                await dc.Continue();
            }
        }
```

Im zweiten Schritt des Dialogs können Sie die im vorherigen Schritt gespeicherten Informationen aus dem Zustand der Dialoginstanz sowie die Antwort des Benutzers auf die Abfrage nach dem Ort abrufen und einen Wetterbericht an den Benutzer senden.

```cs

// This the second step of the weather dialog
private async Task SendWeatherReport(DialogContext dc, IDictionary<string, object> args, SkipStepFunction next)
{
    var dialogState = dc.ActiveDialog.State as IDictionary<string, object>;
    if (args != null)
    {
        if (!dialogState.ContainsKey("Location"))
        {   // Interpret args as a reply to prompt only if they didn't give location
            TextResult locationResult = (TextResult)args;
            dialogState.Add("Location", locationResult.Text);
        }

    }

    // You can add some logic that uses the location and date
    // to get the weather from a weather service instead of hard-coding it
    await dc.Context.SendActivity($"The weather forecast for '{dialogState["Location"]}' is sunny and 70 degrees F.");
    
    // clear the dialog state 
    dc.ActiveDialog.State = new Dictionary<string, object>(); 
    await dc.End();
}
```

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)

Die Nachricht des Benutzers lautete beispielsweise „What's the weather in Seattle?“ (Wie ist das Wetter in Seattle?). Für den Bot in diesem Beispiel gibt der [LuisRecognizer](https://docs.microsoft.com/en-us/javascript/api/botbuilder-ai/luisrecognizer) ein [RecognizerResult](https://docs.microsoft.com/en-us/javascript/api/botbuilder-core-extensions/recognizerresult) mit einer `entities`-Eigenschaft mit folgender Struktur aus:

```json
{
"$instance": {
    "Weather_Location": [
        {
            "startIndex": 22,
            "endIndex": 29,
            "text": "seattle",
            "score": 0.8073087
        }
    ]
},
"Weather_Location": [
        "seattle"
    ]
}
```

Dies `findEntities`-Funktion sucht nach allen von der LUIS-App erkannten `Weather_Location`-Entitäten.

<!-- TODO: Turn into a waterfall -->

```javascript
// Helper function for finding a specified entity
// entityResults are the results from LuisRecognizer.get(context)
function findEntities(entityName, entityResults) {
    let entities = []
    if (entityName in entityResults) {
        entityResults[entityName].forEach(entity => {
            entities.push(entity);
        });
    }
    return entities.length > 0 ? entities : undefined;
}
```

Rufen Sie `findEntities` aus dem `Weather_GetForecast`-Dialog auf.

```javascript
// Pass the LUIS recognizer result to the args parameter
dialogs.add('Weather_GetForecast', [
    async (dialogContext, args) => {
        const locations = findEntities('Weather_Location', args.entities);

        const state = conversationState.get(dialogContext.context);
        state.weatherGetForecast = state.weatherGetForecast ? state.weatherGetForecast + 1 : 1;
        await dialogContext.context.sendActivity(`${state.weatherGetForecast}: You reached the "Weather.GetForecast" dialog.`);
        if (locations) {
            await dialogContext.context.sendActivity(`Found these "Weather_Location" entities:\n${locations.join(', ')}`);
        }
        await dialogContext.end();
    }
]);
```

Die Entitäten werden aus den an `dc.begin` übergebenen LUIS-Ergebnissen extrahiert.

```javascript
await dc.begin("Weather_GetForecast", luisResults);
```
---

## <a name="additional-resources"></a>Zusätzliche Ressourcen

Weitere Hintergrundinformationen zu LUIS finden Sie unter [Language Understanding](./bot-builder-concept-luis.md).

Informationen zum Erstellen der LUIS-Apps in diesen Beispielen finden Sie unter [LUIS-Apps für Bot Builder](https://aka.ms/luis-bot-examples).

LUIS kann mit anderen Cognitive Services kombiniert werden, um Ihren Bot noch leistungsfähiger zu machen. Informationen zum Kombinieren von QnA mit Language Understanding (LUIS) in Ihrem Bot finden Sie in [Kombinieren von LUIS-Apps und QnA-Dienste mit dem Dispatch-Tool](./bot-builder-tutorial-dispatch.md).

## <a name="next-steps"></a>Nächste Schritte


> [!div class="nextstepaction"]
> [Generieren von stark typisierten LUIS-Ergebnissen](./bot-builder-howto-v4-luisgen.md)


