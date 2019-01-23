---
title: Direktes Schreiben in den Speicher | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie mit dem Bot Framework SDK für .NET direkt in den Speicher schreiben.
keywords: Speicher, lesen und schreiben, Arbeitsspeicher, eTag
author: DeniseMak
ms.author: v-demak
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 11/13/18
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: cd1f8270acf426c84d64efef796b7a007c49c2c1
ms.sourcegitcommit: bdb981c0b11ee99d128e30ae0462705b2dae8572
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/17/2019
ms.locfileid: "54360790"
---
# <a name="write-directly-to-storage"></a>Direktes Schreiben in den Speicher

[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

Sie können ohne Verwendung eines Middleware- oder Kontextobjekts direkt aus Ihrem Speicherobjekt lesen und in das Speicherobjekt schreiben. Dies ist ggf. für Daten geeignet, die Ihr Bot verwendet, die aus einer Quelle außerhalb des Unterhaltungsflusses Ihres Bots stammen. Angenommen, Ihr Bot erlaubt dem Benutzer, nach dem Wetterbericht zu fragen, und Ihr Bot ruft den Wetterbericht für ein bestimmtes Datum ab, indem er ihn aus einer externen Datenbank liest. Der Inhalt der Wetterdatenbank ist nicht von Benutzerinformationen oder dem Unterhaltungskontext abhängig, sodass Sie ihn direkt aus dem Speicher lesen können, anstatt den Status-Manager zu verwenden. Die Codebeispiele in diesem Artikel zeigen, wie Sie mithilfe von **Arbeitsspeicher**, **Cosmos DB**, **Blob Storage** und **Azure-Blob-Transkriptspeicher** Daten aus dem Speicher lesen und in den Speicher schreiben. 

## <a name="prerequisites"></a>Voraussetzungen
- Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/en-us/free/) erstellen, bevor Sie beginnen.
- Installieren Sie den Bot Framework-[Emulator](https://aka.ms/Emulator-wiki-getting-started).

## <a name="memory-storage"></a>Arbeitsspeicher

Zunächst erstellen Sie einen Bot, der Daten im Arbeitsspeicher liest und schreibt. Arbeitsspeicher wird nur für Testzwecke genutzt und ist nicht für die Verwendung in der Produktion vorgesehen. Denken Sie daran, den Speicher auf Cosmos DB oder Blob Storage festzulegen, bevor Sie den Bot veröffentlichen.

#### <a name="build-a-basic-bot"></a>Erstellen eines einfachen Bots

Die restlichen Abschnitte dieses Themas beruhen auf einem Echobot. Sie können einen Echobot in [C#](../dotnet/bot-builder-dotnet-sdk-quickstart.md) oder [JS](../javascript/bot-builder-javascript-quickstart.md) erstellen. Mithilfe des Bot Framework-Emulators können Sie eine Verbindung mit Ihrem Bot herstellen, mit ihm eine Konversation führen und ihn testen. Im folgenden Beispiel wird jede Nachricht des Benutzers einer Liste hinzugefügt. Die Datenstruktur, die die Liste enthält, wird in Ihrem Speicher gespeichert.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.Bot.Builder;
using Microsoft.Bot.Builder.TraceExtensions;
using Microsoft.Bot.Builder.Azure;
using Microsoft.Bot.Schema;
using System.Collections.Generic;
using System.Linq;
using System.Threading;

// Create local Memory Storage.
private static readonly MemoryStorage _myStorage = new MemoryStorage();

// Create cancellation token (used by Async Write operation).
public CancellationToken cancellationToken { get; private set; }

// Class for storing a log of utterances (text of messages) as a list.
public class UtteranceLog : IStoreItem
{
     // A list of things that users have said to the bot
     public List<string> UtteranceList { get; } = new List<string>();

     // The number of conversational turns that have occurred        
     public int TurnNumber { get; set; } = 0;

     // Create concurrency control where this is used.
     public string ETag { get; set; } = "*";
}

// Every Conversation turn for our Bot calls this method.
public async Task OnTurnAsync(ITurnContext context)
{
     
     var activityType = context.Activity.Type;
     // See if activity type for this turn is a message from the user.
     if (activityType == ActivityTypes.Message)
     {
         var utterance = context.Activity.Text;
         UtteranceLog logItems = null;
          
         // see if there are previous messages saved in sstorage.
         string[] utteranceList = { "UtteranceLog" };
         logItems = _myStorage.ReadAsync<UtteranceLog>(utteranceList).Result?.FirstOrDefault().Value;

         // If no stored messages were found, create and store a new entry.
         if (logItems is null)
         {
             logItems = new UtteranceLog();
         }
         
         // add new message to list of messages to display.
         logItems.UtteranceList.Add(utterance);
         // increment turn counter.
         logItems.TurnNumber++;
         
         // show user new list of saved messages.
         await context.SendActivityAsync($"The list is now: {string.Join(", ", logItems.UtteranceList)}");
         
         // Create Dictionary object to hold new list of messages.
         {
             changes.Add("UtteranceLog", logItems);
          };
          
          // Save new list to your Storage.
          await _myStorage.WriteAsync(changes,cancellationToken);
     }
     return;
}

```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
const { BotFrameworkAdapter, ConversationState, BotStateSet, MemoryStorage } = require('botbuilder');
const restify = require('restify');

// Create server.
let server = restify.createServer();
server.listen(process.env.port || process.env.PORT || 3978, function () {
    console.log(`${server.name} listening to ${server.url}`);
});

// Create adapter.
const adapter = new BotFrameworkAdapter({
    appId: process.env.MICROSOFT_APP_ID,
    appPassword: process.env.MICROSOFT_APP_PASSWORD
});

// Add memory storage.
var storage = new MemoryStorage();

const conversationState = new ConversationState(storage);
adapter.use(conversationState);

// Listen for incoming activity .
server.post('/api/messages', (req, res) => {
    // Route received activity to adapter for processing.
    adapter.processActivity(req, res, async (context) => {
        if (context.activity.type === 'message') {
            const state = conversationState.get(context);
            const count = state.count === undefined ? state.count = 0 : ++state.count;

            await logMessageText(storage, context);

            await context.sendActivity(`${count}: You said "${context.activity.text}"`);
        } else {
            await context.sendActivity(`[${context.activity.type} event detected]`);
        }
    });
});

async function logMessageText(storage, context) {
    let utterance = context.activity.text;
    try {
        // Read from the storage.
        let storeItems = await storage.read(["UtteranceLog"])
        // Check the result.
        var utteranceLog = storeItems["UtteranceLog"];

        if (typeof (utteranceLog) != 'undefined') {
            // The log exists so we can write to it.
            storeItems["UtteranceLog"].UtteranceList.push(utterance);

            try {
                await storage.write(storeItems)
                context.sendActivity('Successful write to utterance log.');
            } catch (err) {
                context.sendActivity(`Write failed of UtteranceLog: ${err}`);
            }

         } else {
            context.sendActivity(`need to create new utterance log`);
            storeItems["UtteranceLog"] = { UtteranceList: [`${utterance}`], "eTag": "*" }

            try {
                await storage.write(storeItems)
                context.sendActivity('Successful write to log.');
            } catch (err) {
                context.sendActivity(`Write failed: ${err}`);
            }
        }
    } catch (err) {
        context.sendActivity(`Read rejected. ${err}`);
    };
}
```

---

### <a name="start-your-bot"></a>Starten Ihres Bots
Führen Sie Ihren Bot lokal aus.

### <a name="start-the-emulator-and-connect-your-bot"></a>Starten des Emulators und Herstellen einer Verbindung mit Ihrem Bot
Starten Sie im nächste Schritt den Emulator, und stellen Sie dann im Emulator eine Verbindung mit Ihrem Bot her:

1. Klicken Sie im Emulator auf der Registerkarte „Willkommen“ auf den Link **Bot öffnen**. 
2. Wählen Sie in dem Verzeichnis, in dem Sie das Projekt erstellt haben, die BOT-Datei aus.

### <a name="interact-with-your-bot"></a>Interagieren mit Ihrem Bot
Senden Sie eine Nachricht an den Bot. Der Bot listet daraufhin die von ihm empfangenen Nachrichten auf.
![Ausgeführter Emulator](../media/emulator-v4/emulator-running.png)

 
## <a name="using-cosmos-db"></a>Verwenden von Cosmos DB
Nachdem Sie den Arbeitsspeicher verwendet haben, aktualisieren Sie nun den Code, um Azure Cosmos DB zu verwenden. Cosmos DB ist eine global verteilte Datenbank von Microsoft mit mehreren Modellen. Azure Cosmos DB ermöglicht es Ihnen, Durchsatz und Speicher elastisch und unabhängig voneinander über eine beliebige Anzahl von geografischen Azure-Regionen hinweg zu skalieren. Azure Cosmos DB bietet Ihnen mit umfassenden Vereinbarungen zum Servicelevel (Service Level Agreements, SLAs) Durchsatz-, Wartezeit-, Verfügbarkeits- und Konsistenzgarantien. 

### <a name="set-up"></a>Einrichtung
Um Cosmos DB in Ihrem Bot verwenden zu können, müssen Sie einige Einrichtungsschritte durchführen, bevor wir uns mit dem Code befassen.

#### <a name="create-your-database-account"></a>Erstellen Ihres Datenbankkontos
1. Melden Sie sich in einem neuen Browserfenster beim [Azure-Portal](http://portal.azure.com) an.
2. Klicken Sie auf **Ressource erstellen > Datenbanken > Azure Cosmos DB**.
3. Geben Sie auf der Seite **Neues Konto** im Feld **ID** einen eindeutigen Namen an. Wählen Sie für **API** die Option **SQL** aus, und geben Sie Informationen für **Abonnement**, **Standort** und **Ressourcengruppe** an.
4. Klicken Sie dann auf **Erstellen**.

Die Kontoerstellung dauert einige Minuten. Warten Sie, bis im Portal die Seite „Herzlichen Glückwunsch! Ihr Azure Cosmos DB-Konto wurde erstellt“ angezeigt wird.

##### <a name="add-a-collection"></a>Hinzufügen einer Sammlung
1. Klicken Sie auf **Einstellungen > Neue Sammlung**. Der Bereich **Sammlung hinzufügen** wird ganz rechts angezeigt. Möglicherweise müssen Sie nach rechts scrollen, damit Sie ihn sehen. Fügen Sie aufgrund aktueller Updates an Cosmos DB unbedingt einen einzelnen Partitionsschlüssel hinzu: _/id_. Dieser Schlüssel verhindert partitionsübergreifende Abfragefehler.

![Azure Cosmos DB-Sammlung](./media/add_database_collection.png)

2. Ihre neue Datenbanksammlung „bot-cosmos-sql-db“ besitzt die Sammlungs-ID „bot-storage“. Diese Werte verwenden Sie im unten beschriebenen Codebeispiel.
 -
![Cosmos DB](./media/cosmos-db-sql-database.png)

3. Der Endpunkt-URI und der Schlüssel sind in Ihren Datenbankeinstellungen auf der Registerkarte **Schlüssel** verfügbar. Diese Werte sind erforderlich, um den Code an späterer Stelle dieses Artikels zu konfigurieren. 

![Cosmos DB-Schlüssel](./media/comos-db-keys.png)

#### <a name="add-configuration-information"></a>Hinzufügen der Konfigurationsinformationen
Unsere Konfigurationsdaten zum Hinzufügen von Cosmos DB-Speicher sind kurz und einfach. Sie können anhand der gleichen Methoden zusätzliche Konfigurationseinstellungen hinzufügen, wenn Ihr Bot komplexer wird. In diesem Beispiel werden die Cosmos DB-Datenbank- und -Sammlungsnamen aus dem obigen Beispiel verwendet.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
private const string CosmosServiceEndpoint = "<your-cosmos-db-URI>";
private const string CosmosDBKey = "<your-cosmos-db-account-key>";
private const string CosmosDBDatabaseName = "bot-cosmos-sql-db";
private const string CosmosDBCollectionName = "bot-storage";
```


# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Fügen Sie Ihrer `.env`-Datei die folgenden Informationen hinzu. 
 
```javascript
ACTUAL_SERVICE_ENDPOINT=<your database URI>
ACTUAL_AUTH_KEY=<your database key>
DATABASE=Tasks
COLLECTION=Items
```
---

#### <a name="installing-packages"></a>Installieren von Paketen
Stellen Sie sicher, dass Sie über die erforderlichen Pakete für Cosmos DB verfügen.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```powershell
Install-Package Microsoft.Bot.Builder.Azure
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Sie können in Ihrem Projekt Verweise auf „botbuilder-azure“ über npm hinzufügen: -->


```powershell
npm install --save botbuilder-azure 
```

Zur Verwendung der ENV-Konfigurationsdatei benötigt der Bot ein zusätzliches Paket. Rufen Sie zuerst das dotnet-Paket über npm ab:

```powershell
npm install --save dotenv
```

---

### <a name="implementation"></a>Implementierung 

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Der folgende Beispielcode wird mit dem gleichen Botcode ausgeführt wie das obige Beispiel für den [Arbeitsspeicher](#memory-storage).
Der unten stehende Codeausschnitt zeigt eine Implementierung von Cosmos DB-Speicher für _myStorage_, die den lokalen Arbeitsspeicher ersetzt. 

```csharp
using Microsoft.Bot.Builder.Azure;

// Create access to Cosmos DB storage.
// Replaces Memory Storage with reference to Cosmos DB.
private static readonly CosmosDbStorage _myStorage = new CosmosDbStorage(new CosmosDbStorageOptions
{
   AuthKey = CosmosDBKey,
   CollectionId = CosmosDBCollectionName,
   CosmosDBEndpoint = new Uri(CosmosServiceEndpoint),
   DatabaseId = CosmosDBDatabaseName,
});
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Abgesehen von einigen geringfügigen Änderungen ähnelt der folgende Beispielcode dem Code für [Arbeitsspeicher](#memory-storage).

Legen Sie `CosmosDbStorage` als erforderlich für „botbuilder-azure“ fest, und konfigurieren Sie „dotenv“ zum Lesen der `.env`-Datei.

**app.js**
```javascript
const { CosmosDbStorage } = require("botbuilder-azure");
require('dotenv').config()
```

Ersetzen Sie den Arbeitsspeicher (Memory Storage) durch einen Verweis auf Cosmos DB.

```javascript
//Add CosmosDB 
const storage = new CosmosDbStorage({
    serviceEndpoint: process.env.ACTUAL_SERVICE_ENDPOINT, 
    authKey: process.env.ACTUAL_AUTH_KEY, 
    databaseId: process.env.DATABASE,
    collectionId: process.env.COLLECTION
})

const conversationState = new ConversationState(storage);
adapter.use(conversationState);
```

---

## <a name="start-your-bot"></a>Starten Ihres Bots
Führen Sie Ihren Bot lokal aus.

## <a name="start-the-emulator-and-connect-your-bot"></a>Starten des Emulators und Herstellen einer Verbindung mit Ihrem Bot
Starten Sie im nächste Schritt den Emulator, und stellen Sie dann im Emulator eine Verbindung mit Ihrem Bot her:

1. Klicken Sie im Emulator auf der Registerkarte „Willkommen“ auf den Link **Bot öffnen**. 
2. Wählen Sie in dem Verzeichnis, in dem Sie das Projekt erstellt haben, die BOT-Datei aus.

## <a name="interact-with-your-bot"></a>Interagieren mit Ihrem Bot
Senden Sie eine Nachricht an den Bot. Der Bot listet daraufhin die von ihm empfangenen Nachrichten auf.
![Ausgeführter Emulator](../media/emulator-v4/emulator-running.png)


### <a name="view-your-data"></a>Prüfen der Daten
Nachdem Sie den Bot ausgeführt und Ihre Informationen gespeichert haben, können Sie sie im Azure-Portal auf der Registerkarte **Daten-Explorer** anzeigen. 

![Beispiel für die Anzeige im Daten-Explorer](./media/data_explorer.PNG)

### <a name="manage-concurrency-using-etags"></a>Verwalten von Parallelität mit eTags
In unserem Botcodebeispiel legen Sie die `eTag`-Eigenschaft jedes `IStoreItem` auf `*` fest. Der `eTag`-Member (Entitätstag) Ihres Speicherobjekts wird in Cosmos DB zum Verwalten der Parallelität verwendet. Das `eTag` teilt Ihrer Datenbank mit, wie vorzugehen ist, wenn eine andere Instanz des Bots das Objekt in demselben Speicher geändert hat, in den der Bot schreibt. 

<!-- define optimistic concurrency -->

#### <a name="last-write-wins---allow-overwrites"></a>Der letzte Schreibzugriff gewinnt: Überschreiben zulassen
Wenn der Eigenschaftenwert von `eTag` das Sternchen (`*`) ist, zeigt dies an, dass der letzte Schreibvorgang gewinnt. Wenn Sie einen neuen Datenspeicher erstellen, können Sie das `eTag` einer Eigenschaft auf `*` festlegen, um anzugeben, dass Sie die Daten, die Sie schreiben, noch nicht gespeichert haben, oder dass der letzte Schreibvorgang eine zuvor gespeicherte Eigenschaft überschreiben soll. Wenn die Parallelität für Ihren Bot kein Problem darstellt, erlaubt das Festlegen der `eTag`-Eigenschaft auf `*` für alle Daten, die Sie schreiben, das Überschreiben.

#### <a name="maintain-concurrency-and-prevent-overwrites"></a>Verwalten von Parallelität und Verhindern von Überschreibungen
Verwenden Sie beim Speichern Ihrer Daten in Cosmos DB einen anderen Wert als `*` für das `eTag`, wenn Sie den gleichzeitigen Zugriff auf eine Eigenschaft und das Überschreiben von Änderungen von einer anderen Instanz des Bots verhindern möchten. Der Bot erhält eine Fehlerantwort mit der Meldung `etag conflict key=`, wenn er versucht, Zustandsdaten zu speichern, und das `eTag` weist nicht denselben Wert auf wie das `eTag` im Speicher. <!-- To control concurrency of data that is stored using `IStorage`, the BotBuilder SDK checks the entity tag (ETag) for `Storage.Write()` requests. -->

Die `eTag`-Eigenschaft eines Speicherobjekts wird von Cosmos DB standardmäßig jedes Mal auf Gleichheit überprüft, wenn ein Bot in dieses Element schreibt, und nach jedem Schreibvorgang auf einen neuen eindeutigen Wert aktualisiert. Wenn die `eTag`-Eigenschaft beim Schreiben nicht mit der Eigenschaft `eTag` im Speicher übereinstimmt, bedeutet dies, dass ein anderer Bot oder Thread die Daten geändert hat. 

Angenommen, Sie möchten, dass Ihr Bot eine gespeicherte Notiz bearbeitet, aber Sie möchten nicht, dass Ihr Bot Änderungen überschreibt, die eine andere Instanz des Bots vorgenommen hat. Wenn eine andere Instanz des Bots Bearbeitungen vorgenommen hat, möchten Sie, dass der Benutzer die Version mit den neuesten Aktualisierungen bearbeitet.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Erstellen Sie zunächst eine Klasse, die `IStoreItem` implementiert.

```csharp
public class Note : IStoreItem
{
    public string Name { get; set; }
    public string Contents { get; set; }
    public string ETag { get; set; }
}
```

Erstellen Sie anschließend eine erste Notiz, indem Sie ein Speicherobjekt erstellen, und fügen Sie das Objekt Ihrem Speicher hinzu.

```csharp
// create a note for the first time, with a non-null, non-* ETag.
var note = new Note { Name = "Shopping List", Contents = "eggs", ETag = "x" };

var changes = Dictionary<string, object>();
{
    changes.Add("Note", note);
};
await NoteStore.WriteAsync(changes, cancellationToken);
```

Greifen Sie dann später auf die Notiz zu, und aktualisieren Sie sie, indem Sie ihr `eTag`, das Sie aus dem Speicher gelesen haben, beibehalten.

```csharp
var note = NoteStore.ReadAsync<Note>("Note").Result?.FirstOrDefault().Value;

if (note != null)
{
    note.Contents += ", bread";
    var changes = new Dictionary<string, object>();
    {
         changes.Add("Note1", note);
    };
    await NoteStore.WriteAsync(changes, cancellationToken);
}
```

Wenn die Notiz im Speicher aktualisiert wurde, bevor Sie Ihre Änderungen schreiben, löst der Aufruf von `Write` eine Ausnahme aus.


# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Fügen Sie eine Hilfsfunktion am Ende des Bots hinzu, die eine Beispielnotiz in einen Datenspeicher schreibt.
Erstellen Sie zunächst ein `myNoteData`-Objekt.

```javascript
// Helper function for writing a sample note to a data store
async function createSampleNote(storage, context) {
    var myNoteData = {
        name: "Shopping List",
        contents: "eggs",
        // If any Note file is already stored, the eTag field
        // must be set to "*" in order to allow writing without first reading the stored eTag
        // otherwise you'll likely get an exception indicating an eTag conflict. 
        eTag: "*"
    }
}
```

Initialisieren Sie innerhalb der `createSampleNote`-Hilfsfunktion ein `changes`-Objekt, fügen Sie ihm Ihre *Notizen* hinzu, und schreiben Sie es dann in den Speicher.

```javascript
// Write the note data to the "Note" key
var changes = {};
changes["Note"] = myNoteData;
// Creates a file named Note, if it doesn't already exist.
// specifying eTag= "*" will overwrite any existing contents.
// The act of writing to the file automatically updates the eTag property
// The first time you write to Note, the eTag is changed from *, and file contents will become:
//    {"name":"Shopping List","contents":"eggs","eTag":"1"}
try {
    await storage.write(changes);
    await context.sendActivity('Successful created a note.');
} catch (err) {
    await context.sendActivity(`Could not create note: ${err}`);
}
```

Um später auf die Notiz zuzugreifen und sie zu aktualisieren, erstellen Sie dann eine weitere Hilfsfunktion, auf die zugegriffen werden kann, wenn der Benutzer „update note“ (Notiz aktualisieren) eingibt.

```javascript
async function updateSampleNote(storage, context) {
    try {
        // Read in a note
        var note = await storage.read(["Note"]);
        console.log(`note.eTag=${note["Note"].eTag}\n note=${JSON.stringify(note)}`);
        // update the note that we just read
        note["Note"].contents += ", bread";
        console.log(`Updated note=${JSON.stringify(note)}`);

        try {
            await storage.write(note); // Write the changes back to storage
            await context.sendActivity('Successfully updated to note.');
        } catch (err) {            console.log(`Write failed: ${err}`);
        }
    }
    catch (err) {
        await context.sendActivity(`Unable to read the Note: ${err}`);
    }
}
```

Wenn die Notiz im Speicher aktualisiert wurde, bevor Sie Ihre Änderungen schreiben, löst der Aufruf von `write` eine Ausnahme aus.

---

Um die Parallelität zu verwalten, lesen Sie immer eine Eigenschaft aus dem Speicher, und ändern Sie die Eigenschaft dann, die Sie gelesen haben, sodass das `eTag` beibehalten wird. Wenn Sie Benutzerdaten aus dem Speicher lesen, enthält die Antwort die eTag-Eigenschaft. Wenn Sie die Daten ändern und aktualisierte Daten in den Speicher schreiben, sollte Ihre Anforderung die eTag-Eigenschaft enthalten, die den gleichen Wert angibt, den Sie zuvor gelesen haben. Das Schreiben eines Objekts, dessen `eTag` auf `*` festgelegt ist, erlaubt es dem Schreibvorgang jedoch, alle anderen Änderungen zu überschreiben.

## <a name="using-blob-storage"></a>Verwenden von Blob Storage 
Azure Blob Storage ist die Objektspeicherlösung von Microsoft für die Cloud. Blobspeicher ist für die Speicherung großer Mengen von unstrukturierten Daten, z.B. Text oder Binärdaten, optimiert.

### <a name="create-your-blob-storage-account"></a>Erstellen Ihres Blob Storage-Kontos
Um Blob Storage in Ihrem Bot verwenden zu können, müssen Sie einige Einrichtungsschritte durchführen, bevor wir uns mit dem Code befassen.
1. Melden Sie sich in einem neuen Browserfenster beim [Azure-Portal](http://portal.azure.com) an.
2. Klicken Sie auf **Ressource erstellen > Storage > Speicherkonto – Blob, Datei, Tabelle, Warteschlange**.
3. Geben Sie auf der Seite **Neues Konto** einen **Namen** für das Speicherkonto ein, wählen Sie für **Kontoart** die Option **Blob-Speicher** aus, und geben Sie Informationen für **Standort**, **Ressourcengruppe** sowie **Abonnement** an.  
4. Klicken Sie dann auf **Erstellen**.

![Erstellen von Blob-Speicher](./media/create-blob-storage.png)

#### <a name="add-configuration-information"></a>Hinzufügen der Konfigurationsinformationen

Suchen Sie wie oben dargestellt nach den Blob Storage-Schlüsseln, die Sie zum Konfigurieren von Blob Storage für Ihren Bot benötigen:
1. Öffnen Sie im Azure-Portal Ihr Blob Storage-Konto, und klicken Sie auf **Einstellungen > Zugriffsschlüssel**.

![Suchen nach Blob Storage-Schlüsseln](./media/find-blob-storage-keys.png)

Jetzt verwenden Sie zwei dieser Schlüssel, damit der Code auf das Blob Storage-Konto zugreifen kann.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
using Microsoft.Bot.Builder.Azure;
```
Aktualisieren Sie die Zeile des Codes, die _myStorage_ zu Ihrem vorhandenen Blob Storage-Konto verweist.

```csharp


private static readonly AzureBlobStorage _myStorage = new AzureBlobStorage("<your-blob-storage-account-string>", "<your-blob-storage-container-name>");
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)
```javascript
const mystorage = new BlobStorage({
   <youy_containerName>,
   <your_storageAccountOrConnectionString>,
   <your_storageAccessKey>
})
```
---

Sobald _myStorage_ auf Ihr Blob Storage-Konto verweist, verwendet der Botcode zum Speichern und Abrufen von Daten Blob Storage.

## <a name="start-your-bot"></a>Starten Ihres Bots
Führen Sie Ihren Bot lokal aus.

## <a name="start-the-emulator-and-connect-your-bot"></a>Starten des Emulators und Herstellen einer Verbindung mit Ihrem Bot
Starten Sie im nächste Schritt den Emulator, und stellen Sie dann im Emulator eine Verbindung mit Ihrem Bot her:

1. Klicken Sie im Emulator auf der Registerkarte „Willkommen“ auf den Link **Bot öffnen**. 
2. Wählen Sie in dem Verzeichnis, in dem Sie das Projekt erstellt haben, die BOT-Datei aus.

## <a name="interact-with-your-bot"></a>Interagieren mit Ihrem Bot
Senden Sie eine Nachricht an den Bot. Der Bot listet daraufhin die von ihm empfangenen Nachrichten auf.
![Ausgeführter Emulator](../media/emulator-v4/emulator-running.png)

### <a name="view-your-data"></a>Prüfen der Daten
Nachdem Sie den Bot ausgeführt und Ihre Informationen gespeichert haben, können Sie sie im Azure-Portal auf der Registerkarte **Storage-Explorer** anzeigen.

## <a name="blob-transcript-storage"></a>Blob-Transkriptspeicher
Azure-Blob-Transkriptspeicher ist eine spezielle Speicheroption, die Ihnen auf einfache Weise das Speichern und Abrufen von Benutzerkonversationen in Form eines aufgezeichneten Transkripts ermöglicht. Azure-Blob-Transkriptspeicher eignet sich insbesondere für die automatische Erfassung von Benutzereingaben, die dann beim Debuggen zum Untersuchen der Leistung des Bots verwendet werden können.

### <a name="set-up"></a>Einrichtung
Für Azure-Blob-Transkriptspeicher kann das Blob Storage-Konto verwendet werden, das Sie in den obigen Abschnitten _Erstellen Ihres Blob Storage-Kontos_ und _Hinzufügen der Konfigurationsinformationen_ erstellt haben. Für die folgende Erläuterung haben wir einen neuen Blobcontainer _mybottranscripts_ hinzugefügt. 

### <a name="implementation"></a>Implementierung 
Der folgende Code verbindet den Zeiger für den Transkriptspeicher _transcriptStore_ mit Ihrem neuen Azure-Blob-Transkriptspeicherkonto. Der Quellcode zum Speichern der hier angezeigten Benutzerkonversationen basiert auf dem Beispiel zum Konversationsverlauf ([Conversation History](https://aka.ms/bot-history-sample-code)). 

```csharp
// In Startup.cs
using Microsoft.Bot.Builder.Azure;

// Enable the conversation transcript middleware.
blobStore = new AzureBlobTranscriptStore(blobStorageConfig.ConnectionString, storageContainer);
var transcriptMiddleware = new TranscriptLoggerMiddleware(blobStore);
options.Middleware.Add(transcriptMiddleware);

// In ConversationHistoryBot.cs
using Microsoft.Bot.Builder;
using Microsoft.Bot.Builder.Azure;
using Microsoft.Bot.Connector;
using Microsoft.Bot.Schema;

private readonly AzureBlobTranscriptStore _transcriptStore;

/// <param name="transcriptStore">Injected via ASP.NET dependency injection.</param>
public ConversationHistoryBot(AzureBlobTranscriptStore transcriptStore)
{
    _transcriptStore = transcriptStore ?? throw new ArgumentNullException(nameof(transcriptStore));
}

```

### <a name="store-user-conversations-in-azure-blob-transcripts"></a>Speichern von Benutzerkonversationen in Azure-Blob-Transkripts
Sobald ein Blobcontainer zum Speichern von Transkripts verfügbar ist, können Sie beginnen, die Konversationen Ihrer Benutzer mit dem Bot zu speichern. Diese Konversationen können später zum Debuggen verwendet werden, um festzustellen, wie Benutzer mit Ihrem Bot interagieren. Mit dem folgenden Code werden Konversationseingaben des Benutzers gespeichert, wenn „activity.text“ die Eingabenachricht _!history_ erhält.


```csharp
/// <summary>
/// Every Conversation turn for our EchoBot will call this method. 
/// </summary>
/// <param name="turnContext">A <see cref="ITurnContext"/> containing all the data needed
/// for processing this conversation turn. </param>        
public async Task OnTurnAsync(ITurnContext turnContext, CancellationToken cancellationToken = default(CancellationToken))
{
    var activity = turnContext.Activity;
    if (activity.Type == ActivityTypes.Message)
    {
        if (activity.Text == "!history")
        {
           // Download the activities from the Transcript (blob store) when a request to upload history arrives.
           var connectorClient = turnContext.TurnState.Get<ConnectorClient>(typeof(IConnectorClient).FullName);
           // Get all the message type activities from the Transcript.
           string continuationToken = null;
           var count = 0;
           do
           {
               var pagedTranscript = await _transcriptStore.GetTranscriptActivitiesAsync(activity.ChannelId, activity.Conversation.Id, continuationToken);
               var activities = pagedTranscript.Items
                  .Where(a => a.Type == ActivityTypes.Message)
                  .Select(ia => (Activity)ia)
                  .ToList();
               
               var transcript = new Transcript(activities);

               await connectorClient.Conversations.SendConversationHistoryAsync(activity.Conversation.Id, transcript, cancellationToken: cancellationToken);

               continuationToken = pagedTranscript.ContinuationToken;
           }
           while (continuationToken != null);
```

### <a name="find-all-stored-transcripts-for-your-channel"></a>Suchen nach allen gespeicherten Transkripts für Ihren Kanal
Um zu überprüfen, welche Daten gespeichert wurden, können Sie mit dem folgenden Code programmgesteuert nach den _ConversationIDs_ für alle gespeicherten Transkripts suchen. Wenn Sie den Bot-Emulator zum Testen Ihres Codes verwenden, wird bei Auswahl von _Neu beginnen_ ein neues Transkript mit einer neuen _ConversationID_ gestartet.

```csharp
List<string> storedTranscripts = new List<string>();
PagedResult<Transcript> pagedResult = null;
var pageSize = 0;
do
{
    pagedResult = await _transcriptStore.ListTranscriptsAsync("emulator", pagedResult?.ContinuationToken);
    
    // transcript item contains ChannelId, Created, Id.
    // save the converasationIds (Id) found by "ListTranscriptsAsync" to a local list.
    foreach (var item in pagedResult.Items)
    {
         // Make sure we store an unescaped conversationId string.
         var strConversationId = item.Id;
         storedTranscripts.Add(Uri.UnescapeDataString(strConversationId));
    }
} while (pagedResult.ContinuationToken != null);
```

### <a name="retrieve-user-conversations-from-azure-blob-transcript-storage"></a>Abrufen von Benutzerkonversationen aus dem Azure-Blob-Transkriptspeicher
Nachdem Botinteraktionstranskripts in Ihrem Azure-Blob-Transkriptspeicher gespeichert wurden, können Sie sie mit der AzureBlobTranscriptStorage-Methode _GetTranscriptActivities_ programmgesteuert zum Testen oder Debuggen abrufen. Der folgende Codeausschnitt ruft alle Transkripts von Benutzereingaben, die in den letzten 24 Stunden empfangen und gespeichert wurden, aus jedem gespeicherten Transkript ab.


```csharp
var numTranscripts = storedTranscripts.Count();
for (int i = 0; i < numTranscripts; i++)
{
    PagedResult<IActivity> pagedActivities = null;
    do
    {
        string thisConversationId = storedTranscripts[i];
        // Find all inputs in the last 24 hours.
        DateTime yesterday = DateTime.Now.AddDays(-1);
        // Retrieve iActivities for this transcript.
        pagedActivities = await _myTranscripts.GetTranscriptActivitiesAsync("emulator", thisConversationId, pagedActivities?.ContinuationToken, yesterday);
        foreach (var item in pagedActivities.Items)
        {
            // View as message and find value for key "text" :
            var thisMessage = item.AsMessageActivity();
            var userInput = thisMessage.Text;
         }
    } while (pagedActivities.ContinuationToken != null);
}
```

### <a name="remove-stored-transcripts-from-azure-blob-transcript-storage"></a>Entfernen gespeicherter Transkripts aus dem Azure-Blob-Transkriptspeicher
Nachdem Sie die Untersuchung der Benutzereingabedaten zum Testen oder Debuggen des Bots abgeschlossen haben, können Sie gespeicherte Transkripts programmgesteuert aus Ihrem Azure-Blob-Transkriptspeicher entfernen. Der folgende Codeausschnitt entfernt alle gespeicherten Transkripts aus dem Transkriptspeicher Ihres Bots.


```csharp
for (int i = 0; i < numTranscripts; i++)
{
   // Remove all stored transcripts except the last one found.
   if (i > 0)
   {
       string thisConversationId = storedTranscripts[i];    
       await _transcriptStore.DeleteTranscriptAsync("emulator", thisConversationId);
    }
}
```


Unter dem folgenden Link finden Sie weitere Informationen zum [Azure-Blob-Transkriptspeicher](https://docs.microsoft.com/en-us/dotnet/api/microsoft.bot.builder.azure.azureblobtranscriptstore).  

## <a name="next-steps"></a>Nächste Schritte
Da Sie nun wissen, wie Sie direkt aus dem Speicher lesen und in ihn schreiben können, schauen wir uns an, wie der Zustands-Manager diese Aufgabe für Sie übernehmen kann.

> [!div class="nextstepaction"]
> [Speichern des Zustands mithilfe der Unterhaltungs- und Benutzereigenschaften](bot-builder-howto-v4-state.md)

