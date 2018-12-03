---
title: Beenden einer Konversation | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie eine Konversation mit Direct Line API v3.0 beenden.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 12/13/2017
ms.openlocfilehash: f0985f28fd1744bcfb6bf5cea1c2230254670e01
ms.sourcegitcommit: b78fe3d8dd604c4f7233740658a229e85b8535dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2018
ms.locfileid: "50000207"
---
# <a name="end-a-conversation"></a>Beenden einer Konversation

Entweder ein Client oder ein Bot kann das Ende einer Direct Line-Konversation durch Senden einer **endOfConversation**-[Aktivität](bot-framework-rest-connector-activities.md) signalisieren. 

> [!NOTE] 
> Das EndOfConversation-Ereignis wird nur im Cortana-Kanal unterstützt, andere Kanäle implementieren diese Funktion nicht. Die einzelnen Kanäle bestimmen, wie auf eine endOfConversation-Aktivität reagiert wird. Beim Entwerfen eines DirectLine-Clients aktualisieren Sie den Client so, dass er sich entsprechend verhält, z.B. einen Fehler generiert, wenn der Bot eine Aktivität an eine Konversation gesendet hat, die bereits beendet wurde.

## <a name="send-an-endofconversation-activity"></a>Senden einer endOfConversation-Aktivität

Eine **endOfConversation** Aktivität beendet die Kommunikation zwischen Bot und Client. Nach dem Senden einer **endOfConversation**-Aktivität kann der Client mit `HTTP GET` weiterhin [Nachrichten abrufen](bot-framework-rest-direct-line-3-0-receive-activities.md#http-get), jedoch weder der Client noch der Bot kann weitere Nachrichten an die Konversation senden. 

Um eine Konversation zu beenden, geben Sie einfach eine POST-Anforderung zum Senden einer **endOfConversation**-Aktivität aus.

### <a name="request"></a>Anforderung

```http
POST https://directline.botframework.com/v3/directline/conversations/abc123/activities
Authorization: Bearer RCurR_XV9ZA.cwA.BKA.iaJrC8xpy8qbOF5xnR2vtCX7CZj0LdjAPGfiCpg4Fv0
[other headers]
```

```json
{
    "type": "endOfConversation",
    "from": {
        "id": "user1"
    }
}
```

### <a name="response"></a>response

Wenn die Anforderung erfolgreich ist, enthält die Antwort eine ID für die Aktivität, die gesendet wurde.

```http
HTTP/1.1 200 OK
[other headers]
```

```json
{
  "id": "0004"
}
```

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Wichtige Begriffe](bot-framework-rest-direct-line-3-0-concepts.md)
- [Authentifizierung](bot-framework-rest-direct-line-3-0-authentication.md)
- [Senden einer Aktivität an den Bot](bot-framework-rest-direct-line-3-0-send-activity.md)
