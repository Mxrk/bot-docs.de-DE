---
title: Hinzufügen von Rich Card-Anlagen zu Nachrichten | Microsoft-Dokumentation
description: Hier erfahren Sie, wie interaktive, ansprechende Rich Cards mit dem Bot Builder SDK für Node.js gesendet werden.
author: v-ducvo
ms.author: v-ducvo
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 7f94ea05fcccfe7bdeb1dec187d735cef28b1d7c
ms.sourcegitcommit: 2dc75701b169d822c9499e393439161bc87639d2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/24/2018
ms.locfileid: "42905394"
---
# <a name="add-rich-card-attachments-to-messages"></a>Hinzufügen von Rich Card-Anlagen zu Nachrichten

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]


> [!div class="op_single_selector"]
> - [.NET](../dotnet/bot-builder-dotnet-add-rich-card-attachments.md)
> - [Node.js](../nodejs/bot-builder-nodejs-send-rich-cards.md)
> - [REST](../rest-api/bot-framework-rest-connector-add-rich-cards.md)

Verschiedene Kanäle wie Skype und Facebook unterstützen das Versenden von Karten mit ansprechender Grafik und interaktiven Schaltflächen an Benutzer, über die Benutzer eine Aktion initiieren können. Das SDK stellt verschiedene Nachrichten- und Kartengeneratorklassen bereit, die zum Erstellen und Senden von Karten verwendet werden können. Diese Karten werden zur Unterstützung der plattformübergreifenden Kommunikation vom Bot Framework Connector Service mit dem kanaleigenen Schema gerendert. Wenn der Kanal keine Karten wie SMS unterstützt, bemüht sich Bot Framework eine vernünftige Oberfläche für Benutzer zu rendern. 

## <a name="types-of-rich-cards"></a>Typen von Rich Cards 
Bot Framework unterstützt derzeit acht Typen von Rich Cards: 

| Kartentyp | BESCHREIBUNG |
|------|------|
| <a href="/adaptive-cards/get-started/bots">Adaptive Karten</a> | Anpassbare Rich Cards, die eine beliebige Kombination von Text, Sprache, Bildern, Schaltflächen und Eingabefeldern enthalten können.  Weitere Informationen finden Sie unter [Unterstützung pro Kanal](/adaptive-cards/get-started/bots#channel-status). |
| [Animationskarten][animationCard] | Eine Karte, die animierte GIFs oder kurze Videos abspielen kann. |
| [Audiokarten][audioCard] | Eine Karte, die eine Audiodatei abspielen kann. |
| [Hero Cards][heroCard] | Rich Cards, die in der Regel ein einzelnes großes Bild, eine oder mehrere Schaltflächen sowie Text enthalten. |
| [Miniaturbildkarten][thumbnailCard] | Rich Cards, die in der Regel ein Miniaturbild, eine oder mehrere Schaltflächen sowie Text enthalten.|
| [Quittungskarten][receiptCard] | Eine Karte, über die ein Bot Rechnungen für Benutzer ausstellen kann. Sie enthalten in der Regel die Liste der Elemente, die in der Quittung enthalten sein sollen, Steuer- und Gesamtbeträge sowie anderen Text. |
| [SignIn Cards][signinCard] | Eine Karte, über die ein Bot den Benutzer auffordern kann, sich anzumelden. Sie enthalten in der Regel Text und eine oder mehrere Schaltflächen, auf die der Benutzer klicken kann, um den Anmeldevorgang zu initiieren. |
| [Videokarte][videoCard] | Eine Karte, die Videos abspielen kann. |

## <a name="send-a-carousel-of-hero-cards"></a>Senden eines Karussells von Hero Cards
Im folgenden Beispiel ist ein Bot für ein fiktives T-Shirt-Unternehmen dargestellt, das zeigt, wie als Reaktion auf die mündliche Benutzereingabe „Hemden anzeigen“ ein Karussell von Karten gesendet wird. 

```javascript
// Create your bot with a function to receive messages from the user
// Create bot and default message handler
var bot = new builder.UniversalBot(connector, function (session) {
    session.send("Hi... We sell shirts. Say 'show shirts' to see our products.");
});

// Add dialog to return list of shirts available
bot.dialog('showShirts', function (session) {
    var msg = new builder.Message(session);
    msg.attachmentLayout(builder.AttachmentLayout.carousel)
    msg.attachments([
        new builder.HeroCard(session)
            .title("Classic White T-Shirt")
            .subtitle("100% Soft and Luxurious Cotton")
            .text("Price is $25 and carried in sizes (S, M, L, and XL)")
            .images([builder.CardImage.create(session, 'http://petersapparel.parseapp.com/img/whiteshirt.png')])
            .buttons([
                builder.CardAction.imBack(session, "buy classic white t-shirt", "Buy")
            ]),
        new builder.HeroCard(session)
            .title("Classic Gray T-Shirt")
            .subtitle("100% Soft and Luxurious Cotton")
            .text("Price is $25 and carried in sizes (S, M, L, and XL)")
            .images([builder.CardImage.create(session, 'http://petersapparel.parseapp.com/img/grayshirt.png')])
            .buttons([
                builder.CardAction.imBack(session, "buy classic gray t-shirt", "Buy")
            ])
    ]);
    session.send(msg).endDialog();
}).triggerAction({ matches: /^(show|list)/i });
```
In diesem Beispiel wird die [Message][Message]-Klasse zum Erstellen eines Karussells verwendet.  
Ein Karussell besteht aus einer Liste mit [HeroCard][heroCard]-Klassen, die ein Bild, Text und eine Schaltfläche enthalten, die den Kauf eines Artikels auslöst.  
Wenn der Benutzer auf die Schaltfläche **Kaufen** klickt, wird der Versand einer Nachricht ausgelöst. Daher wird ein zweiter Dialog zum Erfassen des Klicks benötigt. 

## <a name="handle-button-input"></a>Behandeln von Schaltflächeneingaben

Der `buyButtonClick`-Dialog wird beim Empfang einer Nachricht ausgelöst, die die Wörter „kaufen“ oder „hinzufügen“ und „Hemd“ oder „Shirt“ enthält. Der Dialog verwendet zunächst reguläre Ausdrücke, um nach einem Hemd in der vom Benutzer angegebenen Farbe und Größe zu suchen.
Dank dieser zusätzlichen Flexibilität können Sie sowohl Klicks als auch Nachrichten in natürlicher Sprache wie etwa „ein graues Hemd in Größe L in meinen Einkaufswagen legen“ unterstützen.
Wenn die Farbe gültig, die Größe jedoch unbekannt ist, wird der Benutzer aufgefordert, eine Größe aus einer Liste auszuwählen, bevor der Artikel in den Einkaufswagen gelegt wird. Sobald der Bot alle erforderlichen Informationen erfasst hat, legt er den Artikel mit **session.userData** in einen Einkaufswagen, der gespeichert wird, und sendet dem Benutzer eine Bestätigungsmeldung.

```javascript
// Add dialog to handle 'Buy' button click
bot.dialog('buyButtonClick', [
    function (session, args, next) {
        // Get color and optional size from users utterance
        var utterance = args.intent.matched[0];
        var color = /(white|gray)/i.exec(utterance);
        var size = /\b(Extra Large|Large|Medium|Small)\b/i.exec(utterance);
        if (color) {
            // Initialize cart item
            var item = session.dialogData.item = { 
                product: "classic " + color[0].toLowerCase() + " t-shirt",
                size: size ? size[0].toLowerCase() : null,
                price: 25.0,
                qty: 1
            };
            if (!item.size) {
                // Prompt for size
                builder.Prompts.choice(session, "What size would you like?", "Small|Medium|Large|Extra Large");
            } else {
                //Skip to next waterfall step
                next();
            }
        } else {
            // Invalid product
            session.send("I'm sorry... That product wasn't found.").endDialog();
        }   
    },
    function (session, results) {
        // Save size if prompted
        var item = session.dialogData.item;
        if (results.response) {
            item.size = results.response.entity.toLowerCase();
        }

        // Add to cart
        if (!session.userData.cart) {
            session.userData.cart = [];
        }
        session.userData.cart.push(item);

        // Send confirmation to users
        session.send("A '%(size)s %(product)s' has been added to your cart.", item).endDialog();
    }
]).triggerAction({ matches: /(buy|add)\s.*shirt/i });
```

<!-- 

> [!NOTE]
> When sending a message that contains images, keep in mind that some channels download images before displaying a message to the user.   
> As a result, a message containing an image followed immediately by a message without images may sometimes be flipped in the user's feed.
> For information on how to avoid messages being sent out of order, see [Message ordering][MessageOrder].  

-->
## <a name="add-a-message-delay-for-image-downloads"></a>Hinzufügen einer Meldungsverzögerung für Bilddownloads
Bei einigen Kanälen werden vor der Anzeige einer Meldung gerne Bilder heruntergeladen, sodass die Reihenfolge von Meldungen im Feed des Benutzers gelegentlich vertauscht ist, wenn eine Meldung, die ein Bild enthält, direkt vor einer Meldung ohne Bilder gesendet wird. Um die Wahrscheinlichkeit zu minimieren, dass dies geschieht, stellen Sie sicher, dass Ihre Bilder aus einem Content Delivery Network (CDN) stammen, und vermeiden Sie übermäßig große Bilder. In Extremfällen müssen Sie möglicherweise sogar eine ein bis zwei Sekunden lange Verzögerung zwischen der Meldung mit Bild und der nachfolgenden Meldung einfügen. Damit sich diese Verzögerung für den Benutzer etwas natürlicher anfühlt, können Sie **session.sendTyping()** aufrufen, damit vor Beginn der Verzögerung ein Eingabeindikator gesendet wird. 

<!-- 
To learn more about sending a typing indicator, see [How to send a typing indicator](bot-builder-nodejs-send-typing-indicator.md).
-->

Bot Framework implementiert eine Batchverarbeitung, um zu verhindern, dass mehrere Meldungen vom Bot falsch angezeigt werden. <!-- Unfortunately, not all channels can guarantee this. --> Wenn Ihr Bot mehrere Antworten an den Benutzer sendet, werden die einzelnen Meldungen automatisch in Batches gruppiert und dem Benutzer als Gruppe bereitgestellt, um die ursprüngliche Reihenfolge der Meldungen beizubehalten. Bei dieser automatischen Batchverarbeitung wird nach jedem Aufruf von **session.send()** 250 ms gewartet, bevor der nächste Aufruf von **send()** initiiert wird.

Die Verzögerung für die Batchverarbeitung von Meldungen kann konfiguriert werden. Um die Logik für die automatische Batchverarbeitung des SDKs zu deaktivieren, legen Sie für die Standardverzögerung einen größeren Wert fest, und rufen Sie anschließend **sendBatch()** manuell mit einem Rückruf auf, der nach der Übermittlung des Batches aufgerufen wird.

## <a name="send-an-adaptive-card"></a>Senden einer Adaptive Card

Die Adaptive Card kann eine beliebige Kombination von Text, Sprache, Bildern, Schaltflächen und Eingabefeldern enthalten. Adaptive Cards werden mit dem in <a href="http://adaptivecards.io" target="_blank">Adaptive Cards</a> angegebenen JSON-Format erstellt, das Ihnen die vollständige Kontrolle über Inhalt und Format der Karte gibt. 

Nutzen Sie beim Erstellen einer Adaptive Card mit Node.js die Informationen auf der <a href="http://adaptivecards.io" target="_blank">Adaptive Cards</a>-Website, um das Adaptive Card-Schema zu verstehen, Adaptive Card-Elemente zu untersuchen und sich JSON-Beispiele anzusehen, die verwendet werden können, um Karten mit unterschiedlichem Aufbau und unterschiedlicher Komplexität zu erstellen. Darüber hinaus können Sie die interaktive Schnellansicht verwenden, um Adaptive Card-Nutzlasten zu entwerfen und die Kartenausgabe in der Vorschau anzeigen.

Dieses Codebeispiel zeigt, wie Sie eine Nachricht erstellen, die eine adaptive Karte für eine Kalendererinnerung enthält: 

[!code-javascript[Add Adaptive Card attachment](../includes/code/node-send-card-buttons.js#addAdaptiveCardAttachment)]

Die daraus resultierende Karte enthält drei Textblöcke, ein Eingabefeld (Auswahlliste) und drei Schaltflächen:

![Adaptive Karte für eine Kalendererinnerung](../media/adaptive-card-reminder.png)

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Funktionsvorschau mit Channel Inspector][inspector]
* <a href="http://adaptivecards.io" target="_blank">Adaptive Cards</a>
* [AnimationCard][animationCard]
* [AudioCard][audioCard]
* [HeroCard][heroCard]
* [ThumbnailCard][thumbnailCard]
* [ReceiptCard][receiptCard]
* [SignInCard][signinCard]
* [VideoCard][videoCard]
* [Message][Message]
* [Senden von Anlagen](bot-builder-nodejs-send-receive-attachments.md)

[MessageOrder]: bot-builder-nodejs-manage-conversation-flow.md#message-ordering
[Message]: https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.message
[IMessage]: http://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.imessage

[animationCard]: https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.animationcard.html 

[audioCard]: https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.audiocard.html 

[heroCard]: https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.herocard.html

[thumbnailCard]: https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.thumbnailcard.html 

[receiptCard]: https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.receiptcard.html 

[signinCard]: https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.signincard.html 

[videoCard]: https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.videocard.html

[inspector]: ../bot-service-channel-inspector.md
