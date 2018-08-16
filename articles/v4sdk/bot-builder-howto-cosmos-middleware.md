---
title: Erstellen von Middleware, die Cosmos DB verwendet | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie benutzerdefinierte Middleware zum Protokollieren in Cosmos DB erstellen.
keywords: Middleware, Cosmos DB, Datenbank, Azure, onTurn
author: ivorb
ms.author: v-ivorb
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 03/21/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 29edff2db0e513a37b8ca8ac0f97e0647282567d
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39302173"
---
# <a name="create-middleware-that-logs-in-cosmos-db"></a>Erstellen von Middleware, die in Cosmos DB protokolliert

Mit dem SDK wird zwar nützliche Middleware bereitgestellt, aber in manchen Situationen müssen Sie Ihre eigene Middleware implementieren, um das gewünschte Ziel zu erreichen.

In diesem Beispiel erstellen wir eine Middleware-Ebene, die eine Verbindung mit Cosmos DB herstellt, um alle empfangenen Nachrichten und die Antworten, die wir zurückgesendet haben, zu protokollieren. Der hier gezeigte Code steht als vollständiger Quellcode mit unseren [Beispielen](../dotnet/bot-builder-dotnet-samples.md) zur Verfügung.

## <a name="set-up"></a>Einrichtung

Wir müssen ein paar Dinge einrichten, bevor wir den Code besprechen.

### <a name="create-your-database"></a>Erstellen der Datenbank

Zunächst benötigen wir einen Speicherort für unsere Datenbank.  Hierzu kann ein Azure-Konto verwendet werden, aber für Testzwecke können Sie auch den [Cosmos DB-Emulator](https://docs.microsoft.com/en-us/azure/cosmos-db/local-emulator) verwenden. Unabhängig von der Methode, die Sie auswählen, benötigen wir einen Endpunkt-URI und einen Schlüssel für die Datenbank.

Im Beispiel und dem hier vorgestellten Code wird der Emulator verwendet. Endpunkt und Schlüssel sind die für das Cosmos DB-Testkonto bereitgestellten – häufig verwendete, mit der Dokumentation des Emulators bereitgestellte Werte, die nicht für die Produktion verwendet werden können.

Wenn Sie eine echte Azure Cosmos DB-Instanz verwenden möchten, führen Sie Schritt 1 des Themas [Azure Cosmos DB: SQL-API – Tutorial zu den ersten Schritten](https://docs.microsoft.com/en-us/azure/cosmos-db/sql-api-get-started) aus. Sobald das geschehen ist, sind sowohl Ihr Endpunkt-URI als auch Ihr Schlüssel in Ihren Datenbankeinstellungen auf der Registerkarte **Schlüssel** verfügbar. Diese Werte sind im Folgenden für unsere Konfigurationsdatei erforderlich.

### <a name="build-a-basic-bot"></a>Erstellen eines einfachen Bots

Im weiteren Verlauf dieses Themas wird das Erstellen eines einfachen Bots wie dem in der Übersicht erstellten „HelloBot“ behandelt. Sie können den [Bot Framework-Emulator](https://github.com/Microsoft/BotFramework-Emulator) verwenden, um eine Verbindung mit Ihrem Bot herzustellen, mit ihm eine Unterhaltung zu führen und ihn zu testen.

Zusätzlich zu den Verweisen, die für Standardbots erforderlich sind, die das Bot Framework verwenden, müssen Sie Verweise auf die folgenden NuGet-Pakete hinzufügen:

* Microsoft.Azure.Documentdb.Core
* System.Configuration.ConfigurationManager

Diese Bibliotheken werden für die Kommunikation mit der Datenbank verwendet, bzw. ermöglichen die Verwendung einer Konfigurationsdatei.

### <a name="add-configuration-file"></a>Konfigurationsdatei hinzufügen

Da die Verwendung einer Konfigurationsdatei sich aus mehreren Gründen empfiehlt, werden wir hier eine verwenden. Unsere Konfigurationsdaten sind kurz und einfach, Sie können jedoch weitere Konfigurationseinstellungen hinzufügen, wenn Ihr Bot komplexer wird.

# <a name="ctabcs"></a>[C#](#tab/cs)
1. Fügen Sie dem Projekt eine Datei mit dem Namen `app.config` hinzu.
2. Speichern Sie darin die folgenden Informationen. Endpunkt und Schlüssel sind für den lokalen Emulator definiert, können aber für Ihre Cosmos DB-Instanz aktualisiert werden.
    ```
    <?xml version="1.0" encoding="utf-8"?>
    <configuration>
        <appSettings>
            <add key="EmulatorDbUrl" value="https://localhost:8081"/>
            <add key="EmulatorDbKey" value="C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw=="/>
        </appSettings>
    </configuration>
    ```
# <a name="javascripttabjs"></a>[JavaScript](#tab/js)
1. Fügen Sie dem Projekt eine Datei mit dem Namen `config.js` hinzu.
2. Speichern Sie darin die folgenden Informationen. Endpunkt und Schlüssel sind für den lokalen Emulator definiert, können aber für Ihre Cosmos DB-Instanz aktualisiert werden.
    ```javascript
        var config = {}

        config.EmulatorServiceEndpoint = "https://localhost:8081";
        config.EmulatorAuthKey = "C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWE+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==";
        config.database = {
            "id": "Tasks"
        };
        config.collection = {
            "id": "Items"
        };

        module.exports = config;
    ```

---

Wenn Sie eine eigentliche Cosmos DB-Instanz verwenden, können Sie die Werte der beiden obigen Schlüssel mit den Werten in Ihren Cosmos DB-Einstellungen ersetzen. Alternativ können Sie Ihrer Konfiguration zwei weitere Schlüssel hinzufügen, die Ihnen ermöglichen, zwischen dem Emulator zum Testen und Ihrer eigentlichen Cosmos DB-Instanz wechseln, wie z.B.:

# <a name="ctabcs"></a>[C#](#tab/cs)
```
    <add key="ActualDbUrl" value="<your database URI>"/>
    <add key="ActualDbKey" value="<your database key>"/>
```
 Wenn Sie zwei zusätzliche Schlüssel hinzufügen und die eigentliche Datenbank verwenden möchten, achten Sie unbedingt darauf, statt `EmulatorDbUrl` und `EmulatorDbKey` den richtigen Schlüssel im Konstruktor unten anzugeben.

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)
```
    config.ActualServiceEndpoint = "your database URI;
    config.ActualAuthKey = "your database key";
```

Wenn Sie zwei zusätzliche Schlüssel hinzufügen und die eigentliche Datenbank verwenden möchten, achten Sie unbedingt darauf, statt `EmulatorServiceEndpoint` und `EmulatorAuthKey` den richtigen Schlüssel im Konstruktor unten anzugeben.

---



## <a name="creating-your-middleware"></a>Erstellen Ihrer Middleware

# <a name="ctabcs"></a>[C#](#tab/cs)
Bevor wir beginnen, die eigentliche Middlewarelogik zu schreiben, erstellen Sie in Ihrem Botprojekt eine neue Klasse für die Middleware. Sobald Sie diese Klasse besitzen, ändern Sie den Namespace in das, was wir für den Rest des Bots verwenden, `Microsoft.Bot.Samples`. Hier sehen Sie, dass wir Verweise auf einige neue Bibliotheken hinzugefügt haben:

* `Newtonsoft.Json;`
* `Microsoft.Azure.Documents;`
* `Microsoft.Azure.Documents.Client;`
* `Microsoft.Azure.Documents.Linq;`

Die verschiedenen `Microsoft.Azure.Documents.*` sind für die verschiedenen Teile der Kommunikation mit unserer Datenbank bestimmt, und `Newtonsoft.Json` unterstützt die Analyse des JSON-Codes für den Zugriff auf unsere Datenbank.

Abschließend wird `IMiddleware` in jede Middlewarekomponente implementiert, sodass wir die entsprechenden Handler definieren müssen, damit die Middleware ordnungsgemäß funktioniert.

```cs
using System;
using System.Collections.Generic;
using System.Threading.Tasks;
using System.Configuration;
using Newtonsoft.Json;
using Microsoft.Bot.Builder;
using Microsoft.Bot.Schema;
using Microsoft.Azure.Documents;
using Microsoft.Azure.Documents.Client;
using Microsoft.Azure.Documents.Linq;

namespace Microsoft.Bot.Samples
{
    public class CosmosMiddleware : IMiddleware
    {
        ...
```

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)
Bevor wir beginnen, die eigentliche Middlewarelogik zu schreiben, müssen wir unsere benutzerdefinierte Middleware erstellen, die mit `onTurn()` beginnt. 

```javascript
adapter.use({onTurn: async (context, next) =>{
    // Middleware logic here...
}})
    
```

---

#### <a name="defining-local-variables"></a>Definieren lokaler Variablen

# <a name="ctabcs"></a>[C#](#tab/cs)
Als Nächstes benötigen wir lokale Variablen für die Bearbeitung unserer Datenbank und eine Klasse zum Speichern der Informationen, die wir protokollieren möchten. Diese Informationsklasse, die wir mit `Log` bezeichnen, definiert, wie die mit den einzelnen Membern verknüpften JSON-Eigenschaften benannt werden. Wir kommen später darauf zurück.

```cs
    private string Endpoint;
    private string Key;
    private static readonly string Database = "BotData";
    private static readonly string Collection = "BotCollection";
    public DocumentClient docClient;

    public class Log
    {
        [JsonProperty("Time")]
        public string Time;

        [JsonProperty("MessageReceived")]
        public string Message;

        [JsonProperty("ReplySent")]
        public string Reply;
    }
```

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)
Als Nächstes benötigen wir eine lokale Variable zum Speichern der Informationen, die wir protokollieren möchten. In der Middleware müssen wir auf conversationState mit Cosmos DB als Speicheranbieter zugreifen. Die Variable `info` wird das Objekt des Status sein, aus dem wir lesen und in das wir schreiben. 

```javascript
// Add conversation state middleware
const conversationState = new ConversationState(storage);
adapter.use(conversationState);

adapter.use({onTurn: async (context, next) =>{

    const info = conversationState.get(context);
    // More middleware logic 
}})
```

--- 


#### <a name="initialize-database-connection"></a>Initialisieren der Datenbankverbindung

# <a name="ctabcs"></a>[C#](#tab/cs)
Unser Konstruktor dieser Middleware zieht die erforderlichen Informationen aus unserer oben definierten Konfigurationsdatei und erstellt den `DocumentClient`, der uns ermöglicht, aus der Datenbank zu lesen und in sie zu schreiben. Wir stellen auch sicher, dass Datenbank und Sammlung eingerichtet werden.

Da die richtige Verwendung eines neuen `DocumentClient` die Entsorgung des alten voraussetzt, erledigt dies unser Destruktor.

Die Erstellung unserer Datenbank hängt von dem Versuch ab, zuerst unsere Datenbank und dann die Sammlung darin gründlich zu lesen. Wenn wir auf eine `NotFound`-Ausnahme treffen, fangen wir sie ab und erstellen die Datenbank oder die Sammlung, statt sie auf dem Stapel abzulegen. In den meisten Fällen werden diese bereits erstellt sein, sodass wir uns für dieses Beispiel vor der ersten Ausführung nicht um die Erstellung dieser Datenbank kümmern müssen. Im Beispielcode sind Datenbank und Sammlung der Einfachheit halber in zwei Funktionen aufgeteilt, aber sie wurden im folgenden Codeausschnitt kombiniert.

Weitere Informationen zu Cosmos DB finden Sie auf der zugehörigen [Website](https://docs.microsoft.com/en-us/azure/cosmos-db/). Im weiteren Verlauf dieses Themas überfliegen wir Details von Cosmos DB selbst und konzentrieren uns auf das, was Ihr Bot verwenden muss.

Die Definitionen von `Key`, `Endpoint` und `docClient` sind in einem obigen Codeausschnitt enthalten und wurden hier nur aus Gründen der Übersichtlichkeit kopiert.

```cs
    private string Endpoint;
    private string Key;
    // ...
    public DocumentClient docClient;
    // ...

    public CosmosMiddleware()
    {
        Endpoint = ConfigurationManager.AppSettings["EmulatorDbUrl"];
        Key = ConfigurationManager.AppSettings["EmulatorDbKey"];
        docClient = new DocumentClient(new Uri(Endpoint), Key);
        CreateDatabaseAndCollection().ConfigureAwait(false);
    }

    ~CosmosMiddleware()
    {
        docClient.Dispose();
    }

    private async Task CreateDatabaseAndCollection()
    {
        try
        {
            await docClient.ReadDatabaseAsync(UriFactory.CreateDatabaseUri(Database));
        }
        catch (DocumentClientException e)
        {
            if (e.StatusCode == System.Net.HttpStatusCode.NotFound)
            {
                await docClient.CreateDatabaseAsync(new Database { Id = Database });
            }
            else
            {
                throw;
            }
        }

        try
        {
            await docClient.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri
                (Database, Collection));
        }
        catch (DocumentClientException e)
        {
            if (e.StatusCode == System.Net.HttpStatusCode.NotFound)
            {
                await docClient.CreateDocumentCollectionAsync(
                    UriFactory.CreateDatabaseUri(Database),
                    new DocumentCollection { Id = Collection });
            }
            else
            {
                throw;
            }
        }
    }
```

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)
Die Erstellung unserer Datenbank hängt von dem Versuch ab, zuerst unsere Datenbank und dann die Sammlung darin gründlich zu lesen. Wenn wir auf `undefined` stoßen, fangen wir dies ab und erstellen ein Objekt namens `log`, für das ein leeres Array festgelegt wird. In den meisten Fällen wird `log` bereits erstellt sein, sodass wir uns für dieses Beispiel vor der ersten Ausführung nicht um die Erstellung dieser Datenbank kümmern müssen. Im Beispielcode überprüfen wir, ob `info.log` undefiniert ist. Wenn dies der Fall ist, legen Sie dafür ein leeres Array fest.

```javascript
adapter.use({onTurn: async (context, next) =>{

    const info = conversationState.get(context);

    if(info.log == undefined){
        info.log = [];
    }

    // More bot logic below
}})
```
---

#### <a name="read-from-database-logic"></a>Lesen von Datenbanklogik

Die letzte Hilfsfunktion liest aus der Datenbank und gibt die zuletzt angegebene Anzahl von Datensätzen zurück. Beachten Sie, dass es bessere Vorgehensweisen zum Abrufen von Daten aus einer Datenbank als die hier verwendete gibt, besonders wenn Ihr Datenspeicher erheblich größer ist.

# <a name="ctabcs"></a>[C#](#tab/cs)
```cs
    public async Task<string> ReadFromDatabase(int numberOfRecords)
    {
        var documents = docClient.CreateDocumentQuery<Log>(
                UriFactory.CreateDocumentCollectionUri(Database, Collection))
                .AsDocumentQuery();
        List<Log> messages = new List<Log>();
        while (documents.HasMoreResults)
        {
            messages.AddRange(await documents.ExecuteNextAsync<Log>());
        }

        // Create a sublist of messages containing the number of requested records.
        List<Log> messageSublist = messages.GetRange(messages.Count - numberOfRecords, numberOfRecords);

        string history = "";

        // Send the last 3 messages.
        foreach (Log logEntry in messageSublist)
        {
            history += ("Message was: " + logEntry.Message + " Reply was: " + logEntry.Reply + "  ");
        }

        return history;
    }
```

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)

Der folgende Code liegt noch innerhalb der `onTurn()`-Funktion und wird aufgerufen, wenn der Benutzer die Zeichenfolge „history“ eingibt.

```javascript
adapter.use({onTurn: async (context, next) =>{

    const utterance = (context.activity.text || '').trim().toLowerCase();
    const isMessage = context.activity.type === 'message';
    const info = conversationState.get(context);

    if(info.log == undefined){
        info.log = [];
    }

    if(isMessage && utterance == 'history'){
        // Loop through the info.log array
        var logHistory = "";
        for(var i = 0; i < info.log.length; i++){
            logHistory += info.log[i] + " ";
        }
        await context.sendActivity(logHistory);
        // Short circuit the middleware
        return
    }
    //...
}})

```

---

#### <a name="define-onturn"></a>Definieren von OnTurn()

Die standardmäßige Middlewaremethode `OnTurn()` behandelt dann die restlichen Aufgaben. Wir möchten nur protokollieren, wenn die aktuelle Aktivität eine Nachricht ist, was wir vor und nach dem Aufruf von `next()` überprüfen.

Zunächst überprüfen wir, ob dies unsere Sonderfallnachricht ist, wo der Benutzer nach dem aktuellen Verlauf fragt. Wenn ja, rufen wir das Lesen unserer Datenbank ab, um die neuesten Datensätze zu erhalten, und senden diese an die Unterhaltung. Da dies die aktuelle Aktivität vollständig behandelt, umgehen wir die Pipeline, anstatt die Ausführung fortzusetzen.

Um die Antwort zu erhalten, die der Bot sendet, erstellen wir jedes Mal dann einen Handler, wenn wir eine Meldung empfangen, diese Antworten zu erfassen, was aus dem Lambdaausdruck hervorgeht, den wir an `OnSendActivity()` übergeben. Er erstellt eine Zeichenfolge, um alle über `SendActivity()` für dieses Kontextobjekt gesendeten Nachrichten zu sammeln.

Sobald die Ausführung die Pipeline aus `next()` zurückgibt, stellen wir unsere Protokolldaten zusammen und schreiben sie in die Datenbank. 

Wenn Sie in den Cosmos DB-Daten-Explorer schauen, nachdem einige Nachrichten an diesen Bot gesendet wurden, sollten Ihre Daten in einzelnen Datensätzen angezeigt werden. Wenn Sie einen dieser Datensätze untersuchen, sollten Sie feststellen, dass die ersten drei Elemente die drei Werte unserer Protokolldaten sind, mit dem Namen der Zeichenfolge, die wir für ihre jeweilige `JsonProperty` angegeben haben.

# <a name="ctabcs"></a>[C#](#tab/cs)
```cs
    public async Task OnTurn
        (ITurnContext context, MiddlewareSet.NextDelegate next)
    {
        string botReply = "";

        if (context.Activity.Type == ActivityTypes.Message)
        {
            if (context.Activity.Text == "history")
            {
                // Read last 3 responses from the database, and short circuit future execution.
                await context.SendActivity(await ReadFromDatabase(3));
                return;
            }

            // Create a send activity handler to grab all response activities 
            // from the activity list.
            context.OnSendActivity(async (activityContext, activityList, activityNext) =>
            {
                foreach (Activity activity in activityList)
                {
                    botReply += (activity.Text + " ");
                }
                await activityNext();
            });
        }

        // Pass execution on to the next layer in the pipeline.
        await next();

        // Save logs for each conversational exchange only.
        if (context.Activity.Type == ActivityTypes.Message)
        {
            // Build a log object to write to the database.
            var logData = new Log
            {
                Time = DateTime.Now.ToString(),
                Message = context.Activity.Text,
                Reply = botReply
            };

            // Write our log to the database.
            try
            {
                var document = await docClient.CreateDocumentAsync(UriFactory.
                    CreateDocumentCollectionUri(Database, Collection), logData);
            }
            catch (Exception ex)
            {
                // More logic for what to do on a failed write can be added here
                throw ex;
            }
        }
    }
```

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)

Aufbauend auf unserer oben beschriebenen `onTurn`-Funktion.

```javascript
adapter.use({onTurn: async (context, next) =>{

    const utterance = (context.activity.text || '').trim().toLowerCase();
    const isMessage = context.activity.type === 'message';
    const info = conversationState.get(context);

    if(info.log == undefined){
        info.log = [];
    }

    if(isMessage && utterance == 'history'){
        // Loop through info.log 
        var logHistory = "";
        for(var i = 0; i < info.log.length; i++){
            logHistory += info.log[i] + " ";
        }
        await context.sendActivity(logHistory);
        // Short circuit the middleware
        return
    } else if(isMessage){
        // Store the users response
        info.log.push(`Message was: ${context.activity.text}`); 

        await context.onSendActivities(async (handlerContext, activities, handlerNext) => 
        {
            // Store the bot's reply
            info.log.push(`Reply was: ${activities[0].text}`);
            
            await handlerNext(); 
        });
        await next();
    }
}})

```

---

## <a name="sample-output"></a>Beispielausgabe

Im vollständigen Beispiel verwendet der Bot die Bibliothek der Eingabeaufforderungen, um den Benutzer nach Namen und Lieblingszahl zu fragen. Details zur Bibliothek der Eingabeaufforderungen finden Sie im Thema [Auffordern von Benutzern zur Eingabe](~/v4sdk/bot-builder-prompts.md).

Hier ist eine Beispielunterhaltung mit unserem Beispielbot, die den oben beschriebenen Code ausführt.

![Cosmos-Middleware-Beispielunterhaltung](./media/cosmos-middleware-conversation.png)

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Cosmos DB](https://docs.microsoft.com/en-us/azure/cosmos-db/)
* [Cosmos DB-Emulator](https://docs.microsoft.com/en-us/azure/cosmos-db/local-emulator)

