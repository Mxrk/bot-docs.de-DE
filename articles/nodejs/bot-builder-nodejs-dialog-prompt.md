---
title: Auffordern zu Benutzereingaben | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Eingabeaufforderungen verwenden, um mit dem Bot Builder SDK für Node.js Benutzereingaben zu sammeln.
author: v-ducvo
ms.author: v-ducvo
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 0926c15d2c62bfa74ddb465d8c816dee7c8fb576
ms.sourcegitcommit: 6c719b51c9e4e84f5642100a33fe346b21360e8a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/28/2018
ms.locfileid: "52451972"
---
# <a name="prompt-for-user-input"></a>Auffordern zu Benutzereingaben

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

Das Bot Builder SDK für Node.js stellt eine Reihe von integrierten Eingabeaufforderungen bereit, um auf einfache Weise Eingaben von einem Benutzer zu sammeln. 

Eine *Eingabeaufforderung* wird verwendet, wenn ein Bot Eingaben vom Benutzer benötigt. Mit Eingabeaufforderungen können Sie einen Benutzer zu einer Reihe von Eingaben auffordern, indem Sie die Eingabeaufforderungen in einem Wasserfall verketten. Eingabeaufforderungen in Verbindung mit einem [Wasserfall](bot-builder-nodejs-dialog-waterfall.md) können Sie zur besseren [Verwaltung des Konversationsflusses](bot-builder-nodejs-manage-conversation-flow.md) in Ihrem Bot verwenden. 

Dieser Artikel hilft Ihnen zu verstehen, wie Eingabeaufforderungen funktionieren und zum Sammeln von Informationen von Benutzern verwendet werden können.

## <a name="prompts-and-responses"></a>Eingabeaufforderungen und Antworten

Jedes Mal, wenn Sie von einem Benutzer eine Eingabe benötigen, können Sie eine Eingabeaufforderung senden, warten, bis der Benutzer mit einer Eingabe reagiert, anschließend die Eingabe verarbeiten und eine Antwort an den Benutzer senden.

Im folgenden Codebeispiel wird der Benutzer zur Eingabe seines Namens aufgefordert und erhält als Antwort eine Begrüßungsnachricht.

```javascript
bot.dialog('greetings', [
    // Step 1
    function (session) {
        builder.Prompts.text(session, 'Hi! What is your name?');
    },
    // Step 2
    function (session, results) {
        session.endDialog(`Hello ${results.response}!`);
    }
]);
```

Mit diesem grundlegenden Konstrukt können Sie Ihren Konversationsfluss modellieren, indem Sie so viele Eingabeaufforderungen und Antworten hinzufügen, wie Ihr Bot benötigt.

## <a name="prompt-results"></a>Ergebnisse von Eingabeaufforderungen 

Die integrierten Eingabeaufforderungen werden als [Dialoge](bot-builder-nodejs-dialog-overview.md) implementiert, welche die Antwort des Benutzers im Feld `results.response` zurückgeben. Bei JSON-Objekten werden die Antworten im Feld `results.response.entity` zurückgegeben. Jeder [Dialoghandler](bot-builder-nodejs-dialog-overview.md#dialog-handlers) kann ungeachtet seines Typs das Ergebnis einer Eingabeaufforderung empfangen. Sobald der Bot eine Antwort erhält, kann er sie nutzen oder durch Aufrufen der [`session.endDialogWithResult`][EndDialogWithResult]-Methode an den aufrufenden Dialog zurückgeben.

Das folgende Codebeispiel zeigt, wie das Ergebnis einer Eingabeaufforderung mit der `session.endDialogWithResult`-Methode an den aufrufenden Dialog zurückgegeben wird. In diesem Beispiel verwendet der `greetings`-Dialog das Ergebnis der Eingabeaufforderung, das der `askName`-Dialog zurückgibt, um den Benutzer mit seinem Namen zu begrüßen.

```javascript
// Ask the user for their name and greet them by name.
bot.dialog('greetings', [
    function (session) {
        session.beginDialog('askName');
    },
    function (session, results) {
        session.endDialog(`Hello ${results.response}!`);
    }
]);
bot.dialog('askName', [
    function (session) {
        builder.Prompts.text(session, 'Hi! What is your name?');
    },
    function (session, results) {
        session.endDialogWithResult(results);
    }
]);
```

## <a name="prompt-types"></a>Eingabeaufforderungstypen
Das Bot Builder SDK für Node.js enthält integrierte Eingabeaufforderungen verschiedenen Typs. 

|**Eingabeaufforderungstyp**     | **Beschreibung** |     
| ------------------ | --------------- |
|[Prompts.text](#promptstext) | Der Benutzer wird zur Eingabe einer Textzeichenfolge aufgefordert. |     
|[Prompts.confirm](#promptsconfirm) | Der Benutzer wird aufgefordert, eine Aktion zu bestätigen.| 
|[Prompts.number](#promptsnumber) | Der Benutzer wird zur Eingabe einer Zahl aufgefordert.     |
|[Prompts.time](#promptstime) | Der Benutzer wird zur Eingabe von Uhrzeit bzw. Datum/Uhrzeit aufgefordert.      |
|[Prompts.choice](#promptschoice) | Der Benutzer wird aufgefordert, aus einer Liste von Optionen auszuwählen.    |
|[Prompts.attachment](#promptsattachment) | Der Benutzer wird aufgefordert, ein Bild oder Video hochzuladen.|       

Die folgenden Abschnitte enthalten weitere Informationen zu den einzelnen Eingabeaufforderungstypen.

### <a name="promptstext"></a>Prompts.text

Mit der [Prompts.text()][PromptsText]-Methode fordern Sie den Benutzer zur Eingabe einer **Textzeichenfolge** auf. Die Eingabeaufforderung gibt die Antwort des Benutzers als [IPromptTextResult][IPromptTextResult] zurück.

```javascript
builder.Prompts.text(session, "What is your name?");
```

### <a name="promptsconfirm"></a>Prompts.confirm

Mit der [Prompts.confirm()][PromptsConfirm] Methode fordern Sie den Benutzer auf, eine Aktion mit der Antwort **Ja/Nein** zu bestätigen. Die Eingabeaufforderung gibt die Antwort des Benutzers als [IPromptConfirmResult](http://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.ipromptconfirmresult.html) zurück.

```javascript
builder.Prompts.confirm(session, "Are you sure you wish to cancel your order?");
```

### <a name="promptsnumber"></a>Prompts.number

Mit der [Prompts.number()][PromptsNumber]-Methode fordern Sie den Benutzer zur Eingabe einer **Zahl** auf. Die Eingabeaufforderung gibt die Antwort des Benutzers als [IPromptNumberResult](http://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.ipromptnumberresult.html) zurück.

```javascript
builder.Prompts.number(session, "How many would you like to order?");
```

### <a name="promptstime"></a>Prompts.time

Mit der [Prompts.time()][PromptsTime]-Methode fordern Sie den Benutzer zur Eingabe von **Uhrzeit** bzw. **Datum/Uhrzeit** auf. Die Eingabeaufforderung gibt die Antwort des Benutzers als [IPromptTimeResult](http://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.iprompttimeresult.html) zurück. Das Framework verwendet die [Chrono](https://github.com/wanasit/chrono)-Bibliothek, um die Antwort des Benutzers zu analysieren und unterstützt sowohl relative Antworten (z. B. "in 5 Minuten") als auch nicht relative Antworten (z. B. "6. Juni, 14 Uhr").

Das Feld [results.response](http://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.iprompttimeresult.html#response), das die Antwort des Benutzers darstellt, enthält ein [entity](http://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.ientity.html)-Objekt, das Datum und Uhrzeit angibt. Um Datum und Uhrzeit in ein JavaScript `Date`-Objekt aufzulösen, verwenden Sie die [EntityRecognizer.resolveTime()](http://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.entityrecognizer.html#resolvetime)-Methode.

> [!TIP] 
> Die vom Benutzer eingegebene Uhrzeit wird basierend auf der Zeitzone des Servers, der den Bot hostet, in UTC-Zeit konvertiert. Da sich der Server möglicherweise in einer anderen Zeitzone als der Benutzer befindet, achten Sie darauf, die Zeitzonen zu berücksichtigen. Damit Datum und Uhrzeit in die Ortszeit des Benutzers konvertiert werden kann, sollten Sie den Benutzer fragen, in welcher Zeitzone er sich befindet.

```javascript
bot.dialog('createAlarm', [
    function (session) {
        session.dialogData.alarm = {};
        builder.Prompts.text(session, "What would you like to name this alarm?");
    },
    function (session, results, next) {
        if (results.response) {
            session.dialogData.name = results.response;
            builder.Prompts.time(session, "What time would you like to set an alarm for?");
        } else {
            next();
        }
    },
    function (session, results) {
        if (results.response) {
            session.dialogData.time = builder.EntityRecognizer.resolveTime([results.response]);
        }

        // Return alarm to caller  
        if (session.dialogData.name && session.dialogData.time) {
            session.endDialogWithResult({ 
                response: { name: session.dialogData.name, time: session.dialogData.time } 
            }); 
        } else {
            session.endDialogWithResult({
                resumed: builder.ResumeReason.notCompleted
            });
        }
    }
]);
```

### <a name="promptschoice"></a>Prompts.choice

Mit der [Prompts.choice()][PromptsChoice]-Methode fordern Sie den Benutzer auf, **aus einer Liste von Optionen auszuwählen**. Der Benutzer kann seine Auswahl entweder durch Eingabe der mit der ausgewählten Option verbunden Nummer oder des entsprechenden Namens übermitteln. Sowohl vollständige als auch teilweise Übereinstimmungen mit dem Namen der Option werden unterstützt. Die Eingabeaufforderung gibt die Antwort des Benutzers als [IPromptChoiceResult](http://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.ipromptchoiceresult.html) zurück. 

Um das Format der Liste anzugeben, die dem Benutzer angezeigt wird, legen Sie die [IPromptOptions.listStyle](http://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.ipromptoptions.html#liststyle)-Eigenschaft fest. Die folgende Tabelle enthält die `ListStyle`-Enumerationswerte für diese Eigenschaft.


Es gelten folgende `ListStyle`-Enumerationswerte:

| Index | NAME | BESCHREIBUNG |
| ---- | ---- | ---- |
| 0 | none | Es wird keine Liste gerendert. Wird verwendet, wenn die Liste als Teil der Eingabeaufforderung enthalten ist. |
| 1 | inline | Optionen werden als eine Inlineliste des Formulars gerendert "1. rot, 2. grün oder 3. blau". |
| 2 | list | Die Optionen werden als nummerierte Liste gerendert. |
| 3 | button | Die Optionen werden als Schaltflächen für die Kanäle gerendert, die Schaltflächen unterstützen. Für andere Kanäle werden sie als Text gerendert. |
| 4 | auto | Das Format wird automatisch anhand des Kanals und der Anzahl der Optionen ausgewählt. | 

Auf diese Enumeration können Sie über das `builder`-Objekt zugreifen, oder Sie können einen Index zum Auswählen des `ListStyle` bereitstellen. Zum Beispiel erfüllen beide Anweisungen im folgenden Codeausschnitt die gleiche Aufgabe.

```javascript
// ListStyle passed in as Enum
builder.Prompts.choice(session, "Which color?", "red|green|blue", { listStyle: builder.ListStyle.button });

// ListStyle passed in as index
builder.Prompts.choice(session, "Which color?", "red|green|blue", { listStyle: 3 });
```

Um die Liste der Optionen anzugeben, können Sie eine durch senkrechte Striche (`|`) getrennte Zeichenfolge, ein Array von Zeichenfolgen oder eine Objektzuordnung verwenden.

Durch senkrechte Striche getrennte Zeichenfolge: 

```javascript
builder.Prompts.choice(session, "Which color?", "red|green|blue");
```

Array von Zeichenfolgen:

```javascript
builder.Prompts.choice(session, "Which color?", ["red","green","blue"]);
```

Objektzuordnung: 

```javascript
var salesData = {
    "west": {
        units: 200,
        total: "$6,000"
    },
    "central": {
        units: 100,
        total: "$3,000"
    },
    "east": {
        units: 300,
        total: "$9,000"
    }
};

bot.dialog('getSalesData', [
    function (session) {
        builder.Prompts.choice(session, "Which region would you like sales for?", salesData); 
    },
    function (session, results) {
        if (results.response) {
            var region = salesData[results.response.entity];
            session.send(`We sold ${region.units} units for a total of ${region.total}.`); 
        } else {
            session.send("OK");
        }
    }
]);
```

### <a name="promptsattachment"></a>Prompts.attachment

Mit der [Prompts.attachment()][PromptsAttachment]-Methode fordern Sie den Benutzer auf, eine Datei (z. B. ein Bild oder Video) hochzuladen. Die Eingabeaufforderung gibt die Antwort des Benutzers als [IPromptAttachmentResult](http://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.ipromptattachmentresult.html) zurück.

```javascript
builder.Prompts.attachment(session, "Upload a picture for me to transform.");
```

## <a name="next-steps"></a>Nächste Schritte

Da Sie nun wissen, wie Sie Benutzer schrittweise durch einen Wasserfall leiten und sie zur Eingabe von Informationen auffordern, schauen wir uns an, wie Sie Ihren Konversationsfluss besser verwalten können.

> [!div class="nextstepaction"]
> [Verwalten des Konversationsflusses](bot-builder-nodejs-dialog-manage-conversation-flow.md)


[SendAttachments]: bot-builder-nodejs-send-receive-attachments.md
[SendCardWithButtons]: bot-builder-nodejs-send-rich-cards.md
[RecognizeUserIntent]: bot-builder-nodejs-recognize-intent-messages.md
[SaveUserData]: bot-builder-nodejs-save-user-data.md

[UniversalBot]: https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.universalbot.html
[ChatConnector]: https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.chatconnector.html
[Session]: https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.session


[SendTyping]: https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.session#sendtyping

[EndDialogWithResult]: https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.session.html#enddialogwithresult

[IPromptResult]: https://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.ipromptresult.html

[Result_Response]: https://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.ipromptresult.html#response

[ResumeReason]: https://docs.botframework.com/en-us/node/builder/chat-reference/enums/_botbuilder_d_.resumereason.html

[Result_Resumed]: https://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.ipromptresult.html#resumed

[entity]: https://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.ientity.html

[ResolveTime]: https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.entityrecognizer.html#resolvetime

[PromptsRef]: https://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.__global.iprompts.html

[PromptsText]: https://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.__global.iprompts.html#text

[IPromptTextResult]: https://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.iprompttextresult.html

[PromptsConfirm]: https://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.__global.iprompts.html#confirm

[IPromptConfirmResult]: https://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.ipromptconfirmresult.html

[PromptsNumber]: https://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.__global.iprompts.html#number

[IPromptNumberResult]: https://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.ipromptnumberresult.html

[PromptsTime]: https://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.__global.iprompts.html#time

[IPromptTimeResult]: https://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.iprompttimeresult.html

[PromptsChoice]: https://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.__global.iprompts.html#choice

[IPromptChoiceResult]: https://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.ipromptchoiceresult.html

[PromptsAttachment]: https://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.__global.iprompts.html#attachment

[IPromptAttachmentResult]: https://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.ipromptattachmentresult.html
