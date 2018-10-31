---
title: Verwalten des Konversations- und Benutzerzustands | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie mithilfe des Bot Builder SDK für .NET Zustandsdaten speichern und abrufen.
keywords: Konversationszustand, Benutzerzustand, Konversationsfluss
author: ivorb
ms.author: v-ivorb
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 09/18/18
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 972df2a12ffa7901ed4e4ecf14ce99233293c5a2
ms.sourcegitcommit: b78fe3d8dd604c4f7233740658a229e85b8535dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2018
ms.locfileid: "49997707"
---
# <a name="manage-conversation-and-user-state"></a>Verwalten des Konversations- und Benutzerzustands

[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

Wesentlich für gute Botentwicklung ist, den Unterhaltungskontext nachzuverfolgen, damit sich der Bot beispielsweise an Antworten auf bereits gestellte Fragen erinnert. Je nachdem, wofür Ihr Bot verwendet wird, müssen Sie möglicherweise auch den Status verfolgen oder Informationen über die Lebensdauer einer Unterhaltung hinaus speichern. Der *Status* eines Bots ist eine Information, die er sich merkt, um auf eingehende Nachrichten angemessen zu reagieren. Das Bot Builder SDK stellt zwei Klassen zum Speichern und Abrufen von Zustandsdaten in Form eines Objekts bereit, das einem Benutzer oder einer Konversation zugeordnet ist.

- Der **Konversationszustand** ermöglicht es Ihrem Bot, seine aktuelle Konversation mit dem Benutzer nachzuverfolgen. Wenn Ihr Bot eine Reihe von Schritten ausführen oder zwischen Unterhaltungsthemen wechseln muss, können Sie mit den Unterhaltungseigenschaften diese Schritte verwalten oder das aktuelle Thema nachverfolgen. 

- Der **Benutzerzustand** kann für viele Zwecke verwendet werden, beispielsweise um festzustellen, an welcher Stelle eine frühere Konversation unterbrochen wurde, oder um einen wiederkehrenden Benutzer mit seinem Namen zu begrüßen. Wenn Sie die Einstellungen eines Benutzers speichern, können Sie diese Informationen zum Anpassen der Konversation beim nächsten Chat verwenden. Sie können beispielsweise den Benutzer auf einen Nachrichtenartikel zu einem Thema, das ihn interessiert, aufmerksam machen oder einen Benutzer benachrichtigen, wenn ein Termin verfügbar wird. 

`ConversationState` und `UserState` sind Zustandsklassen, bei denen es sich um Spezialisierungen der `BotState`-Klasse handelt, die über Richtlinien zum Steuern der Lebensdauer und des Bereichs der in ihnen gespeicherten Objekte verfügen. Komponenten, die den Zustand speichern müssen, erstellen und registrieren eine Eigenschaft mit einem Typ und verwenden einen Eigenschaftenaccessor, um auf den Zustand zuzugreifen. Der Bot-Zustands-Manager kann Arbeitsspeicher, Cosmos DB und Blob Storage verwenden. 

> [!NOTE] 
> Sie können den Bot-Zustands-Manager zum Speichern von Einstellungen, des Benutzernamens oder der letzten Bestellung verwenden, er sollte aber nicht zum Speichern von kritischen Geschäftsdaten genutzt werden. Erstellen Sie für kritische Daten eigene Speicherkomponenten, oder schreiben Sie direkt in den [Speicher](bot-builder-howto-v4-storage.md).
> Der In-Memory-Datenspeicher dient nur zu Testzwecken. Dieser Speicher ist flüchtig und temporär. Die Daten werden jedes Mal gelöscht, wenn der Bot neu gestartet wird.

## <a name="using-conversation-state-and-user-state-to-direct-conversation-flow"></a>Verwenden des Konversations- und Benutzerzustands zum Steuern des Konversationsflusses
Beim Entwerfen eines Konversationsflusses ist es nützlich, ein Zustandsflag zu definieren, um den Konversationsfluss zu leiten. Bei diesem Flag kann es sich um ein einfaches boolesches Flag handeln oder um ein Flag, das den Namen des aktuellen Themas enthält. Mithilfe des Flags können Sie verfolgen, wo Sie sich in einer Konversation befinden. Ein boolesches Flag kann Ihnen beispielsweise mitteilen, ob gerade eine Konversation stattfindet, während eine Eigenschaft mit dem Namen des Themas Aufschluss über die Konversation geben kann, in der Sie sich derzeit befinden.



# <a name="ctabcsharp"></a>[C#](#tab/csharp)
### <a name="conversation-and-user-state"></a>Konversations- und Benutzerzustand
Sie können das [Beispiel „Echo Bot With Counter“](https://aka.ms/EchoBot-With-Counter) (Echobot mit Zähler) als Ausgangspunkt für diese exemplarische Vorgehensweise verwenden. Erstellen Sie zunächst wie unten dargestellt eine `TopicState`-Klasse zum Verwalten des aktuellen Themas der Konversation in `TopicState.cs`:

```csharp
public class TopicState
{
   public string Prompt { get; set; } = "askName";
}
``` 
Erstellen Sie anschließend in `UserProfile.cs` eine `UserProfile`-Klasse zum Verwalten des Benutzerzustands.
```csharp
public class UserProfile
{
    public string UserName { get; set; }
    public string TelephoneNumber { get; set; }
}
``` 
Die `TopicState`-Klasse verfügt über ein Flag zum Nachverfolgen der Konversation und verwendet den Konversationszustand, um diese Informationen zu speichern. Die Eingabeaufforderung wird als „askName“ initialisiert, um die Konversation zu starten. Sobald der Bot die Antwort des Benutzers empfängt, wird die Eingabeaufforderung als „askNumber“ neu definiert, um die nächste Konversation zu initiieren. Die `UserProfile`-Klasse verfolgt den Benutzernamen und die Telefonnummer nach und speichert diese Daten im Benutzerzustand.

### <a name="property-accessors"></a>Eigenschaftenaccessoren
Die `EchoBotAccessors`-Klasse in unserem Beispiel wird als Singleton erstellt und per Abhängigkeitsinjektion an den `class EchoWithCounterBot : IBot`-Konstruktor übergeben. Die `EchoBotAccessors`-Klasse enthält den `ConversationState`, den `UserState` und den zugehörigen `IStatePropertyAccessor`. Das `conversationState`-Objekt speichert den Themenzustand und das `userState`-Objekt, das die Profilinformationen des Benutzers speichert. Die `ConversationState`- und `UserState`-Objekte werden später in der Datei „Startup.cs“ erstellt. In den Konversations- und Benutzerzustandsobjekten werden alle Informationen im Konversations- und Benutzerbereich gespeichert. 

Der Konstruktor wird wie folgt aktualisiert, um `UserState` hinzuzufügen:
```csharp
using EchoBotWithCounter;

public EchoBotAccessors(ConversationState conversationState, UserState userState)
{
    ConversationState = conversationState ?? throw new ArgumentNullException(nameof(conversationState));
    UserState = userState ?? throw new ArgumentNullException(nameof(userState));
}
```
Erstellen Sie eindeutige Namen für die `TopicState`- und `UserProfile`-Accessoren.
```csharp
public static string UserProfileName { get; } = $"{nameof(EchoBotAccessors)}.UserProfile";
public static string TopicStateName { get; } = $"{nameof(EchoBotAccessors)}.TopicState";
```
Als Nächstes definieren Sie zwei Accessoren. Der erste Accessor speichert das Thema der Konversation, und der zweite Accessor speichert den Namen und die Telefonnummer des Benutzers.
```csharp
public IStatePropertyAccessor<TopicState> TopicState { get; set; }
public IStatePropertyAccessor<UserProfile> UserProfile { get; set; }
```

Die Eigenschaften zum Abrufen des Konversationszustands (ConversationState) sind bereits definiert, Sie müssen jedoch wie unten dargestellt `UserState` hinzufügen:
```csharp
public ConversationState ConversationState { get; }
public UserState UserState { get; }
```
Speichern Sie die Datei, nachdem Sie die Änderungen vorgenommen haben. Als Nächstes aktualisieren Sie die Startklasse, um ein `UserState`-Objekt zu erstellen, das alle Informationen im Benutzerbereich speichert. Das `ConversationState`-Objekt ist bereits vorhanden. 
```csharp

services.AddBot<EchoWithCounterBot>(options =>
{
    ...

    IStorage dataStore = new MemoryStorage();
    
    var conversationState = new ConversationState(dataStore);
    options.State.Add(conversationState);
        
    var userState = new UserState(dataStore);  
    options.State.Add(userState);
});
```
Mit den Zeilen `options.State.Add(ConversationState);` und `options.State.Add(userState);` werden der Konversationszustand bzw. der Benutzerzustand hinzugefügt. Als Nächstes erstellen und registrieren Sie Zustandsaccessoren. Accessoren, die hier erstellt werden, werden bei jeder Ausführung an die von IBot abgeleitete Klasse übergeben. Ändern Sie den Code wie unten dargestellt, um den Benutzerzustand einzuschließen:
```csharp
services.AddSingleton<EchoBotAccessors>(sp =>
{
   ...
    var userState = options.State.OfType<UserState>().FirstOrDefault();
    if (userState == null)
    {
        throw new InvalidOperationException("UserState must be defined and added before adding user-scoped state accessors.");
    }
   ...
 });
```

Erstellen Sie nun die beiden Accessoren mit `TopicState` und `UserProfile`, und übergeben Sie sie per Abhängigkeitsinjektion an die `class EchoWithCounterBot : IBot`-Klasse.
```csharp
services.AddSingleton<EchoBotAccessors>(sp =>
{
   ...
    var accessors = new EchoBotAccessors(conversationState, userState)
    {
        TopicState = conversationState.CreateProperty<TopicState>(EchoBotAccessors.TopicStateName),
        UserProfile = userState.CreateProperty<UserProfile>(EchoBotAccessors.UserProfileName),
     };

     return accessors;
 });
```

Der Konversationszustand und der Benutzerzustand sind über den `services.AddSingleton`-Codeblock mit einem Singleton verknüpft und werden über einen Zustandsspeicheraccessor in dem mit `var accessors = new EchoBotAccessor(conversationState, userState)` beginnenden Code gespeichert.

### <a name="use-conversation-and-user-state-properties"></a>Verwenden von Konversations- und Benutzerzustandseigenschaften 
Ändern Sie im `OnTurnAsync`-Handler der `EchoWithCounterBot : IBot`-Klasse den Code, um zur Eingabe des Benutzernamens und anschließend zur Eingabe der Telefonnummer aufzufordern. Um die aktuelle Konversation nachzuverfolgen, verwenden Sie die im „TopicState“ definierte Prompt-Eigenschaft. Diese Eigenschaft wurde als „askName“ initialisiert. Wenn Sie den Benutzernamen erhalten haben, legen Sie die Eigenschaft auf „askNumber“ und „UserName“ auf den vom Benutzer eingegebenen Namen fest. Nach dem Empfang der Telefonnummer senden Sie eine Bestätigungsnachricht, und zudem legen Sie die Eingabeaufforderung auf „confirmation“ fest, da Sie das Ende der Konversation erreicht haben.

```csharp
using EchoBotWithCounter;

if (turnContext.Activity.Type == ActivityTypes.Message)
{
    // Get the conversation state from the turn context.
    var convo = await _accessors.TopicState.GetAsync(turnContext, () => new TopicState());
    
    // Get the user state from the turn context.
    var user = await _accessors.UserProfile.GetAsync(turnContext, () => new UserProfile());
    
    // Ask user name. The Prompt was initialiazed as "askName" in the TopicState.cs file.
    if (convo.Prompt == "askName")
    {
        await turnContext.SendActivityAsync("What is your name?");
        
        // Set the Prompt to ask the next question for this conversation
        convo.Prompt = "askNumber";
        
        // Set the property using the accessor
        await _accessors.TopicState.SetAsync(turnContext, convo);
        
        //Save the new prompt into the conversation state.
        await _accessors.ConversationState.SaveChangesAsync(turnContext);
    }
    else if (convo.Prompt == "askNumber")
    {
        // Set the UserName that is defined in the UserProfile class
        user.UserName = turnContext.Activity.Text;
        
        // Use the user name to prompt the user for phone number
        await turnContext.SendActivityAsync($"Hello, {user.UserName}. What's your telephone number?");
        
        // Set the Prompt now that we have collected all the data
        convo.Prompt = "confirmation";
                 
        await _accessors.TopicState.SetAsync(turnContext, convo);
        await _accessors.ConversationState.SaveChangesAsync(turnContext);

        await _accessors.UserProfile.SetAsync(turnContext, user);
        await _accessors.UserState.SaveChangesAsync(turnContext);
    }
    else if (convo.Prompt == "confirmation")
    { 
        // Set the TelephoneNumber that is defined in the UserProfile class
        user.TelephoneNumber = turnContext.Activity.Text;

        await turnContext.SendActivityAsync($"Got it, {user.UserName}. I'll call you later.");

        // reset initial prompt state
        convo.Prompt = "askName"; // Reset for a new conversation.
        
        await _accessors.TopicState.SetAsync(turnContext, convo);
        await _accessors.ConversationState.SaveChangesAsync(turnContext);
    }
}
```   

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)

### <a name="conversation-and-user-state"></a>Konversations- und Benutzerzustand

Sie können das [Beispiel „Echo Bot With Counter“](https://aka.ms/EchoBot-With-Counter-JS) (Echobot mit Zähler) als Ausgangspunkt für diese exemplarische Vorgehensweise verwenden. In diesem Beispiel wird der `ConversationState` bereits zum Speichern der Nachrichtenanzahl verwendet. Sie müssen ein `TopicStates`-Objekt zum Nachverfolgen des Konversationszustands und `UserState` zum Nachverfolgen von Benutzerinformationen in einem `userProfile`-Objekt hinzufügen. 

Fügen Sie den `UserState` in der Datei `index.js` des Hauptbots der „require“-Liste hinzu:

**index.js**

```javascript
// Import required bot services. See https://aka.ms/bot-services to learn more about the different parts of a bot.
const { BotFrameworkAdapter, MemoryStorage, ConversationState, UserState } = require('botbuilder');
```

Erstellen Sie als Nächstes den `UserState` mit `MemoryStorage` als Speicheranbieter, und übergeben Sie ihn dann als zweites Argument an die `MainDialog`-Klasse.

**index.js**

```javascript
// Create conversation state with in-memory storage provider. 
const conversationState = new ConversationState(memoryStorage);
const userState = new UserState(memoryStorage);
// Create the main bot.
const bot = new EchBot(conversationState, userState);
```

Aktualisieren Sie in Ihrer Datei `bot.js` den Konstruktor, sodass er den `userState` als zweites Argument akzeptiert. Erstellen Sie dann eine `topicState`-Eigenschaft aus dem `conversationState` und eine `userProfile`-Eigenschaft aus dem `userState`.

**bot.js**

```javascript
const TOPIC_STATE = 'topic';
const USER_PROFILE = 'user';

constructor (conversationState, userState) {
    // creates a new state accessor property.see https://aka.ms/about-bot-state-accessors to learn more about the bot state and state accessors 
    this.conversationState = conversationState;
    this.topicState = this.conversationState.createProperty(TOPIC_STATE);

    // User state
    this.userState = userState;
    this.userProfile = this.userState.createProperty(USER_PROFILE);
}
```

### <a name="use-conversation-and-user-state-properties"></a>Verwenden von Konversations- und Benutzerzustandseigenschaften

Ändern Sie im `onTurn`-Handler der `MainDialog`-Klasse den Code, um zur Eingabe des Benutzernamens und anschließend zur Eingabe der Telefonnummer aufzufordern. Um die aktuelle Konversation nachzuverfolgen, verwenden Sie die im `topicState` definierte `prompt`-Eigenschaft. Diese Eigenschaft wird als „askName“ initialisiert. Wenn Sie den Benutzernamen erhalten haben, legen Sie die Eigenschaft auf „askNumber“ und „UserName“ auf den vom Benutzer eingegebenen Namen fest. Nach dem Empfang der Telefonnummer senden Sie eine Bestätigungsnachricht, und zudem legen Sie die Eingabeaufforderung auf `undefined` fest, da Sie das Ende der Konversation erreicht haben.

**dialogs/mainDialog/index.js**

```javascript
// see https://aka.ms/about-bot-activity-message to learn more about the message and other activity types
if (turnContext.activity.type === 'message') {
    // read from state and set default object if object does not exist in storage.
    let topicState = await this.topicState.get(turnContext, {
        //Define the topic state object
        prompt: "askName"
    });
    let userProfile = await this.userProfile.get(turnContext, {  
        // Define the user's profile object
        "userName": "",
        "telephoneNumber": ""
    });

    if(topicState.prompt == "askName"){
        await turnContext.sendActivity("What is your name?");

        // Set next prompt state
        topicState.prompt = "askNumber";

        // Update state
        await this.topicState.set(turnContext, topicState);
    }
    else if(topicState.prompt == "askNumber"){
        // Set the UserName that is defined in the UserProfile class
        userProfile.userName = turnContext.activity.text;

        // Use the user name to prompt the user for phone number
        await turnContext.sendActivity(`Hello, ${userProfile.userName}. What's your telephone number?`);

        // Set next prompt state
        topicState.prompt = "confirmation";

        // Update states
        await this.topicState.set(turnContext, topicState);
        await this.userProfile.set(turnContext, userProfile);
    }
    else if(topicState.prompt == "confirmation"){
        // Set the phone number
        userProfile.telephoneNumber = turnContext.activity.text;

        // Sent confirmation
        await turnContext.sendActivity(`Got it, ${userProfile.userName}. I'll call you later.`)

        // reset initial prompt state
        topicState.prompt = "askName"; // Reset for a new conversation

        // Update states
        await this.topicState.set(turnContext, topicState);
        await this.userProfile.set(turnContext, userProfile);
    }
    
    // Save state changes to storage
    await this.conversationState.saveChanges(turnContext);
    await this.userState.saveChanges(turnContext);
    
}
else {
    await turnContext.sendActivity(`[${context.activity.type} event detected]`);
}
```

---

## <a name="start-your-bot"></a>Starten Ihres Bots
Führen Sie Ihren Bot lokal aus.

### <a name="start-the-emulator-and-connect-your-bot"></a>Starten des Emulators und Herstellen einer Verbindung mit Ihrem Bot
Starten Sie im nächste Schritt den Emulator, und stellen Sie dann im Emulator eine Verbindung mit Ihrem Bot her:

1. Klicken Sie im Emulator auf der Registerkarte „Willkommen“ auf den Link **Bot öffnen**. 
2. Wählen Sie in dem Verzeichnis, in dem Sie die Visual Studio-Projektmappe erstellt haben, die BOT-Datei aus.

### <a name="interact-with-your-bot"></a>Interagieren mit Ihrem Bot

Senden Sie eine Nachricht an Ihren Bot. Dieser antwortet daraufhin mit einer Nachricht.
![Ausgeführter Emulator](../media/emulator-v4/emulator-running.png)

Wenn Sie den Zustand selbst verwalten möchten, lesen Sie das Thema zum [Verwalten des Konversationsflusses mit eigenen Eingabeaufforderungen](bot-builder-primitive-prompts.md). Alternativ können Sie den Wasserfalldialog verwenden. Das Dialogfeld verfolgt den Konversationszustand für Sie, weshalb Sie keine Flags erstellen müssen, um den Zustand nachzuverfolgen. Weitere Informationen finden Sie unter [Verwalten eines einfachen Konversationsflusses mit Dialogen](bot-builder-dialog-manage-conversation-flow.md).

## <a name="next-steps"></a>Nächste Schritte
Da Sie nun wissen, wie Sie den Zustand zum Lesen und Schreiben von Botdaten in den Speicher verwenden, sehen Sie sich an, wie Sie den Speicher direkt lesen und beschreiben können.

> [!div class="nextstepaction"]
> [How to write directly to storage (Direktes Schreiben in den Speicher)](bot-builder-howto-v4-storage.md)
