---
title: Verwalten eines Konversationsablaufs mit einfachen Eingabeaufforderungen | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie einen Konversationsablauf mit einfachen Eingabeaufforderungen im Bot Builder SDK verwalten.
keywords: Konversationsablauf, Eingabeaufforderungen
author: v-ducvo
ms.author: v-ducvo
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 7/20/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 3014e6cd8b18ab44ff343373a034c392e44bca1d
ms.sourcegitcommit: 3bf3dbb1a440b3d83e58499c6a2ac116fe04b2f6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/23/2018
ms.locfileid: "46707366"
---
# <a name="prompt-users-for-input-using-your-own-prompts"></a>Verwenden eigener Eingabeaufforderungen für Benutzer

[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

Eine Konversation zwischen einem Bot und einem Benutzer umfasst in der Regel die Anforderung von Benutzerinformationen, die Analyse der Benutzerantwort und eine entsprechende Reaktion auf die Antwort. Im Thema [Auffordern von Benutzern zur Eingabe](bot-builder-prompts.md) wird erläutert, wie Sie Benutzer mithilfe der Bibliothek **Dialogs** zu einer Eingabe auffordern. Die Bibliothek **Dialogs** verfolgt unter anderem die aktuelle Konversation und die aktuell gestellte Frage nach. Darüber hinaus stellt sie Methoden zum Anfordern und Überprüfen verschiedener Arten von Informationen wie Text, Zahlen oder Datums-/Uhrzeitangaben bereit.

In bestimmten Situationen ist die integrierte Bibliothek **Dialogs** unter Umständen nicht die richtige Lösung für Ihren Bot. Für einfache Bots ist **Dialogs** möglicherweise zu aufwendig, nicht flexibel genug oder nicht für das geeignet, was Sie mit dem Bot erreichen möchten. In diesen Fällen können Sie die Bibliothek weglassen und Ihre eigene Aufforderungslogik implementieren. In diesem Thema erfahren Sie, wie Sie einen einfachen *Echobot* einrichten und eine Konversation mit eigenen Eingabeaufforderungen verwalten.

## <a name="track-prompt-states"></a>Nachverfolgen von Eingabeaufforderungszuständen

In einer durch Eingabeaufforderungen gesteuerten Konversation müssen Sie die aktuelle Position in der Konversation sowie die aktuell gestellte Frage nachverfolgen. Dazu müssen im Code mehrere Flags verwaltet werden.

Sie können beispielsweise einige Eigenschaften erstellen, die Sie nachverfolgen möchten.

Mithilfe dieser Zustände wird nachverfolgt, bei welchem Thema und welcher Eingabeaufforderung wir uns aktuell befinden. Damit diese Flags nach der Bereitstellung in der Cloud erwartungsgemäß funktionieren, werden sie im [Konversationszustand](bot-builder-howto-v4-state.md) gespeichert. 

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Wir erstellen zwei Klassen, um den Zustand nachzuverfolgen. **TopicState** zum Nachverfolgen des Status der Konversationseingabeaufforderungen und **UserProfile** zum Nachverfolgen der vom Benutzer bereitgestellten Informationen. Wir speichern diese Informationen in einem späteren Schritt in unserem [Botzustand](bot-builder-howto-v4-state.md).

```csharp
/// <summary>
/// Contains conversation state information about the conversation in progress.
/// </summary>
public class TopicState
{
    /// <summary>
    /// Identifies the current "topic" of conversation.
    /// </summary>
    public string Topic { get; set; }

    /// <summary>
    /// Indicates whether we asked the user a question last turn, and
    /// if so, what it was.
    /// </summary>
    public string Prompt { get; set; }
}
```

```csharp
/// <summary>
/// Contains user state information for the user's profile.
/// </summary>
public class UserProfile
{
    public string UserName { get; set; }

    public int? Age { get; set; }

    public string WorkPlace { get; set; }
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

**app.js**

```javascript
const storage = new MemoryStorage(); // Volatile memory
const conversationState = new ConversationState(storage);
const userState = new UserState(storage);
const dialogState = conversationState.createProperty('dialogState');
const userProfile = userState.createProperty('userProfile');
```

Platzieren Sie den folgenden Code in der Hauptlogik Ihres Bots:

```javascript
// Pull the state of the dialog out of the conversation state manager.
const convo = await dialogState.get(context, {
    prompt: undefined,
    topic: 'profile'
});

// Pull the user profile out of the user state manager.
const userProfile = await userProfile.get(context, {  // Define the user's profile object
        "userName": undefined,
        "age": undefined,
        "workPlace": undefined
    }
);
```

---

## <a name="manage-a-topic-of-conversation"></a>Verwalten eines Konversationsthemas

In einer sequenziellen Konversation, bei der beispielsweise Informationen von einem Benutzer erfasst werden, müssen Sie wissen, wann der Benutzer die Konversation begonnen hat und wo er sich in der Konversation befindet. Sie können dies im zentralen Durchlaufhandler des Bots nachverfolgen, indem Sie die oben definierten Eingabeaufforderungsflags festlegen und überprüfen und anschließend entsprechend reagieren. Das folgende Beispiel zeigt, wie Sie unter Verwendung dieser Flags im Rahmen der Konversation die Profilinformationen des Benutzers ermitteln.

Der Botcode ist hier dargestellt und wird im nächsten Abschnitt beschrieben.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Für ASP.NET Core müssen wir zuerst unseren Bot und die Abhängigkeitsinjektion einrichten.

Benennen Sie die Datei **EchoWithCounterBot.cs** in **PrimitivePromptsBot.cs** um, und aktualisieren Sie den Klassennamen. Diese Klasse enthält unsere Botlogik, und wir kehren in Kürze zurück, um die Aktualisierung durchzuführen.

Benennen Sie die Datei **EchoBotAccessors.cs** in **BotAccessors.cs** um, und aktualisieren Sie den Klassennamen. Diese Klasse enthält die Objekte für die Zustandsverwaltung und die Accessoren für die Zustandseigenschaft für unseren Bot. Aktualisieren Sie die Definition wie folgt.

```csharp
using Microsoft.Bot.Builder;
using System;

/// <summary>
/// Contains the state and state property accessors for the primitive prompts bot.
/// </summary>
public class BotAccessors
{
    public const string TopicStateName = "PrimitivePrompts.TopicStateAccessor";

    public const string UserProfileName = "PrimitivePrompts.UserProfileAccessor";

    public ConversationState ConversationState { get; }

    public UserState UserState { get; }

    public IStatePropertyAccessor<TopicState> TopicStateAccessor { get; set; }

    public IStatePropertyAccessor<UserProfile> UserProfileAccessor { get; set; }

    public BotAccessors(ConversationState conversationState, UserState userState)
    {
        if (conversationState is null)
        {
            throw new ArgumentNullException(nameof(conversationState));
        }

        if (userState is null)
        {
            throw new ArgumentNullException(nameof(userState));
        }

        this.ConversationState = conversationState;
        this.UserState = userState;
    }
}
```

Aktualisieren Sie die `ConfigureServices`-Methode der Datei **Startup.cs**, indem Sie an der Stelle beginnen, an der Sie das `IStorage`-Objekt eingerichtet haben.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddBot<PrimitivePromptsBot>(options =>
    {
        // ...

        // The Memory Storage used here is for local bot debugging only. When the bot
        // is restarted, everything stored in memory will be gone.
        IStorage dataStore = new MemoryStorage();

        var conversationState = new ConversationState(dataStore);
        options.State.Add(conversationState);

        var userState = new UserState(dataStore);
        options.State.Add(userState);
    });

    // Create and register state accessors.
    // Accessors created here are passed into the IBot-derived class on every turn.
    services.AddSingleton<BotAccessors>(sp =>
    {
        var options = sp.GetRequiredService<IOptions<BotFrameworkOptions>>().Value;
        var conversationState = options.State.OfType<ConversationState>().FirstOrDefault();
        var userState = options.State.OfType<UserState>().FirstOrDefault();

        // Create the custom state accessor.
        // State accessors enable other components to read and write individual properties of state.
        var accessors = new BotAccessors(conversationState, userState)
        {
            TopicStateAccessor = conversationState.CreateProperty<TopicState>(BotAccessors.TopicStateName),
            UserProfileAccessor = userState.CreateProperty<UserProfile>(BotAccessors.UserProfileName),
        };

        return accessors;
    });
}
```

Aktualisieren Sie abschließend die Botlogik in der Datei **PrimitivePromptsBot.cs**.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.Bot.Builder;
using Microsoft.Bot.Schema;

public class PrimitivePromptsBot : IBot
{
    public const string ProfileTopic = "profile";

    /// <summary>
    /// Describes a field in the user profile.
    /// </summary>
    private class UserFieldInfo
    {
        /// <summary>
        /// The ID to use for this field.
        /// </summary>
        public string Key { get; set; }

        /// <summary>
        /// The prompt to use to ask for a value for this field.
        /// </summary>
        public string Prompt { get; set; }

        /// <summary>
        /// Gets the value of the corresponding field.
        /// </summary>
        public Func<UserProfile, string> GetValue { get; set; }

        /// <summary>
        /// Sets the value of the corresponding field.
        /// </summary>
        public Action<UserProfile, string> SetValue { get; set; }
    }

    /// <summary>
    /// The prompts for the user profile, indexed by field name.
    /// </summary>
    private static List<UserFieldInfo> UserFields { get; } = new List<UserFieldInfo>
    {
        new UserFieldInfo {
            Key = nameof(UserProfile.UserName),
            Prompt = "What is your name?",
            GetValue = (profile) => profile.UserName,
            SetValue = (profile, value) => profile.UserName = value,
        },
        new UserFieldInfo {
            Key = nameof(UserProfile.Age),
            Prompt = "How old are you?",
            GetValue = (profile) => profile.Age.HasValue? profile.Age.Value.ToString() : null,
            SetValue = (profile, value) =>
            {
                if (int.TryParse(value, out int age))
                {
                    profile.Age = age;
                }
            },
        },
        new UserFieldInfo {
            Key = nameof(UserProfile.WorkPlace),
            Prompt = "Where do you work?",
            GetValue = (profile) => profile.WorkPlace,
            SetValue = (profile, value) => profile.WorkPlace = value,
        },
    };

    /// <summary>
    /// The state and state accessors for the bot.
    /// </summary>
    private BotAccessors Accessors { get; }

    public PrimitivePromptsBot(BotAccessors accessors)
    {
        Accessors = accessors ?? throw new ArgumentNullException(nameof(accessors));
    }

    public async Task OnTurnAsync(ITurnContext turnContext, CancellationToken cancellationToken = default(CancellationToken))
    {
        if (turnContext.Activity.Type is ActivityTypes.Message)
        {
            // Use the state property accessors to get the topic state and user profile.
            TopicState topicState = await Accessors.TopicStateAccessor.GetAsync(
                turnContext,
                () => new TopicState { Topic = ProfileTopic, Prompt = null },
                cancellationToken);
            UserProfile userProfile = await Accessors.UserProfileAccessor.GetAsync(
                turnContext,
                () => new UserProfile(),
                cancellationToken);

            // Check whether we need more information.
            if (topicState.Topic is ProfileTopic)
            {
                // If we're expecting input, record it in the user's profile.
                if (topicState.Prompt != null)
                {
                    UserFieldInfo field = UserFields.First(f => f.Key.Equals(topicState.Prompt));
                    field.SetValue(userProfile, turnContext.Activity.Text.Trim());
                }

                // Determine which fields are not yet set.
                List<UserFieldInfo> emptyFields = UserFields.Where(f => f.GetValue(userProfile) is null).ToList();

                if (emptyFields.Any())
                {
                    // If all the fields are empty, send a welcome message.
                    if (emptyFields.Count == UserFields.Count)
                    {
                        await turnContext.SendActivityAsync("Welcome new user, please fill out your profile information.");
                    }

                    // We have at least one empty field. Prompt for the next empty field,
                    // and update the prompt flag to indicate which prompt we just sent,
                    // so that the response can be captured at the beginning of the next turn.
                    UserFieldInfo field = emptyFields.First();
                    await turnContext.SendActivityAsync(field.Prompt);
                    topicState.Prompt = field.Key;
                }
                else
                {
                    // Our user profile is complete!
                    await turnContext.SendActivityAsync($"Thank you, {userProfile.UserName}. Your profile is complete.");
                    topicState.Prompt = null;
                    topicState.Topic = null;
                }
            }
            else if (turnContext.Activity.Text.Trim().Equals("hi", StringComparison.InvariantCultureIgnoreCase))
            {
                await turnContext.SendActivityAsync($"Hi. {userProfile.UserName}.");
            }
            else
            {
                await turnContext.SendActivityAsync("Hi. I'm the Contoso cafe bot.");
            }

            // Use the state property accessors to update the topic state and user profile.
            await Accessors.TopicStateAccessor.SetAsync(turnContext, topicState, cancellationToken);
            await Accessors.UserProfileAccessor.SetAsync(turnContext, userProfile, cancellationToken);

            // Save any state changes to storage.
            await Accessors.ConversationState.SaveChangesAsync(turnContext, false, cancellationToken);
            await Accessors.UserState.SaveChangesAsync(turnContext, false, cancellationToken);
        }
    }
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

**app.js**

```javascript

server.post('/api/messages', (req, res) => {
    adapter.processActivity(req, res, async (context) => {
        const isMessage = (context.activity.type === ActivityTypes.Message);

        // Set up a list of fields that we need to collect from the user.
        const fields = {
            userName: "What is your name?",
            age: "How old are you?",
            workPlace: "Where do you work?"
        }

        // Pull the state of the dialog out of the conversation state manager.
        const convo = await dialogState.get(context, {
            topic: 'profile',
            prompt: undefined
        });

        // Pull the user profile out of the user state manager.
        const userProfile = await userProfile.get(context, {  // Define the user's profile object
                "userName": undefined,
                "age": undefined,
                "workPlace": undefined
            }
        );

        if (isMessage) {

            if (convo.topic === 'profile') {
                // If a prompt flag is set in the conversation state, use it to capture the incoming value
                // into the appropriate field of the user profile.
                if (convo.prompt) {
                    userProfile[convo.prompt] = context.activity.text;
                }

                 // Determine which fields are not yet set.
                 const empty_fields = [];
                 Object.keys(fields).forEach(function(key) {
                    if (!userProfile[key]) {
                        empty_fields.push({
                           key: key,
                           prompt: fields[key]
                        });
                     }
                 });

                 if (empty_fields.length) {

                    // If all the fields are empty, send a welcome message.
                    if (empty_fields.length == Object.keys(fields).length) {
                        await context.sendActivity('Welcome new user, please fill out your profile information.');
                    }

                    // We have at least one empty field. Prompt for the next empty field.
                    await context.sendActivity(empty_fields[0].prompt);

                    // update the flag to indicate which prompt we just sent
                    // so that the response can be captured at the beginning of the next turn.
                    convo.prompt = empty_fields[0].key;

                 } else {
                    // Our user profile is complete!
                    await context.sendActivity('Thank you. Your profile is complete.');
                    convo.prompt = null;
                    convo.topic = null;

                 }
            } else if (context.activity.text && context.activity.text.match(/hi/ig)) {
                // Check to see if the user said "hi" and respond with a greeting
                await context.sendActivity(`Hi ${ userProfile.userName }.`);
            } else {
                // Default message
                await context.sendActivity("Hi. I'm the Contoso bot.");
            }
        }

        // End the turn by writing state changes back to storage
        await conversationState.saveChanges(context);
        await userState.saveChanges(context);
    });
});

```

---

Mit dem obigen Codebeispiel wird das Flag _topic_ in `profile` initialisiert, um die Konversation für die Profilsammlung zu starten. Der Bot durchläuft die Konversation, indem das Flag _prompt_ in einen Wert aktualisiert wird, der für die derzeit gestellte Frage steht. Wenn dieses Flag auf den richtigen Wert festgelegt ist, weiß der Bot, was mit der nächsten eingehenden Nachricht des Benutzers zu tun ist, und kann diese entsprechend verarbeiten.

Als Letztes werden die Flags zurückgesetzt, wenn der Bot das Abfragen der Informationen beendet hat. Andernfalls ist der Bot in einer Schleife gefangen und kann nach dem Stellen der letzten Frage nicht fortfahren.

Dieses Muster können Sie bei Bedarf für die Verwaltung komplexerer Konversationsabläufe erweitern, indem Sie weitere Flags definieren oder die Konversation basierend auf der Benutzereingabe verzweigen.

## <a name="manage-multiple-topics-of-conversations"></a>Verwalten mehrerer Konversationsthemen

Wenn Sie mit einem einzelnen Konversationsthema zurechtkommen, können Sie die Logik des Bots auf die Verarbeitung mehrerer Konversationsthemen erweitern. Mehrere Themen können verarbeitet werden, indem eine Prüfung auf zusätzliche Bedingungen durchgeführt und dann der geeignete Pfad gewählt wird.

Sie können das obige Beispiel erweitern, um andere Features und Konversationsthemen zu ermöglichen, z.B. das Reservieren eines Tischs oder Aufgeben einer Bestellung. Fügen Sie dem Themenzustand je nach Bedarf weitere Flags hinzu, um die Konversation nachzuverfolgen.

Es kann auch hilfreich sein, Code auf unabhängige Funktionen oder Methoden aufzuteilen, um dem Konversationsablauf leichter folgen zu können. Ein gängiges Muster besteht darin, die Nachricht und den Zustand vom Bot auswerten zu lassen und die Steuerung dann an Funktionen zu delegieren, mit denen Details des Features implementiert werden.

Erwägen Sie, ein Hauptmenü bereitzustellen, damit Ihre Benutzer besser durch mehrere Konversationsthemen navigieren können. Bei Verwendung von [empfohlenen Aktionen](bot-builder-howto-add-suggested-actions.md) können Sie Ihren Benutzern beispielsweise die Wahl geben, welches Thema sie wählen möchten, anstatt sie raten zu lassen, was mit Ihrem Bot möglich ist.

## <a name="validate-user-input"></a>Validieren von Benutzereingaben

Die Bibliothek **Dialogs** enthält integrierte Überprüfungsmöglichkeiten für Benutzereingaben. Eine solche Überprüfung ist aber auch mit unseren eigenen Eingabeaufforderungen möglich. Wenn wir beispielsweise nach dem Alter des Benutzers fragen, möchten wir natürlich sicherstellen, dass wir eine Zahl und nicht den Namen des Benutzers erhalten.

Das Analysieren von Zahlen oder Datums-/Uhrzeitangaben ist eine komplexe Angelegenheit, die den Rahmen dieses Themas sprengen würde. Zum Glück gibt es jedoch eine Bibliothek, die wir nutzen können. Für die Analyse dieser Informationen verwenden wir die [Texterkennungsbibliothek von Microsoft](https://github.com/Microsoft/Recognizers-Text). Dieses Paket steht über NuGet zur Verfügung oder kann aus dem Repository herunterladen werden. (Es ist auch in der Bibliothek **Dialogs** enthalten. Dies ist ein wichtiger Hinweis, auch wenn die Bibliothek hier nicht verwendet wird.)

Diese Bibliothek ist besonders nützlich zum Analysieren von komplexen Eingaben wie Datumsangaben, Uhrzeiten oder Telefonnummern. In diesem Beispiel wird eine Zahl für die Größe einer Essensgesellschaft überprüft. Das Konzept kann aber auch erweitert werden, um wesentlich detailliertere Überprüfungen durchzuführen.

Im folgenden Beispiel wird nur die Nutzung von `RecognizeNumber` veranschaulicht. Ausführliche Informationen zur Verwendung anderer Erkennungsmethoden aus der Bibliothek finden Sie in der [Dokumentation des Repositorys](https://github.com/Microsoft/Recognizers-Text/blob/master/README.md).

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Fügen Sie das NuGet-Paket ein, und fügen Sie es Ihren using-Anweisungen hinzu, um die Bibliothek **Microsoft.Recognizers.Text.Number** zu verwenden.

```csharp
using Microsoft.Recognizers.Text.Number;
using Microsoft.Recognizers.Text;
using System.Linq;
```

Definieren Sie anschließend eine Funktion für die eigentliche Überprüfung.

```csharp
private async Task<bool> ValidatePartySize(ITurnContext context, string value)
{
    try
    {
        // Recognize the input as a number. This works for responses such as
        // "twelve" as well as "12"
        var result = NumberRecognizer.RecognizeNumber(value, Culture.English);

        // Attempt to convert the Recognizer result to an integer
        int.TryParse(result.First().Text, out int partySize);

        if (partySize < 6)
        {
            throw new Exception("Party size too small.");
        }
        else if (partySize > 20)
        {
            throw new Exception("Party size too big.");
        }

        // If we got through this, the number is valid
        return true;
    }
    catch (Exception)
    {
        await context.SendActivityAsync("Error with your party size. < br /> Please specify a number between 6 - 20.");
        return false;
    }
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Wenn Sie die Bibliothek mit den **Erkennungen** verwenden möchten, müssen Sie dies in **app.js** festlegen:

```javascript
// Required packages for this bot
var Recognizers = require('@microsoft/recognizers-text-suite');
```

Definieren Sie anschließend eine Funktion für die eigentliche Überprüfung.

```javascript
// Support party size between 6 and 20 only
async function validatePartySize(context, input){
    try {
        // Recognize the input as a number. This works for responses such as
        // "twelve" as well as "12"
        var result = Recognizers.recognizeNumber(input, Recognizers.Culture.English);
        var value = parseInt(results[0].resolution.value);

        if (value < 6) {
            throw new Error(`Party size too small.`);
        } else if(value > 20){
            throw new Error(`Party size too big.`);
        }
        return true; // Return the valid value
    } catch (err){
        await context.sendActivity(`${err.message} <br/>Please specify a number between 6 - 20.`);
        return false;
    }
}
```

---

Rufen Sie beim Verarbeiten der Antwort des Benutzers auf die Eingabeaufforderung die Validierungsfunktion auf, bevor Sie mit der nächsten Eingabeaufforderung fortfahren. Ist die Überprüfung nicht erfolgreich, wiederholen Sie die Frage.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
if (topicState.Prompt == "partySize")
{
    if (await ValidatePartySize(turnContext, turnContext.Activity.Text))
    {
        // Save user's response in our state, ReservationInfo, which
        // is a new class we've added to our state
        // UserFieldInfo partySize;
        partySize.SetValue(userProfile, turnContext.Activity.Text);

        // Ask next question.
        topicState.Prompt = "reserveName";
        await turnContext.SendActivityAsync("Who's name will this be under?");
    }
    else
    {
        // Ask again.
        await turnContext.SendActivityAsync("How many people are in your party?");
    }
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

**app.js**

```javascript
// ...
if (convo.prompt == "partySize") {
    if (await validatePartySize(context, context.activity.text)) {
        // Save user's response
        reservationInfo.partySize = context.activity.text;

        // Ask next question
        convo.prompt = "reserveName";
        await context.sendActivity("Who's name will this be under?");
    } else {
        // Ask again
        await context.sendActivity("How many people are in your party?");
    }
}
// ...
```

---

## <a name="next-step"></a>Nächster Schritt

Sie wissen nun, wie Sie Eingabeaufforderungszustände selbst verwalten. Als Nächstes erfahren Sie, wie Sie die Bibliothek **Dialogs** verwenden, um Benutzer zu einer Eingabe aufzufordern.

> [!div class="nextstepaction"]
> [Auffordern von Benutzern zur Eingabe](bot-builder-prompts.md)
