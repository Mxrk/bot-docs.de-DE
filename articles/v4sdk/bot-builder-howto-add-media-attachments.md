---
title: Hinzufügen von Medien zu Nachrichten | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie mit dem Bot Framework SDK Nachrichten Medien hinzufügen.
keywords: Medien, Nachrichten, Bilder, Audio, Video, Dateien, MessageFactory, Rich Cards, Meldungen, Adaptive Cards, Hero-Karte, Vorgeschlagene Aktionen
author: ivorb
ms.author: v-ivorb
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 12/17/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 1ea9daeb35033e49232d64bfe98a223807dabf75
ms.sourcegitcommit: b94361234816e6b95459f142add936732fc40344
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/15/2019
ms.locfileid: "54317600"
---
# <a name="add-media-to-messages"></a>Hinzufügen von Medien zu Nachrichten

[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

Nachrichten, die zwischen Benutzer und Bot ausgetauscht werden, können Medienanlagen enthalten, z.B. Bilder, Videos, Audio und Dateien. Das Bot Framework SDK unterstützt die Aufgabe zum Senden von umfassenden Nachrichten an den Benutzer. Um den Typ der umfassenden Nachrichten zu ermitteln, die von einem Kanal (Facebook, Skype, Slack usw.) unterstützt werden, können Sie in der Dokumentation zum jeweiligen Kanal nach Informationen zu Einschränkungen suchen. Eine Liste mit den verfügbaren Karten (Cards) finden Sie unter [Entwerfen der Benutzeroberfläche](../bot-service-design-user-experience.md). 

## <a name="send-attachments"></a>Senden von Anlagen

Um Benutzerinhalte wie Bilder oder Videos zu senden, können Sie einer Nachricht eine Anlage oder eine Liste von Anlagen hinzufügen.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Die `Attachments`-Eigenschaft des `Activity`-Objekts enthält ein Array von `Attachment`-Objekten, die die an die Nachricht angehängten Medienanlagen und Rich Cards darstellen. Um einer Nachricht eine Medienanlage hinzuzufügen, erstellen Sie ein `Attachment`-Objekt für die Aktivität `message`, und legen Sie die Eigenschaften `ContentType`, `ContentUrl` und `Name` fest.
Der hier gezeigte Quellcode basiert auf dem Beispiel unter [Handling Attachments](https://aka.ms/bot-attachments-sample-code) (Behandeln von Anlagen). 

```csharp
using Microsoft.Bot.Builder;
using Microsoft.Bot.Builder.Core.Extensions;
using Microsoft.Bot.Schema;

var reply = turnContext.Activity.CreateReply();

// Create an attachment.
var attachment = new Attachment
    {
        ContentUrl = "imageUrl.png",
        ContentType = "image/png",
        Name = "imageName",
    };

// Add the attachment to our reply.
reply.Attachments = new List<Attachment>() { attachment };

// Send the activity to the user.
await turnContext.SendActivityAsync(reply, cancellationToken);
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Der hier gezeigte Quellcode basiert auf dem Beispiel unter [JS Handling Attachments](https://aka.ms/bot-attachments-sample-code-js) (Behandeln von Anlagen mit JavaScript).
Um dem Benutzer einen einzelnen Inhalt wie ein Bild oder ein Video zu senden, können Sie Medien senden, die in einer URL enthalten sind:

```javascript
const { ActionTypes, ActivityTypes, CardFactory } = require('botbuilder');

// Call function to get an attachment.
const reply = { type: ActivityTypes.Message };
reply.attachments = [this.getInternetAttachment()];
reply.text = 'This is an internet attachment.';
// Send the activity to the user.
await turnContext.sendActivity(reply);

/* function getInternetAttachment - Returns an attachment to be sent to the user from a HTTPS URL */
getInternetAttachment() {
        return {
            name: 'imageName.png',
            contentType: 'image/png',
            contentUrl: 'imageUrl.png'}
}
```

---

Wenn eine Anlage ein Bild, eine Audiodatei oder ein Video ist, übermittelt der Connector Anlagedaten an den Kanal so, dass der [Kanal](bot-builder-channeldata.md) diese Anlage in der Unterhaltung rendert. Wenn es sich bei der Anlage um eine Datei handelt, wird die Datei-URL als Link in der Unterhaltung gerendert.

## <a name="send-a-hero-card"></a>Senden einer Hero-Karte

Neben einfachen Bild- oder Videoanlagen können Sie auch eine **Hero-Karte** anhängen, mit der Sie Bilder und Schaltflächen in einem Objekt kombinieren und an Benutzer senden können.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Um eine Nachricht mit einer Hero-Karte und einer Schaltfläche zu verfassen, können Sie an eine Nachricht ein `HeroCard`-Element anfügen. Der hier gezeigte Quellcode basiert auf dem Beispiel unter [Handling Attachments](https://aka.ms/bot-attachments-sample-code) (Behandeln von Anlagen). 

```csharp
using Microsoft.Bot.Builder;
using Microsoft.Bot.Builder.Core.Extensions;
using Microsoft.Bot.Schema;

var reply = turnContext.Activity.CreateReply();

// Create a HeroCard with options for the user to choose to interact with the bot.
var card = new HeroCard
{
    Text = "You can upload an image or select one of the following choices",
    Buttons = new List<CardAction>()
    {
        new CardAction(ActionTypes.ImBack, title: "1. Inline Attachment", value: "1"),
        new CardAction(ActionTypes.ImBack, title: "2. Internet Attachment", value: "2"),
        new CardAction(ActionTypes.ImBack, title: "3. Uploaded Attachment", value: "3"),
    },
};

// Add the card to our reply.
reply.Attachments = new List<Attachment>() { card.ToAttachment() };

await turnContext.SendActivityAsync(reply, cancellationToken);
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Um eine Nachricht mit einer Hero-Karte und einer Schaltfläche zu verfassen, können Sie an eine Nachricht ein `HeroCard`-Element anfügen. Der hier gezeigte Quellcode basiert auf dem Beispiel unter [JS Handling Attachments](https://aka.ms/bot-attachments-sample-code-js) (Behandeln von Anlagen mit JavaScript):

```javascript
const { ActionTypes, ActivityTypes, CardFactory } = require('botbuilder');
// build buttons to display.
const buttons = [
            { type: ActionTypes.ImBack, title: '1. Inline Attachment', value: '1' },
            { type: ActionTypes.ImBack, title: '2. Internet Attachment', value: '2' },
            { type: ActionTypes.ImBack, title: '3. Uploaded Attachment', value: '3' }
];

// construct hero card.
const card = CardFactory.heroCard('', undefined,
buttons, { text: 'You can upload an image or select one of the following choices.' });

// add card to Activity.
const reply = { type: ActivityTypes.Message };
reply.attachments = [card];

// Send hero card to the user.
await turnContext.sendActivity(reply);
```

---

## <a name="process-events-within-rich-cards"></a>Verarbeiten von Ereignissen auf Rich Cards

Zum Verarbeiten von Ereignissen in Rich Cards verwenden Sie Objekte vom Typ _Kartenaktion_, um festzulegen, was passieren soll, wenn der Benutzer auf eine Schaltfläche klickt oder auf einen Abschnitt der Rich Card tippt. Jede Kartenaktion hat einen _Typ_ und einen _Wert_.

Weisen Sie für die korrekte Funktionsweise jedem anklickbaren Element auf der Karte einen Aktionstyp zu. In der folgenden Tabelle werden die verfügbaren Aktionstypen aufgeführt und erläutert. Außerdem ist jeweils angegeben, was die zugeordnete Werteigenschaft enthalten muss.

| Typ | BESCHREIBUNG | Wert |
| :---- | :---- | :---- |
| openUrl | Öffnet eine URL im integrierten Browser. | Die zu öffnende URL. |
| imBack | Sendet eine Nachricht an den Bot und veröffentlicht eine sichtbare Antwort im Chat. | Text der zu sendenden Nachricht. |
| postBack | Sendet eine Nachricht an den Bot und veröffentlicht ggf. keine sichtbare Antwort im Chat. | Text der zu sendenden Nachricht. |
| Aufruf | Initiiert einen Telefonanruf. | Ziel für den Telefonanruf im folgenden Format: `tel:123123123123`. |
| playAudio | Gibt Audio wieder. | Die URL des wiederzugebenden Audios. |
| playVideo | Gibt ein Video wieder. | Die URL des wiederzugebenden Videos. |
| showImage | Zeigt ein Bild an. | Die URL des anzuzeigenden Bilds. |
| downloadFile | Lädt eine Datei herunter. | Die URL der herunterzuladenden Bilds. |
| signin | Initiiert einen OAuth-Anmeldevorgang. | Die URL des zu initiierenden OAuth-Flusses. |

## <a name="hero-card-using-various-event-types"></a>Verwenden einer Hero-Karte mit verschiedenen Ereignistypen

Der folgende Code zeigt Beispiele für verschiedene Rich Card-Ereignisse.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
using Microsoft.Bot.Builder;
using Microsoft.Bot.Builder.Core.Extensions;
using Microsoft.Bot.Schema;

var reply = turnContext.Activity.CreateReply();

var card = new HeroCard
{
    Buttons = new List<CardAction>()
    {
        new CardAction(title: "Much Quieter", type: ActionTypes.PostBack, value: "Shh! My Bot friend hears me."),
        new CardAction(ActionTypes.OpenUrl, title: "Azure Bot Service", value: "https://azure.microsoft.com/en-us/services/bot-service/"),
    },
};

```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
const {ActionTypes} = require("botbuilder");

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
Adaptive Karten und MessageFactory werden verwendet, um zur Kommunikation mit Benutzern Rich-Media-Nachrichten mit Texten, Bildern, Video, Audio und Dateien zu senden. Es gibt jedoch einige Unterschiede zwischen diesen beiden Formaten. 

Erstens unterstützen nur einige Kanäle adaptive Karten. Kanäle, bei denen dies nicht der Fall ist, unterstützen adaptive Karten möglicherweise teilweise. Wenn Sie beispielsweise eine adaptive Karte in Facebook senden, funktionieren die Schaltflächen nicht, während die Texte und Bilder einwandfrei funktionieren. MessageFactory ist lediglich eine Hilfsklasse innerhalb des Bot Framework SDK, die zum Automatisieren von Erstellungsschritten dient und von den meisten Kanälen unterstützt wird. 

Zweitens übermitteln adaptive Karten Nachrichten im Kartenformat, und der Kanal bestimmt das Layout der Karte. Das Format der Nachrichten von MessageFactory hängt vom Kanal ab, und sofern die adaptive Karte nicht Teil der Anlage ist, wird nicht unbedingt das Kartenformat verwendet. 

Die neuesten Informationen zur Unterstützung adaptiver Karten durch Kanäle finden Sie unter <a href="http://adaptivecards.io/visualizer/">Adaptive Karten – Schnellansicht</a> (in englischer Sprache).

Fügen Sie das NuGet-Paket `Microsoft.AdaptiveCards` hinzu, um adaptive Karten zu verwenden. 


> [!NOTE]
> Sie sollten diese Funktion mit den Kanälen testen, die Ihr Bot verwenden wird, um festzustellen, ob diese Kanäle adaptive Karten unterstützen.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Der hier gezeigte Quellcode basiert auf dem Beispiel unter [Using Adaptive Cards](https://aka.ms/bot-adaptive-cards-sample-code) (Verwenden von Adaptive Cards):

```csharp
using AdaptiveCards;
using Microsoft.Bot.Builder;
using Microsoft.Bot.Builder.Core.Extensions;
using Microsoft.Bot.Schema;

// Creates an attachment that contains an adaptive card
// filePath is the path to JSON file
private static Attachment CreateAdaptiveCardAttachment(string filePath)
{
    var adaptiveCardJson = File.ReadAllText(filePath);
    var adaptiveCardAttachment = new Attachment()
    {
        ContentType = "application/vnd.microsoft.card.adaptive",
        Content = JsonConvert.DeserializeObject(adaptiveCardJson),
    };
    return adaptiveCardAttachment;
}

// Create adaptive card and attach it to the message 
var cardAttachment = CreateAdaptiveCardAttachment(adaptiveCardJsonFilePath);
var reply = turnContext.Activity.CreateReply();
reply.Attachments = new List<Attachment>() { cardAttachment };

await turnContext.SendActivityAsync(reply, cancellationToken);
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Der hier gezeigte Quellcode basiert auf dem Beispiel unter [JS Using Adaptive Cards](https://aka.ms/bot-adaptive-cards-js-sample-code) (Verwenden von Adaptive Cards mit JavaScript):

```javascript
const { BotFrameworkAdapter } = require('botbuilder');

// Import AdaptiveCard content.
const FlightItineraryCard = require('./resources/FlightItineraryCard.json');
const ImageGalleryCard = require('./resources/ImageGalleryCard.json');
const LargeWeatherCard = require('./resources/LargeWeatherCard.json');
const RestaurantCard = require('./resources/RestaurantCard.json');
const SolitaireCard = require('./resources/SolitaireCard.json');

// Create array of AdaptiveCard content, this will be used to send a random card to the user.
const CARDS = [
    FlightItineraryCard,
    ImageGalleryCard,
    LargeWeatherCard,
    RestaurantCard,
    SolitaireCard
];
// Select a random card to send.
const randomlySelectedCard = CARDS[Math.floor((Math.random() * CARDS.length - 1) + 1)];
// Send adaptive card.
await context.sendActivity({
      text: 'Here is an Adaptive Card:',
       attachments: [CardFactory.adaptiveCard(randomlySelectedCard)]
});
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
const {MessageFactory, CardFactory} = require('botbuilder');

//  init message object
let messageWithCarouselOfCards = MessageFactory.carousel([
    CardFactory.heroCard('title1', ['imageUrl1'], ['button1']),
    CardFactory.heroCard('title2', ['imageUrl2'], ['button2']),
    CardFactory.heroCard('title3', ['imageUrl3'], ['button3'])
]);

await context.sendActivity(messageWithCarouselOfCards);
```

---

<!-- TODO: Add a media card, such as video or audion. Revisit which examples we put here and link to the 06 through 08 samples. -->

## <a name="additional-resources"></a>Zusätzliche Ressourcen

Ausführliche Informationen zum Kartenschema finden Sie im [Bot Framework-Kartenschema](https://aka.ms/botSpecs-cardSchema).

Beispielcode finden Sie hier: Für Karten: [C#](https://aka.ms/bot-cards-sample-code)/[JS](https://aka.ms/bot-cards-js-sample-code). Für adaptive Karten: [C#](https://aka.ms/bot-adaptive-cards-sample-code)/[JS](https://aka.ms/bot-adaptive-cards-js-sample-code). Für Anlagen: [C#](https://aka.ms/bot-attachments-sample-code)/[JS](https://aka.ms/bot-attachments-sample-code-js). Für vorgeschlagene Aktionen: [C#](https://aka.ms/SuggestedActionsCSharp)/[JS](https://aka.ms/SuggestedActionsJS).
Weitere Beispiele finden Sie im Repository mit den Bot Builder-Beispielen auf [GitHub](https://aka.ms/bot-samples-readme).
