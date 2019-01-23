---
title: Implementieren eines sequenziellen Konversationsflusses | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie einen einfachen Konversationsfluss mit Dialogen im Bot Framework SDK für Node.js verwalten.
keywords: einfacher Konversationsfluss, sequenzieller Konversationsfluss, Dialoge, Eingabeaufforderungen, Wasserfälle, Dialogsatz
author: JonathanFingold
ms.author: v-jofing
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 11/18/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 554d040dd4c9d126fa70c24f1af5a1ac97a39204
ms.sourcegitcommit: b94361234816e6b95459f142add936732fc40344
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/15/2019
ms.locfileid: "54317615"
---
# <a name="implement-sequential-conversation-flow"></a>Implementieren eines sequenziellen Konversationsflusses

[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

Mit der Dialogbibliothek können Sie einfache und komplexe Konversationsflüsse verwalten. In einer einfachen Interaktion wird der Bot über eine feste Sequenz von Schritten ausgeführt, und die Konversation endet. In diesem Artikel verwenden wir einen _Wasserfalldialog_, einige _Eingabeaufforderungen_ und einen _Dialogsatz_, um eine einfache Interaktion zu erstellen, bei der dem Benutzer eine Reihe von Fragen gestellt wird.

## <a name="prerequisites"></a>Voraussetzungen
- [Bot Framework-Emulator](https://github.com/Microsoft/BotFramework-Emulator/blob/master/README.md#download)
- Der Code in diesem Artikel basiert auf dem Beispiel für die Eingabeaufforderung für mehrere Durchläufe (**multi-turn-prompt**). Sie benötigen eine Kopie des Beispiels im [C#](https://aka.ms/cs-multi-prompts-sample)- oder [JS](https://aka.ms/js-multi-prompts-sample)-Format.
- Sie müssen mit den [Bot-Grundlagen](bot-builder-basics.md), der [Dialogbibliothek](bot-builder-concept-dialog.md), dem [Dialogzustand ](bot-builder-dialog-state.md) und der [BOT](bot-file-basics.md)-Datei vertraut sein.


In den folgenden Abschnitten werden die Schritte erläutert, die Sie zum Implementieren einfacher Dialoge für die meisten Bots ausführen würden:

## <a name="configure-your-bot"></a>Konfigurieren Ihres Bots

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Wir initialisieren den Zustandseigenschaftenaccessor für den Dialogzustand des Bots im Konfigurationscode in der Datei **Startup.cs**.

Wir definieren eine `MultiTurnPromptsBotAccessors`-Klasse, die die Objekte für die Zustandsverwaltung und die Zustandseigenschaftenaccessoren für den Bot enthält.
Im Folgenden sind nur Teile des Codes aufgeführt.

```csharp
public class MultiTurnPromptsBotAccessors
{
    // Initializes a new instance of the class.
    public MultiTurnPromptsBotAccessors(ConversationState conversationState, UserState userState)
    {
        ConversationState = conversationState ?? throw new ArgumentNullException(nameof(conversationState));
        UserState = userState ?? throw new ArgumentNullException(nameof(userState));
    }

    public IStatePropertyAccessor<DialogState> ConversationDialogState { get; set; }
    public IStatePropertyAccessor<UserProfile> UserProfile { get; set; }

    public ConversationState ConversationState { get; }
    public UserState UserState { get; }
}
```

Wir registrieren die Accessors-Klasse in der `ConfigureServices`-Methode der `Startup`-Klasse.
Auch hier werden nur Teile des Codes gezeigt.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ...

    // Create and register state accessors.
    // Accessors created here are passed into the IBot-derived class on every turn.
    services.AddSingleton<MultiTurnPromptsBotAccessors>(sp =>
    {
        // We need to grab the conversationState we added on the options in the previous step
        var options = sp.GetRequiredService<IOptions<BotFrameworkOptions>>().Value;
        var conversationState = options.State.OfType<ConversationState>().FirstOrDefault();
        var userState = options.State.OfType<UserState>().FirstOrDefault();

        // Create the custom state accessor.
        // State accessors enable other components to read and write individual properties of state.
        var accessors = new MultiTurnPromptsBotAccessors(conversationState, userState)
        {
            ConversationDialogState = conversationState.CreateProperty<DialogState>("DialogState"),
            UserProfile = userState.CreateProperty<UserProfile>("UserProfile"),
        };

        return accessors;
    });
}
```

Die Accessoren sind über Abhängigkeitsinjektion für den Konstruktorcode des Bots verfügbar.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

In der Datei **index.js** definieren wir die Objekte für die Zustandsverwaltung.
Im Folgenden sind nur Teile des Codes aufgeführt.

```javascript
// Import required bot services. See https://aka.ms/bot-services to learn more about the different part of a bot.
const { BotFrameworkAdapter, ConversationState, MemoryStorage, UserState } = require('botbuilder');

// Define the state store for your bot. See https://aka.ms/about-bot-state to learn more about using MemoryStorage.
// A bot requires a state storage system to persist the dialog and user state between messages.
const memoryStorage = new MemoryStorage();

// Create conversation state with in-memory storage provider.
const conversationState = new ConversationState(memoryStorage);
const userState = new UserState(memoryStorage);

// Create the main dialog, which serves as the bot's main handler.
const bot = new MultiTurnBot(conversationState, userState);
```

Der Konstruktor des Bots erstellt die Zustandseigenschaftenaccessoren für den Bot: `this.dialogState` und `this.userProfile`.

---

## <a name="update-the-bot-turn-handler-to-call-the-dialog"></a>Aktualisieren des Turn-Handlers des Bots zum Aufrufen des Dialogs

Zum Ausführen des Dialogs muss der Turn-Handler des Bots einen Dialogkontext für den Dialogsatz erstellen, der die Dialoge für den Bot enthält. Ein Bot kann mehrere Dialogsätze definieren, aber im Allgemeinen sollten Sie nur einen Dialogsatz für Ihren Bot festlegen. 

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Der Dialog wird über den Turn-Handler des Bots ausgeführt. Der Handler erstellt zunächst einen `DialogContext`, und je nach Situation setzt er entweder den aktiven Dialog fort, oder er startet einen neuen Dialog. Am Ende des Turns speichert der Handler dann den Konversations- und Benutzerzustand.

In der `MultiTurnPromptsBot`-Klasse haben wir eine `_dialogs`-Eigenschaft definiert, die den Dialogsatz enthält, aus dem wir einen Dialogkontext generieren. Auch hier wird nur ein Teil des Codes für den Turn-Handler gezeigt.

```csharp
public async Task OnTurnAsync(ITurnContext turnContext, CancellationToken cancellationToken = default(CancellationToken))
{
    // ...
    if (turnContext.Activity.Type == ActivityTypes.Message)
    {
        // Run the DialogSet - let the framework identify the current state of the dialog from
        // the dialog stack and figure out what (if any) is the active dialog.
        var dialogContext = await _dialogs.CreateContextAsync(turnContext, cancellationToken);
        var results = await dialogContext.ContinueDialogAsync(cancellationToken);

        // If the DialogTurnStatus is Empty we should start a new dialog.
        if (results.Status == DialogTurnStatus.Empty)
        {
            await dialogContext.BeginDialogAsync("details", null, cancellationToken);
        }
    }

    // ...
    // Save the dialog state into the conversation state.
    await _accessors.ConversationState.SaveChangesAsync(turnContext, false, cancellationToken);

    // Save the user profile updates into the user state.
    await _accessors.UserState.SaveChangesAsync(turnContext, false, cancellationToken);
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Der Botcode verwendet nur einige der Klassen in der Dialogbibliothek.

```javascript
const { ChoicePrompt, DialogSet, NumberPrompt, TextPrompt, WaterfallDialog } = require('botbuilder-dialogs');
```

Der Dialog wird über den Turn-Handler des Bots ausgeführt. Der Handler erstellt zunächst einen `DialogContext` (`dc`), und je nach Situation setzt er entweder den aktiven Dialog fort, oder er startet einen neuen Dialog. Am Ende des Turns speichert der Handler dann den Konversations- und Benutzerzustand.

Die `MultiTurnBot`-Klasse ist in der Datei **bot.js** definiert. Der Konstruktor für diese Klasse fügt eine `dialogs`-Eigenschaft für den Dialogsatz hinzu, aus dem wir einen Dialogkontext generieren. Dieser Bot erfasst die Benutzerdaten einmal mithilfe des `WHO_ARE_YOU`-Dialogs. Nachdem das Benutzerprofil aufgefüllt wurde, antwortet der Bot mit dem `HELLO_USER`-Dialog. Auch hier wird nur ein Teil des Codes für den Turn-Handler gezeigt.

```javascript
async onTurn(turnContext) {
    if (turnContext.activity.type === ActivityTypes.Message) {
        // Create a dialog context object.
        const dc = await this.dialogs.createContext(turnContext);

        const utterance = (turnContext.activity.text || '').trim().toLowerCase();

        // ...
        // If the bot has not yet responded, continue processing the current dialog.
        await dc.continueDialog();

        // Start the sample dialog in response to any other input.
        if (!turnContext.responded) {
            const user = await this.userProfile.get(dc.context, {});
            if (user.name) {
                await dc.beginDialog(HELLO_USER);
            } else {
                await dc.beginDialog(WHO_ARE_YOU);
            }
        }
    }

    // ...
    // Save changes to the user state.
    await this.userState.saveChanges(turnContext);

    // End this turn by saving changes to the conversation state.
    await this.conversationState.saveChanges(turnContext);
}
```

---

Im Turn-Handler des Bots erstellen wir einen Dialogkontext für den Dialogsatz. Der Dialogkontext greift auf den Zustandscache für den Bot zu und speichert so den Punkt in der Konversation, an dem der letzte Turn unterbrochen wurde.

Wenn ein aktiver Dialog vorhanden ist, wird er von der _continueDialog_-Methode des Dialogkontexts unter Verwendung der Benutzereingabe, durch die dieser Turn ausgelöst wurde, fortgesetzt. Andernfalls ruft der Bot die _beginDialog_-Methode des Dialogkontexts auf, um einen Dialog zu starten.

Zum Schluss rufen wir die _saveChanges_-Methode für die Zustandsverwaltungsobjekte auf, um alle Änderungen zu speichern, die bei diesem Turn aufgetreten sind.

### <a name="about-dialog-and-bot-state"></a>Informationen zu Dialogen und zum Botzustand

In diesem Bot haben wir zwei Zustandseigenschaftenaccessoren definiert:

* Ein Accessor wurde im Konversationszustand für die Dialogzustandseigenschaft erstellt. Der Dialogzustand verfolgt nach, an welcher Stelle sich der Benutzer innerhalb der Dialoge eines Dialogsatzes befindet, und wird vom Dialogkontext aktualisiert (z. B. wenn wir die beginDialog- oder continueDialog-Methoden aufrufen).
* Ein Accessor wurde im Benutzerzustand für die Benutzerprofileigenschaft erstellt. Der Bot verwendet den Benutzerzustand zum Nachverfolgen von Informationen zum Benutzer. Diesen Zustand verwalten wir explizit in unserem Botcode.

Die _get_- und _set_-Methoden eines Zustandseigenschaftenaccessors rufen den Wert der Eigenschaft ab und legen ihn im Cache des Zustandsverwaltungsobjekts fest. Der Cache wird aufgefüllt, wenn der Wert einer Zustandseigenschaft erstmals in einem Turn angefordert wird, er muss jedoch explizit gespeichert werden. Um Änderungen an diesen beiden Zustandseigenschaften beizubehalten, rufen wir die _saveChanges_-Methode des entsprechenden Zustandsverwaltungsobjekts auf.

## <a name="initialize-your-bot-and-define-your-dialog"></a>Initialisieren Ihres Bots und Definieren des Dialogs

Die einfache Konversation in unserem Beispiel wird in Form einer Reihe von Fragen modelliert, die dem Benutzer gestellt werden. Die Schritte in den C#- und JavaScript-Versionen unterscheiden sich geringfügig:

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

1. Nach dem Namen des Benutzers fragen
1. Den Benutzer fragen, ob er bereit ist, sein Alter anzugeben
1. Wenn dies der Fall ist, nach dem Alter fragen, andernfalls diesen Schritt überspringen
1. Fragen, ob die erfassten Informationen korrekt sind
1. Eine Statusmeldung senden und den Dialog beenden

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Für den `who_are_you`-Dialog:

1. Nach dem Namen des Benutzers fragen
1. Den Benutzer fragen, ob er bereit ist, sein Alter anzugeben
1. Wenn dies der Fall ist, nach dem Alter fragen, andernfalls diesen Schritt überspringen
1. Eine Statusmeldung senden und den Dialog beenden

Für den `hello_user`-Dialog:

1. Die Benutzerinformationen anzeigen, die vom Bot erfasst wurden

---

Hier sind einige Punkte angegeben, die Sie beim Definieren Ihrer eigenen Wasserfallschritte bedenken sollten.

* Jeder Turn des Bots spiegelt die Eingabe des Benutzers wider und wird von einer Antwort des Bots gefolgt. Sie fordern den Benutzer also am Ende eines Wasserfallschritts zur Eingabe auf und erhalten die Antwort im nächsten Wasserfallschritt.
* Jede Eingabeaufforderung ist im Grunde ein aus zwei Schritten bestehender Dialog, der die zugehörige Eingabeaufforderung darstellt und eine Schleife ausführt, bis eine „gültige“ Eingabe empfangen wird. 

In diesem Beispiel wird der Dialog in der BOT-Datei definiert und im Konstruktor des Bots initialisiert.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Definieren Sie eine Instanzeigenschaft für den Dialogsatz.

```csharp
// The DialogSet that contains all the Dialogs that can be used at runtime.
private DialogSet _dialogs;
```

Erstellen Sie den Dialogsatz im Konstruktor des Bots, und fügen Sie dem Satz die Eingabeaufforderungen und den Wasserfalldialog hinzu.

```csharp
public MultiTurnPromptsBot(MultiTurnPromptsBotAccessors accessors)
{
    _accessors = accessors ?? throw new ArgumentNullException(nameof(accessors));

    // The DialogSet needs a DialogState accessor, it will call it when it has a turn context.
    _dialogs = new DialogSet(accessors.ConversationDialogState);

    // This array defines how the Waterfall will execute.
    var waterfallSteps = new WaterfallStep[]
    {
        NameStepAsync,
        NameConfirmStepAsync,
        AgeStepAsync,
        ConfirmStepAsync,
        SummaryStepAsync,
    };

    // Add named dialogs to the DialogSet. These names are saved in the dialog state.
    _dialogs.Add(new WaterfallDialog("details", waterfallSteps));
    _dialogs.Add(new TextPrompt("name"));
    _dialogs.Add(new NumberPrompt<int>("age"));
    _dialogs.Add(new ConfirmPrompt("confirm"));
}
```

In diesem Beispiel definieren wir jeden Schritt als separate Methode. Sie können die Schritte auch mithilfe von Lambdaausdrücken inline im Konstruktor definieren.

```csharp
private static async Task<DialogTurnResult> NameStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
{
    // WaterfallStep always finishes with the end of the Waterfall or with another dialog; here it is a Prompt Dialog.
    // Running a prompt here means the next WaterfallStep will be run when the users response is received.
    return await stepContext.PromptAsync("name", new PromptOptions { Prompt = MessageFactory.Text("Please enter your name.") }, cancellationToken);
}

private async Task<DialogTurnResult> NameConfirmStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
{
    // Get the current profile object from user state.
    var userProfile = await _accessors.UserProfile.GetAsync(stepContext.Context, () => new UserProfile(), cancellationToken);

    // Update the profile.
    userProfile.Name = (string)stepContext.Result;

    // We can send messages to the user at any point in the WaterfallStep.
    await stepContext.Context.SendActivityAsync(MessageFactory.Text($"Thanks {stepContext.Result}."), cancellationToken);

    // WaterfallStep always finishes with the end of the Waterfall or with another dialog; here it is a Prompt Dialog.
    return await stepContext.PromptAsync("confirm", new PromptOptions { Prompt = MessageFactory.Text("Would you like to give your age?") }, cancellationToken);
}

private async Task<DialogTurnResult> AgeStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
{
    if ((bool)stepContext.Result)
    {
        // User said "yes" so we will be prompting for the age.

        // Get the current profile object from user state.
        var userProfile = await _accessors.UserProfile.GetAsync(stepContext.Context, () => new UserProfile(), cancellationToken);

        // WaterfallStep always finishes with the end of the Waterfall or with another dialog, here it is a Prompt Dialog.
        return await stepContext.PromptAsync("age", new PromptOptions { Prompt = MessageFactory.Text("Please enter your age.") }, cancellationToken);
    }
    else
    {
        // User said "no" so we will skip the next step. Give -1 as the age.
        return await stepContext.NextAsync(-1, cancellationToken);
    }
}


private async Task<DialogTurnResult> ConfirmStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
{
    // Get the current profile object from user state.
    var userProfile = await _accessors.UserProfile.GetAsync(stepContext.Context, () => new UserProfile(), cancellationToken);

    // Update the profile.
    userProfile.Age = (int)stepContext.Result;

    // We can send messages to the user at any point in the WaterfallStep.
    if (userProfile.Age == -1)
    {
        await stepContext.Context.SendActivityAsync(MessageFactory.Text($"No age given."), cancellationToken);
    }
    else
    {
        // We can send messages to the user at any point in the WaterfallStep.
        await stepContext.Context.SendActivityAsync(MessageFactory.Text($"I have your age as {userProfile.Age}."), cancellationToken);
    }

    // WaterfallStep always finishes with the end of the Waterfall or with another dialog, here it is a Prompt Dialog.
    return await stepContext.PromptAsync("confirm", new PromptOptions { Prompt = MessageFactory.Text("Is this ok?") }, cancellationToken);
}

private async Task<DialogTurnResult> SummaryStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
{
    if ((bool)stepContext.Result)
    {
        // Get the current profile object from user state.
        var userProfile = await _accessors.UserProfile.GetAsync(stepContext.Context, () => new UserProfile(), cancellationToken);

        // We can send messages to the user at any point in the WaterfallStep.
        if (userProfile.Age == -1)
        {
            await stepContext.Context.SendActivityAsync(MessageFactory.Text($"I have your name as {userProfile.Name}."), cancellationToken);
        }
        else
        {
            await stepContext.Context.SendActivityAsync(MessageFactory.Text($"I have your name as {userProfile.Name} and age as {userProfile.Age}."), cancellationToken);
        }
    }
    else
    {
        // We can send messages to the user at any point in the WaterfallStep.
        await stepContext.Context.SendActivityAsync(MessageFactory.Text("Thanks. Your profile will not be kept."), cancellationToken);
    }

    // WaterfallStep always finishes with the end of the Waterfall or with another dialog, here it is the end.
    return await stepContext.EndDialogAsync(cancellationToken: cancellationToken);
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

In diesem Beispiel wird der Wasserfalldialog in der Datei **bot.js** definiert.

Definieren Sie die Bezeichner, die für die Zustandseigenschaftenaccessoren, Eingabeaufforderungen und Dialoge verwendet werden sollen.

```javascript
const DIALOG_STATE_PROPERTY = 'dialogState';
const USER_PROFILE_PROPERTY = 'user';

const WHO_ARE_YOU = 'who_are_you';
const HELLO_USER = 'hello_user';

const NAME_PROMPT = 'name_prompt';
const CONFIRM_PROMPT = 'confirm_prompt';
const AGE_PROMPT = 'age_prompt';
```

Definieren und erstellen Sie den Dialogsatz im Konstruktor des Bots, und fügen Sie ihm die Eingabeaufforderungen und Wasserfalldialoge hinzu.
Die `NumberPrompt` enthält eine benutzerdefinierte Validierung, um sicherzustellen, dass der Benutzer ein Alter größer als 0 eingibt.

```javascript
constructor(conversationState, userState) {
    // Create a new state accessor property. See https://aka.ms/about-bot-state-accessors to learn more about bot state and state accessors.
    this.conversationState = conversationState;
    this.userState = userState;

    this.dialogState = this.conversationState.createProperty(DIALOG_STATE_PROPERTY);

    this.userProfile = this.userState.createProperty(USER_PROFILE_PROPERTY);

    this.dialogs = new DialogSet(this.dialogState);

    // Add prompts that will be used by the main dialogs.
    this.dialogs.add(new TextPrompt(NAME_PROMPT));
    this.dialogs.add(new ChoicePrompt(CONFIRM_PROMPT));
    this.dialogs.add(new NumberPrompt(AGE_PROMPT, async (prompt) => {
        if (prompt.recognized.succeeded) {
            if (prompt.recognized.value <= 0) {
                await prompt.context.sendActivity(`Your age can't be less than zero.`);
                return false;
            } else {
                return true;
            }
        }
        return false;
    }));

    // Create a dialog that asks the user for their name.
    this.dialogs.add(new WaterfallDialog(WHO_ARE_YOU, [
        this.promptForName.bind(this),
        this.confirmAgePrompt.bind(this),
        this.promptForAge.bind(this),
        this.captureAge.bind(this)
    ]));

    // Create a dialog that displays a user name after it has been collected.
    this.dialogs.add(new WaterfallDialog(HELLO_USER, [
        this.displayProfile.bind(this)
    ]));
}
```

Da unsere Dialogschrittmethoden auf Instanzeigenschaften verweisen, müssen wir die `bind`-Methode verwenden, damit das `this`-Objekt in jeder Schrittmethode korrekt aufgelöst wird.

In diesem Beispiel definieren wir jeden Schritt als separate Methode. Sie können die Schritte auch mithilfe von Lambdaausdrücken inline im Konstruktor definieren.

```javascript
// This step in the dialog prompts the user for their name.
async promptForName(step) {
    return await step.prompt(NAME_PROMPT, `What is your name, human?`);
}

// This step captures the user's name, then prompts whether or not to collect an age.
async confirmAgePrompt(step) {
    const user = await this.userProfile.get(step.context, {});
    user.name = step.result;
    await this.userProfile.set(step.context, user);
    await step.prompt(CONFIRM_PROMPT, 'Do you want to give your age?', ['yes', 'no']);
}

// This step checks the user's response - if yes, the bot will proceed to prompt for age.
// Otherwise, the bot will skip the age step.
async promptForAge(step) {
    if (step.result && step.result.value === 'yes') {
        return await step.prompt(AGE_PROMPT, `What is your age?`,
            {
                retryPrompt: 'Sorry, please specify your age as a positive number or say cancel.'
            }
        );
    } else {
        return await step.next(-1);
    }
}

// This step captures the user's age.
async captureAge(step) {
    const user = await this.userProfile.get(step.context, {});
    if (step.result !== -1) {
        user.age = step.result;
        await this.userProfile.set(step.context, user);
        await step.context.sendActivity(`I will remember that you are ${ step.result } years old.`);
    } else {
        await step.context.sendActivity(`No age given.`);
    }
    return await step.endDialog();
}

// This step displays the captured information back to the user.
async displayProfile(step) {
    const user = await this.userProfile.get(step.context, {});
    if (user.age) {
        await step.context.sendActivity(`Your name is ${ user.name } and you are ${ user.age } years old.`);
    } else {
        await step.context.sendActivity(`Your name is ${ user.name } and you did not share your age.`);
    }
    return await step.endDialog();
}
```

---

In diesem Beispiel wird der Zustand des Benutzerprofils innerhalb des Dialogs aktualisiert. Diese Vorgehensweise kann für einen einfachen Bot geeignet sein, funktioniert aber nicht, wenn Sie einen Dialog in mehreren Bots wiederverwenden möchten.

Zum Trennen der Dialogschritte und des Botzustands stehen verschiedene Optionen zur Verfügung. Nachdem der Dialog vollständige Informationen erfasst hat, können Sie beispielsweise wie folgt vorgehen:

* Stellen Sie die erfassten Daten mithilfe der _endDialog_-Methode als Rückgabewert für den übergeordneten Kontext bereit. Hierbei kann es sich um den Turn-Handler des Bots oder einen früheren aktiven Dialog im Dialogstapel handeln. Auf diese Weise werden die Eingabeaufforderungsklassen entworfen.
* Generieren Sie eine Anforderung an einen geeigneten Dienst. Dies kann gut funktionieren, wenn Ihr Bot als Front-End für einen umfangreicheren Dienst fungiert.

## <a name="test-your-dialog"></a>Testen des Dialogs

Erstellen Sie Ihren Bot lokal, führen Sie ihn lokal aus, und interagieren Sie dann mithilfe des Emulators mit dem Bot.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

1. Der Bot sendet eine erste Begrüßungsnachricht als Antwort auf die conversationUpdate-Aktivität, in der der Benutzer der Konversation hinzugefügt wird.
1. Geben Sie `hi` oder einen anderen Text ein. Da in diesem Turn noch kein aktiver Dialog vorhanden ist, startet der Bot den `details`-Dialog.
   * Der Bot sendet die erste Eingabeaufforderung des Dialogs und wartet auf weitere Eingaben.
1. Beantworten Sie die vom Bot gestellten Fragen, um den Dialog zu durchlaufen.
1. Im letzten Schritt des Dialogs wird basierend auf Ihren Eingaben eine `Thanks`-Nachricht gesendet.
   * Wenn der Dialog endet, wird er aus dem Dialogstapel entfernt, und im Bot ist kein aktiver Dialog mehr vorhanden.
1. Geben Sie `hi` oder einen anderen Text ein, um den Dialog erneut zu starten.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

1. Der Bot sendet eine erste Begrüßungsnachricht als Antwort auf die conversationUpdate-Aktivität, in der der Benutzer der Konversation hinzugefügt wird.
1. Geben Sie `hi` oder einen anderen Text ein. Da in diesem Turn noch kein aktiver Dialog und kein Benutzerprofil vorhanden sind, startet der Bot den `who_are_you`-Dialog.
   * Der Bot sendet die erste Eingabeaufforderung des Dialogs und wartet auf weitere Eingaben.
1. Beantworten Sie die vom Bot gestellten Fragen, um den Dialog zu durchlaufen.
1. Im letzten Schritt des Dialogs wird eine kurze Bestätigungsnachricht gesendet.
1. Geben Sie `hi` oder einen anderen Text ein.
   * Der Bot startet den aus einem Schritt bestehenden `hello_user`-Dialog, der Informationen aus den erfassten Daten anzeigt und dann sofort endet.

---

## <a name="additional-resources"></a>Zusätzliche Ressourcen
Sie können die integrierte Validierung für die einzelnen Eingabeaufforderungstypen wie hier gezeigt verwenden oder der Eingabeaufforderung eine eigene benutzerdefinierte Validierung hinzufügen. Weitere Informationen finden Sie unter [Erfassen von Benutzereingaben mit einer Dialogaufforderung](bot-builder-prompts.md).

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erstellen eines erweiterten Konversationsablaufs mithilfe von Branches und Schleifen](bot-builder-dialog-manage-complex-conversation-flow.md)
