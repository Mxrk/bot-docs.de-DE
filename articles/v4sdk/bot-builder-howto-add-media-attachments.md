---
title: Hinzufügen von Medien zu Nachrichten | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie mit dem Bot Builder SDK Nachrichten Medien hinzufügen.
keywords: Medien, Nachrichten, Bilder, Audio, Video, Dateien, Nachrichtenfactory, Rich-Media-Nachrichten
author: ivorb
ms.author: v-ivorb
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 04/03/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: e1b25a3b5c090cbb13b4c27279745a81da64e6c4
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39302576"
---
# <a name="add-media-to-messages"></a>Hinzufügen von Medien zu Nachrichten

[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

Ein Nachrichtenaustausch zwischen Benutzer und Bot kann Medienanlagen enthalten, z.B. Bilder, Video, Audio und Dateien. Das Bot Builder SDK enthält eine neue Klasse `Microsoft.Bot.Builder.MessageFactory`, die das Senden von Rich-Media-Nachrichten an den Benutzer erleichtert.

## <a name="send-attachments"></a>Senden von Anlagen

Um Benutzerinhalte wie Bilder oder Videos zu senden, können Sie einer Nachricht eine Anlage oder eine Liste von Anlagen hinzufügen.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Die `Attachments`-Eigenschaft des `Activity`-Objekts enthält ein Array von `Attachment`-Objekten, die die an die Nachricht angehängten Medienanlagen und Rich Cards darstellen. Um einer Nachricht eine Medienanlage hinzuzufügen, erstellen Sie ein `Attachment`-Objekt für die Aktivität `message`, und legen Sie die Eigenschaften `ContentType`, `ContentUrl` und `Name` fest. 

```csharp
using Microsoft.Bot.Builder;
using Microsoft.Bot.Builder.Core.Extensions;
using Microsoft.Bot.Schema;

// Create the activity and add an attachment.
var activity = MessageFactory.Attachment(
    new Attachment { ContentUrl = "imageUrl.png", ContentType = "image/png" }
);

// Send the activity to the user.
await context.SendActivity(activity);
```

Die Methode `Attachment` der Nachrichtenfactory kann auch eine Liste von Anlagen senden, die übereinander gestapelt sind.

```csharp
using Microsoft.Bot.Builder;
using Microsoft.Bot.Builder.Core.Extensions;
using Microsoft.Bot.Schema;

// Create the activity and add an attachment.
var activity = MessageFactory.Attachment(new Attachment[]
{
    new Attachment { ContentUrl = "imageUrl1.png", ContentType = "image/png" },
    new Attachment { ContentUrl = "imageUrl2.png", ContentType = "image/png" },
    new Attachment { ContentUrl = "imageUrl3.png", ContentType = "image/png" }
});

// Send the activity to the user.
await context.SendActivity(activity);
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Um dem Benutzer einen einzelnen Inhalt wie ein Bild oder ein Video zu senden, können Sie Medien senden, die in einer URL enthalten sind:

```javascript
const {MessageFactory} = require('botbuilder');
let imageOrVideoMessage = MessageFactory.contentUrl('imageUrl.png', 'image/jpeg')

// Send the activity to the user.
await context.sendActivity(imageOrVideoMessage);
```

So senden Sie eine Liste von Anlagen, die übereinander gestapelt sind: <!-- TODO: Convert the hero cards to image attachments in this example. -->

```javascript
// require MessageFactory and CardFactory from botbuilder.
const {MessageFactory} = require('botbuilder');
const {CardFactory} = require('botbuilder');

let messageWithCarouselOfCards = MessageFactory.list([
    CardFactory.heroCard('title1', ['imageUrl1'], ['button1']),
    CardFactory.heroCard('title2', ['imageUrl2'], ['button2']),
    CardFactory.heroCard('title3', ['imageUrl3'], ['button3'])
]);

await context.sendActivity(messageWithCarouselOfCards);
```

---

Wenn eine Anlage ein Bild, eine Audiodatei oder ein Video ist, übermittelt der Connector Anlagedaten an den Kanal so, dass der [Kanal](~/v4sdk/bot-builder-channeldata.md) diese Anlage in der Unterhaltung rendert. Wenn es sich bei der Anlage um eine Datei handelt, wird die Datei-URL als Link in der Unterhaltung gerendert.

## <a name="send-a-hero-card"></a>Senden einer Hero-Karte

Neben einfachen Bild- oder Videoanlagen können Sie auch eine **Hero-Karte** anhängen, mit der Sie Bilder und Schaltflächen in einem Objekt kombinieren und an Benutzer senden können.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Um eine Nachricht mit einer Hero-Karte und einer Schaltfläche zu verfassen, können Sie einer Nachricht `HeroCard` anhängen:

```csharp
using Microsoft.Bot.Builder;
using Microsoft.Bot.Builder.Core.Extensions;
using Microsoft.Bot.Schema;

// Create the activity and attach a Hero card.
var activity = MessageFactory.Attachment(
    new HeroCard(
        title: "White T-Shirt",
        images: new CardImage[] { new CardImage(url: "imageUrl.png") },
        buttons: new CardAction[]
        {
            new CardAction(title: "buy", type: ActionTypes.ImBack, value: "buy")
        })
    .ToAttachment());

// Send the activity as a reply to the user.
await context.SendActivity(activity);
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Um dem Benutzer eine Karte und eine Schaltfläche zu senden, fügen Sie der Nachricht `heroCard` hinzu:

```javascript
// require MessageFactory and CardFactory from botbuilder.
const {MessageFactory} = require('botbuilder');
const {CardFactory} = require('botbuilder');

const message = MessageFactory.attachment(
     CardFactory.heroCard(
        'White T-Shirt',
        ['https://example.com/whiteShirt.jpg'],
        ['buy']
    )
 );

await context.sendActivity(message);
```

---

<!--Lifted from the RESP API documentation-->

Rich Cards bestehen aus einem Titel, einer Beschreibung, Links und Bildern. Eine Nachricht kann mehrere Rich Cards enthalten, die entweder im Listen- oder Karussellformat angezeigt werden. Das Bot Builder SDK unterstützt derzeit eine Vielzahl von Rich Cards. Eine Liste der Rich Cards und Kanäle, in denen diese unterstützt werden, finden Sie unter [Entwerfen von UX-Elementen](../bot-service-design-user-experience.md).

> [!TIP]
> Informationen dazu, wie Sie den Typ der Rich Card bestimmen, die ein Kanal unterstützt, und wie Sie ermitteln, wie der Kanal Rich Cards wiedergibt, finden Sie unter [Channel Inspector](https://docs.microsoft.com/en-us/azure/bot-service/bot-service-channel-inspector?view=azure-bot-service-4.0). In der Dokumentation des Kanals finden Sie Informationen zu Inhaltsbeschränkungen von Rich Cards (z.B. die maximale Anzahl von Schaltflächen oder die maximale Titellänge).

## <a name="process-events-within-rich-cards"></a>Verarbeiten von Ereignissen in Rich Cards

Zum Verarbeiten von Ereignissen in Rich Cards verwenden Sie Objekte vom Typ _Kartenaktion_, um festzulegen, was passieren soll, wenn der Benutzer auf eine Schaltfläche klickt oder auf einen Abschnitt der Rich Card tippt.

Weisen Sie für die korrekte Funktionsweise jedem anklickbaren Element auf der Karte einen Aktionstyp zu. Diese Tabelle listet die gültigen Werte für die Typeigenschaft eines Objekts vom Typ „Kartenaktion“ aus, und beschreibt den erwarteten Inhalt der Werteigenschaft für jeden Typ.

| Typ | Wert |
| :---- | :---- |
| openUrl | Die URL, die im integrierten Browser geöffnet werden soll. Reagiert auf Tippen oder Klicken durch Öffnen der URL. |
| imBack | Der Text der Nachricht, die an den Bot gesendet werden soll (vom Benutzer, der auf die Schaltfläche geklickt oder auf die Karte getippt hat). Diese Nachricht (vom Benutzer an den Bot) ist für alle Teilnehmer der Unterhaltung über die Clientanwendung sichtbar, auf der die Unterhaltung gehostet wird. |
| postBack | Der Text der Nachricht, die an den Bot gesendet werden soll (vom Benutzer, der auf die Schaltfläche geklickt oder auf die Karte getippt hat). Einige Clientanwendungen zeigen diesen Text unter Umständen im Nachrichtenfeed an, wo er für alle Teilnehmer der Unterhaltung sichtbar ist. |
| Aufruf | Ziel für einen Telefonanruf in diesem Format: `tel:123123123123`. Reagiert auf Tippen oder Klicken durch Einleiten eines Anrufs.|
| playAudio | Die URL der Audiodatei, die wiedergegeben werden soll. Reagiert auf Tippen oder Klicken durch Wiedergabe der Audiodatei. |
| playVideo | Die URL des Videos, das wiedergegeben werden soll. Reagiert auf Tippen oder Klicken durch Wiedergabe des Videos. |
| showImage | Die URL des Bilds, das angezeigt werden soll. Reagiert auf Tippen oder Klicken durch Anzeigen des Bilds. |
| downloadFile | Die URL der Datei, die heruntergeladen werden soll.  Reagiert auf Tippen oder Klicken durch Herunterladen der Datei. |
| signin | Die URL des OAuth-Flusses, der initiiert werden soll. Regiert auf Tippen oder Klicken durch Initiieren der Anmeldung. |

## <a name="hero-card-using-various-event-types"></a>Verwenden einer Hero-Karte mit verschiedenen Ereignistypen

Der folgende Code zeigt Beispiele für verschiedene Rich Card-Ereignisse.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
using Microsoft.Bot.Builder;
using Microsoft.Bot.Builder.Core.Extensions;
using Microsoft.Bot.Schema;

// Create the activity and attach a Hero card.
var activity = MessageFactory.Attachment(
    new HeroCard(
        title: "Holler Back Buttons",
        images: new CardImage[] { new CardImage(url: "imageUrl.png") },
        buttons: new CardAction[]
        {
            new CardAction(title: "Shout Out Loud", type: ActionTypes.imBack, value: "You can ALL hear me!"),
            new CardAction(title: "Much Quieter", type: ActionTypes.postBack, value: "Shh! My Bot friend hears me."),
            new CardAction(title: "Show me how to Holler", type: ActionTypes.openURL, value: $"https://en.wikipedia.org/wiki/{cardContent.Key}")
        })
    .ToAttachment());

// Send the activity as a reply to the user.
await context.SendActivity(activity);
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
const {ActionTypes} = require("botbuilder");
```

```javascript
const hero = MessageFactory.attachment(
    CardFactory.heroCard(
        'Holler Back Buttons',
        ['https://example.com/whiteShirt.jpg'],
        [{
            type: ActionTypes.ImBack,
            title: 'ImBack',
            value: 'You can ALL hear me! Shout Out Loud'
        },
        {
            type: ActionTypes.PostBack,
            title: 'PostBack',
            value: 'Shh! My Bot friend hears me. Much Quieter'
        },
        {
            type: ActionTypes.OpenUrl,
            title: 'OpenUrl',
            value: 'https://en.wikipedia.org/wiki/{cardContent.Key}'
        }]
    )
);

await context.sendActivity(hero);

```

---

## <a name="send-an-adaptive-card"></a>Senden adaptiver Karten

Sie können auch eine adaptive Karte als Anlage senden. Derzeit unterstützen nicht alle Kanäle adaptive Karten. Die neuesten Informationen zur Unterstützung adaptiver Karten durch Kanäle finden Sie unter <a href="http://adaptivecards.io/visualizer/">Adaptive Karten – Schnellansicht</a> (in englischer Sprache).

# <a name="ctabcsharp"></a>[C#](#tab/csharp)
Fügen Sie das NuGet-Paket `Microsoft.AdaptiveCards` hinzu, um adaptive Karten zu verwenden.


> [!NOTE]
> Sie sollten diese Funktion mit den Kanälen testen, die Ihr Bot verwenden wird, um festzustellen, ob diese Kanäle adaptive Karten unterstützen.

```csharp
using AdaptiveCards;
using Microsoft.Bot.Builder;
using Microsoft.Bot.Builder.Core.Extensions;
using Microsoft.Bot.Schema;

// Create the activity and attach an Adaptive Card.
var card = new AdaptiveCard();
card.Body.Add(new TextBlock()
{
    Text = "<title>",
    Size = TextSize.Large,
    Wrap = true,
    Weight = TextWeight.Bolder
});
card.Body.Add(new TextBlock() { Text = "<message text>", Wrap = true });
card.Body.Add(new TextInput()
{
    Id = "Title",
    Value = string.Empty,
    Style = TextInputStyle.Text,
    Placeholder = "Title",
    IsRequired = true,
    MaxLength = 50
});
card.Actions.Add(new SubmitAction() { Title = "Submit", DataJson = "{ Action:'Submit' }" });
card.Actions.Add(new SubmitAction() { Title = "Cancel", DataJson = "{ Action:'Cancel'}" });

var activity = MessageFactory.Attachment(new Attachment(AdaptiveCard.ContentType, content: card));

// Send the activity as a reply to the user.
await context.SendActivity(activity);
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
const {CardFactory} = require("botbuilder");

const message = CardFactory.adaptiveCard({
    "$schema": "http://adaptivecards.io/schemas/adaptive-card.json",
    "version": "1.0",
    "type": "AdaptiveCard",
    "speak": "Your flight is confirmed for you from San Francisco to Amsterdam on Friday, October 10 8:30 AM",
    "body": [
        {
            "type": "TextBlock",
            "text": "Passenger",
            "weight": "bolder",
            "isSubtle": false
        },
        {
            "type": "TextBlock",
            "text": "Sarah Hum",
            "separator": true
        },
        {
            "type": "TextBlock",
            "text": "1 Stop",
            "weight": "bolder",
            "spacing": "medium"
        },
        {
            "type": "TextBlock",
            "text": "Fri, October 10 8:30 AM",
            "weight": "bolder",
            "spacing": "none"
        },
        {
            "type": "ColumnSet",
            "separator": true,
            "columns": [
                {
                    "type": "Column",
                    "width": 1,
                    "items": [
                        {
                            "type": "TextBlock",
                            "text": "San Francisco",
                            "isSubtle": true
                        },
                        {
                            "type": "TextBlock",
                            "size": "extraLarge",
                            "color": "accent",
                            "text": "SFO",
                            "spacing": "none"
                        }
                    ]
                },
                {
                    "type": "Column",
                    "width": "auto",
                    "items": [
                        {
                            "type": "TextBlock",
                            "text": " "
                        },
                        {
                            "type": "Image",
                            "url": "http://messagecardplayground.azurewebsites.net/assets/airplane.png",
                            "size": "small",
                            "spacing": "none"
                        }
                    ]
                },
                {
                    "type": "Column",
                    "width": 1,
                    "items": [
                        {
                            "type": "TextBlock",
                            "horizontalAlignment": "right",
                            "text": "Amsterdam",
                            "isSubtle": true
                        },
                        {
                            "type": "TextBlock",
                            "horizontalAlignment": "right",
                            "size": "extraLarge",
                            "color": "accent",
                            "text": "AMS",
                            "spacing": "none"
                        }
                    ]
                }
            ]
        },
        {
            "type": "ColumnSet",
            "spacing": "medium",
            "columns": [
                {
                    "type": "Column",
                    "width": "1",
                    "items": [
                        {
                            "type": "TextBlock",
                            "text": "Total",
                            "size": "medium",
                            "isSubtle": true
                        }
                    ]
                },
                {
                    "type": "Column",
                    "width": 1,
                    "items": [
                        {
                            "type": "TextBlock",
                            "horizontalAlignment": "right",
                            "text": "$1,032.54",
                            "size": "medium",
                            "weight": "bolder"
                        }
                    ]
                }
            ]
        }
    ]
});

// send adaptive card as attachment 
await context.sendActivity({ attachments: [message] })
```

---

## <a name="send-a-carousel-of-cards"></a>Senden eines Kartenkarussells

Nachrichten können auch mehrere Anlagen im Karusselllayout enthalten. Die Anlagen werden nebeneinander platziert und der Benutzer kann zwischen ihnen wechseln.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
using Microsoft.Bot.Builder;
using Microsoft.Bot.Builder.Core.Extensions;
using Microsoft.Bot.Schema;

// Create the activity and attach a set of Hero cards.
var activity = MessageFactory.Carousel(
    new Attachment[]
    {
        new HeroCard(
            title: "title1",
            images: new CardImage[] { new CardImage(url: "imageUrl1.png") },
            buttons: new CardAction[]
            {
                new CardAction(title: "button1", type: ActionTypes.ImBack, value: "item1")
            })
        .ToAttachment(),
        new HeroCard(
            title: "title2",
            images: new CardImage[] { new CardImage(url: "imageUrl2.png") },
            buttons: new CardAction[]
            {
                new CardAction(title: "button2", type: ActionTypes.ImBack, value: "item2")
            })
        .ToAttachment(),
        new HeroCard(
            title: "title3",
            images: new CardImage[] { new CardImage(url: "imageUrl3.png") },
            buttons: new CardAction[]
            {
                new CardAction(title: "button3", type: ActionTypes.ImBack, value: "item3")
            })
        .ToAttachment()
    });

// Send the activity as a reply to the user.
await context.SendActivity(activity);
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
// require MessageFactory and CardFactory from botbuilder.
const {MessageFactory} = require('botbuilder');
const {CardFactory} = require('botbuilder');

//  init message object
let messageWithCarouselOfCards = MessageFactory.carousel([
    CardFactory.heroCard('title1', ['imageUrl1'], ['button1']),
    CardFactory.heroCard('title2', ['imageUrl2'], ['button2']),
    CardFactory.heroCard('title3', ['imageUrl3'], ['button3'])
]);

await context.sendActivity(messageWithCarouselOfCards);
```

---

## <a name="additional-resources"></a>Zusätzliche Ressourcen

[Funktionsvorschau mit Channel Inspector](../bot-service-channel-inspector.md)

---