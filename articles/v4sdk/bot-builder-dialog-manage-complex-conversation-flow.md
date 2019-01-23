---
title: Erstellen eines erweiterten Konversationsflusses mithilfe von Verzweigungen und Schleifen | Microsoft-Dokumentation
description: Hier wird beschrieben, wie Sie einen komplexen Konversationsfluss mit Dialogen im Bot Framework SDK verwalten.
keywords: komplexer Konversationsablauf, Wiederholung, Schleife, Menü, Dialoge, Eingabeaufforderungen, Wasserfälle, Dialogsatz
author: JonathanFingold
ms.author: v-jofing
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 11/28/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 27bcd53e9f3d582a1206c5ed74cc844acab795b4
ms.sourcegitcommit: b15cf37afc4f57d13ca6636d4227433809562f8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/11/2019
ms.locfileid: "54225885"
---
# <a name="create-advanced-conversation-flow-using-branches-and-loops"></a>Erstellen eines erweiterten Konversationsflusses mithilfe von Verzweigungen und Schleifen

[!INCLUDE [pre-release-label](~/includes/pre-release-label.md)]

In diesem Artikel wird veranschaulicht, wie Sie komplexe Konversationen mit Verzweigungen und Schleifen verwalten. Außerdem wird gezeigt, wie Sie Argumente zwischen unterschiedlichen Teilen des Dialogs übergeben.

## <a name="prerequisites"></a>Voraussetzungen

- [Bot Framework-Emulator](https://github.com/Microsoft/BotFramework-Emulator/blob/master/README.md#download)
- Der Code in diesem Artikel basiert auf dem Beispiel für einen komplexen Dialog (**complex dialog**). Sie benötigen eine Kopie des Beispiels im [C#](https://aka.ms/cs-complex-dialog-sample)- oder [JS](https://aka.ms/js-complex-dialog-sample)-Format.
- Sie müssen mit den [Bot-Grundlagen](bot-builder-basics.md), der [Dialogbibliothek](bot-builder-concept-dialog.md), dem [Dialogzustand ](bot-builder-dialog-state.md) und den [BOT](bot-file-basics.md)-Dateien vertraut sein.

## <a name="about-the-sample"></a>Über das Beispiel

In diesem Beispiel wird ein Bot zum Registrieren von Benutzern verwendet, bei dem bis zu zwei Unternehmen bewertet werden können, die in einer Liste enthalten sind.

- Der Bot fragt nach dem Namen und Alter des Benutzers, und dann folgt eine _Verzweigung_, die je nach Benutzeralter den weiteren Ablauf bestimmt.
  - Wenn der Benutzer zu jung ist, wird der Benutzer nicht gebeten, die Unternehmen zu bewerten.
  - Wenn der Benutzer alt genug ist, wird danach gefragt, welche Bewertungen er durchführen möchte.
    - Der Benutzer kann ein Unternehmen für die Bewertung auswählen.
    - Wenn er ein Unternehmen auswählt, wird ein _Schleifenvorgang_ durchgeführt, damit er ein zweites Unternehmen auswählen kann.
- Abschließend wird dem Benutzer für die Teilnahme gedankt.

Es werden zwei Wasserfalldialoge und einige Eingabeaufforderungen verwendet, um die komplexe Konversation zu verwalten.

## <a name="configure-state-for-your-bot"></a>Konfigurieren des Zustands für Ihren Bot

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Definieren Sie die Benutzerinformationen, die erfasst werden sollen.

```csharp
public class UserProfile
{
    public string Name { get; set; }
    public int Age { get; set; }

    //The list of companies the user wants to review.
    public List<string> CompaniesToReview { get; set; } = new List<string>();
}
```

Definieren Sie die Klasse, die die Objekte für die Zustandsverwaltung und die Zustandseigenschaftenaccessoren für den Bot enthält.

```csharp
public class ComplexDialogBotAccessors
{
    public ComplexDialogBotAccessors(ConversationState conversationState, UserState userState)
    {
        ConversationState = conversationState ?? throw new ArgumentNullException(nameof(conversationState));
        UserState = userState ?? throw new ArgumentNullException(nameof(userState));
    }

    public IStatePropertyAccessor<DialogState> DialogStateAccessor { get; set; }
    public IStatePropertyAccessor<UserProfile> UserProfileAccessor { get; set; }

    public ConversationState ConversationState { get; }
    public UserState UserState { get; }
}
```

Erstellen Sie das Objekt für die Zustandsverwaltung, und registrieren Sie die Accessorklasse in der `ConfigureServices`-Methode der `Statup`-Klasse.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Register the bot.

    // Create conversation and user state management objects, using memory storage.
    IStorage dataStore = new MemoryStorage();
    var conversationState = new ConversationState(dataStore);
    var userState = new UserState(dataStore);

    // Create and register state accessors.
    // Accessors created here are passed into the IBot-derived class on every turn.
    services.AddSingleton<ComplexDialogBotAccessors>(sp =>
    {
        // Create the custom state accessor.
        // State accessors enable other components to read and write individual properties of state.
        var accessors = new ComplexDialogBotAccessors(conversationState, userState)
        {
            DialogStateAccessor = conversationState.CreateProperty<DialogState>("DialogState"),
            UserProfileAccessor = userState.CreateProperty<UserProfile>("UserProfile"),
        };

        return accessors;
    });
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

In der Datei **index.js** definieren wir die Objekte für die Zustandsverwaltung.

```javascript
const { BotFrameworkAdapter, MemoryStorage, UserState, ConversationState } = require('botbuilder');

// ...

// Define state store for your bot.
const memoryStorage = new MemoryStorage();

// Create user and conversation state with in-memory storage provider.
const userState = new UserState(memoryStorage);
const conversationState = new ConversationState(memoryStorage);

// Create the bot.
const myBot = new MyBot(conversationState, userState);
```

Der Konstruktor des Bots erstellt die Zustandseigenschaftenaccessoren für den Bot.

---

## <a name="initialize-your-bot"></a>Initialisieren Ihres Bots

Erstellen Sie einen _Dialogsatz_ für den Bot, dem wir dann alle Dialoge für dieses Beispiel hinzufügen können.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Erstellen Sie den Dialogsatz im Konstruktor des Bots, und fügen Sie dem Satz die Eingabeaufforderungen und die beiden Wasserfalldialoge hinzu.

Wir definieren jeden Schritt hier als separate Methode. Die Implementierung führen wir im nächsten Abschnitt durch.

```csharp
public class ComplexDialogBot : IBot
{
    // Define constants for the bot...

    // Define properties for the bot's accessors and dialog set.
    private readonly ComplexDialogBotAccessors _accessors;
    private readonly DialogSet _dialogs;

    // Initialize the bot and add dialogs and prompts to the dialog set.
    public ComplexDialogBot(ComplexDialogBotAccessors accessors)
    {
        _accessors = accessors ?? throw new ArgumentNullException(nameof(accessors));

        // Create a dialog set for the bot. It requires a DialogState accessor, with which
        // to retrieve the dialog state from the turn context.
        _dialogs = new DialogSet(accessors.DialogStateAccessor);

        // Add the prompts we need to the dialog set.
        _dialogs
            .Add(new TextPrompt(NamePrompt))
            .Add(new NumberPrompt<int>(AgePrompt))
            .Add(new ChoicePrompt(SelectionPrompt));

        // Add the dialogs we need to the dialog set.
        _dialogs.Add(new WaterfallDialog(TopLevelDialog)
            .AddStep(NameStepAsync)
            .AddStep(AgeStepAsync)
            .AddStep(StartSelectionStepAsync)
            .AddStep(AcknowledgementStepAsync));

        _dialogs.Add(new WaterfallDialog(ReviewSelectionDialog)
            .AddStep(SelectionStepAsync)
            .AddStep(LoopStepAsync));
    }

    // Turn handler and other supporting methods...
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Definieren und erstellen Sie in der Datei **bot.js** den Dialogsatz im Konstruktor des Bots, und fügen Sie ihm die Eingabeaufforderungen und Wasserfalldialoge hinzu.

Wir definieren jeden Schritt hier als separate Methode. Die Implementierung führen wir im nächsten Abschnitt durch.

```javascript
const { ActivityTypes } = require('botbuilder');
const { DialogSet, WaterfallDialog, TextPrompt, NumberPrompt, ChoicePrompt, DialogTurnStatus } = require('botbuilder-dialogs');

// Define constants for the bot...

class MyBot {
    constructor(conversationState, userState) {
        // Create the state property accessors and save the state management objects.
        this.dialogStateAccessor = conversationState.createProperty(DIALOG_STATE_PROPERTY);
        this.userProfileAccessor = userState.createProperty(USER_PROFILE_PROPERTY);
        this.conversationState = conversationState;
        this.userState = userState;

        // Create a dialog set for the bot. It requires a DialogState accessor, with which
        // to retrieve the dialog state from the turn context.
        this.dialogs = new DialogSet(this.dialogStateAccessor);

        // Add the prompts we need to the dialog set.
        this.dialogs
            .add(new TextPrompt(NAME_PROMPT))
            .add(new NumberPrompt(AGE_PROMPT))
            .add(new ChoicePrompt(SELECTION_PROMPT));

        // Add the dialogs we need to the dialog set.
        this.dialogs.add(new WaterfallDialog(TOP_LEVEL_DIALOG)
            .addStep(this.nameStep.bind(this))
            .addStep(this.ageStep.bind(this))
            .addStep(this.startSelectionStep.bind(this))
            .addStep(this.acknowledgementStep.bind(this)));

        this.dialogs.add(new WaterfallDialog(REVIEW_SELECTION_DIALOG)
            .addStep(this.selectionStep.bind(this))
            .addStep(this.loopStep.bind(this)));
    }

    // Turn handler and other supporting methods...
}
```

---

## <a name="implement-the-steps-for-the-waterfall-dialogs"></a>Implementieren der Schritte für die Wasserfalldialoge

Wir implementieren jetzt die Schritte für unsere beiden Dialoge.

### <a name="the-top-level-dialog"></a>Dialog der obersten Ebene

Der erste Dialog der obersten Ebene umfasst vier Schritte:

1. Fragen nach dem Namen des Benutzers
1. Fragen nach dem Alter des Benutzers
1. Verzweigung basierend auf dem Alter des Benutzers
1. Abschließendes Bedanken beim Benutzer für die Teilnahme und Zurückgeben der erfassten Informationen

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
// The first step of the top-level dialog.
private static async Task<DialogTurnResult> NameStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
{
    // Create an object in which to collect the user's information within the dialog.
    stepContext.Values[UserInfo] = new UserProfile();

    // Ask the user to enter their name.
    return await stepContext.PromptAsync(
        NamePrompt,
        new PromptOptions { Prompt = MessageFactory.Text("Please enter your name.") },
        cancellationToken);
}

// The second step of the top-level dialog.
private async Task<DialogTurnResult> AgeStepAsync(
    WaterfallStepContext stepContext,
    CancellationToken cancellationToken)
{
    // Set the user's name to what they entered in response to the name prompt.
    ((UserProfile)stepContext.Values[UserInfo]).Name = (string)stepContext.Result;

    // Ask the user to enter their age.
    return await stepContext.PromptAsync(
        AgePrompt,
        new PromptOptions { Prompt = MessageFactory.Text("Please enter your age.") },
        cancellationToken);
}

// The third step of the top-level dialog.
private async Task<DialogTurnResult> StartSelectionStepAsync(
    WaterfallStepContext stepContext,
    CancellationToken cancellationToken)
{
    // Set the user's age to what they entered in response to the age prompt.
    int age = (int)stepContext.Result;
    ((UserProfile)stepContext.Values[UserInfo]).Age = age;

    if (age < 25)
    {
        // If they are too young, skip the review-selection dialog, and pass an empty list to the next step.
        await stepContext.Context.SendActivityAsync(
            MessageFactory.Text("You must be 25 or older to participate."),
            cancellationToken);
        return await stepContext.NextAsync(new List<string>(), cancellationToken);
    }
    else
    {
        // Otherwise, start the review-selection dialog.
        return await stepContext.BeginDialogAsync(ReviewSelectionDialog, null, cancellationToken);
    }
}

// The final step of the top-level dialog.
private async Task<DialogTurnResult> AcknowledgementStepAsync(
    WaterfallStepContext stepContext,
    CancellationToken cancellationToken)
{
    // Set the user's company selection to what they entered in the review-selection dialog.
    List<string> list = stepContext.Result as List<string>;
    ((UserProfile)stepContext.Values[UserInfo]).CompaniesToReview = list ?? new List<string>();

    // Thank them for participating.
    await stepContext.Context.SendActivityAsync(
        MessageFactory.Text($"Thanks for participating, {((UserProfile)stepContext.Values[UserInfo]).Name}."),
        cancellationToken);

    // Exit the dialog, returning the collected user information.
    return await stepContext.EndDialogAsync(stepContext.Values[UserInfo], cancellationToken);
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
async nameStep(stepContext) {
    // Create an object in which to collect the user's information within the dialog.
    stepContext.values[USER_INFO] = {};

    // Ask the user to enter their name.
    return await stepContext.prompt(NAME_PROMPT, 'Please enter your name.');
}

async ageStep(stepContext) {
    // Set the user's name to what they entered in response to the name prompt.
    stepContext.values[USER_INFO].name = stepContext.result;

    // Ask the user to enter their age.
    return await stepContext.prompt(AGE_PROMPT, 'Please enter your age.');
}

async startSelectionStep(stepContext) {
    // Set the user's age to what they entered in response to the age prompt.
    stepContext.values[USER_INFO].age = stepContext.result;

    if (stepContext.result < 25) {
        // If they are too young, skip the review-selection dialog, and pass an empty list to the next step.
        await stepContext.context.sendActivity('You must be 25 or older to participate.');
        return await stepContext.next([]);
    } else {
        // Otherwise, start the review-selection dialog.
        return await stepContext.beginDialog(REVIEW_SELECTION_DIALOG);
    }
}

async acknowledgementStep(stepContext) {
    // Set the user's company selection to what they entered in the review-selection dialog.
    const list = stepContext.result || [];
    stepContext.values[USER_INFO].companiesToReview = list;

    // Thank them for participating.
    await stepContext.context.sendActivity(`Thanks for participating, ${stepContext.values[USER_INFO].name}.`);

    // Exit the dialog, returning the collected user information.
    return await stepContext.endDialog(stepContext.values[USER_INFO]);
}
```

---

### <a name="the-review-selection-dialog"></a>Dialog zum Überprüfen der Auswahl

Der Dialog zum Überprüfen der Auswahl umfasst zwei Schritte:

1. Auffordern des Benutzers zum Auswählen eines zu bewertenden Unternehmens oder Auswahl von `done`, um den Vorgang zu beenden
1. Wiederholen dieses Dialogs je nach Bedarf oder Beenden des Vorgangs

Bei diesem Aufbau ist der Dialog der obersten Ebene im Stapel immer vor dem Dialog zur Überprüfung der Auswahl angeordnet. Letzterer kann ein untergeordnetes Element des Dialogs der obersten Ebene sein.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
// The first step of the review-selection dialog.
private async Task<DialogTurnResult> SelectionStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
{
    // Continue using the same selection list, if any, from the previous iteration of this dialog.
    List<string> list = stepContext.Options as List<string> ?? new List<string>();
    stepContext.Values[CompaniesSelected] = list;

    // Create a prompt message.
    string message;
    if (list.Count is 0)
    {
        message = $"Please choose a company to review, or `{DoneOption}` to finish.";
    }
    else
    {
        message = $"You have selected **{list[0]}**. You can review an additional company, " +
            $"or choose `{DoneOption}` to finish.";
    }

    // Create the list of options to choose from.
    List<string> options = _companyOptions.ToList();
    options.Add(DoneOption);
    if (list.Count > 0)
    {
        options.Remove(list[0]);
    }

    // Prompt the user for a choice.
    return await stepContext.PromptAsync(
        SelectionPrompt,
        new PromptOptions
        {
            Prompt = MessageFactory.Text(message),
            RetryPrompt = MessageFactory.Text("Please choose an option from the list."),
            Choices = ChoiceFactory.ToChoices(options),
        },
        cancellationToken);
}

// The final step of the review-selection dialog.
private async Task<DialogTurnResult> LoopStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
{
    // Retrieve their selection list, the choice they made, and whether they chose to finish.
    List<string> list = stepContext.Values[CompaniesSelected] as List<string>;
    FoundChoice choice = (FoundChoice)stepContext.Result;
    bool done = choice.Value == DoneOption;

    if (!done)
    {
        // If they chose a company, add it to the list.
        list.Add(choice.Value);
    }

    if (done || list.Count is 2)
    {
        // If they're done, exit and return their list.
        return await stepContext.EndDialogAsync(list, cancellationToken);
    }
    else
    {
        // Otherwise, repeat this dialog, passing in the list from this iteration.
        return await stepContext.ReplaceDialogAsync(ReviewSelectionDialog, list, cancellationToken);
    }
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
async selectionStep(stepContext) {
    // Continue using the same selection list, if any, from the previous iteration of this dialog.
    const list = Array.isArray(stepContext.options) ? stepContext.options : [];
    stepContext.values[COMPANIES_SELECTED] = list;

    // Create a prompt message.
    let message;
    if (list.length === 0) {
        message = 'Please choose a company to review, or `' + DONE_OPTION + '` to finish.';
    } else {
        message = `You have selected **${list[0]}**. You can review an addition company, ` +
            'or choose `' + DONE_OPTION + '` to finish.';
    }

    // Create the list of options to choose from.
    const options = list.length > 0
        ? COMPANY_OPTIONS.filter(function (item) { return item !== list[0] })
        : COMPANY_OPTIONS.slice();
    options.push(DONE_OPTION);

    // Prompt the user for a choice.
    return await stepContext.prompt(SELECTION_PROMPT, {
        prompt: message,
        retryPrompt: 'Please choose an option from the list.',
        choices: options
    });
}

async loopStep(stepContext) {
    // Retrieve their selection list, the choice they made, and whether they chose to finish.
    const list = stepContext.values[COMPANIES_SELECTED];
    const choice = stepContext.result;
    const done = choice.value === DONE_OPTION;

    if (!done) {
        // If they chose a company, add it to the list.
        list.push(choice.value);
    }

    if (done || list.length > 1) {
        // If they're done, exit and return their list.
        return await stepContext.endDialog(list);
    } else {
        // Otherwise, repeat this dialog, passing in the list from this iteration.
        return await stepContext.replaceDialog(REVIEW_SELECTION_DIALOG, list);
    }
}
```

---

## <a name="update-the-bots-turn-handler"></a>Aktualisieren des Turn-Handlers für den Bot

Mit dem Turn-Handler des Bots wird der Konversationsfluss wiederholt, der für diese Dialoge definiert ist.
Beim Empfang einer Nachricht vom Benutzer:

1. Setzen Sie den aktiven Dialog, falls vorhanden, fort.
   - Wenn kein Dialog aktiv war, löschen wir das Benutzerprofil und starten den Dialog der obersten Ebene.
   - Wenn der aktive Dialog abgeschlossen wurde, erfassen und speichern wir die zurückgegebenen Informationen und zeigen eine Statusmeldung an.
   - Andernfalls befindet sich der aktive Dialog noch mitten im Prozess, und wir müssen noch keine Schritte ausführen.
1. Speichern Sie den Zustand der Konversation, damit alle Aktualisierungen des Dialogzustands beibehalten werden.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
public async Task OnTurnAsync(ITurnContext turnContext, CancellationToken cancellationToken = default(CancellationToken))
{
    if (turnContext == null)
    {
        throw new ArgumentNullException(nameof(turnContext));
    }

    if (turnContext.Activity.Type == ActivityTypes.Message)
    {
        // Run the DialogSet - let the framework identify the current state of the dialog from
        // the dialog stack and figure out what (if any) is the active dialog.
        DialogContext dialogContext = await _dialogs.CreateContextAsync(turnContext, cancellationToken);
        DialogTurnResult results = await dialogContext.ContinueDialogAsync(cancellationToken);
        switch (results.Status)
        {
            case DialogTurnStatus.Cancelled:
            case DialogTurnStatus.Empty:
                // If there is no active dialog, we should clear the user info and start a new dialog.
                await _accessors.UserProfileAccessor.SetAsync(turnContext, new UserProfile(), cancellationToken);
                await _accessors.UserState.SaveChangesAsync(turnContext, false, cancellationToken);
                await dialogContext.BeginDialogAsync(TopLevelDialog, null, cancellationToken);
                break;

            case DialogTurnStatus.Complete:
                // If we just finished the dialog, capture and display the results.
                UserProfile userInfo = results.Result as UserProfile;
                string status = "You are signed up to review "
                    + (userInfo.CompaniesToReview.Count is 0
                        ? "no companies"
                        : string.Join(" and ", userInfo.CompaniesToReview))
                    + ".";
                await turnContext.SendActivityAsync(status);
                await _accessors.UserProfileAccessor.SetAsync(turnContext, userInfo, cancellationToken);
                await _accessors.UserState.SaveChangesAsync(turnContext, false, cancellationToken);
                break;

            case DialogTurnStatus.Waiting:
                // If there is an active dialog, we don't need to do anything here.
                break;
        }

        await _accessors.ConversationState.SaveChangesAsync(turnContext, false, cancellationToken);
    }

    // Processes ConversationUpdate Activities to welcome the user.
    else if (turnContext.Activity.Type == ActivityTypes.ConversationUpdate)
    {
        // Welcome new users...
    }
    else
    {
        // Give a default reply for all other activity types...
    }
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
async onTurn(turnContext) {
    if (turnContext.activity.type === ActivityTypes.Message) {
        // Run the DialogSet - let the framework identify the current state of the dialog from
        // the dialog stack and figure out what (if any) is the active dialog.
        const dialogContext = await this.dialogs.createContext(turnContext);
        const results = await dialogContext.continueDialog();
        switch (results.status) {
            case DialogTurnStatus.cancelled:
            case DialogTurnStatus.empty:
                // If there is no active dialog, we should clear the user info and start a new dialog.
                await this.userProfileAccessor.set(turnContext, {});
                await this.userState.saveChanges(turnContext);
                await dialogContext.beginDialog(TOP_LEVEL_DIALOG);
                break;
            case DialogTurnStatus.complete:
                // If we just finished the dialog, capture and display the results.
                const userInfo = results.result;
                const status = 'You are signed up to review '
                    + (userInfo.companiesToReview.length === 0 ? 'no companies' : userInfo.companiesToReview.join(' and '))
                    + '.';
                await turnContext.sendActivity(status);
                await this.userProfileAccessor.set(turnContext, userInfo);
                await this.userState.saveChanges(turnContext);
                break;
            case DialogTurnStatus.waiting:
                // If there is an active dialog, we don't need to do anything here.
                break;
        }
        await this.conversationState.saveChanges(turnContext);
    } else if (turnContext.activity.type === ActivityTypes.ConversationUpdate) {
        // Welcome new users...
    } else {
        // Give a default reply for all other activity types...
    }
}
```

---

## <a name="test-your-dialog"></a>Testen des Dialogs

1. Führen Sie das Beispiel lokal auf Ihrem Computer aus. Wenn Sie eine Anleitung benötigen, helfen Ihnen die INFODATEIEN zum [C#](https://aka.ms/cs-complex-dialog-sample)- bzw. [JS](https://aka.ms/js-complex-dialog-sample)-Beispiel weiter.
1. Verwenden Sie den Emulator, um den Bot wie unten dargestellt zu testen.

![Testen des Beispiels für einen komplexen Dialog (complex dialog)](~/media/emulator-v4/test-complex-dialog.png)

## <a name="additional-resources"></a>Zusätzliche Ressourcen

Eine Einführung in die Implementierung eines Dialogs finden Sie im Artikel [Implementieren eines sequenziellen Konversationsflusses](bot-builder-dialog-manage-conversation-flow.md). Darin werden ein einzelner Wasserfalldialog und einige Eingabeaufforderungen zum Erstellen einer einfachen Interaktion verwendet, bei der dem Benutzer einige Fragen gestellt werden.

Die Dialogbibliothek enthält eine einfache Validierung für Eingabeaufforderungen. Sie können auch eine benutzerdefinierte Validierung hinzufügen. Weitere Informationen finden Sie unter [Erfassen von Benutzereingaben mit einer Dialogaufforderung](bot-builder-prompts.md).

Zum Vereinfachen Ihres Dialogcodes und Wiederverwenden in mehreren Bots können Sie Teile eines Dialogsatzes als separate Klasse definieren.
Weitere Informationen finden Sie unter [Wiederverwenden von Dialogen](bot-builder-compositcontrol.md).

## <a name="next-steps"></a>Nächste Schritte

Sie können Bots erweitern, damit sie auf weitere Eingaben reagieren, z.B. „Hilfe“ oder „Abbrechen“, mit denen der normale Konversationsfluss unterbrochen wird.

> [!div class="nextstepaction"]
> [Behandeln von Benutzerunterbrechungen](bot-builder-howto-handle-user-interrupt.md)
