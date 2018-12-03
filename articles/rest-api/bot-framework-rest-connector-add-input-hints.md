---
title: Hinzufügen von Eingabehinweisen zu Nachrichten | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Nachrichten mithilfe des Bot Connector-Diensts Eingabehinweise zu Nachrichten hinzufügen.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 12/13/2017
ms.openlocfilehash: cee7e79190d967590296ccbcfec7a112f2ae8588
ms.sourcegitcommit: b78fe3d8dd604c4f7233740658a229e85b8535dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2018
ms.locfileid: "49998582"
---
# <a name="add-input-hints-to-messages"></a>Hinzufügen von Eingabehinweisen zu Nachrichten
> [!div class="op_single_selector"]
> - [.NET](../dotnet/bot-builder-dotnet-add-input-hints.md)
> - [Node.js](../nodejs/bot-builder-nodejs-send-input-hints.md)
> - [REST](../rest-api/bot-framework-rest-connector-add-input-hints.md)

Durch die Angabe eines Eingabehinweises für eine Nachricht können Sie angeben, ob Ihr Bot Benutzereingaben akzeptiert, erwartet oder ignoriert, nachdem die Nachricht an den Client gesendet wurde. Bei vielen Kanälen können Clients dadurch den Zustand von Benutzereingabe-Steuerelementen entsprechend festlegen. Wenn beispielsweise der Eingabehinweis für eine Nachricht angibt, dass der Bot die Benutzereingabe ignoriert, kann der Client das Mikrofon schließen und das Eingabefeld deaktivieren, um die Eingabe durch den Benutzer zu verhindern.

## <a name="accepting-input"></a>Eingabe wird akzeptiert

Um anzugeben, dass Ihr Bot passiv für eine Eingabe bereit ist, aber keine Antwort vom Benutzer erwartet, legen Sie im [Activity][Activity]-Objekt, das Ihre Nachricht darstellt, die Eigenschaft `inputHint` auf **acceptingInput** fest. Bei vielen Kanälen wird dadurch das Eingabefeld des Clients aktiviert und das Mikrofon geschlossen, das aber weiterhin für den Benutzer verfügbar ist. Cortana öffnet beispielsweise das Mikrofon, um Eingaben vom Benutzer anzunehmen, wenn der Benutzer die Mikrofontaste gedrückt hält. 

Das folgende Beispiel zeigt eine Anforderung, die eine Nachricht sendet, und gibt an, dass der Bot eine Eingabe akzeptiert. In dieser Beispielanforderung stellt `https://smba.trafficmanager.net/apis` den Basis-URI dar. Der Basis-URI für Anforderungen, die Ihr Bot ausgibt, kann ein anderer sein. Weitere Informationen zum Festlegen des Basis-URI finden Sie unter [API-Referenz](bot-framework-rest-connector-api-reference.md#base-uri).

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
    "text": "Here's a picture of the house I was telling you about.",
    "inputHint": "acceptingInput",
    "replyToId": "5d5cdc723"
}
```

## <a name="expecting-input"></a>Eingabe wird erwartet

Um anzugeben, dass Ihr Bot eine Antwort vom Benutzer erwartet, legen Sie im [Activity][Activity]-Objekt, das Ihre Nachricht darstellt, die Eigenschaft `inputHint` auf **expectingInput** fest. Bei vielen Kanälen wird dadurch das Eingabefeld des Clients aktiviert und das Mikrofon geöffnet. 

Das folgende Beispiel zeigt eine Anforderung, die eine Nachricht sendet, und gibt an, dass der Bot eine Eingabe erwartet. In dieser Beispielanforderung stellt `https://smba.trafficmanager.net/apis` den Basis-URI dar. Der Basis-URI für Anforderungen, die Ihr Bot ausgibt, kann ein anderer sein. Weitere Informationen zum Festlegen des Basis-URI finden Sie unter [API-Referenz](bot-framework-rest-connector-api-reference.md#base-uri).

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
    "text": "What is your favorite color?",
    "inputHint": "expectingInput",
    "replyToId": "5d5cdc723"
}
```

## <a name="ignoring-input"></a>Eingabe wird ignoriert
 
Um anzugeben, dass Ihr Bot nicht für den Empfang einer Eingabe vom Benutzer bereit ist, legen Sie im [Activity][Activity]-Objekt, das Ihre Nachricht darstellt, die Eigenschaft `inputHint` auf **ignoringInput** fest. Bei vielen Kanälen wird dadurch das Eingabefeld des Clients deaktiviert und das Mikrofon geschlossen. 

Das folgende Beispiel zeigt eine Anforderung, die eine Nachricht sendet, und gibt an, dass der Bot eine Eingabe ignoriert. In dieser Beispielanforderung stellt `https://smba.trafficmanager.net/apis` den Basis-URI dar. Der Basis-URI für Anforderungen, die Ihr Bot ausgibt, kann ein anderer sein. Weitere Informationen zum Festlegen des Basis-URI finden Sie in der [API-Referenz](bot-framework-rest-connector-api-reference.md#base-uri).

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
    "text": "Please hold while I perform the calculation.",
    "inputHint": "ignoringInput",
    "replyToId": "5d5cdc723"
}
```

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Erstellen von Nachrichten](bot-framework-rest-connector-create-messages.md)
- [Senden und Empfangen von Nachrichten](bot-framework-rest-connector-send-and-receive-messages.md)

[Activity]: bot-framework-rest-connector-api-reference.md#activity-object