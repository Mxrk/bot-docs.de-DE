---
title: Ersetzen von Dialogfeldern |Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Dialogfelder ersetzen, um mithilfe des Bot Builder SDK für Node.js erneute Eingabeaufforderungen zu senden und den Konversationsfluss zu verwalten.
author: v-ducvo
ms.author: v-ducvo
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 4343e9742d6bfabeab1ccb6a96f0d379d824125e
ms.sourcegitcommit: 2dc75701b169d822c9499e393439161bc87639d2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/24/2018
ms.locfileid: "42905734"
---
# <a name="replace-dialogs"></a>Ersetzen von Dialogfeldern

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

Es kann nützlich sein, Dialogfelder zu ersetzen, wenn Sie im Laufe einer Konversation eine Benutzereingaben überprüfen oder eine Aktion wiederholen müssen. Mithilfe des Bot Builder SDK für Node.js können Sie über die [`session.replaceDialog`](http://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.session#replacedialog)-Methode ein Dialogfeld ersetzen. Mit dieser Methode können Sie das aktuelle Dialogfeld beenden und durch ein neues ersetzen, ohne dabei den Aufrufer erneut kontaktieren zu müssen. 

## <a name="create-custom-prompts-to-validate-input"></a>Erstellen von benutzerdefinierten Eingabeaufforderungen, um Eingaben zu überprüfen

Das Bot Builder SDK für Node.js umfasst die Eingabeüberprüfung für einige [Eingabeaufforderungstypen](bot-builder-nodejs-dialog-prompt.md) wie `Prompts.time` und `Prompts.choice`. Wenn Sie Texteingaben überprüfen möchten, die Sie als Antwort auf `Prompts.text` erhalten, müssen Sie Ihre eigene Validierungslogik und benutzerdefinierte Eingabeaufforderungen erstellen. 

Sie sollten eine Eingabe überprüfen, wenn diese mit einem bestimmten Wert, Muster, Bereich oder Kriterium konform sein muss. Wenn Fehler bei der Überprüfung einer Eingabe auftreten, kann der Bot über die `session.replaceDialog`-Methode den Benutzer dazu auffordern, diese Informationen erneut einzugeben.

Im folgenden Codebeispiel wird gezeigt, wie Sie eine benutzerdefinierte Eingabeaufforderung erstellen, um die Eingabe einer Telefonnummer durch den Benutzer zu überprüfen.

```javascript
// This dialog prompts the user for a phone number. 
// It will re-prompt the user if the input does not match a pattern for phone number.
bot.dialog('phonePrompt', [
    function (session, args) {
        if (args && args.reprompt) {
            builder.Prompts.text(session, "Enter the number using a format of either: '(555) 123-4567' or '555-123-4567' or '5551234567'")
        } else {
            builder.Prompts.text(session, "What's your phone number?");
        }
    },
    function (session, results) {
        var matched = results.response.match(/\d+/g);
        var number = matched ? matched.join('') : '';
        if (number.length == 10 || number.length == 11) {
            session.userData.phoneNumber = number; // Save the number.
            session.endDialogWithResult({ response: number });
        } else {
            // Repeat the dialog
            session.replaceDialog('phonePrompt', { reprompt: true });
        }
    }
]);
```

In diesem Beispiel wird der Benutzer zunächst aufgefordert, seine Telefonnummer anzugeben. Die Überprüfungslogik verwendet einen regulären Ausdruck, der mit einem bestimmten Ziffernbereich aus der Benutzereingabe übereinstimmt. Wenn die Eingabe 10 oder 11 Ziffern umfasst, wird die Zahl in der Antwort zurückgegeben. Andernfalls wird die `session.replaceDialog`-Methode ausgeführt, um das Dialogfeld `phonePrompt` erneut anzuzeigen, in dem der Benutzer aufgefordert wird, die Eingabe zu wiederholen. Dabei werden genauere Angaben zum Format der erwarteten Eingabe gemacht.

Wenn Sie die `session.replaceDialog`-Methode aufrufen, geben Sie den Namen des Dialogfelds, das erneut angezeigt werden soll, und eine Liste mit Argumenten an. In diesem Beispiel enthält die Liste `{ reprompt: true }`, wodurch der Bot verschiedene Eingabeaufforderungsnachrichten anzeigen kann, die davon abhängig sind, ob es sich um eine erste Aufforderung oder eine erneute Aufforderung handelt. Sie können dabei angeben, welche Argumente Ihr Bot anfordern darf. 

## <a name="repeat-an-action"></a>Wiederholen einer Aktion

Es kann im Laufe einer Konversation dazu kommen, dass Sie ein Dialogfeld erneut anzeigen lassen möchten, damit der Benutzer eine bestimmte Aktion mehrere Male ausführen kann. Wenn Ihr Bot beispielsweis verschiedene Dienste anbietet, können Sie zunächst das Menü mit den Diensten anzeigen, den Benutzer anleiten, wie er einen Dienst anfordern kann und anschließend das Menü mit den Diensten erneut anzeigen, damit der Benutzer einen anderen Dienst anfordern kann. Sie können die [`session.replaceDialog`](https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.session#replacedialog)-Methode verwenden, um das Menü mit den Diensten erneut anzuzeigen, anstatt die Konversation mit der Methode [session.endConversation](https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.session#endconversation) zu beenden. 

Im folgenden Beispiel sehen Sie, wie Sie die `session.replaceDialog`-Methode verwenden können, um ein derartiges Szenario zu implementieren. Zuerst wird das Menü mit den Diensten definiert:

```javascript
// Main menu
var menuItems = { 
    "Order dinner": {
        item: "orderDinner"
    },
    "Dinner reservation": {
        item: "dinnerReservation"
    },
    "Schedule shuttle": {
        item: "scheduleShuttle"
    },
    "Request wake-up call": {
        item: "wakeupCall"
    },
}
```

Das Dialogfeld `mainMenu` wird vom Standarddialogfeld aufgerufen, sodass das Menü dem Benutzer zu Beginn der Konversation angezeigt wird. Außerdem wird eine [`triggerAction`](https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.dialog.html#triggeraction) an das Dialogfeld `mainMenu` angefügt, damit das Menü immer angezeigt wird, wenn der Benutzer das „Hauptmenü“ anfordert. Wenn dem Benutzer das Menü angezeigt wird und er eine Option auswählt, wird das Dialogfeld, das der Auswahl des Benutzers entspricht, über die `session.beginDialog`-Methode aufgerufen.

```javascript
var inMemoryStorage = new builder.MemoryBotStorage();

// This is a reservation bot that has a menu of offerings.
var bot = new builder.UniversalBot(connector, [
    function(session){
        session.send("Welcome to Contoso Hotel and Resort.");
        session.beginDialog("mainMenu");
    }
]).set('storage', inMemoryStorage); // Register in-memory storage 

// Display the main menu and start a new request depending on user input.
bot.dialog("mainMenu", [
    function(session){
        builder.Prompts.choice(session, "Main Menu:", menuItems);
    },
    function(session, results){
        if(results.response){
            session.beginDialog(menuItems[results.response.entity].item);
        }
    }
])
.triggerAction({
    // The user can request this at any time.
    // Once triggered, it clears the stack and prompts the main menu again.
    matches: /^main menu$/i,
    confirmPrompt: "This will cancel your request. Are you sure?"
});
```

Wenn der Benutzer in diesem Beispiel Option 1 verwendet, um ein Abendessen zu bestellen, das zu seinem Zimmer geliefert werden soll, wird das Dialogfeld `orderDinner` aufgerufen, und der Benutzer wird beim Bestellvorgang unterstützt. Am Ende dieses Prozesses bestätigt der Bot die Bestellung und zeigt mithilfe der `session.replaceDialog`-Methode erneut das Hauptmenü an.

```javascript
// Menu: "Order dinner"
// This dialog allows user to order dinner to be delivered to their hotel room.
bot.dialog('orderDinner', [
    function(session){
        session.send("Lets order some dinner!");
        builder.Prompts.choice(session, "Dinner menu:", dinnerMenu);
    },
    function (session, results) {
        if (results.response) {
            var order = dinnerMenu[results.response.entity];
            var msg = `You ordered: %(Description)s for a total of $${order.Price}.`;
            session.dialogData.order = order;
            session.send(msg);
            builder.Prompts.text(session, "What is your room number?");
        } 
    },
    function(session, results){
        if(results.response){
            session.dialogData.room = results.response;
            var msg = `Thank you. Your order will be delivered to room #${results.response}.`;
            session.send(msg);
            session.replaceDialog("mainMenu"); // Display the menu again.
        }
    }
])
.reloadAction(
    "restartOrderDinner", "Ok. Let's start over.",
    {
        matches: /^start over$/i
    }
)
.cancelAction(
    "cancelOrder", "Type 'Main Menu' to continue.", 
    {
        matches: /^cancel$/i,
        confirmPrompt: "This will cancel your order. Are you sure?"
    }
);
```

Es werden zwei Trigger an das Dialogfeld `orderDinner` angefügt, damit der Benutzer jederzeit im Laufe des Bestellprozesses von vorne beginnen oder den Bestellvorgang abbrechen kann. 

Der erste Trigger lautet [`reloadAction`](https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.dialog.html#reloadaction) und kann vom Benutzer verwendet werden, um den Bestellvorgang von vorne zu beginnen, wenn er die Eingabe „Von vorne beginnen“ sendet. Wenn der Trigger mit der Äußerung „Von vorne beginnen“ übereinstimmt, startet `reloadAction` das Dialogfeld von vorne. 

Der zweite Trigger lautet [`cancelAction`](https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.dialog.html#cancelaction) und kann vom Benutzer verwendet werden, um den Bestellvorgang abzubrechen, wenn er die Eingabe „Abbrechen“ sendet. Wenn dieser Trigger ausgelöst wird, wird nicht automatisch wieder das Hauptmenü angezeigt. Stattdessen wird eine Nachricht an den Benutzer gesendet, in der ihm erklärt wird, wie er nun vorgehen soll („Geben Sie ‚Hauptmenü‘ zum Fortfahren ein.“).

### <a name="dialog-loops"></a>Dialogfeldschleifen

Im oben genannten Beispiel kann der Benutzer nur ein Element pro Bestellung auswählen. Das heißt, wenn der Benutzer zwei Elemente aus dem Menü auswählen möchte, muss er zunächst den Bestellprozess für das erste Element abschließen und dann für das zweite Element erneut ausführen. 

Im folgenden Beispiel wird gezeigt, wie Sie den zuvor beschriebenen Bot verbessern können, indem Sie die Speisekarte in ein separates Dialogfeld umwandeln. Dadurch zeigt der Bot die Speisekarte in einer Schleife an, wodurch der Benutzer mehrere Elemente für nur eine Bestellung auswählen kann.

Fügen Sie zuerst die Option „Bestellen“ zum Menü hinzu. Über diese Option kann der Benutzer den Auswahlprozess beenden und die Bestellung aufgeben.

```javascript
// The dinner menu
var dinnerMenu = { 
    //...other menu items...,
    "Check out": {
        Description: "Check out",
        Price: 0 // Order total. Updated as items are added to order.
    }
};
```

Wandeln Sie anschließend die Eingabeaufforderung für die Bestellung in ein eigenes Dialogfeld um, damit der Bot das Menü erneut anzeigen und der Benutzer mehrere Elemente zur Bestellung hinzufügen kann.

```javascript
// Add dinner items to the list by repeating this dialog until the user says `check out`. 
bot.dialog("addDinnerItem", [
    function(session, args){
        if(args && args.reprompt){
            session.send("What else would you like to have for dinner tonight?");
        }
        else{
            // New order
            // Using the conversationData to store the orders
            session.conversationData.orders = new Array();
            session.conversationData.orders.push({ 
                Description: "Check out",
                Price: 0
            })
        }
        builder.Prompts.choice(session, "Dinner menu:", dinnerMenu);
    },
    function(session, results){
        if(results.response){
            if(results.response.entity.match(/^check out$/i)){
                session.endDialog("Checking out...");
            }
            else {
                var order = dinnerMenu[results.response.entity];
                session.conversationData.orders[0].Price += order.Price; // Add to total.
                var msg = `You ordered: ${order.Description} for a total of $${order.Price}.`;
                session.send(msg);
                session.conversationData.orders.push(order);
                session.replaceDialog("addDinnerItem", { reprompt: true }); // Repeat dinner menu
            }
        }
    }
])
.reloadAction(
    "restartOrderDinner", "Ok. Let's start over.",
    {
        matches: /^start over$/i
    }
);
```

In diesem Beispiel werden Bestellungen in einem Botdatenspeicher gespeichert, der auf die aktuelle Konversation beschränkt ist (`session.conversationData.orders`). Für jede neue Bestellung wird die Variable erneut mit einem neuen Array initialisiert, und für jedes Element, das der Benutzer auswählt, fügt der Bot dieses Element zu dem `orders`-Array und den Preis zum Gesamtpreis hinzu, der in der `Price`-Variablen der Bestellung gespeichert ist. Wenn der Benutzer alle gewünschten Elemente für seine Bestellung ausgewählt hat, kann er auf „Bestellen“ klicken und den Bestellvorgang abschließen.

> [!NOTE]
> Weitere Informationen zum Botdatenspeicher finden Sie unter [Manage state data (Verwalten von Zustandsdaten)](bot-builder-nodejs-state.md). 

Aktualisieren Sie anschließend den zweiten Schritt des Wasserfalls im Dialogfeld `orderDinner`, damit die Bestellung verarbeitet wird. 

```javascript
// Menu: "Order dinner"
// This dialog allows user to order dinner and have it delivered to their room.
bot.dialog('orderDinner', [
    function(session){
        session.send("Lets order some dinner!");
        session.beginDialog("addDinnerItem");
    },
    function (session, results) {
        if (results.response) {
            // Display itemize order with price total.
            for(var i = 1; i < session.conversationData.orders.length; i++){
                session.send(`You ordered: ${session.conversationData.orders[i].Description} for a total of $${session.conversationData.orders[i].Price}.`);
            }
            session.send(`Your total is: $${session.conversationData.orders[0].Price}`);

            // Continue with the check out process.
            builder.Prompts.text(session, "What is your room number?");
        } 
    },
    function(session, results){
        if(results.response){
            session.dialogData.room = results.response;
            var msg = `Thank you. Your order will be delivered to room #${results.response}`;
            session.send(msg);
            session.replaceDialog("mainMenu");
        }
    }
])
//...attached triggers...
;
```

## <a name="cancel-a-dialog"></a>Abbrechen eines Dialogfelds

Die `session.replaceDialog`-Methode kann zwar verwendet werden, um das *aktuelle* Dialogfeld durch ein neues zu ersetzen, aber es kann nicht dazu verwendet, um ein Dialogfeld zu ersetzen, das sich weiter unten im Dialogfeldstapel befindet. Verwenden Sie stattdessen die [`session.cancelDialog`](http://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.session#canceldialog)-Methode, um ein Dialogfeld im Dialogfeldstapel zu ersetzen, bei dem es sich nicht um das *aktuelle* Dialogfeld handelt. 

Die `session.cancelDialog`-Methode kann verwendet werden, um ein Dialogfeld zu beenden und anschließend ggf. durch ein neues Dialogfeld zu ersetzen. Dabei spielt es keine Rolle, wo im Dialogfeldstapel sich dieses Dialogfeld befindet. Geben Sie die ID des Dialogfelds, das beendet werden soll, und ggf. die ID des Dialogfelds an, das stattdessen angezeigt werden soll, um die `session.cancelDialog`-Methode aufzurufen. Beispielsweise beendet der folgende Codeausschnitt das Dialogfeld `orderDinner` und ersetzt es durch `mainMenu`:

```javascript
session.cancelDialog('orderDinner', 'mainMenu'); 
```

Wenn die `session.cancelDialog`-Methode aufgerufen wird, wird der Dialogfeldstapel unten angefangen durchsucht, und das erste Dialogfeld, das gefunden wird, wird beendet, wodurch dieses Dialogfeld und alle untergeordneten Dialogfelder (falls vorhanden) aus dem Dialogfeldstapel entfernt werden. Dann übernimmt wieder das aufrufende Dialogfeld, das nach einem [`results.resumed`](http://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.ipromptresult.html#resumed)-Code suchen kann, der [`ResumeReason.notCompleted`](http://docs.botframework.com/en-us/node/builder/chat-reference/enums/_botbuilder_d_.resumereason.html#notcompleted) entspricht, um festzustellen, dass das andere Dialogfeld geschlossen wurde.

Wenn Sie nicht die ID des Dialogfelds angeben möchten, das beendet werden soll, wenn Sie die `session.cancelDialog`-Methode aufrufen, können Sie auch einen nullbasierten Index des betreffenden Dialogfelds angeben, wobei der Index die Position des Dialogfelds im Dialogfeldstapel angibt. Beispielswiese beendet der folgende Codeausschnitt das aktuell aktive Dialogfeld (Index = 0) und startet stattdessen das Dialogfeld `mainMenu`. Das Dialogfeld `mainMenu` wird an Position 0 des Stapels aufgerufen und wird dadurch zum neuen Standarddialogfeld.

```javascript
session.cancelDialog(0, 'mainMenu');
```

Betrachten Sie das in diesem Artikel unter [Dialogfeldschleifen](#dialog-loops) genannte Beispiel. Wenn der Benutzer zum Menü für die Elementauswahl gelangt, handelt es sich bei diesem Dialogfeld (`addDinnerItem`) um das vierte Dialogfeld im Stapel: `[default dialog, mainMenu, orderDinner, addDinnerItem]`. Jetzt möchten Sie sicher wissen, wie Sie dafür sorgen können, dass der Benutzer seine Bestellung über das Dialogfeld `addDinnerItem` abbrechen kann. Wenn Sie den `cancelAction`-Trigger an das Dialogfeld `addDinnerItem` anfügen, wird der Benutzer zurück zum vorherigen Dialogfeld (`orderDinner`) geleitet, welches den Benutzer direkt zum Dialogfeld `addDinnerItem` leitet.

In dieser Situation erweist sich die Methode `session.cancelDialog` als nützlich. Wenn Sie mit dem [Beispiel für Dialogfeldschleifen](#dialog-loops) beginnen, fügen Sie „Bestellung abbrechen“ als explizite Option zur Speisekarte hinzu.

```javascript
// The dinner menu
var dinnerMenu = { 
    //...other menu items...,
    "Check out": {
        Description: "Check out",
        Price: 0      // Order total. Updated as items are added to order.
    },
    "Cancel order": { // Cancel the order and back to Main Menu
        Description: "Cancel order",
        Price: 0
    }
};
```

Aktualisieren Sie anschließend das Dialogfeld `addDinnerItem`, um nach einer „Bestellung abbrechen“-Anforderung zu suchen. Wenn erkannt wird, dass die Option „Abbrechen“ ausgewählt wurde, verwenden Sie die `session.cancelDialog`-Methode, um das Standarddialogfeld (also das Dialogfeld mit dem Index 0 auf dem Stapel) zu beenden und stattdessen das Dialogfeld `mainMenu` aufzurufen. 

```javascript
// Add dinner items to the list by repeating this dialog until the user says `check out`. 
bot.dialog("addDinnerItem", [
    //...waterfall steps...,
    // Last step
    function(session, results){
        if(results.response){
            if(results.response.entity.match(/^check out$/i)){
                session.endDialog("Checking out...");
            }
            else if(results.response.entity.match(/^cancel/i)){
                // Cancel the order and start "mainMenu" dialog.
                session.cancelDialog(0, "mainMenu");
            }
            else {
                //...add item to list and prompt again...
                session.replaceDialog("addDinnerItem", { reprompt: true }); // Repeat dinner menu.
            }
        }
    }
])
//...attached triggers...
;
```

Wenn Sie die `session.cancelDialog`-Methode auf diese Weise verwenden, können Sie jeden belieben Konversationsfluss implementieren, den Ihr Bot anfordert.

## <a name="next-steps"></a>Nächste Schritte

Wie Sie sehen, können verschiedene **Aktionstypen** verwendet werden, um Dialogfelder auf dem Stapel zu ersetzen. Dadurch wird das Verwalten des Konversationsflusses flexibler. Wenn Sie auf den folgenden Link klicken, erhalten Sie weitere Informationen zu **Aktionen** und dazu, wie Sie besser auf Benutzeraktionen eingehen können.

> [!div class="nextstepaction"]
> [Handle user actions (Behandeln von Benutzeraktionen)](bot-builder-nodejs-dialog-actions.md)
