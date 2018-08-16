---
title: Behandeln von Benutzerunterbrechungen | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Benutzerunterbrechungen behandelt und Konversationsflüsse gelenkt werden.
keywords: Unterbrechung, Unterbrechungen, Themenwechsel, unterbrechen
author: v-ducvo
ms.author: v-ducvo
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 04/17/2018
ms.reviewer: ''
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 651a7410893f7a66f5941121edc7b34055807ba7
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39301260"
---
# <a name="handle-user-interrupt"></a>Behandeln von Benutzerunterbrechungen

[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

Die Behandlung von Benutzerunterbrechungen ist ein wichtiger Aspekt eines robusten Bots. Während Sie möglicherweise meinen, dass Ihre Benutzer Ihrem Konversationsfluss Schritt für Schritt folgen, ist die Wahrscheinlichkeit groß, dass sie sich anders entscheiden oder mitten im Prozess eine Frage stellen, statt die Frage zu beantworten. Wie würde Ihr Bot in diesen Situationen die Benutzereingaben behandeln? Wie würde die Benutzererfahrung aussehen? Wie würden Sie Benutzerzustandsdaten verwalten?

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

```cs
public class dinnerItem
{
    public string Description;
    public double Price;
}

public class dinnerMenu
{
    static public Dictionary<string, dinnerItem> dinnerChoices = new Dictionary<string, dinnerItem>
    {
        { "potato salad", new dinnerItem { Description="Potato Salad", Price=5.99 } },
        { "tuna sandwich", new dinnerItem { Description="Tuna Sandwich", Price=6.89 } },
        { "clam chowder", new dinnerItem { Description="Clam Chowder", Price=4.50 } }
    };

    static public string[] choices = new string[] {"Potato Salad", "Tuna Sandwich", "Clam Chowder", "more info", "Process order", "help", "Cancel"};
}
```

# <a name="javascripttabjstab"></a>[JavaScript](#tab/jstab)

```javascript
var dinnerMenu = {
    choices: ["Potato Salad - $5.99", "Tuna Sandwich - $6.89", "Clam Chowder - $4.50", 
            "more info", "Process order", "Cancel"],
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

Bei der Bestelllogik können Sie prüfen, ob der Benutzer einen Zeichenfolgenabgleich oder reguläre Ausdrücke verwendet.

# <a name="ctabcsharptab"></a>[C#](#tab/csharptab)

Zunächst müssen wir eine Hilfsprogramm zum Nachverfolgen unserer Aufträge definieren.

```cs
// Helper class for storing the order in the dictionary
public class Orders
{
    public double total;
    public string order;
    public bool processOrder;

    // Initialize order values
    public Orders()
    {
        total = 0;
        order = "";
        processOrder = false;
    }
}
```

Anschließend fügen Sie den Dialog zu Ihrem Bot hinzu.

```cs
dialogs.Add("orderPrompt", new WaterfallStep[]
{
    async (dc, args, next) =>
    {
        // Prompt the user
        await dc.Prompt("choicePrompt",
            "What would you like for dinner?",
            new ChoicePromptOptions
            {
                Choices = dinnerMenu.choices.Select( s => new Choice { Value = s }).ToList(),
                RetryPromptString = "I'm sorry, I didn't understand that. What would you " +
                    "like for dinner?"
            });
    },
    async(dc, args, next) =>
    {
        var convo = ConversationState<Dictionary<string,object>>.Get(dc.Context);

        // Get the user's choice from the previous prompt
        var response = (args["Value"] as FoundChoice).Value.ToLower();

        if(response == "process order")
        {
            try 
            {
                var order = convo["order"];

                await dc.Context.SendActivity("Order is on it's way!");
                
                // In production, you may want to store something more helpful, 
                // such as send order off to be made
                (order as Orders).processOrder = true;

                // Once it's submitted, clear the current order
                convo.Remove("order");
                await dc.End();
            }
            catch
            {
                await dc.Context.SendActivity("Your order is empty, please add your order choice");
                // Ask again
                await dc.Replace("orderPrompt");
            }
        }
        else if(response == "cancel" )
        {
            // Get rid of current order
            convo.Remove("order");
            await dc.Context.SendActivity("Your order has been canceled");
            await dc.End();
        }
        else if(response == "more info")
        {
            // Send more information about the options
            var msg = "More info: <br/>" +
                "Potato Salad: contains 330 calaries per serving. Cost: 5.99 <br/>"
                + "Tuna Sandwich: contains 700 calaries per serving. Cost: 6.89 <br/>"
                + "Clam Chowder: contains 650 calaries per serving. Cost: 4.50";
            await dc.Context.SendActivity(msg);

            // Ask again
            await dc.Replace("orderPrompt");
        }
        else if(response == "help")
        {
            // Provide help information
            await dc.Context.SendActivity("To make an order, add as many items to your cart as " +
                "you like then choose the \"Process order\" option to check out.");

            // Ask again
            await dc.Replace("orderPrompt");
        }
        else
        {
            // Unlikely to get past the prompt verification, but this will catch 
            // anything that isn't a valid menu choice
            if(!dinnerMenu.dinnerChoices.ContainsKey(response))
            {
                await dc.Context.SendActivity("Sorry, that is not a valid item. " +
                    "Please pick one from the menu.");
    
                // Ask again
                await dc.Replace("orderPrompt");
            }
            else {
                // Add the item to cart
                Orders currentOrder;

                // If there is a current order going, add to it. If not, start a new one
                try
                {
                    currentOrder = convo["order"] as Orders;
                }
                catch
                {
                    convo["order"] = new Orders();
                    currentOrder = convo["order"] as Orders;
                }

                // Add to the current order
                currentOrder.order += (dinnerMenu.dinnerChoices[$"{response}"].Description) + ", ";
                currentOrder.total += (double)dinnerMenu.dinnerChoices[$"{response}"].Price;

                // Save back to the conversation state
                convo["order"] = currentOrder;

                await dc.Context.SendActivity($"Added to cart. Current order: " +
                    $"{currentOrder.order} " +
                    $"<br/>Current total: ${currentOrder.total}");

                // Ask again to allow user to add more items or process order
                await dc.Replace("orderPrompt");
            }
        }
    }
});
```

# <a name="javascripttabjstab"></a>[JavaScript](#tab/jstab)

```javascript
// Helper dialog to repeatedly prompt user for orders
dialogs.add('orderPrompt', [
    async function(dc){
        await dc.prompt('choicePrompt', "What would you like?", dinnerMenu.choices);
    },
    async function(dc, choice){
        if(choice.value.match(/process order/ig)){
            if(orderCart.orders.length > 0) {
                // Process the order
                dc.context.activity.conversation.dinnerOrder = orderCart;

                await dc.end();
            }
            else {
                await dc.context.sendActivity("Your cart was empty. Please add at least one item to the cart.");
                // Ask again
                await dc.replace('orderPrompt');
            }
        }
        else if(choice.value.match(/cancel/ig)){
            orderCart.clear(context);
            await dc.context.sendActivity("Your order has been canceled.");
            await dc.end(choice.value);
        }
        else if(choice.value.match(/more info/ig)){
            var msg = "More info: <br/>Potato Salad: contains 330 calaries per serving. <br/>"
                + "Tuna Sandwich: contains 700 calaries per serving. <br/>" 
                + "Clam Chowder: contains 650 calaries per serving."
            await dc.context.sendActivity(msg);
            
            // Ask again
            await dc.replace('orderPrompt');
        }
        else if(choice.value.match(/help/ig)){
            var msg = `Help: <br/>To make an order, add as many items to your cart as you like then choose the "Process order" option to check out.`
            await dc.context.sendActivity(msg);
            
            // Ask again
            await dc.replace('orderPrompt');
        }
        else {
            var choice = dinnerMenu[choice.value];

            // Only proceed if user chooses an item from the menu
            if(!choice){
                await dc.context.sendActivity("Sorry, that is not a valid item. Please pick one from the menu.");
                
                // Ask again
                await dc.replace('orderPrompt');
            }
            else {
                // Add the item to cart
                orderCart.orders.push(choice);
                orderCart.total += dinnerMenu[choice.value].Price;

                await dc.context.sendActivity(`Added to cart: ${choice.value}. <br/>Current total: $${orderCart.total}`);

                // Ask again
                await dc.replace('orderPrompt');
            }
        }
    }
]);
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

Beispielsweise wenn der Benutzer mitten im Tischreservierungsfluss sagt: „Ich möchte einen Burger bestellen.“ Der Bot weiß in diesem Konversationsfluss nicht, wie er diese Aussage behandeln soll. Da es beim aktuellen Fluss nicht um Bestellungen geht und „Essen bestellen“ ein anderer Konversationsbefehl ist, weiß der Bot mit dieser Eingabe nichts anzufangen. Wenn Sie beispielsweise LUIS verwenden, können Sie das Modell trainieren, sodass es erkennt, dass der Benutzer Essen bestellen möchte (LUIS kann beispielsweise eine „orderFood“-Absicht zurückgeben). Der Bot kann somit antworten: „Anscheinend möchten Sie Essen bestellen. Möchten Sie zu unserem Essensbestellprozess wechseln?“ Weitere Informationen zum Trainieren von LUIS sowie zum Erkennen von Benutzerabsichten finden Sie unter [Verwenden von LUIS für Sprachverständnis](bot-builder-howto-v4-luis.md).

### <a name="default-response"></a>Standardantwort
Wenn alles andere nicht funktioniert, können Sie eine allgemeine Standardantwort senden, statt nichts zu tun und den Benutzer ratlos zurückzulassen. Die Standardantwort sollte dem Benutzer mitteilen, welche Befehle der Bot versteht, sodass der Benutzer wieder in den Prozess hineinfindet.

Sie können anhand des „context.**responded**“-Flags am Ende der Botlogik erkennen, ob der Bot während des Prozesses eine Antwort an den Benutzer gesendet hat. Wenn der Bot die Benutzereingabe verarbeitet, aber nicht antwortet, weiß der Bot mit der Eingabe wahrscheinlich nichts anzufangen. In diesem Fall können Sie einhaken und dem Benutzer eine Standardnachricht senden.

Dieser Bot sendet dem Benutzer standardmäßig den `mainMenu`-Dialog. Entscheiden Sie selbst, welche Oberfläche dem Benutzer in dieser Situation vom Bot angezeigt wird.

# <a name="ctabcsharptab"></a>[C#](#tab/csharptab)

```cs
if(!context.Responded)
{
    await dc.EndAll().Begin("mainMenu");
}
```

# <a name="javascripttabjstab"></a>[JavaScript](#tab/jstab)

```javascript
// Check to see if anyone replied. If not then clear all the stack and present the main menu
if (!context.responded) {
    await dc.endAll().begin('mainMenu');
}
```

---

