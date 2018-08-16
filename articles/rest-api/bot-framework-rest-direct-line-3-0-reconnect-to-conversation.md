---
title: Erneutes Herstellen einer Verbindung mit einer Konversation | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie erneut eine Verbindung mit einer Konversation mit Direct Line API v3.0 herstellen.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
ms.openlocfilehash: 2c6b3a7e9f0fdc7d5227fc8112cb6f3e330a2bcc
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39301728"
---
# <a name="reconnect-to-a-conversation"></a>Erneutes Herstellen einer Verbindung mit einer Konversation

Wenn ein Client die [WebSocket-Schnittstelle](bot-framework-rest-direct-line-3-0-receive-activities.md#connect-via-websocket) verwendet, um Nachrichten zu empfangen, aber die Verbindung getrennt wird, möchte dieser ggf. die Verbindung wiederherstellen. In diesem Szenario muss der Client eine neue WebSocket-Stream-URL generieren, die er verwenden kann, um erneut eine Verbindung mit der Konversation herzustellen.

## <a name="generate-a-new-websocket-stream-url"></a>Generieren einer neuen WebSocket-Stream-URL

Senden Sie die folgende Anforderung, um eine neue WebSocket-Stream-URL zu erstellen, die verwendet werden kann, um erneut eine Verbindung zu der bereits vorhandenen Konversation herzustellen: 

```http
GET https://directline.botframework.com/v3/directline/conversations/{conversationId}?watermark={watermark_value}
Authorization: Bearer SECRET_OR_TOKEN
```

Ersetzen Sie in dieser Anforderungs-URI **{conversationId}** durch die Konversations-ID und **{watermark_value}** durch den Wasserzeichenwert (wenn der Parameter `watermark` angegeben ist). Das `watermark` ist optional. Wenn der `watermark`-Parameter im Anforderungs-URI angegeben ist, wird die Konversation ab dem Wasserzeichen wiederholt. Dadurch wird garantiert, dass keine Nachrichten verloren gehen. Wenn der `watermark`-Parameter nicht im Anforderungs-URI angegeben ist, werden nur die Nachrichten wiederholt, die nach dem Senden der Anforderung zum erneuten Herstellen einer Verbindung empfangen wurden.

Die folgenden Codeausschnitte enthalten ein Beispiel der Reconnect-Anforderung und -Antwort.

### <a name="request"></a>Anforderung

```http
GET https://directline.botframework.com/v3/directline/conversations/abc123?watermark=0000a-42
Authorization: Bearer RCurR_XV9ZA.cwA.BKA.iaJrC8xpy8qbOF5xnR2vtCX7CZj0LdjAPGfiCpg4Fv0y8qbOF5xPGfiCpg4Fv0y8qqbOF5x8qbOF5xn
```

### <a name="response"></a>response

Wenn die Anforderung erfolgreich gesendet wird, enthält die Antwort eine ID zu der Konversation, ein Token und eine neue WebSocket-Stream-URL.

```http
HTTP/1.1 200 OK
[other headers]
```

```json
{
  "conversationId": "abc123",
  "token": "RCurR_XV9ZA.cwA.BKA.iaJrC8xpy8qbOF5xnR2vtCX7CZj0LdjAPGfiCpg4Fv0y8qbOF5xPGfiCpg4Fv0y8qqbOF5x8qbOF5xn",
  "streamUrl": "https://directline.botframework.com/v3/directline/conversations/abc123/stream?watermark=000a-4&amp;t=RCurR_XV9ZA.cwA..."
}
```

## <a name="reconnect-to-the-conversation"></a>Erneutes Herstellen einer Verbindung mit einer Konversation

Der Client muss die neue WebSocket-Stream-URL verwenden, um innerhalb von 60 Sekunden [erneut eine Verbindung zu der Konversation](bot-framework-rest-direct-line-3-0-receive-activities.md#connect-via-websocket) herstellen zu können. Wenn die Verbindung in diesem Zeitraum nicht hergestellt werden kann, muss der Client eine weitere Reconnect-Anforderung senden, um eine neue Stream-URL zu generieren.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Wichtige Begriffe](bot-framework-rest-direct-line-3-0-concepts.md)
- [Authentifizierung](bot-framework-rest-direct-line-3-0-authentication.md)
- [Receive activities via WebSocket stream (Empfangen von Aktivitäten über WebSocket-Stream)](bot-framework-rest-direct-line-3-0-receive-activities.md#connect-via-websocket)