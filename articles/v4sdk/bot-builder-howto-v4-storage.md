---
title: Speichern von Daten | Microsoft Docs
description: Erfahren Sie, wie Sie mit V4 des Bot Builder SDK für .NET direkt in den Speicher schreiben können.
keywords: Speicher, lesen und schreiben, Arbeitsspeicher, eTag
author: DeniseMak
ms.author: v-demak
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 05/2/18
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 653ec6a1983dd59c485a91b2c08ea07d9f2a34c8
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39302829"
---
# <a name="save-data-directly-to-storage"></a>Speichern Sie Daten direkt im Speicher

<!--
 Note for V4: You can write directly to storage without using the state manager. Therefore, this topic isn't called "managing state". State is in a separate topic.
 ## Manage state data by writing directly to storage
-->
[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

Sie können direkt in den Speicher schreiben, ohne das Kontextobjekt oder die Middleware zu verwenden, indem Sie direkt aus Ihrem Speicherobjekt lesen bzw. in dieses schreiben.

Dieses Codebeispiel veranschaulicht das Lesen und Schreiben von Daten aus dem und in den Speicher. In diesem Beispiel ist der Speicher eine Datei, aber Sie können den Code leicht ändern, um das Speicherobjekt zu initialisieren und stattdessen den Arbeitsspeicher des Azure-Tabellenspeichers zu verwenden. 

# <a name="ctabcsharpechorproperty"></a>[C#](#tab/csharpechorproperty)
Wir definieren ein Objekt und verwenden `IStorage.Write` und `IStorage.Read` zum Speichern und Abrufen des Zustands. 

Das folgende Beispiel fügt jede Nachricht des Benutzers einer Liste hinzu. Die Datenstruktur, die die Liste enthält, wird in einer Datei innerhalb des Verzeichnisses gespeichert, das Sie für den `FileStorage`-Konstruktor bereitstellen.

Beginnen Sie mit der Visual Studio-Vorlage „EchoBot“ im BotBuilder V4 SDK.
Bearbeiten Sie die Datei `EchoBot.cs`. Fügen Sie die folgenden using-Anweisungen hinzu, und ersetzen Sie die `EchoBot`-Klassendefinition.

```csharp
using System.Collections.Generic;
using System.Linq;
```

```csharp
// In the constructor initialize file storage
public class EchoBot : IBot
{
    private readonly FileStorage _myStorage;

    public EchoBot()
    {
        _myStorage = new FileStorage(System.IO.Path.GetTempPath());
    }

    // Add a class for storing a log of utterances (text of messages) as a list
    public class UtteranceLog : IStoreItem
    {
        // A list of things that users have said to the bot
        public List<string> UtteranceList { get; private set; } = new List<string>();

        // The number of conversational turns that have occurred        
        public int TurnNumber { get; set; } = 0;

        public string eTag { get; set; } = "*";
    }

    // Replace the OnTurn in EchoBot.cs with the following:
    public async Task OnTurn(ITurnContext context)
    {
        var activityType = context.Activity.Type;

        await context.SendActivity($"Activity type: {context.Activity.Type}.");

        if (activityType == ActivityTypes.Message)
        {
            // *********** begin (create or add to log of messages)
            var utterance = context.Activity.Text;
            bool restartList = false;

            if (utterance.Equals("restart"))
            {
                restartList = true;
            }

            // Attempt to read the existing property bag
            UtteranceLog logItems = null;
            try
            {
                logItems = _myStorage.Read<UtteranceLog>("UtteranceLog").Result?.FirstOrDefault().Value;
            }
            catch (System.Exception ex)
            {
                await context.SendActivity(ex.Message);
            }

            // If the property bag wasn't found, create a new one
            if (logItems is null)
            {
                try
                {
                    // add the current utterance to a new object.
                    logItems = new UtteranceLog();
                    logItems.UtteranceList.Add(utterance);

                    await context.SendActivity($"The list is now: {string.Join(", ",logItems.UtteranceList)}");

                    var changes = new KeyValuePair<string, object>[]
                    {
                        new KeyValuePair<string, object>("UtteranceLog", logItems)
                    };
                    await _myStorage.Write(changes);
                }
                catch (System.Exception ex)
                {
                    await context.SendActivity(ex.Message);
                }
            }
            // logItems.ContainsKey("UtteranceLog") == true, we were able to read a log from storage
            else
            {
                // Modify its property
                if (restartList)
                {
                    logItems.UtteranceList.Clear();
                }
                else
                {
                    logItems.UtteranceList.Add(utterance);
                    logItems.TurnNumber++;
                }

                await context.SendActivity($"The list is now: {string.Join(", ", logItems.UtteranceList)}");

                var changes = new KeyValuePair<string, object>[]
                {
                        new KeyValuePair<string, object>("UtteranceLog", logItems)
                };
                await _myStorage.Write(changes);
            }
        }

        return;
    }
}
```
# <a name="javascripttabjsechoproperty"></a>[JavaScript](#tab/jsechoproperty)

Das folgende Beispiel fügt jede Nachricht des Benutzers einer Liste hinzu. Die Datenstruktur, die die Liste enthält, wird in einer Datei innerhalb des Verzeichnisses gespeichert, das Sie für den `FileStorage`-Konstruktor bereitstellen.

Fügen Sie Folgendes in `app.js` ein.
``` javascript
const { BotFrameworkAdapter, FileStorage, MemoryStorage, ConversationState, BotStateSet } = require('botbuilder');
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

// Add storage.
var storage = new FileStorage("C:/temp");
const conversationState = new ConversationState(storage);
adapter.use(new BotStateSet(conversationState));

// Listen for incoming activities.
server.post('/api/messages', (req, res) => {
    // Route received activity to adapter for processing.
    adapter.processActivity(req, res, async (context) => {
        if (context.activity.type === 'message') {
            const state = conversationState.get(context);
            const count = state.count === undefined ? state.count = 0 : ++state.count;

            let utterance = context.activity.text;
            let storeItems = await storage.read(["UtteranceLog"])
            try {
                // check result
                var utteranceLog = storeItems["UtteranceLog"];
                
                if (typeof (utteranceLog) != 'undefined') {
                    // log exists so we can write to it
                    storeItems["UtteranceLog"].UtteranceList.push(utterance);
                    
                    await storage.write(storeItems)
                    try {
                        context.sendActivity('Successful write.');
                    } catch (err) {
                        context.sendActivity(`Srite failed of UtteranceLog: ${err}`);
                    }

                } else {
                    context.sendActivity(`need to create new utterance log`);
                    storeItems["UtteranceLog"] = { UtteranceList: [`${utterance}`], "eTag": "*" }
                    await storage.write(storeItems)
                    try {
                        context.sendActivity('Successful write.');
                    } catch (err) {
                        context.sendActivity(`Write failed: ${err}`);
                    }
                }
            } catch (err) {
                context.sendActivity(`Read rejected. ${err}`);
            };

            await context.sendActivity(`${count}: You said "${context.activity.text}"`);
        } else {
            await context.sendActivity(`[${context.activity.type} event detected]`);
        }
    });
});
```
---

## <a name="manage-concurrency-using-etags"></a>Verwalten von Parallelität mit eTags

Im vorherigen Beispiel haben Sie die `eTag`-Eigenschaft auf `*` festgelegt. Der `eTag`-Member (Entitätstag) Ihres Speicherobjekts dient zum Verwalten von Parallelität. Das `eTag` gibt an, wie vorzugehen ist, wenn eine andere Instanz des Bots das Objekt im Speicher geändert hat, in das der Bot schreibt. 

<!-- define optimistic concurrency -->

### <a name="last-write-wins---allow-overwrites"></a>Der letzte Schreibzugriff gewinnt: Überschreiben zulassen

Legen Sie das eTag auf `*` fest, um anderen Instanzen des Bots zu erlauben, zuvor geschriebene Daten zu überschreiben. Wenn der Eigenschaftenwert von `eTag` das Sternchen (`*`) ist, zeigt dies an, dass der letzte Schreibvorgang gewinnt. Wenn Sie einen neuen Datenspeicher erstellen, können Sie das `eTag` einer Eigenschaft auf `*` festlegen, um anzugeben, dass Sie die Daten, die Sie schreiben, noch nicht gespeichert haben, oder dass der letzte Schreibvorgang eine zuvor gespeicherte Eigenschaft überschreiben soll. Wenn die Parallelität kein Problem für Ihren Bot darstellt, erlaubt das Festlegen der `eTag`-Eigenschaft auf `*` für alle Daten, die Sie schreiben, das Überschreiben.

Dies wird im folgenden Codebeispiel gezeigt.

# <a name="ctabcsetagoverwrite"></a>[C#](#tab/csetagoverwrite)
Verwenden Sie die `UtteranceLog`-Klasse, die wir zuvor definiert haben.
```csharp
// Add the current utterance to a new object and save it to storage.
logItems = new UtteranceLog();
logItems.UtteranceList.Add(utterance);
logItems.eTag = "*";

var changes = new KeyValuePair<string, object>[]
{
    new KeyValuePair<string, object>("UtteranceLog", logItems)
};
await _myStorage.Write(changes);

```
# <a name="javascripttabjstagoverwrite"></a>[JavaScript](#tab/jstagoverwrite)
Fügen Sie dem Protokoll eine neue Äußerung hinzu, und lassen Sie Überschreiben zu.

```javascript
storeItems["UtteranceLog"] = { UtteranceList: [`${utterance}`], "eTag": "*" }
await storage.write(storeItems)
```
---

### <a name="maintain-concurrency-and-prevent-overwrites"></a>Verwalten von Parallelität und Verhindern von Überschreibungen
Verwenden Sie einen anderen Wert als `*` für das `eTag`, wenn Sie gleichzeitigen Zugriff auf eine Eigenschaft verhindern möchten, um zu vermeiden, dass Änderungen aus einer anderen Instanz des Bots überschrieben werden. Der Bot erhält eine Fehlerantwort mit der Nachricht `etag conflict key=`, wenn er versucht, Zustandsdaten zu speichern, und das `eTag` ist nicht identisch mit dem ETag im Speicher. <!-- To control concurrency of data that is stored using `IStorage`, the BotBuilder SDK checks the entity tag (ETag) for `Storage.Write()` requests. -->

Standardmäßig überprüft der Speicher die `eTag`-Eigenschaft eines Speicherobjekts jedes Mal auf Gleichheit, wenn ein Bot in dieses Element schreibt, und aktualisiert es dann nach jedem Schreibvorgang auf einen neuen eindeutigen Wert. Wenn die `eTag`-Eigenschaft beim Schreiben nicht mit der Eigenschaft `eTag` im Speicher übereinstimmt, bedeutet dies, dass ein anderer Bot oder Thread die Daten geändert hat. 

Angenommen, Sie möchten, dass Ihr Bot eine gespeicherte Notiz bearbeitet, aber Sie möchten nicht, dass Ihr Bot Änderungen überschreibt, die eine andere Instanz des Bots vorgenommen hat. Wenn eine andere Instanz des Bots Bearbeitungen vorgenommen hat, möchten Sie, dass der Benutzer die Version mit den neuesten Aktualisierungen bearbeitet.

# <a name="ctabcsetag"></a>[C#](#tab/csetag)
Erstellen Sie zunächst eine Klasse, die `IStoreItem` implementiert.
```csharp
public class Note : IStoreItem
{
    public string Name { get; set; }
    public string Contents { get; set; }
    public string eTag { get; set; }
}
```
Erstellen Sie anschließend eine erste Notiz, indem Sie ein Speicherobjekt erstellen, und fügen Sie das Objekt Ihrem Speicher hinzu.
```csharp
// create a note for the first time, with a non-null, non-* eTag.
var note = new Note { Name = "Shopping List", Contents = "eggs", eTag = "x" };

var changes = new KeyValuePair<string, object>[]
{
    new KeyValuePair<string, object>("Note", note)
};
await NoteStore.Write(changes);
```
Greifen Sie dann später auf die Notiz zu, und aktualisieren Sie sie, indem Sie ihr `eTag`, das Sie aus dem Speicher gelesen haben, beibehalten.
```csharp
var note = NoteStore.Read<Note>("Note").Result.FirstOrDefault().Value;

if (note != null)
{
    note.Contents += ", bread";
    var changes = new KeyValuePair<string, object>[]
    {
        new KeyValuePair<string, object>("Note1", note)
    };
    await NoteStore.Write(changes);
}
```
Wenn die Notiz im Speicher aktualisiert wurde, bevor Sie Ihre Änderungen schreiben, löst der Aufruf von `Write` eine Ausnahme aus.

# <a name="javascripttabjsetag"></a>[JavaScript](#tab/jsetag)

Erstellen Sie zunächst ein `myNote`-Objekt.
```javascript
var myNote = {
    name: "Shopping List",
    contents: "eggs",
    eTag: "*"
}
```
Im nächsten Schritt initialisieren Sie ein `changes`-Objekt und fügen Ihre *Notizen* hinzu, dann schreiben Sie es in den Speicher.

```javascript
// Write a note
var changes = {};
changes["Note"] = myNote;
await storage.write(changes);
```
Greifen Sie dann später auf die Notiz zu, und aktualisieren Sie sie, indem Sie ihr `eTag`, das Sie aus dem Speicher gelesen haben, beibehalten.
```javascript
// Read in a note
var note = await storage.read(["Note"]);
try {
    // Someone has updated the note. Need to update our local instance to match
    if(myNote.eTag != note.eTag){
        myNote = note;
    }

    // Add any updates to the note and write back out
    myNote.contents += ", bread";   // Add to the note
    changes["Note"] = note;
    await storage.write(changes); // Write the changes back to storage
    try {
        context.sendActivity('Successful write.');
    } catch (err) {
        context.sendActivity(`Write failed: ${err}`);
    }
}
catch (err) {
    context.sendActivity(`Unable to read the Note: ${err}`);
}
```
Wenn die Notiz im Speicher aktualisiert wurde, bevor Sie Ihre Änderungen schreiben, löst der Aufruf von `Write` eine Ausnahme aus.

---

Um die Parallelität zu verwalten, lesen Sie immer eine Eigenschaft aus dem Speicher, und ändern Sie die Eigenschaft dann, die Sie gelesen haben, sodass das `eTag` beibehalten wird. Wenn Sie Benutzerdaten aus dem Speicher lesen, enthält die Antwort die eTag-Eigenschaft. Wenn Sie die Daten ändern und aktualisierte Daten in den Speicher schreiben, sollte Ihre Anforderung die eTag-Eigenschaft enthalten, die den gleichen Wert angibt, den Sie zuvor gelesen haben. Das Schreiben eines Objekts, dessen `eTag` auf `*` festgelegt ist, erlaubt es dem Schreibvorgang jedoch, alle anderen Änderungen zu überschreiben.

<!-- If the ETag specified in your `Storage.Write()` request matches the current value in the store, the server will save the data and specify a new eTag value in the body of the response, that indicates that the data has been updated. If the ETag specified in your Storage.Write() request does not match the current value in the store, the bot responds with an error indicating an eTag conflict, to indicate that the user's data in the store has changed since you last saved or retrieved it. -->

<!-- TODO: new snippet -->

<!-- jf: I think this section can be cut entirely.

## Save a conversation reference using storage

You can use IStorage to save BotBuilder SDK objects as well as user-defined data. This code snippet uses `Storage.Write()` to save a ConversationReference object for use in sending a proactive message later.

# [C#](#tab/csharpwriteconvref)
```csharp
            var reference = context.ConversationReference;
            var userId = reference.User.Id;

            // save the ConversationReference to a global variable of this class
            conversationReference = reference;

            StoreItems storeItems = new StoreItems();
            StoreItem conversationReferenceToStore = new StoreItem();
            // set the eTag to "*" to indicate you're overwriting previous data
            conversationReferenceToStore.eTag = "*";
            conversationReferenceToStore.Add("ref", reference);
            storeItems[$"ConversationReference/{userId}"] = conversationReferenceToStore;

            await storage.Write(storeItems).ConfigureAwait(false);

            SubscribeUser(userId);

            await context.SendActivity("Thank You! We will message you shortly.");
        }

public void SubscribeUser(string userId)
{
    CreateContextForUserAsync(userId, async (ITurnContext context) =>
    {
        await context.SendActivity("You've been notified.");
        await Task.Delay(2000);
    });
                
}
```
# [JavaScript](#tab/jswriteconvref)
```javascript
const { MemoryStorage } = require('botbuilder');

const storage = new MemoryStorage();

// Listen for incoming activity 
server.post('/api/messages', (req, res) => {
    // Route received activity to adapter for processing
    adapter.processActivity(req, res, async (context) => {
        if (context.activity.type === 'message') {
            const utterances = (context.activity.text || '').trim().toLowerCase()
            if (utterances === 'subscribe') {
                const reference = context.activity;
                const userId = reference.id;
                const changes = {};
                changes['reference/' + userId] = reference;
                await storage.write(changes)
                await subscribeUser(userId)
                await context.sendActivity(`Thank You! We will message you shortly.`);
               
            } else{
                await context.sendActivity("Say 'subscribe'");
            }
    
        }
    });
});
```
---

To read the saved object from storage, call `Storage.Read()`.

# [C#](#tab/csharpread)
```csharp


        private async void CreateContextForUserAsync(String userId,Func<ITurnContext, Task> onReady)
        {
            var referenceKey = $"ConversationReference/{userId}";
            
            ConversationReference localRef = null;
            StoreItems conversationReferenceFromStorage = await storage.Read(referenceKey);
            foreach (var item in conversationReferenceFromStorage)
            {
                StoreItem value = item.Value as StoreItem;
                localRef = value["ref"];
            }
            
            await bot.CreateContext(this.conversationReference, onReady);

        }
```
# [JavaScript](#tab/jsread)
```javascript
async function subscribeUser(userId) {
    setTimeout(() => {
        createContextForUser(userId, (context) => {
            context.sendActivity("You have been notified");
        })
    }, 2000);
}

async function createContextForUser(userId, callback) {
    const referenceKey = 'reference/' + userId;
    var rows = await storage.read([referenceKey])
    var reference = await rows[referenceKey]
    await callback(adapter.createContext(reference))
          
}
```
---

-->

## <a name="next-steps"></a>Nächste Schritte

Da Sie nun wissen, wie Sie direkt aus dem Speicher lesen und in ihn schreiben können, schauen wir uns an, wie der Zustands-Manager diese Aufgabe für Sie übernehmen kann.

> [!div class="nextstepaction"]
> [Speichern des Zustands mithilfe der Unterhaltungs- und Benutzereigenschaften](bot-builder-howto-v4-state.md)

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Konzept: Speichervorgänge im Bot Builder SDK](bot-builder-storage-concept.md)


