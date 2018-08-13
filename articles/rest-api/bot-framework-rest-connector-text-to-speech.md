---
title: Hinzufügen von Sprache zu Nachrichten | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie mithilfe des Bot Connector-Diensts Spracheingabe zu Nachrichten hinzufügen.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
ms.openlocfilehash: 91385fa3e8ae1410679ca5274e40db7fe38bafea
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39304221"
---
# <a name="add-speech-to-messages"></a>Hinzufügen von Spracheingabe zu Nachrichten
> [!div class="op_single_selector"]
> - [.NET](../dotnet/bot-builder-dotnet-text-to-speech.md)
> - [Node.js](../nodejs/bot-builder-nodejs-text-to-speech.md)
> - [REST](../rest-api/bot-framework-rest-connector-text-to-speech.md)

Wenn Sie einen Bot für einen sprachaktivierten Kanal wie Cortana erstellen, können Sie Nachrichten erstellen, die den Text angeben, der von Ihrem Bot gesprochen werden soll. Sie können auch versuchen, den Status des Mikrofons des Clients zu beeinflussen, indem Sie einen [Eingabehinweis](bot-framework-rest-connector-add-input-hints.md) angeben, um festzulegen, ob Ihr Bot Benutzereingaben akzeptiert, erwartet oder ignoriert.

## <a name="specify-text-to-be-spoken-by-your-bot"></a>Angeben des von Ihrem Bot zu sprechenden Texts

Um Text anzugeben, der von Ihrem Bot auf einem sprachaktivierten Kanal gesprochen werden soll, setzen Sie die `speak`-Eigenschaft im [Activity][Activity]-Objekt fest, das Ihre Nachricht darstellt. Sie können die `speak`-Eigenschaft entweder auf eine Nur-Text-Zeichenfolge oder eine als <a href="https://msdn.microsoft.com/en-us/library/hh378377(v=office.14).aspx" target="_blank">Speech Synthesis Markup Language (SSML)</a> formatierte Zeichenfolge verwenden. Bei SSML handelt es sich um eine XML-basierte Markupsprache, mit der Sie verschiedene Eigenschaften der Sprache Ihres Bots steuern können, wie z. B. Stimme, Geschwindigkeit, Lautstärke, Aussprache, Tonhöhe und mehr. 

Die folgende Anforderung sendet eine Nachricht, die den anzuzeigenden Text und den zu sprechenden Text festlegt und angibt, dass der Bot [Benutzereingaben erwartet](bot-framework-rest-connector-add-input-hints.md). Dabei wird die `speak`-Eigenschaft unter Verwendung des <a href="https://msdn.microsoft.com/en-us/library/hh378377(v=office.14).aspx" target="_blank">SSML</a>-Formats festgelegt, um anzugeben, dass das Wort „sicher“ mit mäßiger Betonung gesprochen werden soll. In dieser Beispielanforderung stellt `https://smba.trafficmanager.net/apis` den Basis-URI dar. Der Basis-URI für Anforderungen, die Ihr Bot ausgibt, kann ein anderer sein. Weitere Informationen zum Festlegen des Basis-URI finden Sie unter [API-Referenz](bot-framework-rest-connector-api-reference.md#base-uri).

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
    "text": "Are you sure that you want to cancel this transaction?",
    "speak": "Are you <emphasis level='moderate'>sure</emphasis> that you want to cancel this transaction?",
    "inputHint": "expectingInput",
    "replyToId": "5d5cdc723"
}
```

## <a name="input-hints"></a>Eingabehinweise

Wenn Sie eine Nachricht in einem sprachaktivierten Kanal senden, können Sie versuchen, den Status des Mikrofons des Clients zu beeinflussen, indem Sie auch einen Eingabehinweis einschließen, um anzugeben, ob Ihr Bot Benutzereingaben akzeptiert, erwartet oder ignoriert. Weitere Informationen finden Sie unter [Hinzufügen von Eingabehinweisen zu Nachrichten](bot-framework-rest-connector-add-input-hints.md).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Erstellen von Nachrichten](bot-framework-rest-connector-create-messages.md)
- [Senden und Empfangen von Nachrichten](bot-framework-rest-connector-send-and-receive-messages.md)
- [Hinzufügen von Eingabehinweisen zu Nachrichten](bot-framework-rest-connector-add-input-hints.md)
- <a href="https://msdn.microsoft.com/en-us/library/hh378377(v=office.14).aspx" target="_blank">Speech Synthesis Markup Language (SSML)</a>

[Activity]: bot-framework-rest-connector-api-reference.md#activity-object
