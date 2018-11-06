---
title: Verwenden von LUIS- und QnA-Diensten mit dem Dispatch-Tool | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie LUIS und QnA Maker in Ihrem Bot verwenden können.
keywords: luis, qna, dispatch-tool, mehrere dienste
author: DeniseMak
ms.author: v-demak
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 10/31/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 4d029dc7361ac8a7fadb61141faf60d8a62eab3c
ms.sourcegitcommit: a496714fb72550a743d738702f4f79e254c69d06
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/01/2018
ms.locfileid: "50736678"
---
# <a name="use-luis-and-qna-services-with-the-dispatch-tool"></a>Verwenden von LUIS- und QnA-Diensten mit dem Dispatch-Tool

[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

<!--TODO: Add the JS sections back in and update them once the JS sample is working.-->

In diesem Tutorial wird veranschaulicht, wie ein mit dem Dispatch-Tool generiertes LUIS-Modell verwendet wird, um Ihren Bot in mehrere LUIS-Apps (Language Understanding Intelligent Service) und QnA Maker-Dienste zu integrieren. In diesem Beispiel werden die folgenden Dienste kombiniert.

| Dienstart | NAME | BESCHREIBUNG |
|------|------|------|
| LUIS-App | HomeAutomation | Erkennt eine Absicht zur Gebäudeautomatisierung mit den zugeordneten Entitätsdaten.|
| LUIS-App | Weather | Erkennt die Absichten von „Weather.GetForecast“ und „Weather.GetCondition“ mit Standortdaten.|
| QnA Maker-Dienst | Häufig gestellte Fragen  | Stellt Antworten auf einige einfache Fragen zum Bot bereit. |

Der Code für diesen Artikel stammt aus dem Beispiel **NLP with Dispatch** [[C#](https://aka.ms/dispatch-sample-cs)].

<!-- | [JS](https://aka.ms/dispatch-sample-js)-->

Eine Übersicht über die Sprachdienste finden Sie unter [Language Understanding](bot-builder-concept-luis.md). Eine Anleitung zur Implementierung im Bot finden Sie in den Gewusst wie-Artikeln für [LUIS](bot-builder-howto-v4-luis.md) und [QnA Maker](bot-builder-howto-qna.md).

Sie können die Anleitung in der Infodatei des Beispiels befolgen, um den Bot einzurichten und zu testen, oder Sie können zu [Hinweise zum Code](#notes-about-the-code) springen.

## <a name="create-the-services-and-test-the-bot"></a>Erstellen der Dienste und Testen des Bots

Befolgen Sie die Anleitung in der Infodatei für das Beispiel. Sie verwenden diese CLI-Tools zum Erstellen und Veröffentlichen dieser Dienste und zum Aktualisieren der zugehörigen Informationen in Ihrer Konfigurationsdatei (**.bot**).

1. Führen Sie für das Beispielrepository das Klonen oder Pullen durch.
1. Installieren Sie die Bot Builder CLI-Tools.
1. Konfigurieren Sie die erforderlichen Dienste manuell.

### <a name="test-your-bot"></a>Testen Ihres Bots

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Starten Sie Ihren Bot, indem Sie entweder Visual Studio oder Visual Studio Code verwenden.

<!--
# [JavaScript](#tab/javascript)
-->

---

Stellen Sie über Bot Framework Emulator eine Verbindung mit Ihrem Bot her.

Hier ist ein Teil der Eingabe dieser Dienste angegeben, den wir eingefügt haben:

* QnA Maker
  * `hi`, `good morning`
  * `what are you`, `what do you do`
* LUIS (Gebäudeautomatisierung)
  * `turn on bedroom light`
  * `turn off bedroom light`
  * `make some coffee`
* LUIS (Wetter)
  * `whats the weather in chennai india`
  * `what's the forecast for bangalore`
  * `show me the forecast for nebraska`

## <a name="notes-about-the-code"></a>Hinweise zum Code

### <a name="packages"></a>Pakete

In diesem Beispiel werden die folgenden Pakete verwendet:

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Die aktuellen v4-Versionen dieser [NuGet-Pakete](https://docs.microsoft.com/en-us/nuget/tools/package-manager-ui#updating-a-package).

* `Microsoft.Bot.Builder`
* `Microsoft.Bot.Builder.AI.Luis`
* `Microsoft.Bot.Builder.AI.QnA`
* `Microsoft.Bot.Builder.Integration.AspNet.Core`
* `Microsoft.Bot.Configuration`

<!--
# [JavaScript](#tab/javascript)

Download the [LUIS Dispatch sample][DispatchBotJs].  Install the required packages, including the `botbuilder-ai` package for LUIS and QnA Maker, using npm:

* `npm install --save botbuilder`
* `npm install --save botbuilder-ai`
-->

---

### <a name="botbuilder-cli-tools"></a>Bot Builder CLI-Tools

Im Beispiel werden diese [Bot Builder CLI-Tools](https://aka.ms/botbuilder-tools-readme) (über npm verfügbar) genutzt, um die Dienste LUIS, QnA Maker und Dispatch zu erstellen, zu trainieren und zu veröffentlichen. Außerdem werden Informationen zu diesen Diensten in der Konfigurationsdatei (**.bot**) Ihres Bots aufgezeichnet.

* [Dispatch](https://aka.ms/botbuilder-tools-dispatch)
* [LUDown](https://aka.ms/botbuilder-tools-ludown-readme)
* [LUIS](https://aka.ms/botbuilder-tools-luis)
* [MSBot](https://aka.ms/botbuilder-tools-msbot-readme)
* [QnA Maker](https://aka.ms/botbuilder-tools-qnaMaker)

> [!TIP]
> Führen Sie den folgenden Befehl aus, um sicherzustellen, dass Sie die neueste Version von npm und dieser CLI-Tools verwenden.
>
> ```shell
> npm i -g npm dispatch ludown luis-apis msbot qnamaker
> ```

Nachdem Sie die Tools zum Einrichten der Dienste verwendet haben, sollte Ihre **BOT**-Datei für dieses Beispiel in etwa wie folgt aussehen. (Sie können `msbot secret -n` ausführen, um die vertraulichen Werte in dieser Datei zu verschlüsseln.)

```json
{
    "name": "NLP-With-Dispatch-Bot",
    "description": "",
    "services": [
        {
            "type": "endpoint",
            "name": "development",
            "id": "http://localhost:3978/api/messages",
            "appId": "",
            "appPassword": "",
            "endpoint": "http://localhost:3978/api/messages"
        },
        {
            "type": "luis",
            "name": "Home Automation",
            "appId": "<your-home-automation-luis-app-id>",
            "version": "0.1",
            "authoringKey": "<your-luis-authoring-key>",
            "subscriptionKey": "<your-cognitive-services-subscription-key>",
            "region": "westus",
            "id": "110"
        },
        {
            "type": "luis",
            "name": "Weather",
            "appId": "<your-weather-luis-app-id>",
            "version": "0.1",
            "authoringKey": "<your-luis-authoring-key>",
            "subscriptionKey": "<your-cognitive-services-subscription-key>",
            "region": "westus",
            "id": "92"
        },
        {
            "type": "qna",
            "name": "Sample QnA",
            "kbId": "<your-qna-knowledge-base-id>",
            "subscriptionKey": "<your-cognitive-services-subscription-key>",
            "endpointKey": "<your-qna-endpoint-key>",
            "hostname": "<your-qna-host-name>",
            "id": "184"
        },
        {
            "type": "dispatch",
            "name": "NLP-With-Dispatch-BotDispatch",
            "appId": "<your-dispatch-app-id>",
            "authoringKey": "<your-luis-authoring-key>",
            "subscriptionKey": "<your-cognitive-services-subscription-key>",
            "version": "Dispatch",
            "region": "westus",
            "serviceIds": [
                "110",
                "92",
                "184"
            ],
            "id": "27"
        }
    ],
    "padlock": "",
    "version": "2.0"
}
```

### <a name="connecting-to-the-services-from-your-bot"></a>Herstellen einer Verbindung mit den Diensten über Ihren Bot

Zum Herstellen der Verbindung mit den Diensten Dispatch, LUIS und QnA Maker ruft Ihr Bot per Pullvorgang Informationen aus der **BOT**-Datei ab.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

In **Startup.cs** wird die Konfigurationsdatei mit `ConfigureServices` eingelesen, und diese Informationen werden von `InitBotServices` zum Initialisieren der Dienste verwendet. Bei jeder Erstellung wird der Bot mit dem registrierten `BotServices`-Objekt initialisiert. Hier sind die relevanten Teile der beiden Methoden angegeben.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    //...
    var botConfig = BotConfiguration.Load(botFilePath ?? @".\BotConfiguration.bot", secretKey);
    services.AddSingleton(sp => botConfig
        ?? throw new InvalidOperationException($"The .bot config file could not be loaded. ({botConfig})"));

    // Retrieve current endpoint.
    var environment = _isProduction ? "production" : "development";
    var service = botConfig.Services.Where(s => s.Type == "endpoint" && s.Name == environment).FirstOrDefault();
    if (!(service is EndpointService endpointService))
    {
        throw new InvalidOperationException($"The .bot file does not contain an endpoint with name '{environment}'.");
    }

    var connectedServices = InitBotServices(botConfig);

    services.AddSingleton(sp => connectedServices);
    //...
}
```

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
/// <summary>
/// Depending on the intent from Dispatch, routes to the right LUIS model or QnA service.
/// </summary>
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

/// <summary>
/// Dispatches the turn to the request QnAMaker app.
/// </summary>
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

/// <summary>
/// Dispatches the turn to the requested LUIS model.
/// </summary>
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

Durch das Ausführen von `dispatch eval` wird die Datei **Summary.html** generiert, die Statistiken zur vorhergesagten Leistung des Sprachmodells bereitstellt.

> [!TIP]
> Sie können `dispatch eval` auf allen LUIS-Apps ausführen, nicht nur auf LUIS-Apps, die mit dem Dispatch-Tool erstellt wurden.

### <a name="edit-intents-for-duplicates-and-overlaps"></a>Bearbeiten von Absichten für Duplikate und Überlappungen

Überprüfen Sie Beispiel-Äußerungen, die in der Datei **Summary.html** als Duplikate gekennzeichnet sind, und entfernen Sie ähnliche oder überlappende Beispiele. Angenommen, Anforderungen wie „schalte meine Lampen ein“ werden in der LUIS-App `Home Automation` der Absicht „TurnOnLights“ zugeordnet, aber Anforderungen wie „Warum gehen meine Lampen nicht an?“ werden der „None“-Absicht zugeordnet, damit sie an QnA Maker übergeben werden. Wenn Sie die LUIS-App und den QnA Maker mit dem Dispatch-Tool kombinieren, müssen Sie einen der folgenden Schritte ausführen:

* Entfernen Sie die Absicht „None“ aus der ursprünglichen LUIS-App `Home Automation`, und fügen Sie die Äußerungen in dieser Absicht der Absicht „None“ in der Dispatcher-App hinzu.
* Wenn Sie die Absicht „None“ nicht aus der ursprünglichen LUIS-App entfernen, müssen Sie Ihrem Bot Logik hinzufügen, um die Nachrichten, die mit der Absicht übereinstimmen, an den QnA Maker-Dienst zu übergeben.

> [!TIP]
> Tipps zur Verbesserung der Leistung Ihres Sprachmodells finden Sie unter [Best practices for Language Understanding (Bewährte Methoden für Language Understanding)](./bot-builder-concept-luis.md#best-practices-for-language-understanding).

## <a name="to-clean-up-resources-from-this-sample"></a>So bereinigen Sie Ressourcen für dieses Beispiel

In diesem Beispiel werden einige Anwendungen und Ressourcen erstellt. Sie können diese Anleitung befolgen, um sie zu löschen.

### <a name="luis-resources"></a>LUIS-Ressourcen

1. Melden Sie sich am [luis.ai](https://www.luis.ai)-Portal an.
1. Navigieren Sie zur Seite **Meine Apps**.
1. Wählen Sie die Apps aus, die von diesem Beispiel erstellt wurden.
   * `Home Automation`
   * `Weather`
   * `NLP-With-Dispatch-BotDispatch`
1. Klicken Sie auf **Löschen** und dann auf **OK**, um den Vorgang zu bestätigen.

### <a name="qna-maker-resources"></a>QnA Maker-Ressourcen

1. Melden Sie sich am Portal [qnamaker.ai](https://www.qnamaker.ai/) an.
1. Navigieren Sie zur Seite **Meine Wissensdatenbanken**.
1. Klicken Sie für die Wissensdatenbank `Sample QnA` auf die Schaltfläche „Löschen“ und dann auf **Löschen**, um den Vorgang zu bestätigen.

### <a name="azure-resources"></a>Azure-Ressourcen

> [!WARNING]
> Löschen Sie hierbei keine Ressourcen, die von anderen Apps oder Diensten genutzt werden.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
1. Navigieren Sie zur Seite **Übersicht** für die Cognitive Services-Ressource, die Sie für das Beispiel erstellt haben.
1. Klicken Sie auf **Löschen** und dann auf **Ja**, um den Vorgang zu bestätigen.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Sprachverständnis](bot-builder-concept-luis.md)
* [Entwerfen von Wissens-Bots](../bot-service-design-pattern-knowledge-base.md)
* [Konfigurieren der Sprachvorbereitung](../bot-service-manage-speech-priming.md)
* [Verwenden von LUIS für Language Understanding](bot-builder-howto-v4-luis.md)
* [Verwenden von QnA Maker zum Beantworten von Fragen](bot-builder-howto-qna.md)
