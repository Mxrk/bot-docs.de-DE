---
title: Hinzufügen von Features zum Verstehen natürlicher Sprache zu Ihrem Bot | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie LUIS zum Verstehen natürlicher Sprache mit dem Bot Builder SDK verwenden können.
keywords: Language Understanding, LUIS, absicht, erkennung, entitäten, middleware
author: ivorb
ms.author: v-ivorb
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: cognitive-services
ms.date: 11/08/18
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: eab8e2f9d437748d0bb0fefd31c03c8fb350c6b1
ms.sourcegitcommit: 8b7bdbcbb01054f6aeb80d4a65b29177b30e1c20
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/14/2018
ms.locfileid: "51645700"
---
# <a name="add-natural-language-understanding-to-your-bot"></a>Hinzufügen von Features zum Verstehen natürlicher Sprache zu Ihrem Bot

[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

Die Fähigkeit zu verstehen, was Ihr Benutzer in einer Konversation und im Kontext meint, kann eine schwierige Aufgabe sein, Ihrem Bot aber ein natürlicheres Sprachgefühl verleihen. Language Understanding, genannt LUIS, ermöglicht es Ihnen, genau das zu tun, sodass Ihr Bot die Absicht von Benutzernachrichten bestimmen, mehr natürliche Sprache von Ihrem Benutzer erkennen und den Konversationsablauf besser steuern kann. Weitere Hintergrundinformationen zu LUIS finden Sie bei Bedarf unter [Language Understanding](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/what-is-luis) für Bots.

## <a name="prerequisites"></a>Voraussetzungen
In diesem Thema wird die Einrichtung eines einfachen Bots behandelt, für den LUIS verwendet wird, um verschiedene Absichten zu erkennen. Der Code in diesem Artikel basiert auf dem Beispiel „NLP with LUIS“ für [C#](https://aka.ms/cs-luis-sample) und [JavaScript](https://aka.ms/js-luis-sample).

## <a name="create-a-luis-app-in-the-luis-portal"></a>Erstellen einer LUIS-App im LUIS-Portal

Registrieren Sie sich zuerst unter [luis.ai](https://www.luis.ai) für ein Konto, und erstellen Sie im LUIS-Portal eine LUIS-App, indem Sie [diese Anleitung](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/luis-how-to-start-new-app) befolgen. Wenn Sie Ihre eigene Version der in diesem Artikel verwendeten LUIS-App erstellen möchten, können Sie im LUIS-Portal wie folgt vorgehen: [Importieren](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/create-new-app#import-new-app) Sie die Datei `LUIS.Reminders.json` ([C#](https://github.com/Microsoft/BotBuilder-Samples/blob/v4/samples/csharp_dotnetcore/12.nlp-with-luis/CognitiveModels/LUIS-Reminders.json) | [JS](https://github.com/Microsoft/BotBuilder-Samples/blob/master/samples/javascript_nodejs/12.nlp-with-luis/cognitiveModels/reminders.json)), um Ihre LUIS-App zu erstellen, und führen Sie dann das [Trainieren](https://docs.microsoft.com/en-us/azure/cognitive-services/LUIS/luis-how-to-train) und [Veröffentlichen](https://docs.microsoft.com/en-us/azure/cognitive-services/LUIS/publishapp) durch.

### <a name="obtain-values-to-connect-to-your-luis-app"></a>Abrufen von Werten zum Herstellen einer Verbindung mit Ihrer LUIS-App

Nachdem Ihre LUIS-App veröffentlicht wurde, können Sie von Ihrem Bot aus darauf zugreifen. Sie müssen sich mehrere Werte notieren, um von Ihrem Bot aus auf Ihre LUIS-App zugreifen zu können. Sie können diese Informationen mit dem LUIS-Portal oder mit den CLI-Tools abrufen.

#### <a name="using-luis-portal"></a>Verwenden des LUIS-Portals
- Wählen Sie Ihre veröffentlichte LUIS-App unter [luis.ai](https://www.luis.ai) aus.
- Wählen Sie bei geöffneter veröffentlichter LUIS-App die Registerkarte **VERWALTEN**.
- Wählen Sie auf der linken Seite die Registerkarte **Anwendungsinformationen**, und notieren Sie sich den Wert, der unter _Anwendungs-ID_ angezeigt wird, als <IHRE_APP-ID>.
- Wählen Sie auf der linken Seite die Registerkarte **Schlüssel und Endpunkte**, und notieren Sie sich den Wert unter _Erstellungsschlüssel_ als <IHREN_ERSTELLUNGSSCHLÜSSEL>. Beachten Sie, dass <IHR_ABONNEMENTSCHLÜSSEL> mit <IHREM_ERSTELLUNGSSCHLÜSSEL> identisch ist. Scrollen Sie nach unten an das Ende der Seite, und notieren Sie sich den Wert unter _Region_ als <IHRE_REGION> und den Wert unter _Endpunkt_ als <IHREN_ENDPUNKT>.

#### <a name="using-cli-tools"></a>Verwenden von CLI-Tools

Sie können die Bot Builder CLI-Tools [luis](https://aka.ms/botbuilder-tools-luis) und [msbot](https://aka.ms/botbuilder-tools-msbot-readme) nutzen, um Metadaten zu Ihrer LUIS-App abzurufen und Ihrer **BOT**-Datei hinzuzufügen.

1. Öffnen Sie ein Terminal oder eine Eingabeaufforderung, und navigieren Sie zum Stammverzeichnis für Ihr Botprojekt.
2. Stellen Sie sicher, dass die Tools `luis` und `msbot` installiert sind.

    ```shell
    npm install luis msbot
    ```

3. Führen Sie `luis init` aus, um eine LUIS-Ressourcendatei (**.luisrc**) zu erstellen. Geben Sie Ihren LUIS-Erstellungsschlüssel und Ihre Region an, wenn Sie dazu aufgefordert werden. Sie müssen hier nicht Ihre App-ID eingeben.
4. Führen Sie den folgenden Befehl aus, um Ihre Metadaten herunterzuladen und der Konfigurationsdatei Ihres Bots hinzuzufügen.
    Wenn Sie Ihre Konfigurationsdatei verschlüsselt haben, müssen Sie Ihren geheimen Schlüssel angeben, um die Datei zu aktualisieren.

    ```shell
    luis get application --appId <your-app-id> --msbot | msbot connect luis --stdin [--secret <YOUR-SECRET>]
    ```

## <a name="configure-your-bot-to-use-your-luis-app"></a>Konfigurieren Sie Ihren Bot für die Verwendung Ihrer LUIS-App

Ein Verweis auf die LUIS-App wird zuerst beim Initialisieren des Bots hinzugefügt. Dies ermöglicht das Aufrufen in unserer Botlogik.

### <a name="prerequisite"></a>Voraussetzung

Bevor wir mit dem Programmieren beginnen, sollten Sie sicherstellen, dass Sie über die Pakete verfügen, die für die LUIS-App erforderlich sind.

# <a name="ctabcs"></a>[C#](#tab/cs)

Fügen Sie Ihrem Bot das folgende [NuGet-Paket](https://docs.microsoft.com/en-us/nuget/tools/package-manager-ui) hinzu.

* `Microsoft.Bot.Builder.AI.Luis`

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)

Die LUIS-Features sind im Paket `botbuilder-ai` enthalten. Sie können Ihrem Projekt dieses Paket über npm hinzufügen:

```shell
npm install --save botbuilder-ai
```

---

# <a name="ctabcs"></a>[C#](#tab/cs)

Laden Sie den [NLP LUIS-Beispielcode](https://aka.ms/cs-luis-sample) herunter, und öffnen Sie ihn. Wir ändern den Code gemäß unseren Anforderungen. 

Fügen Sie zuerst die Informationen, die zum Zugreifen auf Ihre LUIS-App erforderlich sind, z.B. Anwendungs-ID, Erstellungsschlüssel, Abonnementschlüssel, Endpunkt und Region, der Datei `BotConfiguration.bot` hinzu. Dies sind die Werte, die Sie zuvor aus Ihrer veröffentlichten LUIS-App gespeichert haben.

```csharp
{
  "name": "LuisBot",
  "services": [
    {
      "type": "endpoint",
      "name": "development",
      "endpoint": "http://localhost:3978/api/messages",
      "appId": "",
      "appPassword": "",
      "id": "1"
    },
    {
      "type": "luis",
      "name": "LuisBot",
      "AppId": "<YOUR_APP_ID>",
      "SubscriptionKey": "<YOUR_SUBSCRIPTION_KEY>",
      "AuthoringKey": "<YOUR_AUTHORING_KEY>",
      "GetEndpoint": "<YOUR_ENDPOINT>",
      "Region": "<YOUR_REGION>"
    }
  ],
  "padlock": "",
  "version": "2.0"
}
```

Als Nächstes initialisieren wir eine neue Instanz der Bot Service-Klasse `BotServices.cs`, mit der die obigen Informationen aus Ihrer `.bot`-Datei abgerufen werden. Fügen Sie der Datei `BotServices.cs` den folgenden Code hinzu.

```csharp
public class BotServices
{
    /// Initializes a new instance of the BotServices class
    public BotServices(BotConfiguration botConfiguration)
    {
        foreach (var service in botConfiguration.Services)
        {
            switch (service.Type)
            {
                case ServiceTypes.Luis:
                {
                    var luis = (LuisService)service;
                    if (luis == null)
                    {
                        throw new InvalidOperationException("The LUIS service is not configured correctly in your '.bot' file.");
                    }

                    var app = new LuisApplication(luis.AppId, luis.AuthoringKey, luis.GetEndpoint());
                    var recognizer = new LuisRecognizer(app);
                    this.LuisServices.Add(luis.Name, recognizer);
                    break;
                    }
                }
            }
        }

    /// Gets the set of LUIS Services used.
    /// LuisServices is represented as a dictionary.  
    public Dictionary<string, LuisRecognizer> LuisServices { get; } = new Dictionary<string, LuisRecognizer>();
}
```

Anschließend registrieren wir die LUIS-App als Singleton in der Datei `Startup.cs`, indem wir den folgenden Code in `ConfigureServices` hinzufügen.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    var secretKey = Configuration.GetSection("botFileSecret")?.Value;
    var botFilePath = Configuration.GetSection("botFilePath")?.Value;

    // Loads .bot configuration file and adds a singleton that your Bot can access through dependency injection.
    var botConfig = BotConfiguration.Load(botFilePath ?? @".\BotConfiguration.bot", secretKey);
    services.AddSingleton(sp => botConfig ?? throw new InvalidOperationException($"The .bot config file could not be loaded. ({botConfig})"));

    // Initialize Bot Connected Services clients.
    var connectedServices = new BotServices(botConfig);
    services.AddSingleton(sp => connectedServices);
    services.AddSingleton(sp => botConfig);

    services.AddBot<LuisBot>(options =>
    {
        // Retrieve current endpoint.
        var environment = _isProduction ? "production" : "development";
        var service = botConfig.Services.Where(s => s.Type == "endpoint" && s.Name == environment).FirstOrDefault();
        if (!(service is EndpointService endpointService))
        {
            throw new InvalidOperationException($"The .bot file does not contain an endpoint with name '{environment}'.");
        }

        options.CredentialProvider = new SimpleCredentialProvider(endpointService.AppId, endpointService.AppPassword);

        // Creates a logger for the application to use.
        ILogger logger = _loggerFactory.CreateLogger<LuisBot>();

        // Catches any errors that occur during a conversation turn and logs them.
        options.OnTurnError = async (context, exception) =>
        {
            logger.LogError($"Exception caught : {exception}");
            await context.SendActivityAsync("Sorry, it looks like something went wrong.");
        };
        /// ...
    });
}
```

Als Nächstes müssen Sie diese LUIS-Instanz für Ihren Bot bereitstellen. Öffnen Sie `LuisBot.cs`, und fügen Sie den folgenden Code am Anfang der Datei hinzu.

```csharp
public class LuisBot : IBot
{
    public static readonly string LuisKey = "LuisBot";
    private const string WelcomeText = "This bot will introduce you to natural language processing with LUIS. Type an utterance to get started";

    /// Services configured from the ".bot" file.
    private readonly BotServices _services;

    /// Initializes a new instance of the LuisBot class.
    public LuisBot(BotServices services)
    {
        _services = services ?? throw new System.ArgumentNullException(nameof(services));
        if (!_services.LuisServices.ContainsKey(LuisKey))
        {
            throw new System.ArgumentException($"Invalid configuration. Please check your '.bot' file for a LUIS service named '{LuisKey}'.");
        }
    }
    /// ...
}
```

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)

In unserem Beispiel befindet sich der Startcode in der Datei **index.js** und der Code für die Botlogik in der Datei **bot.js**. Zusätzliche Konfigurationsinformationen sind in der Datei **nlp-with-luis.bot** enthalten.

Nachdem Sie die Anleitungen zur Erstellung Ihrer LUIS-App und zur Aktualisierung Ihrer **BOT**-Datei befolgt haben, sollte Ihre Datei **nlp-with-luis.bot** einen Diensteintrag für Ihre LUIS-App enthalten.

```json
{
    "name": "YOUR_LUIS_APP_NAME",
    "description": "",
    "services": [
        {
            "type": "endpoint",
            "name": "development",
            "endpoint": "http://localhost:3978/api/messages",
            "appId": "",
            "appPassword": "",
            "id": "35"
        },
        {
            "type": "luis",
            "name": "YOUR_LUIS_APP_NAME",
            "appId": "<YOUR_APP_ID>",
            "version": "0.1",
            "authoringKey": "<YOUR_AUTHORING_KEY>",
            "subscriptionKey": "<YOUR_SUBSCRIPTION_KEY>>",
            "region": "<YOUR_REGION>",
            "id": "83"
        }
    ],
    "padlock": "",
    "version": "2.0"
}
```

In der Datei **bot.js** lesen wir die Konfigurationsinformationen ein, um den LUIS-Dienst zu generieren und den Bot zu initialisieren.
Aktualisieren Sie den Wert von `LUIS_CONFIGURATION` in den Namen Ihrer LUIS-App, der in Ihrer Konfigurationsdatei angezeigt wird.

```javascript
// Language Understanding (LUIS) service name as defined in the .bot file.YOUR_LUIS_APP_NAME is "LuisBot" in the C# code.
const LUIS_CONFIGURATION = '<YOUR_LUIS_APP_NAME>';

// Get endpoint and LUIS configurations by service name.
const endpointConfig = botConfig.findServiceByNameOrId(BOT_CONFIGURATION);
const luisConfig = botConfig.findServiceByNameOrId(LUIS_CONFIGURATION);

// Map the contents to the required format for `LuisRecognizer`.
const luisApplication = {
    applicationId: luisConfig.appId,
    endpointKey: luisConfig.subscriptionKey || luisConfig.authoringKey,
    azureRegion: luisConfig.region
};

// Create configuration for LuisRecognizer's runtime behavior.
const luisPredictionOptions = {
    includeAllIntents: true,
    log: true,
    staging: false
};

// Create adapter...

// Create the LuisBot.
let bot;
try {
    bot = new LuisBot(luisApplication, luisPredictionOptions);
} catch (err) {
    console.error(`[botInitializationError]: ${ err }`);
    process.exit();
}
```

Anschließend erstellen wir den HTTP-Server und lauschen auf eingehende Anforderungen, mit denen Aufrufe unserer Botlogik generiert werden.

```javascript
// Create HTTP server.
let server = restify.createServer();
server.listen(process.env.port || process.env.PORT || 3978, function() {
    console.log(`\n${ server.name } listening to ${ server.url }.`);
    console.log(`\nGet Bot Framework Emulator: https://aka.ms/botframework-emulator.`);
    console.log(`\nTo talk to your bot, open nlp-with-luis.bot file in the emulator.`);
});

// Listen for incoming requests.
server.post('/api/messages', (req, res) => {
    adapter.processActivity(req, res, async(turnContext) => {
        await bot.onTurn(turnContext);
    });
});
```

---

LUIS ist jetzt für Ihren Bot konfiguriert. Als Nächstes befassen wir uns damit, wie Sie die Absicht von LUIS abrufen können.

## <a name="get-the-intent-by-calling-luis"></a>Abrufen der Absicht durch Aufrufen von LUIS

Ihr Bot ruft Ergebnisse aus LUIS ab, indem er die LUIS-Erkennung aufruft.

# <a name="ctabcs"></a>[C#](#tab/cs)

Damit Ihr Bot einfach basierend auf der von der LUIS-App erkannten Absicht eine Antwort sendet, rufen Sie `LuisRecognizer` auf, um ein `RecognizerResult` zu erhalten. Diesen Aufruf können Sie jederzeit im Code verwenden, wenn Sie die LUIS-Absicht abrufen müssen.

```cs
public async Task OnTurnAsync(ITurnContext turnContext, CancellationToken cancellationToken = default(CancellationToken))

{
    if (turnContext.Activity.Type == ActivityTypes.Message)
    {
        // Check LUIS model
        var recognizerResult = await _services.LuisServices[LuisKey].RecognizeAsync(turnContext, cancellationToken);
        var topIntent = recognizerResult?.GetTopScoringIntent();
        if (topIntent != null && topIntent.HasValue && topIntent.Value.intent != "None")
        {
            await turnContext.SendActivityAsync($"==>LUIS Top Scoring Intent: {topIntent.Value.intent}, Score: {topIntent.Value.score}\n");
        }
        else
        {
            var msg = @"No LUIS intents were found.
                        This sample is about identifying two user intents:
                        'Calendar.Add'
                        'Calendar.Find'
                        Try typing 'Add Event' or 'Show me tomorrow'.";
            await turnContext.SendActivityAsync(msg);
        }
        }
        else if (turnContext.Activity.Type == ActivityTypes.ConversationUpdate)
        {
            // Send a welcome message to the user and tell them what actions they may perform to use this bot
            await SendWelcomeMessageAsync(turnContext, cancellationToken);
        }
        else
        {
            await turnContext.SendActivityAsync($"{turnContext.Activity.Type} event detected", cancellationToken: cancellationToken);
        }
}
```

Alle in der Äußerung erkannten Absichten werden als Zuordnung von Absichtsbezeichnungen zu den Ergebnissen zurückgegeben und können von `recognizerResult.Intents` aus aufgerufen werden. Es wird eine statische `recognizerResult?.GetTopScoringIntent()`-Methode wird zur Verfügung, um die Suche nach der Absicht mit der besten Bewertung für eine Ergebnismenge zu vereinfachen.

Alle erkannten Entitäten werden als Zuordnung von Entitätsnamen zu Werten zurückgegeben und mit `recognizerResults.entities` aufgerufen. Zusätzliche Entitätsmetadaten können durch Übergabe einer `verbose=true`-Einstellung beim Erstellen des LuisRecognizers zurückgegeben werden. Die hinzugefügten Metadaten können dann mit `recognizerResults.entities.$instance` aufgerufen werden.

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)

In der Datei **bot.js** übergeben wir die Eingabe des Benutzers an die `recognize`-Methode der LUIS-Erkennung, um Antworten von der LUIS-App zu erhalten.

```javascript
const { ActivityTypes } = require('botbuilder');
const { LuisRecognizer } = require('botbuilder-ai');

/**
 * A simple bot that responds to utterances with answers from the Language Understanding (LUIS) service.
 * If an answer is not found for an utterance, the bot responds with help.
 */
class LuisBot {
    /**
     * The LuisBot constructor requires one argument (`application`) which is used to create an instance of `LuisRecognizer`.
     * @param {LuisApplication} luisApplication The basic configuration needed to call LUIS. In this sample the configuration is retrieved from the .bot file.
     * @param {LuisPredictionOptions} luisPredictionOptions (Optional) Contains additional settings for configuring calls to LUIS.
     */
    constructor(application, luisPredictionOptions, includeApiResults) {
        this.luisRecognizer = new LuisRecognizer(application, luisPredictionOptions, true);
    }

    /**
     * Every conversation turn calls this method.
     * @param {TurnContext} turnContext Contains all the data needed for processing the conversation turn.
     */
    async onTurn(turnContext) {
        // By checking the incoming Activity type, the bot only calls LUIS in appropriate cases.
        if (turnContext.activity.type === ActivityTypes.Message) {
            // Perform a call to LUIS to retrieve results for the user's message.
            const results = await this.luisRecognizer.recognize(turnContext);

            // Since the LuisRecognizer was configured to include the raw results, get the `topScoringIntent` as specified by LUIS.
            const topIntent = results.luisResult.topScoringIntent;

            if (topIntent.intent !== 'None') {
                await turnContext.sendActivity(`LUIS Top Scoring Intent: ${ topIntent.intent }, Score: ${ topIntent.score }`);
            } else {
                // If the top scoring intent was "None" tell the user no valid intents were found and provide help.
                await turnContext.sendActivity(`No LUIS intents were found.
                                                \nThis sample is about identifying two user intents:
                                                \n - 'Calendar.Add'
                                                \n - 'Calendar.Find'
                                                \nTry typing 'Add Event' or 'Show me tomorrow'.`);
            }
        } else if (turnContext.activity.type === ActivityTypes.ConversationUpdate &&
            turnContext.activity.recipient.id !== turnContext.activity.membersAdded[0].id) {
            // If the Activity is a ConversationUpdate, send a greeting message to the user.
            await turnContext.sendActivity('Welcome to the NLP with LUIS sample! Send me a message and I will try to predict your intent.');
        } else if (turnContext.activity.type !== ActivityTypes.ConversationUpdate) {
            // Respond to all other Activity types.
            await turnContext.sendActivity(`[${ turnContext.activity.type }]-type activity detected.`);
        }
    }
}

module.exports.LuisBot = LuisBot;
```

Die LUIS-Erkennung gibt Informationen dazu zurück, wie gut die Äußerung mit den verfügbaren Absichten übereingestimmt hat. Die `luisResult.intents`-Eigenschaft des Ergebnisobjekts enthält ein Array mit den bewerteten Absichten. Die `luisResult.topScoringIntent`-Eigenschaft des Ergebnisobjekts enthält die Absicht mit der besten Bewertung und die entsprechende Punktzahl.

---

## <a name="extract-entities"></a>Extrahieren von Entitäten

Neben dem Erkennen von Absichten können mit einer LUIS-App auch Entitäten extrahiert werden, bei denen es sich um wichtige Wörter für die Erfüllung der Anforderung eines Benutzers handelt. Für einen Wetter-Bot kann die LUIS-App beispielsweise den Ort für den Wetterbericht aus der Nachricht des Benutzers extrahieren.

Eine gängige Methode zur Strukturierung von Konversationen ist das Identifizieren von Entitäten in der Nachricht des Benutzers und das Abfragen der benötigten Entitäten, die nicht gefunden werden. Die nachfolgenden Schritte verarbeiten dann die Antwort auf die Eingabeaufforderung.

<!--Snip
# [C#](#tab/cs)

Let's say the message from the user was "What's the weather in Seattle"? The [LuisRecognizer](https://docs.microsoft.com/en-us/dotnet/api/microsoft.bot.builder.ai.luis.luisrecognizer) gives you a [RecognizerResult](https://docs.microsoft.com/en-us/dotnet/api/microsoft.bot.builder.core.extensions.recognizerresult) with an [`Entities` property](https://docs.microsoft.com/en-us/dotnet/api/microsoft.bot.builder.core.extensions.recognizerresult#properties-) that has this structure:

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

The following helper function can be added to your bot to get entities out of the `RecognizerResult` from LUIS. It will require the use of the `Newtonsoft.Json.Linq` library, which you'll have to add to your **using** statements.

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

When gathering information like entities from multiple steps in a conversation, it can be helpful to save the information you need in your state. If an entity is found, it can be added to the appropriate state field. In your conversation if the current step already has the associated field completed, the step to prompt for that information can be skipped.

# [JavaScript](#tab/js)

Let's say the message from the user was "What's the weather in Seattle"? The [LuisRecognizer](https://docs.microsoft.com/en-us/javascript/api/botbuilder-ai/luisrecognizer) gives you a [RecognizerResult](https://docs.microsoft.com/en-us/javascript/api/botbuilder-core-extensions/recognizerresult) with an `entities` property that has this structure:

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

This `findEntities` function looks for any entities recognized by the LUIS app that match the incoming `entityName`.

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
/Snip-->

Wenn Sie Informationen wie z. B. Entitäten aus mehreren Schritten in einer Konversation erfassen, kann es hilfreich sein, die benötigten Informationen im Zustand zu speichern. Wenn eine Entität gefunden wird, kann sie dem entsprechenden Zustandsfeld hinzugefügt werden. Wenn das zugeordnete Feld im aktuellen Schritt Ihrer Konversation bereits ausgefüllt ist, kann der Schritt, in dem zur Eingabe dieser Informationen aufgefordert wird, ausgelassen werden.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

Ein Beispiel mit LUIS finden Sie in den Projekten für [[C#](https://aka.ms/cs-luis-sample)] oder [[JavaScript](https://aka.ms/js-luis-sample)].

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Kombinieren von LUIS- und QnA-Diensten mit dem Dispatch-Tool](./bot-builder-tutorial-dispatch.md)
