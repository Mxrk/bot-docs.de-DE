---
title: Verwenden von mehreren LUIS- und QnA-Modellen | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie LUIS und QnA Maker in Ihrem Bot verwenden können.
keywords: LUIS, QnA, Dispatch-Tool, mehrere Dienste, Weiterleiten von Absichten
author: DeniseMak
ms.author: v-demak
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 11/26/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 9b0ddf5cf8af61048ba78f10824c9573da82fc08
ms.sourcegitcommit: a722f960cd0a8513d46062439eb04de3a0275346
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/27/2018
ms.locfileid: "52336270"
---
# <a name="use-multiple-luis-and-qna-models"></a>Verwenden von mehreren LUIS- und QnA-Modellen

[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

In diesem Tutorial wird veranschaulicht, wie Sie den Dispatch-Dienst zum Weiterleiten von Äußerungen verwenden, wenn von einem Bot mehrere LUIS-Modelle und QnA Maker-Dienste für unterschiedliche Szenarien unterstützt werden. In diesem Fall konfigurieren wir den Dispatch-Vorgang mit mehreren LUIS-Modellen für Konversationen im Bereich Gebäudeautomatisierung und Wetterdaten und den QnA Maker-Dienst für die Beantwortung von Fragen basierend auf einer FAQ-Textdatei als Eingabe. In diesem Beispiel werden die folgenden Dienste kombiniert.

| NAME | BESCHREIBUNG |
|------|------|
| HomeAutomation | Eine LUIS-App, die eine Absicht zur Gebäudeautomatisierung mit den zugeordneten Entitätsdaten erkennt.|
| Weather | Eine LUIS-App, die die Absichten `Weather.GetForecast` und `Weather.GetCondition` mit Standortdaten erkennt.|
| Häufig gestellte Fragen  | Eine QnA Maker-Wissensdatenbank, die Antworten auf einfache Fragen zum Bot liefert. |

## <a name="prerequisites"></a>Voraussetzungen

- Der Code in diesem Artikel basiert auf dem Beispiel **NLP mit Dispatch**. Sie benötigen eine Kopie des Beispiels im [C#](https://aka.ms/dispatch-sample-cs)- oder [JS](https://aka.ms/dispatch-sample-js)-Format.
- Sie müssen mit den [Bot-Grundlagen](bot-builder-basics.md), der [Verarbeitung natürlicher Sprache](bot-builder-howto-v4-luis.md), [QnA Maker](bot-builder-howto-qna.md) und der [BOT](bot-file-basics.md)-Datei vertraut sein.
- [Bot Framework Emulator](https://github.com/Microsoft/BotFramework-Emulator/blob/master/README.md#download) für die Durchführung von Tests.

## <a name="create-the-services-and-test-the-bot"></a>Erstellen der Dienste und Testen des Bots

Befolgen Sie die Anleitung in der **INFODATEI** für [C#](https://github.com/Microsoft/BotBuilder-Samples/blob/master/samples/csharp_dotnetcore/14.nlp-with-dispatch/README.md) oder [JS](https://github.com/Microsoft/BotBuilder-Samples/blob/master/samples/javascript_nodejs/14.nlp-with-dispatch/README.md), um das Beispiel mit dem Emulator zu erstellen und auszuführen. 

Zu Referenzzwecken sind hier einige Fragen und Befehle zu den verwendeten Diensten angegeben:

* QnA Maker
  * `hi`, `good morning`
  * `what are you`, `what do you do`
* LUIS (Gebäudeautomatisierung)
  * `turn on bedroom light`
  * `turn off bedroom light`
  * `make some coffee`
* LUIS (Wetter)
  * `whats the weather in redmond washington`
  * `what's the forecast for london`
  * `show me the forecast for nebraska`

### <a name="connecting-to-the-services-from-your-bot"></a>Herstellen einer Verbindung mit den Diensten über Ihren Bot

Zum Herstellen der Verbindung mit den Diensten Dispatch, LUIS und QnA Maker ruft Ihr Bot per Pullvorgang Informationen aus der **BOT**-Datei ab.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

In **Startup.cs** wird die Konfigurationsdatei mit `ConfigureServices` eingelesen, und diese Informationen werden von `InitBotServices` zum Initialisieren der Dienste verwendet. Bei jeder Erstellung wird der Bot mit dem registrierten `BotServices`-Objekt initialisiert. Hier sind die relevanten Teile der beiden Methoden angegeben.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    //...
    var botConfig = BotConfiguration.Load(botFilePath ?? @".\nlp-with-dispatch.bot", secretKey);
    services.AddSingleton(sp => botConfig
        ?? throw new InvalidOperationException($"The .bot config file could not be loaded. ({botConfig})"));

    // ...
    
    var connectedServices = InitBotServices(botConfig);
    services.AddSingleton(sp => connectedServices);
    
    services.AddBot<NlpDispatchBot>(options =>
    {
          
          // The Memory Storage used here is for local bot debugging only. 
          // When the bot is restarted, everything stored in memory will be gone.

          Storage dataStore = new MemoryStorage();

          // ...

          // Create Conversation State object.
          // The Conversation State object is where we persist anything at the conversation-scope.

          var conversationState = new ConversationState(dataStore);
          options.State.Add(conversationState);
     });
}

```
Mit dem folgenden Code werden die Verweise des Bots auf externe Dienste initialisiert. Beispielsweise werden hier LUIS- und QnA Maker-Dienste erstellt. Diese externen Dienste werden mit der `BotConfiguration`-Klasse konfiguriert (basierend auf dem Inhalt Ihrer BOT-Datei).

```csharp
private static BotServices InitBotServices(BotConfiguration config)
{
    var qnaServices = new Dictionary<string, QnAMaker>();
    var luisServices = new Dictionary<string, LuisRecognizer>();

    foreach (var service in config.Services)
    {
        switch (service.Type)
        {
            case ServiceTypes.Luis:
                {
                    // ...
                    var app = new LuisApplication(luis.AppId, luis.AuthoringKey, luis.GetEndpoint());
                    var recognizer = new LuisRecognizer(app);
                    luisServices.Add(luis.Name, recognizer);
                    break;
                }

            case ServiceTypes.Dispatch:
                // ...
                var dispatchApp = new LuisApplication(dispatch.AppId, dispatch.AuthoringKey, dispatch.GetEndpoint());

                // Since the Dispatch tool generates a LUIS model, we use the LuisRecognizer to resolve the
                // dispatching of the incoming utterance.
                var dispatchARecognizer = new LuisRecognizer(dispatchApp);
                luisServices.Add(dispatch.Name, dispatchARecognizer);
                break;

            case ServiceTypes.QnA:
                {
                    // ...
                    var qnaEndpoint = new QnAMakerEndpoint()
                    {
                        KnowledgeBaseId = qna.KbId,
                        EndpointKey = qna.EndpointKey,
                        Host = qna.Hostname,
                    };

                    var qnaMaker = new QnAMaker(qnaEndpoint);
                    qnaServices.Add(qna.Name, qnaMaker);
                    break;
                }
        }
    }

    return new BotServices(qnaServices, luisServices);
}
```

<!--
# [JavaScript](#tab/javascript)

```javascript
```
-->

---

### <a name="calling-the-services-from-your-bot"></a>Aufrufen von Diensten aus Ihrem Bot

Die Botlogik überprüft die Benutzereingabe anhand des kombinierten Dispatchmodells.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

In der Datei **NlpDispatchBot.cs** ruft der Konstruktor des Bots das `BotServices`-Objekt auf, das wir beim Starten registriert haben.

```csharp
private readonly BotServices _services;

public NlpDispatchBot(BotServices services)
{
    _services = services ?? throw new System.ArgumentNullException(nameof(services));

    //...
}
```

In der `OnTurnAsync`-Methode des Bots überprüfen wir eingehende Nachrichten des Benutzers anhand des Dispatchmodells.

```csharp
// Get the intent recognition result
var recognizerResult = await _services.LuisServices[DispatchKey].RecognizeAsync(context, cancellationToken);
var topIntent = recognizerResult?.GetTopScoringIntent();

if (topIntent == null)
{
    await context.SendActivityAsync("Unable to get the top intent.");
}
else
{
    await DispatchToTopIntentAsync(context, topIntent, cancellationToken);
}
```

<!--
# [JavaScript](#tab/javascript)

```javascript
```
-->

---

### <a name="working-with-the-recognition-results"></a>Verwenden der Erkennungsergebnisse

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Wenn das Modell ein Ergebnis produziert, wird angegeben, welcher Dienst die Äußerung am besten verarbeiten kann. Der Code in diesem Bot leitet die Anforderung an den entsprechenden Dienst weiter und fasst anschließend die Antwort des aufgerufenen Diensts zusammen.

```csharp
// Depending on the intent from Dispatch, routes to the right LUIS model or QnA service.
private async Task DispatchToTopIntentAsync(
    ITurnContext context,
    (string intent, double score)? topIntent,
    CancellationToken cancellationToken = default(CancellationToken))
{
    const string homeAutomationDispatchKey = "l_Home_Automation";
    const string weatherDispatchKey = "l_Weather";
    const string noneDispatchKey = "None";
    const string qnaDispatchKey = "q_sample-qna";

    switch (topIntent.Value.intent)
    {
        case homeAutomationDispatchKey:
            await DispatchToLuisModelAsync(context, HomeAutomationLuisKey);

            // Here, you can add code for calling the hypothetical home automation service, passing in any entity
            // information that you need.
            break;
        case weatherDispatchKey:
            await DispatchToLuisModelAsync(context, WeatherLuisKey);

            // Here, you can add code for calling the hypothetical weather service,
            // passing in any entity information that you need
            break;
        case noneDispatchKey:
            // You can provide logic here to handle the known None intent (none of the above).
            // In this example we fall through to the QnA intent.
        case qnaDispatchKey:
            await DispatchToQnAMakerAsync(context, QnAMakerKey);
            break;

        default:
            // The intent didn't match any case, so just display the recognition results.
            await context.SendActivityAsync($"Dispatch intent: {topIntent.Value.intent} ({topIntent.Value.score}).");
            break;
    }
}

// Dispatches the turn to the request QnAMaker app.
private async Task DispatchToQnAMakerAsync(
    ITurnContext context,
    string appName,
    CancellationToken cancellationToken = default(CancellationToken))
{
    if (!string.IsNullOrEmpty(context.Activity.Text))
    {
        var results = await _services.QnAServices[appName].GetAnswersAsync(context).ConfigureAwait(false);
        if (results.Any())
        {
            await context.SendActivityAsync(results.First().Answer, cancellationToken: cancellationToken);
        }
        else
        {
            await context.SendActivityAsync($"Couldn't find an answer in the {appName}.");
        }
    }
}


// Dispatches the turn to the requested LUIS model.
private async Task DispatchToLuisModelAsync(
    ITurnContext context,
    string appName,
    CancellationToken cancellationToken = default(CancellationToken))
{
    await context.SendActivityAsync($"Sending your request to the {appName} system ...");
    var result = await _services.LuisServices[appName].RecognizeAsync(context, cancellationToken);

    await context.SendActivityAsync($"Intents detected by the {appName} app:\n\n{string.Join("\n\n", result.Intents)}");

    if (result.Entities.Count > 0)
    {
        await context.SendActivityAsync($"The following entities were found in the message:\n\n{string.Join("\n\n", result.Entities)}");
    }
}
```

<!--
# [JavaScript](#tab/javascript)

```javascript
```
-->

---

## <a name="evaluate-the-dispatchers-performance"></a>Auswerten der Leistung des Dispatch-Bots

Manchmal gibt es Benutzernachrichten, die als Beispiele in den LUIS-Apps und den QnA Maker-Diensten angegeben werden, die die kombinierte LUIS-App nicht richtig verarbeiten kann. Sie können die Leistung Ihrer App mit der Option `eval` überprüfen.

```shell
dispatch eval
```

Durch das Ausführen von `dispatch eval` wird die Datei **Summary.html** generiert, die Statistiken zur vorhergesagten Leistung des Sprachmodells bereitstellt. Sie können `dispatch eval` auf allen LUIS-Apps ausführen, nicht nur auf LUIS-Apps, die mit dem Dispatch-Tool erstellt wurden.

### <a name="edit-intents-for-duplicates-and-overlaps"></a>Bearbeiten von Absichten für Duplikate und Überlappungen

Überprüfen Sie Beispiel-Äußerungen, die in der Datei **Summary.html** als Duplikate gekennzeichnet sind, und entfernen Sie ähnliche oder überlappende Beispiele. Angenommen, Anforderungen wie „schalte meine Lampen ein“ werden in der LUIS-App `Home Automation` der Absicht „TurnOnLights“ zugeordnet, aber Anforderungen wie „Warum gehen meine Lampen nicht an?“ werden der „None“-Absicht zugeordnet, damit sie an QnA Maker übergeben werden. Wenn Sie die LUIS-App und den QnA Maker mit dem Dispatch-Tool kombinieren, müssen Sie einen der folgenden Schritte ausführen:

* Entfernen Sie die Absicht „None“ aus der ursprünglichen LUIS-App `Home Automation`, und fügen Sie die Äußerungen in dieser Absicht der Absicht „None“ in der Dispatcher-App hinzu.
* Wenn Sie die Absicht „None“ nicht aus der ursprünglichen LUIS-App entfernen, müssen Sie Ihrem Bot Logik hinzufügen, um die Nachrichten, die mit der Absicht übereinstimmen, an den QnA Maker-Dienst zu übergeben.


## <a name="additional-resources"></a>Zusätzliche Ressourcen 

**Löschen von Ressourcen:** In diesem Beispiel werden einige Anwendungen und Ressourcen erstellt, die Sie mit den unten angegebenen Schritten löschen können. Sie sollten aber keine Ressourcen löschen, die von *anderen Apps oder Diensten* benötigt werden. 

_LUIS-Ressourcen_
1. Melden Sie sich am [luis.ai](https://www.luis.ai)-Portal an.
1. Navigieren Sie zur Seite _Meine Apps_.
1. Wählen Sie die Apps aus, die von diesem Beispiel erstellt wurden.
   * `Home Automation`
   * `Weather`
   * `NLP-With-Dispatch-BotDispatch`
1. Klicken Sie auf _Löschen_ und dann auf _OK_, um den Vorgang zu bestätigen.

_QnA Maker-Ressourcen_
1. Melden Sie sich am Portal [qnamaker.ai](https://www.qnamaker.ai/) an.
1. Navigieren Sie zur Seite _Meine Wissensdatenbanken_.
1. Klicken Sie für die Wissensdatenbank `Sample QnA` auf die Schaltfläche „Löschen“ und dann auf _Löschen_, um den Vorgang zu bestätigen.

**Bewährte Methode:** Halten Sie sich an die bewährte Methode für [LUIS](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/luis-concept-best-practices) und [QnA Maker](https://docs.microsoft.com/en-us/azure/cognitive-services/qnamaker/concepts/best-practices), um die in diesem Beispiel genutzten Dienste zu verbessern.
