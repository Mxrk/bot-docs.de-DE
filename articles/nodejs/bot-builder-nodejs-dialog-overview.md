---
title: Übersicht über Dialoge | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Dialoge im Bot Builder SDK für Node.js verwenden, um Konversationen zu modellieren und den Konversationsfluss zu verwalten.
author: DucVo
ms.author: v-ducvo
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: c92d68c429dc48722640f29032338528ac96e24c
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39298738"
---
# <a name="dialogs-in-the-bot-builder-sdk-for-nodejs"></a>Dialoge im Bot Builder SDK für Node.js
> [!div class="op_single_selector"]
> - [.NET](../dotnet/bot-builder-dotnet-dialogs.md)
> - [Node.js](../nodejs/bot-builder-nodejs-dialog-overview.md)

Mit Dialogen im Bot Builder SDK für Node.js können Sie Konversationen modellieren und den Konversationsfluss verwalten. Ein Bot kommuniziert mit einem Benutzer über Konversationen. Konversationen sind in Dialogen organisiert. Dialoge können Wasserfallschritte und Anweisungen enthalten. Während der Benutzer mit dem Bot interagiert, startet und beendet der Bot verschiedene Dialoge als Reaktion auf Benutzernachrichten und wechselt zwischen diesen. Ein Verständnis der Funktionsweise von Dialogen ist der Schlüssel für das erfolgreiche Entwerfen und Erstellen ausgezeichneter Bots. 

In diesem Artikel werden Konzepte von Dialogen einführend erläutert. Nachdem Sie diesen Artikel gelesen haben, folgen Sie den Links im Abschnitt [Nächste Schritte](#next-steps), um tiefer in diese Konzepte einzudringen.

## <a name="conversations-through-dialogs"></a>Konversationen über Dialoge

Im Bot Builder SDK für Node.js ist eine Konversation als Kommunikation zwischen einem Bot und einem Benutzer über Dialoge definiert. Ein Dialog ist auf grundlegender Ebene ein wiederverwendbares Modul, das einen Vorgang ausführt oder Informationen von einem Benutzer sammelt. Sie können die komplexe Logik Ihres Bots in wiederverwendbarem Dialogcode kapseln.

Eine Konversation kann vielfältig strukturiert und geändert werden:

- Sie kann aus Ihrem [Standarddialog](#default-dialog) stammen.
- Sie kann von einem Dialog zu einem anderen umgeleitet werden.
- Sie kann wieder aufgenommen werden.
- Sie kann einem [Wasserfallmuster](bot-builder-nodejs-dialog-waterfall.md) folgen, das den Benutzer durch eine Reihe von Schritten führt oder dem Benutzer eine Reihe von [Fragen stellt](bot-builder-nodejs-dialog-prompt.md).
- Sie kann [Aktionen](bot-builder-nodejs-dialog-actions.md) verwenden, die auf Wörter oder Ausdrücke lauschen, die einen anderen Dialog auslösen. 

Sie können sich eine Konversation als übergeordnetes Element für Dialoge vorstellen. Eine Konversation enthält einen *Dialogstapel* und verwaltet einen eigenen Satz von Zustandsdaten: `conversationData` und `privateConversationData`. Ein Dialog verwaltet hingegen `dialogData`. Weitere Informationen zu Zustandsdaten finden Sie unter [Verwalten von Zustandsdaten](bot-builder-nodejs-state.md).

## <a name="dialog-stack"></a>Dialogstapel

Ein Bot interagiert mit Benutzern über eine Reihe von Dialogen, die in einem Dialogstapel verwaltet werden. Dialoge werden im Verlauf einer Konversation auf dem Stapel abgelegt und von diesem entnommen. Der Stapel funktioniert wie ein normaler LIFO-Stapel, d.h., der letzte hinzugefügte Dialog ist der erste, der abgeschlossen wird. Nach Abschluss eines Dialogs wird die Steuerung an den vorherigen Dialog im Stapel zurückgegeben.

Beim ersten Starten einer Botkonversation oder beim Ende einer Konversation ist der Dialogstapel leer. Wenn der Benutzer in diesem Zustand eine Nachricht an den Bot sendet, antwortet der Bot mit dem *Standarddialog*.

## <a name="default-dialog"></a>Standarddialog

Vor Version 3.5 von Bot Framework wurde ein *Stammdialog* durch Hinzufügen eines Dialogs mit dem Namen `/` definiert. Dies führte zu Namenskonventionen ähnlich wie bei URLs. Diese Namenskonvention war für die Benennung von Dialogen nicht geeignet. 

> [!NOTE]
> Ab Version 3.5 von Bot Framework wird der *Standarddialog* als zweiter Parameter im Konstruktor von [`UniversalBot`](https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.universalbot.html#constructor) registriert.  

Der folgende Codeausschnitt zeigt, wie Sie den Standarddialog beim Erstellen des `UniversalBot`-Objekts definieren.

```javascript
var bot = new builder.UniversalBot(connector, [
    //...Default dialog waterfall steps...
    ]);
```

Der Standarddialog wird ausgeführt, wenn der Dialogstapel leer ist und kein anderer Dialog über LUIS oder eine andere [Erkennung](bot-builder-nodejs-recognize-intent-messages.md) [ausgelöst](bot-builder-nodejs-dialog-actions.md) wird. Da der Standarddialog die erste Antwort des Bots an den Benutzer ist, sollte der Standarddialog dem Benutzer einige Kontextinformationen bieten, z.B. eine Liste der verfügbaren Befehle oder eine Übersicht über die Möglichkeiten des Bots.

## <a name="dialog-handlers"></a>Dialoghandler

Der Dialoghandler verwaltet den Fluss einer Konversation. Im Verlauf einer Konversation leitet der Dialoghandler den Fluss durch Starten und Beenden von Dialogen. 

## <a name="starting-and-ending-dialogs"></a>Starten und Beenden von Dialogen

Verwenden Sie zum Starten eines neuen Dialogs (und Übertragen auf den Stapel per Push) [`session.beginDialog()`](http://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.session#begindialog). Um einen Dialog zu beenden (und ihn vom Stapel zu entfernen und die Steuerung an den aufrufenden Dialog zurückzugeben), verwenden Sie [`session.endDialog()`](http://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.session#enddialog) oder [`session.endDialogWithResult()`](http://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.session#enddialogwithresult). 

## <a name="using-waterfalls-and-prompts"></a>Verwenden von Wasserfällen und Fragen

Ein [Wasserfall](bot-builder-nodejs-dialog-waterfall.md) ist eine einfache Möglichkeit zum Modellieren und Verwalten des Konversationsflusses. Wasserfälle enthalten eine Abfolge von Schritten. In jedem Schritt können Sie entweder eine Aktion im Namen des Benutzers abschließen oder Informationen vom Benutzer [erfragen](bot-builder-nodejs-dialog-prompt.md).

Ein Wasserfall wird über einen Dialog implementiert, der sich aus einer Sammlung von Funktionen zusammensetzt. Jede Funktion definiert einen Schritt im Wasserfall. Das folgende Codebeispiel zeigt eine einfache Konversation, die mithilfe eines zweistufigen Wasserfalls den Benutzer nach seinem Namen fragt und ihn mit seinem Namen begrüßt.

```javascript
// Ask the user for their name and greet them by name.
bot.dialog('greetings', [
    function (session) {
        builder.Prompts.text(session, 'Hi! What is your name?');
    },
    function (session, results) {
        session.endDialog(`Hello ${results.response}!`);
    }
]);
```

Wenn der Bot das Ende des Wasserfalls erreicht, ohne den Dialog zu beenden, startet die nächste Nachricht des Benutzers den Dialog bei Schritt 1 des Wasserfalls neu. Dies kann Frustration verursachen, wenn der Benutzer den Eindruck hat, in einer Schleife gefangen zu sein. Um diese Situation zu vermeiden, empfiehlt es sich, am Ende einer Konversation oder eines Dialogs explizit `endDialog`, `endDialogWithResult` oder `endConversation` aufzurufen.

## <a name="next-steps"></a>Nächste Schritte

Um tiefer in Dialog einzudringen, müssen Sie verstehen, wie Wasserfallmuster funktionieren und wie Sie sie zum Leiten eines Benutzers durch einen Vorgang verwenden.

> [!div class="nextstepaction"]
> [Definieren von Konversationsschritten mit Wasserfällen](bot-builder-nodejs-dialog-waterfall.md)
