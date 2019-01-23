---
title: Funktionsweise von Bots | Microsoft-Dokumentation
description: Dieser Artikel enthält eine Beschreibung der Funktionsweise von Aktivitäten und HTTP im Bot Framework SDK.
keywords: Konversationsfluss, Turn, Botkonversation, Dialoge, Eingabeaufforderungen, Wasserfälle, Dialogsatz
author: johnataylor
ms.author: johtaylo
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 1/10/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: a1b155db3ec717a1084ae0e098e8f22997a80b0e
ms.sourcegitcommit: b15cf37afc4f57d13ca6636d4227433809562f8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/11/2019
ms.locfileid: "54225935"
---
# <a name="how-bots-work"></a>Funktionsweise von Bots

[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

Ein Bot ist eine App, mit der Benutzer in Form einer Konversation mithilfe von Text, Grafiken (z.B. Karten oder Bilder) oder Sprache interagieren. Bei jeder Interaktion zwischen dem Benutzer und dem Bot wird eine *Aktivität* generiert. Der Bot Framework-Dienst, eine Komponente von Azure Bot Service, sendet Informationen zwischen der mit dem Bot verbundenen App des Benutzers (z.B. Facebook, Skype, Slack usw., als *Kanal* bezeichnet) und dem Bot hin und her. Jeder Kanal kann zusätzliche Informationen in die gesendeten Aktivitäten einfügen. Vor dem Erstellen von Bots sollten Sie sich unbedingt damit vertraut machen, wie ein Bot Aktivitätsobjekte nutzt, um mit seinen Benutzern zu kommunizieren. Zuerst sehen wir uns die Aktivitäten an, die ausgetauscht werden, wenn ein einfacher Echobot ausgeführt wird. 

![Aktivitätsdiagramm](media/bot-builder-activity.png)

Hier sind die beiden folgenden Aktivitätstypen dargestellt: *Konversationsaktualisierung* und *Nachricht*.

Der Bot Framework-Dienst kann beispielsweise eine Konversationsaktualisierung senden, wenn ein Teilnehmer der Konversation beitritt. Zu Beginn einer Konversation mit dem Bot Framework Emulator sind beispielsweise zwei Aktivitäten für die Konversationsaktualisierung vorhanden (eine für den Benutzer, der der Konversation beitritt, und eine für den beitretenden Bot). Zur Unterscheidung dieser Aktivitäten für die Konversationsaktualisierung sollten Sie überprüfen, ob die *members added*-Eigenschaft einen anderen Member als den Bot enthält. 

Mit der Aktivität vom Typ „Nachricht“ werden Konversationsinformationen zwischen den Teilnehmern übertragen. In einem Beispiel mit einem Echobot enthalten die Nachrichtenaktivitäten einfachen Text, der vom Kanal gerendert wird. Alternativ hierzu kann die Nachrichtenaktivität auch gesprochenen Text, vorgeschlagene Aktionen oder Karten enthalten, die angezeigt werden sollen.

In diesem Beispiel hat der Bot eine Nachrichtenaktivität als Antwort auf die erhaltene eingehende Nachrichtenaktivität erstellt und gesendet. Ein Bot kann aber auch auf andere Arten auf eine erhaltene Nachrichtenaktivität antworten. Es ist nicht ungewöhnlich, dass ein Bot auf eine Aktivität für eine Konversationsaktualisierung reagiert, indem er einen Begrüßungstext in einer Nachrichtenaktivität sendet. Weitere Informationen finden Sie unter [Willkommensumgebung für Benutzer](bot-builder-welcome-user.md).

### <a name="http-details"></a>HTTP-Details

Aktivitäten kommen im Bot vom Bot Framework-Dienst über eine HTTP POST-Anforderung an. Der Bot antwortet auf die eingehende POST-Anforderung mit dem HTTP-Statuscode 200. Aktivitäten, die vom Bot an den Kanal gesendet werden, werden in einer separaten HTTP POST-Anforderung an den Bot Framework-Dienst gesendet. Dies wird wiederum mit dem HTTP-Statuscode 200 bestätigt.

Im Protokoll ist aber nicht die Reihenfolge angegeben, in der diese POST-Anforderungen und die zugehörigen Bestätigungen durchgeführt werden. Damit sie zu allgemeinen HTTP-Dienstframeworks passen, sind diese Anforderungen normalerweise geschachtelt. Dies bedeutet, dass die ausgehende HTTP-Anforderung vom Bot innerhalb des Bereichs der eingehenden HTTP-Anforderung gesendet wird. Dieses Muster ist in der Abbildung oben dargestellt. Da hier zwei einzelne HTTP-Verbindungen aufeinander folgen, muss das Sicherheitsmodell beide abdecken.

### <a name="defining-a-turn"></a>Definieren eines Durchlaufs

In einer Konversation sprechen die Personen häufig einzeln. Sie wechseln sich also ab. Ein Bot reagiert im Allgemeinen auf die Eingabe eines Benutzers. Im Bot Framework SDK besteht ein _Turn_ (Durchlauf) aus der eingehenden Aktivität des Benutzers für den Bot und einer beliebigen Aktivität, die vom Bot als direkte Antwort zurück an den Benutzer gesendet wird. Sie können sich einen Durchlauf wie die Verarbeitung vorstellen, die dem Eintreffen einer bestimmten Aktivität zugeordnet ist.

Das Durchlaufkontext-Objekt (*turn context*) enthält Informationen zur Aktivität, z.B. den Absender und Empfänger, den Kanal und andere Daten, die zum Verarbeiten der Aktivität benötigt werden. Außerdem ermöglicht das Objekt das Hinzufügen von Informationen während des Turns für mehrere Ebenen des Bots.

Der Turn-Kontext ist eine der wichtigsten Abstraktionen im SDK. Er überträgt nicht nur die eingehende Aktivität an alle Middlewarekomponenten und die Anwendungslogik, sondern stellt auch den Mechanismus bereit, mit dem die Middlewarekomponenten und die Anwendungslogik ausgehende Aktivitäten senden können.

## <a name="the-activity-processing-stack"></a>Stapel für die Aktivitätsverarbeitung

Wir verwenden das vorherige Diagramm erneut, aber nun liegt der Schwerpunkt auf dem Eintreffen einer Nachrichtenaktivität.

![Stapel für die Aktivitätsverarbeitung](media/bot-builder-activity-processing-stack.png)

Im obigen Beispiel hat der Bot auf die Nachrichtenaktivität mit einer anderen Nachrichtenaktivität geantwortet, die die gleiche Textnachricht enthält. Die Verarbeitung beginnt damit, dass die HTTP POST-Anforderung auf dem Webserver ankommt. Dabei werden die Aktivitätsinformationen als JSON-Nutzlast übertragen. Unter C# ist dies normalerweise ein ASP.NET-Projekt. Bei einem JavaScript-Node.js-Projekt ist dies meist eines der gängigen Frameworks, z.B. Express oder Restify.

Der *Adapter*, eine integrierte Komponente des SDK, ist das Kernstück der SDK-Runtime. Die Aktivität wird als JSON (JavaScript Object Notation) im HTTP-POST-Text übertragen. Dieses JSON-Objekt wird deserialisiert, um das Activity-Objekt zu erstellen, das dann mit einem Aufruf der *process activity*-Methode an den Adapter übergeben wird. Der Adapter erstellt nach dem Empfang der Aktivität einen *Turn-Kontext* und ruft die Middleware auf. Die Bezeichnung *Turn-Kontext* entstammt der Verwendung des Worts „Turn“ (Durchlauf oder Wendung) und beschreibt die gesamte Verarbeitung im Zusammenhang mit dem Empfang einer Aktivität. Der Turn-Kontext ist eine der wichtigsten Abstraktionen im SDK. Er überträgt nicht nur die eingehende Aktivität an alle Middlewarekomponenten und die Anwendungslogik, sondern stellt auch den Mechanismus bereit, mit dem die Middlewarekomponenten und die Anwendungslogik ausgehende Aktivitäten senden können. Der Turn-Kontext stellt Antwortmethoden für das _Senden, Aktualisieren und Löschen von Aktivitäten_ bereit, mit denen auf eine Aktivität reagiert wird. Jede Antwortmethode wird in einem asynchronen Prozess ausgeführt. 

[!INCLUDE [alert-await-send-activity](../includes/alert-await-send-activity.md)]


## <a name="middleware"></a>Middleware
Die Middleware ist ähnlich aufgebaut wie jede andere Messagingmiddleware. Sie besteht aus einem linearen Satz von Komponenten, die nacheinander ausgeführt werden, sodass jede Komponente einen Vorgang für die Aktivität ausführen kann. Der letzte Schritt der Middlewarepipeline ist ein Rückruf, mit dem die Turn-Handlerfunktion (`OnTurnAsync` in C# und `onTurn` in JS) für die Botklasse aufgerufen wird, die die Anwendung im Adapter registriert hat. Der Turn-Handler akzeptiert einen Turn-Kontext als Argument. Die in der Turn-Handlerfunktion ausgeführte Anwendungslogik verarbeitet in der Regel den Inhalt der eingehenden Aktivität und generiert daraufhin eine oder mehrere Aktivitäten, die mithilfe der *send activity*-Funktion des Turn-Kontexts gesendet werden. Durch den Aufruf von *send activity* für den Turn-Kontext werden die Middlewarekomponenten für die ausgehenden Aktivitäten aufgerufen. Middlewarekomponenten werden vor und nach der Turn-Handlerfunktion des Bots ausgeführt. Die Ausführung ist grundsätzlich geschachtelt und wird daher mitunter als „Matroschkapuppe“ bezeichnet. Weitere ausführliche Informationen zu Middleware finden Sie unter dem Thema [Middleware](~/v4sdk/bot-builder-concept-middleware.md).

## <a name="bot-structure"></a>Botstruktur
In den folgenden Abschnitten untersuchen wir die Hauptkomponenten eines Bots.

### <a name="prerequisites"></a>Voraussetzungen
- Eine Kopie des **EchoBotWithCounter**-Beispiels im [C#](https://aka.ms/EchoBotWithStateCSharp)- oder [JS](https://aka.ms/EchoBotWithStateJS)-Format. Hier wird nur der relevante Code angezeigt, aber das Beispiel enthält den vollständigen Quellcode.

# <a name="ctabcs"></a>[C#](#tab/cs)

Ein Bot ist eine Art von [ASP.NET Core](https://docs.microsoft.com/aspnet/core/?view=aspnetcore-2.1)-Webanwendung. Wenn Sie sich die [ASP.NET-Grundlagen](https://docs.microsoft.com/aspnet/core/fundamentals/index?view=aspnetcore-2.1&tabs=aspnetcore2x) ansehen, werden Sie ähnlichen Code in Dateien wie **Program.cs** und **Startup.cs** sehen. Diese Dateien sind für alle Web-Apps erforderlich und nicht botspezifisch. 

### <a name="bot-logic"></a>Bot-Logik

Die Hauptlogik des Bots wird in der `EchoWithCounterBot`-Klasse definiert, die von der `IBot`-Schnittstelle abgeleitet wird. Mit `IBot` wird eine einzelne `OnTurnAsync`-Methode definiert. Ihre Anwendung muss diese Methode implementieren. `OnTurnAsync` verfügt über einen Turn-Kontext (turnContext), der Informationen zur eingehenden Aktivität bereitstellt. Die eingehende Aktivität entspricht der eingehenden HTTP-Anforderung. Es gibt verschiedene Arten von Aktivitäten. Daher überprüfen wir zunächst, ob Ihr Bot eine Nachricht empfangen hat. Wenn eine Nachricht vorhanden ist, rufen wir den Konversationszustand aus dem Turn-Kontext ab, inkrementieren den Turn-Zähler und speichern den neuen Wert des Turn-Zählers dann dauerhaft im Konversationszustand. Anschließend senden wir eine Nachricht zurück an den Benutzer, indem wir den SendActivityAsync-Aufruf verwenden. Die ausgehende Aktivität entspricht der ausgehenden HTTP-Anforderung.

```cs
public async Task OnTurnAsync(ITurnContext turnContext, CancellationToken cancellationToken = default(CancellationToken))
{
    if (turnContext.Activity.Type == ActivityTypes.Message)
    {
        // Get the conversation state from the turn context.
        var oldState = await _accessors.CounterState.GetAsync(turnContext, () => new CounterState());

        // Bump the turn count for this conversation.
        var newState = new CounterState { TurnCount = oldState.TurnCount + 1 };

        // Set the property using the accessor.
        await _accessors.CounterState.SetAsync(turnContext, newState);

        // Save the new turn count into the conversation state.
        await _accessors.ConversationState.SaveChangesAsync(turnContext);

        // Echo back to the user whatever they typed.
        var responseMessage = $"Turn {newState.TurnCount}: You sent '{turnContext.Activity.Text}'\n";
        await turnContext.SendActivityAsync(responseMessage);
    }
    else
    {
        await turnContext.SendActivityAsync($"{turnContext.Activity.Type} event detected");
    }
}
```

### <a name="set-up-services"></a>Einrichten von Diensten

Die `ConfigureServices`-Methode in der Datei „startup.cs“ lädt die verbundenen Dienste aus der [BOT](bot-builder-basics.md#the-bot-file)-Datei, fängt alle Fehler ab, die während des Turns einer Konversation auftreten, und protokolliert sie. Anschließend richtet sie Ihren Anmeldeinformationsanbieter ein und erstellt ein Konversationszustandsobjekt zum Speichern der Konversationsdaten im Arbeitsspeicher.

```csharp
services.AddBot<EchoWithCounterBot>(options =>
{
    // Creates a logger for the application to use.
    ILogger logger = _loggerFactory.CreateLogger<EchoWithCounterBot>();

    var secretKey = Configuration.GetSection("botFileSecret")?.Value;
    var botFilePath = Configuration.GetSection("botFilePath")?.Value;

    // Loads .bot configuration file and adds a singleton that your Bot can access through dependency injection.
    BotConfiguration botConfig = null;
    try
    {
        botConfig = BotConfiguration.Load(botFilePath ?? @".\BotConfiguration.bot", secretKey);
    }
    catch
    {
        //...
    }

    services.AddSingleton(sp => botConfig);

    // Retrieve current endpoint.
    var environment = _isProduction ? "production" : "development";
    var service = botConfig.Services.Where(s => s.Type == "endpoint" && s.Name == environment).FirstOrDefault();
    if (!(service is EndpointService endpointService))
    {
        throw new InvalidOperationException($"The .bot file does not contain an endpoint with name '{environment}'.");
    }

    options.CredentialProvider = new SimpleCredentialProvider(endpointService.AppId, endpointService.AppPassword);

    // Catches any errors that occur during a conversation turn and logs them.
    options.OnTurnError = async (context, exception) =>
    {
        logger.LogError($"Exception caught : {exception}");
        await context.SendActivityAsync("Sorry, it looks like something went wrong.");
    };

    // The Memory Storage used here is for local bot debugging only. When the bot
    // is restarted, everything stored in memory will be gone.
    IStorage dataStore = new MemoryStorage();

    // ...

    // Create Conversation State object.
    // The Conversation State object is where we persist anything at the conversation-scope.
    var conversationState = new ConversationState(dataStore);

    options.State.Add(conversationState);
});
```

Außerdem erstellt und registriert die `ConfigureServices`-Methode `EchoBotAccessors`, die in der Datei **EchoBotStateAccessors.cs** definiert und im öffentlichen Konstruktor `EchoWithCounterBot` übergeben werden, indem in ASP.NET Core das Framework für Abhängigkeitsinjektionen (Dependency Injection) verwendet wird.

```csharp
// Accessors created here are passed into the IBot-derived class on every turn.
services.AddSingleton<EchoBotAccessors>(sp =>
{
    var options = sp.GetRequiredService<IOptions<BotFrameworkOptions>>().Value;
    // ...
    var conversationState = options.State.OfType<ConversationState>().FirstOrDefault();
    // ...

    // Create the custom state accessor.
    // State accessors enable other components to read and write individual properties of state.
    var accessors = new EchoBotAccessors(conversationState)
    {
        CounterState = conversationState.CreateProperty<CounterState>(EchoBotAccessors.CounterStateName),
    };

    return accessors;
});
```

Die `Configure`-Methode gibt an, dass die App das Bot Framework und einige andere Dateien verwendet, und schließt damit die Konfiguration Ihrer App ab. Dieser Konfigurationsaufruf ist für alle Bots erforderlich, die das Bot Framework verwenden. `ConfigureServices` und `Configure` werden von der Runtime aufgerufen, wenn die App gestartet wird.

### <a name="manage-state"></a>Verwalten des Zustands

Diese Datei enthält eine einfache Klasse, die unser Bot zum Verwalten des aktuellen Zustands verwendet. Sie enthält nur eine `int`-Variable, die wir zum Inkrementieren des Zählers verwenden.

```cs
public class CounterState
{
    public int TurnCount { get; set; } = 0;
}
```

### <a name="accessor-class"></a>Accessorklasse

Die `EchoBotAccessors`-Klasse wird als Singleton in der `Startup`-Klasse erstellt und an die von IBot abgeleitete Klasse übergeben. In diesem Fall `public class EchoWithCounterBot : IBot`. Der Bot nutzt den Accessor zum Speichern von Konversationsdaten. Der Konstruktor von `EchoBotAccessors` wird in einem Konversationsobjekt übergeben, das in der Datei „Startup.cs“ erstellt wird.

```cs
public class EchoBotAccessors
{
    public EchoBotAccessors(ConversationState conversationState)
    {
        ConversationState = conversationState ?? throw new ArgumentNullException(nameof(conversationState));
    }

    public static string CounterStateName { get; } = $"{nameof(EchoBotAccessors)}.CounterState";

    public IStatePropertyAccessor<CounterState> CounterState { get; set; }

    public ConversationState ConversationState { get; }
}
```

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)

Der Yeoman-Generator erstellt eine [Restify](http://restify.com/)-Webanwendung. Wenn Sie sich in den zugehörigen Dokumenten den Schnellstart für Restify ansehen, werden Sie dort eine App finden, die der generierten Datei **index.js** ähnelt. In diesem Abschnitt werden in erster Linie die Dateien **package.json**, **.env**, **index.js**, **bot.js** und **echobot-with-counter.bot** beschrieben. Der Code in einigen der Dateien wird hier nicht gezeigt, Sie werden ihn jedoch sehen, wenn Sie den Bot ausführen. Zudem können Sie das Beispiel für den [Node.js-Echobot mit Zähler](https://aka.ms/js-echobot-with-counter) zu Rate ziehen.

### <a name="packagejson"></a>package.json

**package.json** gibt Abhängigkeiten und die zugehörigen Versionen für Ihren Bot an. All dies wird durch die Vorlage und Ihr System eingerichtet.

### <a name="env-file"></a>ENV-Datei

Die **ENV**-Datei gibt die Konfigurationsinformationen für Ihren Bot an, beispielsweise die Portnummer, die App-ID, das Kennwort usw. Wenn Sie bestimmte Technologien verwenden oder diesen Bot in einer Produktionsumgebung einsetzen, müssen Sie der Konfiguration Ihre spezifischen Schlüssel oder Ihre URL hinzufügen. Für diesen Echobot müssen Sie an dieser Stelle jedoch keinerlei Schritte ausführen, d. h. die App-ID und das Kennwort müssen hier nicht angegeben werden.

Zur Verwendung der **ENV**-Konfigurationsdatei muss der Vorlage ein zusätzliches Paket hinzugefügt werden.  Rufen Sie zuerst das `dotenv`-Paket aus npm ab:

`npm install dotenv`

### <a name="indexjs"></a>index.js

Mit `index.js` werden Ihr Bot und der Hostingdienst eingerichtet, mit dem Aktivitäten an Ihre Botlogik weitergeleitet werden.

#### <a name="required-libraries"></a>Erforderliche Bibliotheken

Am Anfang der Datei `index.js` finden Sie eine Reihe von erforderlichen Modulen oder Bibliotheken. Über diese Module erhalten Sie Zugriff auf eine Gruppe von Funktionen, die Sie Ihrer Anwendung ggf. hinzufügen können.

```javascript
// Import required packages
const path = require('path');
const restify = require('restify');

// Import required bot services. See https://aka.ms/bot-services to learn more about the different parts of a bot.
const { BotFrameworkAdapter, ConversationState, MemoryStorage } = require('botbuilder');
// Import required bot configuration.
const { BotConfiguration } = require('botframework-config');

const { EchoBot } = require('./bot');

// Read botFilePath and botFileSecret from .env file
// Note: Ensure you have a .env file and include botFilePath and botFileSecret.
const ENV_FILE = path.join(__dirname, '.env');
const env = require('dotenv').config({ path: ENV_FILE });
```

#### <a name="bot-configuration"></a>Botkonfiguration

Im nächsten Teil werden Informationen aus Ihrer Datei für die Botkonfiguration geladen.

```javascript
// Get the .bot file path
// See https://aka.ms/about-bot-file to learn more about .bot file its use and bot configuration.
const BOT_FILE = path.join(__dirname, (process.env.botFilePath || ''));
let botConfig;
try {
    // Read bot configuration from .bot file.
    botConfig = BotConfiguration.loadSync(BOT_FILE, process.env.botFileSecret);
} catch (err) {
    console.error(`\nError reading bot file. Please ensure you have valid botFilePath and botFileSecret set for your environment.`);
    console.error(`\n - The botFileSecret is available under appsettings for your Azure Bot Service bot.`);
    console.error(`\n - If you are running this bot locally, consider adding a .env file with botFilePath and botFileSecret.`);
    console.error(`\n - See https://aka.ms/about-bot-file to learn more about .bot file its use and bot configuration.\n\n`);
    process.exit();
}

// For local development configuration as defined in .bot file
const DEV_ENVIRONMENT = 'development';

// Define name of the endpoint configuration section from the .bot file
const BOT_CONFIGURATION = (process.env.NODE_ENV || DEV_ENVIRONMENT);

// Get bot endpoint configuration by service name
// Bot configuration as defined in .bot file
const endpointConfig = botConfig.findServiceByNameOrId(BOT_CONFIGURATION);
```

#### <a name="bot-adapter-http-server-and-bot-state"></a>Botadapter, HTTP-Server und Botzustand

In den nächsten Teilen werden der Server und der Adapter eingerichtet, die Ihrem Bot die Kommunikation mit dem Benutzer und das Senden von Antworten ermöglichen. Der Server lauscht am angegebenen Port in der Konfigurationsdatei **BotConfiguration.bot** oder führt ein Fallback zu _3978_ aus, um die Verbindung mit Ihrem Emulator herzustellen. Der Adapter fungiert gewissermaßen als Leiter für Ihren Bot, indem er die ein- und ausgehende Kommunikation, die Authentifizierung usw. steuert.

Außerdem erstellen wir ein Zustandsobjekt, für das `MemoryStorage` als Speicheranbieter verwendet wird. Dieser Zustand ist als `ConversationState` definiert, was einfach bedeutet, dass der Zustand der Konversation beibehalten wird. `ConversationState` speichert die für Sie relevanten Informationen im Arbeitsspeicher. In diesem Fall handelt es sich dabei um einen einfachen Turn-Zähler.

```javascript
// Create bot adapter.
// See https://aka.ms/about-bot-adapter to learn more about bot adapter.
const adapter = new BotFrameworkAdapter({
    appId: endpointConfig.appId || process.env.microsoftAppID,
    appPassword: endpointConfig.appPassword || process.env.microsoftAppPassword
});

// Catch-all for any unhandled errors in your bot.
adapter.onTurnError = async (context, error) => {
    // This check writes out errors to console log .vs. app insights.
    console.error(`\n [onTurnError]: ${ error }`);
    // Send a message to the user
    context.sendActivity(`Oops. Something went wrong!`);
    // Clear out state
    await conversationState.clear(context);
    // Save state changes.
    await conversationState.saveChanges(context);
};

// Define a state store for your bot. See https://aka.ms/about-bot-state to learn more about using MemoryStorage.
// A bot requires a state store to persist the dialog and user state between messages.
let conversationState;

// For local development, in-memory storage is used.
// CAUTION: The Memory Storage used here is for local bot debugging only. When the bot
// is restarted, anything stored in memory will be gone.
const memoryStorage = new MemoryStorage();
conversationState = new ConversationState(memoryStorage);

// Create the main dialog.
const bot = new EchoBot(conversationState);

// Create HTTP server
let server = restify.createServer();
server.listen(process.env.port || process.env.PORT || 3978, function() {
    console.log(`\n${ server.name } listening to ${ server.url }`);
    console.log(`\nGet Bot Framework Emulator: https://aka.ms/botframework-emulator`);
    console.log(`\nTo talk to your bot, open echoBot-with-counter.bot file in the Emulator`);
});
```

#### <a name="bot-logic"></a>Bot-Logik

Mit dem `processActivity`-Element des Adapters werden eingehende Aktivitäten an die Botlogik gesendet.
Der dritte Parameter in `processActivity` ist ein Funktionshandler, der aufgerufen wird, um die Botlogik auszuführen, nachdem die empfangene [Aktivität](#the-activity-processing-stack) vorab vom Adapter verarbeitet und über eine beliebige Middleware weitergeleitet wurde. Die Variable mit dem Turn-Kontext, die als Argument an den Funktionshandler übergeben wird, kann verwendet werden, um Informationen zur eingehenden Aktivität, zum Absender und Empfänger, zum Kanal, zur Konversation usw. bereitzustellen. Die Aktivitätsverarbeitung wird an das `onTurn`-Element des Echobots weitergeleitet.

```javascript
// Listen for incoming requests.
server.post('/api/messages', (req, res) => {
    // Route received request to adapter for processing
    adapter.processActivity(req, res, (context) => {
        // Route to main dialog.
        await bot.onTurn(context);
    });
});
```

### <a name="echobot"></a>Echobot

Die gesamte Aktivitätsverarbeitung wird an den `onTurn`-Handler dieser Klasse weitergeleitet. Wenn die Klasse erstellt wird, wird ein Zustandsobjekt übergeben. Mit diesem Zustandsobjekt erstellt der Konstruktor einen `this.countProperty`-Accessor, um den Turn-Zähler für diesen Bot dauerhaft zu speichern.

Bei jedem Turn überprüfen wir, ob der Bot eine Nachricht empfangen hat. Wenn der Bot keine Nachricht empfangen hat, geben wir den empfangenen Aktivitätstyp zurück. Als Nächstes erstellen wir eine Zustandsvariable, die die Informationen der Konversation Ihres Bots enthält. Wenn für die count-Variable `undefined` angegeben ist, wird sie auf 1 festgelegt (beim ersten Start Ihres Bots) oder mit jeder neuen Nachricht inkrementiert. Wir geben die Anzahl zusammen mit der gesendeten Nachricht an den Benutzer zurück. Abschließend legen wir die Anzahl fest und speichern die Änderungen des Zustands.

```javascript
const { ActivityTypes } = require('botbuilder');

// Turn counter property
const TURN_COUNTER_PROPERTY = 'turnCounterProperty';

class EchoBot {

    constructor(conversationState) {
        // Creates a new state accessor property.
        // See https://aka.ms/about-bot-state-accessors to learn more about the bot state and state accessors
        this.countProperty = conversationState.createProperty(TURN_COUNTER_PROPERTY);
        this.conversationState = conversationState;
    }

    async onTurn(turnContext) {
        // Handle message activity type. User's responses via text or speech or card interactions flow back to the bot as Message activity.
        // Message activities may contain text, speech, interactive cards, and binary or unknown attachments.
        // see https://aka.ms/about-bot-activity-message to learn more about the message and other activity types
        if (turnContext.activity.type === ActivityTypes.Message) {
            // read from state.
            let count = await this.countProperty.get(turnContext);
            count = count === undefined ? 1 : ++count;
            await turnContext.sendActivity(`${ count }: You said "${ turnContext.activity.text }"`);
            // increment and set turn counter.
            await this.countProperty.set(turnContext, count);
        } else {
            // Generic handler for all other activity types.
            await turnContext.sendActivity(`[${ turnContext.activity.type } event detected]`);
        }
        // Save state changes
        await this.conversationState.saveChanges(turnContext);
    }
}

exports.EchoBot = EchoBot;
```

---

## <a name="the-bot-file"></a>BOT-Datei

Die **BOT**-Datei enthält Informationen, z.B. den Endpunkt, die App-ID und das Kennwort, und verweist auf Dienste, die vom Bot genutzt werden. Diese Datei wird für Sie erstellt, wenn Sie mit dem Erstellen eines Bots anhand einer Vorlage beginnen, Sie können jedoch mithilfe des Emulators oder mit anderen Tools eine eigene BOT-Datei erstellen. Sie können die BOT-Datei angeben, die beim Testen Ihres Bots mit dem [Emulator](../bot-service-debug-emulator.md) verwendet werden soll.

```json
{
    "name": "echobot-with-counter",
    "services": [
        {
            "type": "endpoint",
            "name": "development",
            "endpoint": "http://localhost:3978/api/messages",
            "appId": "",
            "appPassword": "",
            "id": "1"
        }
    ],
    "padlock": "",
    "version": "2.0"
}
```

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- Informationen zur Rolle des Zustands in Bots finden Sie unter [Verwalten des Zustands](bot-builder-concept-state.md).
- Informationen zur Rolle einer BOT-Datei bei der Verwaltung von Ressourcen finden Sie unter [Verwalten von Ressourcen mit einer BOT-Datei](bot-file-basics.md).
- Nutzen Sie zum Erstellen Ihres ersten Bots einen der Schnellstarts: Erstellen eines Bots mit [Azure Bot Service](../bot-service-quickstart.md), [C# ](../dotnet/bot-builder-dotnet-sdk-quickstart.md) oder [JavaScript](../javascript/bot-builder-javascript-quickstart.md)
