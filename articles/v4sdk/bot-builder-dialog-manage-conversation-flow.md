---
title: Verwalten eines einfachen Konversationsflusses mit Dialogen | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie einen einfachen Konversationsfluss mit Dialogen im Bot Builder SDK für Node.js verwalten.
keywords: einfacher Konversationsfluss, Dialoge, Eingabeaufforderungen, Wasserfälle, Dialogsatz
author: v-ducvo
ms.author: v-ducvo
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 9/25/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 0225b6d81b8eb9899a5dda8dc032dcbfb573afc1
ms.sourcegitcommit: 984705927561cc8d6a84f811ff24c8c71b71c76b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2018
ms.locfileid: "50965708"
---
# <a name="manage-simple-conversation-flow-with-dialogs"></a>Verwalten eines einfachen Konversationsflusses mit Dialogen

[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

Mit der Dialogbibliothek können Sie einfache und komplexe Konversationsflüsse verwalten. Bei einfachen Konversationsflüssen beginnt der Benutzer mit dem ersten Schritt eines *Wasserfalls* und durchläuft alle weiteren Schritte bis zum letzten Schritt, wo die Konversation dann endet. [Komplexe Konversationsflüsse](~/v4sdk/bot-builder-dialog-manage-complex-conversation-flow.md) enthalten Verzweigungen und Schleifen.

<!-- TODO: This paragraph belongs in a conceptual topic. -->

Dialoge sind Strukturen in Ihrem Bot, die wie Funktionen im Programm Ihres Bots fungieren. Mit Dialogen werden die vom Bot gesendeten Nachrichten erstellt und die erforderlichen Berechnungsaufgaben durchgeführt. Sie sind darauf ausgelegt, bestimmte Vorgänge in einer bestimmten Reihenfolge durchzuführen. Sie können auf unterschiedliche Weise aufgerufen werden, z.B. als Antwort für einen Benutzer, als Antwort aufgrund von äußeren Einflüssen oder von anderen Dialogen.

Durch die Verwendung von Dialogen kann der Bot-Entwickler den Konversationsfluss steuern. Sie können mehrere Dialoge erstellen und miteinander verknüpfen, um beliebige Konversationsflüsse zu erstellen, die von Ihrem Bot verarbeitet werden sollen. Die **Dialogbibliothek** im Bot Builder SDK enthält integrierte Funktionen wie _Eingabeaufforderungen_, _Wasserfalldialoge_ und _Komponentendialoge_, um Ihnen die Verwaltung des Konversationsflusses zu erleichtern. Mit Eingabeaufforderungen können Sie Benutzer zur Eingabe verschiedener Arten von Informationen auffordern. Sie können ein Wasserfall verwenden, um mehrere Schritte in einer Sequenz zu kombinieren. Außerdem können Sie Komponentendialoge nutzen, um modulare Dialogsysteme zu erstellen, die mehrere Unterdialoge enthalten.

In diesem Artikel verwenden wir _Dialogsätze_, um einen Konversationsfluss zu erstellen, der sowohl Eingabeaufforderungen als auch Wasserfälle enthält. Wir verwenden Code aus dem Beispiel für die **Eingabeaufforderung mit mehreren Durchläufen** [[C#](https://aka.ms/cs-multi-prompts-sample)|[JS](https://aka.ms/js-multi-prompts-sample)].

Eine Übersicht über die Dialoge finden Sie in den Artikeln zur [Dialogbibliothek](bot-builder-concept-dialog.md) und zum [Dialogzustand](bot-builder-dialog-state.md).

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Für die allgemeine Nutzung von Dialogen benötigen Sie das NuGet-Paket `Microsoft.Bot.Builder.Dialogs` für Ihr Projekt bzw. Ihre Projektmappe.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Zur Verwendung von Dialogen benötigen Sie in der Regel die Bibliothek `botbuilder-dialogs`. Führen Sie zum Installieren der Bibliothek den folgenden npm-Befehl aus:
```cmd
npm install --save botbuilder-dialogs
```

---

## <a name="using-dialogs-to-guide-the-user-through-steps"></a>Verwendung von Dialogen, um den Benutzer durch Schritte zu führen

In diesem Beispiel erstellen wir einen Dialog mit mehreren Schritten, um den Benutzer zur Eingabe von Informationen aufzufordern, indem wir einen Dialogsatz nutzen.

### <a name="create-a-dialog-with-waterfall-steps"></a>Erstellen eines Dialogs mit Wasserfallschritten

Ein Wasserfalldialog (**WaterfallDialog**) ist eine spezifische Implementierung eines Dialogs, der häufig verwendet wird, um Informationen vom Benutzer zu sammeln oder den Benutzer durch eine Reihe von Aufgaben zu leiten. Jeder Schritt der Konversation wird als Funktion implementiert. Bei jedem Schritt [fordert der Bot den Benutzer zur Eingabe auf](bot-builder-prompts.md), wartet auf eine Antwort und übergibt das Ergebnis dann an den nächsten Schritt. Das Ergebnis der ersten Funktion wird als Argument an die nächste Funktion übergeben usw.

Im folgenden Codebeispiel wird beispielsweise ein Array mit Delegaten definiert, die die Schritte eines **Wasserfalls** darstellen. Nach jeder Eingabeaufforderung bestätigt der Bot die Eingabe des Benutzers. Es gibt viele Möglichkeiten, wie Sie die in einem Dialog gesammelten Eingaben dauerhaft aufbewahren können. Informationen zu einigen Optionen finden Sie unter [Speichern von Benutzerdaten](bot-builder-tutorial-persist-user-inputs.md).

In diesem Beispiel werden Informationen direkt in das Profil des Benutzers geschrieben, wenn diese im Dialog gesammelt werden.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

In diesem Beispiel wird der Wasserfalldialog in der Botdatei definiert.

Verweisen Sie auf die Namespaces, die in dieser Datei verwendet werden.

```csharp
using System;
using System.Linq;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.Bot.Builder;
using Microsoft.Bot.Builder.Dialogs;
using Microsoft.Bot.Schema;
```

Definieren Sie eine Instanzeigenschaft für den Dialogsatz.

```csharp
/// <summary>
/// The <see cref="DialogSet"/> that contains all the Dialogs that can be used at runtime.
/// </summary>
private DialogSet _dialogs;
```

Erstellen Sie den Dialogsatz im Konstruktor des Bots, und fügen Sie dem Satz die Eingabeaufforderungen und den Wasserfalldialog hinzu.

```csharp
/// <summary>
/// Initializes a new instance of the <see cref="MultiTurnPromptsBot"/> class.
/// </summary>
/// <param name="accessors">A class containing <see cref="IStatePropertyAccessor{T}"/> used to manage state.</param>
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

Definieren Sie jeden Schritt außerdem als separate Methode. Sie können die Schritte auch inline definieren, indem Sie Lambda-Ausdrücke nutzen.

```csharp
/// <summary>
/// One of the functions that make up the <see cref="WaterfallDialog"/>.
/// </summary>
/// <param name="stepContext">The <see cref="WaterfallStepContext"/> gives access to the executing dialog runtime.</param>
/// <param name="cancellationToken">A <see cref="CancellationToken"/>.</param>
/// <returns>A <see cref="DialogTurnResult"/> to communicate some flow control back to the containing WaterfallDialog.</returns>
private static async Task<DialogTurnResult> NameStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
{
    // WaterfallStep always finishes with the end of the Waterfall or with another dialog; here it is a Prompt Dialog.
    // Running a prompt here means the next WaterfallStep will be run when the users response is received.
    return await stepContext.PromptAsync("name", new PromptOptions { Prompt = MessageFactory.Text("Please enter your name.") }, cancellationToken);
}

/// <summary>
/// One of the functions that make up the <see cref="WaterfallDialog"/>.
/// </summary>
/// <param name="stepContext">The <see cref="WaterfallStepContext"/> gives access to the executing dialog runtime.</param>
/// <param name="cancellationToken">A <see cref="CancellationToken"/>.</param>
/// <returns>A <see cref="DialogTurnResult"/> to communicate some flow control back to the containing WaterfallDialog.</returns>
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

/// <summary>
/// One of the functions that make up the <see cref="WaterfallDialog"/>.
/// </summary>
/// <param name="stepContext">The <see cref="WaterfallStepContext"/> gives access to the executing dialog runtime.</param>
/// <param name="cancellationToken">A <see cref="CancellationToken"/>.</param>
/// <returns>A <see cref="DialogTurnResult"/> to communicate some flow control back to the containing WaterfallDialog.</returns>
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

/// <summary>
/// One of the functions that make up the <see cref="WaterfallDialog"/>.
/// </summary>
/// <param name="stepContext">The <see cref="WaterfallStepContext"/> gives access to the executing dialog runtime.</param>
/// <param name="cancellationToken">A <see cref="CancellationToken"/>.</param>
/// <returns>A <see cref="DialogTurnResult"/> to communicate some flow control back to the containing WaterfallDialog.</returns>
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

/// <summary>
/// One of the functions that make up the <see cref="WaterfallDialog"/>.
/// </summary>
/// <param name="stepContext">The <see cref="WaterfallStepContext"/> gives access to the executing dialog runtime.</param>
/// <param name="cancellationToken">A <see cref="CancellationToken"/>.</param>
/// <returns>A <see cref="DialogTurnResult"/> to communicate some flow control back to the containing WaterfallDialog.</returns>
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

Der Dialog wird über den Durchlaufhandler des Bots ausgeführt. Zuerst wird ein Dialogkontext erstellt, und dann wird der Vorgang fortgesetzt, oder der Dialog beginnt je nach Bedarf, und die Konversation und der Benutzerzustand werden dann am Ende des Durchlaufs gespeichert.

```csharp
// Run the DialogSet - let the framework identify the current state of the dialog from
// the dialog stack and figure out what (if any) is the active dialog.
var dialogContext = await _dialogs.CreateContextAsync(turnContext, cancellationToken);
var results = await dialogContext.ContinueDialogAsync(cancellationToken);

// If the DialogTurnStatus is Empty we should start a new dialog.
if (results.Status == DialogTurnStatus.Empty)
{
    await dialogContext.BeginDialogAsync("details", null, cancellationToken);
}
```

```csharp
// Save the dialog state into the conversation state.
await _accessors.ConversationState.SaveChangesAsync(turnContext, false, cancellationToken);

// Save the user profile updates into the user state.
await _accessors.UserState.SaveChangesAsync(turnContext, false, cancellationToken);
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

In diesem Beispiel wird der Wasserfalldialog in der Datei **bot.js** definiert.

Importieren Sie die Objekte, die Sie für den Code benötigen.

```javascript
const { ActivityTypes } = require('botbuilder');
const { ChoicePrompt, DialogSet, NumberPrompt, TextPrompt, WaterfallDialog } = require('botbuilder-dialogs');
```

Definieren und erstellen Sie den Dialogsatz im Konstruktor des Bots, und fügen Sie dem Satz die Eingabeaufforderungen und den Wasserfalldialog hinzu.

```javascript
/**
*
* @param {ConversationState} conversationState A ConversationState object used to store the dialog state.
* @param {UserState} userState A UserState object used to store values specific to the user.
*/
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

Definieren Sie jeden Schritt außerdem als separate Methode. Sie können die Schritte auch inline definieren, indem Sie Lambda-Ausdrücke nutzen.

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

Der Dialog wird über den Durchlaufhandler des Bots ausgeführt. Zuerst wird ein Dialogkontext erstellt, und dann wird der Vorgang fortgesetzt, oder der Dialog beginnt je nach Bedarf, und die Konversation und der Benutzerzustand werden dann am Ende des Durchlaufs gespeichert.

```javascript
// Create a dialog context object.
const dc = await this.dialogs.createContext(turnContext);
```

```javascript
// If the bot has not yet responded, continue processing the current dialog.
await dc.continueDialog();
```

```javascript
// Start the sample dialog in response to any other input.
if (!turnContext.responded) {
    const user = await this.userProfile.get(dc.context, {});
    if (user.name) {
        await dc.beginDialog(HELLO_USER);
    } else {
        await dc.beginDialog(WHO_ARE_YOU);
    }
}
```

```javascript
// Save changes to the user state.
await this.userState.saveChanges(turnContext);

// End this turn by saving changes to the conversation state.
await this.conversationState.saveChanges(turnContext);
```

---

## <a name="dialog-context-and-waterfall-step-context-objects"></a>Dialog- und Wasserfallschritt-Kontextobjekte

Verwenden Sie das Dialogkontextobjekt, um aus dem Durchlaufhandler Ihres Bots heraus mit einem Dialogsatz zu interagieren.
Verwenden Sie das Wasserfallschritt-Kontextobjekt, um aus einem Wasserfallschritt heraus mit einem Dialogsatz zu interagieren.

## <a name="to-start-a-dialog"></a>So starten Sie einen Dialog

Um einen Dialog zu starten, übergeben Sie die zu startende *dialogId* an die Methode _beginDialog_, _prompt_ oder _replaceDialog_ des Dialogkontexts. Die _beginDialog_-Methode verschiebt den Dialog an den Anfang des Stapels, während die _replaceDialog_-Methode den aktuellen Dialog aus dem Stapel entfernt und den Ersatzdialog in den Stapel verschiebt.

Die _prompt_-Methode des Dialogkontexts ist eine Hilfsmethode, für die Argumente genutzt und mit der die passenden Optionen für die Eingabeaufforderung erstellt werden. Anschließend beginnt der Eingabeaufforderungsdialog. Weitere Informationen zu Eingabeaufforderungen finden Sie unter [Auffordern von Benutzern zur Eingabe](bot-builder-prompts.md).

## <a name="to-end-a-dialog"></a>So beenden Sie einen Dialog

Die _end dialog_-Methode beendet einen Dialog, indem sie ihn aus dem Stapel entfernt und ein optionales Ergebnis an den übergeordneten Dialog zurückgibt.

Die bewährte Methode besteht darin, am Ende des Dialogs explizit die _endDialog_-Methode aufzurufen.

## <a name="to-clear-the-dialog-stack"></a>So löschen Sie den Dialogstapel

Wenn Sie alle Dialoge aus dem Stapel entfernen möchten, können Sie den Dialogstapel löschen, indem Sie die _cancel all dialogs_-Methode des Dialogkontexts aufrufen.

## <a name="to-repeat-a-dialog"></a>So wiederholen Sie einen Dialog

Verwenden Sie zum Wiederholen eines Dialogs die _replace dialog_-Methode, mit der der aktuelle Dialog aus dem Stapel entfernt, der Ersatzdialog an den Anfang des Stapels verschoben und der Dialog gestartet wird. Dies ist eine hervorragende Möglichkeit zum Behandeln [komplexer Konversationsflüsse](~/v4sdk/bot-builder-dialog-manage-complex-conversation-flow.md) und eine gute Technik zum Verwalten von Menüs.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun wissen, wie Sie einfache Konversationsflüsse verwalten, werfen wir nun einen Blick darauf, wie Sie die _replace dialog_-Methode zum Behandeln komplexer Konversationsflüsse nutzen können.

> [!div class="nextstepaction"]
> [Verwalten komplexer Konversationsabläufe mit Dialogen](bot-builder-dialog-manage-complex-conversation-flow.md)
