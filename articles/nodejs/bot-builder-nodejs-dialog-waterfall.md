---
title: Definieren von Unterhaltungsschritten mit Wasserfällen | Microsoft-Dokumentation
description: In diesem Artikel erfahren Sie, wie mithilfe von Wasserfällen die Schritte einer Konversation mit dem Bot Framework SDK für Node.js definiert werden können.
author: v-ducvo
ms.author: v-ducvo
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 526091d61f10ac0c241b994aa3ea99c1d2a70074
ms.sourcegitcommit: b15cf37afc4f57d13ca6636d4227433809562f8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/11/2019
ms.locfileid: "54225325"
---
# <a name="define-conversation-steps-with-waterfalls"></a>Definieren von Unterhaltungsschritten mit Wasserfällen

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

Bei einer Unterhaltung handelt es sich um einen Austausch mehrerer Nachrichten zwischen einem Benutzer und einem Bot. Wenn das Ziel des Bots darin besteht, den Benutzer durch eine Reihe von Schritten zu leiten, können Sie anhand eines Wasserfalls die Schritte der Unterhaltung definieren.

Ein Wasserfall ist eine spezifische Implementierung eines [Dialogs](bot-builder-nodejs-dialog-overview.md), der am häufigsten dazu verwendet wird, Informationen vom Benutzer zu sammeln oder den Benutzer durch eine Reihe von Aufgaben zu leiten. Die Aufgaben werden als Array von Funktionen implementiert, bei dem die Ergebnisse der ersten Funktion als Eingabe an die nächste Funktion übergeben werden und so weiter. Jede Funktion stellt normalerweise einen Schritt im gesamten Prozess dar. Bei jedem Schritt fordert der Bot den Benutzer zur Eingabe auf, wartet auf eine Antwort und übergibt das Ergebnis dann an den nächsten Schritt.

Dieser Artikel bietet einen Überblick darüber, wie ein Wasserfall funktioniert und wie Sie damit einen [Konversationsfluss verwalten](bot-builder-nodejs-dialog-manage-conversation.md) können.

## <a name="conversation-steps"></a>Unterhaltungsschritte

Eine Unterhaltung umfasst in der Regel mehrere Vorgänge zum Austausch von Eingabeaufforderungen und Antworten zwischen dem Benutzer und dem Bot. Jeder Austausch von Eingabeaufforderungen und Antworten ist ein vorwärtsgerichteter Schritt in der Unterhaltung. In nur zwei Schritten können Sie eine Unterhaltung mithilfe eines Wasserfalls erstellen.

Betrachten Sie beispielsweise den folgenden `greetings`-Dialog: Im ersten Schritt des Wasserfalls erfolgt die Aufforderung zur Eingabe des Benutzernamens, während im zweiten Schritt die Antwort verwendet wird, um den Benutzer mit seinem Namen zu begrüßen.

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

Dies wird durch die Verwendung von Eingabeaufforderungen ermöglicht. Das Bot Framework SDK für Node.js bietet verschiedene Arten von integrierten [Eingabeaufforderungen](bot-builder-nodejs-dialog-prompt.md), mit denen Sie den Benutzer um verschiedene Arten von Informationen bitten können.

Der folgende Beispielcode zeigt einen Dialog, der im Rahmen eines 4-Schritte-Wasserfalls verschiedene Arten von Informationen vom Benutzer mithilfe von Eingabeaufforderungen sammelt.

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
        builder.Prompts.text(session, "How many people are in your party?");
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

In diesem Beispiel weist der Standarddialog vier Funktionen auf, die jeweils einen Schritt im Wasserfall darstellen. Bei jedem Schritt wird der Benutzer zur Eingabe aufgefordert und die Ergebnisse werden zur Verarbeitung an den nächsten Schritt gesendet. Dieser Prozess wird bis zur Ausführung des letzten Schritts fortgeführt, bei dem die Reservierung bestätigt und der Dialog beendet wird.

Das folgende Screenshot zeigt die Ergebnisse, die bei der Ausführung dieses Bots im [Bot Framework Channel Emulator](~/bot-service-debug-emulator.md) entstanden sind.

![Verwalten des Konversationsflusses mit Wasserfällen](~/media/bot-builder-nodejs-dialog-manage-conversation/waterfall-results.png)

## <a name="create-a-conversation-with-multiple-waterfalls"></a>Erstellen einer Unterhaltung mit mehreren Wasserfällen

Innerhalb einer Unterhaltung können Sie mehrere Wasserfälle verwenden, um die jeweilige für Ihren Bot erforderliche Unterhaltungsstruktur zu definieren. Beispielsweise können Sie anhand eines Wasserfalls den Konversationsfluss verwalten und zusätzliche Wasserfälle verwenden, um Informationen vom Benutzer zu sammeln. Jeder Wasserfall wird in einem Dialog gekapselt und kann durch Aufrufen der Funktion `session.beginDialog` aufgerufen werden.

Das folgende Codebeispiel zeigt, wie mehrere Wasserfälle in einer Unterhaltung verwendet werden. Der Wasserfall im Dialog `greetings` verwaltet den Konversationsfluss, während der Wasserfall im Dialog `askName` Informationen vom Benutzer sammelt.

```javascript
bot.dialog('greetings', [
    function (session) {
        session.beginDialog('askName');
    },
    function (session, results) {
        session.endDialog('Hello %s!', results.response);
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

## <a name="advance-the-waterfall"></a>Fortsetzen des Wasserfalls

Ein Wasserfall wird Schritt für Schritt in der Sequenz ausgeführt, in der die Funktionen im Array definiert sind. Die erste Funktion in einem Wasserfall kann die Argumente empfangen, die an den Dialog übergeben werden. Jede Funktion in einem Wasserfall kann auf die Funktion `next` zurückgreifen, um mit dem nächsten Schritt fortzufahren, ohne den Benutzer zur Eingabe aufzufordern.

Im folgenden Codebeispiel wird gezeigt, wie die Funktion `next` in einem Dialog verwendet wird, der den Benutzer durch den Prozess zur Bereitstellung von Informationen zu seinem Benutzerprofil führt. Bei jedem Schritt des Wasserfalls fordert der Bot den Benutzer zur Bereitstellung einer Angabe auf (falls erforderlich). Die Antwort des Benutzers (sofern vorhanden) wird im nachfolgenden Schritt des Wasserfalls verarbeitet. Im letzten Schritt des Wasserfalls im Dialog `ensureProfile` wird dieser Dialog beendet und die vervollständigten Profilinformationen werden im aufrufenden Dialog zurückgegeben, der eine personalisierte Begrüßung an den Benutzer sendet.

```javascript
var inMemoryStorage = new builder.MemoryBotStorage();

// This bot ensures user's profile is up to date.
var bot = new builder.UniversalBot(connector, [
    function (session) {
        session.beginDialog('ensureProfile', session.userData.profile);
    },
    function (session, results) {
        session.userData.profile = results.response; // Save user profile.
        session.send(`Hello ${session.userData.profile.name}! I love ${session.userData.profile.company}!`);
    }
]).set('storage', inMemoryStorage); // Register in-memory storage 

bot.dialog('ensureProfile', [
    function (session, args, next) {
        session.dialogData.profile = args || {}; // Set the profile or create the object.
        if (!session.dialogData.profile.name) {
            builder.Prompts.text(session, "What's your name?");
        } else {
            next(); // Skip if we already have this info.
        }
    },
    function (session, results, next) {
        if (results.response) {
            // Save user's name if we asked for it.
            session.dialogData.profile.name = results.response;
        }
        if (!session.dialogData.profile.company) {
            builder.Prompts.text(session, "What company do you work for?");
        } else {
            next(); // Skip if we already have this info.
        }
    },
    function (session, results) {
        if (results.response) {
            // Save company name if we asked for it.
            session.dialogData.profile.company = results.response;
        }
        session.endDialogWithResult({ response: session.dialogData.profile });
    }
]);
```

> [!NOTE]
> In diesem Beispiel werden zwei unterschiedliche Datenbehälter zum Speichern von Daten verwendet: `dialogData` und `userData`. Wenn der Bot auf mehrere Computeknoten verteilt wurde, kann jeder Schritt des Wasserfalls von einem anderen Knoten verarbeitet werden. Weitere Informationen zum Speichern von Bot-Daten finden Sie unter [Verwalten von Statusdaten](bot-builder-nodejs-state.md).

## <a name="end-a-waterfall"></a>Beenden eines Wasserfalls

Ein Dialog, der mithilfe eines Wasserfalls erstellt wird, muss explizit beendet werden, da der Bot andernfalls den Wasserfall endlos wiederholt. Sie können einen Wasserfall mithilfe der folgenden Methoden beenden:

* `session.endDialog`: Verwenden Sie diese Methode, um den Wasserfall zu beenden, wenn keine Daten an den aufrufenden Dialog zurückgegeben werden.

* `session.endDialogWithResult`: Verwenden Sie diese Methode, um den Wasserfall zu beenden, wenn Daten vorhanden sind, die an den aufrufenden Dialog zurückgegeben werden. Bei dem Argument `response`, das zurückgegeben wird, kann es sich um ein JSON-Objekt oder einen beliebigen primitiven JavaScript-Datentyp handeln. Beispiel: 
  ```javascript
  session.endDialogWithResult({
    response: { name: session.dialogData.name, company: session.dialogData.company }
  });
  ```

* `session.endConversation`: Verwenden Sie diese Methode, um den Wasserfall zu beenden, wenn das Ende des Wasserfall das Ende der Unterhaltung darstellt.

Als Alternative zu diesen drei Methoden zum Beenden eines Wasserfalls können Sie den Trigger `endConversationAction` an den Dialog anfügen. Beispiel: 

```javascript
bot.dialog('dinnerOrder', [
    //...waterfall steps...,
    // Last step
    function(session, results){
        if(results.response){
            session.dialogData.room = results.response;
            var msg = `Thank you. Your order will be delivered to room #${session.dialogData.room}`;
            session.endConversation(msg);
        }
    }
])
.endConversationAction(
    "endOrderDinner", "Ok. Goodbye.",
    {
        matches: /^cancel$|^goodbye$/i,
        confirmPrompt: "This will cancel your order. Are you sure?"
    }
);
```

## <a name="next-steps"></a>Nächste Schritte

Mithilfe eines Wasserfalls können Sie Informationen vom Benutzer mit *Eingabeaufforderungen* erfassen. Werfen wir einen Blick darauf, wie Sie Benutzer zur Eingabe auffordern können.

> [!div class="nextstepaction"]
> [Auffordern der Benutzer zu Eingaben](bot-builder-nodejs-dialog-prompt.md)
