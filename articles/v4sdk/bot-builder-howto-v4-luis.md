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
ms.date: 11/28/18
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: a512cb92f35374b457c4d4cef05667edbd8d2f1f
ms.sourcegitcommit: 892bf81d306ba607c293ee8639d5c6b66ab3710a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/28/2018
ms.locfileid: "52460009"
---
# <a name="add-natural-language-understanding-to-your-bot"></a>Hinzufügen von Features zum Verstehen natürlicher Sprache zu Ihrem Bot

[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

Die Fähigkeit zu verstehen, was Ihr Benutzer in einer Konversation und im Kontext meint, kann eine schwierige Aufgabe sein, Ihrem Bot aber ein natürlicheres Sprachgefühl verleihen. Language Understanding, genannt LUIS, ermöglicht es Ihnen, genau das zu tun, sodass Ihr Bot die Absicht von Benutzernachrichten bestimmen, mehr natürliche Sprache von Ihrem Benutzer erkennen und den Konversationsablauf besser steuern kann. In diesem Thema wird die Einrichtung eines einfachen Bots behandelt, für den LUIS verwendet wird, um verschiedene Absichten zu erkennen. 
## <a name="prerequisites"></a>Voraussetzungen
- [luis.ai](https://www.luis.ai)-Konto
- [Bot Framework-Emulator](https://github.com/Microsoft/BotFramework-Emulator/blob/master/README.md#download)
- Der Code in diesem Artikel basiert auf dem Beispiel **NLP mit LUIS**. Sie benötigen eine Kopie des Beispiels im [C#](https://aka.ms/cs-luis-sample)- oder [JS](https://aka.ms/js-luis-sample)-Format. 
- Sie müssen mit den [Bot-Grundlagen](bot-builder-basics.md), der [Verarbeitung natürlicher Sprache (Natural Language Processing, NLP)](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/what-is-luis) und der [BOT](bot-file-basics.md)-Datei vertraut sein.

## <a name="create-a-luis-app-in-the-luis-portal"></a>Erstellen einer LUIS-App im LUIS-Portal
Melden Sie sich am LUIS-Portal an, um Ihre eigene Version der LUIS-Beispiel-App zu erstellen. Sie können Ihre Anwendungen auf der Seite **Meine Apps** erstellen und verwalten. 

1. Wählen Sie **Import new app** (Neue App importieren). 
1. Klicken Sie auf **Choose App file (JSON format)...** (App-Datei wählen (JSON-Format)...). 
1. Wählen Sie die Datei `reminders.json`, die im Ordner `CognitiveModels` des Beispiels enthalten ist. Geben Sie unter **Optional Name** (Optionaler Name) den Namen **LuisBot** ein. Diese Datei enthält drei Absichten: „Calendar_Add“, „Calendar_Find“ und „None“. Wir verwenden diese Absichten, um zu verstehen, was der Benutzer gemeint hat, als er eine Nachricht an den Bot gesendet hat. Wenn Sie Entitäten einbinden möchten, helfen Ihnen die Informationen im [Abschnitt „Optional“](#optional---extract-entities) am Ende dieses Artikels weiter.
1. [Trainieren](https://docs.microsoft.com/en-us/azure/cognitive-services/LUIS/luis-how-to-train) Sie die App.
1. [Veröffentlichen](https://docs.microsoft.com/en-us/azure/cognitive-services/LUIS/publishapp) Sie die App in der *Produktionsumgebung*.

### <a name="obtain-values-to-connect-to-your-luis-app"></a>Abrufen von Werten zum Herstellen einer Verbindung mit Ihrer LUIS-App

Nachdem Ihre LUIS-App veröffentlicht wurde, können Sie von Ihrem Bot aus darauf zugreifen. Sie müssen sich mehrere Werte notieren, um von Ihrem Bot aus auf Ihre LUIS-App zugreifen zu können. Sie können diese Informationen mit dem LUIS-Portal abrufen.

#### <a name="retrieve-application-information-from-the-luisai-portal"></a>Abrufen von Anwendungsinformationen aus dem Portal „LUIS.ai“
Die BOT-Datei dient als Ort, an dem alle Dienstverweise zentral zusammengefasst werden. Die von Ihnen abgerufenen Informationen werden der BOT-Datei im nächsten Abschnitt hinzugefügt. 
1. Wählen Sie Ihre veröffentlichte LUIS-App unter [luis.ai](https://www.luis.ai) aus.
1. Wählen Sie bei geöffneter veröffentlichter LUIS-App die Registerkarte **VERWALTEN**.
1. Wählen Sie auf der linken Seite die Registerkarte **Anwendungsinformationen**, und notieren Sie sich den Wert, der unter _Anwendungs-ID_ angezeigt wird, als <IHRE_APP-ID>.
1. Wählen Sie auf der linken Seite die Registerkarte **Schlüssel und Endpunkte**, und notieren Sie sich den Wert unter _Erstellungsschlüssel_ als <IHREN_ERSTELLUNGSSCHLÜSSEL>. Beachten Sie, dass *Ihr Abonnementschlüssel* mit *Ihrem Erstellungsschlüssel* identisch ist. 
1. Scrollen Sie nach unten an das Ende der Seite, und notieren Sie sich den Wert, der für _Region_ angezeigt wird, als <IHRE_REGION>.
1. Notieren Sie sich den Wert unter _Endpunkt_ als <IHREN_ENDPUNKT>.

#### <a name="update-the-bot-file"></a>Aktualisieren der Bot-Datei
Fügen Sie die Informationen, die zum Zugreifen auf Ihre LUIS-App erforderlich sind, z.B. Anwendungs-ID, Erstellungsschlüssel, Abonnementschlüssel, Endpunkt und Region, der Datei `nlp-with-luis.bot` hinzu. Dies sind die Werte, die Sie zuvor aus Ihrer veröffentlichten LUIS-App gespeichert haben.

```json
{
    "name": "LuisBot",
    "description": "",
    "services": [
        {
            "type": "endpoint",
            "name": "development",
            "endpoint": "http://localhost:3978/api/messages",
            "appId": "",
            "appPassword": "",
            "id": "166"
        },
        {
            "type": "luis",
            "name": "LuisBot",
            "appId": "<luis appid>",
            "version": "0.1",
            "authoringKey": "<luis authoring key>",
            "subscriptionKey": "<luis subscription key>",
            "region": "<luis region>",
            "id": "158"
        }
    ],
    "padlock": "",
    "version": "2.0"
}
```
# <a name="ctabcs"></a>[C#](#tab/cs)

### <a name="configure-your-bot-to-use-your-luis-app"></a>Konfigurieren Sie Ihren Bot für die Verwendung Ihrer LUIS-App

Als Nächstes initialisieren wir eine neue Instanz der BotService-Klasse in `BotServices.cs`, mit der die obigen Informationen aus Ihrer `.bot`-Datei abgerufen werden. Der externe Dienst wird mit der `BotConfiguration`-Klasse konfiguriert.

```csharp
public class BotServices
{
    // Initializes a new instance of the BotServices class
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

    // Gets the set of LUIS Services used. LuisServices is represented as a dictionary.  
    public Dictionary<string, LuisRecognizer> LuisServices { get; } = new Dictionary<string, LuisRecognizer>();
}
```

Registrieren Sie die LUIS-App anschließend als Singleton in der Datei `Startup.cs`, indem Sie den folgenden Code in der `ConfigureServices`-Methode verwenden.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    var secretKey = Configuration.GetSection("botFileSecret")?.Value;
    var botFilePath = Configuration.GetSection("botFilePath")?.Value;

    // Loads .bot configuration file and adds a singleton that your Bot can access through dependency injection.
    var botConfig = BotConfiguration.Load(botFilePath ?? @".\nlp-with-luis.bot", secretKey);
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
        
        // ...
    });
}
```

Als Nächstes ruft der Bot in der Datei `Luis.cs` diese LUIS-Instanz ab.

```csharp
public class LuisBot : IBot
{
    public static readonly string LuisKey = "LuisBot";
    private const string WelcomeText = "This bot will introduce you to natural language processing with LUIS. Type an utterance to get started";

    // Services configured from the ".bot" file.
    private readonly BotServices _services;

    // Initializes a new instance of the LuisBot class.
    public LuisBot(BotServices services)
    {
        _services = services ?? throw new System.ArgumentNullException(nameof(services));
        if (!_services.LuisServices.ContainsKey(LuisKey))
        {
            throw new System.ArgumentException($"Invalid configuration....");
        }
    }
    // ...
}
```

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)

In unserem Beispiel befindet sich der Startcode in der Datei **index.js** und der Code für die Botlogik in der Datei **bot.js**. Zusätzliche Konfigurationsinformationen sind in der Datei **nlp-with-luis.bot** enthalten.

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

### <a name="get-the-intent-by-calling-luis"></a>Abrufen der Absicht durch Aufrufen von LUIS

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

### <a name="test-the-bot"></a>Testen des Bots

1. Führen Sie das Beispiel lokal auf Ihrem Computer aus. Wenn Sie eine Anleitung benötigen, helfen Ihnen die Infodateien für [C#](https://github.com/Microsoft/BotBuilder-Samples/blob/master/samples/csharp_dotnetcore/12.nlp-with-luis/README.md) bzw. [JS](https://github.com/Microsoft/BotBuilder-Samples/blob/master/samples/javascript_nodejs/12.nlp-with-luis/README.md) weiter.

1. Geben Sie im Emulator wie unten gezeigt eine Nachricht ein. 

![Eingabe zum Testen des NLP-Beispiels](./media/nlp-luis-sample-message.png)

Der Bot antwortet mit der Absicht, die über die höchste Bewertung verfügt. In diesem Fall ist dies die Absicht „Calendar_Add“. Zur Erinnerung: In der Datei `reminders.json`, die Sie im luis.ai-Portal importiert haben, sind die Absichten „Calendar_Add“, „Calendar_Find“ und „None“ definiert. 

![Antwort zum Testen des NLP-Beispiels](./media/nlp-luis-sample-response.png) 

Ein Vorhersageergebnis gibt den Grad der Zuverlässigkeit an, den LUIS in die Ergebnisse von Vorhersagen hat. Ein Vorhersageergebnis liegt zwischen 0 (null) und 1 (eins). Ein Beispiel für eine hohe Zuverlässigkeitsbewertung von LUIS ist der Wert 0,99. Ein Beispiel für eine Bewertung mit niedriger Zuverlässigkeit ist 0,01. 

## <a name="optional---extract-entities"></a>Optional: Extrahieren von Entitäten

Neben dem Erkennen der Benutzerabsicht kann eine LUIS-App auch Entitäten zurückgeben. Entitäten sind wichtige Wörter, die sich auf die Absicht beziehen. Sie können unter Umständen sehr wichtig dafür sein, die Anfrage eines Benutzers zu erfüllen oder für Ihren Bot ein intelligenteres Verhalten zu ermöglichen. 

### <a name="why-use-entities"></a>Gründe für die Verwendung von Entitäten

Mit LUIS-Entitäten kann Ihr Bot auf intelligente Weise bestimmte Dinge oder Ereignisse verstehen, die von Standardabsichten abweichen. So können Sie zusätzliche Informationen vom Benutzer sammeln, um die Antwort des Bots intelligenter zu gestalten oder auch bestimmte Fragen zu überspringen, mit denen diese Informationen vom Benutzer abgefragt werden. Bei einem Wetter-Bot kann für die LUIS-App beispielsweise die Entität _Location_ verwendet werden, um aus der Nachricht des Benutzers den Ort für den angeforderten Wetterbericht zu extrahieren. Der Bot kann dann die Frage nach dem Standort des Benutzers überspringen und die Konversation mit dem Benutzer intelligenter und kürzer gestalten.

### <a name="prerequisites"></a>Voraussetzungen

Zum Verwenden von Entitäten mit diesem Beispiel müssen Sie eine LUIS-App erstellen, die Entitäten enthält. Führen Sie die Schritte im obigen Abschnitt zur [Erstellung der LUIS-App](#create-a-luis-app-in-the-luis-portal) aus. Verwenden Sie dabei aber nicht die Datei `reminders.json`, sondern die Datei [reminders-with-entities.json](https://github.com/Microsoft/BotFramework-Samples/tree/master/SDKV4-Samples/dotnet_core/nlp-with-luis), um Ihre LUIS-App zu erstellen. Diese Datei enthält die gleichen Absichten sowie zusätzlich drei weitere Entitäten („Appointment“ (Termin), „Meeting“ (Besprechung) und „Schedule“ (Zeitplan)). Diese Entitäten unterstützen LUIS bei der Ermittlung der Absicht in der Nachricht Ihres Benutzers. 

### <a name="extract-and-display-entities"></a>Extrahieren und Anzeigen von Entitäten
Der folgende optionale Code kann dieser Beispiel-App hinzugefügt werden, um die Entitätsinformationen zu extrahieren und anzuzeigen, wenn eine Entität von LUIS verwendet wird, um die Absicht des Benutzers zu identifizieren. 

# <a name="ctabcs"></a>[C#](#tab/cs)

Die folgende Hilfsfunktion kann dem Bot hinzugefügt werden, um Entitäten aus dem `RecognizerResult` von LUIS abzurufen. Diese Funktion erfordert die Verwendung der Bibliothek `Newtonsoft.Json.Linq`, die Sie Ihren **using**-Anweisungen hinzufügen müssen. Wenn beim Analysieren des JSON-Codes, der von LUIS zurückgegeben wird, Entitätsinformationen gefunden werden, konvertiert die Newtonsoft-Funktion _DeserializeObject_ diesen JSON-Code in ein dynamisches Objekt und ermöglicht so den Zugriff auf die Entitätsinformationen.

```cs
using Newtonsoft.Json.Linq;

private string ParseLuisForEntities(RecognizerResult recognizerResult)
{
   var result = string.Empty;

   // recognizerResult.Entities returns type JObject.
   foreach (var entity in recognizerResult.Entities)
   {
       // Parse JObject for a known entity types: Appointment, Meeting, and Schedule.
       var appointFound = JObject.Parse(entity.Value.ToString())["Appointment"];
       var meetingFound = JObject.Parse(entity.Value.ToString())["Meeting"];
       var schedFound = JObject.Parse(entity.Value.ToString())["Schedule"];

       // We will return info on the first entity found.
       if (appointFound != null)
       {
           // use JsonConvert to convert entity.Value to a dynamic object.
           dynamic o = JsonConvert.DeserializeObject<dynamic>(entity.Value.ToString());
           if (o.Appointment[0] != null)
           {
              // Find and return the entity type and score.
              var entType = o.Appointment[0].type;
              var entScore = o.Appointment[0].score;
              result = "Entity: " + entType + ", Score: " + entScore + ".";
              
              return result;
            }
        }

        if (meetingFound != null)
        {
            // use JsonConvert to convert entity.Value to a dynamic object.
            dynamic o = JsonConvert.DeserializeObject<dynamic>(entity.Value.ToString());
            if (o.Meeting[0] != null)
            {
                // Find and return the entity type and score.
                var entType = o.Meeting[0].type;
                var entScore = o.Meeting[0].score;
                result = "Entity: " + entType + ", Score: " + entScore + ".";
                
                return result;
            }
        }

        if (schedFound != null)
        {
            // use JsonConvert to convert entity.Value to a dynamic object.
            dynamic o = JsonConvert.DeserializeObject<dynamic>(entity.Value.ToString());
            if (o.Schedule[0] != null)
            {
                // Find and return the entity type and score.
                var entType = o.Schedule[0].type;
                var entScore = o.Schedule[0].score;
                result = "Entity: " + entType + ", Score: " + entScore + ".";
                
                return result;
            }
        }
    }

    // No entities were found, empty string returned.
    return result;
}
```

Diese erkannten Entitätsinformationen können dann zusammen mit der identifizierten Benutzerabsicht angezeigt werden. Fügen Sie zum Anzeigen dieser Informationen der Aufgabe _OnTurnAsync_ des Beispielcodes die folgenden Codezeilen hinzu, und zwar direkt nach dem Anzeigen der Absichtsinformationen.

```cs
// See if LUIS found and used an entity to determine user intent.
var entityFound = ParseLuisForEntities(recognizerResult);

// Inform the user if LUIS used an entity.
if (entityFound.ToString() != string.Empty)
{
   await turnContext.SendActivityAsync($"==>LUIS Entity Found: {entityFound}\n");
}
else
{
   await turnContext.SendActivityAsync($"==>No LUIS Entities Found.\n");
}
```
# <a name="javascripttabjs"></a>[JavaScript](#tab/js)

Der folgende Code kann Ihrem Bot hinzugefügt werden, um alle Entitätsinformationen aus dem `luisRecognizer`-Ergebnis zu extrahieren, das von LUIS zurückgegeben wird. Fügen Sie bei der `onTurn`-Verarbeitung der Codebeispieldatei „bot.js“ die folgende Zeile direkt nach der Deklaration der Konstante _topIntent_ hinzu. Hiermit werden alle zurückgegebenen Entitätsinformationen erfasst: 

```javascript
// Since the LuisRecognizer was configured to include the raw results, get returned entity data.
var entityData = results.luisResult.entities;

```

Fügen Sie zum Anzeigen der zurückgegebenen Entitätsinformationen für den Benutzer die folgenden Codezeilen direkt nach dem _sendActivity_-Aufruf hinzu, der im Beispielcode verwendet wird. Er dient dazu, den Benutzer zu informieren, wenn ein topIntent-Element gefunden wurde.

```javascript
// See if LUIS found and used an entity to determine user intent.
if (entityData.length > 0)
{
   if ((entityData[0].type == "Appointment") || (entityData[0].type == "Meeting") || (entityData[0].type == "Schedule") )
   {
      // inform user if LUIS used an entity.
      await turnContext.sendActivity(`LUIS Entity Found: Entity: ${entityData[0].entity}, Score: ${entityData[0].score}.`);
   }
}
else{
       await turnContext.sendActivity(`No LUIS Entities Found.`);
}
```

Mit diesem Code wird zuerst überprüft, ob im zurückgegebenen Ergebnis Entitätsinformationen von LUIS enthalten waren. Wenn ja, werden die Informationen zur ersten erkannten Entität angezeigt.

---

### <a name="test-bot-with-entities"></a>Testen des Bots mit Entitäten

1. Führen Sie zum Testen Ihres Bots, wenn Entitäten enthalten sind, das Beispiel wie [oben](#test-the-bot) beschrieben lokal aus.

1. Geben Sie im Emulator die unten angezeigte Nachricht ein. 

![Eingabe zum Testen des NLP-Beispiels](./media/nlp-luis-sample-message.png)

Der Bot antwortet mit der am höchsten bewerteten Absicht „Calendar_Add“ und mit der Entität „Meetings“, die von LUIS zum Ermitteln der Benutzerabsicht verwendet wurde.

![Antwort zum Testen des NLP-Beispiels](./media/nlp-luis-sample-entity-response.png) 

Mit der Erkennung von Entitäten kann die Gesamtleistung Ihres Bots verbessert werden. Die Erkennung der Entität „Meeting“ (oben dargestellt) kann es Ihrer Anwendung beispielsweise ermöglichen, nun eine spezielle Funktion aufzurufen, die für die Erstellung einer neuen Besprechung im Kalender des Benutzers konzipiert ist.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Verwenden von QnA Maker zum Beantworten von Fragen](./bot-builder-howto-qna.md)
