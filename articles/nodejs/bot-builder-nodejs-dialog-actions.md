---
title: Bearbeiten von Benutzeraktionen | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie mit Benutzeraktionen umgehen, indem Sie Ihrem Bot ermöglichen, mit dem Bot Builder SDK für Node.js auf Benutzereingaben mit bestimmten Schlüsselwörtern zu achten und diese zu bearbeiten.
author: DucVo
ms.author: v-ducvo
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 26f6e9520fe5d2ebb83ceb4e6a497a35e9d2611f
ms.sourcegitcommit: b78fe3d8dd604c4f7233740658a229e85b8535dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2018
ms.locfileid: "49999257"
---
# <a name="handle-user-actions"></a>Bearbeiten von Benutzeraktionen

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

> [!div class="op_single_selector"]
> - [.NET](../dotnet/bot-builder-dotnet-global-handlers.md)
> - [Node.js](../nodejs/bot-builder-nodejs-dialog-actions.md)

Benutzer möchten in der Regel mithilfe von Schlüsselwörtern wie „Help“ (Hilfe), „Cancel“ (Abbrechen) oder „Start over“ (Von vorn anfangen) auf bestimmte Funktionen innerhalb des Bots zugreifen. Das geschieht häufig mitten im Gespräch, wenn der Bot eigentlich eine andere Antwort erwartet. Durch die Implementierung von **Aktionen** können Sie Ihren Bot so gestalten, dass der derartige Anforderungen höflicher bearbeitet. Der Handler untersucht die Eingabe des Benutzers hinsichtlich der von Ihnen angegebenen Schlüsselwörter wie „Help“ (Hilfe), „Cancel“ (Abbrechen) oder „Start over“ (Von vorn anfangen) und antwortet entsprechend. 

![So sprechen Benutzer](../media/designing-bots/capabilities/trigger-actions.png)

## <a name="action-types"></a>Aktionstypen

Die Aktionstypen, die an einen Dialog angefügt werden können, werden in der folgenden Tabelle aufgeführt. Über den Link für jeden Aktionsnamen gelangen Sie in einen Abschnitt mit weiteren Informationen über diese Aktion.

| Aktion | Bereich | BESCHREIBUNG |
|------|------| ---- |
| [triggerAction](#bind-a-triggeraction) | Global | Verknüpft eine Aktion mit dem Dialog, die den Dialogstapel löscht und sich an die untere Position des Stapels setzt. Verwenden Sie die `onSelectAction`-Option, um dieses Standardverhalten außer Kraft zu setzen. |
| [customAction](#bind-a-customaction) | Global | Verknüpft eine benutzerdefinierte Aktion mit dem Bot, die Informationen verarbeiten oder Aktionen durchführen kann, ohne den Dialogstapel zu beeinflussen. Verwenden Sie die `onSelectAction`-Option, um die Funktionalität der Aktion anzupassen. |
[beginDialogAction](#bind-a-begindialogaction) | Kontextbezogen | Verknüpft eine Aktion mit dem Dialog, wodurch beim Auslösen ein anderer Dialog gestartet wird. Der Startdialog wird in den Stapel übertragen und nach dessen Ende wieder ausgeblendet. |
[reloadAction](#bind-a-reloadaction) | Kontextbezogen | Verknüpft eine Aktion mit dem Dialog, wodurch beim Auslösen der Dialog neu geladen wird. Sie können `reloadAction` verwenden, um Benutzeräußerungen wie „Start over“ (Von vorn anfangen) zu bearbeiten. |
[cancelAction](#bind-a-cancelaction) | Kontextbezogen | Verknüpft eine Aktion mit dem Dialog, wodurch beim Auslösen der Dialog abgebrochen wird. Sie können `cancelAction` verwenden, um Benutzeräußerungen wie „Cancel“ (Abbrechen) oder „Nevermind“ (Schon gut) zu bearbeiten. |
[endConversationAction](#bind-an-endconversationaction) | Kontextbezogen | Verknüpft eine Aktion mit dem Dialog, wodurch die Konversation mit dem Benutzer beim Auslösen beendet wird. Sie können `endConversationAction` verwenden, um Benutzeräußerungen wie „Goodbye“ (Auf wiedersehen) zu bearbeiten. |

## <a name="action-precedence"></a>Rangfolge der Aktionen 

Wenn ein Bot eine Äußerung von einem Benutzer erhält, gleicht er die Äußerung mit allen registrierten Aktionen im Dialogstapel ab. Der Abgleich verläuft von oben nach unten durch den Stapel. Werden keine Übereinstimmungen gefunden, wird die Äußerung mit der `matches`-Option aller globalen Aktionen abgeglichen.

Die Rangfolge der Aktionen ist wichtig, wenn Sie denselben Befehl in unterschiedlichen Kontexten verwenden. Sie können z.B. den Befehl „Help“ (Hilfe) für den Bot als eine allgemeine Hilfe verwenden. Sie können „Help“ (Hilfe) auch für jede der Aufgaben verwenden, aber diese Hilfebefehle sind dann bei jeder Aufgabe kontextbezogen. Ein funktionierendes Beispiel, das auf diesen Punkt eingeht, finden Sie unter [Antworten auf Benutzereingaben](bot-builder-nodejs-dialog-manage-conversation-flow.md#respond-to-user-input).

## <a name="bind-actions-to-dialog"></a>Verknüpfen von Aktionen an einen Dialog

Benutzeraussagen oder Klicks auf Schaltflächen können eine Aktion *auslösen*, die mit einem [Dialog](https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.dialog.html) verknüpft ist.
Wenn *Übereinstimmungen* angegeben sind, wird die Aktion darauf warten, dass der Benutzer ein Wort oder eine Phrase sagt, die die Aktion auslöst.  Die `matches`-Option kann ein regulärer Ausdruck oder der Name einer [Erkennung][RecognizeIntent] sein.
Um die Aktion mit dem Klicken einer Schaltfläche zu verknüpfen, verwenden Sie [CardAction.dialogAction()][CardAction], um die Aktion auszulösen.

Aktionen sind *verkettbare*, sodass Sie beliebig viele Aktionen mit einem Dialog verknüpfen können.

### <a name="bind-a-triggeraction"></a>Verknüpfen einer triggerAction

So verknüpfen Sie eine [triggerAction][triggerAction] mit einem Dialog:

```javascript
// Order dinner.
bot.dialog('orderDinner', [
    //...waterfall steps...
])
// Once triggered, will clear the dialog stack and pushes
// the 'orderDinner' dialog onto the bottom of stack.
.triggerAction({
    matches: /^order dinner$/i
});
```

Beim Verknüpfen einer `triggerAction` mit einem Dialog, wird diese beim Bot registriert. Nach dem Auslösen wird der Dialog durch die `triggerAction` gelöscht und an den Stapel übertragen. Diese Aktion wird am besten verwendet, um zwischen [Themen der Konversation](bot-builder-nodejs-dialog-manage-conversation-flow.md#change-the-topic-of-conversation) zu wechseln oder um es Benutzern zu ermöglichen, beliebige eigenständige Aufgaben anzufordern. Wenn Sie das Verhalten außer Kraft setzen, bei dem diese Aktion den Dialogstapel löscht, fügen Sie eine `onSelectAction`-Aktion zur `triggerAction` hinzu.

Der folgende Codeausschnitt zeigt, wie Sie allgemeine Hilfe aus einem globalen Kontext bereitstellen können, ohne den Dialogstapel zu löschen.

```javascript
bot.dialog('help', function (session, args, next) {
    //Send a help message
    session.endDialog("Global help menu.");
})
// Once triggered, will start a new dialog as specified by
// the 'onSelectAction' option.
.triggerAction({
    matches: /^help$/i,
    onSelectAction: (session, args, next) => {
        // Add the help dialog to the top of the dialog stack 
        // (override the default behavior of replacing the stack)
        session.beginDialog(args.action, args);
    }
});
```

In diesem Fall wird die `triggerAction` an den `help`-Dialog selbst angefügt (im Gegensatz zum `orderDinner`-Dialog). Mit der `onSelectAction`-Aktion können Sie diesen Dialog starten, ohne den Dialogstapel zu löschen. Dadurch können Sie globale Anforderungen bearbeiten, wie „Help“ (Hilfe), „About“ (Info), „Support“ usw. Beachten Sie, dass die `onSelectAction`-Option zum Starten des ausgelösten Dialogs explizit die `session.beginDialog`-Methode aufruft. Die ID des ausgelösten Dialogs wird über `args.action` bereitgestellt. Kodieren Sie die Dialog-ID (z.B. „Help“ (Hilfe)) nicht manuell in dieser Methode. Ansonsten werden möglicherweise Laufzeitfehler ausgegeben. Wenn Sie eine kontextbezogene Hilfenachricht für die `orderDinner`-Aufgabe selbst auslösen möchten, können Sie stattdessen ggf. eine `beginDialogAction` an den `orderDinner`-Dialog anfügen.

### <a name="bind-a-customaction"></a>Verknüpfen einer customAction

Im Gegensatz zu anderen Aktionstypen ist für `customAction` keine Standardaktion definiert. Sie können die Standardaktion selbst definieren. Der Vorteil der Verwendung von `customAction` ist, dass Sie die Möglichkeit haben, Benutzeranfragen zu bearbeiten, ohne den Dialogstapel zu bearbeiten. Wenn eine `customAction` ausgelöst wird, kann die `onSelectAction`-Option die Anforderung bearbeiten, ohne neue Dialoge auf den Stapel zu übertragen. Sobald die Aktion abgeschlossen ist, wird die Kontrolle an den Dialog zurückgegeben, der sich oben auf dem Stapel befindet, und der Bot kann fortfahren.

Sie können eine `customAction` verwenden, um allgemeine und schnelle Aktionsanforderungen bereitzustellen, wie z.B. „What is the temperature outside right now?“ (Wie hoch ist die aktuelle Außentemperatur?), „What time is it right now in Paris?“ (Wie spät ist es gerade in Paris?), „Remind me to buy milk at 5pm today.“ (Erinnere mich daran, heute um 17 Uhr Milch zu kaufen.) usw. Dies sind allgemeine Aktionen, die der Bot ausführen kann, ohne den Stack zu bearbeiten.

Ein weiterer wichtiger Unterschied zu `customAction` ist, dass er sich an den Bot bindet und nicht an einen Dialog.

Im folgenden Codebeispiel wird veranschaulicht, wie Sie eine `customAction` mit dem `bot` verknüpfen, der auf Anforderungen lauscht, um eine Erinnerung festzulegen.

```javascript
bot.customAction({
    matches: /remind|reminder/gi,
    onSelectAction: (session, args, next) => {
        // Set reminder...
        session.send("Reminder is set.");
    }
})
```

### <a name="bind-a-begindialogaction"></a>Verknüpfen einer beginDialogAction

Beim Verknüpfen einer `beginDialogAction` mit einem Dialog wird die Aktion im Dialog registriert. Diese Methode startet nach dem Auslösen einen weiteren Dialog. Das Verhalten dieser Aktion ähnelt dem Aufrufen der [beginDialog](https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.session.html#begindialog)-Methode. Der neue Dialog wird an die oberste Stelle des Dialogstapels übertragen, sodass die aktuelle Aufgabe nicht automatisch beendet wird. Die aktuelle Aufgabe wird fortgesetzt, sobald der neue Dialog beendet ist. 

Der folgende Codeausschnitt zeigt, wie Sie eine [beginDialogAction][beginDialogAction] mit einem Dialog verknüpfen.

```javascript
// Order dinner.
bot.dialog('orderDinner', [
    //...waterfall steps...
])
// Once triggered, will start the 'showDinnerCart' dialog.
// Then, the waterfall will resumed from the step that was interrupted.
.beginDialogAction('showCartAction', 'showDinnerCart', {
    matches: /^show cart$/i
});

// Show dinner items in cart
bot.dialog('showDinnerCart', function(session){
    for(var i = 1; i < session.conversationData.orders.length; i++){
        session.send(`You ordered: ${session.conversationData.orders[i].Description} for a total of $${session.conversationData.orders[i].Price}.`);
    }

    // End this dialog
    session.endDialog(`Your total is: $${session.conversationData.orders[0].Price}`);
});
```

Wenn Sie zusätzliche Argumente an den neuen Dialog übergeben müssen, können Sie eine [`dialogArgs`](https://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.idialogactionoptions#dialogargs)-Option zur Aktion hinzufügen.

Mit dem obigen Beispiel können Sie es so modifizieren, dass es Argumente akzeptiert, die über `dialogArgs` übergeben werden.

```javascript
// Order dinner.
bot.dialog('orderDinner', [
    //...waterfall steps...
])
// Once triggered, will start the 'showDinnerCart' dialog.
// Then, the waterfall will resumed from the step that was interrupted.
.beginDialogAction('showCartAction', 'showDinnerCart', {
    matches: /^show cart$/i,
    dialogArgs: {
        showTotal: true;
    }
});

// Show dinner items in cart with the option to show total or not.
bot.dialog('showDinnerCart', function(session, args){
    for(var i = 1; i < session.conversationData.orders.length; i++){
        session.send(`You ordered: ${session.conversationData.orders[i].Description} for a total of $${session.conversationData.orders[i].Price}.`);
    }

    if(args && args.showTotal){
        // End this dialog with total.
        session.endDialog(`Your total is: $${session.conversationData.orders[0].Price}`);
    }
    else{
        session.endDialog(); // Ends without a message.
    }
});
```

### <a name="bind-a-reloadaction"></a>Verknüpfen einer reloadAction

Beim Verknüpfen einer `reloadAction` mit einem Dialog wird die Aktion im Dialog registriert. Durch das Verknüpfen dieser Aktion mit einem Dialog wird der Dialog neu gestartet, wenn die Aktion ausgelöst wird. Das Auslösen dieser Aktion ähnelt dem Aufrufen der [replaceDialog](https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.session.html#replacedialog)-Methode. Dies ist nützlich für die Implementierung von Logik, um Benutzeräußerungen wie „Start over“ (Von vorn anfangen) zu bearbeiten oder um [Schleifen](bot-builder-nodejs-dialog-replace.md#repeat-an-action) zu erzeugen.

Der folgende Codeausschnitt zeigt, wie Sie eine [reloadAction][reloadAction] mit einem Dialog verknüpfen.

```javascript
// Order dinner.
bot.dialog('orderDinner', [
    //...waterfall steps...
])
// Once triggered, will restart the dialog.
.reloadAction('startOver', 'Ok, starting over.', {
    matches: /^start over$/i
});
```

Wenn Sie zusätzliche Argumente an den neu geladenen Dialog übergeben müssen, können Sie eine [`dialogArgs`](https://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.idialogactionoptions#dialogargs)-Option zur Aktion hinzufügen. Diese Option wird an den `args`-Parameter übergeben. Das Umschreiben des obigen Beispielcodes, um ein Argument für eine Aktion zum Neuladen zu erhalten, sieht ungefähr so aus:

```javascript
// Order dinner.
bot.dialog('orderDinner', [
    function(session, args, next){
        if(args && args.isReloaded){
            // Reload action was triggered.
        }

        session.send("Lets order some dinner!");
        builder.Prompts.choice(session, "Dinner menu:", dinnerMenu);
    }
    //...other waterfall steps...
])
// Once triggered, will restart the dialog.
.reloadAction('startOver', 'Ok, starting over.', {
    matches: /^start over$/i,
    dialogArgs: {
        isReloaded: true;
    }
});
```

### <a name="bind-a-cancelaction"></a>Verknüpfen einer cancelAction

Beim Verknüpfen einer `cancelAction` wird die Aktion im Dialog registriert. Nach dem Auslösen beendet diese Aktion den Dialog abrupt. Sobald der Dialog beendet ist, wird der übergeordnete Dialog mit einem wieder aufgenommenen Code fortgesetzt, der angibt, dass der Dialog `canceled` wurde. Mit dieser Aktion können Sie Äußerungen bearbeiten, wie „Nevermind“ (Schon gut) oder „Cancel“ (Abbrechen). Wenn Sie stattdessen einen Dialog programmgesteuert abbrechen müssen, finden Sie weitere Informationen unter [Dialog abbrechen](bot-builder-nodejs-dialog-replace.md#cancel-a-dialog). Weitere Informationen zum *fortgesetzten Code* finden Sie unter [Ergebnisse anzeigen](bot-builder-nodejs-dialog-prompt.md#prompt-results). 

Der folgende Codeausschnitt zeigt, wie Sie eine [cancelAction][cancelAction] mit einem Dialog verknüpfen.

```javascript
// Order dinner.
bot.dialog('orderDinner', [
    //...waterfall steps...
])
//Once triggered, will end the dialog.
.cancelAction('cancelAction', 'Ok, cancel order.', {
    matches: /^nevermind$|^cancel$|^cancel.*order/i
});
```

### <a name="bind-an-endconversationaction"></a>Verknüpfen einer endConversationAction

Beim Verknüpfen einer `endConversationAction` wird die Aktion im Dialog registriert. Nach dem Auslösen beendet diese Aktion die Konversation mit dem Benutzer. Das Auslösen dieser Aktion ähnelt dem Aufrufen der [endConversation](https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.session.html#endconversation)-Methode. Sobald eine Konversation beendet ist, löscht das Bot Builder SDK für Node.js den Dialogstapel und persistente Zustandsdaten. Weitere Informationen zu persistenten Zustandsdaten finden Sie unter [Verwalten von Zustandsdaten](bot-builder-nodejs-state.md).

Der folgende Codeausschnitt zeigt, wie Sie eine [endConversationAction][endConversationAction] mit einem Dialog verknüpfen.

```javascript
// Order dinner.
bot.dialog('orderDinner', [
    //...waterfall steps...
])
// Once triggered, will end the conversation.
.endConversationAction('endConversationAction', 'Ok, goodbye!', {
    matches: /^goodbye$/i
});
```

## <a name="confirm-interruptions"></a>Bestätigen von Unterbrechungen

Die meisten, wenn nicht all diese Aktionen, unterbrechen den normalen Konversationsablauf. Viele sind störend und sollten mit Vorsicht behandelt werden. Beispielsweise wird mit `triggerAction`, `cancelAction` oder `endConversationAction` der Dialogstapel gelöscht. Wenn der Benutzer den Fehler gemacht hat, eine dieser Aktionen auszulösen, muss er die Aufgabe neu starten. Um sicherzustellen, dass der Benutzer diese Aktionen wirklich auslösen wollte, können Sie eine `confirmPrompt`-Option zu diesen Aktionen hinzufügen. Mit `confirmPrompt` wird der Benutzer gefragt, ob wer die aktuelle Aufgabe wirklich abbrechen oder beenden möchte. So kann der Benutzer seine Meinung ändern und den Vorgang fortsetzen.

Der Codeausschnitt unten zeigt eine [cancelAction][cancelAction] mit einem [confirmPrompt](http://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.itriggeractionoptions#confirmprompt) um sicherzustellen, dass der Benutzer den Bestellvorgang wirklich abbrechen möchte.

```javascript
// Order dinner.
bot.dialog('orderDinner', [
    //...waterfall steps...
])
// Confirm before triggering the action.
// Once triggered, will end the dialog. 
.cancelAction('cancelAction', 'Ok, cancel order.', {
    matches: /^nevermind$|^cancel$|^cancel.*order/i,
    confirmPrompt: "Are you sure?"
});
```

Nachdem diese Aktion ausgelöst wurde, wird der Benutzer gefragt „Are you sure“ (Sind Sie sicher?). Der Benutzer muss „Yes“ (Ja) antworten, um die Aktion durchzuführen, oder mit „No“ (Nein), um die Aktion abzubrechen und an der vorherigen Stelle weiterzumachen.

## <a name="next-steps"></a>Nächste Schritte

**Aktionen** erlauben es Ihnen, Benutzeranforderungen zu antizipieren und dem Bot zu erlauben, diese elegant zu bearbeiten. Viele der folgenden Aktionen stören den aktuellen Konversationsablauf. Wenn Sie den Benutzern die Möglichkeit geben möchten, einen Konversationsablauf auszuschalten und wieder aufzunehmen, müssen Sie den Benutzerzustand vor dem Ausschalten speichern. Schauen wir uns genauer an, wie Sie den Benutzerzustand speichern und Zustandsdaten verwalten können.

> [!div class="nextstepaction"]
> [Verwalten von Zustandsdaten](bot-builder-nodejs-state.md)


[triggerAction]: https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.dialog.html#triggeraction

[cancelAction]: https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.dialog.html#cancelaction

[reloadAction]: https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.dialog.html#reloadaction

[beginDialogAction]: https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.dialog.html#begindialogaction

[endConversationAction]: https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.dialog.html#endconversationaction

[RecognizeIntent]: bot-builder-nodejs-recognize-intent-messages.md

[CardAction]: https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.cardaction#dialogaction
