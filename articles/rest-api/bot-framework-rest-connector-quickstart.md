---
title: Erstellen eines Bots mit dem Bot Connector-Dienst | Microsoft-Dokumentation
description: Erstellen Sie einen Bot mit dem Bot Connector-Dienst.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 12/13/2017
ms.openlocfilehash: 57babac9594118c12805ff9023cf7086e526a273
ms.sourcegitcommit: b78fe3d8dd604c4f7233740658a229e85b8535dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2018
ms.locfileid: "49997938"
---
# <a name="create-a-bot-with-the-bot-connector-service"></a>Erstellen eines Bots mit dem Bot Connector-Dienst
> [!div class="op_single_selector"]
> - [.NET](../dotnet/bot-builder-dotnet-quickstart.md)
> - [Node.js](../nodejs/bot-builder-nodejs-quickstart.md)
> - [Bot Service](../bot-service-quickstart.md)
> - [REST](../rest-api/bot-framework-rest-connector-quickstart.md)

Der Bot Connector-Dienst ermöglicht es Ihrem Bot, Nachrichten mit im <a href="https://dev.botframework.com/" target="_blank">Bot Framework-Portal</a> konfigurierten Kanälen auszutauschen, indem die Branchenstandards REST und JSON gegenüber HTTPS bevorzugt werden. In diesem Tutorial erhalten Sie Anweisungen zum Abrufen eines Zugriffstokens aus Bot Framework und zum Verwenden des Bot Connector-Dienst zum Austauschen von Nachrichten zwischen Benutzern.

## <a id="get-token"></a> Abrufen eines Zugriffstokens

> [!IMPORTANT]
> Falls nicht bereits geschehen, müssen Sie Ihren Bot bei Bot Framework [registrieren](../bot-service-quickstart-registration.md), um dessen App-ID und Kennwort zu erhalten. Sie benötigen die App-ID und das Kennwort des Bots, um ein Zugriffstoken abzurufen.

Um mit dem Bot Connector-Dienst zu kommunizieren, müssen Sie ein Zugriffstoken im `Authorization`-Header jeder API-Anforderung unter Verwendung dieses Formats angeben: 

```http
Authorization: Bearer ACCESS_TOKEN
```

Sie können das Zugriffstoken für Ihren Bot abrufen, indem Sie eine API-Anforderung senden.

### <a name="request"></a>Anforderung

Führen Sie die folgende Anforderung aus, um ein Zugriffstoken anzufordern, dass zum Authentifizieren von Anforderungen an den Bot Connector-Dienst verwendet werden kann. Ersetzen Sie dabei **MICROSOFT-APP-ID** und **MICROSOFT-APP-PASSWORD** durch die App-ID und das Kennwort, das Sie bei der [Registrierung](../bot-service-quickstart-registration.md) Ihres Bots bei Bot Framework erhalten haben.

```http
POST https://login.microsoftonline.com/botframework.com/oauth2/v2.0/token
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials&client_id=MICROSOFT-APP-ID&client_secret=MICROSOFT-APP-PASSWORD&scope=https%3A%2F%2Fapi.botframework.com%2F.default
```

### <a name="response"></a>response

Wenn die Anforderung erfolgreich gesendet wird, erhalten Sie die Antwort „HTTP 200“, in der das Zugriffstoken und Informationen zu dessen Ablaufdatum angegeben werden. 

```json
{
    "token_type":"Bearer",
    "expires_in":3600,
    "ext_expires_in":3600,
    "access_token":"eyJhbGciOiJIUzI1Ni..."
}
```

> [!TIP]
> Weitere Informationen zur Authentifizierung im Bot Connector-Dienst finden Sie unter [Authentifizierung](bot-framework-rest-connector-authentication.md).

## <a name="exchange-messages-with-the-user"></a>Austauschen von Nachrichten mit dem Benutzer

Bei einer Konversation handelt es sich um einen Austausch mehrerer Nachrichten zwischen einem Benutzer und Ihrem Bot. 

### <a name="receive-a-message-from-the-user"></a>Empfangen einer Nachricht vom Benutzer

Wenn der Benutzer eine Nachricht sendet, sendet der Bot Framework Connector eine Anforderung an den Endpunkt, den Sie bei der [Registrierung](../bot-service-quickstart-registration.md) Ihres Bots angegeben haben. Bei dem Anforderungstext handelt es sich um ein [Activity][Activity]-Objekt. Das folgende Beispiel zeigt den Anforderungstext, den der Bot empfängt, wenn der Benutzer eine einfache Nachricht an den Bot sendet. 

```json
{
    "type": "message",
    "id": "bf3cc9a2f5de...",
    "timestamp": "2016-10-19T20:17:52.2891902Z",
    "serviceUrl": "https://smba.trafficmanager.net/apis",
    "channelId": "channel's name/id",
    "from": {
        "id": "1234abcd",
        "name": "user's name"
    },
    "conversation": {
        "id": "abcd1234",
        "name": "conversation's name"
    },
    "recipient": {
        "id": "12345678",
        "name": "bot's name"
    },
    "text": "Haircut on Saturday"
}
```

### <a name="reply-to-the-users-message"></a>Antworten auf die Nachricht des Benutzers

Wenn der Botendpunkt eine `POST`-Anforderung empfängt, die eine Nachricht des Benutzers darstellt (d.h. `type` = **Nachricht**), verwenden Sie die Informationen in dieser Anforderung, um das [Activity][Activity]-Objekt für Ihre Antwort zu erstellen.

1. Legen Sie die **conversation**-Eigenschaft auf die Inhalte der **conversation**-Eigenschaft in der Nachricht des Benutzers fest.
2. Legen Sie die **from**-Eigenschaft auf die Inhalte der **recipient**-Eigenschaft in der Nachricht des Benutzers fest.
3. Legen Sie die **recipient**-Eigenschaft auf die Inhalte der **from**-Eigenschaft in der Nachricht des Benutzers fest.
4. Legen Sie die Eigenschaften **text** und **attachments** nach Bedarf fest.

Verwenden Sie die `serviceUrl`-Eigenschaft in der eingehenden Anforderung zum [Identifizieren des Basis-URI](bot-framework-rest-connector-api-reference.md#base-uri), über den Ihr Bot die Antwort ausgeben sollte. 

Senden (`POST`) Sie Ihr [Activity][Activity]-Objekt wie im folgenden Beispiel dargestellt an `/v3/conversations/{conversationId}/activities/{activityId}`, um die Antwort zu übermitteln. Bei dem Anforderungstext handelt es sich um ein [Activity][Activity]-Objekt, das den Benutzer dazu auffordert, einen verfügbaren Termin auszuwählen.

```http
POST https://smba.trafficmanager.net/apis/v3/conversations/abcd1234/activities/bf3cc9a2f5de... 
Authorization: Bearer eyJhbGciOiJIUzI1Ni...
Content-Type: application/json
```

```json
{
    "type": "message",
    "from": {
        "id": "12345678",
        "name": "bot's name"
    },
    "conversation": {
        "id": "abcd1234",
        "name": "conversation's name"
   },
   "recipient": {
        "id": "1234abcd",
        "name": "user's name"
    },
    "text": "I have these times available:",
    "replyToId": "bf3cc9a2f5de..."
}
```

In dieser Beispielanforderung stellt `https://smba.trafficmanager.net/apis` den Basis-URI dar. Der Basis-URI für Anforderungen, die Ihr Bot ausgibt, kann ein anderer sein. Weitere Informationen zum Festlegen des Basis-URI finden Sie unter [API-Referenz](bot-framework-rest-connector-api-reference.md#base-uri). 

> [!IMPORTANT]
> Wie in diesem Beispiel gezeigt, muss der `Authorization`-Header jeder von Ihnen gesendeten API-Anforderung das Wort **Bearer** enthalten. Darauf muss das Zugriffstoken folgen, das Sie von [Bot Framework erhalten haben](#get-token).

Senden (`POST`) Sie eine weitere Anforderung an denselben Endpunkt, um eine andere Nachricht zu senden, über die der Benutzer mithilfe eines Klicks auf eine Schaltfläche einen verfügbaren Termin auswählen kann:

```http
POST https://smba.trafficmanager.net/apis/v3/conversations/abcd1234/activities/bf3cc9a2f5de... 
Authorization: Bearer eyJhbGciOiJIUzI1Ni...
Content-Type: application/json
```

```json
{
    "type": "message",
    "from": {
        "id": "12345678",
        "name": "bot's name"
    },
    "conversation": {
        "id": "abcd1234",
        "name": "conversation's name"
   },
   "recipient": {
        "id": "1234abcd",
        "name": "user's name"
    },
    "attachmentLayout": "list",
    "attachments": [
      {
        "contentType": "application/vnd.microsoft.card.thumbnail",
        "content": {
          "buttons": [
            {
              "type": "imBack",
              "title": "10:30",
              "value": "10:30"
            },
            {
              "type": "imBack",
              "title": "11:30",
              "value": "11:30"
            },
            {
              "type": "openUrl",
              "title": "See more",
              "value": "http://www.contososalon.com/scheduling"
            }
          ]
        }
      }
    ],
    "replyToId": "bf3cc9a2f5de..."
}
```   

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial wurde erläutert, wie Sie ein Zugriffstoken von Bot Framework erhalten und den Bot Connector-Dienst zum Austauschen von Nachrichten zwischen Benutzern verwenden können. Sie können den [Bot Framework-Emulator](../bot-service-debug-emulator.md) zum Testen und Debuggen Ihres Bots verwenden. Wenn Sie Ihren Bot für andere freigeben möchten, müssen Sie diesen für die Ausführung auf mindestens einem Kanal [konfigurieren](../bot-service-manage-channels.md).


[Activity]: bot-framework-rest-connector-api-reference.md#activity-object