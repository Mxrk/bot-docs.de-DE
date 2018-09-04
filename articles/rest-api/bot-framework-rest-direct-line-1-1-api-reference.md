---
title: API-Referenz – Direct Line API 1.1 | Microsoft-Dokumentation
description: Informationen Sie zu Headern, HTTP-Statuscodes, Schema, Vorgängen und Objekten in Direct Line API 1.1.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
ms.openlocfilehash: 3569e3bfbb3be51cf9023b4686ed4693e90ed50c
ms.sourcegitcommit: ee63d9dc1944a6843368bdabf5878950229f61d0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2018
ms.locfileid: "42795179"
---
# <a name="api-reference---direct-line-api-11"></a>API-Referenz – Direct Line API 1.1

> [!IMPORTANT]
> Dieser Artikel enthält Referenzinformationen für Direct Line API 1.1. Wenn Sie eine neue Verbindung zwischen Ihrer Clientanwendung und dem Bot erstellen, verwenden Sie stattdessen [Direct Line API 3.0](bot-framework-rest-direct-line-3-0-api-reference.md).

Mit Direct Line API 1.1 Sie können Ihre Clientanwendung für die Kommunikation mit Ihrem Bot aktivieren. Direct Line API 1.1 verwendet die Branchenstandards REST und JSON über HTTPS.

## <a name="base-uri"></a>Basis-URI

Verwenden Sie für den Zugriff auf Direct Line API 1.1 diesen Basis-URI für alle API-Anforderungen:

`https://directline.botframework.com`

## <a name="headers"></a>Header

Zusätzlich zu den Standard-HTTP-Anforderungsheadern muss eine Direct Line-API-Anforderung einen `Authorization`-Header enthalten, der einen Geheimnis oder ein Token zum Authentifizieren des Clients angibt, der die Anforderung sendet. Sie können den `Authorization`-Header mit dem Schema „Bearer“ oder dem Schema „BotConnector“ angeben. 

**Bearer-Schema**:
```http
Authorization: Bearer SECRET_OR_TOKEN
```

**BotConnector-Schema**:
```http
Authorization: BotConnector SECRET_OR_TOKEN
```

Ausführliche Informationen zum Abrufen eines Geheimnisses oder eines Tokens, mit denen der Client seine Direct Line-API-Anforderungen authentifizieren kann, finden Sie unter [Authentifizierung](bot-framework-rest-direct-line-1-1-authentication.md).

## <a name="http-status-codes"></a>HTTP-Statuscodes

Der <a href="http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html" target="_blank">HTTP-Statuscode</a>, der mit jeder Antwort zurückgegeben wird, gibt das Ergebnis der entsprechende Anforderung an. 

| HTTP-Statuscode | Bedeutung |
|----|----|
| 200 | Die Anforderung war erfolgreich. |
| 204 | Die Anforderung war erfolgreich, aber es wurden keine Inhalte zurückgegeben. |
| 400 | Die Anforderung war falsch formatiert oder auf andere Weise fehlerhaft. |
| 401 | Der Client ist nicht zum Ausführen der Anforderung autorisiert. Dieser Statuscode tritt häufig aufgrund eines fehlenden oder falsch formatierten `Authorization`-Headers auf. |
| 403 | Der Client darf den angeforderten Vorgang nicht durchführen. Dieser Statuscode tritt häufig auf, weil der `Authorization`-Header ein ungültiges Token oder einen ungültigen geheimen Schlüssel angibt. |
| 404 | Die angeforderte Ressource wurde nicht gefunden. In der Regel weist dieser Statuscode auf einen ungültigen Anforderungs-URI hin. |
| 500 | Innerhalb des Direct Line-Diensts ist ein interner Serverfehler aufgetreten. |
| 502 | Im Bot ist ein Fehler aufgetreten. Der Bot ist nicht verfügbar oder hat einen Fehler zurückgegeben.  **Dies ist ein häufig auftretender Fehlercode.** |

## <a name="token-operations"></a>Tokenvorgänge 
Verwenden Sie diese Vorgänge zum Erstellen oder Aktualisieren eines Tokens, mit dessen Hilfe ein Client auf eine einzelne Konversationen zugreifen kann.

| Vorgang | BESCHREIBUNG |
|----|----|
| [Token generieren](#generate-token) | Generiert ein Token für eine neue Konversation. | 
| [Token aktualisieren](#refresh-token) | Aktualisiert ein Token. | 

### <a name="generate-token"></a>Generieren eines Tokens
Generiert ein Token, das für eine Konversation gültig ist. 
```http 
POST /api/tokens/conversation
```

| | |
|----|----|
| **Anforderungstext** | – |
| **Rückgabe** | Eine Zeichenfolge, die das Token darstellt | 

### <a name="refresh-token"></a>Token aktualisieren
Aktualisiert das Token. 
```http 
GET /api/tokens/{conversationId}/renew
```

| | |
|----|----|
| **Anforderungstext** | – |
| **Rückgabe** | Eine Zeichenfolge, die das neue Token darstellt | 

## <a name="conversation-operations"></a>Konversationsvorgänge 
Verwenden Sie diese Vorgänge, um eine Konversation mit Ihrem Bot zu öffnen und Nachrichten zwischen Client und Bot auszutauschen.

| Vorgang | BESCHREIBUNG |
|----|----|
| [Konversation starten](#start-conversation) | Startet eine neue Konversation mit dem Bot. | 
| [Get Messages](#get-messages) | Empfängt Nachrichten vom Bot. |
| [Nachricht senden](#send-a-message) | Sendet eine Nachricht an den Bot. | 
| [Datei(en) hochladen und senden](#upload-send-files) | Lädt Dateien hoch und sendet sie als Anlagen. |

### <a name="start-conversation"></a>Starten einer Konversation
Startet eine neue Konversation mit dem Bot. 
```http 
POST /api/conversations
```

| | |
|----|----|
| **Anforderungstext** | – |
| **Rückgabe** | Ein [Conversation](#conversation-object)-Objekt. | 

### <a name="get-messages"></a>Nachrichten abrufen
Ruft Nachrichten vom Bot für eine angegebene Konversation ab. Legen Sie den `watermark`-Parameter im Anforderungs-URI so fest, dass die neueste Nachricht vom Client angezeigt wird. 

```http
GET /api/conversations/{conversationId}/messages?watermark={watermark_value}
```

| | |
|----|----|
| **Anforderungstext** | – |
| **Rückgabe** | Ein [MessageSet](#messageset-object)-Objekt. Die Antwort enthält `watermark` als Eigenschaft des `MessageSet`-Objekts. Clients sollten durch die verfügbaren Nachrichten blättern, indem der `watermark`-Wert erhöht wird, bis keine Nachrichten mehr zurückgegeben werden. | 

### <a name="send-a-message"></a>Nachricht senden
Sendet eine Nachricht an den Bot. 
```http 
POST /api/conversations/{conversationId}/messages
```

| | |
|----|----|
| **Anforderungstext** | Ein [Message](#message-object)-Objekt |
| **Rückgabe** | Es werden keine Daten im Text der Antwort zurückgegeben. Der Dienst antwortet mit einem Statuscode „HTTP 204“, wenn die Nachricht erfolgreich gesendet wurde. Der Client kann die gesendete Nachricht (zusammen mit allen Nachrichten, die vom Bot an den Client gesendet wurden) mithilfe des [Nachrichten abrufen](#get-messages)-Vorgangs abrufen. |

### <a id="upload-send-files">Datei(en) hochladen und senden</a>
Lädt Dateien hoch und sendet sie als Anlagen. Legen Sie den `userId`-Parameter im Anforderungs-URI auf die ID des Benutzers fest, der die Anlagen sendet.
```http 
POST /api/conversations/{conversationId}/upload?userId={userId}
```

| | |
|----|----|
| **Anforderungstext** | Füllen Sie für eine einzelne Anlage den Anforderungstext mit dem Dateiinhalt. Für mehrere Anlagen erstellen Sie einen mehrteiligen Anforderungstext, der einen Teil für jede Anlage und (optional) auch einen Teil für das [Message](#message-object)-Objekt enthält, das als Container für die angegebene(n) Anlage(n) fungieren soll. Weitere Informationen finden Sie unter [Senden einer Nachricht an den Bot](bot-framework-rest-direct-line-1-1-send-message.md). |
| **Rückgabe** | Es werden keine Daten im Text der Antwort zurückgegeben. Der Dienst antwortet mit einem Statuscode „HTTP 204“, wenn die Nachricht erfolgreich gesendet wurde. Der Client kann die gesendete Nachricht (zusammen mit allen Nachrichten, die vom Bot an den Client gesendet wurden) mithilfe des [Nachrichten abrufen](#get-messages)-Vorgangs abrufen. | 

> [!NOTE]
> Hochgeladene Dateien werden nach 24 Stunden gelöscht.

## <a name="schema"></a>Schema

Das Direct Line 1.1-Schema ist eine vereinfachte Kopie des Bot Framework v1-Schemas, das die folgenden Objekte enthält.

### <a name="message-object"></a>Message-Objekt

Definiert eine Nachricht, die ein Client an einen Bot sendet oder von einem Bot empfängt.

| Eigenschaft | Typ | BESCHREIBUNG |
|----|----|----|
| **id** | Zeichenfolge | ID, die die (von Direct Line zugewiesene) Nachricht eindeutig identifiziert. | 
| **conversationId** | Zeichenfolge | ID, die die Konversation identifiziert.  | 
| **created** | Zeichenfolge | Datum und Uhrzeit der Nachrichtenerstellung, ausgedrückt im <a href="https://en.wikipedia.org/wiki/ISO_8601" target="_blank">ISO-8601</a>-Format. | 
| **from** | Zeichenfolge | ID, die den Benutzer identifiziert, der der Absender der Nachricht ist. Wenn Sie eine Nachricht erstellen, sollten Clients diese Eigenschaft auf eine stabile Benutzer-ID festlegen. Obwohl Direct Line eine Benutzer-ID zuweist, wenn kein Wert angegeben ist, führt dies in der Regel zu unerwartetem Verhalten. | 
| **text** | Zeichenfolge | Der Text der Nachricht, die vom Benutzer an den Bot oder vom Bot an den Benutzer gesendet wird. | 
| **channelData** | object | Ein Objekt mit kanalspezifischem Inhalt. Einige Kanäle stellen Funktionen bereit, für die zusätzliche Informationen erforderlich sind, die mit dem Anlagenschema nicht dargestellt werden können. Legen Sie für diese Fälle diese Eigenschaft auf den kanalspezifischen Inhalt fest, wie in der Dokumentation des Kanals definiert. Diese Daten werden zwischen Client und Bot unverändert gesendet. Diese Eigenschaft muss entweder auf ein komplexes Objekt festgelegt oder leer gelassen werden. Legen Sie sie nicht auf eine Zeichenfolge, Zahl oder einen anderen einfachen Typ fest. | 
| **images** | string[] | Array von Zeichenfolgen, das die URL(s) für die Bilder in der Nachricht enthält. In einigen Fällen können Zeichenfolgen in diesem Array relative URLs sein. Wenn eine Zeichenfolge in diesem Array nicht mit „http“ oder „https“ beginnt, setzen Sie `https://directline.botframework.com` vor die Zeichenfolge, um die vollständige URL zu erstellen. | 
| **attachments** | [Attachment](#attachment-object)[] | Array von **Attachment**-Objekten, die die in der Nachricht enthaltenen Anlagen ohne Bilder darstellen. Jedes Objekt im Array enthält eine `url`-Eigenschaft und eine `contentType`-Eigenschaft. In Nachrichten, die ein Client von einem Bot erhält, kann die `url`-Eigenschaft möglicherweise manchmal eine relative URL angeben. Wenn ein `url`-Eigenschaftswert nicht mit „http“ oder „https“ beginnt, setzen Sie `https://directline.botframework.com` vor die Zeichenfolge, um die vollständige URL zu erstellen. | 

Das folgende Beispiel zeigt ein Message-Objekt, das alle möglichen Eigenschaften enthält. In den meisten Fällen muss der Client beim Erstellen einer Nachricht nur die `from`-Eigenschaft und mindestens eine Content-Eigenschaft angeben (z. B. `text`, `images`, `attachments` oder `channelData`).

```json
{
    "id": "CuvLPID4kDb|000000000000000004",
    "conversationId": "CuvLPID4kDb",
    "created": "2016-10-28T21:19:51.0357965Z",
    "from": "examplebot",
    "text": "Hello!",
    "channelData": {
        "examplefield": "abc123"
    },
    "images": [
        "/attachments/CuvLPID4kDb/0.jpg?..."
    ],
    "attachments": [
        {
            "url": "https://example.com/example.docx",
            "contentType": "application/vnd.openxmlformats-officedocument.wordprocessingml.document"
        }, 
        {
            "url": "https://example.com/example.doc",
            "contentType": "application/msword"
        }
    ]
}
```

### <a name="messageset-object"></a>MessageSet-Objekt 
Definiert einen Satz von Nachrichten.<br/><br/>

| Eigenschaft | Typ | BESCHREIBUNG |
|----|----|----|
| **messages** | [Message](#message-object)[] | Array von **Message**-Objekten. |
| **watermark** | Zeichenfolge | Maximales Wasserzeichen von Nachrichten innerhalb des Satzes. Ein Client kann den `watermark`-Wert verwenden, um die letzte beim [Abrufen von Nachrichten vom Bot](bot-framework-rest-direct-line-1-1-receive-messages.md) angezeigte Nachricht anzugeben. |

### <a name="attachment-object"></a>Attachment-Objekt
Definiert eine Anlage ohne Bilder.<br/><br/> 

| Eigenschaft | Typ | BESCHREIBUNG |
|----|----|----|
| **contentType** | Zeichenfolge | Der Medientyp des Inhalts der Anlage. |
| **url** | Zeichenfolge | Die URL für den Inhalt der Anlage. |

### <a name="conversation-object"></a>Conversation-Objekt
Definiert eine Direct Line-Konversation.<br/><br/>

| Eigenschaft | Typ | BESCHREIBUNG |
|----|----|----|
| **conversationId** | Zeichenfolge | ID, die Konversation, für die das angegebene Token gültig ist, eindeutig identifiziert. |
| **token** | Zeichenfolge | Token, das für die angegebene Konversation gültig ist. |
| **expires_in** | number | Die Anzahl der Sekunden bis zum Ablauf des Tokens. |

### <a name="error-object"></a>Error-Objekt
Definiert einen Fehler.<br/><br/> 

| Eigenschaft | Typ | BESCHREIBUNG |
|----|----|----|
| **code** | Zeichenfolge | Fehlercode Einer der folgenden Werte: **MissingProperty**, **MalformedData**, **NotFound**, **ServiceError**, **Internal**, **InvalidRange**, **NotSupported**, **NotAllowed**, **BadCertificate**. |
| **message** | Zeichenfolge | Eine Beschreibung des Fehlers. |
| **statusCode** | number | Statuscode. |

### <a name="errormessage-object"></a>ErrorMessage-Objekt
Fehlernutzlast einer standardisierten Nachricht.<br/><br/> 


|        Eigenschaft        |          Typ          |                                 BESCHREIBUNG                                 |
|------------------------|------------------------|-----------------------------------------------------------------------------|
| <strong>error</strong> | [Fehler](#error-object) | Ein <strong>Error</strong>-Objekt mit Informationen zum Fehler. |

