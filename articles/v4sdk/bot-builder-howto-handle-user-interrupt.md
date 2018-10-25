---
title: Behandeln von Benutzerunterbrechungen | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Benutzerunterbrechungen behandelt und Konversationsflüsse gelenkt werden.
keywords: Unterbrechung, Unterbrechungen, Themenwechsel, unterbrechen
author: ivorb
ms.author: v-ivorb
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 09/20/2018
ms.reviewer: ''
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 15c3ed60dacf1751e82c2bb0804cdd938f286f40
ms.sourcegitcommit: 6c2426c43cd2212bdea1ecbbf8ed245145b3c30d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/08/2018
ms.locfileid: "48852325"
---
# <a name="handle-user-interruptions"></a>Behandeln von Benutzerunterbrechungen

[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

Die Behandlung von Unterbrechungen ist ein wichtiger Aspekt eines stabilen Bots.

Während Sie möglicherweise meinen, dass Ihre Benutzer Ihrem Konversationsfluss Schritt für Schritt folgen, ist die Wahrscheinlichkeit groß, dass sie sich anders entscheiden oder mitten im Prozess eine Frage stellen, statt die Frage zu beantworten. Wie würde Ihr Bot in diesen Situationen die Benutzereingaben behandeln? Wie würde die Benutzererfahrung aussehen? Wie würden Sie Benutzerzustandsdaten verwalten? Beim Behandeln von Unterbrechungen ist dafür zu sorgen, dass Ihr Bot in der Lage ist, in solchen Situationen entsprechend zu reagieren.

Auf diese Fragen gibt es nicht eine richtige Antwort, da jede Situation vom jeweiligen Szenario abhängt, für dessen Behandlung Sie Ihren Bot entwickelt haben. In diesem Thema werden einige allgemeine Verfahren zur Behandlung von Benutzerunterbrechungen erläutert und Möglichkeiten zur Implementierung im Bot vorgeschlagen.

## <a name="handle-expected-interruptions"></a>Behandeln von erwarteten Unterbrechungen

Ein prozeduraler Konversationsfluss besteht aus einer Reihe von Schritten, durch die Sie den Benutzer führen möchten. Alle Benutzeraktionen, die von diesen Schritten abweichen, sind potenzielle Unterbrechungen. Bei einem normalen Fluss, gibt es Unterbrechungen, die erwartet werden können.

**Tischreservierung** Bei einem Tischreservierungsbot können die Schritte aus den Fragen nach Datum und Uhrzeit, nach Anzahl der Personen und nach dem Reservierungsnamen bestehen. Bei diesem Prozess können folgende Unterbrechungen erwartet werden:

* `cancel`: Zum Beenden des Prozesses.
* `help`: Zum Bereitstellen von zusätzlichen Anweisungen zu diesem Prozess.
* `more info`: Zum Bereitstellen von Hinweisen und Vorschlägen oder von alternativen Möglichkeiten zum Reservieren eines Tisches (z.B. eine E-Mail-Adresse oder Telefonnummer).
* `show list of available tables`: Wenn dies möglich ist, eine Liste mit Tischen anzeigen, die für das gewünschte Datum und die entsprechende Uhrzeit verfügbar sind.

**Essensbestellung** Bei einem Bot zum Bestellen von Speisen können die Schritte in der Bereitstellung einer Liste mit Speisen und der Möglichkeit, dass Benutzer ihrem Einkaufswagen Speisen hinzufügen, bestehen. Bei diesem Prozess können folgende Unterbrechungen erwartet werden:

* `cancel`: Zum Beenden des Bestellprozesses.
* `more info`: Zum Bereitstellen von lebensmitteltechnische Details zu den einzelnen Speisen.
* `help`: Zum Bereitstellen von Hilfe zur Verwendung des Systems.
* `process order`: Zum Verarbeiten der Bestellung.

Sie können diese in Form einer Liste mit **Aktionsvorschlägen** oder als Hinweis bereitstellen, sodass der Benutzer zumindest weiß, welche Befehle vom Bot verstanden werden und somit gesendet werden können.

Beim Essensbestellfluss können Sie beispielsweise zusammen mit den Speisen erwartete Unterbrechungen bereitstellen. In diesem Fall werden die Speisen als `choices` gesendet.

# <a name="ctabcsharptab"></a>[C#](#tab/csharptab)

Wir definieren den Dialogsatz als eine Unterklasse **DialogSet**.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.Bot.Builder;
using Microsoft.Bot.Builder.Dialogs;
using Microsoft.Bot.Builder.Dialogs.Choices;

public class OrderDinnerDialogs : DialogSet
{
    public OrderDinnerDialogs(IStatePropertyAccessor<DialogState> dialogStateAccessor)
        : base(dialogStateAccessor)
    {
    }
}
```

Wir definieren einige interne Klassen, um das Menü zu beschreiben.

```cs
/// <summary>
/// Contains information about an item on the menu.
/// </summary>
public class DinnerItem
{
    public string Description { get; set; }

    public double Price { get; set; }
}

/// <summary>
/// Describes the dinner menu, including the items on the menu and options for
/// interrupting the ordering process.
/// </summary>
public class DinnerMenu
{
    /// <summary>Gets the items on the menu.</summary>
    public static Dictionary<string, DinnerItem> MenuItems { get; } = new Dictionary<string, DinnerItem>
    {
        ["Potato salad"] = new DinnerItem { Description = "Potato Salad", Price = 5.99 },
        ["Tuna sandwich"] = new DinnerItem { Description = "Tuna Sandwich", Price = 6.89 },
        ["Clam chowder"] = new DinnerItem { Description = "Clam Chowder", Price = 4.50 },
    };

    /// <summary>Gets all the "interruptions" the bot knows how to process.</summary>
    public static List<string> Interrupts { get; } = new List<string>
    {
        "More info", "Process order", "Help", "Cancel",
    };

    /// <summary>Gets all of the valid inputs a user can make.</summary>
    public static List<string> Choices { get; }
        = MenuItems.Select(c => c.Key).Concat(Interrupts).ToList();
}
```

# <a name="javascripttabjstab"></a>[JavaScript](#tab/jstab)

Wir beginnen mit einer einfachen EchoBot-Vorlage. Entsprechende Anweisungen finden Sie in der [Schnellstartanleitung für JavaScript](~/javascript/bot-builder-javascript-quickstart.md).

Die `botbuilder-dialogs`-Bibliothek kann von NPM heruntergeladen werden. Um die `botbuilder-dialogs`-Bibliothek zu installieren, führen Sie den folgenden npm-Befehl aus:

```cmd
npm install --save botbuilder-dialogs
```

Verweisen Sie in der Datei **bot.js** auf die Klassen, auf die wir verweisen werden, und definieren Sie Bezeichner, die wir für den Dialog verwenden werden.

```javascript
const { ActivityTypes } = require('botbuilder');
const { DialogSet, ChoicePrompt, WaterfallDialog, DialogTurnStatus } = require('botbuilder-dialogs');

// Name for the dialog state property accessor.
const DIALOG_STATE_PROPERTY = 'dialogStateProperty';

// Name of the order-prompt dialog.
const ORDER_PROMPT = 'orderingDialog';

// Name for the choice prompt for use in the dialog.
const CHOICE_PROMPT = 'choicePrompt';
```

Definieren Sie die Optionen, die in unseren Bestelldialog angezeigt werden sollen.

```javascript
// The options on the dinner menu, including commands for the bot.
const dinnerMenu = {
    choices: ["Potato Salad - $5.99", "Tuna Sandwich - $6.89", "Clam Chowder - $4.50",
        "Process order", "Cancel", "More info", "Help"],
    "Potato Salad - $5.99": {
        description: "Potato Salad",
        price: 5.99
    },
    "Tuna Sandwich - $6.89": {
        description: "Tuna Sandwich",
        price: 6.89
    },
    "Clam Chowder - $4.50": {
        description: "Clam Chowder",
        price: 4.50
    }
}
```

---

Bei der Bestelllogik können Sie prüfen, ob der Benutzer einen Zeichenfolgenabgleich oder reguläre Ausdrücke verwendet.

# <a name="ctabcsharptab"></a>[C#](#tab/csharptab)

Zunächst müssen wir ein Hilfsprogramm zum Nachverfolgen unserer Bestellungen definieren.

```cs
/// <summary>Helper class for storing the order.</summary>
public class Order
{
    public double Total { get; set; } = 0.0;

    public List<DinnerItem> Items { get; set; } = new List<DinnerItem>();

    public bool ReadyToProcess { get; set; } = false;

    public bool OrderProcessed { get; set; } = false;
}
```

Fügen Sie einige Konstanten hinzu, um die benötigten IDs nachzuverfolgen.

```csharp
/// <summary>The ID of the top-level dialog.</summary>
public const string MainDialogId = "mainMenu";

/// <summary>The ID of the choice prompt.</summary>
public const string ChoicePromptId = "choicePrompt";

/// <summary>The ID of the order card value, tracked inside the dialog.</summary>
public const string OrderCartId = "orderCart";
```

Aktualisieren Sie den Konstruktor, um eine Auswahleingabeaufforderung und unseren Wasserfalldialogfeld hinzuzufügen. Wir definieren auch die Methoden, die die Wasserfallschritte implementieren.

```cs
public OrderDinnerDialogs(IStatePropertyAccessor<DialogState> dialogStateAccessor)
    : base(dialogStateAccessor)
{
    // Add a choice prompt for the dialog.
    Add(new ChoicePrompt(ChoicePromptId));

    // Define and add the main waterfall dialog.
    WaterfallStep[] steps = new WaterfallStep[]
    {
        PromptUserAsync,
        ProcessInputAsync,
    };

    Add(new WaterfallDialog(MainDialogId, steps));
}

/// <summary>
/// Defines the first step of the main dialog, which is to ask for input from the user.
/// </summary>
/// <param name="stepContext">The current waterfall step context.</param>
/// <param name="cancellationToken">The cancellation token.</param>
/// <returns>The task to perform.</returns>
private async Task<DialogTurnResult> PromptUserAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
{
    // Initialize order, continuing any order that was passed in.
    Order order = (stepContext.Options is Order oldCart && oldCart != null)
        ? new Order
        {
            Items = new List<DinnerItem>(oldCart.Items),
            Total = oldCart.Total,
            ReadyToProcess = oldCart.ReadyToProcess,
            OrderProcessed = oldCart.OrderProcessed,
        }
        : new Order();

    // Set the order cart in dialog state.
    stepContext.Values[OrderCartId] = order;

    // Prompt the user.
    return await stepContext.PromptAsync(
        "choicePrompt",
        new PromptOptions
        {
            Prompt = MessageFactory.Text("What would you like for dinner?"),
            RetryPrompt = MessageFactory.Text(
                "I'm sorry, I didn't understand that. What would you like for dinner?"),
            Choices = ChoiceFactory.ToChoices(DinnerMenu.Choices),
        },
        cancellationToken);
}

/// <summary>
/// Defines the second step of the main dialog, which is to process the user's input, and
/// repeat or exit as appropriate.
/// </summary>
/// <param name="stepContext">The current waterfall step context.</param>
/// <param name="cancellationToken">The cancellation token.</param>
/// <returns>The task to perform.</returns>
private async Task<DialogTurnResult> ProcessInputAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
{
    // Get the order cart from dialog state.
    Order order = stepContext.Values[OrderCartId] as Order;

    // Get the user's choice from the previous prompt.
    string response = (stepContext.Result as FoundChoice).Value;

    if (response.Equals("process order", StringComparison.InvariantCultureIgnoreCase))
    {
        order.ReadyToProcess = true;

        await stepContext.Context.SendActivityAsync(
            "Your order is on it's way!",
            cancellationToken: cancellationToken);

        // In production, you may want to store something more helpful.
        // "Process" the order and exit.
        order.OrderProcessed = true;
        return await stepContext.EndDialogAsync(null, cancellationToken);
    }
    else if (response.Equals("cancel", StringComparison.InvariantCultureIgnoreCase))
    {
        // Cancel the order.
        await stepContext.Context.SendActivityAsync(
            "Your order has been canceled",
            cancellationToken: cancellationToken);

        // Exit without processing the order.
        return await stepContext.EndDialogAsync(null, cancellationToken);
    }
    else if (response.Equals("more info", StringComparison.InvariantCultureIgnoreCase))
    {
        // Send more information about the options.
        string message = "More info: <br/>" +
            "Potato Salad: contains 330 calories per serving. Cost: 5.99 <br/>"
            + "Tuna Sandwich: contains 700 calories per serving. Cost: 6.89 <br/>"
            + "Clam Chowder: contains 650 calories per serving. Cost: 4.50";
        await stepContext.Context.SendActivityAsync(
            message,
            cancellationToken: cancellationToken);

        // Continue the ordering process, passing in the current order cart.
        return await stepContext.ReplaceDialogAsync(MainDialogId, order, cancellationToken);
    }
    else if (response.Equals("help", StringComparison.InvariantCultureIgnoreCase))
    {
        // Provide help information.
        string message = "To make an order, add as many items to your cart as " +
            "you like. Choose the `Process order` to check out. " +
            "Choose `Cancel` to cancel your order and exit.";
        await stepContext.Context.SendActivityAsync(
            message,
            cancellationToken: cancellationToken);

        // Continue the ordering process, passing in the current order cart.
        return await stepContext.ReplaceDialogAsync(MainDialogId, order, cancellationToken);
    }

    // We've checked for expected interruptions. Check for a valid item choice.
    if (!DinnerMenu.MenuItems.ContainsKey(response))
    {
        await stepContext.Context.SendActivityAsync("Sorry, that is not a valid item. " +
            "Please pick one from the menu.");

        // Continue the ordering process, passing in the current order cart.
        return await stepContext.ReplaceDialogAsync(MainDialogId, order, cancellationToken);
    }
    else
    {
        // Add the item to cart.
        DinnerItem item = DinnerMenu.MenuItems[response];
        order.Items.Add(item);
        order.Total += item.Price;

        // Acknowledge the input.
        await stepContext.Context.SendActivityAsync(
            $"Added `{response}` to your order; your total is ${order.Total:0.00}.",
            cancellationToken: cancellationToken);

        // Continue the ordering process, passing in the current order cart.
        return await stepContext.ReplaceDialogAsync(MainDialogId, order, cancellationToken);
    }
}
```

# <a name="javascripttabjstab"></a>[JavaScript](#tab/jstab)

Definieren Sie Ihren Dialog im Botkonstruktor. Hinweis: Der Code überprüft _zuerst_, ob Unterbrechungen vorliegen und behandelt diese ggf., bevor er mit dem nächsten logischen Schritt fortfährt.

```javascript
constructor(conversationState) {
    this.dialogStateAccessor = conversationState.createProperty(DIALOG_STATE_PROPERTY);
    this.conversationState = conversationState;

    this.dialogs = new DialogSet(this.dialogStateAccessor)
        .add(new ChoicePrompt(CHOICE_PROMPT))
        .add(new WaterfallDialog(ORDER_PROMPT, [
            async (step) => {
                if (step.options && step.options.orders) {
                    // If an order cart was passed in, continue to use it.
                    step.values.orderCart = step.options;
                } else {
                    // Otherwise, start a new cart.
                    step.values.orderCart = {
                        orders: [],
                        total: 0
                    };
                }
                return await step.prompt(CHOICE_PROMPT, "What would you like?", dinnerMenu.choices);
            },
            async (step) => {
                const choice = step.result;
                if (choice.value.match(/process order/ig)) {
                    if (step.values.orderCart.orders.length > 0) {
                        // If the cart is not empty, process the order by returning the order to the parent context.
                        await step.context.sendActivity("Your order has been processed.");
                        return await step.endDialog(step.values.orderCart);
                    } else {
                        // Otherwise, prompt again.
                        await step.context.sendActivity("Your cart was empty. Please add at least one item to the cart.");
                        return await step.replaceDialog(ORDER_PROMPT);
                    }
                } else if (choice.value.match(/cancel/ig)) {
                    // Cancel the order, and return "cancel" to the parent context.
                    await step.context.sendActivity("Your order has been canceled.");
                    return await step.endDialog("cancelled");
                } else if (choice.value.match(/more info/ig)) {
                    // Provide more information, and then continue the ordering process.
                    var msg = "More info: <br/>Potato Salad: contains 330 calories per serving. <br/>"
                        + "Tuna Sandwich: contains 700 calories per serving. <br/>"
                        + "Clam Chowder: contains 650 calories per serving."
                    await step.context.sendActivity(msg);
                    return await step.replaceDialog(ORDER_PROMPT, step.values.orderCart);
                } else if (choice.value.match(/help/ig)) {
                    // Provide help information, and then continue the ordering process.
                    var msg = `Help: <br/>`
                        + `To make an order, add as many items to your cart as you like then choose `
                        + `the "Process order" option to check out.`
                    await step.context.sendActivity(msg);
                    return await step.replaceDialog(ORDER_PROMPT, step.values.orderCart);
                } else {
                    // The user has chosen a food item from the menu. Add the item to cart.
                    var item = dinnerMenu[choice.value];
                    step.values.orderCart.orders.push(item.description);
                    step.values.orderCart.total += item.price;

                    await step.context.sendActivity(`Added ${item.description} to your cart. <br/>`
                        + `Current total: $${step.values.orderCart.total}`);

                    // Continue the ordering process.
                    return await step.replaceDialog(ORDER_PROMPT, step.values.orderCart);
                }
            }
        ]));
}
```

Aktualisieren Sie den OnTurn-Handler zum Aufrufen des Dialogfelds und zum Anzeigen der Ergebnisse aus den Bestellprozess.

```javascript
async onTurn(turnContext) {
    if (turnContext.activity.type === ActivityTypes.Message) {
        let dc = await this.dialogs.createContext(turnContext);
        let dialogTurnResult = await dc.continueDialog();
        if (dialogTurnResult.status === DialogTurnStatus.complete) {
            // The dialog completed this turn.
            const result = dialogTurnResult.result;
            if (!result || result === "cancelled") {
                await turnContext.sendActivity('You cancelled your order.');
            } else {
                await turnContext.sendActivity(`Your order came to $${result.total}`);
            }
        } else if (!turnContext.responded) {
            // No dialog was active.
            await turnContext.sendActivity("Let's order dinner...");
            await dc.cancelAllDialogs();
            await dc.beginDialog(ORDER_PROMPT);
        } else {
            // The dialog is active.
        }
    } else {
        await turnContext.sendActivity(`[${turnContext.activity.type} event detected]`);
    }
    // Save state changes
    await this.conversationState.saveChanges(turnContext);
}
```

---

## <a name="handle-unexpected-interruptions"></a>Behandeln von unerwarteten Unterbrechungen

Es gibt Unterbrechungen, die außerhalb des Anwendungsbereichs Ihres Bots liegen.
So können Sie zwar nicht alle Unterbrechungen vorhersehen, Sie können jedoch Ihren Bot für die Behandlung bestimmter Unterbrechungsmuster programmieren.

### <a name="switching-topic-of-conversations"></a>Wechseln des Gesprächsthemas

Was geschieht, wenn der Benutzer mitten in der Konversation das Thema wechseln möchte? Mit Ihrem Bot kann beispielsweise ein Tisch reserviert und Essen bestellt werden.
Ein Benutzer befindet sich im _Tischreservierungs_fluss und sendet die Nachricht „Essen bestellen“, statt die Frage nach der Anzahl der Personen zu beantworten. In diesem Fall hat es sich der Benutzer anders überlegt und stattdessen eine Konversation zur Essensbestellung begonnen. Wie sollten Sie diese Unterbrechung behandeln?

Sie können das Thema wechseln und den Essensbestellfluss beginnen oder dem Benutzer mitteilen, dass eine Zahl erwartet wird und ihn erneut zur Eingabe auffordern. Wenn Sie einen Themenwechsel zulassen, müssen Sie entscheiden, ob der Status gespeichert werden soll, sodass der Benutzer den Vorgang später fortsetzen kann, oder ob alle eingegebenen Informationen gelöscht werden sollen, sodass der Benutzer den Vorgang neu beginnen muss, wenn er beim nächsten Mal einen Tisch reservieren möchte. Weitere Informationen zum Verwalten von Benutzerzustandsdaten finden Sie unter [Speichern des Zustands mit Unterhaltungs- und Benutzereigenschaften](bot-builder-howto-v4-state.md).

### <a name="apply-artificial-intelligence"></a>Anwenden von künstlicher Intelligenz

Bei Unterbrechungen außerhalb des Anwendungsbereichs Ihres Bots können Sie versuchen, die Absicht des Benutzers zu erraten. Hierzu können Sie KI-Dienste wie QnAMaker, LUIS oder benutzerdefinierte Logiken verwenden und Vorschläge für Möglichkeiten anbieten, von denen der Bot meint, dass sie der Absicht des Benutzers entsprechen.

Beispielsweise wenn der Benutzer mitten im Tischreservierungsfluss sagt: „Ich möchte einen Burger bestellen.“ Der Bot weiß in diesem Konversationsfluss nicht, wie er diese Aussage behandeln soll. Da es beim aktuellen Fluss nicht um Bestellungen geht und „Essen bestellen“ ein anderer Konversationsbefehl ist, weiß der Bot mit dieser Eingabe nichts anzufangen. Wenn Sie beispielsweise LUIS verwenden, können Sie das Modell trainieren, sodass es erkennt, dass der Benutzer Essen bestellen möchte (LUIS kann beispielsweise eine „orderFood“-Absicht zurückgeben). Der Bot kann somit wie folgt antworten: „Anscheinend möchten Sie Essen bestellen. Möchten Sie zu unserem Essensbestellprozess wechseln?“ Weitere Informationen zum Trainieren von LUIS und Erkennen von Benutzerabsichten finden Sie unter [Verwenden von LUIS für Language Understanding](bot-builder-howto-v4-luis.md).

### <a name="default-response"></a>Standardantwort

Wenn alles andere nicht funktioniert, sollten Sie eine Standardantwort senden, statt nichts zu tun und den Benutzer ratlos zurückzulassen. Die Standardantwort sollte dem Benutzer mitteilen, welche Befehle der Bot versteht, sodass der Benutzer wieder in den Prozess hineinfindet.

Sie können anhand des „context.**responded**“-Flags am Ende der Botlogik erkennen, ob der Bot während des Prozesses eine Antwort an den Benutzer gesendet hat. Wenn der Bot die Benutzereingabe verarbeitet, aber nicht antwortet, weiß der Bot mit der Eingabe wahrscheinlich nichts anzufangen. In diesem Fall können Sie einhaken und dem Benutzer eine Standardnachricht senden.

Wenn Ihr Bot dem Benutzer standardmäßig einen `mainMenu`-Dialog sendet. Entscheiden Sie selbst, welche Oberfläche dem Benutzer in dieser Situation vom Bot angezeigt wird.

# <a name="ctabcsharptab"></a>[C#](#tab/csharptab)

```cs
// Check whether we replied. If not then clear the dialog stack and present the main menu.
if (!turnContext.Responded)
{
    await dc.CancelAllDialogsAsync(cancellationToken);
    await dc.BeginDialogAsync(OrderDinnerDialogs.MainDialogId, null, cancellationToken);
}
```

# <a name="javascripttabjstab"></a>[JavaScript](#tab/jstab)

```javascript
// Check to see if anyone replied. If not then clear all the stack and present the main menu
if (!context.responded) {
    await dc.cancelAllDialogs();
    await step.beginDialog('mainMenu');
}
```

---

## <a name="handling-global-interruptions"></a>Behandeln von globalen Unterbrechungen

Im obigen Beispiel behandeln wir Unterbrechungen, die in einem bestimmten Durchlauf in einen bestimmten Dialog auftreten können. Wie aber behandeln wir globale Unterbrechungen, wie sie jederzeit auftreten können?

Dazu können Sie unsere Logik zur Behandlung von Unterbrechungen in den Haupthandler des Bots aufnehmen – die Funktion, die das eingehende `turnContext`-Element verarbeitet und entscheidet, wie damit verfahren wird.

Im folgenden Beispiel überprüft der Bot _zuerst_ den Text der eingehenden Nachricht auf Anzeichen dafür, dass der Benutzer Hilfe benötigt oder den Vorgang abbrechen möchte – zwei Unterbrechungen, die bei Bots häufig auftreten. Nachdem diese Überprüfung abgeschlossen ist, ruft der Bot `dc.continueDialog()` auf, um alle noch ausstehenden aktiven Dialoge zu verarbeiten.

# <a name="ctabcsharptab"></a>[C#](#tab/csharptab)

```cs
// Check for top-level interruptions.
string utterance = turnContext.Activity.Text.Trim().ToLowerInvariant();

if (utterance == "help")
{
    // Start a general help dialog. Dialogs already on the stack remain and will continue
    // normally if the help dialog exits normally.
    await dc.BeginDialogAsync(OrderDinnerDialogs.HelpDialogId, null, cancellationToken);
}
else if (utterance == "cancel")
{
    // Cancel any dialog on the stack.
    await turnContext.SendActivityAsync("Canceled.", cancellationToken: cancellationToken);
    await dc.CancelAllDialogsAsync(cancellationToken);
}
else
{
    await dc.ContinueDialogAsync(cancellationToken);

    // Check whether we replied. If not then clear the dialog stack and present the main menu.
    if (!turnContext.Responded)
    {
        await dc.CancelAllDialogsAsync(cancellationToken);
        await dc.BeginDialogAsync(OrderDinnerDialogs.MainDialogId, null, cancellationToken);
    }
}
```

# <a name="javascripttabjstab"></a>[JavaScript](#tab/jstab)

Wir konnten Unterbrechungen vor dem Senden einer Benutzerantwort an einen Dialog behandeln.

Dieser Ausschnitt setzt voraus, dass unser Dialogsatz ein `helpDialog`- und ein `mainMenu`-Element enthält.

```javascript
const utterance = (context.activity.text || '').trim();

// Let's look for some interruptions first!
if (utterance.match(/help/ig)) {
    // Launch a new help dialog if the user asked for help
    await dc.beginDialog('helpDialog');
} else if (utterance.match(/cancel/ig)) {
    // Cancel any active dialogs if the user says cancel
    await dc.context.sendActivity('Canceled.');
    await dc.cancelAllDialogs();
}

// If the bot hasn't yet responded...
if (!context.responded) {
    // Continue any active dialog, which might send a response...
    await dc.continueDialog();

    // Finally, if the bot still hasn't sent a response, send instructions.
    if (!context.responded) {
        await dc.cancelAllDialogs();
        await context.sendActivity("Starting the main menu...");
        await dc.beginDialog('mainMenu');
    }
}
```

---
