---
title: Starten einer Unterhaltung | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie eine Unterhaltung mit Direct Line API v3.0 starten.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 12/13/2017
ms.openlocfilehash: 6754935070fefb890c3d5b7bd90f1c1a4c5d401d
ms.sourcegitcommit: b78fe3d8dd604c4f7233740658a229e85b8535dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2018
ms.locfileid: "50000297"
---
# <a name="start-a-conversation"></a>Starten einer Unterhaltung

Direct Line-Unterhaltungen werden explizit von Clients geöffnet und können solange ausgeführt werden, wie Bot und Client daran teilnehmen und gültige Anmeldeinformationen aufweisen. Solange die Unterhaltung geöffnet ist, können der Bot und der Client Nachrichten senden. Es können mehrere Clients eine Verbindung mit einer bestimmten Unterhaltung herstellen, und jeder Client kann im Namen mehrerer Benutzer teilnehmen.

## <a name="open-a-new-conversation"></a>Öffnen einer neuen Unterhaltung

Um eine neue Unterhaltung mit einem Bot zu öffnen, erstellen Sie diese Anforderung:

```http
POST https://directline.botframework.com/v3/directline/conversations
Authorization: Bearer SECRET_OR_TOKEN
```

Die folgenden Codeausschnitte enthalten ein Beispiel für die Anforderung zum Starten einer Unterhaltung und die Antwort darauf.

### <a name="request"></a>Anforderung

```http
POST https://directline.botframework.com/v3/directline/conversations
Authorization: Bearer RCurR_XV9ZA.cwA.BKA.iaJrC8xpy8qbOF5xnR2vtCX7CZj0LdjAPGfiCpg4Fv0y8qbOF5xPGfiCpg4Fv0y8qqbOF5x8qbOF5xn
```

### <a name="response"></a>response

Wenn die Anforderung erfolgreich war, enthält die Antwort eine ID für die Unterhaltung, ein Token, einen Wert, der die Anzahl von Sekunden bis zum Ablauf des Tokens angibt, und eine Stream-URL, mit der der Client [Aktivitäten über den WebSocket-Stream empfangen](bot-framework-rest-direct-line-3-0-receive-activities.md#connect-via-websocket) kann.

```http
HTTP/1.1 201 Created
[other headers]
```

```json
{
  "conversationId": "abc123",
  "token": "RCurR_XV9ZA.cwA.BKA.iaJrC8xpy8qbOF5xnR2vtCX7CZj0LdjAPGfiCpg4Fv0y8qbOF5xPGfiCpg4Fv0y8qqbOF5x8qbOF5xn",
  "expires_in": 1800,
  "streamUrl": "https://directline.botframework.com/v3/directline/conversations/abc123/stream?t=RCurR_XV9ZA.cwA..."
}
```

In der Regel wird eine Anforderung zum Starten einer Unterhaltung verwendet, um eine neue Unterhaltung zu öffnen. Ein **HTTP 201**-Statuscode wird zurückgegeben, wenn die neue Unterhaltung erfolgreich gestartet wurde. Wenn ein Client jedoch eine Anforderung zum Starten einer Unterhaltung mit einem Direct Line-Token im `Authorization`-Header übermittelt, der vorher zum Starten einer Unterhaltung mithilfe des Vorgangs „Unterhaltung starten“ verwendet wurde, wird ein **HTTP 200**-Statuscode zurückgegeben, um anzugeben, dass die Anforderung akzeptabel ist, jedoch keine Unterhaltung erstellt wurde (da diese bereits vorhanden ist).

> [!TIP]
> Sie haben 60 Sekunden, um eine Verbindung mit der WebSocket-Stream-URL herzustellen. Wenn die Verbindung in diesem Zeitraum nicht hergestellt werden kann, können Sie [erneut eine Verbindung mit der Unterhaltung herstellen](bot-framework-rest-direct-line-3-0-reconnect-to-conversation.md), um eine neue Stream-URL zu generieren.

## <a name="start-conversation-versus-generate-token"></a>Starten einer Unterhaltung vs. Generieren eines Tokens

Der Vorgang „Unterhaltung starten“ (`POST /v3/directline/conversations`) ist vergleichbar mit dem Vorgang [Token generieren](bot-framework-rest-direct-line-3-0-authentication.md#generate-token) (`POST /v3/directline/tokens/generate`). Beide Vorgänge geben ein `token` zurück, mit dem auf eine einzelne Unterhaltung zugegriffen werden kann. Der Vorgang zum Starten einer Unterhaltung beginnt jedoch die Unterhaltung, kontaktiert den Bot und erstellt eine WebSocket-Stream-URL, wohingegen der Vorgang zum Generieren eines Tokens weder eine Unterhaltung startet noch einen Bot kontaktiert. 

Wenn Sie die Unterhaltung sofort beginnen möchten, verwenden Sie den Vorgang zum Starten einer Unterhaltung. Wenn Sie beabsichtigen, das Token an Clients zu verteilen, und möchten, dass diese die Unterhaltung initiieren, verwenden Sie stattdessen den Vorgang [Token generieren](bot-framework-rest-direct-line-3-0-authentication.md#generate-token). 

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Wichtige Begriffe](bot-framework-rest-direct-line-3-0-concepts.md)
- [Authentifizierung](bot-framework-rest-direct-line-3-0-authentication.md)
- [Empfangen von Aktivitäten über den WebSocket-Stream](bot-framework-rest-direct-line-3-0-receive-activities.md#connect-via-websocket)
- [Erneutes Herstellen einer Verbindung mit einer Unterhaltung](bot-framework-rest-direct-line-3-0-reconnect-to-conversation.md)