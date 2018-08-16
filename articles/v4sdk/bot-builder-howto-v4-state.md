---
title: Speichern des Zustands mithilfe der Konversations- und Benutzereigenschaften | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie mithilfe des Bot Builder SDK V4 für .NET Daten speichern und abrufen.
keywords: konversationszustand, benutzerzustand, zustandsmiddleware, konversationsfluss, file storage, azure table storage
author: ivorb
ms.author: v-demak
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 05/03/18
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 16df371b1cabb4b3eb47d1f491a5d45e26627d38
ms.sourcegitcommit: dcbc8ad992a3e242a11ebcdf0ee99714d919a877
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/30/2018
ms.locfileid: "39352849"
---
# <a name="save-state-using-conversation-and-user-properties"></a>Speichern des Zustands mithilfe der Konversations- und Benutzereigenschaften

[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

Damit Ihr Bot den Konversations- und den Benutzerzustand speichern kann, müssen Sie zunächst die Middleware für den Zustands-Manager initialisieren und anschließend die Konversations- und Benutzerzustandseigenschaften verwenden.
Weitere Informationen zur Verwendung dieses Zustands finden Sie unter [State and storage (Zustand und Speicher)](./bot-builder-storage-concept.md).

## <a name="initialize-state-manager-middleware"></a>Initialisieren der Zustands-Manager-Middleware

Sie müssen den Bot-Adapter im SDK initialisieren, um die Zustands-Manager-Middleware zu verwenden, bevor Sie den Speicher für die Konversations- und Benutzereigenschaften verwenden können. Der _Konversationszustand_ wird für Konversationseigenschaften verwendet, und der _Benutzerzustand_ wird für Benutzereigenschaften verwendet. (Der Zugriff auf die Benutzerzustandseigenschaften kann über mehrere Konversationen hinweg erfolgen.) Die Zustands-Manager-Middleware stellt eine Abstraktion bereit, mit der Sie unabhängig vom Typ des zugrunde liegenden Speichers mithilfe eines einfachen Schlüsselwerts oder Objektspeichers auf Eigenschaften zugreifen können. Der Zustands-Manager übernimmt das Schreiben von Daten in den Speicher und das Verwalten der Parallelität, unabhängig davon, ob der zugrunde liegende Speichertyp In-Memory, File Storage oder Azure Table Storage ist.


# <a name="ctabcsharp"></a>[C#](#tab/csharp)
Um zu erfahren, wie `ConversationState` initialisiert wird, sehen Sie sich `Startup.cs` im Beispiel Microsoft.Bot.Samples.EchoBot-AspNetCore an.

```csharp
services.AddBot<EchoBot>(options =>
{
    options.CredentialProvider = new ConfigurationCredentialProvider(Configuration);

    IStorage dataStore = new MemoryStorage();
    options.Middleware.Add(new ConversationState<EchoState>(dataStore));
});
```

In der Zeile `options.Middleware.Add(new ConversationState<EchoState>(dataStore));` ist `ConversationState` das Konversationszustands-Manager-Objekt, das dem Bot als Middleware hinzugefügt wird. Der Typparameter `EchoState` ist der Typ, der darstellt, wie die Konversationszustandsinformationen gespeichert werden sollen. Ein Bot kann einen beliebigen Klassentyp für Konversations- oder Benutzerzustandsdaten verwenden.

Die Implementierung von `EchoState` befindet sich in `EchoBot.cs`:
```csharp
public class EchoState
{
    public int TurnNumber { get; set; }
}
``` 

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)

Definieren Sie den Speicheranbieter, und weisen Sie ihn dem Zustands-Manager zu, den Sie verwenden möchten.
Sie können den gleichen Speicheranbieter für die Verwaltungsmiddleware für `ConversationState` und `UserState` verwenden.
Verwenden Sie dann die `BotStateSet`-Bibliothek, um sie mit der Ebene der Middleware zu verbinden, die die Beibehaltung der Daten verwaltet.

```javascript
const { BotFrameworkAdapter, MemoryStorage, ConversationState, UserState, BotStateSet } = require('botbuilder');
const restify = require('restify');

// Create adapter
const adapter = new BotFrameworkAdapter({ 
    appId: process.env.MICROSOFT_APP_ID, 
    appPassword: process.env.MICROSOFT_APP_PASSWORD 
});

// Add conversation state middleware.
const conversationState = new ConversationState(new MemoryStorage());
adapter.use(conversationState);

// Alternatively, use both conversation and user state middleware.
// const storage = new MemoryStorage;
// const conversationState = new ConverstationState(storage);
// const userState = new UserState(storage);
// adapter.use(new BotStateSet(conversationState, userState));
```    
---

> [!NOTE] 
> Der In-Memory-Datenspeicher dient nur zu Testzwecken. Dieser Speicher ist flüchtig und temporär. Die Daten werden jedes Mal gelöscht, wenn der Bot neu gestartet wird. Im späteren Verlauf des Artikels erfahren Sie unter [File Storage](#file-storage) und [Azure Table Storage](#azure-table-storage), wie Sie andere zugrunde liegende Speichermedien für den Konversationszustand und Benutzerzustand einrichten. 

### <a name="configuring-state-manager-middleware"></a>Konfigurieren der Zustands-Manager-Middleware

Beim Initialisieren ermöglicht die Zustandsmiddleware einen optionalen _state settings_-Parameter, mit dem Sie das Standardverhalten der Speicherung von Eigenschaften ändern können. Die Standardeinstellungen sind folgende:

* Eigenschaften nach Ablauf des Turn-Kontexts beibehalten.
* Wenn mehrere Instanzen des Bots in eine Eigenschaft schreiben, kann die letzte Instanz des Bots die vorherige überschreiben.

## <a name="use-conversation-and-user-state-properties"></a>Verwenden von Konversations- und Benutzerzustandseigenschaften 
<!-- middleware and message context properties -->

Sobald die Zustands-Manager-Middleware konfiguriert wurde, können Sie die Konversations- und Benutzerzustandseigenschaften über das Kontextobjekt abrufen.
<!-- Changes are written to storage before the `SendActivity()` pipeline completes. -->

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Das `Microsoft.Bot.Samples.EchoBot`-Beispiel veranschaulicht, wie dies im Bot Builder SDK funktioniert. 

Im `OnTurn`-Handler ruft `context.GetConversationState` den Konversationszustand ab, um auf die von Ihnen definierten Daten zuzugreifen. Den Zustand können Sie mithilfe der Eigenschaften ändern (in diesem Fall durch erhöhen von `TurnNumber`).

```csharp
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
```   

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)

Der aus dem Yeoman-Generator-Beispiel generierte EchoBot veranschaulicht, wie dies funktioniert.

In diesem Codebeispiel wird gezeigt, wie Sie einen Turnzähler im Konversationszustand speichern können.

```javascript
const { BotFrameworkAdapter, MemoryStorage, ConversationState } = require('botbuilder');
const restify = require('restify');

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

// Add conversation state middleware
const conversationState = new ConversationState(new MemoryStorage());
adapter.use(conversationState);

// Listen for incoming requests 
server.post('/api/messages', (req, res) => {
    // Route received request to adapter for processing
    adapter.processActivity(req, res, async (context) => {
        if (context.activity.type === 'message') {
            const state = conversationState.get(context);
            const count = state.count === undefined ? state.count = 0 : ++state.count;
            await context.sendActivity(`${count}: You said "${context.activity.text}"`);
        } else {
            await context.sendActivity(`[${context.activity.type} event detected]`);
        }
    });
});
```
---

## <a name="using-conversation-state-to-direct-conversation-flow"></a>Verwenden des Konversationszustand zum Leiten des Konversationsflusses

Beim Entwerfen eines Konversationsflusses ist es nützlich, ein Zustandsflag zu definieren, um den Konversationsfluss zu leiten. Dieses kann ein einfaches **boolesches** Flag sein oder den Namen des aktuellen Themas enthalten. Mithilfe des Flags können Sie verfolgen, wo Sie sich in einer Konversation befinden. Ein **boolesches** Flag kann Ihnen beispielsweise mitteilen, ob Sie sich in einer Konversation befinden oder nicht, während eine Eigenschaft mit dem Namen des Themas Ihnen mitteilt, in welcher Konversation Sie sich derzeit befinden.

Im folgenden Beispiel wird eine boolesche _haveAskedName_-Eigenschaft verwendet, um anzugeben, wann der Bot den Namen des Benutzers abgefragt hat. Wenn die nächste Nachricht empfangen wird, überprüft der Bot die Eigenschaft. Wenn die Eigenschaft auf `true` festgelegt ist, weiß der Bot, dass der Benutzername eben abgefragt wurde, und er interpretiert die nächste eingehende Nachricht als Namen, der als Benutzereigenschaft gespeichert wird.


# <a name="ctabcsharp"></a>[C#](#tab/csharp)
```csharp
public class ConversationInfo
{
    public bool haveAskedNameFlag { get; set; }
    public bool haveAskedNumberFlag { get; set; }
}

public class UserInfo
{
    public string name { get; set; }
    public string telephoneNumber { get; set; }
    public bool done { get; set; }
}

public async Task OnTurn(ITurnContext context)
{
    // Get state objects. Default objects are created if they don't already exist.
    var convo = ConversationState<ConversationInfo>.Get(context);
    var user = UserState<UserInfo>.Get(context);

    if (context.Activity.Type is ActivityTypes.Message)
    {
        if (string.IsNullOrEmpty(user.name) && !convo.haveAskedNameFlag)
        {
            // Ask for the name.
            await context.SendActivity("Hello. What's your name?");

            // Set flag to show we've asked for the name. We save this out so the
            // context object for the next turn of the conversation can check haveAskedName
            convo.haveAskedNameFlag = true;
        }
        else if (!convo.haveAskedNumberFlag)
        {
            // Save the name.
            var name = context.Activity.AsMessageActivity().Text;
            user.name = name;
            convo.haveAskedNameFlag = false; // Reset flag

            // Ask for the phone number. You might want a flag to track this, too.
            await context.SendActivity($"Hello, {name}. What's your telephone number?");
            convo.haveAskedNumberFlag = true;
        }
        else if (convo.haveAskedNumberFlag)
        {
            // save the telephone number
            var telephonenumber = context.Activity.AsMessageActivity().Text;

            user.telephoneNumber = telephonenumber;
            convo.haveAskedNumberFlag = false; // Reset flag
            await context.SendActivity($"Got it. I'll call you later.");
        }
    }
}
```

Um den Benutzerzustand so einzurichten, dass er von `UserState<UserInfo>.Get(context)` zurückgegeben werden kann, fügen Sie Benutzerzustandsmiddleware hinzu, beispielsweise in der `Startup.cs` von ASP.NET Core EchoBot, wodurch der Code in der Datei „ConfigureServices.cs“ geändert wird:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddBot<EchoBot>(options =>
    {
        options.CredentialProvider = new ConfigurationCredentialProvider(Configuration);
        
        IStorage dataStore = new MemoryStorage();
        options.Middleware.Add(new ConversationState<ConversationInfo>(dataStore));
        options.Middleware.Add(new UserState<UserInfo>(dataStore));
    });
}
```

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)

**app.js**

```js
const { BotFrameworkAdapter, MemoryStorage, ConversationState, UserState, BotStateSet } = require('botbuilder');
const restify = require('restify');

// Create server
let server = restify.createServer();
server.listen(process.env.port || process.env.PORT || 3978, function () {
    console.log(`${server.name} listening to ${server.url}`);
});

// Create adapter (it's ok for MICROSOFT_APP_ID and MICROSOFT_APP_PASSWORD to be blank for now)  
const adapter = new BotFrameworkAdapter({ 
    appId: process.env.MICROSOFT_APP_ID, 
    appPassword: process.env.MICROSOFT_APP_PASSWORD 
});

// Storage
const storage = new MemoryStorage();
const conversationState = new ConversationState(storage);
const userState  = new UserState(storage);
adapter.use(new BotStateSet(conversationState, userState));

// Listen for incoming requests 
server.post('/api/messages', (req, res) => {
    // Route received request to adapter for processing
    adapter.processActivity(req, res, async (context) => {
        const isMessage = (context.activity.type === 'message');
        const convo = conversationState.get(context);
        const user = userState.get(context);

        if (isMessage) {
            if(!user.name && !convo.haveAskedNameFlag){
                // Ask for the name.
                await context.sendActivity("What is your name?")
                // Set flag to show we've asked for the name. We save this out so the
                // context object for the next turn of the conversation can check haveAskedNameFlag
                convo.haveAskedNameFlag = true;
            } else if(convo.haveAskedNameFlag){
                // Save the name.
                user.name = context.activity.text;
                convo.haveAskedNameFlag = false; // Reset flag

                await context.sendActivity(`Hello, ${user.name}. What's your telephone number?`);
                convo.haveAskedNumberFlag = true; // Set flag
            } else if(convo.haveAskedNumberFlag){
                // save the phone number
                user.telephonenumber = context.activity.text;
                convo.haveAskedNumberFlag = false; // Reset flag
                await context.sendActivity(`Got it. I'll call you later.`);
            }
        }

        // ...
    });
});

```

---

Alternativ können Sie das _Wasserfallmodell_ eines Dialogfelds verwenden. Das Dialogfeld verfolgt den Konversationszustand für Sie, weshalb Sie keine Flags erstellen müssen, um den Zustand nachzuverfolgen. Weitere Informationen finden Sie unter [Manage conversation with Dialogs (Verwalten von Konversationen mithilfe von Dialogfeldern)](bot-builder-dialog-manage-conversation-flow.md).

## <a name="file-storage"></a>File Storage

Der Arbeitsspeicheranbieter verwendet In-Memory-Speicher, der beim Neustart des Bots verworfen wird. Dies empfiehlt sich nur für Testzwecke. Wenn Sie Daten beibehalten möchten, aber Ihren Bot nicht mit einer Datenbank verknüpfen möchten, können Sie den File Storage-Anbieter verwenden. Obwohl dieser Anbieter auch zu Testzwecken konzipiert wurde, speichert er Zustandsdaten in eine Datei, damit Sie sie überprüfen können. Die Daten werden im JSON-Format in eine Datei geschrieben.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Öffnen Sie im Microsoft.Bot.Samples.EchoBot-AspNetCore-Beispiel die Datei `Startup.cs`, und bearbeiten Sie den Code in der `ConfigureServices`-Methode.
```csharp
// This method gets called by the runtime. Use this method to add services to the container.
public void ConfigureServices(IServiceCollection services)
{
    services.AddBot<EchoBot>(options =>
    {
        options.CredentialProvider = new ConfigurationCredentialProvider(Configuration);

        // Using file storage instead of in-memory storage.
        IStorage dataStore = new FileStorage(System.IO.Path.GetTempPath());
        options.Middleware.Add(new ConversationState<EchoState>(dataStore));
    });
}
``` 

Führen Sie den Code aus, und lassen Sie Ihre Eingabe ein paar Mal vom EchoBot zurückgeben.

Navigieren Sie dann zum von `System.IO.Path.GetTempPath()` angegebenen Verzeichnis. Eine Datei mit einem Namen, der mit „conversation“ beginnt, sollte angezeigt werden. Öffnen Sie die Datei, und sehen Sie sich den JSON-Code an. Der Inhalt der Datei sieht in etwa wie folgt aus:
```json
{
  "$type": "Microsoft.Bot.Samples.Echo.EchoState, Microsoft.Bot.Samples.EchoBot",
  "TurnNumber": "3",
  "eTag": "ecfe2a23566b4b52b2fe697cffc59385"
}
```

`$type` gibt den Typ der Datenstruktur an, den Sie in Ihrem Bot zum Speichern des Konversationszustands verwenden. Das Feld `TurnNumber` entspricht der `TurnNumber`-Eigenschaft in der `EchoState`-Klasse. Das Feld `eTag` ist ein eindeutiger Wert, der von `IStoreItem` geerbt und jedes Mal automatisch aktualisiert wird, wenn Ihr Bot den Konversationszustand aktualisiert.  Das eTag-Feld ermöglicht dem Bot das Aktivieren optimistischer Parallelität.

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)

Aktualisieren Sie wie zuvor im Abschnitt [Verwenden von Konversations- und Benutzerzustandseigenschaften](#use-conversation-and-user-state-properties) beschrieben das EchoBot-Beispiel, um `FileStorage` zu verwenden. Stellen Sie sicher, dass `storage` auf `FileStorage` festgelegt ist und nicht auf `MemoryStorage`. Fordern Sie außerdem `FileStorage` vom BotBuilder SDK an. Dies sind die einzigen erforderlichen Änderungen. 

```javascript
// Storage
const storage = new FileStorage("c:/temp");
const conversationState = new ConversationState(storage);
const userState  = new UserState(storage);
adapter.use(new BotStateSet(conversationState, userState));
```

Der `FileStorage`-Anbieter akzeptiert einen „Pfad“ als Parameter. Durch Angabe eines Pfads können Sie die Datei mit den beibehaltenen Informationen von Ihrem Bot einfach finden. Für jede *Konversation* wird eine neue Datei angelegt. Also finden Sie im *Pfad* möglicherweise mehrere Dateinamen, die mit `conversation!` beginnen. Sie können nach dem Datum sortieren, um die neuesten Konversationen leichter zu finden. Dahingegen gibt es nur eine Datei für den *Benutzerzustand*. Der Dateiname beginnt mit `user!`. Jedes Mal, wenn der Zustand eines dieser Objekte geändert wird, aktualisiert der Zustands-Manager die Datei, um die Änderungen widerzuspiegeln.

Führen Sie den Bot aus, und senden Sie ihm ein paar Nachrichten. Suchen Sie anschließend die Speicherdatei, und öffnen Sie diese. Hier ist ein Beispiel dafür, wie der JSON-Inhalt für den EchoBot aussehen könnte, der den Turnzähler nachverfolgt.

```json
{
  "turnNumber": "3",
  "eTag": "322"
}
```
---

## <a name="azure-table-storage"></a>Azure Table Storage

Sie können auch Azure Table Storage als Speichermedium verwenden.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Fügen Sie im Beispiel „Microsoft.Bot.Samples.EchoBot-AspNetCore“ einen Verweis auf das NuGet-Paket `Microsoft.Bot.Builder.Azure` hinzu.

Öffnen Sie dann die Datei `Startup.cs`, fügen Sie die Anweisung `using Microsoft.Bot.Builder.Azure;` hinzu, und bearbeiten Sie den Code in der `ConfigureServices`-Methode.
```csharp
services.AddBot<EchoBot>(options =>
{
    options.CredentialProvider = new ConfigurationCredentialProvider(Configuration);
    // The parameters are the connection string and table name.
    // "UseDevelopmentStorage=true" is the connection string to use if you are using the Azure Storage Emulator.
    // Replace it with your own connection string if you're not using the emulator
    options.Middleware.Add(new ConversationState<EchoState>(new AzureTableStorage("UseDevelopmentStorage=true","conversationstatetable")));
    // you could also specify the cloud storage account instead of the connection string
    /* options.Middleware.Add(new ConversationState<EchoState>(
        new AzureTableStorage(WindowsAzure.Storage.CloudStorageAccount.DevelopmentStorageAccount, "conversationstatetable"))); */
    options.EnableProactiveMessages = true;
});
```
Sie können die Verbindungszeichenfolge `UseDevelopmentStorage=true` mit dem [Azure Storage-Emulator][AzureStorageEmulator] verwenden. Ersetzen Sie diese mit Ihrer eigenen Verbindungszeichenfolge, wenn Sie den Emulator nicht verwenden.

Wenn die Tabelle mit dem Namen, den Sie im Konstruktor für `AzureTableStorage` angeben, nicht existiert, wird diese erstellt.

<!-- 
TODO: step-by-step inspection of the stored table
-->

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)

In der `app.js`-Datei des EchoBot-Beispiels können Sie die ConversationState-Klasse mit `AzureTableStorage` erstellen. 

```bash
npm install --save botbuilder-azure@preview
```

```javascript
const { BotFrameworkAdapter, FileStorage, MemoryStorage, ConversationState } = require('botbuilder');
const { TableStorage } = require('botbuilder-azure');

// ...

// Create adapter
const adapter = new BotFrameworkAdapter({
    appId: process.env.MICROSOFT_APP_ID,
    appPassword: process.env.MICROSOFT_APP_PASSWORD
});

// Add conversation state middleware
// The parameters are the connection string and table name.
// "UseDevelopmentStorage=true" is the connection string to use if you are using the Azure Storage Emulator.
// Replace it with your own connection string if you're not using the emulator
var azureStorage = new TableStorage({ tableName: "TestAzureTable1", storageAccountOrConnectionString: "UseDevelopmentStorage=true"})

// You can alternatively use your account name and table name
// var azureStorage = new TableStorage({tableName: "TestAzureTable2", storageAccessKey: "V3ah0go4DLkMtQKUPC6EbgFeXnE6GeA+veCwDNFNcdE6rqSVE/EQO/kjfemJaitPwtAkmR9lMKLtcvgPhzuxZg==", storageAccountOrConnectionString: "storageaccount"});

const conversationState = new ConversationState(azureStorage);
adapter.use(conversationState);

```

Sie können die Verbindungszeichenfolge `UseDevelopmentStorage=true` mit dem [Azure Storage-Emulator][AzureStorageEmulator] verwenden.
Wenn die Tabelle mit dem Namen, den Sie im Konstruktor für `AzureTableStorage` angeben, nicht existiert, wird diese erstellt.

---

Führen Sie das Beispiel aus, und öffnen Sie anschließend die Tabelle mit [Azure Storage-Explorer][AzureStorageExplorer], um die gespeicherten Daten für den Konversationszustand anzuzeigen.

![Konversationszustandsdaten von EchoBot in Azure Storage-Explorer](media/how-to-state/echostate-azure-storage-explorer.png)


Der **Partitionsschlüssel** ist ein eindeutig generierter Schlüssel, der spezifisch für die aktuelle Konversation gilt. Wenn Sie den Bot neu starten oder eine neue Konversation starten, wird eine neue Zeile mit einem eigenen Partitionsschlüssel für die neue Konversation erstellt. Die `EchoState`-Datenstruktur wird in eine JSON-Datei serialisiert und in der **JSON**-Spalte der Azure-Tabelle gespeichert. 

```json
{
    "$type":"Microsoft.Bot.Samples.Echo.AspNetCore.EchoState, Microsoft.Bot.Samples.EchoBot-AspNetCore",
    "TurnNumber":2,
    "LastMessage":"second message",
    "eTag":"*"
}
```

Die Felder `$type` und `eTag` werden vom BotBuilder SDK hinzugefügt. Weitere Informationen über eTags finden Sie unter [Managing optimistic concurrency (Verwalten der optimistischen Parallelität)](bot-builder-howto-v4-storage.md#manage-concurrency-using-etags)


## <a name="next-steps"></a>Nächste Schritte

Da Sie nun wissen, wie Sie den Zustand zum Lesen und Schreiben von Botdaten in den Speicher verwenden, sehen Sie sich an, wie Sie den Speicher direkt lesen und beschreiben können.

> [!div class="nextstepaction"]
> [How to write directly to storage (Direktes Schreiben in den Speicher)](bot-builder-howto-v4-storage.md)

## <a name="additional-resources"></a>Zusätzliche Ressourcen
Weitere Informationen zum Speicher finden Sie unter [Storage in the Bot Builder SDK (Speicher im Bot Builder SDK)](bot-builder-storage-concept.md)

<!-- Links -->
[AzureStorageEmulator]: https://docs.microsoft.com/en-us/azure/storage/common/storage-use-emulator
[AzureStorageExplorer]: https://azure.microsoft.com/en-us/features/storage-explorer/
