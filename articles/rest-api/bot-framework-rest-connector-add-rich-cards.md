---
title: Hinzufügen von Rich Card-Anlagen zu Nachrichten | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Nachrichten mithilfe des Bot Connector-Diensts Rich Cards hinzufügen.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 12/13/2017
ms.openlocfilehash: e38bb7ca93c5fc4174d67d1c5ebb0655eef68653
ms.sourcegitcommit: b78fe3d8dd604c4f7233740658a229e85b8535dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2018
ms.locfileid: "49997912"
---
# <a name="add-rich-card-attachments-to-messages"></a>Hinzufügen von Rich Card-Anlagen zu Nachrichten
> [!div class="op_single_selector"]
> - [.NET](../dotnet/bot-builder-dotnet-add-rich-card-attachments.md)
> - [Node.js](../nodejs/bot-builder-nodejs-send-rich-cards.md)
> - [REST](../rest-api/bot-framework-rest-connector-add-rich-cards.md)

Bots und Kanäle tauschen in der Regel Textzeichenfolgen aus, aber einige Kanäle unterstützen auch den Austausch von Anlagen, wodurch Ihr Bot reichhaltigere Nachrichten an Benutzer senden kann. Ihr Bot kann z. B. Rich Cards und Medienanlagen (z. B. Bilder, Videos, Audio, Dateien) senden. In diesem Artikel wird beschrieben, wie Sie mit dem Bot Connector-Dienst Rich Card-Anlagen zu Nachrichten hinzufügen.

> [!NOTE]
> Weitere Informationen zur Vorgehensweise beim Hinzufügen von Medienanlagen zu Nachrichten finden Sie unter [Hinzufügen von Medienanlagen zu Nachrichten](bot-framework-rest-connector-add-media-attachments.md).

## <a id="types-of-cards"></a> Typen von Rich Cards

Rich Cards bestehen aus einem Titel, einer Beschreibung, Links und Bildern. Eine Nachricht kann mehrere Rich Cards enthalten, die entweder im Listen- oder Carousel-Format angezeigt werden.
Bot Framework unterstützt derzeit acht Typen von Rich Cards: 

| Kartentyp | BESCHREIBUNG |
|----|----|
| <a href="/adaptive-cards/get-started/bots">AdaptiveCard</a> | Anpassbare Rich Cards, die eine beliebige Kombination von Text, Sprache, Bildern, Schaltflächen und Eingabefeldern enthalten können. Weitere Informationen finden Sie unter [Unterstützung pro Kanal](/adaptive-cards/get-started/bots#channel-status).  |
| [AnimationCard][animationCard] | Rich Cards, die animierte GIF-Dateien oder kurze Videos abspielen können. |
| [AudioCard][audioCard] | Rich Cards, die eine Audiodatei abspielen können. |
| [HeroCard][heroCard] | Rich Cards, die in der Regel ein einzelnes großes Bild, eine oder mehrere Schaltflächen sowie Text enthalten. |
| [ThumbnailCard][thumbnailCard] | Rich Cards, die in der Regel ein Miniaturbild, eine oder mehrere Schaltflächen sowie Text enthalten. |
| [ReceiptCard][receiptCard] | Rich Cards, die einen Bot enthalten, der Benutzern eine Quittung bereitstellen kann. Sie enthalten in der Regel die Liste der Elemente, die in der Quittung enthalten sein sollen, Steuer- und Gesamtbeträge sowie anderen Text. |
| [SignInCard][signinCard] | Rich Cards, die einen Bot enthalten, der den Benutzer auffordert, sich anzumelden. Sie enthalten in der Regel Text und eine oder mehrere Schaltflächen, auf die der Benutzer klicken kann, um den Anmeldevorgang zu initiieren. |
| [VideoCard][videoCard] | Rich Cards, die Videos abspielen können. |

> [!TIP]
> Informationen dazu, wie Sie den Typ der Rich Card bestimmen, die ein Kanal unterstützt, und wie Sie ermitteln, wie der Kanal Rich Cards wiedergibt, finden Sie unter [Kanalprüfung][ChannelInspector]. In der Dokumentation des Kanals finden Sie Informationen über Beschränkungen des Inhalts der Rich Cards (z.B. die maximale Anzahl der Schaltflächen oder die maximale Länge des Titels).

## <a name="process-events-within-rich-cards"></a>Verarbeiten von Ereignissen auf Rich Cards

Zum Verarbeiten von Ereignissen in Rich Cards verwenden Sie [CardAction][CardAction]-Objekte, um festzulegen, was passieren soll, wenn der Benutzer auf eine Schaltfläche klickt oder auf einen Abschnitt der Rich Card tippt. Jedes [CardAction][CardAction]-Objekt enthält die folgenden Eigenschaften:

| Eigenschaft | Typ | BESCHREIBUNG | 
|----|----|----|
| type | Zeichenfolge | Typ der Aktion (einer der in der folgenden Tabelle angegebenen Werte) |
| title | Zeichenfolge | Titel der Schaltfläche |
| image | Zeichenfolge | Bild-URL für die Schaltfläche |
| value | Zeichenfolge | Wert, der zum Ausführen des angegebenen Aktionstyps erforderlich ist |

> [!NOTE]
> Schaltflächen auf adaptiven Karten werden nicht mit `CardAction`-Objekten, sondern mit dem von <a href="http://adaptivecards.io" target="_blank">adaptiven Karten</a> definierten Schema erstellt. Ein Beispiel, das zeigt, wie Sie Schaltflächen zu einer Adaptive Card hinzufügen, finden Sie unter [Hinzufügen einer Adaptive Card zu einer Nachricht](#adaptive-card).

Diese Tabelle listet die gültigen Werte für die `type` Eigenschaft eine [CardAction][CardAction] Objekt aus, und beschreibt den erwarteten Inhalt der `value` -Eigenschaft für jeden Typ:

| type | value | 
|----|----|
| openUrl | Die URL, die im integrierten Browser geöffnet werden soll. |
| imBack | Der Text der Nachricht, die an den Bot gesendet werden soll (vom Benutzer, der auf die Schaltfläche geklickt oder auf die Karte getippt hat). Diese Nachricht (vom Benutzer an den Bot) ist für alle Unterhaltungsteilnehmer über die Clientanwendung sichtbar, auf der die Unterhaltung gehostet wird. |
| postBack | Der Text der Nachricht, die an den Bot gesendet werden soll (vom Benutzer, der auf die Schaltfläche geklickt oder auf die Karte getippt hat). Einige Clientanwendungen zeigen diesen Text ggf. im Nachrichtenfeed an, wo er für alle Teilnehmer der Unterhaltung sichtbar ist. |
| Aufruf | Ziel für einen Telefonanruf, im folgenden Format: **tel:123123123123**. |
| playAudio | Die URL der Audiodatei, die wiedergegeben werden soll. |
| playVideo | Die URL des Videos, das wiedergegeben werden soll. |
| showImage | Die URL des Bilds, das angezeigt werden soll. |
| downloadFile | Die URL der Datei, die heruntergeladen werden soll. |
| signin | Die URL des OAuth-Flusses, der initiiert werden soll. |

## <a name="add-a-hero-card-to-a-message"></a>Hinzufügen einer Hero Card zu einer Nachricht

Um eine Rich Card-Anlage zu einer Nachricht hinzuzufügen, erstellen Sie zunächst ein Objekt, das dem [Kartentyp](#types-of-cards) entspricht, den Sie zur Nachricht hinzufügen möchten. Erstellen Sie dann ein [Attachment][Attachment]-Objekt, legen Sie seine `contentType`-Eigenschaft auf den Medientyp der Karte und seine `content`-Eigenschaft auf das Objekt fest, das Sie zur Darstellung der Karte erstellt haben. Geben Sie Ihr [Attachment][Attachment]-Objekt innerhalb des `attachments`-Arrays der Nachricht an.

> [!TIP]
> Nachrichten mit Rich Card-Anlagen enthalten in der Regel kein `text`-Element.

Einige Kanäle ermöglichen Ihnen, mehrere Rich Cards zum `attachments`-Array in einer Nachricht hinzuzufügen. Diese Funktion kann in Szenarien nützlich sein, in denen Sie dem Benutzer mehrere Optionen bereitstellen möchten. Wenn Benutzer mit Ihrem Bot z. B. Hotelzimmer buchen können, könnte der Bot dem Benutzer eine Liste mit Rich Cards für die verfügbaren Raumtypen anzeigen. Jede Karte könnte ein Bild und eine Liste der Leistungen gemäß Raumtyp enthalten, und der Benutzer könnte durch Tippen auf eine Karte tippt oder Klicken auf eine Schaltfläche einen Raumtyp auswählen.

> [!TIP]
> Um mehrere Rich Cards im Listenformat anzuzeigen, legen Sie die `attachmentLayout`-Eigenschaft des [Activity][Activity]-Objekts auf „list“ fest. Um mehrere Rich Cards im Karussellformat anzuzeigen, legen Sie die `attachmentLayout`-Eigenschaft des [Activity][Activity]-Objekts auf „carousel“ fest. Wenn der Kanal das Karussellformat nicht unterstützt, werden die Rich Cards im Listenformat angezeigt, auch wenn die `attachmentLayout`-Eigenschaft auf „carousel“ festgelegt ist.

Das folgende Beispiel zeigt eine Anforderung, die eine Nachricht mit einer einzelnen Hero Card-Anlage sendet. In dieser Beispielanforderung stellt `https://smba.trafficmanager.net/apis` den Basis-URI dar. Der Basis-URI für Anforderungen, die Ihr Bot ausgibt, kann ein anderer sein. Weitere Informationen zum Festlegen des Basis-URI finden Sie unter [API-Referenz](bot-framework-rest-connector-api-reference.md#base-uri).

```http
POST https://smba.trafficmanager.net/apis/v3/conversations/abcd1234/activities/5d5cdc723 
Authorization: Bearer ACCESS_TOKEN
Content-Type: application/json
```

```json
{
    "type": "message",
    "from": {
        "id": "12345678",
        "name": "sender's name"
    },
    "conversation": {
        "id": "abcd1234",
        "name": "conversation's name"
    },
    "recipient": {
        "id": "1234abcd",
        "name": "recipient's name"
    },
    "attachments": [
        {
            "contentType": "application/vnd.microsoft.card.hero",
            "content": {
                "title": "title goes here",
                "subtitle": "subtitle goes here",
                "text": "descriptive text goes here",
                "images": [
                    {
                        "url": "http://aka.ms/Fo983c",
                        "alt": "picture of a duck",
                        "tap": {
                            "type": "playAudio",
                            "value": "url to an audio track of a duck call goes here"
                        }
                    }
                ],
                "buttons": [
                    {
                        "type": "playAudio",
                        "title": "Duck Call",
                        "value": "url to an audio track of a duck call goes here"
                    },
                    {
                        "type": "openUrl",
                        "title": "Watch Video",
                        "image": "http://aka.ms/Fo983c",
                        "value": "url goes here of the duck in flight"
                    }
                ]
            }
        }
    ],
    "replyToId": "5d5cdc723"
}
```

## <a id="adaptive-card"></a> Hinzufügen einer Adaptive Card zu einer Nachricht

Adaptive Karten können eine beliebige Kombination von Text, Sprache, Bildern, Schaltflächen und Eingabefeldern enthalten. Adaptive Cards werden mit dem in <a href="http://adaptivecards.io" target="_blank">Adaptive Cards</a> angegebenen JSON-Format erstellt, das Ihnen die vollständige Kontrolle über Inhalt und Format der Karte gibt. 

Nutzen Sie die Informationen auf der <a href="http://adaptivecards.io" target="_blank">Adaptive Cards</a>-Website, um das Adaptive Card-Schema zu verstehen, Adaptive Card-Elemente zu untersuchen und sich JSON-Beispiele anzuschauen, die verwendet werden können, um Karten mit unterschiedlichem Aufbau und unterschiedlicher Komplexität zu erstellen. Darüber hinaus können Sie die interaktive Schnellansicht verwenden, um Adaptive Card-Nutzlasten zu entwerfen und die Kartenausgabe in der Vorschau anzeigen.

Das folgende Beispiel zeigt eine Anforderung, die eine Nachricht mit einer einzelnen Adaptive Card für eine Kalendererinnerung sendet. In dieser Beispielanforderung stellt `https://smba.trafficmanager.net/apis` den Basis-URI dar. Der Basis-URI für Anforderungen, die Ihr Bot ausgibt, kann ein anderer sein. Weitere Informationen zum Festlegen des Basis-URI finden Sie unter [API-Referenz](bot-framework-rest-connector-api-reference.md#base-uri).

```http
POST https://smba.trafficmanager.net/apis/v3/conversations/abcd1234/activities/5d5cdc723 
Authorization: Bearer ACCESS_TOKEN
Content-Type: application/json
```

```json
{
    "type": "message",
    "from": {
        "id": "12345678",
        "name": "sender's name"
    },
    "conversation": {
        "id": "abcd1234",
        "name": "conversation's name"
    },
    "recipient": {
        "id": "1234abcd",
        "name": "recipient's name"
    },
    "attachments": [
        {
            "contentType": "application/vnd.microsoft.card.adaptive",
            "content": {
                "type": "AdaptiveCard",
                "body": [
                    {
                        "type": "TextBlock",
                        "text": "Adaptive Card design session",
                        "size": "large",
                        "weight": "bolder"
                    },
                    {
                        "type": "TextBlock",
                        "text": "Conf Room 112/3377 (10)"
                    },
                    {
                        "type": "TextBlock",
                        "text": "12:30 PM - 1:30 PM"
                    },
                    {
                        "type": "TextBlock",
                        "text": "Snooze for"
                    },
                    {
                        "type": "Input.ChoiceSet",
                        "id": "snooze",
                        "style": "compact",
                        "choices": [
                            {
                                "title": "5 minutes",
                                "value": "5",
                                "isSelected": true
                            },
                            {
                                "title": "15 minutes",
                                "value": "15"
                            },
                            {
                                "title": "30 minutes",
                                "value": "30"
                            }
                        ]
                    }
                ],
                "actions": [
                    {
                        "type": "Action.Http",
                        "method": "POST",
                        "url": "http://foo.com",
                        "title": "Snooze"
                    },
                    {
                        "type": "Action.Http",
                        "method": "POST",
                        "url": "http://foo.com",
                        "title": "I'll be late"
                    },
                    {
                        "type": "Action.Http",
                        "method": "POST",
                        "url": "http://foo.com",
                        "title": "Dismiss"
                    }
                ]
            }
        }
    ],
    "replyToId": "5d5cdc723"
}
```

Die daraus resultierende Karte enthält drei Textblöcke, ein Eingabefeld (Auswahlliste) und drei Schaltflächen:

![Adaptive Karte für eine Kalendererinnerung](../media/adaptive-card-reminder.png)


## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Erstellen von Nachrichten](bot-framework-rest-connector-create-messages.md)
- [Senden und Empfangen von Nachrichten](bot-framework-rest-connector-send-and-receive-messages.md)
- [Hinzufügen von Medienanlagen zu Nachrichten](bot-framework-rest-connector-add-media-attachments.md)
- [Kanalprüfung][ChannelInspector]
- <a href="http://adaptivecards.io" target="_blank">Adaptive Cards</a>

[ChannelInspector]: ../bot-service-channel-inspector.md

[animationCard]: bot-framework-rest-connector-api-reference.md#animationcard-object
[audioCard]: bot-framework-rest-connector-api-reference.md#audiocard-object
[heroCard]: bot-framework-rest-connector-api-reference.md#herocard-object
[thumbnailCard]: bot-framework-rest-connector-api-reference.md#thumbnailcard-object
[receiptCard]: bot-framework-rest-connector-api-reference.md#receiptcard-object
[signinCard]: bot-framework-rest-connector-api-reference.md#signincard-object
[videoCard]: bot-framework-rest-connector-api-reference.md#videocard-object

[CardAction]: bot-framework-rest-connector-api-reference.md#cardaction-object
[Activity]: bot-framework-rest-connector-api-reference.md#activity-object
[Attachment]: bot-framework-rest-connector-api-reference.md#attachment-object