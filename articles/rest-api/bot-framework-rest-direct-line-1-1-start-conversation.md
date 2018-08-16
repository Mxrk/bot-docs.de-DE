---
title: Starten einer Unterhaltung | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie eine Unterhaltung mit Direct Line API v1.1 starten.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
ms.openlocfilehash: 36645ce3811c77a3ca7ed697eeae63027fa1644a
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39302509"
---
# <a name="start-a-conversation"></a>Starten einer Unterhaltung

> [!IMPORTANT]
> Dieser Artikel beschreibt, wie Sie eine Unterhaltung mit Direct Line API v1.1 starten. Wenn Sie eine neue Verbindung zwischen Ihrer Clientanwendung und dem Bot erstellen, verwenden Sie stattdessen [Direct Line API 3.0](bot-framework-rest-direct-line-3-0-start-conversation.md).

Direct Line-Unterhaltungen werden explizit von Clients geöffnet und können solange ausgeführt werden, wie Bot und Client daran teilnehmen und gültige Anmeldeinformationen haben. Solange die Unterhaltung geöffnet ist, können der Bot und der Client Nachrichten senden. Mehr als ein Client kann eine Verbindung zu einer bestimmten Unterhaltung herstellen, und jeder Client kann im Namen mehrerer Benutzer teilnehmen.

## <a name="open-a-new-conversation"></a>Öffnen einer neuen Unterhaltung

Um eine neue Unterhaltung mit einem Bot zu öffnen, erstellen Sie diese Anforderung:

```http
POST https://directline.botframework.com/api/conversations
Authorization: Bearer SECRET_OR_TOKEN
```

Die folgenden Codeausschnitte enthalten ein Beispiel für die Anforderung zum und die Antwort auf das Starten einer Unterhaltung.

### <a name="request"></a>Anforderung

```http
POST https://directline.botframework.com/api/conversations
Authorization: Bearer RCurR_XV9ZA.cwA.BKA.iaJrC8xpy8qbOF5xnR2vtCX7CZj0LdjAPGfiCpg4Fv0y8qbOF5xPGfiCpg4Fv0y8qqbOF5x8qbOF5xn
```

### <a name="response"></a>response

Wenn die Anforderung erfolgreich war, enthält die Antwort eine ID für die Unterhaltung, ein Token und einen Wert, der die Anzahl von Sekunden bis zum Ablauf des Tokens angibt.

```http
HTTP/1.1 200 OK
[other headers]
```

```json
{
  "conversationId": "abc123",
  "token": "RCurR_XV9ZA.cwA.BKA.iaJrC8xpy8qbOF5xnR2vtCX7CZj0LdjAPGfiCpg4Fv0y8qbOF5xPGfiCpg4Fv0y8qqbOF5x8qbOF5xn",
  "expires_in": 1800
}
```

## <a name="start-conversation-versus-generate-token"></a>Starten einer Unterhaltung vs. Generieren eines Tokens

Der Vorgang zum Starten einer Unterhaltung (`POST /api/conversations`) ist vergleichbar mit dem Vorgang zum [Generieren eines Tokens](bot-framework-rest-direct-line-1-1-authentication.md#generate-token) (`POST /api/tokens/conversation`). Beide Vorgänge geben ein `token` zurück, mit dem auf eine einzelne Unterhaltung zugegriffen werden kann. Der Vorgang zum Starten einer Unterhaltung beginnt jedoch die Unterhaltung und kontaktiert den Bot, wohingegen das Generieren eines Tokens weder eine Unterhaltung startet noch einen Bot kontaktiert. 

Wenn Sie die Unterhaltung sofort beginnen möchten, verwenden Sie den Vorgang zum Starten einer Unterhaltung. Wenn Sie beabsichtigen, das Token an Clients zu verteilen, und möchten, dass diese die Unterhaltung initiieren, verwenden Sie stattdessen den Vorgang [Token generieren](bot-framework-rest-direct-line-1-1-authentication.md#generate-token). 

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Wichtige Begriffe](bot-framework-rest-direct-line-1-1-concepts.md)
- [Authentifizierung](bot-framework-rest-direct-line-1-1-authentication.md)