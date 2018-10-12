---
title: Verwenden von LUIS für Language Understanding | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie LUIS zum Verstehen natürlicher Sprache mit dem Bot Builder SDK verwenden können.
keywords: Language Understanding, LUIS, absicht, erkennung, entitäten, middleware
author: ivorb
ms.author: v-ivorb
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 09/19/18
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: c4a7cfba6f588c95dbebf4886ffd7e432d99c3ae
ms.sourcegitcommit: 3cb288cf2f09eaede317e1bc8d6255becf1aec61
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/27/2018
ms.locfileid: "47389800"
---
# <a name="using-luis-for-language-understanding"></a>Verwenden von LUIS für Language Understanding

[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

Die Fähigkeit zu verstehen, was Ihr Benutzer in einer Konversation und im Kontext meint, kann eine schwierige Aufgabe sein, kann Ihrem Bot aber ein natürlicheres Sprachgefühl geben. Language Understanding, genannt LUIS, ermöglicht es Ihnen, genau das zu tun, sodass Ihr Bot die Absicht von Benutzernachrichten bestimmen, mehr natürliche Sprache von Ihrem Benutzer erkennen und den Konversationsablauf besser steuern kann. Weitere Hintergrundinformationen zur Integration von LUIS in einen Bot finden Sie unter [Language Understanding für Bots](./bot-builder-concept-LUIS.md). 

Dieses Thema behandelt die Einrichtung einfacher Bots, die LUIS verwenden, um verschiedene Absichten zu erkennen.

## <a name="installing-packages"></a>Installieren von Paketen

Stellen Sie zunächst sicher, dass Sie über die erforderlichen Pakete für LUIS verfügen.

# <a name="ctabcs"></a>[C#](#tab/cs)

[Fügen Sie einen Verweis](https://docs.microsoft.com/en-us/nuget/tools/package-manager-ui) auf die v4-Version der folgenden NuGet-Pakete hinzu:


* `Microsoft.Bot.Builder.AI.LUIS`

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)

Installieren Sie die Pakete „botbuilder“ und „botbuilder-ai“ mithilfe von npm in Ihrem Projekt:

* `npm install --save botbuilder`
* `npm install --save botbuilder-ai`

---

## <a name="set-up-your-luis-app"></a>Einrichten Ihrer LUIS-App

Richten Sie zuerst eine _LUIS-App_ ein. Dies ist ein Dienst, den Sie auf [luis.ai](https://www.luis.ai) erstellen. Diese LUIS-App kann hinsichtlich bestimmter Absichten trainiert werden, die sie erkennen soll. Informationen zum Erstellen einer LUIS-App finden Sie auf der LUIS-Website.

Für dieses Beispiel verwenden Sie einfach eine Demo-LUIS-App, die die Absichten „Help“ (Hilfe), „Cancel“ (Abbrechen) und „Weather“ (Wetter) erkennen kann; die App-ID ist bereits im Beispielcode enthalten. Sie benötigen einen Cognitive Services-Schlüssel. Diesen erhalten Sie, indem Sie sich auf [luis.ai](https://www.luis.ai) anmelden und den Schlüssel unter **Benutzereinstellungen** > **Authoring Key** (Erstellungsschlüssel) kopieren.

> [!NOTE]
> Um eine eigene Kopie der in diesem Beispiel verwendeten öffentlichen LUIS-App zu erstellen, kopieren Sie die [JSON](https://github.com/Microsoft/LUIS-Samples/blob/master/examples/simple-bot-example/FirstSimpleBotExample.json)-LUIS-Datei. Anschließend [importieren Sie die LUIS-App](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/create-new-app#import-new-app), [trainieren](https://docs.microsoft.com/en-us/azure/cognitive-services/LUIS/luis-how-to-train) und [veröffentlichen](https://docs.microsoft.com/en-us/azure/cognitive-services/LUIS/publishapp) sie. Ersetzen Sie die öffentlichen App-ID im Beispielcode durch die App-ID Ihrer neue LUIS-App.


### <a name="configure-your-bot-to-call-your-luis-app"></a>Konfigurieren Sie Ihren Bot zum Aufrufen Ihrer LUIS-App.

# <a name="ctabcs"></a>[C#](#tab/cs)

Obwohl es möglich ist, die LUIS-App bei jeder Ausführung zu erstellen und aufzurufen, empfiehlt es sich, den LUIS-Dienst als Singleton zu registrieren und ihn dann als Parameter an den Konstruktor Ihres Bots übergeben. Wir stellen diese Methode hier vor, da sie etwas komplizierter ist.

Starten Sie mit der Echo-Bot-Vorlage, und öffnen Sie **Startup.cs**. 

Fügen Sie eine `using` -Anweisung für `Microsoft.Bot.Builder.AI.LUIS` hinzu.

```csharp
// add this
using Microsoft.Bot.Builder.AI.LUIS;
```

Fügen Sie den folgenden Code am Ende von `ConfigureServices` nach der Zustandsinitialisierung hinzu. Dadurch werden die Informationen aus der `appsettings.json`-Datei abgerufen, diese Zeichenfolgen können jedoch wie im Beispiel unter dem Link am Ende dieses Artikels aus Ihrer `.bot`-Datei erfasst oder zu Testzwecken hartcodiert werden.

Das Singleton gibt eine neue `LuisRecognizer` an den Konstruktor zurück.

```csharp
    // Create and register a LUIS recognizer.
    services.AddSingleton(sp =>
    {
        // Get LUIS information from appsettings.json.
        var section = this.Configuration.GetSection("Luis");
        var luisApp = new LuisApplication(
            applicationId: section["AppId"],
            endpointKey: section["SubscriptionKey"],
            azureRegion: section["Region"]);

        // Specify LUIS options. These may vary for your bot.
        var luisPredictionOptions = new LuisPredictionOptions
        {
            IncludeAllIntents = true,
        };

        return new LuisRecognizer(
            application: luisApp,
            predictionOptions: luisPredictionOptions,
            includeApiResults: true);
    });
```

Fügen Sie den Abonnementschlüssel von [luis.ai](https://www.luis.ai) anstelle von `<subscriptionKey>` ein. Diesen Schlüssel können Sie ermitteln, indem Sie oben rechts auf den Namen Ihres Kontos klicken und dann zu **Einstellungen** navigieren. Der Schlüssel wird dort als Ihr **Authoring Key** (Erstellungsschlüssel) angezeigt.

> [!NOTE]
> Wenn Sie Ihre eigene LUIS-App anstelle der öffentlichen App verwenden, können Sie die ID, den Abonnementschlüssel und die URL für Ihre LUIS-App auf [luis.ai](https://www.luis.ai) einsehen. Sie finden diese Informationen auf den Registerkarten **Veröffentlichen** und **Einstellungen** der Seite Ihrer App.
>
>Die zu verwendende Basis-URL finden Sie in Ihrem `LuisModel`. Melden Sie sich auf [luis.ai](https://www.luis.ai) an, und wechseln Sie zur Registerkarte **Veröffentlichen**. Die Basis-URL wird in der Spalte **Endpunkt** unter **Ressourcen und Schlüssel** angezeigt. Die Basis-URL ist der Teil der **Endpunkt-URL** vor der Abonnement-ID und anderen Parametern.

Als Nächstes müssen Sie diese LUIS-Instanz für Ihren Bot bereitstellen. Öffnen Sie **EchoBot.cs**, und fügen Sie am Anfang der Datei den folgenden Code hinzu. Die Klassenüberschrift und die Zustandselemente sind zu Referenzzwecken ebenfalls enthalten, sie werden hier jedoch nicht erläutert.

```csharp
public class EchoBot : IBot
{
    /// <summary>
    /// Gets the Echo Bot state.
    /// </summary>
    private IStatePropertyAccessor<EchoState> EchoStateAccessor { get; }

    /// <summary>
    /// Gets the LUIS recognizer.
    /// </summary>
    private LuisRecognizer Recognizer { get; } = null;

    public EchoBot(ConversationState state, LuisRecognizer luis)
    {
        EchoStateAccessor = state.CreateProperty<EchoState>("EchoBot.EchoState");

        // The incoming luis variable is the LUIS Recognizer we added above.
        this.Recognizer = luis ?? throw new ArgumentNullException(nameof(luis));
    }

    /// ...
```

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)

Führen Sie zuerst die Schritte im JavaScript-[Schnellstart](../javascript/bot-builder-javascript-quickstart.md) aus, um einen Bot zu erstellen. Die LUIS-Informationen werden hier im Bot hartcodiert, sie können aber wie im Beispiel unter dem Link am Ende dieses Artikels aus der `.bot`-Datei gepullt werden.

Bearbeiten Sie im neuen Bot die Datei **app.js**, um die `LuisRecognizer`-Klasse als erforderlich festzulegen, und erstellen Sie eine Instanz für das LUIS-Modell:

```javascript
const { ActivityTypes } = require('botbuilder');
const { LuisRecognizer } = require('botbuilder-ai');

const luisApplication = {
    // This appID is for a public app that's made available for demo purposes
    // You can use it or use your own LUIS "Application ID" at https://www.luis.ai under "App Settings".
     applicationId: 'eb0bf5e0-b468-421b-9375-fdfb644c512e',
    // Replace endpointKey with your "Subscription Key"
    // your key is at https://www.luis.ai under Publish > Resources and Keys, look in the Endpoint column
    // The "subscription-key" is embeded in the Endpoint link. 
    endpointKey: '<your subscription key>',
    // You can find your app's region info embeded in the Endpoint link as well.
    // Some examples of regions are `westus`, `westcentralus`, `eastus2`, and `southeastasia`.
    azureRegion: 'westus'
}

// Create configuration for LuisRecognizer's runtime behavior.
const luisPredictionOptions = {
    includeAllIntents: true,
    log: true,
    staging: false
}

// Create the bot that handles incoming Activities.
const luisBot = new LuisBot(luisApplication, luisPredictionOptions);
```

Rufen Sie anschließend im Konstruktor Ihres Bots `LuisBot` die Anwendung zum Erstellen der LuisRecognizer-Instanz ab.

```javascript
    /**
     * The LuisBot constructor requires one argument (`application`) which is used to create an instance of `LuisRecognizer`.
     * @param {object} luisApplication The basic configuration needed to call LUIS. In this sample the configuration is retrieved from the .bot file.
     * @param {object} luisPredictionOptions (Optional) Contains additional settings for configuring calls to LUIS.
     */
    constructor(application, luisPredictionOptions) {
        this.luisRecognizer = new LuisRecognizer(application, luisPredictionOptions, true);
    }
```

> [!NOTE] 
> Wenn Sie Ihre eigene LUIS-App anstelle der öffentlichen App verwenden, können Sie die Anwendungs-ID, den Abonnementschlüssel und die URL für Ihre LUIS-App auf [https://www.luis.ai](https://www.luis.ai) einsehen. Sie finden diese Informationen auf den Registerkarten „Veröffentlichen“ und „Einstellungen“ der Seite Ihrer App.

---

LUIS (Language Understanding) ist jetzt für Ihren Bot konfiguriert. Als Nächstes befassen wir uns damit, wie Sie die Absicht von LUIS abrufen können.

## <a name="get-the-intent-by-calling-luis"></a>Abrufen der Absicht durch Aufrufen von LUIS

Ihr Bot ruft Ergebnisse aus LUIS ab, indem er die LUIS-Erkennung aufruft.

# <a name="ctabcs"></a>[C#](#tab/cs)

Damit Ihr Bot einfach basierend auf der von der LUIS-App erkannten Absicht eine Antwort sendet, rufen Sie die `LuisRecognizer` auf, um ein `RecognizerResult` zu erhalten. Diesen Aufruf können Sie jederzeit im Code verwenden, wenn Sie die LUIS-Absicht abrufen müssen.

```cs
using System.Threading.Tasks;
using Microsoft.Bot;
using Microsoft.Bot.Builder;
using Microsoft.Bot.Builder.Core.Extensions;
using Microsoft.Bot.Schema;
// add this reference
using Microsoft.Bot.Builder.AI.LUIS;

namespace EchoBot
{
    public class EchoBot : IBot
    {
        /// <summary>
        /// Echo bot turn handler 
        /// </summary>
        /// <param name="context">Turn scoped context containing all the data needed
        /// for processing this conversation turn. </param>        
        public async Task OnTurnAsync(ITurnContext context, System.Threading.CancellationToken token)
        {            
            // This bot is only handling Messages
            if (context.Activity.Type == ActivityTypes.Message)
            {
                // Call LUIS recognizer
                var result = this.Recognizer.RecognizeAsync(context, System.Threading.CancellationToken.None);
                
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
                        // Report the weather.
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

Alle in der Äußerung erkannten Absichten werden als Zuordnung von Absichtsbezeichnungen zu den Ergebnissen zurückgegeben und können von `result.Intents` aus aufgerufen werden. Es wird eine statische `LuisRecognizer.topIntent()`-Methode wird zur Verfügung, um die Suche nach der Absicht mit der besten Bewertung für eine Ergebnismenge zu vereinfachen.

Alle erkannten Entitäten werden als Zuordnung von Entitätsnamen zu Werten zurückgegeben und mit `results.entities` aufgerufen. Zusätzliche Entitätsmetadaten können durch Übergabe einer `verbose=true`-Einstellung beim Erstellen des LuisRecognizers zurückgegeben werden. Die hinzugefügten Metadaten können dann mit `results.entities.$instance` aufgerufen werden.

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)

Bearbeiten Sie den Code zum Lauschen auf eingehende Aktivitäten, sodass er `LuisRecognizer` aufruft, um ein `RecognizerResult` abzurufen.

```javascript
const { ActivityTypes } = require('botbuilder');
const { LuisRecognizer } = require('botbuilder-ai');

// Listen for incoming requests 
server.post('/api/messages', (req, res) => {
    // Route received request to adapter for processing
    adapter.processActivity(req, res, async (context) => {
        if (context.activity.type === 'message') {
            // Perform a call to LUIS to retrieve results for the user's message.
            const results = await this.luisRecognizer.recognize(turnContext);

            // Since the LuisRecognizer was configured to include the raw results, get the `topScoringIntent` as specified by LUIS.
            const topIntent = results.luisResult.topScoringIntent;
            
            switch (topIntent) {
                case 'None':
                    await context.sendActivity("Sorry, I don't understand.")
                    break;
                case 'Cancel':
                    await context.sendActivity("<cancelling the process>")
                    break;
                case 'Help':
                    await context.sendActivity("<here's some help>");
                    break;
                case 'Weather':
                    await context.sendActivity("The weather today is sunny.");
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


---

Versuchen Sie, den Bot im Bot Framework-Emulator auszuführen, und sagen Sie etwas wie „Weather“ (Wetter), „Help“ (Hilfe) oder „Cancel“ (Abbrechen).

![Ausführen des Bots](./media/how-to-luis/run-luis-bot.png)

## <a name="extract-entities"></a>Extrahieren von Entitäten

Neben dem Erkennen von Absichten können mit einer LUIS-App auch Entitäten extrahiert werden, bei denen es sich um wichtige Wörter für die Erfüllung der Anforderung eines Benutzers handelt. Für einen Wetter-Bot kann die LUIS-App beispielsweise den Ort für den Wetterbericht aus der Nachricht des Benutzers extrahieren.

Eine gängige Methode zur Strukturierung von Konversationen ist das Identifizieren von Entitäten in der Nachricht des Benutzers und das Abfragen der benötigten Entitäten, die nicht gefunden werden. Die nachfolgenden Schritte verarbeiten dann die Antwort auf die Eingabeaufforderung.

# <a name="ctabcs"></a>[C#](#tab/cs)

Die Nachricht des Benutzers lautete beispielsweise „What's the weather in Seattle?“ (Wie ist das Wetter in Seattle?). In diesem Fall erhalten Sie von der `LuisRecognizer` ein `RecognizerResult` mit einer `Entities`-Eigenschaft, das die folgende Struktur aufweist:

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

Die folgende Hilfsfunktion kann dem Bot hinzugefügt werden, um Entitäten aus dem `RecognizerResult` von LUIS abzurufen. Diese Funktion erfordert die Verwendung der Bibliothek `Newtonsoft.Json.Linq`, die Sie Ihren **using**-Anweisungen hinzufügen müssen.

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

Wenn Sie Informationen wie z. B. Entitäten aus mehreren Schritten in einer Konversation erfassen, kann es hilfreich sein, die benötigten Informationen im Zustand zu speichern. Wenn eine Entität gefunden wird, kann sie dem entsprechenden Zustandsfeld hinzugefügt werden. Wenn das zugeordnete Feld im aktuellen Schritt Ihrer Konversation bereits ausgefüllt ist, kann der Schritt, in dem zur Eingabe dieser Informationen aufgefordert wird, ausgelassen werden.

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)

Die Nachricht des Benutzers lautete beispielsweise „What's the weather in Seattle?“ (Wie ist das Wetter in Seattle?). In diesem Fall erhalten Sie von der `LuisRecognizer` ein `RecognizerResult` mit einer `entities`-Eigenschaft, das die folgende Struktur aufweist:

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

Diese `findEntities`-Funktion sucht nach allen von der LUIS-App erkannten Entitäten, die dem eingehenden `entityName` entsprechen.


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

Wenn Sie Informationen wie z. B. Entitäten aus mehreren Schritten in einer Konversation erfassen, kann es hilfreich sein, die benötigten Informationen im Zustand zu speichern. Wenn eine Entität gefunden wird, kann sie dem entsprechenden Zustandsfeld hinzugefügt werden. Wenn das zugeordnete Feld im aktuellen Schritt Ihrer Konversation bereits ausgefüllt ist, kann der Schritt, in dem zur Eingabe dieser Informationen aufgefordert wird, ausgelassen werden.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

Ein Beispiel mit LUIS finden Sie in den Projekten für [[C#](https://aka.ms/cs-luis-sample)] oder [[JavaScript](https://aka.ms/js-luis-sample)].

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Kombinieren von LUIS- und QnA-Diensten mit dem Dispatch-Tool](./bot-builder-tutorial-dispatch.md)
