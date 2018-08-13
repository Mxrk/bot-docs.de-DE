---
title: Beenden einer Konversation | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie eine Konversation mit Direct Line API v3.0 beenden.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
ms.openlocfilehash: ac984609acfdd8f85088bd47ccded1f45e953b2c
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39302824"
---
# <a name="end-a-conversation"></a>Beenden einer Konversation

Entweder ein Client oder ein Bot kann das Ende einer Direct Line-Konversation durch Senden einer **endOfConversation**-[Aktivität](bot-framework-rest-connector-activities.md) signalisieren. 

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
