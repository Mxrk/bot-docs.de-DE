---
title: Verwalten eines komplexen Konversationsablaufs mit Dialogen | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie einen komplexen Konversationsablauf mit Dialogen im Bot Builder SDK für Node.js verwalten.
keywords: komplexer Konversationsablauf, Wiederholung, Schleife, Menü, Dialoge, Eingabeaufforderungen, Wasserfälle, Dialogsatz
author: v-ducvo
ms.author: v-ducvo
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 10/03/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 871bfd9f8d693c5082fe1ccf38349f4d3d46ece2
ms.sourcegitcommit: b8bd66fa955217cc00b6650f5d591b2b73c3254b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2018
ms.locfileid: "49326567"
---
# <a name="manage-complex-conversation-flows-with-dialogs"></a>Verwalten komplexer Konversationsabläufe mit Dialogen

[!INCLUDE [pre-release-label](~/includes/pre-release-label.md)]

Im letzten Artikel wurde veranschaulicht, wie die Dialogbibliothek zum Verwalten einfacher Konversationen genutzt wird. Bei [einfachen Konversationsabläufen](bot-builder-dialog-manage-conversation-flow.md) beginnt der Benutzer mit dem ersten Schritt eines *Wasserfalls* und durchläuft alle weiteren Schritte bis zum letzten Schritt, wo die Konversation dann endet. In diesem Artikel verwenden wir Dialoge, um komplexere Konversationen mit Verzweigungen und Schleifen zu verwalten. Hierfür verwenden wir verschiedene Methoden, die im Dialogkontext und Wasserfallschrittkontext definiert sind, und übergeben Argumente zwischen verschiedenen Teilen des Dialogs.

Weitere Hintergrundinformationen zu Dialogen finden Sie unter [Dialogbibliothek](bot-builder-concept-dialog.md).

Mit der Methode _replace dialog_ aus der Bibliothek **Dialogs** erhalten Sie mehr Kontrolle über den *Dialogstapel*. Diese Methode ermöglicht es Ihnen, den derzeit aktiven Dialog gegen einen anderen auszutauschen, während der Zustand und Fluss der Konversation beibehalten werden. Mit den Methoden _begin dialog_ und _replace dialog_ können Sie je nach Bedarf Verzweigungen und Schleifen einrichten, um komplexere Interaktionen zu erstellen. Sollte sich die Komplexität Ihrer Konversationen erhöhen und sich die Verwaltung Ihrer Wasserfalldialoge daher schwierig gestalten, können Sie die Verwendung von [Komponentendialogen](bot-builder-compositcontrol.md) erwägen oder basierend auf der `Dialog`-Basisklasse eine benutzerdefinierte Klasse für die Dialogverwaltung erstellen.

In diesem Artikel erstellen wir Beispieldialoge für einen Bot, der als Hotel-Concierge fungiert und von Gästen zum Zugreifen auf häufige Dienste genutzt werden kann: Reservieren eines Tischs im Hotelrestaurant und Aufgeben einer Essensbestellung beim Zimmerservice.  Diese Features werden zusammen mit einem übergreifenden Menü als Dialoge im Dialogsatz erstellt.

Der Botdialog auf der obersten Ebene bietet dem Gast zwei Optionen. Wenn der Gast einen Tisch reservieren möchte, verwendet der Dialog der obersten Ebene die asynchrone _begin dialog_-Methode, um den Dialog für die Tischreservierung zu starten. Wenn der Gast eine Bestellung beim Zimmerservice aufgeben möchte, wird vom Dialog der obersten Ebene stattdessen der Dialog für die Essensbestellung gestartet.

Im Dialog für die Essensbestellung wird der Gast gebeten, etwas von einer Speisekarte zu wählen. Anschließend wird er nach seiner Zimmernummer gefragt. Die Auswahl der Gerichte ist _ebenfalls_ ein Dialog. Dessen Wiedergabe wird mehrfach aufgerufen, während ein Gast Gerichte von der Speisekarte wählt, bevor die Essensbestellung übermittelt wird.

Das folgende Diagramm veranschaulicht die Dialoge, die in diesem Artikel erstellt werden, sowie die Beziehungen zwischen ihnen.

![Abbildung der Dialoge aus diesem Artikel](~/media/complex-conversation-flows.png)

## <a name="how-to-branch"></a>Verwenden von Verzweigungen

Der Dialogkontext pflegt einen _Dialogstapel_ und verfolgt für jeden Dialog aus dem Stapel nach, welcher Schritt als Nächstes ansteht. Die Methode _begin dialog_ fügt per Push ganz oben im Stapel einen Dialog ein, und die Methode _end dialog_ entfernt den obersten Dialog per Pop aus dem Stapel.

Treffen Sie für eine Verzweigung zuerst eine Auswahl aus einer Gruppe von untergeordneten Dialogen. Weitere Informationen zu diesem Konzept finden Sie unter [Verzweigen einer Konversation](bot-builder-concept-dialog.md#branch-a-conversation).

## <a name="how-to-loop"></a>Verwenden einer Schleife

Mit der _replace dialog_-Methode des Dialogkontexts können Sie den Dialog ersetzen, der sich ganz oben im Stapel befindet. Der Zustand des alten Dialogs wird verworfen, und der neue Dialog beginnt von vorn. Mit dieser Methode können Sie also Schleife erstellen, indem Sie einen Dialog durch sich selbst ersetzen. Aber um [Informationen dauerhaft zu speichern, die vom Bot bereits gesammelt wurden](bot-builder-howto-v4-state.md), müssen Sie diese Informationen an die neue Instanz des Dialogs im Aufruf der _replace dialog_-Methode übergeben.

Im folgenden Beispiel sehen Sie, dass im Dialogzustand die Bestellung beim Zimmerservice gespeichert und bei der Wiederholung des Dialogs `orderPrompt` der aktuelle Dialogzustand übergeben wird, damit der neue Dialogzustand weitere Elemente hinzufügen kann. Wenn Sie diese Informationen lieber in einem Botzustand außerhalb des Dialogs speichern möchten, hilft Ihnen der Artikel [Speichern von Benutzerdaten](bot-builder-tutorial-persist-user-inputs.md) weiter.

## <a name="create-the-dialogs-for-the-hotel-bot"></a>Erstellen der Dialoge für den Hotelbot

In diesem Abschnitt erstellen wir die Dialoge zur Verwaltung einer Konversation für den zuvor beschriebenen Hotelbot.

### <a name="install-the-dialogs-library"></a>Installieren der Dialogebibliothek

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Wir beginnen mit einer einfachen EchoBot-Vorlage. Entsprechende Anweisungen finden Sie in der [Schnellstartanleitung für .NET](../dotnet/bot-builder-dotnet-sdk-quickstart.md).

Um Dialoge zu verwenden, installieren Sie das `Microsoft.Bot.Builder.Dialogs`NuGet-Paket für Ihr Projekt oder Ihre Projektmappe.
Verweisen Sie anschließend in using-Anweisungen Ihrer Codedateien auf die Dialogbibliothek.

```csharp
using Microsoft.Bot.Builder.Dialogs;
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Wir beginnen mit einer EchoBot-Vorlage. Entsprechende Anweisungen finden Sie in der [Schnellstartanleitung für JavaScript](../javascript/bot-builder-javascript-quickstart.md).



Die `botbuilder-dialogs`-Bibliothek kann von npm heruntergeladen werden. Um die `botbuilder-dialogs`-Bibliothek zu installieren, führen Sie den folgenden npm-Befehl aus:

```cmd
npm install --save botbuilder-dialogs
```

Um **Dialoge** in Ihrem Bot zu verwenden, schließen Sie sie in den Botcode ein. Fügen Sie der Datei **index.js** beispielsweise Folgendes hinzu:

```javascript
const { DialogSet } = require('botbuilder-dialogs');
```

Und fügen Sie der Datei **bot.js** Folgendes hinzu:

```javascript
const { DialogSet, NumberPrompt, ChoicePrompt, WaterfallDialog } = require('botbuilder-dialogs');
```

---

### <a name="create-a-dialog-set"></a>Erstellen eines Dialogsatzes

Erstellen Sie einen _Dialogsatz_, dem wir dann alle Dialoge für dieses Beispiel hinzufügen können.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Erstellen Sie eine Klasse mit dem Namen **HotelDialogs**, und fügen Sie anschließend die erforderlichen using-Anweisungen hinzu.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.Bot.Builder;
using Microsoft.Bot.Builder.Dialogs;
using Microsoft.Bot.Builder.Dialogs.Choices;
using Microsoft.Bot.Schema;
```

Leiten Sie die Klasse von **DialogSet** ab. Fügen Sie einen Konstruktor ein, für den der Parameter `IStatePropertyAccessor<DialogState>` genutzt wird, um den internen Zustand einer Instanz des Dialogsatzes zu verwalten. Definieren Sie außerdem die IDs und Schlüssel zur Identifizierung der Dialoge, Eingabeaufforderungen und Zustandsinformationen für diesen Dialogsatz.

```csharp
/// <summary>Contains the set of dialogs and prompts for the hotel bot.</summary>
public class HotelDialogs : DialogSet
{
    /// <summary>The ID of the top-level dialog.</summary>
    public const string MainMenu = "mainMenu";

    public HotelDialogs(IStatePropertyAccessor<DialogState> dialogStateAccessor)
        : base(dialogStateAccessor)
    {
    }

    /// <summary>Contains the IDs for the other dialogs in the set.</summary>
    private static class Dialogs
    {
        public const string OrderDinner = "orderDinner";
        public const string OrderPrompt = "orderPrompt";
        public const string ReserveTable = "reserveTable";
    }

    /// <summary>Contains the IDs for the prompts used by the dialogs.</summary>
    private static class Inputs
    {
        public const string Choice = "choicePrompt";
        public const string Number = "numberPrompt";
    }

    /// <summary>Contains the keys used to manage dialog state.</summary>
    private static class Outputs
    {
        public const string OrderCart = "orderCart";
        public const string OrderTotal = "orderTotal";
        public const string RoomNumber = "roomNumber";
    }
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Fügen Sie in der Datei **index.js** den Code zum Erstellen eines Zustandseigenschaftenaccessors für die Verwaltung des Dialogzustands hinzu, und verwenden Sie ihn, um den für den Bot bestimmten Dialogsatz zu erstellen.

```javascript
// Create conversation state with in-memory storage provider.
const conversationState = new ConversationState(memoryStorage);
const dialogStateAccessor = conversationState.createProperty('dialogState');

// Create a dialog set for the bot.
const dialogSet = new DialogSet(dialogStateAccessor);

// Create the bot.
const bot = new MyBot(conversationState, dialogSet)
```

Aktualisieren Sie anschließend den Aufruf für die Verarbeitung der Aktivität, damit das Botobjekt verwendet wird.

```javascript
// Listen for incoming requests.
server.post('/api/messages', (req, res) => {
    adapter.processActivity(req, res, async (context) => {
        // Route to the bot's turn handler.
        await bot.onTurn(context);
    });
});
```

---

### <a name="add-the-prompts-to-the-set"></a>Hinzufügen der Eingabeaufforderungen zu dem Satz

Wir verwenden **ChoicePrompt**, um Gäste zu fragen, ob sie etwas zu essen bestellen oder einen Tisch reservieren möchten, und um es ihnen zu ermöglichen, etwas von der Speisekarte zu wählen. Außerdem verwenden wir **NumberPrompt**, um den Gast nach seiner Zimmernummer zu fragen, wenn er etwas zu essen bestellt.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Fügen Sie dem Konstruktor **HotelDialogs** die beiden folgenden Eingabeaufforderungen hinzu:

```csharp
// Add the prompts.
Add(new ChoicePrompt(Inputs.Choice));
Add(new NumberPrompt<int>(Inputs.Number));
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Aktualisieren Sie den Botkonstruktor, und fügen Sie dem Dialogsatz zwei Eingabeaufforderungen hinzu.

```javascript
constructor(conversationState, dialogSet) {
    // Creates a new state accessor property.
    // See https://aka.ms/about-bot-state-accessors to learn more about the bot state and state accessors.
    this.countProperty = conversationState.createProperty(TURN_COUNTER_PROPERTY);
    this.conversationState = conversationState;
    this.dialogSet = dialogSet;

    this.dialogSet.add(new ChoicePrompt('choicePrompt'));
    this.dialogSet.add(new NumberPrompt('numberPrompt'));
}
```

---

### <a name="define-some-of-the-supporting-information"></a>Definieren einiger Zusatzinformationen

Als Nächstes richten wir die benötigen Informationen zu den einzelnen Speisekartenoptionen ein.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Erstellen Sie für die Informationen eine interne statische Klasse vom Typ **Lists**. Erstellen Sie außerdem die internen Klassen **WelcomeChoice** und **MenuChoice** für Informationen zu den einzelnen Optionen.

Bei dieser Gelegenheit fügen wir auch Informationen für die Optionenliste des übergeordneten Willkommensdialogs hinzu und erstellen Hilfslisten, die wir später verwenden, wenn diese Informationen von den Gästen abgefragt werden. Das macht zwar im Vorfeld etwas mehr Arbeit, vereinfacht aber den Dialogcode.

```csharp
/// <summary>Describes an option for the top-level dialog.</summary>
private class WelcomeChoice
{
    /// <summary>Gets or sets the text to show the guest for this option.</summary>
    public string Description { get; set; }

    /// <summary>Gets or sets the ID of the associated dialog for this option.</summary>
    public string DialogName { get; set; }
}

/// <summary>Describes an option for the food-selection dialog.</summary>
/// <remarks>We have two types of options. One represents meal items that the guest
/// can add to their order. The other represents a request to process or cancel the
/// order.</remarks>
private class MenuChoice
{
    /// <summary>The request text for cancelling the meal order.</summary>
    public const string Cancel = "Cancel order";

    /// <summary>The request text for processing the meal order.</summary>
    public const string Process = "Process order";

    /// <summary>Gets or sets the name of the meal item or the request.</summary>
    public string Name { get; set; }

    /// <summary>Gets or sets the price of the meal item; or NaN for a request.</summary>
    public double Price { get; set; }

    /// <summary>Gets the text to show the guest for this option.</summary>
    public string Description => double.IsNaN(Price) ? Name : $"{Name} - ${Price:0.00}";
}
```

```csharp
/// <summary>Contains the lists used to present options to the guest.</summary>
private static class Lists
{
    /// <summary>Gets the options for the top-level dialog.</summary>
    public static List<WelcomeChoice> WelcomeOptions { get; } = new List<WelcomeChoice>
    {
        new WelcomeChoice { Description = "Order dinner", DialogName = Dialogs.OrderDinner },
        new WelcomeChoice { Description = "Reserve a table", DialogName = Dialogs.ReserveTable },
    };

    private static readonly List<string> _welcomeList = WelcomeOptions.Select(x => x.Description).ToList();

    /// <summary>Gets the choices to present in the choice prompt for the top-level dialog.</summary>
    public static IList<Choice> WelcomeChoices { get; } = ChoiceFactory.ToChoices(_welcomeList);

    /// <summary>Gets the reprompt action for the top-level dialog.</summary>
    public static Activity WelcomeReprompt
    {
        get
        {
            var reprompt = MessageFactory.SuggestedActions(_welcomeList, "Please choose an option");
            reprompt.AttachmentLayout = AttachmentLayoutTypes.List;
            return reprompt as Activity;
        }
    }

    /// <summary>Gets the options for the food-selection dialog.</summary>
    public static List<MenuChoice> MenuOptions { get; } = new List<MenuChoice>
    {
        new MenuChoice { Name = "Potato Salad", Price = 5.99 },
        new MenuChoice { Name = "Tuna Sandwich", Price = 6.89 },
        new MenuChoice { Name = "Clam Chowder", Price = 4.50 },
        new MenuChoice { Name = MenuChoice.Process, Price = double.NaN },
        new MenuChoice { Name = MenuChoice.Cancel, Price = double.NaN },
    };

    private static readonly List<string> _menuList = MenuOptions.Select(x => x.Description).ToList();

    /// <summary>Gets the choices to present in the choice prompt for the food-selection dialog.</summary>
    public static IList<Choice> MenuChoices { get; } = ChoiceFactory.ToChoices(_menuList);

    /// <summary>Gets the reprompt action for the food-selection dialog.</summary>
    public static Activity MenuReprompt
    {
        get
        {
            var reprompt = MessageFactory.SuggestedActions(_menuList, "Please choose an option");
            reprompt.AttachmentLayout = AttachmentLayoutTypes.List;
            return reprompt as Activity;
        }
    }
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Erstellen Sie in der Datei **bot.js** eine **dinnerMenu**-Konstante für diese Informationen.

```javascript
const dinnerMenu = {
    choices: ["Potato Salad - $5.99", "Tuna Sandwich - $6.89", "Clam Chowder - $4.50",
        "Process order", "Cancel"],
    "Potato Salad - $5.99": {
        Description: "Potato Salad",
        Price: 5.99
    },
    "Tuna Sandwich - $6.89": {
        Description: "Tuna Sandwich",
        Price: 6.89
    },
    "Clam Chowder - $4.50": {
        Description: "Clam Chowder",
        Price: 4.50
    }
}
```

---

### <a name="create-the-welcome-dialog"></a>Erstellen des Willkommensdialogs

In diesem Dialog wird ein `ChoicePrompt`-Element verwendet, um das Menü anzuzeigen, und dann wird darauf gewartet, dass der Benutzer eine Option auswählt. Wenn der Benutzer entweder `Order dinner` oder `Reserve a table` wählt, wird der entsprechende Dialog gestartet. Wenn der untergeordnete Dialog abgeschlossen ist, wird der Dialog nicht einfach beendet und der Benutzer über den Ablauf im Unklaren gelassen, sondern für den Dialog `mainMenu` wird eine Schleife ausgeführt. Hierzu wird der Dialog `mainMenu` erneut gestartet. Mit dieser einfachen Struktur zeigt der Bot immer das Menü an, und der Benutzer weiß jederzeit, welche Optionen er hat.

Der Dialog `mainMenu` enthält die folgenden Wasserfallschritte:

* Im ersten Schritt des Wasserfalls wird der Dialogzustand initialisiert oder gelöscht. Außerdem wird der Benutzer begrüßt und dazu aufgefordert, eine der verfügbaren Optionen (`Order dinner` oder `Reserve a table`) auszuwählen.
* Im zweiten Schritt wird die getroffene Auswahl abgerufen und dann der entsprechende untergeordnete Dialog gestartet. Am Ende des untergeordneten Dialogs wird der ursprüngliche Dialog mit dem folgenden Schritt fortgesetzt.
* Im letzten Schritt ersetzen wir diesen Dialog mithilfe der _replace dialog_-Methode durch eine neue Instanz von sich selbst und verwandeln dadurch den Willkommensdialog in eine Menüschleife.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Fügen Sie den Wasserfalldialog im Konstruktor hinzu. Definieren Sie anschließend die Wasserfallschritte. Hier haben wir diese Schritte in einer geschachtelten Klasse definiert.

Im Konstruktor **HotelDialogs**:

```csharp
// Define the steps for and add the main welcome dialog.
WaterfallStep[] welcomeDialogSteps = new WaterfallStep[]
{
    MainDialogSteps.PresentMenuAsync,
    MainDialogSteps.ProcessInputAsync,
    MainDialogSteps.RepeatMenuAsync,
};

Add(new WaterfallDialog(MainMenu, welcomeDialogSteps));
```

Fügen Sie die Schrittdefinitionen in der **HotelDialogs**-Klasse hinzu.

```csharp
/// <summary>
/// Contains the waterfall dialog steps for the order-dinner dialog.
/// </summary>
private static class MainDialogSteps
{
    public static async Task<DialogTurnResult> PresentMenuAsync(
        WaterfallStepContext stepContext,
        CancellationToken cancellationToken)
    {
        // Greet the guest and ask them to choose an option.
        await stepContext.Context.SendActivityAsync(
            "Welcome to Contoso Hotel and Resort.",
            cancellationToken: cancellationToken);
        return await stepContext.PromptAsync(
            Inputs.Choice,
            new PromptOptions
            {
                Prompt = MessageFactory.Text("How may we serve you today?"),
                RetryPrompt = Lists.WelcomeReprompt,
                Choices = Lists.WelcomeChoices,
            },
            cancellationToken);
    }

    public static async Task<DialogTurnResult> ProcessInputAsync(
        WaterfallStepContext stepContext,
        CancellationToken cancellationToken)
    {
        // Begin a child dialog associated with the chosen option.
        var choice = (FoundChoice)stepContext.Result;
        var dialogId = Lists.WelcomeOptions[choice.Index].DialogName;

        return await stepContext.BeginDialogAsync(dialogId, null, cancellationToken);
    }

    public static async Task<DialogTurnResult> RepeatMenuAsync(
        WaterfallStepContext stepContext,
        CancellationToken cancellationToken)
    {
        // Start this dialog over again.
        return await stepContext.ReplaceDialogAsync(MainMenu, null, cancellationToken);
    }
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Fügen Sie im Botkonstruktor den Wasserfalldialog `mainMenu` hinzu.

```javascript
// Display a menu and ask user to choose a menu item. Direct user to the item selected otherwise, show
// the menu again.
this.dialogSet.add(new WaterfallDialog('mainMenu', [
    async function (step) {
        // Welcome the user and send a prompt.
        await step.context.sendActivity("Welcome to Contoso Hotel and Resort.");
        return await step.prompt('choicePrompt', "How may we serve you today?", ['Order Dinner', 'Reserve a table']);
    },
    async function (step) {
        // Handle the user's response to the previous prompt and branch the dialog.
        if (step.result.value.match(/order dinner/ig)) {
            return await step.beginDialog('orderDinner');
        } else if (step.result.value.match(/reserve a table/ig)) {
            return await step.beginDialog('reserveTable');
        }
    },
    async function (step) {
        // Calling replaceDialog will loop the main menu
        return await step.replaceDialog('mainMenu');
    }
]));
```

---

### <a name="create-the-order-dinner-dialog"></a>Erstellen des Dialogs für die Essensbestellung

Beim Dialog für die Essensbestellung wird der Gast zunächst beim Bestellservice willkommen geheißen und umgehend der Dialog für die Essenswahl gestartet. Auf diesen Dialog gehen wir im nächsten Abschnitt ein. Wichtig: Wenn der Gast die Verarbeitung der Bestellung anfordert, gibt der Dialog für die Essenswahl die Liste mit den Bestellungen zurück. Zum Abschluss des Prozesses wird nach einer Zimmernummer gefragt, an die das Essen geliefert werden soll, und es wird eine Bestätigungsnachricht gesendet. Wenn der Gast die Bestellung abbricht, wird nicht nach einer Zimmernummer gefragt, und der Dialog wird sofort beendet.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Fügen Sie der Klasse **HotelDialog** eine Datenstruktur zum Nachverfolgen der Essensbestellung des Gasts hinzu. Da dies im Dialogzustand gespeichert wird, benötigt die Klasse den Standardkonstruktor für die Serialisierung.

```csharp
/// <summary>Contains the guest's dinner order.</summary>
private class OrderCart : List<MenuChoice>
{
    public OrderCart() : base() { }

    public OrderCart(OrderCart other) : base(other) { }
}
```

Fügen Sie den Wasserfalldialog im Konstruktor hinzu. Definieren Sie anschließend die Wasserfallschritte. Hier haben wir diese Schritte in einer geschachtelten Klasse definiert.

Im Konstruktor **HotelDialogs**:

```csharp
// Define the steps for and add the order-dinner dialog.
WaterfallStep[] orderDinnerDialogSteps = new WaterfallStep[]
{
    OrderDinnerSteps.StartFoodSelectionAsync,
    OrderDinnerSteps.GetRoomNumberAsync,
    OrderDinnerSteps.ProcessOrderAsync,
};

Add(new WaterfallDialog(Dialogs.OrderDinner, orderDinnerDialogSteps));
```

Fügen Sie die Schrittdefinitionen in der **HotelDialogs**-Klasse hinzu.

* Im ersten Schritt wird eine Willkommensnachricht gesendet, und der Dialog für die Essenswahl wird gestartet.
* Im zweiten Schritt wird überprüft, ob der Dialog für die Essenswahl einen Warenkorb zurückgegeben hat.
  * Falls ja, wird nach der Zimmernummer gefragt, und die Warenkorbinformationen werden gespeichert.
  * Andernfalls wird davon ausgegangen, dass die Bestellung abgebrochen wurde, und es wird die _end dialog_-Methode aufgerufen, um den Dialog zu beenden.
* Im letzten Schritt wird die Zimmernummer erfasst und eine Bestätigung an den Gast gesendet. Damit ist der Vorgang abgeschlossen. In diesem Schritt würde der Bot dann die Bestellung an einen Verarbeitungsdienst weiterleiten.

```csharp
/// <summary>
/// Contains the waterfall dialog steps for the order-dinner dialog.
/// </summary>
private static class OrderDinnerSteps
{
    public static async Task<DialogTurnResult> StartFoodSelectionAsync(
        WaterfallStepContext stepContext,
        CancellationToken cancellationToken)
    {
        await stepContext.Context.SendActivityAsync(
            "Welcome to our Dinner order service.",
            cancellationToken: cancellationToken);

        // Start the food selection dialog.
        return await stepContext.BeginDialogAsync(Dialogs.OrderPrompt, null, cancellationToken);
    }

    public static async Task<DialogTurnResult> GetRoomNumberAsync(
        WaterfallStepContext stepContext,
        CancellationToken cancellationToken)
    {
        if (stepContext.Result != null && stepContext.Result is OrderCart cart)
        {
            // If there are items in the order, record the order and ask for a room number.
            stepContext.Values[Outputs.OrderCart] = cart;
            return await stepContext.PromptAsync(
                Inputs.Number,
                new PromptOptions
                {
                    Prompt = MessageFactory.Text("What is your room number?"),
                    RetryPrompt = MessageFactory.Text("Please enter your room number."),
                },
                cancellationToken);
        }
        else
        {
            // Otherwise, assume the order was cancelled by the guest and exit.
            return await stepContext.EndDialogAsync(null, cancellationToken);
        }
    }

    public static async Task<DialogTurnResult> ProcessOrderAsync(
        WaterfallStepContext stepContext,
        CancellationToken cancellationToken)
    {
        // Get and save the guest's answer.
        var roomNumber = (int)stepContext.Result;
        stepContext.Values[Outputs.RoomNumber] = roomNumber;

        // Process the dinner order using the collected order cart and room number.

        await stepContext.Context.SendActivityAsync(
            $"Thank you. Your order will be delivered to room {roomNumber} within 45 minutes.",
            cancellationToken: cancellationToken);
        return await stepContext.EndDialogAsync(null, cancellationToken);
    }
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Fügen Sie im Botkonstruktor den Wasserfalldialog `orderDinner` hinzu.

```javascript
// Order dinner:
// Help user order dinner from a menu
this.dialogSet.add(new WaterfallDialog('orderDinner', [
    async function (step) {
        await step.context.sendActivity("Welcome to our dinner order service.");

        return await step.beginDialog('orderPrompt', step.values.orderCart = {
            orders: [],
            total: 0
        }); // Prompt for orders
    },
    async function (step) {
        if (step.result == "Cancel") {
            return await step.endDialog();
        } else {
            return await step.prompt('numberPrompt', "What is your room number?");
        }
    },
    async function (step) {
        await step.context.sendActivity(`Thank you. Your order will be delivered to room ${step.result} within 45 minutes.`);
        return await step.endDialog();
    }
]));
```

---

### <a name="create-the-order-prompt-dialog"></a>Erstellen des Dialogs mit der Eingabeaufforderung für die Bestellung

Im Dialog für die Essenswahl wird dem Gast eine Liste mit Optionen angezeigt. Diese Liste umfasst sowohl die bestellbaren Essensoptionen als auch die beiden Bestellverarbeitungsanforderungen. Der Dialog durchläuft eine Schleife, bis der Gast den Bot auffordert, die Bestellung entweder zu verarbeiten oder abzubrechen.

* Wenn der Gast eine Essensoption auswählt, wird diese dem _Warenkorb_ für den Gast hinzugefügt.
* Wenn sich der Gast für die Verarbeitung der Bestellung entscheidet, wird zuerst geprüft, ob der Warenkorb leer ist.
  * Ist der Warenkorb leer, wird eine Fehlermeldung ausgegeben, und die Schleife wird fortgesetzt.
  * Andernfalls wir der Dialog beendet, und die Warenkorbinformationen werden an den übergeordneten Dialog zurückgegeben.
* Wenn sich der Gast entscheidet, die Bestellung abzubrechen, wird der Dialog ohne Rückgabe von Warenkorbinformationen beendet.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Fügen Sie den Wasserfalldialog im Konstruktor hinzu. Definieren Sie anschließend die Wasserfallschritte. Hier haben wir diese Schritte in einer geschachtelten Klasse definiert.

Im Konstruktor **HotelDialogs**:

```csharp
// Define the steps for and add the order-prompt dialog.
WaterfallStep[] orderPromptDialogSteps = new WaterfallStep[]
{
    OrderPromptSteps.PromptForItemAsync,
    OrderPromptSteps.ProcessInputAsync,
};

Add(new WaterfallDialog(Dialogs.OrderPrompt, orderPromptDialogSteps));
```

* Im ersten Schritt wird der Dialogzustand initialisiert. Wenn die Eingabeargumente für den Dialog Warenkorbinformationen enthalten, werden diese im Dialogzustand gespeichert. Andernfalls wird ein leerer Warenkorb erstellt und hinzufügt. Anschließend wird der Gast aufgefordert, etwas von der Speisekarte auszuwählen.
* Im nächsten Schritt wird die vom Gast gewählte Option überprüft:
  * Wurde die Verarbeitung der Bestellung angefordert, wird überprüft, ob sich etwas im Warenkorb befindet.
    * Ist dies der Fall, wird der Dialog beendet, und der Inhalt des Warenkorbs wird zurückgegeben.
    * Andernfalls wird eine Fehlermeldung an den Gast gesendet, und der Dialog beginnt von vorn.
  * Wurde der Abbruch der Bestellung angefordert, wird der Dialog beendet und ein leeres Wörterbuch zurückgegeben.
  * Handelt es sich um ein Essenselement, wird es dem Warenkorb hinzugefügt, es wird eine Statusnachricht gesendet, der Dialog wird von vorn gestartet, und der aktuelle Bestellzustand wird übergeben.

Fügen Sie die Schrittdefinitionen in der **HotelDialogs**-Klasse hinzu.

```csharp
/// <summary>
/// Contains the waterfall dialog steps for the order-prompt dialog.
/// </summary>
private static class OrderPromptSteps
{
    public static async Task<DialogTurnResult> PromptForItemAsync(
        WaterfallStepContext stepContext,
        CancellationToken cancellationToken)
    {
        // First time through, options will be null.
        var cart = (stepContext.Options is OrderCart oldCart && oldCart != null)
            ? new OrderCart(oldCart) : new OrderCart();

        stepContext.Values[Outputs.OrderCart] = cart;
        stepContext.Values[Outputs.OrderTotal] = cart.Sum(item => item.Price);

        return await stepContext.PromptAsync(
            Inputs.Choice,
            new PromptOptions
            {
                Prompt = MessageFactory.Text("What would you like?"),
                RetryPrompt = Lists.MenuReprompt,
                Choices = Lists.MenuChoices,
            },
            cancellationToken);
    }

    public static async Task<DialogTurnResult> ProcessInputAsync(
        WaterfallStepContext stepContext,
        CancellationToken cancellationToken)
    {
        // Get the guest's choice.
        var choice = (FoundChoice)stepContext.Result;
        var menuOption = Lists.MenuOptions[choice.Index];

        // Get the current order from dialog state.
        var cart = (OrderCart)stepContext.Values[Outputs.OrderCart];

        if (menuOption.Name is MenuChoice.Process)
        {
            if (cart.Count > 0)
            {
                // If there are any items in the order, then exit this dialog,
                // and return the list of selected food items.
                return await stepContext.EndDialogAsync(cart, cancellationToken);
            }
            else
            {
                // Otherwise, send an error message and restart from
                // the beginning of this dialog.
                await stepContext.Context.SendActivityAsync(
                    "Your cart is empty. Please add at least one item to the cart.",
                    cancellationToken: cancellationToken);
                return await stepContext.ReplaceDialogAsync(Dialogs.OrderPrompt, null, cancellationToken);
            }
        }
        else if (menuOption.Name is MenuChoice.Cancel)
        {
            await stepContext.Context.SendActivityAsync(
                "Your order has been cancelled.",
                cancellationToken: cancellationToken);

            // Exit this dialog, returning null.
            return await stepContext.EndDialogAsync(null, cancellationToken);
        }
        else
        {
            // Add the selected food item to the order and update the order total.
            cart.Add(menuOption);
            var total = (double)stepContext.Values[Outputs.OrderTotal] + menuOption.Price;
            stepContext.Values[Outputs.OrderTotal] = total;

            await stepContext.Context.SendActivityAsync(
                $"Added {menuOption.Name} (${menuOption.Price:0.00}) to your order." +
                    Environment.NewLine + Environment.NewLine +
                    $"Your current total is ${total:0.00}.",
                cancellationToken: cancellationToken);

            // Present the order options again, passing in the current order state.
            return await stepContext.ReplaceDialogAsync(Dialogs.OrderPrompt, cart);
        }
    }
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Fügen Sie im Botkonstruktor den Wasserfalldialog `orderPrompt` hinzu.

```javascript
// Helper dialog to repeatedly prompt user for orders
this.dialogSet.add(new WaterfallDialog('orderPrompt', [
    async function (step) {
        // Define a new cart of one does not exists
        step.values.orderCart = step.options;

        return await step.prompt('choicePrompt', "What would you like?", dinnerMenu.choices);
    },
    async function (step) {
        const choice = step.result;
        if (choice.value.match(/process order/ig)) {
            if (step.values.orderCart.orders.length > 0) {
                // Process the order
                await step.context.sendActivity("Processing your order.");
                // ...
                step.values.orderCart = undefined; // Reset cart
                return await step.endDialog();
            } else {
                await step.context.sendActivity("Your cart was empty. Please add at least one item to the cart.");
                // Ask again
                return await step.replaceDialog('orderPrompt', step.values.orderCart);
            }
        } else if (choice.value.match(/cancel/ig)) {
            await step.context.sendActivity("Your order has been canceled.");
            return await step.endDialog(choice.value);
        } else {
            var item = dinnerMenu[choice.value];

            // Only proceed if user chooses an item from the menu
            if (!item) {
                await step.context.sendActivity("Sorry, that is not a valid item. Please pick one from the menu.");

                // Ask again
                return await step.replaceDialog('orderPrompt', step.values.orderCart);
            } else {
                // Add the item to cart
                step.values.orderCart.orders.push(item);
                step.values.orderCart.total += item.Price;

                await step.context.sendActivity(`Added to cart: ${choice.value}. <br/>Current total: $${step.values.orderCart.total}`);

                // Ask again
                return await step.replaceDialog('orderPrompt', step.values.orderCart);
            }
        }
    }
]));
```

Der obige Beispielcode zeigt, dass der Hauptdialog `orderDinner` einen Hilfsdialog mit dem Namen `orderPrompt` verwendet, um die Benutzerauswahl zu verarbeiten. Der Dialog `orderPrompt` zeigt das Menü mit den Essensoptionen an, fordert den Benutzer auf, etwas auszuwählen, fügt die Auswahl dem Warenkorb hinzu und kehrt in einer Schleife wieder zur Eingabeaufforderung zurück. So kann der Benutzer seiner Bestellung mehrere Artikel hinzufügen. Die Dialogschleife wird solange ausgeführt, bis der Benutzer `Process order` oder `Cancel` auswählt. An diesem Punkt wird die Ausführung wieder dem übergeordneten Dialog (`orderDinner`) übergeben. Der `orderDinner`-Dialog erledigt einige abschließende Dinge, wenn der Benutzer die Bestellung aufgeben möchte; andernfalls endet er und gibt die Ausführung an den übergeordneten Dialog zurück (z.B.: `mainMenu`). Der `mainMenu`-Dialog wiederum setzt die Ausführung des letzten Schritts fort, die einfach im erneuten Anzeigen der Hauptmenüoptionen besteht.

---

### <a name="create-the-reserve-table-dialog"></a>Erstellen des Dialogs für die Tischreservierung

<!-- TODO: Update the "Manage simple conversation flows" topic to use a reserveTable dialog, and then reference that from here. -->

Damit dieses Beispiel nicht zu lang wird, beschränken wir uns hier auf eine minimale Implementierung des Dialogs `reserveTable`.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Fügen Sie den Wasserfalldialog im Konstruktor hinzu. Definieren Sie anschließend die Wasserfallschritte. Hier haben wir diese Schritte in einer geschachtelten Klasse definiert.

Im Konstruktor **HotelDialogs**:

```csharp
// Define the steps for and add the reserve-table dialog.
WaterfallStep[] reserveTableDialogSteps = new WaterfallStep[]
{
    ReserveTableSteps.StubAsync,
};

Add(new WaterfallDialog(Dialogs.ReserveTable, reserveTableDialogSteps));
```

Fügen Sie die Schrittdefinitionen in der **HotelDialogs**-Klasse hinzu.

```csharp
/// <summary>
/// Contains the waterfall dialog steps for the reserve-table dialog.
/// </summary>
private static class ReserveTableSteps
{
    public static async Task<DialogTurnResult> StubAsync(
        WaterfallStepContext stepContext,
        CancellationToken cancellationToken)
    {
        await stepContext.Context.SendActivityAsync(
            "Your table has been reserved.",
            cancellationToken: cancellationToken);

        return await stepContext.EndDialogAsync(null, cancellationToken);
    }
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Fügen Sie im Botkonstruktor den Platzhalter-Wasserfalldialog `reserveTable` hinzu.

```javascript
// Reserve a table:
// Help the user to reserve a table
this.dialogSet.add(new WaterfallDialog('reserveTable', [
    // Replace this waterfall with your reservation steps.
    async function(step){
        await step.context.sendActivity("Your table has been reserved");
        await step.endDialog();
    }
]));
```

---

### <a name="update-the-bot-code-to-call-the-dialogs"></a>Aktualisieren des Botcodes zum Aufrufen der Dialoge

Aktualisieren Sie den Code für den Turn-Handler des Bots, um den Dialog aufzurufen.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Benennen Sie **EchoBotAccessors.cs** in **BotAccessors.cs** und die Klasse von `EchoBotAccessors` in `BotAccessors` um. Aktualisieren Sie die using-Anweisungen und die Klassendefinition, um den Zustandseigenschaftenaccessor bereitzustellen, den wir für diesen Bot benötigen.

```csharp
using System;
using Microsoft.Bot.Builder;
using Microsoft.Bot.Builder.Dialogs;
```

```csharp
/// <summary>
/// This class is created as a Singleton and passed into the IBot-derived constructor.
///  - See <see cref="EchoWithCounterBot"/> constructor for how that is injected.
///  - See the Startup.cs file for more details on creating the Singleton that gets
///    injected into the constructor.
/// </summary>
public class BotAccessors
{
    /// <summary>
    /// Initializes a new instance of the <see cref="BotAccessors"/> class.
    /// Contains the <see cref="ConversationState"/> and associated <see cref="IStatePropertyAccessor{T}"/>.
    /// </summary>
    /// <param name="conversationState">The state object that stores the counter.</param>
    public BotAccessors(ConversationState conversationState)
    {
        ConversationState = conversationState ?? throw new ArgumentNullException(nameof(conversationState));
    }

    /// <summary>
    /// Gets the <see cref="IStatePropertyAccessor{T}"/> name used for the <see cref="DialogState"/> accessor.
    /// </summary>
    /// <remarks>Accessors require a unique name.</remarks>
    /// <value>The accessor name for the dialog state accessor.</value>
    public static string DialogStateAccessorName { get; } = $"{nameof(BotAccessors)}.DialogState";

    /// <summary>
    /// Gets or sets the DialogState property accessor.
    /// </summary>
    /// <value>
    /// The DialogState property accessor.
    /// </value>
    public IStatePropertyAccessor<DialogState> DialogStateAccessor { get; set; }

    /// <summary>
    /// Gets the <see cref="ConversationState"/> object for the conversation.
    /// </summary>
    /// <value>The <see cref="ConversationState"/> object.</value>
    public ConversationState ConversationState { get; }
}
```

Aktualisieren Sie die Datei **Startup.cs**, um das `BotAccessors`-Singleton zu konfigurieren.

1. Aktualisieren Sie die using-Anweisungen.

    ```csharp
    using System;
    using System.Linq;
    using Microsoft.AspNetCore.Builder;
    using Microsoft.AspNetCore.Hosting;
    using Microsoft.Bot.Builder;
    using Microsoft.Bot.Builder.Dialogs;
    using Microsoft.Bot.Builder.Integration;
    using Microsoft.Bot.Builder.Integration.AspNet.Core;
    using Microsoft.Bot.Configuration;
    using Microsoft.Bot.Connector.Authentication;
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.DependencyInjection;
    using Microsoft.Extensions.Logging;
    using Microsoft.Extensions.Options;
    ```

1. Aktualisieren Sie den Teil der `ConfigureServices`-Methode, mit dem die Bot-Zustandseigenschaftenaccessoren registriert werden.

    ```csharp
    // Create and register state accesssors.
    // Acessors created here are passed into the IBot-derived class on every turn.
    services.AddSingleton<BotAccessors>(sp =>
    {
        var options = sp.GetRequiredService<IOptions<BotFrameworkOptions>>().Value;
        var conversationState = options.State.OfType<ConversationState>().FirstOrDefault();

        // Create the custom state accessor.
        // State accessors enable other components to read and write individual properties of state.
        var accessors = new BotAccessors(conversationState)
        {
            DialogStateAccessor = conversationState.CreateProperty<DialogState>(BotAccessors.DialogStateAccessorName),
        };

        return accessors;
    });
    ```

Benennen Sie die Datei „EchoWithCounterBot.cs“ in „HotelBot.cs“ und die Klasse von „EchoWithCounterBot“ in „HotelBot“ um.

1. Aktualisieren Sie den Initialisierungscode für den Bot.

    ```csharp
    private readonly BotAccessors _accessors;
    private readonly HotelDialogs _dialogs;
    private readonly ILogger _logger;

    /// <summary>
    /// Initializes a new instance of the <see cref="HotelBot"/> class.
    /// </summary>
    /// <param name="accessors">A class containing <see cref="IStatePropertyAccessor{T}"/> used to manage state.</param>
    /// <param name="loggerFactory">A <see cref="ILoggerFactory"/> that is hooked to the Azure App Service provider.</param>
    public HotelBot(BotAccessors accessors, ILoggerFactory loggerFactory)
    {
        _logger = loggerFactory.CreateLogger<HotelBot>();
        _logger.LogTrace("EchoBot turn start.");
        _accessors = accessors;
        _dialogs = new HotelDialogs(_accessors.DialogStateAccessor);
    }
    ```

1. Aktualisieren Sie den Turn-Handler für den Bot, damit er den Dialog ausführt.

    ```csharp
    public async Task OnTurnAsync(ITurnContext turnContext, CancellationToken cancellationToken = default(CancellationToken))
    {
        var dc = await _dialogs.CreateContextAsync(turnContext, cancellationToken);

        if (turnContext.Activity.Type == ActivityTypes.Message)
        {
            await dc.ContinueDialogAsync(cancellationToken);
            if (!turnContext.Responded)
            {
                await dc.BeginDialogAsync(HotelDialogs.MainMenu, null, cancellationToken);
            }
        }
        else if (turnContext.Activity.Type == ActivityTypes.ConversationUpdate)
        {
            var activity = turnContext.Activity.AsConversationUpdateActivity();
            if (activity.MembersAdded.Any(member => member.Id != activity.Recipient.Id))
            {
                await dc.BeginDialogAsync(HotelDialogs.MainMenu, null, cancellationToken);
            }
        }

        await _accessors.ConversationState.SaveChangesAsync(turnContext, false, cancellationToken);
    }
    ```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
async onTurn(turnContext) {
    let dc = await this.dialogSet.createContext(turnContext);

    // See https://aka.ms/about-bot-activity-message to learn more about the message and other activity types.
    if (turnContext.activity.type === ActivityTypes.Message) {

        await dc.continueDialog();

        if (!turnContext.responded) {
            await dc.beginDialog('mainMenu');
        }
    } else if (turnContext.activity.type === ActivityTypes.ConversationUpdate) {
        // Do we have any new members added to the conversation?
        if (turnContext.activity.membersAdded.length !== 0) {
            // Iterate over all new members added to the conversation
            for (var idx in turnContext.activity.membersAdded) {
                // Greet anyone that was not the target (recipient) of this message.
                // Since the bot is the recipient for events from the channel,
                // context.activity.membersAdded === context.activity.recipient.Id indicates the
                // bot was added to the conversation, and the opposite indicates this is a user.
                if (turnContext.activity.membersAdded[idx].id !== turnContext.activity.recipient.id) {
                    // Start the dialog.
                    await dc.beginDialog('mainMenu');
                }
            }
        }
    }

    // Save state changes
    await this.conversationState.saveChanges(turnContext);
}
```

---

## <a name="next-steps"></a>Nächste Schritte

Sie können diesen Bot um weitere Menüoptionen wie „Weitere Informationen“ oder „Hilfe“ erweitern. Weitere Informationen zur Implementierung dieser Arten von Unterbrechungen finden Sie unter [Behandeln von Benutzerunterbrechungen](bot-builder-howto-handle-user-interrupt.md).

Sie wissen nun, wie Sie komplexe Dialogabläufe mithilfe von Dialogen, Eingabeaufforderungen und Wasserfällen verwalten. Im nächsten Teil erfahren Sie, wie Sie Dialoge (beispielsweise `orderDinner` und `reserveTable`) in separate Objekte unterteilen können, anstatt alles in einem großen Dialogsatz zusammenzufassen.

> [!div class="nextstepaction"]
> [Erstellen modularer Botlogik mit zusammengesetzten Steuerelementen](bot-builder-compositcontrol.md)
