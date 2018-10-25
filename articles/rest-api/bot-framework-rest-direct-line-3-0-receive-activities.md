---
title: Empfangen von Aktivitäten vom Bot | Microsoft-Dokumentation
description: Erfahren Sie, wie Aktivitäten vom Bot mithilfe von Direct Line API v3.0 empfangen werden.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 12/13/2017
ms.openlocfilehash: c2d4b9a8e2b8ffc1656df44e04ee1bde912e36ea
ms.sourcegitcommit: b78fe3d8dd604c4f7233740658a229e85b8535dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2018
ms.locfileid: "49998157"
---
# <a name="receive-activities-from-the-bot"></a>Empfangen von Aktivitäten vom Bot

Mithilfe des Direct Line 3.0-Protokolls können Clients Aktivitäten über `WebSocket`-Stream empfangen oder Aktivitäten durch Ausgeben von `HTTP GET`-Anforderungen abrufen. 

## <a name="websocket-vs-http-get"></a>WebSocket oder HTTP GET

Ein streamender WebSocket sendet Nachrichten effizient per Push an Clients, während die GET-Schnittstelle Clients ermöglicht, Nachrichten explizit anzufordern. Der WebSocket-Mechanismus wird zwar häufig aufgrund seiner Effizienz bevorzugt, der GET-Mechanismus kann aber für Clients, die keine WebSockets verwenden können, oder für Clients, die den Konversationsverlauf abrufen möchten, hilfreich sein. 

Nicht alle [Aktivitätstypen](bot-framework-rest-connector-activities.md) sind sowohl über WebSocket als auch über HTTP GET verfügbar. Die folgende Tabelle beschreibt die Verfügbarkeit der verschiedenen Aktivitätstypen für Clients, die das Direct Line-Protokoll verwenden.

| Aktivitätstyp | Verfügbarkeit | 
|----|----|
| Message: | HTTP GET und WebSocket |
| typing | Nur WebSocket |
| conversationUpdate | Über Client nicht gesendet/empfangen |
| contactRelationUpdate | In Direct Line nicht unterstützt |
| endOfConversation | HTTP GET und WebSocket |
| Alle anderen Aktivitätstypen | HTTP GET und WebSocket |

## <a id="connect-via-websocket"></a>-Empfangsaktivität über WebSocket-Stream

Wenn ein Client eine [Konversation starten](bot-framework-rest-direct-line-3-0-start-conversation.md)-Anforderung sendet, um eine Konversation mit einem Bot zu öffnen, enthält die Antwort des Diensts eine `streamUrl`-Eigenschaft, die der Client anschließend für die Verbindungen über WebSocket verwenden kann. Die Stream-URL ist vorautorisiert und daher erfordert die Anforderung des Clients, die Verbindung über WebSocket herzustellen, KEINEN `Authorization`-Header.

Das folgende Beispiel zeigt eine Anforderung, die eine `streamUrl` für die Verbindung über WebSocket verwendet.

```http
-- connect to wss://directline.botframework.com --
GET /v3/directline/conversations/abc123/stream?t=RCurR_XV9ZA.cwA..."
Upgrade: websocket
Connection: upgrade
[other headers]
```

Der Dienst antwortet mit dem Statuscode HTTP 101 („Protokolle wechseln“).

```http
HTTP/1.1 101 Switching Protocols
[other headers]
```

### <a name="receive-messages"></a>Empfangen von Nachrichten

Nachdem die Verbindung über WebSocket hergestellt wurde, kann ein Client diese Arten von Nachrichten vom Direct Line-Dienst empfangen:

- Eine Nachricht mit einem [ActivitySet](bot-framework-rest-direct-line-3-0-api-reference.md#activityset-object), das eine oder mehrere Aktivitäten und ein Wasserzeichen (siehe unten) enthält.
- Eine leere Nachricht, die der Direct Line-Dienst verwendet, um sicherzustellen, dass die Verbindung noch gültig ist.
- Zusätzliche Typen, die später definiert werden. Diese Typen werden durch die Eigenschaften im JSON-Stamm identifiziert.

Ein `ActivitySet` enthält vom Bot und allen Benutzern in der Konversation gesendete Nachrichten. Das folgende Beispiel zeigt ein `ActivitySet`, das eine einzelne Nachricht enthält.

```json
{
    "activities": [
        {
            "type": "message",
            "channelId": "directline",
            "conversation": {
                "id": "abc123"
            },
            "id": "abc123|0000",
            "from": {
                "id": "user1"
            },
            "text": "hello"
        }
    ],
    "watermark": "0000a-42"
}
```

### <a name="process-messages"></a>Verarbeiten von Nachrichten

Ein Client sollte den `watermark`-Wert nachverfolgen, den er in jedem [ActivitySet](bot-framework-rest-direct-line-3-0-api-reference.md#activityset-object) empfängt, sodass er das Wasserzeichen verwenden kann, um sicherzustellen, dass keine Nachrichten verloren gehen, wenn die Verbindung unterbrochen wird und ein [erneutes Verbinden mit der Konversation](bot-framework-rest-direct-line-3-0-reconnect-to-conversation.md) erforderlich ist. Wenn der Client ein `ActivitySet` empfängt, in dem die `watermark`-Eigenschaft `null` oder nicht vorhanden ist, sollte er diesen Wert ignorieren und das zuvor empfangene Wasserzeichen nicht überschreiben.

Ein Client sollte vom Direct Line-Dienst empfangene leere Nachrichten ignorieren.

Ein Client kann leere Nachrichten an den Direct Line-Dienst senden, um die Konnektivität zu überprüfen. Der Direct Line-Dienst ignoriert vom Client empfangene leere Nachrichten.

Der Direct Line-Dienst kann das Schließen der WebSocket-Verbindung unter bestimmten Bedingungen erzwingen. Wenn der Client keine `endOfConversation`-Aktivität empfangen hat, kann er [eine neue WebSocket-Stream-URL generieren](bot-framework-rest-direct-line-3-0-reconnect-to-conversation.md) und verwenden, um die Verbindung mit der Konversation wieder herzustellen. 

Der WebSocket-Stream enthält Liveupdates und erst vor kurzem gesendete Nachrichten (seit der Aufruf zum Herstellen der Verbindung über WebSocket ausgegeben wurde), enthält aber keine Nachrichten, die vor dem letzten `POST` an `/v3/directline/conversations/{id}` gesendet wurden. Verwenden Sie zum Abrufen von vorher in der Konversation gesendeten Nachrichten `HTTP GET`, wie unten beschrieben.

## <a id="http-get"></a> Abrufen von Aktivitäten mit HTTP-GET

Clients, die WebSockets nicht verwenden können, oder Clients, die den Konversationsverlauf abrufen möchten, können Aktivitäten mithilfe von `HTTP GET` abrufen.

Geben Sie zum Abrufen von Nachrichten für eine bestimmte Konversation eine `GET`-Anforderung an den `/v3/directline/conversations/{conversationId}/activities`-Endpunkt aus, wobei Sie optional den `watermark`-Parameter festlegen, um die neueste vom Client angezeigte Nachricht anzugeben. 

Die folgenden Codeausschnitte enthalten ein Beispiel für die Get Conversation Activities-Anforderung und -Antwort. Die Get Conversation Activities-Antwort enthält `watermark` als Eigenschaft von [ActivitySet](bot-framework-rest-direct-line-3-0-api-reference.md#activityset-object). Clients sollten durch die verfügbaren Aktivitäten blättern, indem der `watermark`-Wert erhöht wird, bis keine Aktivitäten mehr zurückgegeben werden.

### <a name="request"></a>Anforderung

```http
GET https://directline.botframework.com/v3/directline/conversations/abc123/activities?watermark=0001a-94
Authorization: Bearer RCurR_XV9ZA.cwA.BKA.iaJrC8xpy8qbOF5xnR2vtCX7CZj0LdjAPGfiCpg4Fv0
```

### <a name="response"></a>response

```http
HTTP/1.1 200 OK
[other headers]
```

```json
{
    "activities": [
        {
            "type": "message",
            "channelId": "directline",
            "conversation": {
                "id": "abc123"
            },
            "id": "abc123|0000",
            "from": {
                "id": "user1"
            },
            "text": "hello"
        }, 
        {
            "type": "message",
            "channelId": "directline",
            "conversation": {
                "id": "abc123"
            },
            "id": "abc123|0001",
            "from": {
                "id": "bot1"
            },
            "text": "Nice to see you, user1!"
        }
    ],
    "watermark": "0001a-95"
}
```

## <a name="timing-considerations"></a>Überlegungen zum Timing

Die meisten Clients möchten einen vollständige Nachrichtenverlauf speichern. Direct Line ist zwar ein mehrteiliges Protokoll mit möglichen Lücken im Timing, das Protokoll und der Dienst wurden jedoch entwickelt, um das Erstellen eines zuverlässigen-Clients zu erleichtern.

- Die im WebSocket-Stream und der Get Conversation Activities-Antwort gesendete `watermark`-Eigenschaft ist zuverlässig. Ein Client verpasst keine Nachrichten, solange er das Wasserzeichen wörtlich wiedergibt.

- Wenn ein Client eine Konversation startet und eine Verbindung mit dem WebSocket-Stream herstellt, werden alle Aktivitäten, die nach dem POST, aber vor dem Öffnen des Sockets gesendet werden, vor neuen Aktivitäten wiedergegeben.

- Wenn ein Client eine Get Conversation Activities-Anforderung ausgibt (um den Verlauf zu aktualisieren), während er mit dem WebSocket-Stream verbunden ist, können Aktivitäten über beide Kanäle dupliziert werden. Clients sollten alle bekannten Aktivitäts-IDs nachverfolgen, damit sie in der Lage sind, doppelte Aktivitäten abzulehnen, sofern diese auftraten.

Clients, die Polling mithilfe von `HTTP GET` durchführen, sollten ein Abrufintervall wählen, das ihrer vorgesehenen Verwendung entspricht.

- Dienst-zu-Dienst-Anwendungen verwenden häufig ein Abrufintervall von 5 oder 10 Sekunden.

- Anwendungen mit Clientkontakt verwenden oft ein Pollingintervall von 1s und geben etwa 300ms nach jeder Nachricht, die der Client sendet, eine zusätzliche Anforderung aus (um die Antwort eines Bots schnell abzurufen). Diese Verzögerung von 300ms sollte basierend auf der Geschwindigkeit und Übertragungszeit des Bots angepasst werden.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Wichtige Begriffe](bot-framework-rest-direct-line-3-0-concepts.md)
- [Authentifizierung](bot-framework-rest-direct-line-3-0-authentication.md)
- [Starten einer Konversation](bot-framework-rest-direct-line-3-0-start-conversation.md)
- [Erneutes Verbinden mit einer Konversation](bot-framework-rest-direct-line-3-0-reconnect-to-conversation.md)
- [Senden einer Aktivität an den Bot](bot-framework-rest-direct-line-3-0-send-activity.md)
- [Beenden einer Konversation](bot-framework-rest-direct-line-3-0-end-conversation.md)