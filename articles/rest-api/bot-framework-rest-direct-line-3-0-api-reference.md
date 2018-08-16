---
title: Referenz zur Direct Line-API 3.0 | Microsoft-Dokumentation
description: Erhalten Sie Informationen zu Headern, HTTP-Statuscodes, Schemata, Vorgängen und Objekten in der Direct Line-API 3.0.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
ms.openlocfilehash: 2e47591b04a91ce02cfeb6bd6485080426d201b5
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39301864"
---
# <a name="api-reference---direct-line-api-30"></a>Referenz zur Direct Line-API 3.0

Mit der Direct Line-API 3.0 kann Ihre Clientanwendung mit Ihrem Bot kommunizieren. Die Direct Line-API 3.0 verwendet die Branchenstandards REST und JSON über HTTPS.

## <a name="base-uri"></a>Basis-URI

Verwenden Sie den folgenden Basis-URI für alle API-Anforderungen, um auf die Direct Line-API 3.0 zuzugreifen:

`https://directline.botframework.com`

## <a name="headers"></a>Header

Zusätzlich zu den Standard-HTTP-Anforderungsheadern muss eine Direct Line-API-Anforderung einen `Authorization`-Header enthalten, der einen Geheimnis oder ein Token zum Authentifizieren des Clients angibt, der die Anforderung sendet. Geben Sie den `Authorization`-Header im folgenden Format an:

```http
Authorization: Bearer SECRET_OR_TOKEN
```

Ausführliche Informationen zum Abrufen eines Geheimnisses oder eines Tokens, mit denen der Client seine Direct Line-API-Anforderungen authentifizieren kann, finden Sie unter [Authentifizierung](bot-framework-rest-direct-line-3-0-authentication.md).

## <a name="http-status-codes"></a>HTTP-Statuscodes

Der <a href="http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html" target="_blank">HTTP-Statuscode</a>, der mit jeder Antwort zurückgegeben wird, gibt das Ergebnis der entsprechende Anforderung an. 

| HTTP-Statuscode | Bedeutung |
|----|----|
| 200 | Die Anforderung war erfolgreich. |
| 201 | Die Anforderung war erfolgreich. |
| 202 | Die Anforderung wurde für die Verarbeitung akzeptiert. |
| 204 | Die Anforderung war erfolgreich, aber es wurden keine Inhalte zurückgegeben. |
| 400 | Die Anforderung war falsch formatiert oder auf andere Weise fehlerhaft. |
| 401 | Der Client ist nicht zum Ausführen der Anforderung autorisiert. Dieser Statuscode tritt häufig aufgrund eines fehlenden oder falsch formatierten `Authorization`-Headers auf. |
| 403 | Der Client darf den angeforderten Vorgang nicht durchführen. Wenn in der Anforderung ein Token angegeben wurde, das vorher gültig war, nun jedoch abgelaufen ist, wird die `code`-Eigenschaft des [Error](bot-framework-rest-connector-api-reference.md#error-object)-Objekts, das innerhalb des [ErrorResponse](bot-framework-rest-connector-api-reference.md#errorresponse-object)-Objekts zurückgegeben wird, auf `TokenExpired` festgelegt. |
| 404 | Die angeforderte Ressource wurde nicht gefunden. In der Regel weist dieser Statuscode auf einen ungültigen Anforderungs-URI hin. |
| 500 | Innerhalb des Direct Line-Diensts ist ein interner Serverfehler aufgetreten. |
| 502 | Der Bot ist nicht verfügbar oder hat einen Fehler zurückgegeben. **Dies ist ein häufig auftretender Fehlercode.** |

> [!NOTE]
> Der HTTP-Statuscode 101 wird für den WebSocket-Verbindungspfad verwendet. Vermutlich wird der Code jedoch von Ihrem WebSocket-Client verarbeitet.

### <a name="errors"></a>Errors

Jede Antwort, die einen HTTP-Statuscode im Bereich 4xx oder 5xx angibt, enthält ein [ErrorResponse](bot-framework-rest-connector-api-reference.md#errorresponse-object)-Objekt im Text der Antwort, das Informationen zum Fehler bereitstellt. Wenn Sie eine Fehlerantwort im Bereich 4xx erhalten, überprüfen Sie das **ErrorResponse**-Objekt, um die Fehlerursache zu identifizieren und Ihr Problem zu beheben, bevor Sie die Anforderung erneut senden.

> [!NOTE]
> HTTP-Statuscodes und Werte, die in der `code`-Eigenschaft innerhalb des **ErrorResponse**-Objekts festgelegt werden, sind stabil. Werte in der `message`-Eigenschaft innerhalb des **ErrorResponse**-Objekts können sich im Laufe der Zeit ändern.

In den folgenden Codeausschnitten wird eine Beispielanforderung und die resultierende Fehlermeldung gezeigt.

#### <a name="request"></a>Anforderung

```http
POST https://directline.botframework.com/v3/directline/conversations/abc123/activities
[detail omitted]
```

#### <a name="response"></a>response
```http
HTTP/1.1 502 Bad Gateway
[other headers]
```
```json
{
    "error": {
        "code": "BotRejectedActivity",
        "message": "Failed to send activity: bot returned an error"
    }
}
```

## <a name="token-operations"></a>Tokenvorgänge 
Verwenden Sie diese Vorgänge zum Erstellen oder Aktualisieren eines Tokens, mit dessen Hilfe ein Client auf eine einzelne Konversationen zugreifen kann.

| Vorgang | BESCHREIBUNG |
|----|----|
| [Token generieren](#generate-token) | Generiert ein Token für eine neue Konversation. | 
| [Token aktualisieren](#refresh-token) | Aktualisiert ein Token. | 

### <a name="generate-token"></a>Generieren eines Tokens
Generiert ein Token, das für eine Konversation gültig ist. 
```http 
POST /v3/directline/tokens/generate
```

| | |
|----|----|
| **Anforderungstext** | – |
| **Rückgabe** | Ein [Conversation](#conversation-object)-Objekt. | 

### <a name="refresh-token"></a>Aktualisieren eines Tokens
Aktualisiert das Token. 
```http 
POST /v3/directline/tokens/refresh
```

| | |
|----|----|
| **Anforderungstext** | – |
| **Rückgabe** | Ein [Conversation](#conversation-object)-Objekt. | 

## <a name="conversation-operations"></a>Konversationsvorgänge 
Mit den folgenden Vorgängen starten Sie eine Konversation mit Ihrem Bot und tauschen Aktivitäten zwischen Client und Bot aus.

| Vorgang | BESCHREIBUNG |
|----|----|
| [Konversation starten](#start-conversation) | Startet eine neue Konversation mit dem Bot. | 
| [Konversationsinformationen abrufen](#get-conversation-information) | Ruft Informationen zu einer vorhandenen Konversation ab. Dieser Vorgang generiert eine WebSocket-Stream-URL, mit denen ein Client [erneut eine Verbindung zu einer Konversation herstellen kann](bot-framework-rest-direct-line-3-0-reconnect-to-conversation.md). |
| [Aktivitäten abrufen](#get-activities) | Ruft Aktivitäten des Bots ab. |
| [Aktivität senden](#send-an-activity) | Sendet eine Aktivität an den Bot. | 
| [Datei(en) hochladen und senden](#upload-send-files) | Lädt Dateien hoch und sendet sie als Anlagen. |

### <a name="start-conversation"></a>Starten einer Konversation
Startet eine neue Konversation mit dem Bot. 
```http 
POST /v3/directline/conversations
```

| | |
|----|----|
| **Anforderungstext** | – |
| **Rückgabe** | Ein [Conversation](#conversation-object)-Objekt. | 

### <a name="get-conversation-information"></a>Abrufen von Konversationsinformationen
Ruft Informationen zu einer vorhandenen Konversation ab und generiert eine neue WebSocket-Stream-URL, mit der ein Client [erneut eine Verbindung zu einer Konversation herstellen kann](bot-framework-rest-direct-line-3-0-reconnect-to-conversation.md). Sie können den `watermark`-Parameter im Anforderungs-URI optional so festlegen, dass die neueste Nachricht vom Client angezeigt wird.
```http 
GET /v3/directline/conversations/{conversationId}?watermark={watermark_value}
```

| | |
|----|----|
| **Anforderungstext** | – |
| **Rückgabe** | Ein [Conversation](#conversation-object)-Objekt. | 

### <a name="get-activities"></a>Abrufen von Aktivitäten
Ruft Aktivitäten vom Bot für die angegebene Konversation ab. Sie können den `watermark`-Parameter im Anforderungs-URI optional so festlegen, dass die neueste Nachricht vom Client angezeigt wird. 

```http 
GET /v3/directline/conversations/{conversationId}/activities?watermark={watermark_value}
```

| | |
|----|----|
| **Anforderungstext** | – |
| **Rückgabe** | Ein [ActivitySet](#activityset-object)-Objekt. Die Antwort enthält `watermark` als Eigenschaft des `ActivitySet`-Objekts. Clients sollten durch die verfügbaren Aktivitäten blättern, indem der `watermark`-Wert erhöht wird, bis keine Aktivitäten mehr zurückgegeben werden. | 

### <a name="send-an-activity"></a>Senden einer Aktivität
Sendet eine Aktivität an den Bot. 
```http 
POST /v3/directline/conversations/{conversationId}/activities
```

| | |
|----|----|
| **Anforderungstext** | Ein [Activity](bot-framework-rest-connector-api-reference.md#activity-object)-Objekt. |
| **Rückgabe** | Ein [ResourceResponse](bot-framework-rest-connector-api-reference.md#resourceresponse-object)-Objekt, das eine `id`-Eigenschaft mit der ID der Aktivität enthält, die an den Bot gesendet wurde. | 

### <a id="upload-send-files"></a> Datei(en) hochladen und senden
Lädt Dateien hoch und sendet sie als Anlagen. Legen Sie den `userId`-Parameter im Anforderungs-URI auf die ID des Benutzers fest, der die Anlagen sendet.
```http 
POST /v3/directline/conversations/{conversationId}/upload?userId={userId}
```

| | |
|----|----|
| **Anforderungstext** | Füllen Sie für eine einzelne Anlage den Anforderungstext mit dem Dateiinhalt. Für mehrere Anlagen erstellen Sie einen mehrteiligen Anforderungstext, der einen Teil für jede Anlage und (optional) auch einen Teil für das [Activity](bot-framework-rest-connector-api-reference.md#activity-object)-Objekt enthält, das als Container für die angegebenen Anlagen verwendet werden soll. Weitere Informationen finden Sie unter [Senden einer Aktivität an den Bot](bot-framework-rest-direct-line-3-0-send-activity.md). |
| **Rückgabe** | Ein [ResourceResponse](bot-framework-rest-connector-api-reference.md#resourceresponse-object)-Objekt, das eine `id`-Eigenschaft mit der ID der Aktivität enthält, die an den Bot gesendet wurde. | 

> [!NOTE]
> Hochgeladene Dateien werden nach 24 Stunden gelöscht.

## <a name="schema"></a>Schema

Das Direct Line 3.0-Schema enthält alle Objekte, die im [Schema von Bot Framework v3](bot-framework-rest-connector-api-reference.md#objects) definiert sind, sowie das `ActivitySet`- und `Conversation`-Objekt.

### <a name="activityset-object"></a>ActivitySet-Objekt 
Definiert eine Aktivitätenmenge.<br/><br/>

| Eigenschaft | Typ | BESCHREIBUNG |
|----|----|----|
| **activities** | [Activity](bot-framework-rest-connector-api-reference.md#activity-object)[] | Array von **Aktivität**-Objekten. |
| **watermark** | Zeichenfolge | Maximaler Wasserzeichenwert für Aktivitäten innerhalb der Menge. Ein Client kann mit dem `watermark`-Wert beim [Abrufen von Aktivitäten des Bots](bot-framework-rest-direct-line-3-0-receive-activities.md#http-get) oder beim [Erstellen einer WebSocket-Stream-URL](bot-framework-rest-direct-line-3-0-reconnect-to-conversation.md) die neueste Nachricht kennzeichnen. |

### <a name="conversation-object"></a>Conversation-Objekt
Definiert eine Direct Line-Konversation.<br/><br/>

| Eigenschaft | Typ | BESCHREIBUNG |
|----|----|----|
| **conversationId** | Zeichenfolge | ID, die Konversation, für die das angegebene Token gültig ist, eindeutig identifiziert. |
| **expires_in** | number | Die Anzahl der Sekunden bis zum Ablauf des Tokens. |
| **streamUrl** | Zeichenfolge | URL für den Nachrichtendatenstrom der Konversation. |
| **token** | Zeichenfolge | Token, das für die angegebene Konversation gültig ist. |

### <a name="activities"></a>Aktivitäten

Für jedes [Activity](bot-framework-rest-connector-api-reference.md#activity-object)-Objekt, das ein Client von einem Bot über Direct Line erhält, gilt Folgendes:

- Anlagekarten werden beibehalten.
- URLs für hochgeladene Anhänge werden mithilfe eines privaten Links anonymisiert.
- Die `channelData`-Eigenschaft wird gespeichert, ohne dass Änderungen an ihr vorgenommen werden. 

Clients können [mehrere](bot-framework-rest-direct-line-3-0-receive-activities.md) Aktivitäten eines Bot als Teil eines [ActivitySet](#activityset-object)-Objekts enthalten. 

Wenn ein Client ein [Activity](bot-framework-rest-connector-api-reference.md#activity-object)-Objekt über Direct Line an den Bot sendet, gilt Folgendes:

- Die `type`-Eigenschaft legt den Aktivitätstyp fest (in der Regel **message**).
- Für die `from`-Eigenschaft muss automatisch eine vom Client ausgewählte Benutzer-ID festgelegt werden.
- Anlagen können URLs zu vorhandenen Ressourcen oder URLs enthalten, die über den Direct Line-Anlageendpunkt hochgeladen wurden.
- Die `channelData`-Eigenschaft wird gespeichert, ohne dass Änderungen an ihr vorgenommen werden.

Clients können pro Anforderung eine einzelne Aktivität [senden](bot-framework-rest-direct-line-3-0-send-activity.md). 
