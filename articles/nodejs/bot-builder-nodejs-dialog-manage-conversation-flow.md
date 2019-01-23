---
title: Verwalten eines Unterhaltungsablaufs mit Dialogen | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie eine Unterhaltung mit Dialogen zwischen einem Bot und einem Benutzer im Bot Framework SDK für Node.js verwalten.
author: v-ducvo
ms.author: v-ducvo
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 96c28101c3ea72c70c6ad53b06306f4ea00b2929
ms.sourcegitcommit: b15cf37afc4f57d13ca6636d4227433809562f8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/11/2019
ms.locfileid: "54225605"
---
# <a name="manage-conversation-flow-with-dialogs"></a>Verwalten des Unterhaltungsablaufs mit Dialogen

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

> [!div class="op_single_selector"]
> - [.NET](../dotnet/bot-builder-dotnet-manage-conversation-flow.md)
> - [Node.js](../nodejs/bot-builder-nodejs-dialog-manage-conversation-flow.md)

Das Verwalten des Unterhaltungsablaufs ist eine wesentliche Aufgabe beim Erstellen von Bots. Ein Bot muss in der Lage sein, Kernaufgaben elegant auszuführen und Unterbrechungen ordnungsgemäß zu behandeln. Mit dem Bot Framework SDK für Node.js können Sie den Unterhaltungsablauf mit Dialogen verwalten.

Ein Dialog ist wie eine Funktion in einem Programm. Er dient in der Regel einem bestimmten Vorgang und kann so oft aufgerufen werden, wie er benötigt wird. Sie können mehrere Dialoge verketten, um jeden Unterhaltungsablauf zu behandeln, den Ihr Bot abwickeln soll. Das Bot Framework SDK für Node.js enthält integrierte Funktionen wie z.B. [Eingabeaufforderungen](bot-builder-nodejs-dialog-prompt.md) und [Wasserfälle](bot-builder-nodejs-dialog-waterfall.md), um Ihnen das Verwalten des Unterhaltungsablaufs zu erleichtern.

Dieser Artikel enthält eine Reihe von Beispielen, um zu erläutern, wie sich sowohl einfache als auch komplexe Unterhaltungsabläufe handhaben lassen, in denen Ihr Bot Unterbrechungen behandeln kann und den Ablauf mithilfe von Dialogen ordnungsgemäß fortsetzen kann. Die Beispiele basieren auf den folgenden Szenarien: 

1. Ihr Bot soll eine Restaurantreservierung annehmen.
2. Ihr Bot kann jederzeit während des Reservierungsvorgangs eine „Hilfe“-Anforderung verarbeiten.
3. Ihr Bot kann kontextbezogene „Hilfe“ für den aktuellen Schritt der Reservierung verarbeiten.
4. Ihr Bot kann mehrere Unterhaltungsthemen verarbeiten.

## <a name="manage-conversation-flow-with-a-waterfall"></a>Verwalten des Unterhaltungsablaufs mit Wasserfällen

Ein [Wasserfall](bot-builder-nodejs-dialog-waterfall.md) ist ein Dialog, der es dem Bot ermöglicht, einen Benutzer komfortabel durch eine Reihe von Aufgaben zu führen. In diesem Beispiel stellt der Reservierungsbot dem Benutzer eine Reihe von Fragen, damit der Bot die Reservierungsanfrage verarbeiten kann. Der Bot fordert den Benutzer zur Angabe der folgenden Informationen auf:

1. Datum und Uhrzeit der Reservierung
2. Anzahl der Personen in der Gruppe
3. Name der Person, die die Reservierung tätigt

Das folgende Codebeispiel zeigt die Verwendung eines Wasserfalls, um den Benutzer durch eine Reihe von Abfragen zu führen.

```javascript
var inMemoryStorage = new builder.MemoryBotStorage();

// This is a dinner reservation bot that uses a waterfall technique to prompt users for input.
var bot = new builder.UniversalBot(connector, [
    function (session) {
        session.send("Welcome to the dinner reservation.");
        builder.Prompts.time(session, "Please provide a reservation date and time (e.g.: June 6th at 5pm)");
    },
    function (session, results) {
        session.dialogData.reservationDate = builder.EntityRecognizer.resolveTime([results.response]);
        builder.Prompts.number(session, "How many people are in your party?");
    },
    function (session, results) {
        session.dialogData.partySize = results.response;
        builder.Prompts.text(session, "Whose name will this reservation be under?");
    },
    function (session, results) {
        session.dialogData.reservationName = results.response;
        
        // Process request and display reservation details
        session.send(`Reservation confirmed. Reservation details: <br/>Date/Time: ${session.dialogData.reservationDate} <br/>Party size: ${session.dialogData.partySize} <br/>Reservation name: ${session.dialogData.reservationName}`);
        session.endDialog();
    }
]).set('storage', inMemoryStorage); // Register in-memory storage 
```

Die Kernfunktionalität dieses Bots tritt im Standarddialog zutage. Der Standarddialog wird bei der Erstellung des Bots definiert: 

```javascript
var bot = new builder.UniversalBot(connector, [..waterfall steps..]); 
```

Ferner können Sie während dieses Erstellungsvorgangs festlegen, welchen [Datenspeicher](bot-builder-nodejs-state.md) Sie verwenden möchten. Wenn Sie beispielsweise die Speicherung im Arbeitsspeicher verwenden möchten, können Sie sie in dieser Weise festlegen:

```javascript
var inMemoryStorage = new builder.MemoryBotStorage();
var bot = new builder.UniversalBot(connector, [..waterfall steps..]).set('storage', inMemoryStorage); // Register in-memory storage 
```

Der Standarddialog wird als Abfolge von Funktionen erstellt, durch die die Schritte des Wasserfalls definiert werden. Im Beispiel gibt es vier Funktionen, daher weist der Wasserfall vier Schritte auf. Jeder Schritt führt eine einzelne Aufgabe aus, und die Ergebnisse werden im nächsten Schritt verarbeitet. Der Prozess wird bis zum letzten Schritt fortgesetzt, in dem die Reservierung bestätigt wird und der Dialog endet.

Das folgende Screenshot zeigt die Ergebnisse, die bei der Ausführung dieses Bots im [Bot Framework Emulator](../bot-service-debug-emulator.md) entstanden sind:

![Verwalten des Unterhaltungsablaufs mit Wasserfällen](../media/bot-builder-nodejs-dialog-manage-conversation/waterfall-results.png)

### <a name="prompt-user-for-input"></a>Benutzer zu Eingabe auffordern

Jeder Schritt in diesem Beispiel verwendet eine Anfrage, um den Benutzer um eine Eingabe zu bitten. Eine Anfrage ist eine besondere Art von Dialog, die um eine Benutzereingabe bittet, auf eine Antwort wartet und die Antwort an den nächsten Schritt im Wasserfall weitergibt. Informationen über die vielen verschiedenen Arten von Anfragen, die Sie in Ihrem Bot verwenden können, finden Sie unter [Auffordern von Benutzern zur Eingabe](bot-builder-nodejs-dialog-prompt.md).

In diesem Beispiel verwendet der Bot `Prompts.text()`, um eine formlose Antwort des Benutzers in Textform zu erbitten. Der Benutzer kann mit einem beliebigen Text antworten, und der Bot muss entscheiden, wie die Antwort behandelt werden soll. `Prompts.time()` verwendet die [Chrono](https://github.com/wanasit/chrono)-Bibliothek, um die Datums- und Uhrzeitinformationen in einer Zeichenfolge zu analysieren. Dies ermöglicht Ihrem Bot das Verständnis von eher natürlicher Sprache bei den Angaben für Datum und Uhrzeit. Beispiel:  „6. Juni 2017 um 9 Uhr“, „Heute um 7:30 Uhr“, „nächsten Montag um 6“ usw.

> [!TIP] 
> Die vom Benutzer eingegebene Uhrzeit wird basierend auf der Zeitzone des Servers, der den Bot hostet, in UTC-Zeit konvertiert. Da sich der Server möglicherweise in einer anderen Zeitzone als der Benutzer befindet, achten Sie darauf, die Zeitzonen zu berücksichtigen. Damit Datum und Uhrzeit in die Ortszeit des Benutzers konvertiert werden können, sollten Sie den Benutzer fragen, in welcher Zeitzone er sich befindet.

## <a name="manage-a-conversation-flow-with-multiple-dialogs"></a>Verwalten eines Unterhaltungsablaufs mit Dialogen | Microsoft-Dokumentation

Ein weiteres Verfahren zum Verwalten des Unterhaltungsablaufs ist die Verwendung einer Kombination aus Wasserfall und mehreren Dialogen. Mithilfe des Wasserfalls können Sie Funktionen gemeinsam in einem Dialog verketten, während Dialoge Ihnen die Möglichkeit geben, eine Unterhaltung in kleinere Funktionseinheiten aufzubrechen, die jederzeit wiederverwendet werden können.

Betrachten Sie beispielsweise den Bot zur Restaurantreservierung. Im folgenden Codebeispiel finden Sie das vorherige Beispiel so umgeschrieben, dass Wasserfall und mehrere Dialoge verwendet werden.

```javascript
var inMemoryStorage = new builder.MemoryBotStorage();

// This is a dinner reservation bot that uses multiple dialogs to prompt users for input.
var bot = new builder.UniversalBot(connector, [
    function (session) {
        session.send("Welcome to the dinner reservation.");
        session.beginDialog('askForDateTime');
    },
    function (session, results) {
        session.dialogData.reservationDate = builder.EntityRecognizer.resolveTime([results.response]);
        session.beginDialog('askForPartySize');
    },
    function (session, results) {
        session.dialogData.partySize = results.response;
        session.beginDialog('askForReserverName');
    },
    function (session, results) {
        session.dialogData.reservationName = results.response;

        // Process request and display reservation details
        session.send(`Reservation confirmed. Reservation details: <br/>Date/Time: ${session.dialogData.reservationDate} <br/>Party size: ${session.dialogData.partySize} <br/>Reservation name: ${session.dialogData.reservationName}`);
        session.endDialog();
    }
]).set('storage', inMemoryStorage); // Register in-memory storage 

// Dialog to ask for a date and time
bot.dialog('askForDateTime', [
    function (session) {
        builder.Prompts.time(session, "Please provide a reservation date and time (e.g.: June 6th at 5pm)");
    },
    function (session, results) {
        session.endDialogWithResult(results);
    }
]);

// Dialog to ask for number of people in the party
bot.dialog('askForPartySize', [
    function (session) {
        builder.Prompts.text(session, "How many people are in your party?");
    },
    function (session, results) {
        session.endDialogWithResult(results);
    }
])

// Dialog to ask for the reservation name.
bot.dialog('askForReserverName', [
    function (session) {
        builder.Prompts.text(session, "Who's name will this reservation be under?");
    },
    function (session, results) {
        session.endDialogWithResult(results);
    }
]);
```

Die Ergebnisse der Ausführung dieses Bots sind exakt die gleichen wie im Beispiel zuvor, in dem nur Wasserfall verwendet wird. Programmtechnisch gibt es jedoch zwei Hauptunterschiede:

1. Der Standarddialog dient ausschließlich der Verwaltung des Unterhaltungsablaufs.
2. Die Aufgabe für jeden Schritt der Unterhaltung wird von einem separaten Dialog verwaltet. In diesem Fall benötigte der Bot drei Informationselemente, also fragt er drei Mal beim Benutzer an. Jede Anfrage ist jetzt in einem eigenen Dialog enthalten.

Mit dieser Technik können Sie den Unterhaltungsablauf von der Aufgabenlogik trennen. Dadurch können die Dialoge bei Bedarf in anderen Unterhaltungsabläufen wiederverwendet werden. 

## <a name="respond-to-user-input"></a>Reaktion auf Benutzereingaben

Wenn der Benutzer, während er durch eine Reihe von Aufgaben geführt wird, Fragen hat oder weitere Informationen wünscht, bevor er eine Frage beantwortet – wie würden Sie solche Anfragen behandeln? Wie sollte der Bot beispielsweise reagieren, wenn der Benutzer – unabhängig von der Position in der Unterhaltung – „Hilfe“, „Support“ oder „Abbrechen“ eingibt? Was geschieht, wenn der Benutzer weitere Informationen zu einem Schritt wünscht? Was geschieht, wenn der Benutzer seine Meinung ändert und die aktuelle Aufgabe abbrechen möchte, um mit einer ganz anderen Aufgabe anzufangen?

Das Bot Framework SDK für Node.js ermöglicht es einem Bot, auf bestimmte Eingaben innerhalb eines globalen Kontext oder innerhalb eines lokalen Kontext im Bereich des aktuellen Dialogs zu lauschen. Diese Eingaben heißen [Aktionen](bot-builder-nodejs-dialog-actions.md), die es dem Bot ermöglichen, auf der Grundlage einer `matches`-Klausel nach Benutzereingaben zu lauschen. Die Entscheidung, wie auf bestimmte Benutzereingaben reagiert wird, liegt beim Bot.

### <a name="handle-global-action"></a>Behandeln von globalen Aktionen

Wenn Sie möchten, dass Ihr Bot in der Lage ist, an jedem beliebigen Punkt in einer Unterhaltung Aktionen zu behandeln, verwenden Sie `triggerAction`. Trigger ermöglichen es dem Bot, einen spezifischen Dialog aufzurufen, wenn die Eingabe mit dem angegebenen Ausdruck übereinstimmt. Wenn Sie beispielsweise eine globale „Hilfe“-Option unterstützen möchten, können Sie einen Hilfedialog erstellen und ihm ein `triggerAction` anfügen, das nach Eingaben lauscht, die mit „Hilfe“ übereinstimmen.

Das folgende Codebeispiel zeigt, wie Sie ein `triggerAction` an einen Dialog anfügen, um anzugeben, dass der Dialog aufgerufen werden soll, wenn der Benutzer „Hilfe“ eingibt.

```javascript
// The dialog stack is cleared and this dialog is invoked when the user enters 'help'.
bot.dialog('help', function (session, args, next) {
    session.endDialog("This is a bot that can help you make a dinner reservation. <br/>Please say 'next' to continue");
})
.triggerAction({
    matches: /^help$/i,
});
```

Standardmäßig wird bei der Ausführung von `triggerAction` der Dialogstapel gelöscht, und der ausgelöste Dialog wird zum neuen Standarddialog. In diesem Beispiel wird bei Ausführung von `triggerAction` der Dialogstapel gelöscht, und anschließend wird der `help`-Dialog dem Stapel als neuer Standarddialog hinzugefügt. Wenn dies nicht das gewünschte Verhalten ist, können Sie `triggerAction` die Option `onSelectAction` hinzufügen. Die Option `onSelectAction` ermöglicht es dem Bot, einen neuen Dialog zu starten, ohne den Dialogstapel zu löschen. Dadurch kann die Unterhaltung vorübergehend umgeleitet und später dort wieder fortgesetzt werden, wo sie unterbrochen worden war.

Das folgende Codebeispiel zeigt die Verwendung der Option `onSelectAction` mit `triggerAction`, so dass der `help`-Dialog dem vorhandenen Dialogstapel hinzugefügt (und der Dialogstapel nicht gelöscht) wird.

```javascript
bot.dialog('help', function (session, args, next) {
    session.endDialog("This is a bot that can help you make a dinner reservation. <br/>Please say 'next' to continue");
})
.triggerAction({
    matches: /^help$/i,
    onSelectAction: (session, args, next) => {
        // Add the help dialog to the dialog stack 
        // (override the default behavior of replacing the stack)
        session.beginDialog(args.action, args);
    }
});
```

In diesem Beispiel übernimmt der `help`-Dialog die Steuerung der Unterhaltung, wenn der Benutzer „Hilfe“ eingibt. Da `triggerAction` die Option `onSelectAction` enthält, wird der `help`-Dialog auf den vorhandenen Dialogstapel gepusht, und der Stapel wird nicht gelöscht. Wenn der `help`-Dialog endet, wird er aus dem Dialogstapel entfernt, und die Unterhaltung wird an dem Punkt fortgesetzt, an dem sie durch den Befehl `help` unterbrochen worden war.

### <a name="handle-contextual-action"></a>Behandeln von kontextbezogenen Aktionen

In vorstehenden Beispiel wird der `help`-Dialog aufgerufen, wenn der Benutzer an einer beliebigen Stelle der Unterhaltung „Hilfe“ eingibt. An sich kann der Dialog nur allgemeine Hilfestellung bieten, da es keinen spezifischen Kontext für die Hilfeanfrage des Benutzers gibt. Was aber, wenn der Benutzer Hilfe zu einem bestimmten Punkt in der Unterhaltung anfordern möchte? In diesem Fall muss der `help`-Dialog innerhalb des Kontexts des aktuellen Dialogs ausgelöst werden.

Betrachten Sie beispielsweise den Bot zur Restaurantreservierung. Was, wenn ein Benutzer bei der Frage nach der Anzahl der zu erwartenden Gäste nach der größten möglichen Zahl der Gäste fragt? Um dieses Szenario zu behandeln, können Sie ein `beginDialogAction` an den Dialog `askForPartySize` anfügen, das nach der Benutzereingabe „Hilfe“ lauscht.

Das folgende Codebeispiel zeigt, wie kontextsensitive Hilfe mithilfe von `beginDialogAction` an einen Dialog angefügt werden kann.

```javascript
// Dialog to ask for number of people in the party
bot.dialog('askForPartySize', [
    function (session) {
        builder.Prompts.text(session, "How many people are in your party?");
    },
    function (session, results) {
       session.endDialogWithResult(results);
    }
])
.beginDialogAction('partySizeHelpAction', 'partySizeHelp', { matches: /^help$/i });

// Context Help dialog for party size
bot.dialog('partySizeHelp', function(session, args, next) {
    var msg = "Party size help: Our restaurant can support party sizes up to 150 members.";
    session.endDialog(msg);
})
```

Wenn der Benutzer in diesem Beispiel „Hilfe“ eingibt, pusht der Bot den Dialog `partySizeHelp` auf den Stapel. Dieser Dialog sendet eine Hilfenachricht an den Benutzer und beendet dann den Dialog. Die Steuerung wird wieder an den `askForPartySize`-Dialog zurückgegeben, der den Benutzer erneut nach der Größe der zu erwartenden Gruppe fragt.

Es ist wichtig, zu beachten, dass diese kontextbezogene Hilfe nur ausgeführt wird, wenn sich der Benutzer im `askForPartySize`-Dialog befindet. Andernfalls wird stattdessen die allgemeine Hilfenachricht aus `triggerAction` ausgeführt. Das heißt, die lokale `matches`-Klausel hat immer Vorfang vor der globalen `matches`-Klausel. Wenn beispielsweise ein `beginDialogAction` eine Übereinstimmung für **Hilfe** aufweist, bewirkt die Übereinstimmung für **Hilfe** in `triggerAction` keine Aktion. Weitere Informationen finden Sie unter [Vorrang von Aktionen](bot-builder-nodejs-dialog-actions.md#action-precedence).

### <a name="change-the-topic-of-conversation"></a>Ändern des Themas einer Unterhaltung

Standardmäßig wird durch Ausführen von `triggerAction` der Dialogstapel gelöscht und die Unterhaltung zurückgesetzt, die dann mit dem angegebenen Dialog beginnt. Dieses Verhalten ist oft vorzuziehen, wenn Ihr Bot von einem Unterhaltungsthema zu einem anderen wechseln muss, etwa wenn ein Benutzer mitten in einer Restaurantreservierung seine Meinung ändert und sich stattdessen entscheidet, das Essen in sein Hotelzimmer liefern zu lassen. 

Das folgende Beispiel baut insofern auf dem vorherigen auf, als der Bot es dem Benutzer ermöglicht, entweder eine Restaurantreservierung zu platzieren oder das Essen liefern zu lassen. In diesem Bot ist der Standarddialog ein Begrüßungsdialog, der dem Benutzer zwei Optionen bietet: `Dinner Reservation` und `Order Dinner`.

```javascript
var inMemoryStorage = new builder.MemoryBotStorage();

// This bot enables users to either make a dinner reservation or order dinner.
var bot = new builder.UniversalBot(connector, function(session){
    var msg = "Welcome to the reservation bot. Please say `Dinner Reservation` or `Order Dinner`";
    session.send(msg);
}).set('storage', inMemoryStorage); // Register in-memory storage 
```

Die Logik zur Restaurantreservierung, die sich im vorherigen Beispiel im Standarddialog befand, ist jetzt in einem eigenen Dialog mit dem Namen `dinnerReservation` untergebracht. Der Ablauf der `dinnerReservation` bleibt die gleiche wie in der Version mit mehreren Dialogen, die weiter oben erörtert wurde. Der einzige Unterschied besteht darin, dass dem Dialog ein `triggerAction` angefügt ist. Beachten Sie, dass in dieser Version der `confirmPrompt` den Benutzer um eine Bestätigung bittet, dass er das Thema der Unterhaltung wechseln möchte, bevor er den neuen Dialog aufruft. Es hat sich bewährt, in Szenarien wie diesem ein `confirmPrompt` einzuschließen, da der Benutzer im Anschluss an das Löschen des Dialogstapels zum neuen Unterhaltungsthema geleitet wird, wodurch das Thema der Unterhaltung, die im Gang war, aufgegeben wird.

```javascript
// This dialog helps the user make a dinner reservation.
bot.dialog('dinnerReservation', [
    function (session) {
        session.send("Welcome to the dinner reservation.");
        session.beginDialog('askForDateTime');
    },
    function (session, results) {
        session.dialogData.reservationDate = builder.EntityRecognizer.resolveTime([results.response]);
        session.beginDialog('askForPartySize');
    },
    function (session, results) {
        session.dialogData.partySize = results.response;
        session.beginDialog('askForReserverName');
    },
    function (session, results) {
        session.dialogData.reservationName = results.response;

        // Process request and display reservation details
        session.send(`Reservation confirmed. Reservation details: <br/>Date/Time: ${session.dialogData.reservationDate} <br/>Party size: ${session.dialogData.partySize} <br/>Reservation name: ${session.dialogData.reservationName}`);
        session.endDialog();
    }
])
.triggerAction({
    matches: /^dinner reservation$/i,
    confirmPrompt: "This will cancel your current request. Are you sure?"
});
```

Das zweite Unterhaltungsthema ist in `orderDinner` mithilfe eines Wasserfalls definiert. Dieser Dialog zeigt einfach die Speisekarte an und fordert den Benutzer zur Eingabe einer Zimmernummer auf, nachdem er seine Bestellung aufgegeben hat. Ein `triggerAction` wird an den Dialog angefügt, um anzugeben, dass er aufgerufen werden soll, wenn der Benutzer „Essen bestellen“ eingibt, und um sicherzustellen, dass der Benutzer zur Bestätigung seiner Auswahl aufgefordert wird, sollte er den Wunsch zum Wechsel des Unterhaltungsthemas äußern.

```javascript
// This dialog help the user order dinner to be delivered to their hotel room.
var dinnerMenu = {
    "Potato Salad - $5.99": {
        Description: "Potato Salad",
        Price: 5.99
    },
    "Tuna Sandwich - $6.89": {
        Description: "Tuna Sandwich",
        Price: 6.89
    },
    "Clam Chowder - $4.50":{
        Description: "Clam Chowder",
        Price: 4.50
    }
};

bot.dialog('orderDinner', [
    function(session){
        session.send("Lets order some dinner!");
        builder.Prompts.choice(session, "Dinner menu:", dinnerMenu);
    },
    function (session, results) {
        if (results.response) {
            var order = dinnerMenu[results.response.entity];
            var msg = `You ordered: ${order.Description} for a total of $${order.Price}.`;
            session.dialogData.order = order;
            session.send(msg);
            builder.Prompts.text(session, "What is your room number?");
        } 
    },
    function(session, results){
        if(results.response){
            session.dialogData.room = results.response;
            var msg = `Thank you. Your order will be delivered to room #${session.dialogData.room}`;
            session.endDialog(msg);
        }
    }
])
.triggerAction({
    matches: /^order dinner$/i,
    confirmPrompt: "This will cancel your order. Are you sure?"
});
```

Nachdem der Benutzer eine Unterhaltung begonnen und entweder `Dinner Reservation` oder `Order Dinner` ausgewählt hat, kann er seine Meinung jederzeit ändern. Wenn sich der Benutzer beispielsweise mitten in der Restaurantreservierung befindet und dann „Essen bestellen“ eingibt, fordert der Bot eine Bestätigung an, indem er sagt „Dadurch wird Ihre aktuelle Anfrage storniert. Möchten Sie das?“ Wenn der Benutzer „Nein“ eingibt, wird die Anfrage storniert, und der Benutzer kann mit dem Vorgang der Restaurantreservierung fortfahren. Wenn der Benutzer „Ja“ eingibt, löscht der Bot den Dialogstapel und übergibt die Steuerung der Unterhaltung an den `orderDinner`-Dialog.

## <a name="end-conversation"></a>Beenden der Unterhaltung

In den Beispielen oben werden Dialoge entweder durch `session.endDialog` oder `session.endDialogWithResult` beendet. Beide beenden den Dialog, entfernen ihn vom Stapel und geben die Steuerung an den aufrufenden Dialog zurück. In Situationen, in denen der Benutzer das Ende der Unterhaltung erreicht hat, sollten Sie `session.endConversation` verwenden, um anzugeben, dass die Unterhaltung beendet ist.

Die Methode [`session.endConversation`](https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.session#endconversation) beendet eine Unterhaltung und sendet optional eine Nachricht an den Benutzer. Beispielsweise könnte der Dialog `orderDinner` im vorherigen Beispiel die Unterhaltung mithilfe von `session.endConversation` beenden, wie im folgenden Codebeispiel gezeigt.

```javascript
bot.dialog('orderDinner', [
    //...waterfall steps...
    // Last step
    function(session, results){
        if(results.response){
            session.dialogData.room = results.response;
            var msg = `Thank you. Your order will be delivered to room #${session.dialogData.room}`;
            session.endConversation(msg);
        }
    }
]);
```

Durch Aufrufen von `session.endConversation` wird die Unterhaltung beendet, indem der Dialogstapel gelöscht und der [`session.conversationData`](https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.session#conversationdata)-Speicher zurückgesetzt wird. Weitere Informationen zum Datenspeicher finden Sie unter [Manage state data (Verwalten von Zustandsdaten)](bot-builder-nodejs-state.md).

Das Aufrufen von `session.endConversation` ist ein logischer Schritt, wenn der Benutzer den Unterhaltungsablauf, für den der Bot entworfen wurde, abgeschlossen hat. Alternativ können Sie `session.endConversation` zum Beenden der Unterhaltung auch in Situationen verwenden, in denen der Benutzer mitten in einer Unterhaltung „Abbrechen“ oder „Auf Wiedersehen“ eingibt. Fügen Sie dazu einfach ein `endConversationAction` an den Dialog an, und lassen Sie diesen Trigger auf Eingaben lauschen, die mit „Abbrechen“ oder „Auf Wiedersehen“ übereinstimmen.

Das folgende Codebeispiel zeigt, wie ein `endConversationAction` an einen Dialog angefügt wird, um die Unterhaltung zu beenden, wenn der Benutzer „Abbrechen“ oder „Auf Wiedersehen“ eingibt.

```javascript
bot.dialog('dinnerOrder', [
    //...waterfall steps...
])
.endConversationAction(
    "endOrderDinner", "Ok. Goodbye.",
    {
        matches: /^cancel$|^goodbye$/i,
        confirmPrompt: "This will cancel your order. Are you sure?"
    }
);
```

Da das Beenden einer Unterhaltung mit `session.endConversation` oder `endConversationAction` ein Löschen des Dialogstapels bewirkt und den Benutzer zwingt, von vorn zu beginnen, sollten Sie ebenfalls ein `confirmPrompt` einschließen, um sicherzustellen, dass das Verhalten wirklich den Absichten des Benutzers entspricht.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie Verfahren zum Verwalten von Unterhaltungen kennengelernt, die ihrer Natur nach sequenziell sind. Aber wie gehen Sie vor, wenn Sie einen Dialog wiederholen oder Schleifenmuster in Ihrer Unterhaltung verwenden möchten? Sehen wir uns an, wie Sie dazu vorgehen können, indem wir die Dialoge auf dem Stapel ersetzen.

> [!div class="nextstepaction"]
> [Ersetzen von Dialogen](bot-builder-nodejs-dialog-replace.md)

