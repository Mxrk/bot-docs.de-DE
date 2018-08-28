---
title: Verwalten eines komplexen Konversationsablaufs mit Dialogen | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie einen komplexen Konversationsablauf mit Dialogen im Bot Builder SDK für Node.js verwalten.
keywords: komplexer Konversationsablauf, Wiederholung, Schleife, Menü, Dialoge, Eingabeaufforderungen, Wasserfälle, Dialogsatz
author: v-ducvo
ms.author: v-ducvo
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 7/27/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 304de6783a268140bf74f95d96cd9c24e12c4c05
ms.sourcegitcommit: 1abc32353c20acd103e0383121db21b705e5eec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/21/2018
ms.locfileid: "40236360"
---
# <a name="manage-complex-conversation-flows-with-dialogs"></a>Verwalten komplexer Konversationsabläufe mit Dialogen

[!INCLUDE [pre-release-label](~/includes/pre-release-label.md)]

Mit der Dialogbibliothek können Sie sowohl einfache als auch komplexe Konversationsabläufe verwalten. Bei [einfachen Konversationsabläufen](bot-builder-dialog-manage-conversation-flow.md) beginnt der Benutzer mit dem ersten Schritt eines *Wasserfalls* und durchläuft alle weiteren Schritte bis zum letzten Schritt, wo die Konversation dann endet. In diesem Artikel verwenden wir Dialoge, um komplexere Konversationen mit Verzweigungen und Schleifen zu verwalten. Zu diesem Zweck verwenden wir die Methode _replace_ des Dialogkontexts und übergeben Argumente zwischen verschiedenen Teilen des Dialogs.

<!-- TODO: We need a dialogs conceptual topic to link to, so we can reference that here, in place of describing what they are and what their features are in a how-to topic. -->

Die Methode _replace_ aus der Bibliothek **Dialogs** gibt Ihnen mehr Kontrolle über den *Dialogstapel*. Mit dieser Methode können Sie den aktuellen Dialog per Pop aus dem Stapel entfernen, per Push ganz oben im Stapel einen neuen Dialog einfügen und den neuen Dialog starten. Dies ermöglicht komplexere Konversationen. Mithilfe dieser Techniken sind der Komplexität der Konversationen keine Grenzen gesetzt. Sollten die Dialoge aufgrund der hohen Komplexität der Konversation nur noch schwer zu verwalten sein, können Sie Ihre eigene Ablaufsteuerungslogik erstellen, um die Konversation des Benutzers weiter nachvollziehen zu können.

<!-- TODO: This is probably a good place to add a link to the modular/composite dialogs topic. -->

In diesem Artikel erstellen wir Dialoge für einen Hotelbot, mit denen ein Gast einen Tisch reservieren oder sich etwas zu essen aufs Zimmer bestellen kann. Der Dialog auf der obersten Ebene bietet dem Gast zwei Optionen. Wenn der Gast einen Tisch reservieren möchte, wird ein Dialog für eine Tischreservierung gestartet. Wenn der Gast etwas zu essen bestellen möchte, wird ein Dialog für die Essensbestellung gestartet. Im Dialog für die Essensbestellung wird der Gast gebeten, etwas von einer Speisekarte zu wählen. Anschließend wird er nach seiner Zimmernummer gefragt. Die Wahl der Speisen ist ebenfalls ein Dialog, sodass der Gast mehrere Dinge auswählen kann, bevor die Essensbestellung verarbeitet wird.

Das folgende Diagramm veranschaulicht die Dialoge, die in diesem Artikel erstellt werden, sowie die Beziehungen zwischen ihnen.

![Abbildung der Dialoge aus diesem Artikel](~/media/complex-conversation-flows.png)

## <a name="how-to-branch"></a>Verwenden von Verzweigungen

Der Dialogkontext pflegt einen _Dialogstapel_ und verfolgt für jeden Dialog aus dem Stapel nach, welcher Schritt als Nächstes ansteht. Die Methode _begin_ fügt per Push ganz oben im Stapel einen Dialog ein, und die Methode _end_ entfernt den obersten Dialog per Pop aus dem Stapel.

Ein Dialog kann innerhalb des gleichen Dialogsatzes einen neuen Dialog starten (Verzweigung). Hierzu wird die Methode _begin_ des Dialogkontexts aufgerufen und die ID des neuen Dialogs angegeben. Der ursprüngliche Dialog befindet sich zwar weiterhin im Stapel, Aufrufe der Methode _continue_ des Dialogkontexts werden aber nur an den Dialog gesendet, der sich ganz oben im Stapel befindet (_aktiver Dialog_). Wenn ein Dialog per Pop aus dem Stapel entfernt wird, fährt der Dialogkontext mit dem ursprünglichen nächsten Schritt des Wasserfalls aus dem Stapel fort.

Somit können Sie eine Verzweigung innerhalb Ihres Konversationsablaufs erstellen, indem Sie einen Schritt in einen Dialog einschließen, der bei Bedarf einen Dialog aus einer Gruppe potenzieller Dialoge startet.

## <a name="how-to-loop"></a>Verwenden einer Schleife

Mit der Methode _replace_ des Dialogkontexts können Sie den Dialog ersetzen, der sich ganz oben im Stapel befindet. Der Zustand des alten Dialogs wird verworfen, und der neue Dialog beginnt von vorn. Mit dieser Methode können Sie also Schleife erstellen, indem Sie einen Dialog durch sich selbst ersetzen. Damit der [Zustand erhalten](bot-builder-howto-v4-state.md) bleibt, müssen Sie im Aufruf der Methode _replace_ allerdings Informationen an die neue Instanz des Dialogs übergeben. Im folgenden Beispiel sehen Sie, dass im Dialogzustand der Warenkorb für die Essensbestellung gespeichert und bei der Wiederholung des Dialogs `orderPrompt` der aktuelle Dialogzustand übergeben wird, damit der neue Dialogzustand weitere Elemente hinzufügen kann. Falls Sie diese Informationen lieber in den anderen Zuständen speichern möchten, lesen Sie [Speichern von Benutzerdaten](bot-builder-tutorial-persist-user-inputs.md).

## <a name="create-the-dialogs-for-the-hotel-bot"></a>Erstellen der Dialoge für den Hotelbot

In diesem Abschnitt erstellen wir die Dialoge zur Verwaltung einer Konversation für den zuvor beschriebenen Hotelbot.

### <a name="install-the-dialogs-library"></a>Installieren der Dialogebibliothek

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Wir beginnen mit einer einfachen EchoBot-Vorlage. Entsprechende Anweisungen finden Sie in der [Schnellstartanleitung für .NET](~/dotnet/bot-builder-dotnet-quickstart.md).

Um Dialoge zu verwenden, installieren Sie das `Microsoft.Bot.Builder.Dialogs`NuGet-Paket für Ihr Projekt oder Ihre Projektmappe.
Verweisen Sie anschließend in using-Anweisungen Ihrer Codedateien auf die Dialogbibliothek.

```csharp
using Microsoft.Bot.Builder.Dialogs;
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Die `botbuilder-dialogs`-Bibliothek kann von NPM heruntergeladen werden. Um die `botbuilder-dialogs`-Bibliothek zu installieren, führen Sie den folgenden NPM-Befehl aus:

```cmd
npm install --save botbuilder-dialogs
```

Um **Dialoge** in Ihrem Bot zu verwenden, schließen Sie sie in den Botcode ein. Fügen Sie der Datei **app.js** beispielsweise Folgendes hinzu:

```javascript
const botbuilder_dialogs = require('botbuilder-dialogs');
```

---

### <a name="create-a-dialog-set"></a>Erstellen eines Dialogsatzes

Erstellen Sie einen _Dialogsatz_, dem wir dann alle Dialoge für dieses Beispiel hinzufügen können.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Erstellen Sie eine Klasse namens **HotelDialog**, und fügen Sie anschließend die erforderlichen using-Anweisungen hinzu.

```csharp
using Microsoft.Bot.Builder.Core.Extensions;
using Microsoft.Bot.Builder.Dialogs;
using Microsoft.Bot.Builder.Prompts.Choices;
using Microsoft.Bot.Schema;
using Microsoft.Recognizers.Text;
using System;
using System.Collections.Generic;
using System.Linq;
```

Leiten Sie die Klasse von **DialogSet** ab, und definieren Sie die IDs und Schlüssel zur Identifizierung der Dialoge, Eingabeaufforderungen und Zustandsinformationen für diesen Dialogsatz.

```csharp
/// <summary>Contains the set of dialogs and prompts for the hotel bot.</summary>
public class HotelDialog : DialogSet
{
    /// <summary>The ID of the top-level dialog.</summary>
    public const string MainMenu = "mainMenu";

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

Um **Dialoge** in Ihrem Bot zu verwenden, schließen Sie sie in den Botcode ein.

Verweisen Sie auf die Bibliothek aus der Datei **app.js**.

```javascript
const botbuilder_dialogs = require('botbuilder-dialogs');
```

Erstellen Sie dann den Dialogsatz.

```javascript
const dialogs = new botbuilder_dialogs.DialogSet();
```

---

### <a name="add-the-prompts-to-the-set"></a>Hinzufügen der Eingabeaufforderungen zu dem Satz

Wir verwenden **ChoicePrompt**, um Gäste zu fragen, ob sie etwas zu essen bestellen oder einen Tisch reservieren möchten, und um es ihnen zu ermöglichen, etwas von der Speisekarte zu wählen. Außerdem verwenden wir **NumberPrompt**, um den Gast nach seiner Zimmernummer zu fragen, wenn er etwas zu essen bestellt.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Fügen Sie dem Konstruktor **HotelDialog** die beiden folgenden Eingabeaufforderungen hinzu:

```csharp
// Add the prompts.
this.Add(Inputs.Choice, new ChoicePrompt(Culture.English));
this.Add(Inputs.Number, new NumberPrompt<int>(Culture.English));
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Fügen Sie dem Dialogsatz die beiden folgenden Eingabeaufforderungen hinzu:

```javascript
dialogs.add('choicePrompt', new botbuilder_dialogs.ChoicePrompt());
dialogs.add('numberPrompt', new botbuilder_dialogs.NumberPrompt());
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
    /// <summary>The text to show the guest for this option.</summary>
    public string Description { get; set; }

    /// <summary>The ID of the associated dialog for this option.</summary>
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

    /// <summary>The name of the meal item or the request.</summary>
    public string Name { get; set; }

    /// <summary>The price of the meal item; or NaN for a request.</summary>
    public double Price { get; set; }

    /// <summary>The text to show the guest for this option.</summary>
    public string Description => (double.IsNaN(Price)) ? Name : $"{Name} - ${Price:0.00}";
}

/// <summary>Contains the lists used to present options to the guest.</summary>
private static class Lists
{
    /// <summary>The options for the top-level dialog.</summary>
    public static List<WelcomeChoice> WelcomeOptions { get; } = new List<WelcomeChoice>
    {
        new WelcomeChoice { Description = "Order dinner", DialogName = Dialogs.OrderDinner },
        new WelcomeChoice { Description = "Reserve a table", DialogName = Dialogs.ReserveTable },
    };

    private static List<string> WelcomeList { get; } = WelcomeOptions.Select(x => x.Description).ToList();

    /// <summary>The choices to present in the choice prompt for the top-level dialog.</summary>
    public static List<Choice> WelcomeChoices { get; } = ChoiceFactory.ToChoices(WelcomeList);

    /// <summary>The reprompt action for the top-level dialog.</summary>
    public static Activity WelcomeReprompt
    {
        get
        {
            var reprompt = MessageFactory.SuggestedActions(WelcomeList, "Please choose an option");
            reprompt.AttachmentLayout = AttachmentLayoutTypes.List;
            return reprompt as Activity;
        }
    }

    /// <summary>The options for the food-selection dialog.</summary>
    public static List<MenuChoice> MenuOptions { get; } = new List<MenuChoice>
    {
        new MenuChoice { Name = "Potato Salad", Price = 5.99 },
        new MenuChoice { Name = "Tuna Sandwich", Price = 6.89 },
        new MenuChoice { Name = "Clam Chowder", Price = 4.50 },
        new MenuChoice { Name = MenuChoice.Process, Price = double.NaN },
        new MenuChoice { Name = MenuChoice.Cancel, Price = double.NaN },
    };

    private static List<string> MenuList { get; } = MenuOptions.Select(x => x.Description).ToList();

    /// <summary>The choices to present in the choice prompt for the food-selection dialog.</summary>
    public static List<Choice> MenuChoices { get; } = ChoiceFactory.ToChoices(MenuList);

    /// <summary>The reprompt action for the food-selection dialog.</summary>
    public static Activity MenuReprompt
    {
        get
        {
            var reprompt = MessageFactory.SuggestedActions(MenuList, "Please choose an option");
            reprompt.AttachmentLayout = AttachmentLayoutTypes.List;
            return reprompt as Activity;
        }
    }
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Erstellen Sie für diese Informationen die Konstante **dinnerMenu**.

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

Dieser Dialog verwendet einen `ChoicePrompt` zur Anzeige des Menüs und wartet darauf, dass der Benutzer eine Option auswählt. Wenn der Benutzer entweder `Order dinner` oder `Reserve a table` auswählt, wird der entsprechende Dialog gestartet. Nach Abschluss dieses Dialogs wird der Dialog `mainMenu` wiederholt. Hierzu wird der Dialog `mainMenu` erneut gestartet, anstatt ihn im letzten Schritt einfach zu beenden und den Benutzer im Dunkeln zu lassen. Mit dieser einfachen Struktur zeigt der Bot immer das Menü an, und der Benutzer weiß jederzeit, welche Optionen er hat.

Der Dialog **MainMenu** enthält folgende Wasserfallschritte:

* Im ersten Schritt des Wasserfalls wird der Dialogzustand initialisiert oder gelöscht. Außerdem wird der Benutzer begrüßt und dazu aufgefordert, eine der verfügbaren Optionen (`Order dinner` oder `Reserve a table`) auszuwählen.
* Im zweiten Schritt wird die getroffene Auswahl abgerufen und der entsprechende untergeordnete Dialog gestartet. Am Ende des untergeordneten Dialogs wird der ursprüngliche Dialog mit dem folgenden Schritt fortgesetzt.
* Im letzten Schritt ersetzen wir diesen Dialog mithilfe der Methode **DialogContext.Replace** durch eine neue Instanz von sich selbst und verwandeln dadurch den Willkommensdialog in eine Menüschleife.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
// Add the main welcome dialog.
this.Add(MainMenu, new WaterfallStep[]
{
    async (dc, args, next) =>
    {
        // Greet the guest and ask them to choose an option.
        await dc.Context.SendActivity("Welcome to Contoso Hotel and Resort.");
        await dc.Prompt(Inputs.Choice, "How may we serve you today?", new ChoicePromptOptions
        {
            Choices = Lists.WelcomeChoices,
            RetryPromptActivity = Lists.WelcomeReprompt,
        });
    },
    async (dc, args, next) =>
    {
        // Begin a child dialog associated with the chosen option.
        var choice = (FoundChoice)args["Value"];
        var dialogId = Lists.WelcomeOptions[choice.Index].DialogName;

        await dc.Begin(dialogId, dc.ActiveDialog.State);
    },
    async (dc, args, next) =>
    {
        // Start this dialog over again.
        await dc.Replace(MainMenu);
    },
});
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
// Display a menu and ask user to choose a menu item. Direct user to the item selected otherwise, show
// the menu again.
dialogs.add('mainMenu', [
    async function(dc){
        await dc.context.sendActivity("Welcome to Contoso Hotel and Resort.");
        await dc.prompt('choicePrompt', "How may we serve you today?", ['Order Dinner', 'Reserve a table']);
    },
    async function(dc, result){
        if(result.value.match(/order dinner/ig)){
            await dc.begin('orderDinner');
        }
        else if(result.value.match(/reserve a table/ig)){
            await dc.begin('reserveTable');
        }
    },
    async function(dc, result){
        // Start over
        await dc.endAll().begin('mainMenu');
    }
]);
```

---

### <a name="create-the-order-dinner-dialog"></a>Erstellen des Dialogs für die Essensbestellung

Beim Dialog für die Essensbestellung wird der Gast zunächst beim Bestellservice willkommen geheißen und umgehend der Dialog für die Essenswahl gestartet. Auf diesen Dialog gehen wir im nächsten Abschnitt ein. Wichtig: Wenn der Gast die Verarbeitung der Bestellung anfordert, gibt der Dialog für die Essenswahl die Liste mit den Bestellungen zurück. Zum Abschluss des Prozesses wird nach einer Zimmernummer gefragt, an die das Essen geliefert werden soll, und es wird eine Bestätigungsnachricht gesendet. Wenn der Gast die Bestellung abbricht, wird nicht nach einer Zimmernummer gefragt, und der Dialog wird sofort beendet.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Fügen Sie der Klasse **HotelDialog** eine Datenstruktur zum Nachverfolgen der Essensbestellung des Gasts hinzu.

```csharp
/// <summary>Contains the guest's dinner order.</summary>
private class OrderCart : List<MenuChoice> { }
```

Fügen Sie dem Konstruktor **HotelDialog** den Dialog für die Essensbestellung hinzu.

* Im ersten Schritt wird eine Willkommensnachricht gesendet, und der Dialog für die Essenswahl wird gestartet.
* Im zweiten Schritt wird überprüft, ob der Dialog für die Essenswahl einen Warenkorb zurückgegeben hat.
  * Falls ja, wird nach der Zimmernummer gefragt, und die Warenkorbinformationen werden gespeichert.
  * Andernfalls wird davon ausgegangen, dass die Bestellung abgebrochen wurde, und es wird **DialogContext.End** aufgerufen, um den Dialog zu beenden.
* Im letzten Schritt wird die Zimmernummer erfasst und eine Bestätigung an den Gast gesendet. Damit ist der Vorgang abgeschlossen. In diesem Schritt würde der Bot dann die Bestellung an einen Verarbeitungsdienst weiterleiten.

```csharp
// Add the order-dinner dialog.
this.Add(Dialogs.OrderDinner, new WaterfallStep[]
{
    async (dc, args, next) =>
    {
        await dc.Context.SendActivity("Welcome to our Dinner order service.");

        // Start the food selection dialog.
        await dc.Begin(Dialogs.OrderPrompt);
    },
    async (dc, args, next) =>
    {
        if (args.TryGetValue(Outputs.OrderCart, out object arg) && arg is OrderCart cart)
        {
            // If there are items in the order, record the order and ask for a room number.
            dc.ActiveDialog.State[Outputs.OrderCart] = cart;
            await dc.Prompt(Inputs.Number, "What is your room number?", new PromptOptions
            {
                RetryPromptString = "Please enter your room number."
            });
        }
        else
        {
            // Otherwise, assume the order was cancelled by the guest and exit.
            await dc.End();
        }
    },
    async (dc, args, next) =>
    {
        // Get and save the guest's answer.
        var roomNumber = args["Text"] as string;
        dc.ActiveDialog.State[Outputs.RoomNumber] = roomNumber;

        // Process the dinner order using the collected order cart and room number.

        await dc.Context.SendActivity($"Thank you. Your order will be delivered to room {roomNumber} within 45 minutes.");
        await dc.End();
    },
});
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
// Order dinner:
// Help user order dinner from a menu
dialogs.add('orderDinner', [
    async function (dc){
        await dc.context.sendActivity("Welcome to our Dinner order service.");
        
        await dc.begin('orderPrompt', dc.activeDialog.state.orderCart); // Prompt for orders
    },
    async function (dc, result) {
        if(result == "Cancel"){
            await dc.end();
        }
        else { 
            await dc.prompt('numberPrompt', "What is your room number?");
        }
    },
    async function(dc, result){
        await dc.context.sendActivity(`Thank you. Your order will be delivered to room ${result} within 45 minutes.`);
        await dc.end();
    }
]);
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

Fügen Sie dem Konstruktor **HotelDialog** den Dialog für die Essenswahl hinzu.

* Im ersten Schritt wird der Dialogzustand initialisiert. Wenn die Eingabeargumente für den Dialog Warenkorbinformationen enthalten, werden diese im Dialogzustand gespeichert. Andernfalls wird ein leerer Warenkorb erstellt und hinzufügt. Anschließend wird der Gast aufgefordert, etwas von der Speisekarte auszuwählen.
* Im nächsten Schritt wird die vom Gast gewählte Option überprüft:
  * Wurde die Verarbeitung der Bestellung angefordert, wird überprüft, ob sich etwas im Warenkorb befindet.
    * Ist dies der Fall, wird der Dialog beendet, und der Inhalt des Warenkorbs wird zurückgegeben.
    * Andernfalls wird eine Fehlermeldung an den Gast gesendet, und der Dialog beginnt von vorn.
  * Wurde der Abbruch der Bestellung angefordert, wird der Dialog beendet und ein leeres Wörterbuch zurückgegeben.
  * Handelt es sich um ein Essenselement, wird es dem Warenkorb hinzugefügt, es wird eine Statusnachricht gesendet, der Dialog wird von vorn gestartet, und der aktuelle Bestellzustand wird übergeben.

```csharp
// Add the food-selection dialog.
this.Add(Dialogs.OrderPrompt, new WaterfallStep[]
    {
        async (dc, args, next) =>
        {
            if (args is null || !args.ContainsKey(Outputs.OrderCart))
            {
                // First time through, initialize the order state.
                dc.ActiveDialog.State[Outputs.OrderCart] = new OrderCart();
                dc.ActiveDialog.State[Outputs.OrderTotal] = 0.0;
            }
            else
            {
                // Otherwise, set the order state to that of the arguments.
                dc.ActiveDialog.State = new Dictionary<string, object>(args);
            }

            await dc.Prompt(Inputs.Choice, "What would you like?", new ChoicePromptOptions
            {
                Choices = Lists.MenuChoices,
                RetryPromptActivity = Lists.MenuReprompt,
            });
        },
        async (dc, args, next) =>
        {
            // Get the guest's choice.
            var choice = (FoundChoice)args["Value"];
            var option = Lists.MenuOptions[choice.Index];

            // Get the current order from dialog state.
            var cart = (OrderCart)dc.ActiveDialog.State[Outputs.OrderCart];

            if (option.Name is MenuChoice.Process)
            {
                if (cart.Count > 0)
                {
                    // If there are any items in the order, then exit this dialog,
                    // and return the list of selected food items.
                    await dc.End(new Dictionary<string, object>
                    {
                        [Outputs.OrderCart] = cart
                    });
                }
                else
                {
                    // Otherwise, send an error message and restart from
                    // the beginning of this dialog.
                    await dc.Context.SendActivity(
                        "Your cart is empty. Please add at least one item to the cart.");
                    await dc.Replace(Dialogs.OrderPrompt);
                }
            }
            else if (option.Name is MenuChoice.Cancel)
            {
                await dc.Context.SendActivity("Your order has been cancelled.");

                // Exit this dialog, returning an empty property bag.
                dc.ActiveDialog.State.Clear();
                await dc.End(new Dictionary<string, object>());
            }
            else
            {
                // Add the selected food item to the order and update the order total.
                cart.Add(option);
                var total = (double)dc.ActiveDialog.State[Outputs.OrderTotal] + option.Price;
                dc.ActiveDialog.State[Outputs.OrderTotal] = total;

                await dc.Context.SendActivity($"Added {option.Name} (${option.Price:0.00}) to your order." +
                    Environment.NewLine + Environment.NewLine +
                    $"Your current total is ${total:0.00}.");

                // Present the order options again, passing in the current order state.
                await dc.Replace(Dialogs.OrderPrompt, dc.ActiveDialog.State);
            }
        },
    });
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
// Helper dialog to repeatedly prompt user for orders
dialogs.add('orderPrompt', [
    async function(dc, orderCart){
        // Define a new cart of one does not exists
        if(!orderCart){
            // Initialize a new cart
            // convoState = conversationState.get(dc.context);
            dc.activeDialog.state.orderCart = {
                orders: [],
                total: 0
            };
        }
        else {
            dc.activeDialog.state.orderCart = orderCart;
        }
        await dc.prompt('choicePrompt', "What would you like?", dinnerMenu.choices);
    },
    async function(dc, choice){
        // Get state object
        // convoState = conversationState.get(dc.context);

        if(choice.value.match(/process order/ig)){
            if(dc.activeDialog.state.orderCart.orders.length > 0) {
                // Process the order
                // ...
                dc.activeDialog.state.orderCart = undefined; // Reset cart
                await dc.context.sendActivity("Processing your order.");
                await dc.end();
            }
            else {
                await dc.context.sendActivity("Your cart was empty. Please add at least one item to the cart.");
                // Ask again
                await dc.replace('orderPrompt');
            }
        }
        else if(choice.value.match(/cancel/ig)){
            //dc.activeDialog.state.orderCart = undefined; // Reset cart
            await dc.context.sendActivity("Your order has been canceled.");
            await dc.end(choice.value);
        }
        else {
            var item = dinnerMenu[choice.value];

            // Only proceed if user chooses an item from the menu
            if(!item){
                await dc.context.sendActivity("Sorry, that is not a valid item. Please pick one from the menu.");
                
                // Ask again
                await dc.replace('orderPrompt');
            }
            else {
                // Add the item to cart
                dc.activeDialog.state.orderCart.orders.push(item);
                dc.activeDialog.state.orderCart.total += item.Price;

                await dc.context.sendActivity(`Added to cart: ${choice.value}. <br/>Current total: $${dc.activeDialog.state.orderCart.total}`);

                // Ask again
                await dc.replace('orderPrompt', dc.activeDialog.state.orderCart);
            }
        }
    }
]);
```

Der obige Beispielcode zeigt, dass der Hauptdialog `orderDinner` einen Hilfsdialog mit dem Namen `orderPrompt` verwendet, um die Benutzerauswahl zu verarbeiten. Der Dialog `orderPrompt` zeigt das Menü mit den Essensoptionen an, fordert den Benutzer auf, etwas auszuwählen, fügt die Auswahl dem Warenkorb hinzu und kehrt in einer Schleife wieder zur Eingabeaufforderung zurück. So kann der Benutzer seiner Bestellung mehrere Artikel hinzufügen. Die Dialogschleife wird solange ausgeführt, bis der Benutzer `Process order` oder `Cancel` auswählt. An diesem Punkt wird die Ausführung wieder dem übergeordneten Dialog (`orderDinner`) übergeben. Der `orderDinner`-Dialog erledigt einige abschließende Dinge, wenn der Benutzer die Bestellung aufgeben möchte; andernfalls endet er und gibt die Ausführung an den übergeordneten Dialog zurück (z.B.: `mainMenu`). Der `mainMenu`-Dialog wiederum setzt die Ausführung des letzten Schritts fort, die einfach im erneuten Anzeigen der Hauptmenüoptionen besteht.

---
### <a name="create-the-reserve-table-dialog"></a>Erstellen des Dialogs für die Tischreservierung

<!-- TODO: Update the "Manage simple conversation flows" topic to use a reserveTable dialog, and then reference that from here. -->

Damit dieses Beispiel nicht zu lang wird, beschränken wir uns hier auf eine minimale Implementierung des Dialogs `reserveTable`.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
// Add the table-reservation dialog.
this.Add(Dialogs.ReserveTable, new WaterfallStep[]
    {
        // Replace this waterfall with your reservation steps.
        async (dc, args, next) =>
        {
            await dc.Context.SendActivity("Your table has been reserved.");
            await dc.End();
        }
    });
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
// Reserve a table:
// Help the user to reserve a table

dialogs.add('reserveTable', [
    // Replace this waterfall with your reservation steps.
    async function(dc, args, next){
        await dc.context.sendActivity("Your table has been reserved");
        await dc.end();
    }
]);
```

---

## <a name="next-steps"></a>Nächste Schritte

Sie können diesen Bot um weitere Menüoptionen wie „Weitere Informationen“ oder „Hilfe“ erweitern. Weitere Informationen zur Implementierung dieser Arten von Unterbrechungen finden Sie unter [Behandeln von Benutzerunterbrechungen](bot-builder-howto-handle-user-interrupt.md).

Sie wissen nun, wie Sie komplexe Dialogabläufe mithilfe von Dialogen, Eingabeaufforderungen und Wasserfällen verwalten. Im nächsten Teil erfahren Sie, wie Sie Dialoge (beispielsweise `orderDinner` und `reserveTable`) in separate Objekte unterteilen können, anstatt alles in einem großen Dialogsatz zusammenzufassen.

> [!div class="nextstepaction"]
> [Erstellen modularer Botlogik mit zusammengesetzten Steuerelementen](bot-builder-compositcontrol.md)
