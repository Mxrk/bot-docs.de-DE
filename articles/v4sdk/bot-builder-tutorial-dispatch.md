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
ms.openlocfilehash: 62cf3663a6e1c9b9321d7b74393b95e4a2ed3a69
ms.sourcegitcommit: fd7781a06303fee5f39a253da5b3a3818d54b2ba
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/28/2018
ms.locfileid: "53806771"
---
# <a name="use-multiple-luis-and-qna-models"></a>Verwenden von mehreren LUIS- und QnA-Modellen

[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

In diesem Tutorial wird veranschaulicht, wie Sie den Dispatch-Dienst zum Weiterleiten von Äußerungen verwenden, wenn von einem Bot mehrere LUIS-Modelle und QnA Maker-Dienste für unterschiedliche Szenarien unterstützt werden. In diesem Fall konfigurieren wir den Dispatch-Dienst mit mehreren LUIS-Modellen für Konversationen im Bereich Gebäudeautomatisierung und Wetterdaten und den QnA Maker-Dienst für die Beantwortung von Fragen basierend auf einer FAQ-Textdatei als Eingabe. In diesem Beispiel werden die folgenden Dienste kombiniert.

| Name | Beschreibung |
|------|------|
| HomeAutomation | Eine LUIS-App, die eine Absicht zur Gebäudeautomatisierung mit den zugeordneten Entitätsdaten erkennt.|
| Weather | Eine LUIS-App, die die Absichten `Weather.GetForecast` und `Weather.GetCondition` mit Standortdaten erkennt.|
| Häufig gestellte Fragen  | Eine QnA Maker-Wissensdatenbank, die Antworten auf einfache Fragen zum Bot liefert. |

## <a name="prerequisites"></a>Voraussetzungen

- Der Code in diesem Artikel basiert auf dem Beispiel **NLP mit Dispatch**. Sie benötigen eine Kopie des Beispiels im [C#](https://aka.ms/dispatch-sample-cs)- oder [JS](https://aka.ms/dispatch-sample-js)-Format.
- Sie müssen mit den [Bot-Grundlagen](bot-builder-basics.md), der [Verarbeitung natürlicher Sprache](bot-builder-howto-v4-luis.md), [QnA Maker](bot-builder-howto-qna.md) und der [BOT](bot-file-basics.md)-Datei vertraut sein.
- [Bot Framework Emulator](https://github.com/Microsoft/BotFramework-Emulator/blob/master/README.md#download) für die Durchführung von Tests.

## <a name="create-the-services-and-test-the-bot"></a>Erstellen der Dienste und Testen des Bots

Sie können gemäß der Anleitung in der **Infodatei** für [C#](https://aka.ms/dispatch-sample-readme-cs) oder [JS](https://aka.ms/dispatch-sample-readme-js) vorgehen und den Bot mithilfe von CLI-Aufrufen erstellen. Alternativ können Sie Ihren Bot mithilfe der folgenden Schritte manuell über die Benutzeroberflächen von Azure, LUIS und QnA Maker erstellen.

 ### <a name="create-your-bot-using-service-ui"></a>Erstellen Ihres Bots per Dienstbenutzeroberfläche
 
Wenn Sie Ihren Bot manuell erstellen möchten, laden Sie zunächst die vier folgenden Dateien aus dem GitHub-Repository [BotFramework-Samples](https://github.com/Microsoft/BotFramework-Samples) in einen lokalen Ordner herunter: [home-automation.json](https://aka.ms/dispatch-home-automation-json), [weather.json](https://aka.ms/dispatch-weather-json), [nlp-with-dispatchDispatch.json](https://aka.ms/dispatch-dispatch-json), [QnAMaker.tsv](https://aka.ms/dispatch-qnamaker-tsv)

### <a name="manually-create-luis-apps"></a>Manuelles Erstellen von LUIS-Apps

Melden Sie sich beim [LUIS-Webportal](https://www.luis.ai/) an. Wählen Sie im Abschnitt _Meine Apps_ die Registerkarte _Import new app_ (Neue App importieren) aus. Daraufhin wird das folgende Dialogfeld angezeigt:

![Importieren der LUIS-JSON-Datei](./media/tutorial-dispatch/import-new-luis-app.png)

Wählen Sie die Schaltfläche _Choose app file_ (App-Datei auswählen) und anschließend die heruntergeladene Datei „home-automation.json“ aus. Lassen Sie das optionale Namensfeld leer. Wählen Sie _Fertig_aus.

Warten Sie, bis LUIS Ihre Home Automation-App geöffnet hat, und wählen Sie anschließend die Schaltfläche _Trainieren_ aus. Dadurch wird Ihre App auf der Grundlage der Äußerungen trainiert, die Sie soeben mithilfe der Datei „home-automation.json“ importiert haben.

Wählen Sie nach Abschluss des Trainings die Schaltfläche _Veröffentlichen_ aus. Daraufhin wird das folgende Dialogfeld angezeigt:

![Veröffentlichen der LUIS-App](./media/tutorial-dispatch/publish-luis-app.png)

Wählen Sie die Produktionsumgebung und anschließend die Schaltfläche _Veröffentlichen_ aus.

Warten Sie, bis Ihre neue LUIS-App veröffentlicht wurde, und wählen Sie dann die Registerkarte _VERWALTEN_ aus. Notieren Sie sich die Werte `Application ID` und `Display name` von der Seite mit den Anwendungsinformationen. Notieren Sie sich die Werte `Authoring Key` und `Region` von der Seite mit den Schlüssel- und Endpunktinformationen. Diese Werte werden später von der Datei „nlp-with-dispatch.bot“ verwendet.

_Trainieren_ und _veröffentlichen_ Sie anschließend sowohl Ihre LUIS-Wetter-App als auch Ihre LUIS-Dispatch-App, indem Sie die gleichen Schritte für die lokal heruntergeladenen Dateien „weather.json“ und „nlp-with-dispatchDispatch.json“ ausführen.

### <a name="manually-create-qna-maker-app"></a>Manuelles Erstellen der QnA Maker-App

Wenn Sie eine QnA Maker-Wissensdatenbank einrichten möchten, müssen Sie zunächst einen QnA Maker-Dienst in Azure einrichten. Eine entsprechende Anleitung finden Sie [hier](https://aka.ms/create-qna-maker). Melden Sie sich als Nächstes beim [QnA Maker-Webportal](https://qnamaker.ai) an. Navigieren Sie zum zweiten Schritt:

![QnA-Erstellung: Schritt 2](./media/tutorial-dispatch/create-qna-step-2.png)

Wählen Sie Folgendes aus:
1. Ihr Azure AD-Konto
1. Den Namen Ihres Azure-Abonnements
1. Den Namen, den Sie für Ihren QnA Maker-Dienst erstellt haben (Sollte Ihr Azure QnA-Dienst zunächst nicht in der Pulldownliste angezeigt werden, aktualisieren Sie die Seite.) 

Navigieren Sie zum dritten Schritt:

![QnA-Erstellung: Schritt 3](./media/tutorial-dispatch/create-qna-step-3.png)

Geben Sie einen Namen für Ihre QnA Maker-Wissensdatenbank an. In diesem Beispiel verwenden wir den Namen „sample-qna“.

Navigieren Sie zum vierten Schritt:

![QnA-Erstellung: Schritt 4](./media/tutorial-dispatch/create-qna-step-4.png)

Wählen Sie die Option _+ Datei hinzufügen_ und anschließend die heruntergeladene Datei „QnAMaker.tsv“ aus.

Es besteht zusätzlich die Möglichkeit, Ihrer Wissensdatenbank eine Persönlichkeit für _Geplauder_ hinzuzufügen, diese Option wird in unserem Beispiel jedoch nicht genutzt.

Wählen Sie _Save and train_ (Speichern und trainieren) und nach Abschluss dieses Vorgangs die Registerkarte _VERÖFFENTLICHEN_ aus, um Ihre App zu veröffentlichen.

Wählen Sie nach Veröffentlichung Ihrer QnA Maker-App die Registerkarte _EINSTELLUNGEN_ aus, und scrollen Sie nach unten zu den Bereitstellungsdetails. Notieren Sie sich die folgenden Werte aus der HTTP-Beispielanforderung für _Postman_.

```
POST /knowledgebases/<Your_Knowledgebase_Id>/generateAnswer
Host: <Your_Hostname>
Authorization: EndpointKey <Your_Endpoint_Key>
```
Diese Werte werden später von der Datei „nlp-with-dispatch.bot“ verwendet.

### <a name="manually-update-your-bot-file"></a>Manuelles Aktualisieren Ihrer BOT-Datei

Nach Erstellung aller Dienst-Apps müssen der Datei „nlp-with-dispatch.bot“ die jeweiligen Informationen hinzugefügt werden. Öffnen Sie die Datei innerhalb der zuvor heruntergeladenen C#- oder JS-Beispieldatei. Fügen Sie jedem Abschnitt vom Typ „luis“ oder „dispatch“ die folgenden Werte hinzu:

```
"appId": "<Your_Recorded_App_Id>",
"authoringKey": "<Your_Recorded_Authoring_Key>",
"subscriptionKey": "<Your_Recorded_Authoring_Key>",
"version": "0.1",
"region": "<Your_Recorded_Region>",
```

Fügen Sie dem Abschnitt vom Typ „qna“ die folgenden Werte hinzu:

```
"type": "qna",
"name": "sample-qna",
"id": "201",
"kbId": "<Your_Recorded_Knowledgebase_Id>",
"subscriptionKey": "<Your_Azure_Subscription_Key>", // Used when creating your QnA service.
"endpointKey": "<Your_Recorded_Endpoint_Key>",
"hostname": "<Your_Recorded_Hostname>"
```

Speichern Sie die Datei, nachdem Sie alle erforderlichen Änderungen vorgenommen haben.

### <a name="test-your-bot"></a>Testen Ihres Bots

Verwenden Sie als Nächstes den Emulator, um das Beispiel auszuführen. Wählen Sie nach dem Öffnen des Emulators die Datei „nlp-with-dispatch.bot“ aus.

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

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Die verschiedenen Abschnitte der BOT-Datei sind im Beispielcode durch vordefinierte Namenskonstanten gekennzeichnet. Sollten Sie die ursprünglichen Abschnittsnamen des Beispiels in der Datei _nlp-with-dispatch.bot_ geändert haben, navigieren Sie in der Datei **bot.js**, **homeAutomation.js**, **qna.js** oder **weather.js** zur entsprechenden Konstantendeklaration, und ändern Sie den Eintrag in den geänderten Namen.  
```javascript
// In file bot.js
// this is the LUIS service type entry in the .bot file.
const DISPATCH_CONFIG = 'nlp-with-dispatchDispatch';

// In file homeAutomation.js
// this is the LUIS service type entry in the .bot file.
const LUIS_CONFIGURATION = 'Home Automation';

// In file qna.js
// Name of the QnA Maker service in the .bot file.
const QNA_CONFIGURATION = 'sample-qna';

// In file weather.js
// this is the LUIS service type entry in the .bot file.
const WEATHER_LUIS_CONFIGURATION = 'Weather';
```

In **bot.js** werden die Informationen aus der Konfigurationsdatei _nlp-with-dispatch.bot_ verwendet, um eine Verbindung zwischen Ihrem Dispatch-Bot und den verschiedenen Diensten herzustellen. Jeder Konstruktor sucht und verwendet die entsprechenden Abschnitte der Konfigurationsdatei anhand der oben angegebenen Abschnittsnamen.

```javascript
class DispatchBot {
    constructor(conversationState, userState, botConfig) {
        //...
        this.homeAutomationDialog = new HomeAutomation(conversationState, userState, botConfig);
        this.weatherDialog = new Weather(botConfig);
        this.qnaDialog = new QnA(botConfig);

        this.conversationState = conversationState;
        this.userState = userState;

        // dispatch recognizer
        const dispatchConfig = botConfig.findServiceByNameOrId(DISPATCH_CONFIG);
        //...
```
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

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

In der Methode `onTurn` von **bot.js** wird geprüft, ob eingehende Nachrichten des Benutzers vorhanden sind. Wenn der Typ _ActivityType.Message_ empfangen wird, wird die Nachricht per _dispatchRecognizer_ des Bots übermittelt.

```javascript
if (turnContext.activity.type === ActivityTypes.Message) {
    // determine which dialog should fulfill this request
    // call the dispatch LUIS model to get results.
    const dispatchResults = await this.dispatchRecognizer.recognize(turnContext);
    const dispatchTopIntent = LuisRecognizer.topIntent(dispatchResults);
    //...
 }
```
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

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Wenn das Modell ein Ergebnis produziert, wird angegeben, welcher Dienst die Äußerung am besten verarbeiten kann. Der Code in diesem Bot leitet die Anforderung an den entsprechenden Dienst weiter.

```javascript
switch (dispatchTopIntent) {
   case HOME_AUTOMATION_INTENT:
      await this.homeAutomationDialog.onTurn(turnContext);
      break;
   case WEATHER_INTENT:
      await this.weatherDialog.onTurn(turnContext);
      break;
   case QNA_INTENT:
      await this.qnaDialog.onTurn(turnContext);
      break;
   case NONE_INTENT:
      default:
      // Unknown request
       await turnContext.sendActivity(`I do not understand that.`);
       await turnContext.sendActivity(`I can help with weather forecast, turning devices on and off and answer general questions like 'hi', 'who are you' etc.`);
 }
 
 // In homeAutomation.js
 async onTurn(turnContext) {
    // make call to LUIS recognizer to get home automation intent + entities
    const homeAutoResults = await this.luisRecognizer.recognize(turnContext);
    const topHomeAutoIntent = LuisRecognizer.topIntent(homeAutoResults);
    // depending on intent, call turn on or turn off or return unknown
    switch (topHomeAutoIntent) {
       case HOME_AUTOMATION_INTENT:
          await this.handleDeviceUpdate(homeAutoResults, turnContext);
          break;
       case NONE_INTENT:
       default:
         await turnContext.sendActivity(`HomeAutomation dialog cannot fulfill this request.`);
    }
}
    
// In weather.js
async onTurn(turnContext) {
   // Call weather LUIS model.
   const weatherResults = await this.luisRecognizer.recognize(turnContext);
   const topWeatherIntent = LuisRecognizer.topIntent(weatherResults);
   // Get location entity if available.
   const locationEntity = (LOCATION_ENTITY in weatherResults.entities) ? weatherResults.entities[LOCATION_ENTITY][0] : undefined;
   const locationPatternAnyEntity = (LOCATION_PATTERNANY_ENTITY in weatherResults.entities) ? weatherResults.entities[LOCATION_PATTERNANY_ENTITY][0] : undefined;
   // Depending on intent, call "Turn On" or "Turn Off" or return unknown.
   switch (topWeatherIntent) {
      case GET_CONDITION_INTENT:
         await turnContext.sendActivity(`You asked for current weather condition in Location = ` + (locationEntity || locationPatternAnyEntity));
         break;
      case GET_FORECAST_INTENT:
         await turnContext.sendActivity(`You asked for weather forecast in Location = ` + (locationEntity || locationPatternAnyEntity));
         break;
      case NONE_INTENT:
      default:
         wait turnContext.sendActivity(`Weather dialog cannot fulfill this request.`);
   }
}
    
// In qna.js
async onTurn(turnContext) {
   // Call QnA Maker and get results.
   const qnaResult = await this.qnaRecognizer.generateAnswer(turnContext.activity.text, QNA_TOP_N, QNA_CONFIDENCE_THRESHOLD);
   if (!qnaResult || qnaResult.length === 0 || !qnaResult[0].answer) {
       await turnContext.sendActivity(`No answer found in QnA Maker KB.`);
       return;
    }
    // respond with qna result
    await turnContext.sendActivity(qnaResult[0].answer);
}
```
---

## <a name="edit-intents-to-improve-performance"></a>Bearbeiten von Absichten zur Optimierung der Leistung

Sobald Ihr Bot aktiv ist, kann dessen Leistung durch Entfernen ähnlicher oder sich überschneidender Äußerungen optimiert werden. Angenommen, Anforderungen wie „schalte meine Lampen ein“ werden in der LUIS-App `Home Automation` der Absicht „TurnOnLights“ zugeordnet, aber Anforderungen wie „Warum gehen meine Lampen nicht an?“ werden der „None“-Absicht zugeordnet, damit sie an QnA Maker übergeben werden. Wenn Sie die LUIS-App und den QnA Maker mit dem Dispatch-Tool kombinieren, müssen Sie einen der folgenden Schritte ausführen:

* Entfernen Sie die Absicht „None“ aus der ursprünglichen LUIS-App `Home Automation`, und fügen Sie die Äußerungen aus dieser Absicht stattdessen der Absicht „None“ in der Dispatcher-App hinzu.
* Wenn Sie die Absicht „None“ nicht aus der ursprünglichen LUIS-App entfernen, müssen Sie Ihrem Bot stattdessen eine Logik hinzufügen, die Nachrichten mit der Absicht „None“ an den QnA Maker-Dienst übergibt.

Beide Aktionen sorgen dafür, dass Ihr Bot Benutzeranfragen seltener damit beantwortet, dass keine Antwort gefunden wurde. 

## <a name="additional-resources"></a>Zusätzliche Ressourcen 

**Löschen von Ressourcen:** In diesem Beispiel werden einige Anwendungen und Ressourcen erstellt, die Sie mit den unten angegebenen Schritten löschen können. Sie sollten jedoch keine Ressourcen löschen, die von *anderen Apps oder Diensten* benötigt werden. 

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

**Bewährte Methode:** Machen Sie sich mit der bewährten Methode für [LUIS](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/luis-concept-best-practices) und [QnA Maker](https://docs.microsoft.com/en-us/azure/cognitive-services/qnamaker/concepts/best-practices) vertraut, um die in diesem Beispiel genutzten Dienste zu verbessern.
