---
title: Senden einer Aktivität an den Bot | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie mithilfe von Direct Line API v3.0 eine Aktivität an den Bot senden.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
ms.openlocfilehash: 3f881f353f04be95ce3785c2fd82b724dd58cb88
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39302800"
---
# <a name="send-an-activity-to-the-bot"></a>Senden einer Aktivität an den Bot

Mit dem Direct Line 3.0-Protokoll können Clients und Bots verschiedene Arten von [Aktivitäten](bot-framework-rest-connector-activities.md) austauschen, einschließlich **message**-Aktivitäten, **typing**-Aktivitäten und benutzerdefinierte Aktivitäten, die der Bot unterstützt. Ein Client kann eine einzelne Aktivität pro Anforderung senden. 

## <a name="send-an-activity"></a>Senden einer Aktivität

Um eine Aktivität an den Bot zu senden, muss der Client ein [Activity](bot-framework-rest-connector-api-reference.md#activity-object)-Objekt erstellen, um die Aktivität zu definieren, und dann an `https://directline.botframework.com/v3/directline/conversations/{conversationId}/activities` eine `POST`-Anforderung mit dem Activity-Objekt im Text der Anforderung ausgeben.

Die folgenden Codeausschnitte enthalten ein Beispiel der Send Activity-Anforderung und -Antwort.

### <a name="request"></a>Anforderung

```http
POST https://directline.botframework.com/v3/directline/conversations/abc123/activities
Authorization: Bearer RCurR_XV9ZA.cwA.BKA.iaJrC8xpy8qbOF5xnR2vtCX7CZj0LdjAPGfiCpg4Fv0
Content-Type: application/json
[other headers]
```

```json
{
    "type": "message",
    "from": {
        "id": "user1"
    },
    "text": "hello"
}
```

### <a name="response"></a>response

Wenn die Aktivität an den Bot übermittelt wird, antwortet der Dienst mit einem HTTP-Statuscode, der den Statuscode des Bots widerspiegelt. Wenn der Bot einen Fehler generiert, wird eine HTTP 502-Antwort („Ungültiges Gateway“) als Antwort auf eine die Send Activity-Anforderung an den Client zurückgegeben. Wenn das Senden (POST) erfolgreich ist, enthält die Antwort eine JSON-Nutzlast, die die ID der Aktivität angibt, die an den Bot gesendet wurde.

```http
HTTP/1.1 200 OK
[other headers]
```

```json
{
    "id": "0001"
}
```

### <a name="total-time-for-the-send-activity-requestresponse"></a>Gesamtzeit für die Send Activity-Anforderung/Antwort

Die Gesamtzeit zum Senden (POST) einer Nachricht an eine Direct Line-Konversation ist die Summe aus folgenden Zeiten:

- Übertragungszeit der Übermittlung der HTTP-Anforderung vom Client an den Direct Line-Dienst
- Interne Verarbeitungszeit in Direct Line (in der Regel weniger als 120ms)
- Übertragungszeit vom Direct Line-Dienst an den Bot
- Verarbeitungszeit im Bot
- Übertragungszeit der Übermittlung der HTTP-Antwort zurück an den Client

## <a name="send-attachments-to-the-bot"></a>Senden von Anlagen an den Bot

Manchmal muss ein Client Anlagen an den Bot senden, z. B. Bilder oder Dokumente. Ein Client kann Anlagen an den Bot entweder durch [Angeben der URL(s)](#send-by-url) der Anlage(n) in dem mithilfe von `POST /v3/directline/conversations/{conversationId}/activities` gesendeten [Activity](bot-framework-rest-connector-api-reference.md#activity-object)-Objekt oder durch [Hochladen der Anlage(n)](#upload-attachments) mit `POST /v3/directline/conversations/{conversationId}/upload` senden.

## <a id="send-by-url"></a> Senden von Anlagen mittels URL

Um einen oder mehrere Anlagen als Teil des [Activity](bot-framework-rest-connector-api-reference.md#activity-object)-Objekts mit `POST /v3/directline/conversations/{conversationId}/activities` zu senden, fügen Sie ein oder mehrere [Attachment](bot-framework-rest-connector-api-reference.md#attachment-object)-Objekte in das Activity-Objekt ein, und legen Sie die `contentUrl`-Eigenschaft jedes Attachment-Objekts auf den HTTP-, HTTPS- oder `data`-URI der Anlage fest.

## <a id="upload-attachments"></a> Senden von Anlagen mittels Upload

Häufig möchte ein Client auf einem Gerät gespeicherte Bilder oder Dokumente an den Bot senden, aber es gibt keine URLs für diese Dateien. In diesem Fall kann ein Client eine `POST /v3/directline/conversations/{conversationId}/upload`-Anforderung zum Senden von Anlagen an den Bot per Upload ausgeben. Format und Inhalt der Anforderung richtet sich danach, ob der Client [eine einzelne Anlage](#upload-one-attachment) oder [mehrere Anlagen](#upload-multiple-attachments) sendet.

### <a id="upload-one-attachment"></a> Senden einer einzelnen Anlage per Upload

Wenn Sie eine einzelne Anlage per Upload senden möchten, geben Sie diese Anforderung aus: 

```http
POST https://directline.botframework.com/v3/directline/conversations/{conversationId}/upload?userId={userId}
Authorization: Bearer SECRET_OR_TOKEN
Content-Type: TYPE_OF_ATTACHMENT
Content-Disposition: ATTACHMENT_INFO
[other headers]

[file content]
```

Ersetzen Sie in diesem Anforderungs-URI **{conversationId}** durch die ID der Konversation und **{userId}** durch die ID des Benutzers, der die Nachricht sendet. Der `userId`-Parameter ist erforderlich. Legen Sie in den Anforderungsheadern `Content-Type` auf den Typ der Anlage und `Content-Disposition` auf den Dateinamen der Anlage fest.

Die folgenden Codeausschnitte enthalten ein Beispiel für die Send (single) Attachment-Anforderung und -Antwort.

#### <a name="request"></a>Anforderung

```http
POST https://directline.botframework.com/v3/directline/conversations/abc123/upload?userId=user1
Authorization: Bearer RCurR_XV9ZA.cwA.BKA.iaJrC8xpy8qbOF5xnR2vtCX7CZj0LdjAPGfiCpg4Fv0
Content-Type: image/jpeg
Content-Disposition: name="file"; filename="badjokeeel.jpg"
[other headers]

[JPEG content]
```

#### <a name="response"></a>response

Ist die Anforderung erfolgreich, wird eine **message**-Aktivität an den Bot gesendet, wenn der Upload abgeschlossen ist, und die Antwort, die der Client empfängt, enthält die ID der gesendeten Aktivität.

```http
HTTP/1.1 200 OK
[other headers]
```

```json
{
  "id": "0003"
}
```

### <a id="upload-multiple-attachments"></a> Senden mehrerer Anlagen durch Upload

Um mehrere Anlagen per Upload zu senden, senden Sie eine mehrteilige `POST`-Anforderung an den `/v3/directline/conversations/{conversationId}/upload`-Endpunkt. Legen Sie den `Content-Type`-Header der Anforderung auf `multipart/form-data` fest, und fügen Sie den `Content-Type`-Header und den `Content-Disposition`-Header für die einzelnen Teile hinzu, um Typ und Dateinamen der Anlage anzugeben. Legen Sie den `userId`-Parameter im Anforderungs-URI auf die ID des Benutzers fest, der die Nachricht sendet. 

Sie können ein [Activity](bot-framework-rest-connector-api-reference.md#activity-object)-Objekt in die Anforderung einfügen, indem Sie einen Teil hinzufügen, der den `Content-Type`-Headerwert `application/vnd.microsoft.activity` angibt. Wenn die Anforderung eine Aktivität enthält, werden die von anderen Teile der Nutzlast angegebenen Anlagen vor dem Senden als Anlagen zu dieser Aktivität hinzugefügt. Wenn die Anforderung keine Aktivität enthält, wird eine leere Aktivität als Container erstellt, in dem die angegebenen Anlagen gesendet werden.

Die folgenden Codeausschnitte enthalten ein Beispiel für die Send (multiple) Attachments-Anforderung und -Antwort. In diesem Beispiel sendet die Anforderung eine Nachricht, die Text und ein einzelnes Bild als Anlage enthält. Um der Nachricht mehrere Anlagen hinzuzufügen, können zusätzliche Teile zur Anforderung hinzugefügt werden.

#### <a name="request"></a>Anforderung

```http
POST https://directline.botframework.com/v3/directline/conversations/abc123/upload?userId=user1
Authorization: Bearer RCurR_XV9ZA.cwA.BKA.iaJrC8xpy8qbOF5xnR2vtCX7CZj0LdjAPGfiCpg4Fv0
Content-Type: multipart/form-data; boundary=----DD4E5147-E865-4652-B662-F223701A8A89
[other headers]

----DD4E5147-E865-4652-B662-F223701A8A89
Content-Type: image/jpeg
Content-Disposition: form-data; name="file"; filename="badjokeeel.jpg"
[other headers]

[JPEG content]

----DD4E5147-E865-4652-B662-F223701A8A89
Content-Type: application/vnd.microsoft.activity
[other headers]

{
  "type": "message",
  "from": {
    "id": "user1"
  },
  "text": "Hey I just IM'd you\n\nand this is crazy\n\nbut here's my webhook\n\nso POST me maybe"
}

----DD4E5147-E865-4652-B662-F223701A8A89
```

#### <a name="response"></a>response

Ist die Anforderung erfolgreich, wird eine „message“-Aktivität an den Bot gesendet, wenn der Upload abgeschlossen ist, und die Antwort, die der Client empfängt, enthält die ID der gesendeten Aktivität.

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
- [Starten einer Konversation](bot-framework-rest-direct-line-3-0-start-conversation.md)
- [Erneutes Verbinden mit einer Konversation](bot-framework-rest-direct-line-3-0-reconnect-to-conversation.md)
- [Empfangen von Aktivitäten vom Bot](bot-framework-rest-direct-line-3-0-receive-activities.md)
- [Beenden einer Konversation](bot-framework-rest-direct-line-3-0-end-conversation.md)
