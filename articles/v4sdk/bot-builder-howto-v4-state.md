---
title: Speichern von Benutzer- und Konversationsdaten | Microsoft-Dokumentation
description: Es wird beschrieben, wie Sie mit dem Bot Builder SDK Zustandsdaten speichern und abrufen.
keywords: Konversationsstatus, Benutzerstatus, Konversation, Speichern des Zustands, Verwalten des Bot-Zustands
author: ivorb
ms.author: v-ivorb
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 11/26/18
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 8f979aed3bc1c4bb4c74629bcffb258e139ce77d
ms.sourcegitcommit: bcde20bd4ab830d749cb835c2edb35659324d926
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/27/2018
ms.locfileid: "52338553"
---
# <a name="save-user-and-conversation-data"></a>Speichern von Benutzer- und Konversationsdaten

[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

Ein Bot ist grundsätzlich zustandslos. Nachdem Ihr Bot bereitgestellt wurde, kann es sein, dass er für die einzelnen Durchläufe nicht als Teil desselben Prozesses oder auf demselben Computer ausgeführt wird. Unter Umständen muss Ihr Bot aber den Kontext einer Konversation nachverfolgen, damit er das Verhalten verwalten und sich Antworten auf vorherige Fragen merken kann. Mit den Zustands- und Speicherfeatures des SDK können Sie Ihrem Bot den Zustand hinzufügen.

## <a name="prerequisites"></a>Voraussetzungen

- Sie müssen mit den [Bot-Grundlagen](bot-builder-basics.md) und der [Verwaltung des Zustands](bot-builder-concept-state.md) durch Bots vertraut sein.
- Der Code in diesem Artikel basiert auf dem Beispiel **StateBot**. Sie benötigen eine Kopie des Beispiels im [C#](https://github.com/Microsoft/BotFramework-Samples/tree/master/SDKV4-Samples/dotnet_core/StateBot)- oder [JS]()-Format.
- Einen [Bot Framework Emulator](https://aka.ms/Emulator-wiki-getting-started) zum lokalen Testen des Bots.

## <a name="about-the-sample-code"></a>Informationen zum Beispielcode

In diesem Artikel werden die Konfigurationsaspekte zur Verwaltung des Zustands eines Bots beschrieben. Zum Hinzufügen des Zustands konfigurieren wir die Zustandseigenschaften, die Zustandsverwaltung und den Speicher und verwenden diese Komponenten dann im Bot.

- Jede _Zustandseigenschaft_ enthält Zustandsinformationen für Ihren Bot.
- Mit jedem Zustandseigenschaftenaccessor können Sie den Wert der zugeordneten Zustandseigenschaft abrufen oder festlegen.
- Mit jedem Objekt für die Zustandsverwaltung wird das Lesen und Schreiben der zugehörigen Zustandsinformationen in den Speicher automatisiert.
- Für die Speicherebene wird eine Verbindung mit dem Hintergrundspeicher für den Zustand hergestellt, z.B. In-Memory (für Tests) oder Azure Cosmos DB-Speicher (für die Produktion).

Wir müssen den Bot mit Zustandseigenschaftenaccessoren konfigurieren, mit deren Hilfe er den Zustand zur Laufzeit abrufen und festlegen kann, wenn eine Aktivität verarbeitet wird. Ein Zustandseigenschaftenaccessor wird mit einem Objekt für die Zustandsverwaltung erstellt, und ein Objekt für die Zustandsverwaltung wird mit einer Speicherebene erstellt. Wir beginnen also auf der Speicherebene und arbeiten uns von dort aus hoch.

## <a name="configure-storage"></a>Konfigurieren des Speichers

Da wir nicht planen, diesen Bot bereitzustellen, nutzen wir _Arbeitsspeicher_. Wir verwenden ihn im nächsten Schritt, um den Benutzer- und Konversationszustand zu konfigurieren.

### <a name="ctabcsharp"></a>[C#](#tab/csharp)

Konfigurieren Sie auf der Speicherebene die Datei **Startup.cs**.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ...
    IStorage storage = new MemoryStorage();
    // ...
}
```

### <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Konfigurieren Sie in Ihrer Datei **index.js** die Speicherebene.

```javascript
// Define state store for your bot.
const memoryStorage = new MemoryStorage();
```

---

## <a name="create-state-management-objects"></a>Erstellen von Objekten für die Zustandsverwaltung

Wir verfolgen sowohl den Zustand für den _Benutzer_ als auch für die _Konversation_ und nutzen diese Informationen, um im nächsten Schritt _Zustandseigenschaftenaccessoren_ zu erstellen.

### <a name="ctabcsharp"></a>[C#](#tab/csharp)

Verweisen Sie in **Startup.cs** auf die Speicherebene, wenn Sie Ihre Objekte für die Zustandsverwaltung erstellen.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ...
    ConversationState conversationState = new ConversationState(storage);
    UserState userState = new UserState(storage);
    // ...
}
```

### <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Fügen Sie in Ihrer Datei **index.js** der require-Anweisung das Element `UserState` hinzu.

```javascript
const { BotFrameworkAdapter, MemoryStorage, ConversationState, UserState } = require('botbuilder');
```

Verweisen Sie anschließend auf die Speicherebene, wenn Sie Ihre Objekte für die Zustandsverwaltung für die Konversation und den Benutzer erstellen.

```javascript
// Create conversation and user state with in-memory storage provider.
const conversationState = new ConversationState(memoryStorage);
const userState = new UserState(memoryStorage);
```

---

## <a name="create-state-property-accessors"></a>Erstellen von Zustandseigenschaftenaccessoren

Erstellen Sie zum _Deklarieren_ einer Zustandseigenschaft zuerst einen Zustandseigenschaftenaccessor, indem Sie eines unserer Objekte für die Zustandsverwaltung verwenden. Wir konfigurieren den Bot für die Nachverfolgung der folgenden Informationen:

- Benutzername, den wir in unserem Benutzerzustand definieren.
- Ob wir den Benutzer gerade nach seinem Namen und weiteren Informationen zur gerade gesendeten Nachricht gefragt haben.

Der Bot nutzt den Accessor zum Abrufen der Zustandseigenschaft aus dem Turn-Kontext.

### <a name="ctabcsharp"></a>[C#](#tab/csharp)

Zuerst definieren wir Klassen für alle Informationen, die wir in den einzelnen Zustandstypen verwalten möchten.

- Eine `UserProfile`-Klasse für die Benutzerinformationen, die vom Bot gesammelt werden.
- Eine `ConversationData`-Klasse zum Nachverfolgen der Informationen, wann eine Nachricht eintrifft und von wem sie stammt.

```csharp
// Defines a state property used to track information about the user.
public class UserProfile
{
    public string Name { get; set; }
}
```

```csharp
// Defines a state property used to track conversation data.
public class ConversationData
{
    // The time-stamp of the most recent incoming message.
    public string Timestamp { get; set; }

    // The ID of the user's channel.
    public string ChannelId { get; set; }

    // Track whether we have already asked the user's name
    public bool PromptedUserForName { get; set; } = false;
}
```

Als Nächstes definieren wir eine Klasse mit den Informationen zur Zustandsverwaltung, die wir zum Konfigurieren der Bot-Instanz benötigen.

```csharp
public class StateBotAccessors
{
    public StateBotAccessors(ConversationState conversationState, UserState userState)
    {
        ConversationState = conversationState ?? throw new ArgumentNullException(nameof(conversationState));
        UserState = userState ?? throw new ArgumentNullException(nameof(userState));
    }
  
    public static string UserProfileName { get; } = "UserProfile";

    public static string ConversationDataName { get; } = "ConversationData";

    public IStatePropertyAccessor<UserProfile> UserProfileAccessor { get; set; }

    public IStatePropertyAccessor<ConversationData> ConversationDataAccessor { get; set; }
  
    public ConversationState ConversationState { get; }
  
    public UserState UserState { get; }
}
```

### <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Wir übergeben die Objekte für die Zustandsverwaltung direkt an den Konstruktor des Bots und lassen den Bot die Zustandseigenschaftenaccessoren selbst erstellen.

Geben Sie in **index.js** die Objekte für die Zustandsverwaltung an, wenn Sie den Bot erstellen.

```javascript
// Create the bot.
const myBot = new MyBot(conversationState, userState);
```

Definieren Sie in **bot.js** die Bezeichner, die Sie zum Verwalten und Nachverfolgen des Zustands benötigen.

```javascript
// The accessor names for the conversation data and user profile state property accessors.
const CONVERSATION_DATA_PROPERTY = 'conversationData';
const USER_PROFILE_PROPERTY = 'userProfile';
```

---

## <a name="configure-your-bot"></a>Konfigurieren Ihres Bots

Jetzt ist alles zum Definieren der Zustandseigenschaftenaccessoren und Konfigurieren des Bots bereit.
Wir verwenden das Objekt zum Verwalten des Konversationszustands für den Konversationsfluss-Zustandseigenschaftenaccessor.
Wir verwenden das Objekt zum Verwalten des Benutzerzustands für den Benutzerprofil-Zustandseigenschaftenaccessor.

### <a name="ctabcsharp"></a>[C#](#tab/csharp)

In **Startup.cs** konfigurieren wir ASP.NET, um die gebündelte Zustandseigenschaft und die Objekte für die Verwaltung bereitzustellen. Diese Informationen werden aus dem Konstruktor des Bots über das Framework für die Abhängigkeitsinjektion (Dependency Injection) in ASP.NET Core abgerufen.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ...
    services.AddSingleton<StateBotAccessors>(sp =>
    {
        // Create the custom state accessor.
        return new StateBotAccessors(conversationState, userState)
        {
            ConversationDataAccessor = conversationState.CreateProperty<ConversationData>(StateBotAccessors.ConversationDataName),
            UserProfileAccessor = userState.CreateProperty<UserProfile>(StateBotAccessors.UserProfileName),
        };
    });
}
```

Im Konstruktor des Bots wird das `CustomPromptBotAccessors`-Objekt bereitgestellt, wenn ASP.NET den Bot erstellt.

```csharp
// Defines a bot for filling a user profile.
public class CustomPromptBot : IBot
{
    private readonly StateBotAccessors _accessors;

    public StateBot(StateBotAccessors accessors, ILoggerFactory loggerFactory)
    {
        // ...
        accessors = accessors ?? throw new System.ArgumentNullException(nameof(accessors));
    }

    // The bot's turn handler and other supporting code...
}
```

### <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Im Konstruktor des Bots (in der Datei **bot.js**) erstellen wir die Zustandseigenschaftenaccessoren und fügen sie dem Bot hinzu. Außerdem fügen wir Verweise auf die Objekte für die Zustandsverwaltung hinzu, da wir sie benötigen, um alle vorgenommenen Änderungen zu speichern.

```javascript
constructor(conversationState, userState) {
    // Create the state property accessors for the conversation data and user profile.
    this.conversationData = conversationState.createProperty(CONVERSATION_DATA_PROPERTY);
    this.userProfile = userState.createProperty(USER_PROFILE_PROPERTY);

    // The state management objects for the conversation and user state.
    this.conversationState = conversationState;
    this.userState = userState;
}
```

---

## <a name="access-state-from-your-bot"></a>Zugreifen auf den Zustand von Ihrem Bot aus

In den vorherigen Abschnitten wurden die Initialisierungsschritte zum Hinzufügen der Zustandseigenschaftenaccessoren zu unserem Bot behandelt.
Jetzt können wir diese Accessoren zur Laufzeit verwenden, um Zustandsinformationen zu lesen und zu schreiben.

1. Bevor wir unsere Zustandseigenschaften verwenden, nutzen wir jeden Accessor, um die Eigenschaft aus dem Speicher zu laden und aus dem Zustandscache abzurufen.
   - Bei jedem Abrufen einer Zustandseigenschaft über ihren Accessor sollten Sie einen Standardwert angeben. Andernfalls erhalten Sie ggf. einen Fehler aufgrund eines NULL-Werts.
1. Vor dem Beenden des Turn-Handlers:
   1. Wir verwenden die _set_-Methode des Accessors, um Änderungen für den Bot-Zustand zu übertragen.
   1. Wir verwenden die _save changes_-Methode des Objekts für die Zustandsverwaltung, um diese Änderungen in den Speicher zu schreiben.

### <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
// The bot's turn handler.
public async Task OnTurnAsync(ITurnContext turnContext, CancellationToken cancellationToken = default(CancellationToken))
{
    if (turnContext.Activity.Type == ActivityTypes.Message)
    {
        // Get the state properties from the turn context.
        UserProfile userProfile =
            await _accessors.UserProfileAccessor.GetAsync(turnContext, () => new UserProfile());
        ConversationData conversationData =
            await _accessors.ConversationDataAccessor.GetAsync(turnContext, () => new ConversationData());

        if (string.IsNullOrEmpty(userProfile.Name))
        {
            // First time around this is set to false, so we will prompt user for name.
            if (conversationData.PromptedUserForName)
            {
                // Set the name to what the user provided
                userProfile.Name = turnContext.Activity.Text?.Trim();

                // Acknowledge that we got their name.
                await turnContext.SendActivityAsync($"Thanks {userProfile.Name}.");

                // Reset the flag to allow the bot to go though the cycle again.
                conversationData.PromptedUserForName = false;
            }
            else
            {
                // Prompt the user for their name.
                await turnContext.SendActivityAsync($"What is your name?");

                // Set the flag to true, so we don't prompt in the next turn.
                conversationData.PromptedUserForName = true;
            }

            // Save user state and save changes.
            await _accessors.UserProfileAccessor.SetAsync(turnContext, userProfile);
            await _accessors.UserState.SaveChangesAsync(turnContext);
        }
        else
        {
            // Add message details to the conversation data.
            conversationData.Timestamp = turnContext.Activity.Timestamp.ToString();
            conversationData.ChannelId = turnContext.Activity.ChannelId.ToString();

            // Display state data
            await turnContext.SendActivityAsync($"{userProfile.Name} sent: {turnContext.Activity.Text}");
            await turnContext.SendActivityAsync($"Message received at: {conversationData.Timestamp}");
            await turnContext.SendActivityAsync($"Message received from: {conversationData.ChannelId}");
        }

        // Update conversation state and save changes.
        await _accessors.ConversationDataAccessor.SetAsync(turnContext, conversationData);
        await _accessors.ConversationState.SaveChangesAsync(turnContext);
    }
}
```

### <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
// The bot's turn handler.
async onTurn(turnContext) {
    if (turnContext.activity.type === ActivityTypes.Message) {
        // Get the state properties from the turn context.
        const userProfile = await this.userProfile.get(turnContext, {});
        const conversationData = await this.conversationData.get(
            turnContext, { promptedForUserName: false });

        if (!userProfile.name) {
            // First time around this is undefined, so we will prompt user for name.
            if (conversationData.promptedForUserName) {
                // Set the name to what the user provided.
                userProfile.name = turnContext.activity.text;

                // Acknowledge that we got their name.
                await turnContext.sendActivity(`Thanks ${userProfile.name}.`);

                // Reset the flag to allow the bot to go though the cycle again.
                conversationData.promptedForUserName = false;
            } else {
                // Prompt the user for their name.
                await turnContext.sendActivity('What is your name?');

                // Set the flag to true, so we don't prompt in the next turn.
                conversationData.promptedForUserName = true;
            }
            // Save user state and save changes.
            await this.userProfile.set(turnContext, userProfile);
            await this.userState.saveChanges(turnContext);
        } else {
            // Add message details to the conversation data.
            conversationData.timestamp = turnContext.activity.timestamp.toLocaleString();
            conversationData.channelId = turnContext.activity.channelId;

            // Display state data.
            await turnContext.sendActivity(`${userProfile.name} sent: ${turnContext.activity.text}`);
            await turnContext.sendActivity(`Message received at: ${conversationData.timestamp}`);
            await turnContext.sendActivity(`Message received from: ${conversationData.channelId}`);
        }
        // Update conversation state and save changes.
        await this.conversationData.set(turnContext, conversationData);
        await this.conversationState.saveChanges(turnContext);
    }
}
```

---

## <a name="test-the-bot"></a>Testen des Bots

1. Führen Sie das Beispiel lokal auf Ihrem Computer aus. Wenn Sie eine Anleitung benötigen, helfen Ihnen die INFODATEIEN zum [C#](https://github.com/Microsoft/BotFramework-Samples/tree/master/SDKV4-Samples/dotnet_core/StateBot)- bzw. [JS](https://github.com/Microsoft/BotFramework-Samples/tree/master/SDKV4-Samples/js/stateBot)-Beispiel weiter.
1. Verwenden Sie den Emulator, um den Bot wie unten dargestellt zu testen.

![Testen des Beispiels „state bot“](media/state-bot-testing-emulator.png)

## <a name="additional-resources"></a>Zusätzliche Ressourcen

**Datenschutz:** Wenn Sie die persönlichen Daten von Benutzern speichern möchten, sollten Sie sicherstellen, dass die Anforderungen der [Datenschutz-Grundverordnung](https://blog.botframework.com/2018/04/23/general-data-protection-regulation-gdpr) erfüllt sind.

**Zustandsverwaltung:** Alle Aufrufe der Zustandsverwaltung sind asynchron, und standardmäßig gilt, dass „der letzte Schreibvorgang gewinnt“ (last-writer-wins). In der Praxis sollten Sie das Abrufen, Festlegen und Speichern des Zustands in Ihrem Bot möglichst nah beieinander anordnen.

**Kritische Geschäftsdaten:** Verwenden Sie den Bot-Zustand zum Speichern von Einstellungen, des Benutzernamens oder der letzten Bestellung, aber nicht zum Speichern von kritischen Geschäftsdaten. [Erstellen Sie für kritische Daten eigene Speicherkomponenten](bot-builder-custom-storage.md), oder schreiben Sie direkt in den [Speicher](bot-builder-howto-v4-storage.md).

**Recognizer-Text:** In diesem Beispiel werden die Microsoft/Recognizers-Text-Bibliotheken genutzt, um Benutzereingaben zu analysieren und zu überprüfen. Weitere Informationen finden Sie auf der Seite mit der [Übersicht](https://github.com/Microsoft/Recognizers-Text#microsoft-recognizers-text-overview).

## <a name="next-step"></a>Nächster Schritt

Nachdem Sie nun wissen, wie Sie den Zustand konfigurieren, um Bot-Daten im Speicher zu lesen und zu schreiben, können Sie sich über Folgendes informieren: Stellen einer Reihe von Fragen an Benutzer, Überprüfen der Antworten und Speichern der Eingaben.

> [!div class="nextstepaction"]
> [Erstellen eigener Eingabeaufforderungen zum Erfassen von Benutzereingaben](bot-builder-primitive-prompts.md)
