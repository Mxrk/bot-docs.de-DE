---
title: Senden einer Begrüßungsnachricht an Benutzer | Microsoft-Dokumentation
description: Es wird beschrieben, wie Sie Ihren Bot so entwickeln, dass eine einladende Benutzerumgebung geschaffen wird.
keywords: Übersicht, entwickeln, Benutzeroberfläche, Willkommen, personalisierte Benutzeroberfläche, C#, JS, Begrüßungsnachricht, Bot, begrüßen, Begrüßung
author: DanDev33
ms.author: v-dashel
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 12/20/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 7e6ea963ce018833b362be3f413f15da4d4bf658
ms.sourcegitcommit: f7a8f05fc05ff4a7212a437d540485bf68831604
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/21/2018
ms.locfileid: "53735940"
---
# <a name="send-welcome-message-to-users"></a>Senden einer Begrüßungsnachricht an Benutzer

[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

Das Hauptziel bei der Erstellung eines Bots besteht darin, mit dem Benutzer eine aussagekräftige Konversation zu führen. Dieses Ziel erreichen Sie am besten, indem Sie Folgendes sicherstellen: Gleich nach dem ersten Anmelden müssen Benutzer den Hauptzweck und die Funktionen des Bots erkennen können und sich darüber im Klaren sein, aus welchem Grund der Bot erstellt wurde. Dieser Artikel enthält Codebeispiele für die Begrüßung von Benutzern in Ihrem Bot.

## <a name="prerequisites"></a>Voraussetzungen
- Sie müssen mit den [Bot-Grundlagen](bot-builder-basics.md) vertraut sein. 
- Eine Kopie des **Beispiels zur Benutzerbegrüßung** im [C#](https://aka.ms/bot-welcome-sample-cs)- oder [JS](https://aka.ms/bot-welcome-sample-js)-Format. Der Code aus dem Beispiel wird verwendet, um zu beschreiben, wie Sie Begrüßungsnachrichten senden.

## <a name="same-welcome-for-different-channels"></a>Gleiche Begrüßung für unterschiedliche Kanäle
Es sollte immer eine Begrüßungsnachricht generiert werden, wenn Ihre Benutzer zum ersten Mal mit Ihrem Bot interagieren. Hierzu können Sie die Aktivitätstypen Ihres Bots überwachen und auf neue Verbindungen warten. Für jede neue Verbindung können je nach Kanal bis zu zwei Aktivitäten zum Aktualisieren der Konversation generiert werden.

- Eine Aktivität, wenn der Bot des Benutzers mit der Konversation verbunden wird.
- Eine weitere Aktivität, wenn der Benutzer der Konversation beitritt.

Es ist verlockend, jeweils einfach eine Begrüßungsnachricht zu generieren, wenn eine neue Konversationsaktualisierung erkannt wird. Dies kann aber zu unerwarteten Ergebnissen führen, wenn auf Ihren Bot über viele verschiedene Kanäle zugegriffen wird.

Für einige Kanäle wird eine Konversationsaktualisierung erstellt, wenn ein Benutzer zum ersten Mal eine Verbindung mit dem Kanal herstellt, und erst dann eine separate Konversationsaktualisierung, wenn vom Benutzer eine erste Eingabenachricht empfangen wird. Andere Kanäle generieren beide Aktivitäten, wenn der Benutzer zum ersten Mal eine Verbindung mit dem Kanal herstellt. Wenn Sie einfach auf ein Ereignis zur Konversationsaktualisierung warten und eine Begrüßungsnachricht in einem Kanal mit zwei Aktivitäten zur Konversationsaktualisierung anzeigen, erhält der Benutzer unter Umständen Folgendes:

![Doppelte Begrüßungsnachricht](./media/double_welcome_message.png)

Diese doppelte Nachricht kann vermieden werden, indem nur für das zweite Ereignis zur Konversationsaktualisierung eine erste Begrüßungsnachricht generiert wird. Das zweite Ereignis kann erkannt werden, wenn die beiden folgenden Bedingungen erfüllt sind:
- Ein Ereignis zur Konversationsaktualisierung ist eingetreten.
- Der Konversation wurde ein neues Mitglied (Benutzer) hinzugefügt.

Im folgenden Beispiel wird nach einer neuen *Aktivität zum Aktualisieren der Konversation* gesucht, eine einzelne Begrüßungsnachricht gesendet (basierend auf dem Beitritt des Benutzers zur Konversation) und ein Statusflag für die Eingabeaufforderung festgelegt, um die erste Konversationseingabe des Benutzers zu ignorieren. 

[!INCLUDE [alert-await-send-activity](../includes/alert-await-send-activity.md)]

## <a name="ctabcsharp"></a>[C#](#tab/csharp)

Wir müssen ein Zustandsobjekt für einen bestimmten Benutzer einer Konversation und den zugehörigen Accessor erstellen.

```csharp
/// The state object is used to keep track of various state related to a user in a conversation.
/// In this example, we are tracking if the bot has replied to customer first interaction.
public class WelcomeUserState
{
    public bool DidBotWelcomeUser { get; set; } = false;
}

/// Initializes a new instance of the <see cref="WelcomeUserStateAccessors"/> class.
public class WelcomeUserStateAccessors
{
    public WelcomeUserStateAccessors(UserState userState)
    {
        this.UserState = userState ?? throw new ArgumentNullException(nameof(userState));
    }

    public IStatePropertyAccessor<bool> DidBotWelcomeUser { get; set; }

    public UserState UserState { get; }
}
```

Anschließend führen wir einfach eine Überprüfung auf ein Aktivitätsupdate durch, um zu ermitteln, ob der Konversation ein neuer Benutzer hinzugefügt wurde. An diesen Benutzer senden wir dann eine Begrüßungsnachricht.

```csharp
public class WelcomeUserBot : IBot
{
// Generic message to be sent to user
private const string _genericMessage = @"This is a simple Welcome Bot sample. You can say 'intro' to 
                                         see the introduction card. If you are running this bot in the Bot 
                                         Framework Emulator, press the 'Start Over' button to simulate user joining a bot or a channel";

// The bot state accessor object. Use this to access specific state properties.
private readonly WelcomeUserStateAccessors _welcomeUserStateAccessors;

// Initializes a new instance of the <see cref="WelcomeUserBot"/> class.
public WelcomeUserBot(WelcomeUserStateAccessors statePropertyAccessor)
{
    _welcomeUserStateAccessors = statePropertyAccessor ?? throw new System.ArgumentNullException("state accessor can't be null");
}

// Every conversation turn for our WelcomeUser Bot will call this method, including
// any type of activities such as ConversationUpdate or ContactRelationUpdate which
// are sent when a user joins a conversation.
// This bot doesn't use any dialogs; it's "single turn" processing, meaning a single
// request and response.
// This bot uses UserState to keep track of first message a user sends.
public async Task OnTurnAsync(ITurnContext turnContext, CancellationToken cancellationToken = new CancellationToken())
{
    // Use state accessor to extract the didBotWelcomeUser flag
    var didBotWelcomeUser = await _welcomeUserStateAccessors.DidBotWelcomeUser.GetAsync(turnContext, () => false);

    if (turnContext.Activity.Type == ActivityTypes.Message)
    {
        // Your bot should proactively send a welcome message to a personal chat the first time
        // (and only the first time) a user initiates a personal chat with your bot.
        if (didBotWelcomeUser == false)
        {
            // Update user state flag to reflect bot handled first user interaction.
            await _welcomeUserStateAccessors.DidBotWelcomeUser.SetAsync(turnContext, true);
            await _welcomeUserStateAccessors.UserState.SaveChangesAsync(turnContext);

            // the channel should sends the user name in the 'From' object
            var userName = turnContext.Activity.From.Name;

            await turnContext.SendActivityAsync($"You are seeing this message because this was your first message ever to this bot.", cancellationToken: cancellationToken);
            await turnContext.SendActivityAsync($"It is a good practice to welcome the user and provide a personal greeting. For example, welcome {userName}.", cancellationToken: cancellationToken);
        }
    }
    else if (turnContext.Activity.Type == ActivityTypes.ConversationUpdate) // Greet when users are added to the conversation.
    {
        if (turnContext.Activity.MembersAdded.Any())
        {
            // Iterate over all new members added to the conversation
            foreach (var member in turnContext.Activity.MembersAdded)
            {
                // Greet anyone that was not the target (recipient) of this message
                // the 'bot' is the recipient for events from the channel,
                // turnContext.Activity.MembersAdded == turnContext.Activity.Recipient.Id indicates the
                // bot was added to the conversation.
                if (member.Id != turnContext.Activity.Recipient.Id)
                {
                    await turnContext.SendActivityAsync($"Hi there - {member.Name}. Welcome to the 'Welcome User' Bot. This bot will introduce you to welcoming and greeting users.", cancellationToken: cancellationToken);
                    await turnContext.SendActivityAsync($"You are seeing this message because the bot recieved at least one 'ConversationUpdate' event,indicating you (and possibly others) joined the conversation. If you are using the emulator, pressing the 'Start Over' button to trigger this event again. The specifics of the 'ConversationUpdate' event depends on the channel. You can read more information at https://aka.ms/about-botframewor-welcome-user", cancellationToken: cancellationToken);
                    await turnContext.SendActivityAsync($"It is a good pattern to use this event to send general greeting to user, explaning what your bot can do. In this example, the bot handles 'hello', 'hi', 'help' and 'intro. Try it now, type 'hi'", cancellationToken: cancellationToken);
                }
            }
        }
    }
    // save any state changes made to your state objects.
    await _welcomeUserStateAccessors.UserState.SaveChangesAsync(turnContext);
}
```

## <a name="javascripttabjs"></a>[JavaScript](#tab/js)

Mit diesem JavaScript-Code wird eine Begrüßungsnachricht gesendet, wenn ein Benutzer hinzugefügt wird. Hierfür wird die Konversationsaktivität überprüft und sichergestellt, dass der Konversation ein neues Mitglied hinzugefügt wurde.

```javascript
// Import required Bot Framework classes.
const { ActivityTypes } = require('botbuilder');
const { CardFactory } = require('botbuilder');

// Welcomed User property name
const WELCOMED_USER = 'DidBotWelcomeUser';

class MainDialog {
    constructor (userState) {
        // Creates a new user property accessor.
        // See https://aka.ms/about-bot-state-accessors to learn more about the bot state and state accessors.
        this.welcomedUserPropery = userState.createProperty(WELCOMED_USER);
    }

    async onTurn(turnContext) 
    {
        // See https://aka.ms/about-bot-activity-message to learn more about the message and other activity types.
        if (turnContext.activity.type === ActivityTypes.Message) 
        {
            // Read UserState. If the 'DidBotWelcomeUser' does not exist (first time ever for a user)
            // set the default to false.
            let didBotWelcomeUser = await this.welcomedUserPropery.get(turnContext, false);

            // Your bot should proactively send a welcome message to a personal chat the first time
            // (and only the first time) a user initiates a personal chat with your bot.
            if (didBotWelcomeUser === false) 
            {
                // The channel should send the user name in the 'From' object
                let userName = turnContext.activity.from.name;
                await turnContext.sendActivity("You are seeing this message because this was your first message ever sent to this bot.");
                await turnContext.sendActivity(`It is a good practice to welcome the user and provdie personal greeting. For example, welcome ${userName}.`);
                
                // Set the flag indicating the bot handled the user's first message.
                await this.welcomedUserPropery.set(turnContext, true);
            }
            . . .
            
            // Save state changes
            await this.userState.saveChanges(turnContext);
        } else if (turnContext.activity.type === ActivityTypes.ConversationUpdate) {
            // Send greeting when users are added to the conversation.
            await this.sendWelcomeMessage(turnContext);
        } else {
            // Generic message for all other activities
            await turnContext.sendActivity(`[${ turnContext.activity.type } event detected]`);
        }
    }
    
    // Sends welcome messages to conversation members when they join the conversation.
    // Messages are only sent to conversation members who aren't the bot.
    async sendWelcomeMessage(turnContext) {
        // If any new membmers added to the conversation
        if (turnContext.activity && turnContext.activity.membersAdded) {
            // Define a promise that will welcome the user
            async function welcomeUserFunc(conversationMember) {
                // Greet anyone that was not the target (recipient) of this message.
                // The bot is the recipient of all events from the channel, including all ConversationUpdate-type activities
                // turnContext.activity.membersAdded !== turnContext.activity.aecipient.id indicates 
                // a user was added to the conversation 
                if (conversationMember.id !== this.activity.recipient.id) {
                    // Because the TurnContext was bound to this function, the bot can call
                    // `TurnContext.sendActivity` via `this.sendActivity`;
                    await this.sendActivity(`Welcome to the 'Welcome User' Bot. This bot will introduce you to welcoming and greeting users.`);
                    await this.sendActivity("You are seeing this message because the bot recieved at least one 'ConversationUpdate'" + 
                                            "event,indicating you (and possibly others) joined the conversation. If you are using the emulator, "+ 
                                            "pressing the 'Start Over' button to trigger this event again. The specifics of the 'ConversationUpdate' "+
                                            "event depends on the channel. You can read more information at https://aka.ms/about-botframewor-welcome-user");
                    await this.sendActivity(`It is a good pattern to use this event to send general greeting to user, explaining what your bot can do. `+ 
                                            `In this example, the bot handles 'hello', 'hi', 'help' and 'intro. ` +
                                            `Try it now, type 'hi'`);
                }
            }
    
            // Prepare Promises to greet the  user.
            // The current TurnContext is bound so `replyForReceivedAttachments` can also send replies.
            const replyPromises = turnContext.activity.membersAdded.map(welcomeUserFunc.bind(turnContext));
            await Promise.all(replyPromises);
        }
    }
}

module.exports = MainDialog;
```

---

## <a name="discard-initial-user-input"></a>Verwerfen der ersten Benutzereingabe
Es ist auch wichtig zu berücksichtigen, wann die Eingabe Ihres Benutzers ggf. nützliche Informationen enthält. Auch dies kann je nach Kanal variieren. Um sicherzustellen, dass Ihr Benutzer über alle möglichen Kanäle eine benutzerfreundliche Oberfläche erhält, vermeiden wir es, ungültige Antwortdaten zu verarbeiten. Hierfür stellen wir eine anfängliche Eingabeaufforderung bereit und richten Schlüsselwörter ein, nach denen in den Antworten des Benutzers gesucht wird.

## <a name="ctabcsharpmulti"></a>[C#](#tab/csharpmulti)

```csharp
public async Task OnTurnAsync(ITurnContext turnContext, CancellationToken cancellationToken = new CancellationToken())
{
    // Use state accessor to extract the didBotWelcomeUser flag
    var didBotWelcomeUser = await _welcomeUserStateAccessors.DidBotWelcomeUser.GetAsync(turnContext, () => false);

    if (turnContext.Activity.Type == ActivityTypes.Message)
    {
        // Your bot should proactively send a welcome message to a personal chat the first time
        // (and only the first time) a user initiates a personal chat with your bot.
        if (didBotWelcomeUser == false)
        {
            // Previous Code Sample
        }
        else
        {
            // This example hardcodes specific uterances. You should use LUIS or QnA for more advance language understanding.
            var text = turnContext.Activity.Text.ToLowerInvariant();
            switch (text)
            {
                case "hello":
                case "hi":
                    await turnContext.SendActivityAsync($"You said {text}.", cancellationToken: cancellationToken);
                    break;
                case "intro":
                case "help":
                default:
                    await turnContext.SendActivityAsync(_genericMessage, cancellationToken: cancellationToken);
                    break;
            }
        }
    }
    else if (turnContext.Activity.Type == ActivityTypes.ConversationUpdate) // Greet when users are added to the conversation.
    {
        if (turnContext.Activity.MembersAdded.Any())
        {
            // Iterate over all new members added to the conversation
            foreach (var member in turnContext.Activity.MembersAdded)
            {
                // Previous Code Sample
            }
        }
    }
    else
    {
        // Default behaivor for all other type of events.
        var ev = turnContext.Activity.AsEventActivity();
        await turnContext.SendActivityAsync($"Received event: {ev.Name}");
    }
    // save any state changes made to your state objects.
    await _welcomeUserStateAccessors.UserState.SaveChangesAsync(turnContext);
}

```

## <a name="javascripttabjsmulti"></a>[JavaScript](#tab/jsmulti)

```javascript
class MainDialog 
{ 
    // Previous Code Sample
    
    async onTurn(turnContext) 
    {
        // See https://aka.ms/about-bot-activity-message to learn more about the message and other activity types.
        if (turnContext.activity.type === ActivityTypes.Message) {
            
            // Previous Code Sample
            
            if (didBotWelcomeUser === false) 
            {
                // Previous Code Sample
            }
            else 
            {
                // This example hardcodes specific uterances. You should use LUIS or QnA for more advance language understanding.
                let text = turnContext.activity.text.toLowerCase();
                switch (text) 
                {
                    case "hello":
                    case "hi":
                        await turnContext.sendActivity(`You said "${turnContext.activity.text}"`);
                        break;
                    case "intro":
                    case "help":
                    default :
                        await turnContext.sendActivity(`This is a simple Welcome Bot sample. You can say 'intro' to 
                                                        see the introduction card. If you are running this bot in the Bot 
                                                        Framework Emulator, press the 'Start Over' button to simulate user joining a bot or a channel`);
                }
            }
        }
       
       // Previous Sample Code
    } 
}
```

---

## <a name="using-adaptive-card-greeting"></a>Verwenden der Begrüßung über adaptive Karten

Eine weitere Möglichkeit zum Begrüßen von Benutzern ist die Nutzung der Begrüßung über adaptive Karten. Weitere Informationen zur Begrüßung über adaptive Karten finden Sie hier: [Senden adaptiver Karten](./bot-builder-howto-add-media-attachments.md).

## <a name="ctabcsharpwelcomeback"></a>[C#](#tab/csharpwelcomeback)

```csharp
// Sends an adaptive card greeting.
private static async Task SendIntroCardAsync(ITurnContext turnContext, CancellationToken cancellationToken)
{
    var response = turnContext.Activity.CreateReply();

    var introCard = File.ReadAllText(@".\Resources\IntroCard.json");

    response.Attachments = new List<Attachment>
    {
        new Attachment()
        {
            ContentType = "application/vnd.microsoft.card.adaptive",
            Content = JsonConvert.DeserializeObject(introCard),
        },
    };

    await turnContext.SendActivityAsync(response, cancellationToken);
}
```

Als Nächstes können wir die Karte mit dem folgenden await-Befehl senden. Wir fügen dies in den Code _switch (text) case "help"_ des Bots ein.

```csharp
switch (text)
{
    case "hello":
    case "hi":
        await turnContext.SendActivityAsync($"You said {text}.", cancellationToken: cancellationToken);
        break;
    case "intro":
    case "help":
        await SendIntroCardAsync(turnContext, cancellationToken);
        break;
    default:
        await turnContext.SendActivityAsync(_genericMessage, cancellationToken: cancellationToken);
        break;
}
```


## <a name="javascripttabjswelcomeback"></a>[JavaScript](#tab/jswelcomeback)

Zuerst fügen wir unsere adaptive Karte dem Bot oben in der Datei _index.js_ direkt unterhalb unserer Importe hinzu.

```javascript
// Adaptive Card content
const IntroCard = require('./Resources/IntroCard.json');
```

Als Nächstes können wir den unten angegebenen Code im Abschnitt _switch (text)_ _case "help"_ des Bots verwenden, um auf die Benutzereingabeaufforderung mit der adaptiven Karte zu reagieren.

```javascript
switch (text) 
{
    case "hello":
    case "hi":
        await turnContext.sendActivity(`You said "${turnContext.activity.text}"`);
        break;
    case "intro":
    case "help":
        await turnContext.sendActivity({
            text: 'Intro Adaptive Card',
            attachments: [CardFactory.adaptiveCard(IntroCard)]
        });
        break;
    default :
        await turnContext.sendActivity(`This is a simple Welcome Bot sample. You can say 'intro' to 
                                        see the introduction card. If you are running this bot in the Bot 
                                        Framework Emulator, press the 'Start Over' button to simulate user joining a bot or a channel`);
}
```
---
## <a name="test-the-bot"></a>Testen des Bots
Eine Anleitung zum Ausführen und Testen des Bots finden Sie in der [INFODATEI](https://aka.ms/bot-welcome-sample-cs).

## <a name="next-steps"></a>Nächste Schritte
> [!div class="nextstepaction"]
> [Erfassen von Benutzereingaben](bot-builder-prompts.md)
