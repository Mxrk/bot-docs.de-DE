---
title: Anatomie eines Bots | Microsoft-Dokumentation
description: Aufschlüsselung der verschiedenen Teile eines Bots und ihrer Funktionsweise
keywords: ''
author: ivorb
ms.author: ivorb
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 06/25/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 238be22eff746edff30a5446d20991dfaedc945a
ms.sourcegitcommit: 44f100a588ffda19c275b118f4f97029f12d1449
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/26/2018
ms.locfileid: "42928288"
---
# <a name="anatomy-of-a-bot"></a>Anatomie eines Bots

[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

[Einfach erklärt](bot-builder-basics.md), ist ein Bot eine Konversations-App, mit der Benutzer mithilfe von Nachrichten kommunizieren. Im Folgenden werden die grundlegende Struktur einer Web-App in der jeweiligen Programmiersprache und darauf aufbauend das Bot Framework SDK erläutert.

Ein Bot besteht aus mehreren verschiedenen Komponenten, die Ihnen das [Senden von Nachrichten](./bot-builder-howto-send-messages.md) und Nachverfolgen des [Zustands](./bot-builder-storage-concept.md) ermöglichen. Sie können mit Ihrem Bot über den [Emulator](../bot-service-debug-emulator.md), [WebChat](../bot-service-manage-test-webchat.md) oder in der Produktionsumgebung über einen der [Kanäle](bot-concepts.md) kommunizieren. 

In diesem Dokument werden wir einen einfachen Echobot Schritt für Schritt durchgehen und jedes der dazugehörigen Elemente untersuchen.

## <a name="files-created-for-echo-bot"></a>Für den Echobot erstellte Dateien

Jede Programmiersprache erstellt andere Dateien für den Echobot, dem wir in diesem Beispiel den Namen **BasicEcho** gegeben haben. Diese Dateien lassen sich in drei Abschnitte unterteilen: Systemabschnitt, Hilfsprogrammelemente und Kern des Bots. In der folgenden Tabelle sind die wichtigsten Dateien für C# und JavaScript aufgeführt.

| C# | JavaScript |
| --- | --- |
| `Properties > launchSettings.json` <br> `wwwroot > default.htm` <br> `appsettings.json` <br> `BasicEcho.bot` <br> `EchoBot.cs` <br> `EchoState.cs` <br> `Program.cs` <br> `readme.md` <br> `Startup.cs` | `.env` <br> `app.js` <br> `package.json` <br> `README.md` <br> `basicEcho.bot` |

Im Folgenden werden wir nach Abschnitt unterteilt einige wichtige Features dieser Dateien erläutern. Einige der Dateien verfügen über einen eigenen Satz von Includes, bei denen es sich sowohl um botspezifische als auch allgemeine Programmierbibliotheken handelt. Diese Includes stellen eine Reihe erforderlicher sowie einige möglicherweise erwünschte Funktionen für Ihre Anwendung bereit. Wir werden die Details dieser Includes hier nicht behandeln. Falls Sie sich dafür interessieren, können Sie jedoch in Visual Studio die Definitionen nachlesen und überprüfen, zu welchen Namespaces sie gehören.

## <a name="system-section"></a>Systemabschnitt

Der Systemabschnitt enthält die Elemente, die Sie benötigen, um Ihren Bot als Standardanwendung einzurichten. Er umfasst die Konfigurationsdateien, den Adapter und einige JSON-Dateien. Mit Ausnahme einiger Konfigurationsdateien, für die Sie spezifische Informationen angeben müssen, können diese Elemente unverändert bleiben (oft ist dies sogar empfehlenswert).

# <a name="ctabcs"></a>[C#](#tab/cs)

Ein Bot ist eine Art von [ASP.NET Core-Web](https://docs.microsoft.com/en-us/aspnet/core/?view=aspnetcore-2.1)-Anwendungsframework. Wenn Sie sich die [ASP.NET-Grundlagen](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/index?view=aspnetcore-2.1&tabs=aspnetcore2x) ansehen, werden Sie ähnlichen Code in Dateien wie „appsettings.json“, „Program.cs“ und „Startup.cs“ sehen, die im Folgenden erörtert werden. Diese Dateien sind für alle Web-Apps erforderlich und nicht botspezifisch. Der Code in einigen der Dateien wird hier nicht gezeigt, Sie werden ihn jedoch sehen, wenn Sie den Bot ausführen.

### <a name="appsettingsjson"></a>appsettings.json

Diese Datei enthält lediglich die Einstellungsinformationen für Ihren Bot in einem einfachen JSON-Format. In erster Linie handelt es sich dabei um die App-ID und das Kennwort.  Bei Tests mit dem [Emulator](../bot-service-debug-emulator.md) werden diese Informationen nicht benötigt und können leer bleiben, in der Produktion sind sie jedoch erforderlich.

### <a name="programcs"></a>Program.cs

Diese Datei ist für die korrekte Ausführung Ihrer ASP.NET-Web-App notwendig und gibt die auszuführende `Startup`-Klasse an.

### <a name="startupcs"></a>Startup.cs

**Startup.cs** enthält weitere interessante Teile, die an späterer Stelle behandelt werden. Zunächst werden wir uns jedoch mit den darin enthaltenen Systemabschnitten befassen.

Der Konstruktor für `Startup` gibt die App-Einstellungen und Umgebungsvariablen an und erstellt die Konfiguration für Ihre Web-App.

Die `Configure`-Methode gibt an, dass die App das Bot Framework und einige andere Dateien verwendet, und schließt damit die Konfiguration Ihrer App ab. Dieser Konfigurationsaufruf ist für alle Bots erforderlich, die das Bot Framework verwenden.

```cs
using System;
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Bot.Builder.BotFramework;
using Microsoft.Bot.Builder.Core.Extensions;
using Microsoft.Bot.Builder.Integration.AspNet.Core;
using Microsoft.Bot.Builder.TraceExtensions;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;

namespace BasicEcho
{
    public class Startup
    {
        // This method gets called by the runtime. Use this method to add services to the container.
        // For more information on how to configure your application, visit https://go.microsoft.com/fwlink/?LinkID=398940
        public Startup(IHostingEnvironment env)
        {
            var builder = new ConfigurationBuilder()
                .SetBasePath(env.ContentRootPath)
                .AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
                .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true)
                .AddEnvironmentVariables();

            Configuration = builder.Build();
        }

        public IConfiguration Configuration { get; }

        // ...
        // Definition of ConfigureServices covered in the next section
        // ...

        // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
        public void Configure(IApplicationBuilder app, IHostingEnvironment env)
        {
            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }

            app.UseDefaultFiles()
                .UseStaticFiles()
                .UseBotFramework();
        }
    }
}

```

### <a name="launchsettingsjson-and-readmemd"></a>„launchSettings.json“ und „readme.md“

**launchSettings.json** enthält lediglich einige Informationen zum Einrichten der Konfiguration für Web-Apps. **readme.md** ist eine einfache einzeilige Erklärung zum Echobot. Sie müssen den Inhalt dieser Dateien nicht kennen, um den Bot zu verstehen. 

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)

Der Systemabschnitt enthält im Wesentlichen die Datei **package.json**, die **ENV**-Datei sowie die Dateien **app.js** und **README.md**. Der Code in einigen der Dateien wird hier nicht gezeigt, Sie werden ihn jedoch sehen, wenn Sie den Bot ausführen.

### <a name="packagejson"></a>package.json

**package.json** gibt Abhängigkeiten und die zugehörigen Versionen für Ihren Bot an. All dies wird durch die Vorlage und Ihr System eingerichtet.

### <a name="env-file"></a>ENV-Datei

Die **ENV**-Datei gibt die Konfigurationsinformationen für Ihren Bot an, beispielsweise die Portnummer, die App-ID, das Kennwort usw. Wenn Sie bestimmte Technologien verwenden oder diesen Bot in einer Produktionsumgebung einsetzen, müssen Sie der Konfiguration Ihre spezifischen Schlüssel oder Ihre URL hinzufügen. Für diesen Echobot müssen Sie an dieser Stelle jedoch keinerlei Schritte ausführen, d. h. die App-ID und das Kennwort müssen hier nicht angegeben werden. 

Zur Verwendung der **ENV**-Konfigurationsdatei muss der Vorlage ein zusätzliches Paket hinzugefügt werden.  Rufen Sie zuerst das `dotenv`-Paket aus npm ab:

`npm install dotenv`

Fügen Sie Ihrem Bot anschließend die folgende Zeile sowie die anderen erforderlichen Bibliotheken hinzu:

```javascript
const dotenv = require('dotenv');
```

### <a name="appjs"></a>app.js

Am Anfang der Datei `app.js` sind der Server und der Adapter angegeben, die Ihrem Bot die Kommunikation mit dem Benutzer und das Senden von Antworten ermöglichen. Der Server lauscht am angegebenen Port in der **ENV**-Konfiguration oder führt ein Fallback zu 3978 aus, um die Verbindung mit Ihrem Emulator herzustellen. Der Adapter fungiert gewissermaßen als Leiter für Ihren Bot, indem er die ein- und ausgehende Kommunikation, die Authentifizierung usw. steuert. 

```javascript
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
``` 

### <a name="readmemd"></a>README.md

Die Datei **README.md** enthält nützliche Informationen zu einigen Aspekten der Erstellung eines Bots, beispielsweise eine einfache Bot-Struktur und Informationen zu *Dialogen*, sie ist jedoch nicht maßgeblich für das Verständnis Ihres Bots.

---


## <a name="helper-items"></a>Hilfsprogrammelemente

Wie bei jedem Programm kann die Verwendung von Hilfsmethoden und anderen Definitionen den Code vereinfachen. Unser Bot enthält einige Teile, die in diese Kategorie fallen, beispielsweise die `.bot`-Datei. Diese Elemente sind wichtig für den Bot, aber nicht notwendigerweise Teil der Kernlogik des Bots.

### <a name="bot-file"></a>BOT-Datei

Die `.bot`-Datei enthält Informationen, die [Kanälen](bot-concepts.md) das Herstellen einer Verbindung mit Ihrem Bot ermöglichen. Dazu zählen der Endpunkt, die App-ID und das Kennwort. Diese Datei wird für Sie erstellt, wenn Sie mit dem Erstellen eines Bots anhand einer Vorlage beginnen, Sie können jedoch mithilfe des Emulators oder mit anderen Tools eine eigene BOT-Datei erstellen.

Der Inhalt Ihrer Datei sollte wie hier dargestellt aussehen, es sei denn, Sie verwenden einen anderen Namen als *BasicEcho* für Ihren Bot. Je nachdem, wie Ihr Bot verwendet wird, müssen Sie diese Informationen möglicherweise ändern und aktualisieren. Zur Ausführung dieses Echobots sind hier aber keine Änderungen erforderlich.

```json
{
  "name": "BasicEcho",
  "secretKey": "",
  "services": [
    {
      "appId": "",
      "id": "http://localhost:3978/api/messages",
      "type": "endpoint",
      "appPassword": "",
      "endpoint": "http://localhost:3978/api/messages",
      "name": "BasicEcho"
    }
  ]
}
```

# <a name="ctabcs"></a>[C#](#tab/cs)

### <a name="echostatecs"></a>EchoState.cs

**EchoState.cs** enthält eine einfache Klasse, die unser Bot zum Verwalten des aktuellen Zustands verwendet. Sie enthält nur eine `int`-Variable, die wir zum Erhöhen des Turn-Zählers verwenden. Wir werden uns im nächsten Abschnitt ausführlicher mit dem Zustand befassen. Vorerst müssen Sie jedoch nur wissen, dass `EchoState` die Klasse ist, die die Anzahl von Turns enthält.

```cs
namespace BasicEcho
{
    /// <summary>
    /// Class for storing conversation state.
    /// </summary>
    public class EchoState
    {
        public int TurnCount { get; set; } = 0;
    }
}
```

### <a name="defaulthtm"></a>default.htm

**default.htm** ist die beim Ausführen des Bots angezeigte Webseite und in `html` geschrieben. Sie zeigt hilfreiche Informationen zur Verbindungsherstellung und Interaktion mit Ihrem Bot an, der Inhalt der Seite hat jedoch keinen Einfluss auf das Verhalten Ihres Bots. Der Code wird angezeigt, wenn Sie den Bot ausführen.

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)

### <a name="required-libraries"></a>Erforderliche Bibliotheken

Am Anfang der Datei `app.js` finden Sie eine Reihe von erforderlichen Modulen oder Bibliotheken. Über diese Module erhalten Sie Zugriff auf eine Gruppe von Funktionen, die Sie Ihrer Anwendung ggf. hinzufügen können. 

```javascript
const { BotFrameworkAdapter, MemoryStorage, ConversationState } = require('botbuilder');
const restify = require('restify');
```

---

## <a name="core-of-the-bot"></a>Kern des Bots

Kommen wir nun zu dem Teil, der Sie wirklich interessiert. Der Kern des Bots definiert, wie der Bot mit dem Benutzer interagiert. Er enthält die Middleware und die Bot-Logik.

# <a name="ctabcs"></a>[C#](#tab/cs)

### <a name="middleware"></a>Middleware

Die Datei **Startup.cs** enthält eine Methode mit dem Namen `ConfigureServices`. Diese Methode richtet den Anmeldeinformationsanbieter ein und fügt Middleware hinzu.

Die hier hinzugefügte [Middleware](bot-builder-concept-middleware.md) übernimmt zwei Aufgaben. Die erste Middleware dient zur Behandlung von Ausnahmen, damit Ihr Bot ordnungsgemäß auf Fehler reagieren kann. Sie wird inline als Lambda-Ausdruck definiert und gibt die Ausnahme einfach an das Terminal aus, um den Benutzer zu informieren, dass ein Fehler aufgetreten ist.

Die zweite Middleware verwaltet den Zustand mit der direkt davor definierten `MemoryStorage`-Klasse. Dieser Zustand ist als `ConversationState` definiert, was einfach bedeutet, dass der Zustand der Konversation beibehalten wird. Sie verwendet die `EchoState`-Klasse, die wir mit dem Turn-Zähler definiert haben, um die für Sie relevanten Informationen zu speichern.

``` cs
// This method gets called by the runtime. Use this method to add services to the container.
public void ConfigureServices(IServiceCollection services)
{
    services.AddBot<EchoBot>(options =>
    {
        options.CredentialProvider = new ConfigurationCredentialProvider(Configuration);

        // The CatchExceptionMiddleware provides a top-level exception handler for your bot.
        // Any exceptions thrown by other Middleware, or by your OnTurn method, will be 
        // caught here. To facilitate debugging, the exception is sent out, via Trace, 
        // to the emulator. Trace activities are NOT displayed to users, so in addition
        // an "Ooops" message is sent.
        options.Middleware.Add(new CatchExceptionMiddleware<Exception>(async (context, exception) =>
        {
            await context.TraceActivity("EchoBot Exception", exception);
            await context.SendActivity("Sorry, it looks like something went wrong!");
        }));

        // The Memory Storage used here is for local bot debugging only. When the bot
        // is restarted, anything stored in memory will be gone. 
        IStorage dataStore = new MemoryStorage();

        // The File data store, shown here, is suitable for bots that run on 
        // a single machine and need durable state across application restarts.

        // IStorage dataStore = new FileStorage(System.IO.Path.GetTempPath());

        // For production bots use the Azure Table Store, Azure Blob, or 
        // Azure CosmosDB storage provides, as seen below. To include any of 
        // the Azure based storage providers, add the Microsoft.Bot.Builder.Azure 
        // Nuget package to your solution. That package is found at:
        //      https://www.nuget.org/packages/Microsoft.Bot.Builder.Azure/

        // IStorage dataStore = new Microsoft.Bot.Builder.Azure.AzureTableStorage("AzureTablesConnectionString", "TableName");
        // IStorage dataStore = new Microsoft.Bot.Builder.Azure.AzureBlobStorage("AzureBlobConnectionString", "containerName");

        options.Middleware.Add(new ConversationState<EchoState>(dataStore));
    });
}
```

### <a name="bot-logic"></a>Bot-Logik

Die Hauptlogik des Bots wird im `OnTurn`-Handler des Bots definiert. Für `OnTurn` wird eine [Kontextvariable](./bot-builder-concept-activity-processing.md#turn-context) als Argument bereitgestellt, die Informationen zur eingehenden [Aktivität](bot-builder-concept-activity-processing.md), Konversation usw. enthält.

Es gibt [verschiedene Typen](../bot-service-activities-entities.md#activity-types) von eingehenden Aktivitäten. Daher überprüfen wir zunächst, ob Ihr Bot eine Nachricht empfangen hat. Wenn es sich um eine Nachricht handelt, erstellen wir eine Zustandsvariable, die die aktuellen Informationen der Konversation Ihres Bots enthält. Anschließend erhöhen wir die `int`-Variable, die die Anzahl von Turns verfolgt, bevor die Anzahl zusammen mit der gesendeten Nachricht an den Benutzer zurückgegeben wird.

```cs
using System.Threading.Tasks;
using Microsoft.Bot;
using Microsoft.Bot.Builder;
using Microsoft.Bot.Builder.Core.Extensions;
using Microsoft.Bot.Schema;

namespace BasicEcho
{
    public class EchoBot : IBot
    {
        /// <summary>
        /// Every Conversation turn for our EchoBot will call this method. In here
        /// the bot checks the Activty type to verify it's a message, bumps the 
        /// turn conversation 'Turn' count, and then echoes the users typing
        /// back to them. 
        /// </summary>
        /// <param name="context">Turn scoped context containing all the data needed
        /// for processing this conversation turn. </param>        
        public async Task OnTurn(ITurnContext context)
        {
            // This bot is only handling Messages
            if (context.Activity.Type == ActivityTypes.Message)
            {
                // Get the conversation state from the turn context
                var state = context.GetConversationState<EchoState>();

                // Bump the turn count. 
                state.TurnCount++;

                // Echo back to the user whatever they typed.
                await context.SendActivity($"Turn {state.TurnCount}: You sent '{context.Activity.Text}'");
            }
        }
    }    
}
```

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)

### <a name="middleware"></a>Middleware

In der Datei **app.js** verwenden wir in diesem Bot [Middleware](bot-builder-concept-middleware.md), um den Zustand mit der `MemoryStorage`-Klasse zu verwalten, die am Anfang von `botbuilder` als eines der erforderlichen Module definiert wurde. Dieser Zustand ist als `ConversationState` definiert, was einfach bedeutet, dass der Zustand der Konversation beibehalten wird. `ConversationState` speichert die für Sie relevanten Informationen im Arbeitsspeicher. In diesem Fall handelt es sich dabei um einen einfachen Turn-Zähler.

```javascript
// Add conversation state middleware
const conversationState = new ConversationState(new MemoryStorage());
adapter.use(conversationState);
```

### <a name="bot-logic"></a>Bot-Logik

Der dritte Parameter in *processActivity* ist ein Funktionshandler, der aufgerufen wird, um die Bot-Logik auszuführen, nachdem die empfangene [Aktivität](bot-builder-concept-activity-processing.md) vorab vom Adapter verarbeitet und über eine beliebige Middleware weitergeleitet wurde. Die [Kontextvariable](./bot-builder-concept-activity-processing.md#turn-context), die als Argument an den Funktionshandler übergeben wird, kann verwendet werden, um Informationen zur eingehenden Aktivität, zum Absender und Empfänger, zum Kanal, zur Konversation usw. bereitzustellen.

Zunächst überprüfen wir, ob der Bot eine Nachricht empfangen hat. Wenn der Bot keine Nachricht empfangen hat, geben wir den empfangenen Aktivitätstyp zurück. Als Nächstes erstellen wir eine Zustandsvariable, die die Informationen der Konversation Ihres Bots enthält. Anschließend legen wir die count-Variable, sofern sie nicht definiert ist (dies ist beim Starten des Bots der Fall), auf 0 fest oder erhöhen sie bei jeder neuen Nachricht. Zum Schluss geben wir die Anzahl zusammen mit der gesendeten Nachricht an den Benutzer zurück.

```javascript
// Listen for incoming requests 
server.post('/api/messages', (req, res) => {
    // Route received request to adapter for processing
    adapter.processActivity(req, res, (context) => {
        // This bot is only handling Messages
        if (context.activity.type === 'message') {

            // Get the conversation state
            const state = conversationState.get(context);

            // If state.count is undefined set it to 0, otherwise increment it by 1
            const count = state.count === undefined ? state.count = 0 : ++state.count;

            // Echo back to the user whatever they typed.
            return context.sendActivity(`${count}: You said "${context.activity.text}"`);
        } else {
            // Echo back the type of activity the bot detected if not of type message
            return context.sendActivity(`[${context.activity.type} event detected]`);
        }
    });
});
```

---
