---
title: Senden und Empfangen von Nachrichten | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie mithilfe des Bot Connector-Diensts Nachrichten senden und empfangen.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 12/13/2017
ms.openlocfilehash: d0b7b3250a62a995113bc9c7e087e2e62af0f413
ms.sourcegitcommit: b78fe3d8dd604c4f7233740658a229e85b8535dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2018
ms.locfileid: "49997067"
---
# <a name="send-and-receive-messages"></a>Senden und Empfangen von Nachrichten

Der Bot Connector-Dienst ermöglicht einem Bot, über verschiedene Kanäle wie Skype, E-Mail, Slack und andere zu kommunizieren. Er ermöglicht die Kommunikation zwischen Bot und Benutzer, indem [Aktivitäten](bot-framework-rest-connector-activities.md) vom Bot zum Kanal und vom Kanal zum Bot weitergeleitet werden. Jede Aktivität enthält Informationen für die Weiterleitung der Nachricht an das passende Ziel zusammen mit Informationen zum Ersteller der Nachricht, dem Kontext der Nachricht und dem Empfänger der Nachricht. In diesem Artikel wird beschrieben, wie mit dem Bot Connector-Dienst Aktivitäten vom Typ **Nachricht** zwischen dem Bot und dem Benutzer in einem Kanal ausgetauscht werden. 

## <a id="create-reply"></a> Beantworten einer Nachricht

### <a name="create-a-reply"></a>Erstellen einer Antwort 

Wenn der Benutzer eine Nachricht an Ihren Bot sendet, empfängt der Bot die Nachricht als [Aktivitätsobjekt][Activity] vom Typ **message**. Um eine Antwort auf die Nachricht eines Benutzers zu erstellen, erstellen Sie ein neues [Aktivitätsobjekt][Activity]. Beginnen Sie mit dem Festlegen dieser Eigenschaften:

| Eigenschaft | Wert |
|----|----|
| conversation | Legen Sie diese Eigenschaft auf den Inhalt der `conversation`-Eigenschaft in der Nachricht des Benutzers fest. |
| from | Legen Sie diese Eigenschaft auf den Inhalt der `recipient`-Eigenschaft in der Nachricht des Benutzers fest. |
| locale | Legen Sie diese Eigenschaft auf den Inhalt der `locale`-Eigenschaft in der Nachricht des Benutzers fest (sofern angegeben). |
| recipient | Legen Sie diese Eigenschaft auf den Inhalt der `from`-Eigenschaft in der Nachricht des Benutzers fest. |
| replyToId | Legen Sie diese Eigenschaft auf den Inhalt der `id`-Eigenschaft in der Nachricht des Benutzers fest. |
| type | Legen Sie diese Eigenschaft auf **message** fest. |

Legen Sie als Nächstes die Eigenschaften fest, die die Informationen enthalten, die Sie dem Benutzer mitteilen möchten. Sie können beispielsweise mit der `text`-Eigenschaft den in der Nachricht anzuzeigenden Text und mit der `speak`-Eigenschaft den durch den Bot zu sprechenden Text festlegen und mit der `attachments`-Eigenschaft Medienanhänge oder Rich Cards angeben, die in die Nachricht eingeschlossen werden sollen. Ausführliche Informationen zu häufig verwendeten Nachrichteneigenschaften finden Sie unter [Erstellen von Nachrichten](bot-framework-rest-connector-create-messages.md).

### <a name="send-the-reply"></a>Senden der Antwort

Verwenden Sie die `serviceUrl`-Eigenschaft in der eingehenden Aktivität zum [Identifizieren des Basis-URI](bot-framework-rest-connector-api-reference.md#base-uri), an den Ihr Bot die Antwort ausgeben sollte. 

Um die Antwort zu senden, geben Sie diese Anforderung aus: 

```http
POST /v3/conversations/{conversationId}/activities/{activityId}
```

Ersetzen Sie in diesem Anforderungs-URI **{conversationId}** durch den Wert der `id`-Eigenschaft des `conversation`-Objekts in der (Antwort-)Aktivität, und ersetzen Sie **{activityId}** durch den Wert der `replyToId`-Eigenschaft in der (Antwort-)Aktivität. Legen Sie den Text der Anforderung auf das [Aktivitätsobjekt][Activity] fest, das Sie für Ihre Antwort erstellt haben.

Das folgende Beispiel zeigt eine Anforderung, die eine einfache Nur-Text-Antwort auf eine Nachricht eines Benutzers sendet. In dieser Beispielanforderung stellt `https://smba.trafficmanager.net/apis` den Basis-URI dar. Der Basis-URI für Anforderungen, die Ihr Bot ausgibt, kann ein anderer sein. Weitere Informationen zum Festlegen des Basis-URI finden Sie in der [API-Referenz](bot-framework-rest-connector-api-reference.md#base-uri).

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
        "name": "Pepper's News Feed"
    },
    "conversation": {
        "id": "abcd1234",
        "name": "Convo1"
   },
   "recipient": {
        "id": "1234abcd",
        "name": "SteveW"
    },
    "text": "My bot's reply",
    "replyToId": "5d5cdc723"
}
```

## <a id="send-message"></a> Senden einer (nicht als Antwort ausgelegten) Nachricht

Ein Großteil der Nachrichten, die Ihr Bot sendet, sind Antworten auf vom Benutzer empfangene Meldungen. Zu bestimmten Zeiten muss der Bot jedoch eine Nachricht an die Konversation senden, die keine direkte Antwort auf eine Nachricht des Benutzers ist. Beispielsweise muss der Bot möglicherweise ein neues Konversationsthema starten oder eine Verabschiedungsnachricht am Ende der Konversation senden. 

Um eine Nachricht an eine Konversation, die keine direkte Antwort auf eine Nachricht des Benutzers ist, zu senden, geben Sie diese Anforderung aus: 

```http
POST /v3/conversations/{conversationId}/activities
```

Ersetzen Sie in diesem Anforderungs-URI **{conversationId}** durch die ID der Konversation. 
    
Legen Sie den Text der Anforderung auf ein [Aktivitätsobjekt][Activity] fest, das Sie für Ihre Antwort erstellen.

> [!NOTE]
> In Bot Framework ist die Anzahl der Nachrichten, die ein Bot senden kann, nicht eingeschränkt. Die meisten Kanäle erzwingen jedoch Drosselungslimits, damit Bots nicht eine große Anzahl von Nachrichten innerhalb kurzer Zeit senden. Wenn die Bots mehrere Nachrichten in schneller Folge senden, kann der Kanal darüber hinaus die Nachrichten nicht immer in der richtigen Reihenfolge rendern.

## <a name="start-a-conversation"></a>Starten einer Unterhaltung

Zu bestimmten Zeiten muss der Bot eine Konversation mit einem oder mehreren Benutzern initiieren. Um eine Konversation mit einem Benutzer in einem Kanal zu starten, muss Ihr Bot dessen Kontoinformationen und die Kontoinformationen des Benutzers in diesem Kanal kennen. 

> [!TIP]
> Wenn Ihr Bot möglicherweise zukünftig Konversationen mit Benutzern starten muss, speichern Sie Benutzerkontoinformationen, weitere relevante Informationen wie Benutzereinstellungen und das Gebietsschema sowie die Dienst-URL (zur Verwendung als Basis-URI in der Anforderung zum Starten der Konversation). 

Um eine Konversation zu starten, geben Sie diese Anforderung aus: 

```http
POST /v3/conversations
```

Legen Sie den Hauptteil der Anforderung auf ein [Conversation][Conversation]-Objekt fest, das die Kontoinformationen des Bots und die Kontoinformationen der Benutzer, die in die Konversation aufgenommen werden sollen, angibt.

> [!NOTE]
> Nicht alle Kanäle unterstützen Gruppenkonversationen. Lesen Sie die Dokumentation des Kanals, um zu ermitteln, ob ein Kanal Gruppenkonversationen unterstützt, und die maximale Anzahl von Teilnehmern herauszufinden, die ein Kanal in einer Konversation zulässt.

Das folgende Beispiel zeigt eine Anforderung zum Starten einer Konversation. In dieser Beispielanforderung stellt `https://smba.trafficmanager.net/apis` den Basis-URI dar. Der Basis-URI für Anforderungen, die Ihr Bot ausgibt, kann ein anderer sein. Weitere Informationen zum Festlegen des Basis-URI finden Sie in der [API-Referenz](bot-framework-rest-connector-api-reference.md#base-uri).

```http
POST https://smba.trafficmanager.net/apis/v3/conversations 
Authorization: Bearer ACCESS_TOKEN
Content-Type: application/json
```

```json
{
    "bot": {
        "id": "12345678",
        "name": "bot's name"
    },
    "isGroup": false,
    "members": [
        {
            "id": "1234abcd",
            "name": "recipient's name"
        }
    ],
    "topicName": "News Alert"
}
```

Wenn die Konversation mit den angegebenen Benutzern eingerichtet wurde, enthält die Antwort eine ID, die die Konversation identifiziert. 

```json
{
    "id": "abcd1234"
}
```

Ihr Bot kann mit dieser Konversations-ID dann an die Benutzer in der Konversation [eine Nachricht senden](#send-message).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Übersicht über Aktivitäten](bot-framework-rest-connector-activities.md)
- [Erstellen von Nachrichten](bot-framework-rest-connector-create-messages.md)

[Activity]: bot-framework-rest-connector-api-reference.md#activity-object
[ConversationAccount]: bot-framework-rest-connector-api-reference.md#conversationaccount-object
[Conversation]: bot-framework-rest-connector-api-reference.md#conversation-object

