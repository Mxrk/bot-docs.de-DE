---
title: Auffordern von Benutzern zur Eingabe mithilfe der Dialogbibliothek | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Benutzer mithilfe der Dialogbibliothek im Bot Builder SDK für Node.js zur Eingabe auffordern.
keywords: Eingabeaufforderungen, Dialogfelder, AttachmentPrompt, ChoicePrompt, ConfirmPrompt, DatetimePrompt, NumberPrompt, TextPrompt, erneute Eingabeaufforderung, Überprüfung
author: v-ducvo
ms.author: v-ducvo
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 9/25/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 27066f76db29a82b4ab9dd75bf5eee01dcce3116
ms.sourcegitcommit: 3cb288cf2f09eaede317e1bc8d6255becf1aec61
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/27/2018
ms.locfileid: "47389709"
---
# <a name="prompt-users-for-input-using-the-dialogs-library"></a>Auffordern von Benutzern zur Eingabe mithilfe der Dialogbibliothek

[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

Das Sammeln von Informationen durch das Stellen von Fragen ist eine der Hauptvorgehensweisen, mit denen ein Bot mit Benutzern interagiert. Dies ist auch direkt möglich, indem die _send activity_-Methode des [turn context](bot-builder-concept-activity-processing.md#turn-context)-Objekts verwendet wird und dann die nächste eingehende Nachricht als Antwort verarbeitet wird. Über das Bot Builder SDK wird aber eine Bibliothek mit **Dialogen** bereitgestellt. Sie enthält Methoden, die zur Vereinfachung des Stellens von Fragen konzipiert sind und mit denen sichergestellt wird, dass die Antwort mit einem bestimmten Datentyp übereinstimmt oder benutzerdefinierte Validierungsregeln erfüllt. In diesem Thema wird ausführlich beschrieben, wie Sie dies erreichen, indem Sie **Eingabeaufforderungen** nutzen, um einen Benutzer zur Eingabe aufzufordern.

In diesem Artikel wird beschrieben, wie Eingabeaufforderungen in einem Dialogfeld verwendet werden. Informationen zur Verwendung von Dialogen im Allgemeinen finden Sie im Thema zur [Verwendung von Dialogen zum Verwalten eines einfachen Konversationsflusses](bot-builder-dialog-manage-conversation-flow.md).

## <a name="prompt-types"></a>Eingabeaufforderungstypen

Die Dialogbibliothek enthält viele verschiedene Eingabeaufforderungen, die jeweils zum Anfordern einer anderen Art von Antwort verwendet werden.

| Prompt | BESCHREIBUNG |
|:----|:----|
| **AttachmentPrompt** | Der Benutzer wird aufgefordert, eine Anlage anzugeben, z.B. ein Dokument oder ein Bild. |
| **ChoicePrompt** | Der Benutzer wird aufgefordert, aus einer Sammlung von Optionen auszuwählen. |
| **ConfirmPrompt** | Der Benutzer aufgefordert, seine Aktion zu bestätigen. |
| **DatetimePrompt** | Der Benutzer wird aufgefordert, eine Datums-/Uhrzeitangabe einzugeben. Benutzer können mithilfe von natürlicher Sprache antworten, z.B. „morgen um 20: 00 Uhr“ oder „Freitag um 10 Uhr“. Das Bot Framework SDK verwendet die vordefinierte LUIS `builtin.datetimeV2`-Entität. Weitere Informationen finden Sie unter [builtin.datetimev2](https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-prebuilt-entities#builtindatetimev2). |
| **NumberPrompt** | Der Benutzer wird aufgefordert, eine Zahl einzugeben. Der Benutzer kann z.B. mit „10“ oder „zehn“ antworten. Wenn die Antwort z.B. „zehn“ ist, konvertiert die Eingabeaufforderung die Antwort in eine Zahl und gibt `10` als Ergebnis zurück. |
| **TextPrompt** | Der Benutzer wird aufgefordert, eine Textzeichenfolge einzugeben. |

## <a name="add-references-to-prompt-library"></a>Hinzufügen von Verweisen zur Eingabeaufforderungsbibliothek

Sie können die Bibliothek mit den **Dialogen** abrufen, indem Sie Ihrem Bot das Paket **botbuilder-dialogs** hinzufügen. Dialoge werden im Thema zur [Verwendung von Dialogen zum Verwalten eines einfachen Konversationsflusses](bot-builder-dialog-manage-conversation-flow.md) ausführlich behandelt. Wir verwenden aber Dialoge für unsere Eingabeaufforderungen.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Installieren Sie das Paket **Microsoft.Bot.Builder.Dialogs** von NuGet.

Fügen Sie dann einen Verweis auf die Bibliothek aus Ihrem Botcode hinzu.

```cs
using Microsoft.Bot.Builder.Dialogs;
```

Sie müssen den Konversationsdialogzustand über Accessoren einrichten. Wir gehen auf den Code hier nicht näher ein. Weitere Informationen finden Sie im Artikel [Verwalten des Konversations- und Benutzerzustands](bot-builder-howto-v4-state.md).

Definieren Sie in Ihren Botoptionen in **Startup.cs** zuerst Ihre Zustandsobjekte, und fügen Sie dann das Singleton hinzu, um die Accessorklasse für den Botkonstruktor bereitzustellen. Mit der Klasse für `BotAccessor` werden einfach die Konversation und der Benutzerzustand sowie die jeweiligen Accessoren für diese Komponenten gespeichert. Die vollständige Klassendefinition ist im Beispiel unter dem Link am Ende dieses Artikels angegeben. 

```cs
    services.AddBot<MultiTurnPromptsBot>(options =>
    {
        InitCredentialProvider(options);

        // Create and add conversation state.
        var convoState = new ConversationState(dataStore);
        options.State.Add(convoState);

        // Create and add user state.
        var userState = new UserState(dataStore);
        options.State.Add(userState);
    });

    services.AddSingleton(sp =>
    {
        // We need to grab the conversationState we added on the options in the previous step
        var options = sp.GetRequiredService<IOptions<BotFrameworkOptions>>().Value;
        if (options == null)
        {
            throw new InvalidOperationException("BotFrameworkOptions must be configured prior to setting up the State Accessors");
        }

        var conversationState = options.State.OfType<ConversationState>().FirstOrDefault();
        if (conversationState == null)
        {
            throw new InvalidOperationException("ConversationState must be defined and added before adding conversation-scoped state accessors.");
        }

        var userState = options.State.OfType<UserState>().FirstOrDefault();
        if (userState == null)
        {
            throw new InvalidOperationException("UserState must be defined and added before adding user-scoped state accessors.");
        }

        // The dialogs will need a state store accessor. Creating it here once (on-demand) allows the dependency injection
        // to hand it to our IBot class that is create per-request.
        var accessors = new BotAccessors(conversationState, userState)
        {
            ConversationDialogState = conversationState.CreateProperty<DialogState>("DialogState"),
            UserProfile = userState.CreateProperty<UserProfile>("UserProfile"),
        };

        return accessors;
    });
```

Definieren Sie dann in Ihrem Botcode die folgenden Objekte für den Dialogsatz.

```cs
    private readonly BotAccessors _accessors;

    /// <summary>
    /// The <see cref="DialogSet"/> that contains all the Dialogs that can be used at runtime.
    /// </summary>
    private DialogSet _dialogs;

    /// <summary>
    /// Initializes a new instance of the <see cref="MultiTurnPromptsBot"/> class.
    /// </summary>
    /// <param name="accessors">A class containing <see cref="IStatePropertyAccessor{T}"/> used to manage state.</param>
    public MultiTurnPromptsBot(BotAccessors accessors)
    {
        _accessors = accessors ?? throw new ArgumentNullException(nameof(accessors));

        // The DialogSet needs a DialogState accessor, it will call it when it has a turn context.
        _dialogs = new DialogSet(accessors.ConversationDialogState);

        // ...
        // other constructor items
        // ...
    }
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Installieren Sie das Paket für die Dialogfelder von npm:

```cmd
npm install --save botbuilder-dialogs
```

Um **Dialogfelder** in Ihrem Bot zu verwenden, schließen Sie es in den Botcode ein.

Fügen Sie Folgendes in der Datei „app.js“ hinzu.

```javascript
// Import components from the dialogs library.
const { DialogSet } = require("botbuilder-dialogs");
// Import components from the main Bot Builder library.
const { ConversationState, MemoryStorage } = require('botbuilder');

// Set up a memory storage system to store information.
const storage = new MemoryStorage();
// We'll use ConversationState to track the state of the dialogs.
const conversationState = new ConversationState(storage);
// Create a property used to track state.
const dialogState = conversationState.createProperty('dialogState');

// Create a dialog set to control our prompts, store the state in dialogState
const dialogs = new DialogSet(dialogState);
```

---

## <a name="prompt-the-user"></a>Auffordern des Benutzers zu einer Eingabe

Definieren Sie zum Auffordern eines Benutzer zu einer Eingabe eine Eingabeaufforderung, indem Sie eine der integrierten Klassen verwenden, z.B. **TextPrompt**. Fügen Sie diese dann Ihrem Dialogsatz hinzu, und weisen Sie eine Dialog-ID dafür zu.

Nutzen Sie eine Eingabeaufforderung nach dem Hinzufügen in einem Wasserfalldialog mit zwei Schritten. Ein Dialog vom Typ *Wasserfall* ist eine Möglichkeit zum Definieren einer Folge von Schritten. Mehrere Eingabeaufforderungen können verkettet werden, um Konversationen mit mehreren Schritten zu erstellen. Weitere Informationen finden Sie im Abschnitt zum [Verwenden von Dialogen](bot-builder-dialog-manage-conversation-flow.md#using-dialogs-to-guide-the-user-through-steps) unter [Verwalten eines einfachen Konversationsflusses mit Dialogen](bot-builder-dialog-manage-conversation-flow.md).

Beispielsweise fordert der folgende Dialog den Benutzer zur Eingabe seines Namens auf und nutzt anschließend die Antwort, um ihn zu begrüßen. Im ersten Schritt wird im Dialog der Name des Benutzers abgefragt. Die Antwort des Benutzers wird als Parameter an die Funktion für den zweiten Schritt übergeben, in dem die Eingabe verarbeitet und die personalisierte Begrüßung gesendet wird.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Jede Eingabeaufforderung, die Sie in Ihrem Dialogfeld verwenden, erhält auch einen Namen, der vom Dialogfeld oder Ihrem Bot verwendet wird, um auf die Eingabeaufforderung zuzugreifen. In allen diesen Beispielen werden die Eingabeaufforderungs-IDs als Konstanten bereitgestellt.

Fügen Sie in Ihrem Botkonstruktor Definitionen für Ihren Wasserfalldialog mit zwei Schritten sowie die Eingabeaufforderung hinzu, die für den Dialog genutzt werden kann. Hier werden sie als unabhängige Funktionen hinzugefügt, aber sie können auch als „Inline-Lambda“ definiert werden, falls dies bevorzugt wird.

```csharp
 public MultiTurnPromptsBot(BotAccessors accessors)
{
    _accessors = accessors ?? throw new ArgumentNullException(nameof(accessors));

    // The DialogSet needs a DialogState accessor, it will call it when it has a turn context.
    _dialogs = new DialogSet(accessors.ConversationDialogState);

    // This array defines how the Waterfall will execute.
    var waterfallSteps = new WaterfallStep[]
    {
        NameStepAsync,
        SayHiAsync,
    };

    _dialogs.Add(new WaterfallDialog("details", waterfallSteps));
    _dialogs.Add(new TextPrompt("name"));
}
```

Anschließend können Sie die zwei Wasserfallschritte in Ihrem Bot definieren. Für die Texteingabeaufforderung geben Sie die oben definierte *name*-ID von `TextPrompt` an. Beachten Sie, dass die Methodennamen mit denen des obigen `WaterfallStep[]`-Elements übereinstimmen. Weitere hier angegebene Beispiele enthalten diesen Code nicht. Sie sollten aber wissen, dass Sie den Methodennamen für weitere Schritte in diesem `WaterfallStep[]`-Element in der richtigen Reihenfolge hinzufügen müssen.

```cs
    private static async Task<DialogTurnResult> NameStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
    {
        // WaterfallStep always finishes with the end of the Waterfall or with another dialog; here it is a Prompt Dialog.
        // Running a prompt here means the next WaterfallStep will be run when the users response is received.
        return await stepContext.PromptAsync("name", new PromptOptions { Prompt = MessageFactory.Text("Please enter your name.") }, cancellationToken);
    }

    private static async Task<DialogTurnResult> SayHiAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
    {
        await stepContext.Context.SendActivityAsync($"Hi {stepContext.Result}");

        return await stepContext.EndDialogAsync(cancellationToken);
    }
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Importieren Sie die TextPrompt-Klasse in Ihre App.

```javascript
const { TextPrompt } = require("botbuilder-dialogs");
```

Erstellen Sie die neue Eingabeaufforderung, und fügen Sie sie dem Dialogsatz hinzu.

```javascript
// Greet user:
// Ask for the user name and then greet them by name.
dialogs.add(new TextPrompt('textPrompt'));
dialogs.add('greetings', [
    async function (step){
        // the results of this prompt will be passed to the next step
        return await step.prompt('textPrompt', 'What is your name?');
    },
    async function(step) {
        // step.result is the result of the prompt defined above
        const userName = step.result;
        await step.context.sendActivity(`Hi ${userName}!`);
        return await step.endDialog();
    }
]);
```

---

> [!NOTE]
> Um ein Dialogfeld zu starten, rufen Sie einen Dialogfeldkontext ab und verwenden dessen _begin_-Methode. Weitere Informationen finden Sie im Thema zur [Verwendung von Dialogen zum Verwalten eines einfachen Konversationsflusses](./bot-builder-dialog-manage-conversation-flow.md).

## <a name="reusable-prompts"></a>Wiederverwendbare Eingabeaufforderungen

Eine Eingabeaufforderung kann wiederverwendet werden, um andere Fragen zu stellen, solange die Antworten den gleichen Typ aufweisen. Im obigen Beispielcode wurde z.B. eine Texteingabeaufforderung definiert und verwendet, um den Benutzer zur Eingabe seines Namens aufzufordern. Sie können diese Eingabeaufforderung auch verwenden, um den Benutzer nach einer anderen Textzeichenfolge zu fragen, z.B.: „Wo arbeiten Sie?“

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Im Beispiel ist die ID für unsere Texteingabeaufforderung (*name*) nicht hilfreich, was die Eindeutigkeit des Codes betrifft. Es ist aber ein gutes Beispiel dafür, dass Sie die ID für Ihre Eingabeaufforderung beliebig wählen können.

Unsere Methoden enthalten nun einen dritten Schritt, in dem gefragt wird, wo der Benutzer arbeitet.

```cs
    private static async Task<DialogTurnResult> NameStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
    {
        // WaterfallStep always finishes with the end of the Waterfall or with another dialog; here it is a Prompt Dialog.
        // Running a prompt here means the next WaterfallStep will be run when the users response is received.
        return await stepContext.PromptAsync("name", new PromptOptions { Prompt = MessageFactory.Text("Please enter your name.") }, cancellationToken);
    }

    private static async Task<DialogTurnResult> WorkAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
    {
        await stepContext.Context.SendActivityAsync($"Hi {stepContext.Result}!");

        return await stepContext.PromptAsync("name", new PromptOptions { Prompt = MessageFactory.Text("Where do you work?") }, cancellationToken);
    }

    private static async Task<DialogTurnResult> SayHiAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
    {
        await stepContext.Context.SendActivityAsync($"{stepContext.Result} is a cool place!");

        return await stepContext.EndDialogAsync(cancellationToken);
    }
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
// Greet user:
// Ask for the user name and then greet them by name.
// Ask them where they work.
dialogs.add(new TextPrompt('textPrompt'));
dialogs.add('greetings',[
    async function (step){
        // Use the textPrompt to ask for a name.
        return await step.prompt('textPrompt', 'What is your name?');
    },
    async function (step){
        const userName = step.result;
        await step.context.sendActivity(`Hi ${ userName }!`);

        // Now, reuse the same prompt to ask them where they work.
        return await step.prompt('textPrompt', 'Where do you work?');
    },
    async function(step) {
        const workPlace = step.result;
        await step.context.sendActivity(`${ workPlace } is a cool place!`);

        return await step.endDialog();
    }
]);
```

---

Wenn Sie mehrere unterschiedliche Eingabeaufforderungen benötigen, sollten Sie jede Eingabeaufforderung mit einer eindeutigen *dialogId* versehen. Für jeden Dialog bzw. jede Eingabeaufforderung, der bzw. die einem Dialogsatz hinzugefügt wird, ist eine eindeutige ID erforderlich. Sie können auch mehrere **Eingabeaufforderung**-Dialoge des gleichen Typs erstellen. Beispielsweise könnten Sie zwei **TextPrompt**-Dialogfelder für das Beispiel oben erstellen:

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```cs
_dialogs.Add(new WaterfallDialog("details", waterfallSteps));
_dialogs.Add(new TextPrompt("name"));
_dialogs.Add(new TextPrompt("workplace"));
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
dialogs.add(new TextPrompt('namePrompt'));
dialogs.add(new TextPrompt('workPlacePrompt'));
```

---

Mit Blick auf die Wiederverwendbarkeit des Codes muss für alle diese Eingabeaufforderungen nur ein `TextPrompt`-Element definiert werden, da jeweils ein Text als Antwort erwartet wird. Die Möglichkeit zur Benennung von Dialogen ist praktisch, wenn Sie auf die Eingabe der Eingabeaufforderungen unterschiedliche Validierungsregeln anwenden müssen. Wir sehen uns nun an, wie Sie Antworten für eine Eingabeaufforderung mit einem `NumberPrompt`-Element überprüfen können.

## <a name="specify-prompt-options"></a>Angeben von Eingabeaufforderungsoptionen

Wenn Sie eine Eingabeaufforderung in einem Dialogfeldschritt verwenden, können Sie auch Eingabeaufforderungsoptionen angeben, z.B. eine Zeichenfolge für eine erneute Eingabeaufforderung.

Die Angabe einer erneuten Eingabeaufforderung ist dann sinnvoll, wenn die Benutzereingabe eine Eingabeaufforderung nicht erfüllen kann, entweder weil sie in einem Format vorliegt, das die Eingabeaufforderung nicht analysieren kann, z.B. „morgen“ für eine Zahleneingabeaufforderung, oder weil die Eingabe ein Überprüfungskriterium nicht erfüllt. Die Zahleneingabeaufforderung kann eine Vielzahl von Eingaben interpretieren, z.B. „12“ oder „ein Viertel“ sowie „12“ und „0,25“.

„local“ ist ein optionaler Parameter für bestimmte Eingabeaufforderungen, z.B. **NumberPrompt**. Dies kann dazu beitragen, dass die Eingabeaufforderung die Eingabe präziser analysieren kann, aber die Verwendung ist nicht obligatorisch.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Mit dem folgende Code wird dem vorhandenen Dialogsatz **_dialogs** eine Zahleneingabeaufforderung hinzugefügt.

```csharp
_dialogs.Add(new NumberPrompt<int>("age"));
```

In einem Dialogfeldschritt würde der folgende Code den Benutzer zur Eingabe auffordern und eine erneute Eingabeaufforderung bereitstellen, die verwendet wird, wenn die Eingabe nicht als Zahl interpretiert werden kann.

```csharp
return await stepContext.PromptAsync(
    "age",
    new PromptOptions {
        Prompt = MessageFactory.Text("Please enter your age."),
        RetryPrompt = MessageFactory.Text("I didn't get that. Please enter a valid age."),
    },
    cancellationToken);
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
// Import the NumberPrompt class from the dialog library.
const { NumberPrompt } = require("botbuilder-dialogs");

// Add a NumberPrompt to our dialog set and give it the ID numberPrompt.
dialogs.add(new NumberPrompt('numberPrompt'));

// Call the numberPrompt dialog with the (optional) retryPrompt parameter.
await dc.prompt('numberPrompt', 'How many people in your party?', { retryPrompt: `Sorry, please specify the number of people in your party.` })
```

---

Die Auswahleingabeaufforderung verfügt über einen weiteren erforderlichen Parameter: die Liste mit den Auswahlmöglichkeiten für den Benutzer.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Wenn wir die **ChoicePrompt**-Eingabeaufforderung verwenden, um den Benutzer zu bitten, zwischen mehreren Optionen zu wählen, müssen wir die Eingabeaufforderung mit dieser Sammlung von Optionen versehen. Diese werden in einem **PromptOptions**-Objekt bereitgestellt. Hier verwenden wir die **ChoiceFactory**, um eine Liste mit Optionen in ein geeignetes Format zu konvertieren.

```csharp
private static async Task<DialogTurnResult> FavoriteColorAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
{
    await stepContext.Context.SendActivityAsync($"Hi {stepContext.Result}!");

    return await stepContext.PromptAsync(
        "color",
        new PromptOptions {
            Prompt = MessageFactory.Text("What's your favorite color?"),
            Choices = ChoiceFactory.ToChoices(new List<string> { "blue", "green", "red" }),
        },
        cancellationToken);
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
// Import the ChoicePrompt class into your app from the dialogs library.
const { ChoicePrompt } = require("botbuilder-dialogs");
```

```javascript
// Add a ChoicePrompt to the dialog set and give it an ID of choicePrompt.
dialogs.add(new ChoicePrompt('choicePrompt'));
```

```javascript
// Call the choicePrompt into action, passing in an array of options.
const list = ['green', 'blue', 'red', 'yellow'];
await dc.prompt('choicePrompt', 'Please make a choice', list, { retryPrompt: 'Please choose a color.' });
```

---

## <a name="validate-a-prompt-response"></a>Überprüfen der Antwort auf eine Eingabeaufforderung

Sie können die Antwort auf eine Eingabeaufforderung vor der Rückgabe des Werts an den nächsten Schritt des **Wasserfalls** überprüfen. Um beispielsweise eine **NumberPrompt**-Eingabeaufforderung in einem Bereich von Zahlen zwischen **6** und **20** zu überprüfen, fügen Sie eine Validierungsfunktion ein, die in etwa wie folgt aussieht:

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Änderung beim Hinzufügen der Eingabeaufforderung zum Dialogsatz zur Einbindung der Validierungsfunktion

```cs
_dialogs.Add(new NumberPrompt<int>("partySize", PartySizeValidatorAsync));
```

Die Validierung wird dann als eigene Methode definiert, bei der „true“ oder „false“ angegeben wird (je nachdem, ob die Validierung bestanden wurde). Wenn „false“ zurückgegeben wird, erhält der Benutzer erneut eine Eingabeaufforderung.

```cs
private Task<bool> PartySizeValidatorAsync(PromptValidatorContext<int> promptContext, CancellationToken cancellationToken)
{
    var result = promptContext.Recognized.Value;

    if (result < 6 || result > 20)
    {
        return Task.FromResult(false);
    }

    return Task.FromResult(true);
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
// Customized prompts with validations
// A number prompt with validation for valid party size within a range.
dialogs.add(new NumberPrompt('partySizePrompt', async (promptContext) => {
    // Check to make sure a value was recognized.
    if (promptContext.recognized.succeeded) {
        const value = promptContext.recognized.value;
        try {
            if (value < 6 ) {
                throw new Error('Party size too small.');
            } else if (value > 20) {
                throw new Error('Party size too big.')
            } else {
                return true; // Indicate that this is a valid value.
            }
        } catch (err) {
            await promptContext.context.sendActivity(`${ err.message } <br/>Please provide a valid number between 6 and 20.`);
            return false; // Indicate that this is invalid.
        }
    } else {
        return false;
    }
}));
```

---

Wenn Sie eine **DatetimePrompt**-Antwort für ein Datum und eine Uhrzeit in der Zukunft überprüfen möchten, können Sie eine ähnliche Überprüfungslogik verwenden:

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```cs
    private Task<bool> DateTimeValidatorAsync(PromptValidatorContext<IList<DateTimeResolution>> prompt, CancellationToken cancellationToken)
    {
        if (prompt.Recognized.Succeeded)
        {
            var resolution = prompt.Recognized.Value.First();

            // Verify that the Timex received is within the desired bounds, compared to today.
            var now = DateTime.Now;
            DateTime.TryParse(resolution.Value, out var time);

            if (time < now)
            {
                return Task.FromResult(false);
            }

            return Task.FromResult(true);
        }

        return Task.FromResult(false);
    }
```

```csharp
_dialogs.Add(new DateTimePrompt("date", DateTimeValidatorAsync));
```

Weitere Beispiele finden Sie in unserem [Beispielrepository](https://aka.ms/bot-samples-readme).

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```JavaScript
// A date and time prompt with validation for date/time in the future.
dialogs.add(new atetimePrompt('dateTimePrompt', async (promptContext) => {
    if (promptContext.recognized.succeeded) {
        const values = promptContext.recognized.value;
        try {
            if (values.length < 0) { throw new Error('missing time') }
            if (values[0].type !== 'date') { throw new Error('unsupported type') }
            const value = new Date(values[0].value);
            if (value.getTime() < new Date().getTime()) { throw new Error('in the past') }

            // update the return value of the prompt to be a real date object
            promptContext.recognized.value = value;
            return true; // indicate valid 
        } catch (err) {
            await promptContext.context.sendActivity(`Please enter a valid time in the future like "tomorrow at 9am".`);
            return false; // indicate invalid
        }
    } else {
        return false;
    }
}));
```

Weitere Beispiele finden Sie in unserem [Beispielrepository](https://aka.ms/bot-samples-readme).

---

> [!TIP]
> Eingabeaufforderungen für Datum und Uhrzeit können in unterschiedliche Datumsangaben aufgelöst werden, wenn der Benutzer eine mehrdeutige Antwort gibt. Je nachdem, wofür Sie diese Angaben verwenden, möchten Sie vielleicht alle Auflösungen überprüfen, die das Eingabeaufforderungsergebnis liefert, nicht nur die erste Auflösung.

Sie können ähnliche Verfahren verwenden, um Antworten auf Eingabeaufforderungen für beliebige Eingabeaufforderungstypen zu überprüfen.

## <a name="save-user-data"></a>Speichern von Benutzerdaten

Wenn Benutzer zu einer Eingabe aufgefordert werden, bestehen mehrere Optionen für die Verarbeitung dieser Eingabe. Beispielsweise können Sie die Eingabe nutzen und verwerfen, Sie können sie in einer globalen Variablen speichern, Sie können sie in einem flüchtigen oder speicherinternen Speichercontainer speichern, Sie können sie in einer Datei speichern, oder Sie können sie in einer externen Datenbank speichern. Weitere Informationen zum Speichern von Benutzerdaten finden Sie unter [Verwalten von Benutzerdaten](bot-builder-howto-v4-state.md).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

Ein vollständiges Beispiel, in dem einige dieser Eingabeaufforderungen verwendet werden, finden Sie unter dem Eingabeaufforderungs-Bot mit mehreren Durchläufen für [C#](https://aka.ms/cs-multi-prompts-sample) oder [JavaScript](https://aka.ms/js-multi-prompts-sample).

## <a name="next-steps"></a>Nächste Schritte

Da Sie jetzt wissen, wie ein Benutzer zu einer Eingabe aufgefordert wird, können Sie den Botcode und die Benutzeroberfläche verbessern, indem Sie verschiedene Gesprächsflüsse über Dialogfelder verwalten.
