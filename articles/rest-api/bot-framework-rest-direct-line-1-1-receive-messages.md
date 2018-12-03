---
title: Empfangen von Nachrichten vom Bot | Microsoft-Dokumentation
description: Erfahren Sie, wie Nachrichten vom Bot mithilfe von Direct Line API v1.1 empfangen werden.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 12/13/2017
ms.openlocfilehash: 6679afa688917bdf3d558d5ed47717ee30d0e52e
ms.sourcegitcommit: b78fe3d8dd604c4f7233740658a229e85b8535dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2018
ms.locfileid: "49999357"
---
# <a name="receive-messages-from-the-bot"></a>Empfangen von Nachrichten vom Bot

> [!IMPORTANT]
> In diesem Artikel wird beschrieben, wie Nachrichten vom Bot mithilfe von Direct Line API 1.1 empfangen werden. Wenn Sie eine neue Verbindung zwischen Ihrer Clientanwendung und dem Bot erstellen, verwenden Sie stattdessen [Direct Line API 3.0](bot-framework-rest-direct-line-3-0-receive-activities.md).

Bei Verwendung des Direct Line 1.1-Protokolls müssen Clients eine `HTTP GET`-Schnittstelle abfragen, um Nachrichten zu empfangen. 

## <a name="retrieve-messages-with-http-get"></a>Abrufen von Nachrichten mit HTTP GET

Geben Sie zum Abrufen von Nachrichten für eine bestimmte Konversation eine `GET`-Anforderung an den `api/conversations/{conversationId}/messages`-Endpunkt aus, wobei Sie optional den `watermark`-Parameter festlegen, um die neueste vom Client angezeigte Nachricht anzugeben. Ein aktualisierter `watermark`-Wert wird in der JSON-Antwort zurückgegeben, selbst wenn keine Nachrichten enthalten sind.

Die folgenden Codeausschnitte enthalten ein Beispiel der Get Messages-Anforderung und -Antwort. Die Get Messages-Antwort enthält `watermark` als Eigenschaft von [MessageSet](bot-framework-rest-direct-line-1-1-api-reference.md#messageset-object). Clients sollten durch die verfügbaren Nachrichten blättern, indem der `watermark`-Wert erhöht wird, bis keine Nachrichten mehr zurückgegeben werden. 

### <a name="request"></a>Anforderung

```http
GET https://directline.botframework.com/api/conversations/abc123/messages?watermark=0001a-94
Authorization: Bearer RCurR_XV9ZA.cwA.BKA.iaJrC8xpy8qbOF5xnR2vtCX7CZj0LdjAPGfiCpg4Fv0
```

### <a name="response"></a>response

```http
HTTP/1.1 200 OK
[other headers]
```

```json
{
    "messages": [
        {
            "conversation": "abc123",
            "id": "abc123|0000",
            "text": "hello",
            "from": "user1"
        }, 
        {
            "conversation": "abc123",
            "id": "abc123|0001",
            "text": "Nice to see you, user1!",
            "from": "bot1"
        }
    ],
    "watermark": "0001a-95"
}
```

## <a name="timing-considerations"></a>Überlegungen zum Timing

Direct Line ist zwar ein mehrteiliges Protokoll mit möglichen Lücken im Timing, das Protokoll und der Dienst wurden jedoch entwickelt, um das Erstellen eines zuverlässigen-Clients zu erleichtern. Die `watermark`-Eigenschaft, die in der Get Messages-Antwort gesendet wird, ist zuverlässig. Ein Client verpasst keine Nachrichten, solange er das Wasserzeichen wörtlich wiedergibt.

Clients sollten ein Abrufintervall wählen, das ihrer vorgesehenen Verwendung entspricht.

- Dienst-zu-Dienst-Anwendungen verwenden häufig ein Abrufintervall von 5 oder 10 Sekunden.

- Anwendungen mit Clientkontakt verwenden oft ein Pollingintervall von 1s und geben etwa 300ms nach jeder Nachricht, die der Client sendet, eine zusätzliche Anforderung aus (um die Antwort eines Bots schnell abzurufen). Diese Verzögerung von 300ms sollte basierend auf der Geschwindigkeit und Übertragungszeit des Bots angepasst werden.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Wichtige Begriffe](bot-framework-rest-direct-line-1-1-concepts.md)
- [Authentifizierung](bot-framework-rest-direct-line-1-1-authentication.md)
- [Starten einer Konversation](bot-framework-rest-direct-line-1-1-start-conversation.md)
- [Senden einer Nachricht an den Bot](bot-framework-rest-direct-line-1-1-send-message.md)