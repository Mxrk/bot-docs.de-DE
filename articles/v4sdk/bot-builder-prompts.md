---
title: Auffordern von Benutzern zur Eingabe mithilfe der Dialogbibliothek | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Benutzer mithilfe der Dialogbibliothek im Bot Builder SDK für Node.js zur Eingabe auffordern.
keywords: Eingabeaufforderungen, Dialogfelder, AttachmentPrompt, ChoicePrompt, ConfirmPrompt, DatetimePrompt, NumberPrompt, TextPrompt, erneute Eingabeaufforderung, Überprüfung
author: v-ducvo
ms.author: v-ducvo
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 9/25/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: bd1fe8516cddaf2b75d3c11b469e372265b59be3
ms.sourcegitcommit: b78fe3d8dd604c4f7233740658a229e85b8535dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2018
ms.locfileid: "49997627"
---
# <a name="prompt-users-for-input-using-the-dialogs-library"></a>Auffordern von Benutzern zur Eingabe mithilfe der Dialogbibliothek

[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

Das Sammeln von Informationen durch das Stellen von Fragen ist eine der Hauptvorgehensweisen, mit denen ein Bot mit Benutzern interagiert. Dies ist auch direkt möglich, indem die _send activity_-Methode des [turn context](~/v4sdk/bot-builder-basics.md#defining-a-turn)-Objekts verwendet wird und dann die nächste eingehende Nachricht als Antwort verarbeitet wird. Über das Bot Builder SDK wird aber eine Bibliothek mit **Dialogen** bereitgestellt. Sie enthält Methoden, die zur Vereinfachung des Stellens von Fragen konzipiert sind und mit denen sichergestellt wird, dass die Antwort mit einem bestimmten Datentyp übereinstimmt oder benutzerdefinierte Validierungsregeln erfüllt. In diesem Thema wird ausführlich beschrieben, wie Sie dies erreichen, indem Sie **Eingabeaufforderungen** nutzen, um einen Benutzer zur Eingabe aufzufordern.

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

Erstellen Sie einen JavaScript-Bot mit der Echo-Vorlage. Weitere Informationen finden Sie in der [JavaScript-Schnellstartanleitung](../javascript/bot-builder-javascript-quickstart.md).

Installieren Sie das Paket mit den Dialogen über npm:

```cmd
npm install --save botbuilder-dialogs
```

Um **Dialoge** in Ihrem Bot zu verwenden, schließen Sie sie in den Botcode ein.

1. Fügen Sie in der Datei **bot.js** Folgendes hinzu:

    ```javascript
    // Import components from the dialogs library.
    const { DialogSet, TextPrompt, WaterfallDialog } = require("botbuilder-dialogs");

    // Name for the dialog state property accessor.
    const DIALOG_STATE_PROPERTY = 'dialogState';

    // Define the names for the prompts and dialogs for the dialog set.
    const TEXT_PROMPT = 'textPrompt';
    const MAIN_DIALOG = 'mainDialog';
    ```

    Der _Dialogsatz_ enthält die Dialoge für diesen Bot, und wir verwenden die _Texteingabeaufforderung_, um den Benutzer zur Eingabe aufzufordern. Wir benötigen auch einen Dialog-Zustandseigenschaftenaccessor, der vom Dialogsatz zum Nachverfolgen seines Zustands verwendet werden kann.

1. Aktualisieren Sie den Konstruktorcode Ihres Bots. Wir fügen hierzu in Kürze mehr hinzu.

    ```javascript
      constructor(conversationState) {
        // Track the conversation state object.
        this.conversationState = conversationState;

        // Create a state property accessor for the dialog set.
        this.dialogState = conversationState.createProperty(DIALOG_STATE_PROPERTY);
    }
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

1. Erstellen Sie im Botkonstruktor den Dialogsatz, und fügen Sie eine Texteingabeaufforderung und einen Wasserfalldialog hinzu.

    ```javascript
    // Create the dialog set, and add the prompt and the waterfall dialog.
    this.dialogs = new DialogSet(this.dialogState)
        .add(new TextPrompt(TEXT_PROMPT))
        .add(new WaterfallDialog(MAIN_DIALOG, [
            async (step) => {
                // The results of this prompt will be passed to the next step.
                return await step.prompt(TEXT_PROMPT, 'What is your name?');
            },
            async (step) => {
                // The result property contains the result from the previous step.
                const userName = step.result;
                await step.context.sendActivity(`Hi ${userName}!`);
                return await step.endDialog();
            }
        ]));
    ```

1. Aktualisieren Sie den Turn-Handler des Bots, um den Dialog auszuführen.

    ```javascript
    async onTurn(turnContext) {
        // See https://aka.ms/about-bot-activity-message to learn more about the message and other activity types.
        if (turnContext.activity.type === ActivityTypes.Message) {
            // Create a dialog context for the dialog set.
            const dc = await this.dialogs.createContext(turnContext);
            // Continue the dialog if it's active.
            await dc.continueDialog();
            if (!turnContext.responded) {
                // Otherwise, start the dialog.
                await dc.beginDialog(MAIN_DIALOG);
            }
        } else {
            // Send a default message for activity types that we don't handle.
            await turnContext.sendActivity(`[${turnContext.activity.type} event detected]`);
        }
        // Save state changes
        await this.conversationState.saveChanges(turnContext);
        }
    }
    ```

---

> [!NOTE]
> Um einen Dialog zu starten, rufen Sie einen Dialogkontext ab und verwenden dessen _begin dialog_-Methode. Weitere Informationen finden Sie im Thema zur [Verwendung von Dialogen zum Verwalten eines einfachen Konversationsflusses](./bot-builder-dialog-manage-conversation-flow.md).

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

Ändern Sie im Konstruktor des Bots den Wasserfall so, dass eine zweite Frage gestellt wird.

```javascript
// Create the dialog set, and add the prompt and the waterfall dialog.
this.dialogs = new DialogSet(this.dialogState)
    .add(new TextPrompt(TEXT_PROMPT))
    .add(new WaterfallDialog(MAIN_DIALOG, [
    async (step) => {
        // Ask the user for their name.
        return await step.prompt(TEXT_PROMPT, 'What is your name?');
    },
    async (step) => {
        // Acknowledge their response and ask for their place of work.
        const userName = step.result;
        return await step.prompt(TEXT_PROMPT, `Hi ${userName}; where do you work?`);
    },
    async (step) => {
        // Acknowledge their response and exit the dialog.
        const workPlace = step.result;
        await step.context.sendActivity(`${workPlace} is a cool place!`);
        return await step.endDialog();
    }
    ]));
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

Ersetzen Sie beispielsweise:

```javascript
.add(new TextPrompt(TEXT_PROMPT))
```

Durch Folgendes:

```javascript
.add(new TextPrompt('namePrompt'))
.add(new TextPrompt('workPlacePrompt'))
```

Aktualisieren Sie anschließend die entsprechenden Wasserfallschritte, damit diese Eingabeaufforderungen mit ihren jeweiligen Namen verwendet werden.

---

Mit Blick auf die Wiederverwendbarkeit des Codes muss für alle diese Eingabeaufforderungen nur ein `TextPrompt`-Element definiert werden, da jeweils ein Text als Antwort erwartet wird. Die Möglichkeit zur Benennung von Dialogen ist praktisch, wenn Sie auf die Eingabe der Eingabeaufforderungen unterschiedliche Validierungsregeln anwenden müssen. Wir sehen uns nun an, wie Sie Antworten für eine Eingabeaufforderung mit einem `NumberPrompt`-Element überprüfen können.

## <a name="specify-prompt-options"></a>Angeben von Eingabeaufforderungsoptionen

Wenn Sie eine Eingabeaufforderung in einem Dialogfeldschritt verwenden, können Sie auch Eingabeaufforderungsoptionen angeben, z.B. eine Zeichenfolge für eine erneute Eingabeaufforderung.

Die Angabe einer erneuten Eingabeaufforderung ist dann sinnvoll, wenn die Benutzereingabe eine Eingabeaufforderung nicht erfüllen kann, entweder weil sie in einem Format vorliegt, das die Eingabeaufforderung nicht analysieren kann, z.B. „morgen“ für eine Zahleneingabeaufforderung, oder weil die Eingabe ein Überprüfungskriterium nicht erfüllt. Die Zahleneingabeaufforderung kann eine Vielzahl von Eingaben interpretieren, z.B. „12“ oder „ein Viertel“ sowie „12“ und „0,25“.

„locale“ ist ein optionaler Parameter für bestimmte Eingabeaufforderungen, z.B. **NumberPrompt**. Dies kann dazu beitragen, dass die Eingabeaufforderung die Eingabe präziser analysieren kann, aber die Verwendung ist nicht obligatorisch.

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

Importieren Sie die `NumberPrompt`-Klasse aus der Dialogbibliothek.

```javascript
const { NumberPrompt } = require("botbuilder-dialogs");
```

Verwenden Sie die Zahleneingabeaufforderung in Ihrem Wasserfalldialog, und geben Sie dafür sowohl die erste Zeichenfolge als auch die Zeichenfolgen für die nachfolgenden Wiederholungen an.

```javascript
// Create the dialog set, and add the prompt and the waterfall dialog.
this.dialogs = new DialogSet(this.dialogState)
    .add(new NumberPrompt('partySize'))
    .add(new WaterfallDialog(MAIN_DIALOG, [
    async (step) => {
        // Ask the user for their party size.
        return await step.prompt('partySize', {
            prompt: 'How many people in your party?',
            retryPrompt: 'Sorry, please specify the number of people in your party.'
        });
    },
    async (step) => {
        // Acknowledge their response and exit the dialog.
        const partySize = step.result;
        await step.context.sendActivity(`That's a party of ${partySize}, thanks.`);
        return await step.endDialog();
    }
]));
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

Importieren Sie die `NumberPrompt`-Klasse aus der Dialogbibliothek.

```javascript
const { ChoicePrompt } = require("botbuilder-dialogs");
```

Verwenden Sie die Auswahleingabeaufforderung in Ihrem Wasserfalldialog, und geben Sie die verfügbaren Auswahlmöglichkeiten an.

```javascript
// Create the dialog set, and add the prompt and the waterfall dialog.
const list = ['green', 'blue', 'red', 'yellow'];
this.dialogs = new DialogSet(this.dialogState)
    .add(new ChoicePrompt('choicePrompt'))
    .add(new WaterfallDialog(MAIN_DIALOG, [
    async (step) => {
        // Ask the user for their party size.
        return await step.prompt('choicePrompt', {
            prompt: 'Please choose a color:',
            retryPrompt: 'Sorry, please choose a color from the list.',
            choices: list
        });
    },
    async (step) => {
        // Acknowledge their response and exit the dialog.
        const choice = step.result;
        await step.context.sendActivity(`That's ${choice.value}, thanks.`);
        return await step.endDialog();
    }
]));
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

Fügen Sie eine Validierungsmethode hinzu, wenn Sie die Eingabeaufforderung erstellen.

```javascript
// Create the dialog set, and add the prompt and the waterfall dialog.
this.dialogs = new DialogSet(this.dialogState)
    .add(new NumberPrompt('partySizePrompt', async (promptContext) =>                                                 {
        // Check to make sure a value was recognized.
        if (promptContext.recognized.succeeded) {
            const value = promptContext.recognized.value;
            try {
                if (value < 6) {
                    throw new Error('Party size too small.');
                } else if (value > 20) {
                    throw new Error('Party size too big.')
                } else {
                    return true; // Indicate that this is a valid value.
                }
            } catch (err) {
                await promptContext.context.sendActivity(`${err.message} <br/>Please provide a valid number between 6 and 20.`);
                return false; // Indicate that this is invalid.
            }
        } else {
            return false;
        }
    }))
    .add(new WaterfallDialog(MAIN_DIALOG, [
        async (step) => {
            // Ask the user for their party size.
            return await step.prompt('partySizePrompt', {
                prompt: 'How large is your party?',
                retryPrompt: 'Sorry, please specify a size between 6 and 20.'
            });
        },
        async (step) => {
            // Acknowledge their response and exit the dialog.
            const size = step.result;
            await step.context.sendActivity(`That's a party of ${size}, thanks.`);
            return await step.endDialog();
        }
    ]));
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

```javascript
const { DateTimePrompt } = require("botbuilder-dialogs");
```

```JavaScript
// Create the dialog set, and add the prompt and the waterfall dialog.
this.dialogs = new DialogSet(this.dialogState)
    .add(new DateTimePrompt('dateTimePrompt', async (promptContext) => {
        try {
            if (!promptContext.recognized.succeeded) { throw new Error('Value not recognized.') }
            const values = promptContext.recognized.value;
            if (!Array.isArray(values) || values.length < 0) { throw new Error('Value missing.'); }
            if ((values[0].type !== 'datetime') && (values[0].type !== 'date')) { throw new Error('Unsupported type.'); }
            const now = new Date();
            const value = new Date(values[0].value);
            if (value.getTime() < now.getTime()) { throw new Error('Value in the past.') }

            // update the return value of the prompt to be a real date object
            promptContext.recognized.value = [value];
            return true; // indicate valid
        } catch (err) {
            await promptContext.context.sendActivity(`${err} Please specify a date or a date and time in the future, like tomorrow at 9am.`);
            return false; // indicate invalid
        }
    }))
    .add(new WaterfallDialog(MAIN_DIALOG, [
        async (step) => {
            // Ask the user for their party size.
            return await step.prompt('dateTimePrompt', 'When would you like to schedule that for?');
        },
        async (step) => {
            // Acknowledge their response and exit the dialog.
            const time = step.result;
            await step.context.sendActivity(`That's ${time}, thanks.`);
            return await step.endDialog();
        }
    ]));
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

> [!div class="nextstepaction"]
> [Verwalten eines einfachen Konversationsflusses mit Dialogen](bot-builder-dialog-manage-conversation-flow.md)
