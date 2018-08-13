---
title: Senden einer Nachricht an den Bot | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie mithilfe von Direct Line API v1.1 eine Nachricht an den Bot senden.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
ms.openlocfilehash: 3bc56d08f45ffd1e389a2dca1868a788d65e087e
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39303896"
---
# <a name="send-a-message-to-the-bot"></a>Senden einer Nachricht an den Bot

> [!IMPORTANT]
> In diesem Artikel wird beschrieben, wie Sie mithilfe von Direct Line API 1.1 eine Nachricht an den Bot senden. Wenn Sie eine neue Verbindung zwischen Ihrer Clientanwendung und dem Bot erstellen, verwenden Sie stattdessen [Direct Line API 3.0](bot-framework-rest-direct-line-3-0-send-activity.md).

Durch Verwenden des Direct Line 1.1-Protokolls können Clients Nachrichten mit Bots austauschen. Diese Nachrichten werden in das Schema konvertiert, das der Bot (Bot Framework v1 oder Bot Framework v3) unterstützt. Ein Client kann eine einzelne Nachricht pro Anforderung senden. 

## <a name="send-a-message"></a>Senden einer Nachricht

Um eine Nachricht an den Bot zu senden, muss der Client ein [Message](bot-framework-rest-direct-line-1-1-api-reference.md#message-object)-Objekt erstellen, um die Nachricht zu definieren, und dann an `https://directline.botframework.com/api/conversations/{conversationId}/messages` eine `POST`-Anforderung mit dem Message-Objekt im Text der Anforderung ausgeben.

Die folgenden Codeausschnitte enthalten ein Beispiel der Send Message-Anforderung und -Antwort.

### <a name="request"></a>Anforderung

```http
POST https://directline.botframework.com/api/conversations/abc123/messages
Authorization: Bearer RCurR_XV9ZA.cwA.BKA.iaJrC8xpy8qbOF5xnR2vtCX7CZj0LdjAPGfiCpg4Fv0
[other headers]
```

```json
{
  "text": "hello",
  "from": "user1"
}
```

### <a name="response"></a>response

Wenn die Nachricht an den Bot übermittelt wird, antwortet der Dienst mit einem HTTP-Statuscode, der den Statuscode des Bots widerspiegelt. Wenn der Bot einen Fehler generiert, wird eine HTTP 500-Antwort („Interner Serverfehler“) als Antwort auf eine die Send Message-Anforderung an den Client zurückgegeben. Wenn POST erfolgreich ist, gibt der Dienst den Statuscode „HTTP 204“ zurück. Es werden keine Daten im Text der Antwort zurückgegeben. Nachricht des Clients und alle Nachrichten vom Bot erhalten Sie über [Polling](bot-framework-rest-direct-line-1-1-receive-messages.md). 

```http
HTTP/1.1 204 No Content
[other headers]
```

### <a name="total-time-for-the-send-message-requestresponse"></a>Gesamtzeit für die Send Message-Anforderung/Antwort

Die Gesamtzeit zum Senden (POST) einer Nachricht an eine Direct Line-Konversation ist die Summe aus folgenden Zeiten:

- Übertragungszeit der Übermittlung der HTTP-Anforderung vom Client an den Direct Line-Dienst
- Interne Verarbeitungszeit in Direct Line (in der Regel weniger als 120ms)
- Übertragungszeit vom Direct Line-Dienst an den Bot
- Verarbeitungszeit im Bot
- Übertragungszeit der Übermittlung der HTTP-Antwort zurück an den Client

## <a name="send-attachments-to-the-bot"></a>Senden von Anlagen an den Bot

Manchmal muss ein Client Anlagen an den Bot senden, z. B. Bilder oder Dokumente. Ein Client kann Anlagen an den Bot entweder durch [Angeben der URL(s)](#send-by-url) der Anlage(n) in dem mithilfe von `POST /api/conversations/{conversationId}/messages` gesendeten [Message](bot-framework-rest-direct-line-1-1-api-reference.md#message-object)-Objekt oder durch [Hochladen der Anlage(n)](#upload-attachments) mit `POST /api/conversations/{conversationId}/upload` senden.

## <a id="send-by-url"></a> Senden von Anlagen mittels URL

Um eine oder mehrere Anlagen als Teil des [Message](bot-framework-rest-direct-line-1-1-api-reference.md#message-object)-Objekts unter Verwendung von `POST /api/conversations/{conversationId}/messages` zu senden, geben Sie die URL(s) der Anlage(n) im `images`-Array bzw. `attachments`-Array der Nachricht an.

## <a id="upload-attachments"></a> Senden von Anlagen mittels Upload

Häufig möchte ein Client auf einem Gerät gespeicherte Bilder oder Dokumente an den Bot senden, aber es gibt keine URLs für diese Dateien. In diesem Fall kann ein Client eine `POST /api/conversations/{conversationId}/upload`-Anforderung zum Senden von Anlagen an den Bot per Upload ausgeben. Format und Inhalt der Anforderung richtet sich danach, ob der Client [eine einzelne Anlage](#upload-one-attachment) oder [mehrere Anlagen](#upload-multiple-attachments) sendet.

### <a id="upload-one-attachment"></a> Senden einer einzelnen Anlage per Upload

Wenn Sie eine einzelne Anlage per Upload senden möchten, geben Sie diese Anforderung aus: 

```http
POST https://directline.botframework.com/api/conversations/{conversationId}/upload?userId={userId}
Authorization: Bearer SECRET_OR_TOKEN
Content-Type: TYPE_OF_ATTACHMENT
Content-Disposition: ATTACHMENT_INFO
[other headers]

[file content]
```

Ersetzen Sie in diesem Anforderungs-URI **{conversationId}** durch die ID der Konversation und **{userId}** durch die ID des Benutzers, der die Nachricht sendet. Legen Sie in den Anforderungsheadern `Content-Type` auf den Typ der Anlage und `Content-Disposition` auf den Dateinamen der Anlage fest.

Die folgenden Codeausschnitte enthalten ein Beispiel für die Send (single) Attachment-Anforderung und -Antwort.

#### <a name="request"></a>Anforderung

```http
POST https://directline.botframework.com/api/conversations/abc123/upload?userId=user1
Authorization: Bearer RCurR_XV9ZA.cwA.BKA.iaJrC8xpy8qbOF5xnR2vtCX7CZj0LdjAPGfiCpg4Fv0
Content-Type: image/jpeg
Content-Disposition: name="file"; filename="badjokeeel.jpg"
[other headers]

[JPEG content]
```

#### <a name="response"></a>response

Wenn die Anforderung erfolgreich ist, wird eine Nachricht an den Bot gesendet, sobald der Upload abgeschlossen ist, und der Dienst gibt den Statuscode „HTTP 204“ zurück.

```http
HTTP/1.1 204 No Content
[other headers]
```

### <a id="upload-multiple-attachments"></a> Senden mehrerer Anlagen durch Upload

Um mehrere Anlagen per Upload zu senden, senden Sie eine mehrteilige `POST`-Anforderung an den `/api/conversations/{conversationId}/upload`-Endpunkt. Legen Sie den `Content-Type`-Header der Anforderung auf `multipart/form-data` fest, und fügen Sie den `Content-Type`-Header und den `Content-Disposition`-Header für die einzelnen Teile hinzu, um Typ und Dateinamen der Anlage anzugeben. Legen Sie den `userId`-Parameter im Anforderungs-URI auf die ID des Benutzers fest, der die Nachricht sendet. 

Sie können ein [Message](bot-framework-rest-direct-line-1-1-api-reference.md#message-object)-Objekt in die Anforderung einfügen, indem Sie einen Teil hinzufügen, der den `Content-Type`-Headerwert `application/vnd.microsoft.bot.message` angibt. Dadurch kann der Client die Nachricht, die die Anlagen enthält, anpassen. Wenn die Anforderung eine Nachricht enthält, werden die von anderen Teile der Nutzlast angegebenen Anlagen vor dem Senden als Anlagen zu dieser Nachricht hinzugefügt. 

Die folgenden Codeausschnitte enthalten ein Beispiel für die Send (multiple) Attachments-Anforderung und -Antwort. In diesem Beispiel sendet die Anforderung eine Nachricht, die Text und ein einzelnes Bild als Anlage enthält. Um der Nachricht mehrere Anlagen hinzuzufügen, können zusätzliche Teile zur Anforderung hinzugefügt werden.

#### <a name="request"></a>Anforderung

```http
POST https://directline.botframework.com/api/conversations/abc123/upload?userId=user1
Authorization: Bearer RCurR_XV9ZA.cwA.BKA.iaJrC8xpy8qbOF5xnR2vtCX7CZj0LdjAPGfiCpg4Fv0
Content-Type: multipart/form-data; boundary=----DD4E5147-E865-4652-B662-F223701A8A89
[other headers]

----DD4E5147-E865-4652-B662-F223701A8A89
Content-Type: image/jpeg
Content-Disposition: form-data; name="file"; filename="badjokeeel.jpg"
[other headers]

[JPEG content]

----DD4E5147-E865-4652-B662-F223701A8A89
Content-Type: application/vnd.microsoft.bot.message
[other headers]

{
  "text": "Hey I just IM'd you\n\nand this is crazy\n\nbut here's my webhook\n\nso POST me maybe",
  "from": "user1"
}

----DD4E5147-E865-4652-B662-F223701A8A89
```

#### <a name="response"></a>response

Wenn die Anforderung erfolgreich ist, wird eine Nachricht an den Bot gesendet, sobald der Upload abgeschlossen ist, und der Dienst gibt den Statuscode „HTTP 204“ zurück.

```http
HTTP/1.1 204 No Content
[other headers]
```

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Wichtige Begriffe](bot-framework-rest-direct-line-1-1-concepts.md)
- [Authentifizierung](bot-framework-rest-direct-line-1-1-authentication.md)
- [Starten einer Konversation](bot-framework-rest-direct-line-1-1-start-conversation.md)
- [Empfangen von Nachrichten vom Bot](bot-framework-rest-direct-line-1-1-receive-messages.md)