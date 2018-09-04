---
title: Integrieren mehrerer LUIS- und QnA-Dienste mit dem Dispatch-Tool | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie LUIS und QnA Maker in Ihrem Bot verwenden können.
keywords: luis, qna, dispatch-tool, mehrere dienste
author: DeniseMak
ms.author: v-demak
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 08/27/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 02dffcdd8cb4fd27f59fa8a763d7f2027aa0bcf2
ms.sourcegitcommit: 0b2be801e55f6baa048b49c7211944480e83ba95
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/28/2018
ms.locfileid: "43115035"
---
# <a name="integrate-multiple-luis-and-qna-services-with-the-dispatch-tool"></a>Integrieren mehrerer LUIS- und QnA-Dienste mit dem Dispatch-Tool

[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

In diesem Tutorial wird veranschaulicht, wie ein mit dem Dispatch-Tool generiertes LUIS-Modell verwendet wird, um Ihren Bot in mehrere LUIS-Apps (Language Understanding Intelligent Service) und QnA Maker-Dienste zu integrieren. 

Angenommen Sie haben die folgenden Dienste entwickelt, und Sie möchten einen Bot erstellen, der in all diese Dienste integriert wird.

| Dienstart | NAME | BESCHREIBUNG |
|------|------|------|
| LUIS-App | HomeAutomation | Erkennt die Absichten HomeAutomation.TurnOn, HomeAutomation.TurnOff und HomeAutomation.None.|
| LUIS-App | Weather | Erkennt die Absichten von Weather.GetForecast und Weather.GetCondition.|
| QnA Maker-Dienst | Häufig gestellte Fragen  | Stellt Antworten auf Fragen zu einem automatisierten Heimbeleuchtungssystem bereit. |

Erstellen Sie zunächst die Apps und Dienste, und integrieren Sie sie anschließend mithilfe des Dispatch-Tools miteinander.

## <a name="create-the-luis-apps"></a>Erstellen der LUIS-Apps

Die schnellste Möglichkeit, die LUIS-Apps *HomeAutomation* und *Weather* zu erstellen, ist das Herunterladen der Dateien [homeautomation.json][HomeAutomationJSON] und [weather.json][WeatherJSON]. Führen Sie anschließend die folgenden Schritte aus, um diese beiden LUIS-Apps zu importieren.

1. Navigieren Sie zur [LUIS-Website](https://www.luis.ai/home), und melden Sie sich an. 
2. Klicken Sie auf der Seite **My Apps** (Meine Apps) auf **Import new app** (Neue App importieren).
   1. Wählen Sie für die *HomeAutomation*-App die Datei **homeautomation.json** aus, und geben Sie ihr den Namen „homeautomation“. Klicken Sie auf **Done** (Fertig), um die App zu importieren. Klicken Sie nach dem Importieren der App auf **Publish** (Veröffentlichen), um die App zu veröffentlichen.
   1. Wählen Sie für die *Weather*-App die Datei **weather.json** aus, und geben Sie ihr den Namen „weather“. Klicken Sie auf **Done** (Fertig), um die App zu importieren. Klicken Sie nach dem Importieren der App auf **Publish** (Veröffentlichen), um die App zu veröffentlichen.

## <a name="create-the-qna-cognitive-service-in-azure"></a>Erstellen von QnA-Cognitive Services in Azure

Der QnA Maker besteht aus zwei Teilen: Cognitive Services in Azure und die Wissensdatenbank von Q&A-Paaren, die Sie mit Cognitive Services veröffentlichen. Wenn Sie Ihre Wissensdatenbank erstellen, können Sie sie mit dem Azure-Dienst verknüpfen, indem Sie den **Azure-Dienstnamen** auswählen, den Sie in diesem Schritt erstellt haben.

Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und führen Sie die folgenden Schritte aus, um Cognitive Services in Azure zu erstellen:

1. Klicken Sie auf **Alle Dienste**.
1. Suchen Sie nach `Cognitive`, und wählen Sie **Cognitive Services** aus.
1. Klicken Sie auf **Hinzufügen**.
1. Suchen Sie nach `QnA`, und wählen Sie **QnA Maker** aus.
1. Klicken Sie auf dem QnA Maker-Blatt auf **Erstellen**.
1. Geben Sie die Informationen ein, und erstellen Sie den QnA Maker-Dienst.

    <!-- TODO: Add screenshot.-->

    * Geben Sie einen Namen für den Dienst ein. Verwenden Sie für dieses Tutorial `SmartLightQnA`.
    * Wählen Sie das zu verwendende Abonnement aus.
    * Wählen Sie den zu verwendenden Modellverwaltungstarif aus. In diesem Tutorial wird der Tarif F0 (Kostenlos) verwendet.
    * Erstellen Sie eine Ressourcengruppe, oder wählen Sie eine aus. In diesem Tutorial wird eine neue `SmartLightQnA`-Ressourcengruppe erstellt.
    * Wählen Sie einen Tarif für die Suche aus. In diesem Tutorial wird der Tarif B (Basic) verwendet.
    * Wählen Sie einen Standort für die Suche aus. In diesem Tutorial wird `West US` verwendet.
    * Geben Sie einen Anwendungsnamen ein. In diesem Tutorial wird die Standardeinstellung `SmartLightQnA` verwendet.
    * Wählen Sie einen Standort für die Website aus. In diesem Tutorial wird `West US` verwendet.
    * Behalten Sie die Standardeinstellung für die Aktivierung von App-Erkenntnissen bei.
    * Wählen Sie einen Standort für App-Erkenntnisse aus. In diesem Tutorial wird `West US` verwendet.
    * Klicken Sie auf **Erstellen**, um Ihren QnA Maker-Dienst zu erstellen.
    * Azure erstellt Ihren Dienst und beginnt mit der Bereitstellung.

1. Sobald der Dienst bereitgestellt wurde, überprüfen Sie die Benachrichtigung, und klicken Sie auf **Zu Ressource wechseln**, um das Blatt für den Dienst zu öffnen.
1. Klicken Sie auf **Schlüssel**, um Ihre Schlüssel abzurufen.

    * Kopieren Sie den Namen des Diensts und des ersten Schlüssels. Sie müssen diesen Namen beim Erstellen Ihrer Wissensdatenbank verwenden, damit der Dienst mit der Wissensdatenbank verknüpft wird. Sie verwenden diesen Schlüssel auch anstelle von „YOUR-AZURE-QNA-SUBSCRIPTION-KEY“ in den nachfolgenden Schritten für den Dispatcher.
    * Sie erhalten zwei Schlüssel, sodass Sie sie einzeln erneut generieren können und den Dienst nicht unterbrechen müssen.

## <a name="create-and-publish-the-qna-maker-knowledge-base"></a>Erstellen und Veröffentlichen einer QnA Maker-Wissensdatenbank

Führen Sie folgende Schritte aus, um die Wissensdatenbank zu erstellen:

1. Öffnen Sie die [QnA Maker-Website](https://qnamaker.ai), und melden Sie sich an. 
1. Klicken Sie auf **Create a knowledge base** (Neue Wissensdatenbank erstellen), um eine neue Wissensdatenbank zu erstellen. Da Sie die Cognitive Services in Azure bereits erstellt haben, können Sie **Schritt 1** überspringen.
1. Wählen Sie in **Schritt 2** für das Feld **Azure QnA service** (Azure QnA-Dienst) den Cognitive Services-Namen aus, den Sie in Azure erstellt haben. Dadurch wird die Wissensdatenbank mit dem Dienst verknüpft, den Sie in Azure erstellt haben.
1. Geben Sie in **Schritt 3** den Namen „FAQ“ für die Wissensdatenbank ein. 
1. Füllen Sie in **Schritt 4** die Wissensdatenbank mit dieser [TSV-Beispieldatei][FAQ_TSV] auf. Alternativ können Sie Inhalte von einer Webseite verwenden. Fügen Sie in diesem Fall den Link in das Feld **URL** ein.
1. Klicken Sie in **Schritt 5** auf **Create your KB**  (Wissensdatenbank erstellen), um die Wissensdatenbank zu erstellen.

Nachdem die Wissensdatenbank erstellt wurde, müssen Sie sie **veröffentlichen**, um die ID der Wissensdatenbank und den Endpunkt zu erhalten. Sie benötigen die ID und den Endpunkt an späterer Stelle dieses Prozesses.


## <a name="use-the-dispatch-tool-to-create-the-dispatcher-luis-app"></a>Verwenden des Dispatch-Tools zum Erstellen einer LUIS-Dispatcher-App
Nachdem Sie die beiden LUIS-Apps und eine Wissensdatenbank erstellt haben, generieren Sie als Nächstes mit dem Dispatch-Tool eine LUIS-App, die diese drei Komponenten zu einer LUIS-App kombiniert. 

### <a name="step-1-install-the-dispatch-tool"></a>Schritt 1: Installieren des Dispatch-Tools

Öffnen Sie ein **Eingabeaufforderungsfenster**, und navigieren Sie zu Ihrem Dispatcher-Projekt. Führen Sie dann den folgenden NPM-Befehl aus, um das [Dispatch-Tool][DispatchTool] zu installieren.

```cmd
npm install -g botdispatch
```

### <a name="step-2-initialize-the-dispatch-tool"></a>Schritt 2: Initialisieren des Dispatch-Tools

Führen Sie den folgenden Befehl aus, um das Dispatch-Tool mit dem Namen `CombineWeatherAndLights` zu initialisieren. Ersetzen Sie `"YOUR-LUIS-AUTHORING-KEY"` in der folgenden Befehlszeile durch Ihren [LUIS-Erstellungsschlüssel](https://docs.microsoft.com/en-us/azure/cognitive-services/LUIS/luis-concept-keys).

```cmd
dispatch init -name CombineWeatherAndLights -luisAuthoringKey "YOUR-LUIS-AUTHORING-KEY" -luisAuthoringRegion westus
```

### <a name="step-3-add-apps-to-dispatcher"></a>Schritt 3: Hinzufügen von Apps zum Dispatcher

Rufen Sie die LUIS-App-ID für alle LUIS-Apps ab, die Sie erstellt haben. Diese finden Sie für jede App auf der [LUIS-Website](https://www.luis.ai/home) unter **My Apps** (Meine Apps). Klicken Sie dort auf den App-Namen und dann auf **Settings** (Einstellungen), um die **Anwendungs-ID** anzuzeigen. Verwenden Sie den *LUIS-Erstellungsschlüssel* aus **Schritt 2**.

Führen Sie den folgenden Befehl für jede der LUIS-Apps aus, die Sie erstellt haben (z. B. **homeautomation** und **weather**). Denken Sie daran, im unten stehenden Befehl die entsprechende ID zu verwenden.

```
dispatch add -type luis -id "HOMEAUTOMATION-APP-ID" -name homeautomation -version 0.1 -key "YOUR-LUIS-AUTHORING-KEY"
dispatch add -type luis -id "WEATHER-APP-ID" -name weather -version 0.1 -key "YOUR-LUIS-AUTHORING-KEY"

```

Fügen Sie anschließend die QnAMaker-Wissensdatenbank hinzu, indem Sie den folgenden Befehl ausführen. In diesem Befehl bezieht sich `QNA-KB-ID` auf die Wissensdatenbank-ID, die Sie nach der Veröffentlichung der Wissensdatenbank erhalten haben, und `YOUR-AZURE-QNA-SUBSCRIPTION-KEY` auf den Schlüssel, den Sie aus den in Azure erstellten Cognitive Services abgerufen haben.

```
dispatch add -type qna -id "QNA-KB-ID" -name faq -key "YOUR-AZURE-QNA-SUBSCRIPTION-KEY"
```

### <a name="step-4-create-the-dispatcher-luis-app"></a>Schritt 4: Erstellen der LUIS-Dispatcher-App

Nachdem alle Apps dem Dispatch-Tool hinzugefügt wurden, und führen Sie den folgenden Befehl aus, um die Dispatch-App zu erstellen. Falls Sie die Dispatch-Datei überprüfen möchten, finden Sie die Informationen in einer Datei mit der Erweiterung **.dispatch**.

```
dispatch create
```

Dieser Befehl erstellt die LUIS-Dispatcher-App namens **CombineWeatherAndLights**. Die neue App wird auf [https://www.luis.ai/home](https://www.luis.ai/home) angezeigt. 

![Die Dispatcher-App in LUIS.ai](media/tutorial-dispatch/dispatch-app-in-luis.png)

Klicken Sie auf die neue App. Unter **Intents** (Absichten) können Sie sehen, dass die App über die Absichten `l_homeautomation`, `l_weather` und `q_faq` verfügt.

![Die Dispatcher-Absichten in LUIS.ai](media/tutorial-dispatch/dispatch-intents-in-luis.png)

Klicken Sie auf die Schaltfläche **Train** (Trainieren), um die LUIS-App zu trainieren, und nutzen Sie die Registerkarte **PUBLISH** (VERÖFFENTLICHEN), um sie zu [veröffentlichen](https://docs.microsoft.com/en-us/azure/cognitive-services/LUIS/publishapp). Klicken Sie auf **Settings** (Einstellungen), um die ID der neuen App zu kopieren. Sie benötigen diese ID für den Bot, der mit dieser App verbunden werden soll.

## <a name="create-the-bot"></a>Erstellen des Bots

Sie können die Absichten der Dispatcher-App nun mit der Logik im Bot verknüpfen, die Nachrichten an die ursprünglichen LUIS-Apps und den QnA Maker-Dienst weiterleitet.

Sie können das im Bot Builder SDK enthaltene Beispiel als Ausgangspunkt verwenden.

# <a name="ctabcsaddref"></a>[C#](#tab/csaddref)

Beginnen Sie mit dem Code im [LUIS-Dispatch-Beispiel][DispatchBotCS]. [Aktualisieren Sie die NuGet-Pakete](https://docs.microsoft.com/en-us/nuget/tools/package-manager-ui#updating-a-package) in Visual Studio auf die neuesten Vorschauversionen der Folgenden:

* `Microsoft.Bot.Builder.Integration.AspNet.Core`
* `Microsoft.Bot.Builder.Ai.QnA` (für QnA Maker erforderlich)
* `Microsoft.Bot.Builder.Ai.Luis` (für LUIS erforderlich)

# <a name="javascripttabjsaddref"></a>[JavaScript](#tab/jsaddref)

Laden Sie das [LUIS-Dispatch-Beispiel][DispatchBotJs] herunter.  Installieren Sie über npm die erforderlichen Pakete, einschließlich des `botbuilder-ai`-Pakets für LUIS und QnA Maker:

* `npm install --save botbuilder@preview`
* `npm install --save botbuilder-ai@preview`

---


Richten Sie das Beispiel für die Verwendung der Dispatcher-App ein.

# <a name="ctabcsbotconfig"></a>[C#](#tab/csbotconfig)

Bearbeiten Sie die folgenden Felder in der Datei **appsettings.json** im [LUIS-Dispatch-Beispiel][DispatchBotCS].

| NAME | BESCHREIBUNG |
|------|------|
| `Luis-SubscriptionKey` |  Ihr LUIS-Abonnementschlüssel. Dies kann ein Endpunktschlüssel oder ein Erstellungsschlüssel sein. Weitere Informationen finden Sie [hier](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/luis-concept-keys). | 
| `Luis-ModelId-Dispatcher` | App-ID der LUIS-App, die das Dispatch-Tool generiert. | 
| `Luis-ModelId-HomeAutomation` | App-ID der App, die Sie aus der Datei „homeautomation.json“ erstellt haben.  | 
| `Luis-ModelId-Weather` | App-ID der App, die Sie aus der Datei „weather.json“ erstellt haben. | 
| `QnAMaker-Endpoint-Url` | Für QnA Maker-Vorschaudienste sollte dieses Feld auf https://westus.api.cognitive.microsoft.com/qnamaker/v2.0 festgelegt werden. <br/>Legen Sie für neue (GA) QnA Maker-Dienste https://YOUR-QNA-SERVICE-NAME.azurewebsites.net/qnamaker fest.|
| `QnAMaker-SubscriptionKey` | Ihr Azure QnA Maker-Abonnementschlüssel. | 
| `QnAMaker-KnowledgeBaseId` | Die ID der Wissensdatenbank, die Sie im [QnAMaker-Portal](https://qnamaker.ai) erstellen.| 



Sehen Sie sich die `ConfigureServices`-Methode in der Datei **Startup.cs** an. Sie enthält Code zum Initialisieren von `LuisRecognizerMiddleware` mithilfe der App-ID der LUIS-App, die Sie soeben erstellt haben.

```csharp
// This method gets called by the runtime. Use this method to add services to the container.
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton(this.Configuration);
    services.AddBot<LuisDispatchBot>(options =>
    {
        options.CredentialProvider = new ConfigurationCredentialProvider(Configuration);

        var (luisModelId, luisSubscriptionKey, luisUri) = GetLuisConfiguration(this.Configuration, "Dispatcher");

        var luisModel = new LuisModel(luisModelId, luisSubscriptionKey, luisUri);

        // If you want to get all the intents and scores that LUIS recognized, set Verbose = true in luisOptions
        var luisOptions = new LuisRequest { Verbose = true };

        var middleware = options.Middleware;
        middleware.Add(new LuisRecognizerMiddleware(luisModel, luisOptions: luisOptions));
    });
}
```
Die Methoden `GetLuisConfiguration` und `GetQnAMakerConfiguration` rufen Ihre LUIS- und QnA-Konfigurationen aus der Datei „appsettings.json“ ab.
```csharp
public static (string modelId, string subscriptionId, Uri uri) GetLuisConfiguration(IConfiguration configuration, string serviceName)
{
    var modelId = configuration.GetSection($"Luis-ModelId-{serviceName}")?.Value;
    var subscriptionId = configuration.GetSection("Luis-SubscriptionKey")?.Value;
    var uri = new Uri(configuration.GetSection("Luis-Url")?.Value);
    return (modelId, subscriptionId, uri);
}

public static (string knowledgeBaseId, string subscriptionKey, string uri) GetQnAMakerConfiguration(IConfiguration configuration)
{
    var knowledgeBaseId = configuration.GetSection("QnAMaker-KnowledgeBaseId")?.Value;
    var subscriptionKey = configuration.GetSection("QnAMaker-SubscriptionKey")?.Value;
    var uri = configuration.GetSection("QnAMaker-Endpoint-Url")?.Value;
    return (knowledgeBaseId, subscriptionKey, uri);
}
```

### <a name="dispatch-the-message"></a>Weiterleiten der Nachricht

Sehen Sie sich die Datei **LuisDispatchBot.cs** an, in der der Bot die Nachricht an die LUIS-App oder den QnA Maker-Dienst für eine Unterkomponente weiterleitet. 

Wenn Ihre Dispatcher-App die Absichten `l_homeautomation` oder `l_weather` in `DispatchToTopIntent` erkennt, ruft sie eine `DispatchToTopIntent`-Methode auf, die ein `LuisRecognizer`-Objekt erstellt, um die ursprünglichen `homeautomation`- und `weather`-Apps aufzurufen. Wenn der Bot die Absicht `q_faq` oder die Fallback-Absicht `none` erkennt, ruft er eine Methode auf, die QnA Maker abfragt.

> [!NOTE] 
> Wenn die Namen der Absichten `l_homeautomation`, `l_weather` oder `q_faq` nicht mit der LUIS-App übereinstimmen, die Sie mit dem Dispatch-Tool erstellt haben, bearbeiten Sie sie so, dass Sie mit den kleingeschriebenen Absichtsnamen übereinstimmen, die im [LUIS-Portal](https://www.luis.ai) angezeigt werden.

```csharp
private async Task DispatchToTopIntent(ITurnContext context, (string intent, double score)? topIntent)
{
    switch (topIntent.Value.intent.ToLowerInvariant())
    {
        case "l_homeautomation":
            await DispatchToLuisModel(context, this.luisModelHomeAutomation, "home automation");
            break;
        case "l_weather":
            await DispatchToLuisModel(context, this.luisModelWeather, "weather");
            break;
        case "none":
        // You can provide logic here to handle the known None intent (none of the above).
        // In this example we fall through to the QnA intent.
        case "q_faq":
            await DispatchToQnAMaker(context, this.qnaEndpoint, "FAQ");
            break;
        default:
            // The intent didn't match any case, so just display the recognition results.
            await context.SendActivity($"Dispatch intent: {topIntent.Value.intent} ({topIntent.Value.score}).");

            break;
    }
}
```

Die `DispatchToQnAMaker`-Methode sendet die Nachricht des Benutzers an den QnA Maker-Dienst. Stellen Sie sicher, dass Sie den Dienst im [QnA Maker-Portal](https://qnamaker.ai) veröffentlicht haben, bevor Sie den Bot ausführen.

```csharp
private static async Task DispatchToQnAMaker(ITurnContext context, QnAMakerEndpoint qnaOptions, string appName)
{
    QnAMaker qnaMaker = new QnAMaker(qnaOptions);
    if (!string.IsNullOrEmpty(context.Activity.Text))
    {
        var results = await qnaMaker.GetAnswers(context.Activity.Text.Trim()).ConfigureAwait(false);
        if (results.Any())
        {
            await context.SendActivity(results.First().Answer);
        }
        else
        {
            await context.SendActivity($"Couldn't find an answer in the {appName}.");
        }
    }
}
```

Die `DispatchToLuisModel`-Methode sendet die Nachricht des Benutzers an die ursprünglichen LUIS-Apps `homeautomation` und `weather`. Stellen Sie sicher, dass Sie diese LUIS-Apps im [LUIS-Portal](https://www.luis.ai) bereitgestellt haben, bevor Sie den Bot ausführen.

```csharp
private static async Task DispatchToLuisModel(ITurnContext context, LuisModel luisModel, string appName)
{
    await context.SendActivity($"Sending your request to the {appName} system ...");
    var (intents, entities) = await RecognizeAsync(luisModel, context.Activity.Text);

    await context.SendActivity($"Intents detected by the {appName} app:\n\n{string.Join("\n\n", intents)}");

    if (entities.Count() > 0)
    {
        await context.SendActivity($"The following entities were found in the message:\n\n{string.Join("\n\n", entities)}");
    }
    
    // Here, you can add code for calling the hypothetical home automation or weather service, 
    // passing in the appName and any intent or entity information that you need 
}
```

Die `RecognizeAsync`-Methode ruft `LuisRecognizer` auf, um Ergebnisse von einer LUIS-App abzurufen.

```cs
private static async Task<(IEnumerable<string> intents, IEnumerable<string> entities)> RecognizeAsync(LuisModel luisModel, string text)
{
    var luisRecognizer = new LuisRecognizer(luisModel);
    var recognizerResult = await luisRecognizer.Recognize(text, System.Threading.CancellationToken.None);

    // list the intents
    var intents = new List<string>();
    foreach (var intent in recognizerResult.Intents)
    {
        intents.Add($"'{intent.Key}', score {intent.Value}");
    }

    // list the entities
    var entities = new List<string>();
    foreach (var entity in recognizerResult.Entities)
    {
        if (!entity.Key.ToString().Equals("$instance"))
        {
            entities.Add($"{entity.Key}: {entity.Value.First}");
        }
    }

    return (intents, entities);
}
```

# <a name="javascripttabjsbotconfig"></a>[JavaScript](#tab/jsbotconfig)

Beginnen Sie mit dem Code im [Dispatch-Bot-Beispiel][DispatchBotJs]. Öffnen Sie die Datei **app.js**, und ersetzen Sie optional die `appId`-Felder mit den IDs der LUIS-Apps, die Sie erstellt haben. Wenn Sie die `appId`-Felder nicht ändern, werden öffentliche LUIS-Apps verwendet, die zu Demonstrationszwecken erstellt wurden. Den `LUIS_SUBSCRIPTION_KEY` finden Sie auf der Veröffentlichungsseite jeder LUIS-App. Er ist in den Link für den Endpunkt auf dieser Seite eingebettet.

```javascript
// Packages
const { BotFrameworkAdapter, MemoryStorage, ConversationState } = require('botbuilder');
const { restify } = require('restify');
const { LuisRecognizer, QnAMaker } = require('botbuilder-ai');
const { DialogSet } = require('botbuilder-dialogs');

// Create LuisRecognizers and QnAMaker
// The LUIS applications are public, meaning you can use your own subscription key to test the applications.
// For QnAMaker, users are required to create their own knowledge base.
// The exported LUIS applications and QnAMaker knowledge base can be found with the sample bot.

// The corresponding LUIS application JSON is `dispatchSample.json`
const dispatcher = new LuisRecognizer({
    appId: '0b18ab4f-5c3d-4724-8b0b-191015b48ea9',
    subscriptionKey: process.env.LUIS_SUBSCRIPTION_KEY,
    serviceEndpoint: 'https://westus.api.cognitive.microsoft.com/',
    verbose: true
});

// The corresponding LUIS application JSON is `homeautomation.json`
const homeAutomation = new LuisRecognizer({
    appId: 'c6d161a5-e3e5-4982-8726-3ecec9b4ed8d',
    subscriptionKey: process.env.LUIS_SUBSCRIPTION_KEY,
    serviceEndpoint: 'https://westus.api.cognitive.microsoft.com/',
    verbose: true
});

// The corresponding LUIS application JSON is `weather.json`
const weather = new LuisRecognizer({
    appId: '9d0c9e9d-ce04-4257-a08a-a612955f2fb5',
    subscriptionKey: process.env.LUIS_SUBSCRIPTION_KEY,
    serviceEndpoint: 'https://westus.api.cognitive.microsoft.com/',
    verbose: true
});
```

Ersetzen Sie im folgenden Code `endpointKey` und `knowledgeBaseID` durch Ihren Schlüssel und Ihre Wissensdatenbank-ID von QnA Maker. `host` sollte für QnA Maker-Vorschaudienste auf `https://westus.api.cognitive.microsoft.com/qnamaker/v2.0` und für neue (GA) QnA Maker-Dienste auf `https://YOUR-QNA-SERVICE-NAME.azurewebsites.net/qnamaker` festgelegt werden.

```javascript
const faq = new QnAMaker(
    {
        knowledgeBaseId: '',
        endpointKey: '',
        host: ''
    },
    {
        answerBeforeNext: true
    }
);

```

Der restliche Code in der Datei **app.js** verarbeitet die Absichten `l_homeautomation`, `l_weather` und `None`, indem entsprechende Dialogfelder gestartet werden. Für die Absicht `q_faq` antwortet sie mit der Antwort des QnA Maker-Diensts.

> [!NOTE] 
> Wenn die Namen der Absichten `l_homeautomation`, `l_weather` oder `q_faq` nicht mit der LUIS-App übereinstimmen, die Sie mit dem Dispatch-Tool erstellt haben, bearbeiten Sie sie so, dass Sie mit den Absichtsnamen übereinstimmen, die im [LUIS-Portal](https://www.luis.ai) angezeigt werden.

```javascript
// create conversation state
const conversationState = new ConversationState(new MemoryStorage());
adapter.use(conversationState);

// register some dialogs for usage with the LUIS apps that are being dispatched to
const dialogs = new DialogSet();

function findEntities(entityName, entityResults) {
    let entities = []
    if (entityName in entityResults) {
        entityResults[entityName].forEach((entity, idx) => {
            entities.push(entity);
        });
    }
    return entities.length > 0 ? entities : undefined;
}

dialogs.add('HomeAutomation_TurnOff', [
    async (dialogContext, args) => {
        const devices = findEntities('HomeAutomation_Device', args.entities);
        const operations = findEntities('HomeAutomation_Operation', args.entities);

        const state = conversationState.get(dialogContext.context);
        state.homeAutomationTurnOff = state.homeAutomationTurnOff ? state.homeAutomationTurnOff + 1 : 1;
        await dialogContext.context.sendActivity(`${state.homeAutomationTurnOff}: You reached the "HomeAutomation.TurnOff" dialog.`);
        if (devices) {
            await dialogContext.context.sendActivity(`Found these "HomeAutomation_Device" entities:\n${devices.join(', ')}`);
        }
        if (operations) {
            await dialogContext.context.sendActivity(`Found these "HomeAutomation_Operation" entities:\n${operations.join(', ')}`);
        }
        await dialogContext.end();
    }
]);

dialogs.add('HomeAutomation_TurnOn', [
    async (dialogContext, args) => {
        const devices = findEntities('HomeAutomation_Device', args.entities);
        const operations = findEntities('HomeAutomation_Operation', args.entities);

        const state = conversationState.get(dialogContext.context);
        state.homeAutomationTurnOn = state.homeAutomationTurnOn ? state.homeAutomationTurnOn + 1 : 1;
        await dialogContext.context.sendActivity(`${state.homeAutomationTurnOn}: You reached the "HomeAutomation.TurnOn" dialog.`);
        if (devices) {
            await dialogContext.context.sendActivity(`Found these "HomeAutomation_Device" entities:\n${devices.join(', ')}`);
        }
        if (operations) {
            await dialogContext.context.sendActivity(`Found these "HomeAutomation_Operation" entities:\n${operations.join(', ')}`);
        }
        await dialogContext.end();
    }
]);

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

dialogs.add('Weather_GetCondition', [
    async (dialogContext, args) => {
        const locations = findEntities('Weather_Location', args.entities);

        const state = conversationState.get(dialogContext.context);
        state.weatherGetCondition = state.weatherGetCondition ? state.weatherGetCondition + 1 : 1;
        await dialogContext.context.sendActivity(`${state.weatherGetCondition}: You reached the "Weather.GetCondition" dialog.`);
        if (locations) {
            await dialogContext.context.sendActivity(`Found these "Weather_Location" entities:\n${locations.join(', ')}`);
        }
        await dialogContext.end();
    }
]);

dialogs.add('None', [
    async (dialogContext) => {
        const state = conversationState.get(dialogContext.context);
        state.noneIntent = state.noneIntent ? state.noneIntent + 1 : 1;
        await dialogContext.context.sendActivity(`${state.noneIntent}: You reached the "None" dialog.`);
        await dialogContext.end();
    }
]);

adapter.use(dispatcher);

// Listen for incoming Activities
server.post('/api/messages', (req, res) => {
    adapter.processActivity(req, res, async (context) => {
        if (context.activity.type === 'message') {
            const state = conversationState.get(context);
            const dc = dialogs.createContext(context, state);

            // Retrieve the LUIS results from our dispatcher LUIS application
            const luisResults = dispatcher.get(context);

            // Extract the top intent from LUIS and use it to select which LUIS application to dispatch to
            const topIntent = LuisRecognizer.topIntent(luisResults);

            const isMessage = context.activity.type === 'message';
            if (isMessage) {
                switch (topIntent) {
                    case 'l_homeautomation':
                        const homeAutoResults = await homeAutomation.recognize(context);
                        const topHomeAutoIntent = LuisRecognizer.topIntent(homeAutoResults);
                        await dc.begin(topHomeAutoIntent, homeAutoResults);
                        break;
                    case 'l_weather':
                        const weatherResults = await weather.recognize(context);
                        const topWeatherIntent = LuisRecognizer.topIntent(weatherResults);
                        await dc.begin(topWeatherIntent, weatherResults);
                        break;
                    case 'q_faq':
                        await faq.answer(context);
                        break;
                    default:
                        await dc.begin('None');
                }
            }

            if (!context.responded) {
                await dc.continue();
                if (!context.responded && isMessage) {
                    await dc.context.sendActivity(`Hi! I'm the LUIS dispatch bot. Say something and LUIS will decide how the message should be routed.`);
                }
            }
        }
    });
});

```

---
## <a name="run-the-bot"></a>Ausführen des Bots

Testen Sie den Bot mit dem [Bot Framework-Emulator](../bot-service-debug-emulator.md). Senden Sie Nachrichten wie „Licht anschalten“, um die Nachricht an die LUIS-App „HomeAutomation“ weiterzuleiten. Senden Sie Nachrichten wie „Wie ist das Wetter in Seattle“, um die Nachricht an die LUIS-App „Weather“ weiterzuleiten.

> [!NOTE] 
> Stellen Sie sicher, dass Sie alle LUIS-Apps veröffentlicht haben, die Sie im [LUIS-Portal](https://www.luis.ai) erstellt haben, bevor Sie den Bot ausführen. Stellen Sie auch sicher, dass Sie den QnA Maker-Dienst im [QnA Maker-Portal](https://qnamaker.ai) veröffentlicht haben.

![Senden von Nachrichten an den Dispatch-Bot](media/tutorial-dispatch/run-dispatch-bot.png)

## <a name="evaluate-the-dispatchers-performance"></a>Auswerten der Leistung des Dispatch-Bots

Manchmal gibt es Benutzernachrichten, die als Beispiele in den LUIS-Apps und den QnA Maker-Diensten angegeben werden, die die kombinierte LUIS-App nicht richtig verarbeiten kann. Überprüfen Sie die Leistung Ihrer App mit der Option `eval`. 

```
dispatch eval
```

Durch Ausführen von `dispatch eval` wird eine **Summary.html**-Datei generiert, die Statistiken zur Leistung des neuen Sprachmodells bereitstellt.

> [!TIP] 
> Sie können `dispatch eval` auf allen LUIS-Apps ausführen, nicht nur auf LUIS-Apps, die mit dem Dispatch-Tool erstellt wurden.

### <a name="edit-intents-for-duplicates-and-overlaps"></a>Bearbeiten von Absichten für Duplikate und Überlappungen

Überprüfen Sie Beispiel-Äußerungen, die in der Datei **Summary.html** als Duplikate gekennzeichnet sind, und entfernen Sie ähnliche oder überlappende Beispiele. Angenommen, Anforderungen wie „schalte meine Lampen ein“ werden in der LUIS-App `homeautomation` der Absicht „TurnOnLights“ zugeordnet, aber Anforderungen wie „Wieso gehen meine Lampen nicht an?“ werden der „None“-Absicht zugeordnet, weshalb sie an QnA Maker übergeben werden. Wenn Sie die LUIS-App und den QnA Maker mit dem Dispatch-Tool kombinieren, müssen Sie einen der folgenden Schritte durchführen: 

* Entfernen Sie die Absicht „None“ aus der ursprünglichen LUIS-App `homeautomation`, und fügen Sie die Äußerungen in dieser Absicht der Absicht „None“ in der Dispatcher-App hinzu.
* Wenn Sie die Absicht „None“ nicht aus der ursprünglichen LUIS-App entfernen, müssen Sie Ihrem Bot Logik hinzufügen, um die Nachrichten, die mit der Absicht übereinstimmen, an den QnA Maker-Dienst zu übergeben.

> [!TIP] 
> Tipps zur Verbesserung der Leistung Ihres Sprachmodells finden Sie unter [Best practices for Language Understanding (Bewährte Methoden für Language Understanding)](./bot-builder-concept-luis.md#best-practices-for-language-understanding).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Using LUIS for conversation flow (Verwenden von LUIS für den Konversationsfluss)][luis-v4-how-to]

[luis-v4-how-to]: bot-builder-howto-v4-luis.md
<!-- links -->

[HomeAutomationJSON]: https://aka.ms/dispatch-luis1
[WeatherJSON]: https://aka.ms/dispatch-luis2
[DispatchJSON]: https://aka.ms/dispatch-luis
[FAQ_TSV]: https://aka.ms/dispatch-qna-tsv

[DispatchTool]: https://github.com/Microsoft/botbuilder-tools/tree/master/Dispatch
[DispatchBotCS]: https://aka.ms/dispatch-sample-cs
[DispatchBotJs]: https://aka.ms/dispatch-sample-js



