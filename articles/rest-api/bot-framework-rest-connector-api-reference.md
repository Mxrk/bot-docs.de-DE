---
title: API-Referenz | Microsoft Docs
description: Informationen zu den Headern, Vorgängen, Objekten und Fehlern im Bot Connector-Dienst und Bot State-Dienst.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
ms.openlocfilehash: d76daffcfc4661a87d1efaf85e6bb08e3e999988
ms.sourcegitcommit: e8c513d3af5f0c514cadcbcd0a737a7393405afa
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/20/2018
ms.locfileid: "42756656"
---
# <a name="api-reference"></a>API-Referenz

> [!NOTE]
> Die REST-API ist nicht gleichbedeutend mit dem SDK. Die REST-API wird bereitgestellt, um die standardmäßige REST-Kommunikation zu ermöglichen. Die bevorzugte Methode für die Interaktion mit dem Bot Framework ist jedoch das SDK. 

Innerhalb des Bot Frameworks ermöglicht der Bot Connector-Dienst Ihrem Bot den Austausch von Nachrichten mit Benutzern über Kanäle, die im Bot Framework-Portal konfiguriert sind, und der Bot State-Dienst ermöglicht Ihrem Bot das Speichern und Abrufen von Zustandsdaten, die sich auf die Unterhaltungen beziehen, die Ihr Bot über den Bot Connector-Dienst führt. Beide Dienste verwenden den Branchenstandard REST und JSON über HTTPS.

> [!IMPORTANT]
> Die Bot Framework State-Dienst-API wird nicht für Produktionsumgebungen empfohlen und kann in einem zukünftigen Release veraltet sein. Es wird empfohlen, dass Sie Ihren Bot-Code aktualisieren, um den In-Memory-Speicher für Testzwecke zu nutzen, oder eine der **Azure-Erweiterungen** für Produktions-Bots verwenden. Weitere Informationen finden Sie im Thema **Verwalten von Zustandsdaten** für die Implementierung unter [.NET](~/dotnet/bot-builder-dotnet-state.md) bzw. [Node](~/nodejs/bot-builder-nodejs-state.md).

## <a name="base-uri"></a>Basis-URI

Wenn ein Benutzer eine Nachricht an Ihren Bot sendet, enthält die eingehende Anforderung ein [Activity](#activity-object)-Objekt mit einer `serviceUrl`-Eigenschaft, die den Endpunkt angibt, an den Ihr Bot seine Antwort senden soll. Um auf den Bot Connector-Dienst oder den Bot State-Dienst zuzugreifen, verwenden Sie den Wert `serviceUrl` als Basis-URI für API-Anforderungen. 

Nehmen Sie zum Beispiel an, dass Ihr Bot die folgende Aktivität empfängt, wenn der Benutzer eine Nachricht an den Bot sendet.

```json
{
    "type": "message",
    "id": "bf3cc9a2f5de...",
    "timestamp": "2016-10-19T20:17:52.2891902Z",
    "serviceUrl": "https://smba.trafficmanager.net/apis",
    "channelId": "channel's name/id",
    "from": {
        "id": "1234abcd",
        "name": "user's name"
    },
    "conversation": {
        "id": "abcd1234",
        "name": "conversation's name"
    },
    "recipient": {
        "id": "12345678",
        "name": "bot's name"
    },
    "text": "Haircut on Saturday"
}
```

Die `serviceUrl`-Eigenschaft in der Nachricht des Benutzers gibt an, dass der Bot seine Antwort an den `https://smba.trafficmanager.net/apis`-Endpunkt senden soll. Dies ist der Basis-URI für alle nachfolgenden Anforderungen, die der Bot im Rahmen dieser Unterhaltung ausgibt. Wenn Ihr Bot eine proaktive Nachricht an den Benutzer senden muss, speichern Sie unbedingt den Wert von `serviceUrl`.

Das folgende Beispiel zeigt die Anforderung, die der Bot ausgibt, um auf die Nachricht des Benutzers zu antworten. 

```http
POST https://smba.trafficmanager.net/apis/v3/conversations/abcd1234/activities/bf3cc9a2f5de... 
Authorization: Bearer eyJhbGciOiJIUzI1Ni...
Content-Type: application/json
```

```json
{
    "type": "message",
    "from": {
        "id": "12345678",
        "name": "bot's name"
    },
    "conversation": {
        "id": "abcd1234",
        "name": "conversation's name"
    },
   "recipient": {
        "id": "1234abcd",
        "name": "user's name"
    },
    "text": "I have several times available on Saturday!",
    "replyToId": "bf3cc9a2f5de..."
}
```

## <a name="headers"></a>Header

### <a name="request-headers"></a>Anforderungsheader

Zusätzlich zu den HTTP-Standardanforderungsheadern muss jede API-Anforderung, die Sie ausgeben, einen `Authorization`-Header enthalten, der ein Zugriffstoken für die Authentifizierung Ihres Bots angibt. Geben Sie den `Authorization`-Header im folgenden Format an:

```http
Authorization: Bearer ACCESS_TOKEN
```

Ausführliche Informationen zum Abrufen eines Zugriffstokens für Ihren Bot finden Sie unter [Authentifizieren von Anforderungen von Ihrem Bot mit dem Bot Connector-Dienst](bot-framework-rest-connector-authentication.md#bot-to-connector).

### <a name="response-headers"></a>Antwortheader

Zusätzlich zu den HTTP-Standardantwortheadern enthält jede Antwort einen `X-Correlating-OperationId`-Header. Der Wert dieses Headers ist eine ID, die dem Bot Framework-Protokolleintrag entspricht, der Details zur Anforderung enthält. Jedes Mal, wenn Sie eine Fehlerantwort erhalten, sollten Sie den Wert dieses Headers erfassen. Wenn Sie nicht in der Lage sind, das Problem selbstständig zu lösen, nehmen Sie diesen Wert in die Informationen auf, die Sie dem Supportteam zur Verfügung stellen, wenn Sie das Problem melden.

## <a name="http-status-codes"></a>HTTP-Statuscodes

Der <a href="http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html" target="_blank">HTTP-Statuscode</a>, der mit jeder Antwort zurückgegeben wird, gibt das Ergebnis der entsprechende Anforderung an. 

| HTTP-Statuscode | Bedeutung |
|----|----|
| 200 | Die Anforderung war erfolgreich. |
| 201 | Die Anforderung war erfolgreich. |
| 202 | Die Anforderung wurde für die Verarbeitung akzeptiert. |
| 204 | Die Anforderung war erfolgreich, aber es wurden keine Inhalte zurückgegeben. |
| 400 | Die Anforderung war falsch formatiert oder auf andere Weise fehlerhaft. |
| 401 | Der Bot ist nicht zum Ausführen der Anforderung autorisiert. |
| 403 | Der Bot darf den angeforderten Vorgang nicht ausführen. |
| 404 | Die angeforderte Ressource wurde nicht gefunden. |
| 500 | Interner Serverfehler. |
| 503 | Der Dienst ist nicht verfügbar. |

### <a name="errors"></a>Errors

Jede Antwort, die einen HTTP-Statuscode im Bereich 4xx oder 5xx angibt, enthält ein [ErrorResponse](#errorresponse-object)-Objekt im Text der Antwort, das Informationen zum Fehler bereitstellt. Wenn Sie eine Fehlerantwort im Bereich 4xx erhalten, überprüfen Sie das **ErrorResponse**-Objekt, um die Fehlerursache zu identifizieren und Ihr Problem zu beheben, bevor Sie die Anforderung erneut senden.

## <a name="conversation-operations"></a>Unterhaltungsvorgänge 
Verwenden Sie diese Vorgänge, um Unterhaltung zu erstellen, Nachrichten (Aktivitäten) zu senden und den Inhalt von Unterhaltungen zu verwalten.

| Vorgang | BESCHREIBUNG |
|----|----|
| [Unterhaltung erstellen](#create-conversation) | Erstellt eine neue Unterhaltung. | 
| [Unterhaltung senden](#send-to-conversation) | Sendet eine Aktivität (Nachricht) an das Ende der angegebenen Unterhaltung. | 
| [Auf Aktivität antworten](#reply-to-activity) | Sendet eine Aktivität (Nachricht) an die angegebene Unterhaltung als Antwort auf die angegebene Aktivität. | 
| [Mitglieder der Unterhaltung abrufen](#get-conversation-members) | Ruft die Mitglieder der angegebenen Unterhaltung ab. |
| [Aktivitätsmitglieder abrufen](#get-activity-members) | Ruft die Mitglieder der angegebenen Aktivität innerhalb der angegebenen Unterhaltung ab. | 
| [Aktivität aktualisieren](#update-activity) | Aktualisiert eine vorhandene Aktivität. | 
| [Aktivität löschen](#delete-activity) | Löscht eine vorhandene Aktivität. | 
| [Anlage in Kanal hochladen](#upload-attachment-to-channel) | Lädt eine Anlage direkt in einen Blobspeicher eines Kanals hoch. |

### <a name="create-conversation"></a>Unterhaltung erstellen
Erstellt eine neue Unterhaltung. 
```http 
POST /v3/conversations
```

| | |
|----|----|
| **Anforderungstext** | Ein [Conversation](#conversation-object)-Objekt. |
| **Rückgabe** | Ein [ResourceResponse](#resourceresponse-object)-Objekt. | 

### <a name="send-to-conversation"></a>An Unterhaltung senden
Sendet eine Aktivität (Nachricht) an die angegebene Unterhaltung. Die Aktivität wird am Ende der Unterhaltung gemäß dem Zeitstempel oder der Semantik des Kanals angefügt. Verwenden Sie zum Antworten auf eine bestimmte Nachricht in der Unterhaltung stattdessen [Auf Aktivität antworten](#reply-to-activity).
```http
POST /v3/conversations/{conversationId}/activities
```

| | |
|----|----|
| **Anforderungstext** | Ein [Activity](#activity-object)-Objekt. |
| **Rückgabe** | Ein [Identification](#identification-object)-Objekt. | 

### <a name="reply-to-activity"></a>Auf Aktivität antworten
Sendet eine Aktivität (Nachricht) an die angegebene Unterhaltung als Antwort auf die angegebene Aktivität. Die Aktivität wird als Antwort auf eine andere Aktivität hinzugefügt, wenn der Kanal dies unterstützt. Wenn der Kanal keine geschachtelten Antworten unterstützt, verhält sich dieser Vorgang wie [An Unterhaltung senden](#send-to-conversation).
```http
POST /v3/conversations/{conversationId}/activities/{activityId}
```

| | |
|----|----|
| **Anforderungstext** | Ein [Activity](#activity-object)-Objekt. |
| **Rückgabe** | Ein [Identification](#identification-object)-Objekt. | 

### <a name="get-conversation-members"></a>Mitglieder der Unterhaltung abrufen
Ruft die Mitglieder der angegebenen Unterhaltung ab.
```http
GET /v3/conversations/{conversationId}/members
```

| | |
|----|----|
| **Anforderungstext** | – |
| **Rückgabe** | Ein Array von [ChannelAccount](#channelaccount-object)-Objekten. | 

### <a name="get-activity-members"></a>Aktivitätsmitglieder abrufen
Ruft die Mitglieder der angegebenen Aktivität innerhalb der angegebenen Unterhaltung ab.
```http
GET /v3/conversations/{conversationId}/activities/{activityId}/members
```

| | |
|----|----|
| **Anforderungstext** | – |
| **Rückgabe** | Ein Array von [ChannelAccount](#channelaccount-object)-Objekten. | 

### <a name="update-activity"></a>Aktivität aktualisieren
Einige Kanäle ermöglichen das Bearbeiten einer vorhandenen Aktivität, um den neuen Zustand einer Botunterhaltung widerzuspiegeln. Beispielsweise können Sie Schaltflächen aus einer Nachricht in der Unterhaltung entfernen, nachdem der Benutzer auf eine der Schaltflächen geklickt hat. Bei Erfolg aktualisiert dieser Vorgang die angegebene Aktivität innerhalb der angegebenen Unterhaltung. 
```http
PUT /v3/conversations/{conversationId}/activities/{activityId}
```

| | |
|----|----|
| **Anforderungstext** | Ein [Activity](#activity-object)-Objekt. |
| **Rückgabe** | Ein [Identification](#identification-object)-Objekt. | 

### <a name="delete-activity"></a>Aktivität löschen
Einige Kanäle ermöglichen das Löschen einer vorhandenen Aktivität. Bei Erfolg entfernt dieser Vorgang die angegebene Aktivität aus der angegebenen Unterhaltung.
```http
DELETE /v3/conversations/{conversationId}/activities/{activityId}
```

| | |
|----|----|
| **Anforderungstext** | – |
| **Rückgabe** | Ein HTTP-Statuscode, der das Ergebnis des Vorgangs angibt. Im Text der Antwort wird nichts angegeben. | 

### <a name="upload-attachment-to-channel"></a>Anlage in Kanal hochladen
Lädt eine Anlage für die angegebene Unterhaltung direkt in den Blobspeicher eines Kanals hoch. Auf diese Weise können Sie Daten in einem kompatiblen Speicher speichern. 
```http 
POST /v3/conversations/{conversationId}/attachments
```

| | |
|----|----|
| **Anforderungstext** | Ein [AttachmentUpload](#attachmentupload-object)-Objekt. |
| **Rückgabe** | Ein [ResourceResponse](#resourceresponse-object)-Objekt. Die **id**-Eigenschaft gibt die Anlagen-ID an, die mit dem Vorgang [Anlageninformationen abrufen](#get-attachment-info) und dem Vorgang [Anlage abrufen](#get-attachment) verwendet werden kann. | 

## <a name="attachment-operations"></a>Vorgänge für Anlagen 
Verwenden Sie diese Vorgänge zum Abrufen von Informationen zu einer Anlage sowie für die Binärdaten für die Datei selbst.

| Vorgang | BESCHREIBUNG |
|----|----|
| [Anlageninformationen abrufen](#get-attachment-info) | Ruft Informationen zur angegebenen Anlage ab, einschließlich Dateiname, Dateityp und verfügbare Ansichten (z.B. Originalansicht oder Miniaturansicht). |
| [Anlage abrufen](#get-attachment) | Ruft die angegebene Ansicht der angegebenen Anlage als binären Inhalt ab. | 

### <a name="get-attachment-info"></a>Anlageninformationen abrufen 
Ruft Informationen zur angegebenen Anlage ab, einschließlich Dateiname, Dateityp und verfügbare Ansichten (z.B. Originalansicht oder Miniaturansicht).
```http
GET /v3/attachments/{attachmentId}
```

| | |
|----|----|
| **Anforderungstext** | – |
| **Rückgabe** | Ein [AttachmentInfo](#attachmentinfo-object)-Objekt. | 

### <a name="get-attachment"></a>Anlage abrufen
Ruft die angegebene Ansicht der angegebenen Anlage als binären Inhalt ab.
```http
GET /v3/attachments/{attachmentId}/views/{viewId}
```

| | |
|----|----|
| **Anforderungstext** | – |
| **Rückgabe** | Binärer Inhalt, der die angegebene Ansicht der angegebenen Anlage darstellt. | 

## <a name="state-operations"></a>Zustandsvorgänge
Verwenden Sie diese Vorgänge zum Speichern und Abrufen von Zustandsdaten.

| Vorgang | BESCHREIBUNG |
|----|----|
| [Benutzerdaten festlegen](#set-user-data) | Speichert Zustandsdaten für einen bestimmten Benutzer in einem Kanal. | 
| [Unterhaltungsdaten festlegen](#set-conversation-data) | Speichert Zustandsdaten für eine bestimmte Unterhaltung in einem Kanal. | 
| [Private Unterhaltungsdaten festlegen](#set-private-conversation-data) | Speichert Zustandsdaten für einen bestimmten Benutzer im Kontext einer bestimmten Unterhaltung in einem Kanal. | 
| [Benutzerdaten abrufen](#get-user-data) | Ruft Zustandsdaten ab, die zuvor für einen bestimmten Benutzer in allen Unterhaltungen in einem Kanal gespeichert wurden. | 
| [Unterhaltungsdaten abrufen](#get-conversation-data) | Ruft Zustandsdaten ab, die zuvor für eine bestimmte Unterhaltung in einem Kanal gespeichert wurden. | 
| [Private Unterhaltungsdaten abrufen](#get-private-conversation-data) | Ruft Zustandsdaten ab, die zuvor für einen bestimmten Benutzer im Kontext einer bestimmten Unterhaltung in einem Kanal gespeichert wurden. | 
| [Zustand für Benutzer löschen](#delete-state-for-user) | Löscht Zustandsdaten, die zuvor für einen Benutzer mit dem Vorgang [Benutzerdaten festlegen](#set-user-data) oder [Private Unterhaltungsdaten festlegen](#set-private-conversation-data) gespeichert wurden. | 

### <a name="set-user-data"></a>Benutzerdaten festlegen
Speichert Zustandsdaten für den angegebenen Benutzer im angegebenen Kanal.
```http
POST /v3/botstate/{channelId}/users/{userId} 
```

| | |
|----|----|
| **Anforderungstext** | Ein [BotData](#botdata-object)-Objekt. |
| **Rückgabe** | Ein [BotData](#botdata-object)-Objekt. | 

### <a name="set-conversation-data"></a>Unterhaltungsdaten festlegen
Speichert Zustandsdaten für die angegebene Unterhaltung im angegebenen Kanal.
```http
POST /v3/botstate/{channelId}/conversations/{conversationId}
```

| | |
|----|----|
| **Anforderungstext** | Ein [BotData](#botdata-object)-Objekt. |
| **Rückgabe** | Ein [BotData](#botdata-object)-Objekt. | 

### <a name="set-private-conversation-data"></a>Private Unterhaltungsdaten festlegen
Speichert Zustandsdaten für den angegebenen Benutzer im Kontext der angegebenen Unterhaltung im angegebenen Kanal.
```http
POST /v3/botstate/{channelId}/conversations/{conversationId}/users/{userId} 
```

| | |
|----|----|
| **Anforderungstext** | Ein [BotData](#botdata-object)-Objekt. |
| **Rückgabe** | Ein [BotData](#botdata-object)-Objekt. | 

### <a name="get-user-data"></a>Benutzerdaten abrufen
Ruft Zustandsdaten ab, die zuvor für den angegebenen Benutzer in allen Unterhaltungen in einem angegebenen Kanal gespeichert wurden.
```http
GET /v3/botstate/{channelId}/users/{userId} 
```

| | |
|----|----|
| **Anforderungstext** | – |
| **Rückgabe** | Ein [BotData](#botdata-object)-Objekt. | 

### <a name="get-conversation-data"></a>Unterhaltungsdaten abrufen
Ruft Zustandsdaten ab, die zuvor für die angegebene Unterhaltung im angegebenen Kanal gespeichert wurden.
```http
GET /v3/botstate/{channelId}/conversations/{conversationId} 
```

| | |
|----|----|
| **Anforderungstext** | – |
| **Rückgabe** | Ein [BotData](#botdata-object)-Objekt. | 

### <a name="get-private-conversation-data"></a>Private Unterhaltungsdaten abrufen
Ruft Zustandsdaten ab, die zuvor für den angegebenen Benutzer im Kontext der angegebenen Unterhaltung im angegebenen Kanal gespeichert wurden.
```http
GET /v3/botstate/{channelId}/conversations/{conversationId}/users/{userId} 
```

| | |
|----|----|
| **Anforderungstext** | – |
| **Rückgabe** | Ein [BotData](#botdata-object)-Objekt. | 

### <a name="delete-state-for-user"></a>Zustand für Benutzer löschen
Löscht Zustandsdaten, die zuvor für den angegebenen Benutzer im angegebenen Kanal mit dem Vorgang [Benutzerdaten festlegen](#set-user-data) oder [Private Unterhaltungsdaten festlegen](#set-private-conversation-data) gespeichert wurden.
```http
DELETE /v3/botstate/{channelId}/users/{userId} 
```

| | |
|----|----|
| **Anforderungstext** | – |
| **Rückgabe** | Ein Array von Zeichenfolgen (IDs). | 

## <a id="objects"></a>-Schema

Das Schema definiert das Objekt und seine Eigenschaften, die Ihr Bot für die Kommunikation mit einem Benutzer verwenden kann. 

| Objekt | BESCHREIBUNG |
| ---- | ---- |
| [Activity-Objekt](#activity-object) | Definiert eine Nachricht, die zwischen Bot und Benutzer ausgetauscht wird. |
| [AnimationCard-Objekt](#animationcard-object) | Definiert eine Rich Card, die animierte GIF-Dateien oder kurze Videos wiedergeben kann. |
| [Attachment-Objekt](#attachment-object) | Definiert zusätzliche Informationen, die in die Nachricht eingeschlossen werden sollen. Eine Anlage kann eine Mediendatei (z.B. Audio, Video, Bild, Datei) oder eine Rich Card sein. |
| [AttachmentData-Objekt](#attachmentdata-object) |Beschreibt Anlagendaten. |
| [AttachmentInfo-Objekt](#attachmentinfo-object) | Beschreibt eine Anlage. |
| [AttachmentView-Objekt](#attachmentview-object) | Definiert eine Anlagenansicht. |
| [AttachmentUpload-Objekt](#attachmentupload-object) | Definiert eine hochzuladende Anlage. |
| [AudioCard-Objekt](#audiocard-object) | Definiert eine Rich Card, die eine Audiodatei wiedergeben kann. |
| [BotData-Objekt](#botdata-object) | Definiert Zustandsdaten für einen Benutzer, eine Unterhaltung oder einen Benutzer im Kontext einer bestimmten Unterhaltung, die mithilfe des Bot State-Diensts gespeichert werden. |
| [CardAction-Objekt](#cardaction-object) | Definiert eine auszuführende Aktion. |
| [CardImage-Objekt](#cardimage-object) | Definiert ein Bild, das auf einer Rich Card angezeigt werden soll. |
| [ChannelAccount-Objekt](#channelaccount-object) | Definiert einen Bot oder ein Benutzerkonto im Kanal. |
| [Conversation-Objekt](#conversation-object) | Definiert eine Unterhaltung einschließlich des Bots und der Benutzer, die in der Unterhaltung enthalten sind. |
| [ConversationAccount-Objekt](#conversationaccount-object) | Definiert eine Unterhaltung in einem Kanal. |
| [ConversationParameters-Objekt](#conversationparameters-object) | Definieren von Parametern zum Erstellen einer neuen Unterhaltung |
| [ConversationReference-Objekt](#conversationreference-object) | Definiert einen bestimmten Zeitpunkt in einer Unterhaltung. |
| [ConversationResourceResponse-Objekt](#conversationresourceresponse-object) | Eine Antwort, die eine Ressource enthält. |
| [Entity-Objekt](#entity-object) | Definiert ein Entitätsobjekt. |
| [Error-Objekt](#error-object) | Definiert einen Fehler. |
| [ErrorResponse-Objekt](#errorresponse-object) | Definiert eine HTTP-API-Antwort. |
| [Fact-Objekt](#fact-object) | Definiert ein Schlüssel-Wert-Paar, das einen Fakt enthält. |
| [Geocoordinates-Objekt](#geocoordinates-object) | Definiert einen geografischen Standort mithilfe von WSG84-Koordinaten (World Geodetic System). |
| [HeroCard-Objekt](#herocard-object) | Definiert eine Rich Card mit einem großen Bild, Titel, Text und Aktionsschaltflächen. |
| [Identification-Objekt](#identification-object) | Identifiziert eine Ressource. |
| [MediaEventValue-Objekt](#mediaeventvalue-object) |Zusätzlicher Parameter für Medienereignisse.|
| [MediaUrl-Objekt](#mediaurl-object) | Definiert die URL zur Quelle einer Mediendatei. |
| [Mention-Objekt](#mention-object) | Definiert einen Benutzer oder Bot, der in der Unterhaltung erwähnt wurde. |
| [MessageReaction-Objekt](#messagereaction-object) | Definiert eine Reaktion auf eine Nachricht. |
| [Place-Objekt](#place-object) | Definiert einen Ort, der in der Unterhaltung erwähnt wurde. |
| [ReceiptCard-Objekt](#receiptcard-object) | Definiert eine Rich Card, die eine Quittung für einen Kauf enthält. |
| [ReceiptItem-Objekt](#receiptitem-object) | Definiert eine Position in einer Quittung. |
| [ResourceResponse-Objekt](#resourceresponse-object) | Definiert eine Ressource. |
| [SignInCard-Objekt](#signincard-object) | Definiert eine Rich Card, die es einem Benutzer ermöglicht, sich bei einem Dienst anzumelden. |
| [SuggestedActions-Objekt](#suggestedactions-object) | Definiert die Optionen, unter denen ein Benutzer auswählen kann. |
| [ThumbnailCard-Objekt](#thumbnailcard-object) | Definiert eine Rich Card mit einem Miniaturbild, Titel, Text und Aktionsschaltflächen. |
| [ThumbnailUrl-Objekt](#thumbnailurl-object) | Definiert die URL zur Quelle eines Bilds. |
| [VideoCard-Objekt](#videocard-object) | Definiert eine Rich Card, die Videos wiedergeben kann. |


### <a name="activity-object"></a>Activity-Objekt
Definiert eine Nachricht, die zwischen Bot und Benutzer ausgetauscht wird.<br/><br/> 

| Eigenschaft | Typ | BESCHREIBUNG |
|----|----|----|
| **action** | Zeichenfolge | Die anzuwendende Aktion oder die Aktion, die angewendet wurde. Verwenden Sie die **type**-Eigenschaft, um den Kontext für die Aktion zu ermitteln. Wenn beispielsweise **type** **contactRelationUpdate** ist, wäre der Wert der **action**-Eigenschaft **add**, wenn der Benutzer Ihren Bot seiner Kontaktliste hinzugefügt hat, oder **remove**, wenn er Ihren Bot aus seiner Kontaktliste entfernt hat. |
| **attachments** | [Attachment](#attachment-object)[] | Ein Array von **Attachment**-Objekten, die zusätzliche Informationen definieren, die in die Nachricht eingeschlossen werden sollen. Jede dieser Anlagen kann eine Mediendatei (z.B. Audio, Video, Bild, Datei) oder eine Rich Card sein. |
| **attachmentLayout** | Zeichenfolge | Das Layout der Rich Card-**Anlagen**, die die Nachricht enthält. Einer der folgenden Werte: **carousel**, **list**. Weitere Informationen zu Rich Card-Anlagen finden Sie unter [Hinzufügen von Rich Card-Anlagen zu Nachrichten](bot-framework-rest-connector-add-rich-cards.md). |
| **channelData** | object | Ein Objekt mit kanalspezifischem Inhalt. Einige Kanäle stellen Funktionen bereit, für die zusätzliche Informationen erforderlich sind, die mit dem Anlagenschema nicht dargestellt werden können. Legen Sie für diese Fälle diese Eigenschaft auf den kanalspezifischen Inhalt fest, wie in der Dokumentation des Kanals definiert. Weitere Informationen finden Sie unter [Implementieren von kanalspezifischer Funktionalität](bot-framework-rest-connector-channeldata.md). |
| **channelId** | Zeichenfolge | Eine ID, die den Kanal eindeutig identifiziert. Wird vom Kanal festgelegt. | 
| **Unterhaltung** | [ConversationAccount](#conversationaccount-object) | Ein **ConversationAccount**-Objekt, das die Unterhaltung definiert, zu der die Aktivität gehört. |
| **code** | Zeichenfolge | Code, der angibt, warum die Unterhaltung beendet wurde. |
| **entities** | object[] | Ein Array von Objekten, das die Entitäten darstellt, die in der Nachricht erwähnt wurden. Objekte in diesem Array können ein beliebiges <a href="http://schema.org/" target="_blank">Schema.org</a>-Objekt sein. Zum Beispiel kann das Array [Mention](#mention-object)-Objekte enthalten, die jemanden identifizieren, der in der Unterhaltung erwähnt wurde, und [Place](#place-object)-Objekte, die einen Ort identifizieren, der in der Unterhaltung erwähnt wurde. |
| **from** | [ChannelAccount](#channelaccount-object) | Ein **ChannelAccount**-Objekt, das den Absender der Nachricht angibt. |
| **historyDisclosed** | boolean | Ein Flag, das angibt, ob der Verlauf offengelegt wird. Der Standardwert ist **false**. |
| **id** | Zeichenfolge | Eine ID, die die Aktivität im Kanal eindeutig identifiziert. | 
| **inputHint** | Zeichenfolge | Ein Wert, der angibt, ob Ihr Bot Benutzereingaben akzeptiert, erwartet oder ignoriert, nachdem die Nachricht an den Client gesendet wurde. Einer der folgenden Werte: **acceptingInput**, **expectingInput**, **ignoringInput**. |
| **locale** | Zeichenfolge | Das Gebietsschema der Sprache, die zum Anzeigen von Text in der Nachricht verwendet werden soll (im Format `<language>-<country>`). Der Kanal verwendet diese Eigenschaft, um die Sprache des Benutzers anzugeben, sodass Ihr Bot Anzeigezeichenfolgen in dieser Sprache angeben kann. Der Standardwert ist **en-US**. |
| **localTimestamp** | Zeichenfolge | Datum und Uhrzeit des Sendens der Nachricht in der lokalen Zeitzone, ausgedrückt im <a href="https://en.wikipedia.org/wiki/ISO_8601" target="_blank">ISO-8601</a>-Format. |
| **membersAdded** | [ChannelAccount](#channelaccount-object)[] | Ein Array von **ChannelAccount**-Objekten, das die Liste der Benutzer darstellt, die an der Unterhaltung teilgenommen haben. Nur vorhanden, wenn der **type** der Aktivität „conversationUpdate“ ist und Benutzer an der Unterhaltung teilgenommen haben. | 
| **membersRemoved** | [ChannelAccount](#channelaccount-object)[] | Ein Array von **ChannelAccount**-Objekten, das die Liste der Benutzer darstellt, die die Unterhaltung verlassen haben. Nur vorhanden, wenn der **type** der Aktivität „conversationUpdate“ ist und Benutzer die Unterhaltung verlassen haben. | 
| **name** | Zeichenfolge | Der Name des aufzurufenden Vorgangs oder der Name des Ereignisses. |
| **recipient** | [ChannelAccount](#channelaccount-object) | Ein **ChannelAccount**-Objekt, das den Empfänger der Nachricht angibt. |
| **relatesTo** | [ConversationReference](#conversationreference-object) | Ein **ConversationReference**-Objekt, das einen bestimmten Zeitpunkt in einer Unterhaltung definiert. |
| **replyToId** | Zeichenfolge | Die ID der Nachricht, auf die diese Nachricht eine Antwort ist. Um auf eine Nachricht zu antworten, die der Benutzer gesendet hat, legen Sie diese Eigenschaft auf die ID der Nachricht des Benutzers fest. Nicht alle Kanäle unterstützen Threadantworten. In diesen Fällen ignoriert der Kanal diese Eigenschaft und verwendet zeitorientierte Semantik (Zeitstempel), um die Nachricht an die Unterhaltung anzufügen. | 
| **serviceUrl** | Zeichenfolge | Die URL, die den Dienstendpunkt des Kanals angibt. Wird vom Kanal festgelegt. | 
| **speak** | Zeichenfolge | Text, der von Ihrem Bot in einem sprachaktivierten Kanal gesprochen werden soll. Um verschiedene Eigenschaften der Sprache Ihres Bots zu steuern (z.B. Stimme, Rate, Lautstärke, Aussprache und Tonhöhe), geben Sie diese Eigenschaft im <a href="https://msdn.microsoft.com/en-us/library/hh378377(v=office.14).aspx" target="_blank">SSML-Format (Speech Synthesis Markup Language)</a> an. |
| **suggestedActions** | [SuggestedActions](#suggestedactions-object) | Ein **SuggestedActions**-Objekt, das die Optionen definiert, aus denen der Benutzer auswählen kann. |
| **summary** | Zeichenfolge | Eine Zusammenfassung der Informationen, die die Nachricht enthält. Für eine Nachricht, die in einem E-Mail-Kanal gesendet wird, kann diese Eigenschaft z.B. die ersten 50 Zeichen der E-Mail-Nachricht angeben. |
| **text** | Zeichenfolge | Der Text der Nachricht, die vom Benutzer an den Bot oder vom Bot an den Benutzer gesendet wird. In der Dokumentation des Kanals finden Sie die Einschränkungen, die für den Inhalt dieser Eigenschaft gelten. |
| **textFormat** | Zeichenfolge | Das Format des **text** der Nachricht. Einer der folgenden Werte: **markdown**, **plain**, **xml**. Weitere Informationen zum Textformat finden Sie unter [Erstellen von Nachrichten](bot-framework-rest-connector-create-messages.md). |
| **timestamp** | Zeichenfolge | Datum und Uhrzeit des Sendens der Nachricht in der UTC-Zeitzone, ausgedrückt im <a href="https://en.wikipedia.org/wiki/ISO_8601" target="_blank">ISO-8601</a>-Format. |
| **topicName** | Zeichenfolge | Das Thema der Unterhaltung, zu der die Aktivität gehört. |
| **type** | Zeichenfolge | Der Typ der Aktivität. Einer der folgenden Werte: **contactRelationUpdate**, **conversationUpdate**, **deleteUserData**, **message**, **typing**, **endOfConversation**. Weitere Informationen zu Aktivitätstypen finden Sie unter [Aktivitäten: Überblick](bot-framework-rest-connector-activities.md). |
| **value** | object | Ein Wert mit unbestimmtem Ende. |

<a href="#objects">Zurück zur Schematabelle</a>

### <a name="animationcard-object"></a>AnimationCard-Objekt
Definiert eine Rich Card, die animierte GIF-Dateien oder kurze Videos wiedergeben kann.<br/><br/> 

| Eigenschaft | Typ | BESCHREIBUNG |
|----|----|----|
| **autoloop** | boolean | Ein Flag, das angibt, ob die Liste der animierte GIF-Dateien wiedergeben werden soll, nachdem die letzte Datei wiedergeben wurde. Legen Sie diese Eigenschaft auf **TRUE** fest, um die Animation automatisch wiederzugeben, andernfalls auf **FALSE**. Der Standardwert lautet **true**. |
| **autostart** | boolean | Ein Flag, das angibt, ob die Animation automatisch wiedergegeben werden soll, wenn die Rich Card angezeigt wird. Legen Sie diese Eigenschaft auf **TRUE** fest, um die Animation automatisch wiederzugeben, andernfalls auf **FALSE**. Der Standardwert lautet **true**. |
| **buttons** | [CardAction](#cardaction-object)[] | Ein Array von **CardAction**-Objekten, das es dem Benutzer ermöglicht, mindestens eine Aktion auszuführen. Der Kanal bestimmt die Anzahl der Schaltflächen, die Sie angeben können. |
| **image** | [ThumbnailUrl](#thumbnailurl-object) | Ein **ThumbnailUrl**-Objekt, das das auf der Rich Card anzuzeigende Bild angibt. |
| **media** | [MediaUrl](#mediaurl-object)[] | Ein Array von **MediaUrl**-Objekten, das die Liste der animierten GIF-Dateien angibt, die wiedergegeben werden sollen. |
| **shareable** | boolean | Ein Flag, das angibt, ob die Animation für andere Benutzer freigegeben werden kann. Legen Sie diese Eigenschaft auf **TRUE** fest, wenn die Animation freigegeben werden kann, andernfalls auf **FALSE**. Der Standardwert lautet **true**. |
| **subtitle** | Zeichenfolge | Der Untertitel, der unter dem Titel der Rich Card angezeigt werden soll. |
| **text** | Zeichenfolge | Eine Beschreibung oder Eingabeaufforderung, die unter dem Titel oder Untertitel der Rich Card angezeigt werden soll. |
| **title** | Zeichenfolge | Der Titel der Rich Card. |
| **value** | object | Zusätzlicher Parameter für diese Rich Card. |

<a href="#objects">Zurück zur Schematabelle</a>

### <a name="attachment-object"></a>Attachment-Objekt
Definiert zusätzliche Informationen, die in die Nachricht eingeschlossen werden sollen. Eine Anlage kann eine Mediendatei (z.B. Audio, Video, Bild, Datei) oder eine Rich Card sein.<br/><br/> 

| Eigenschaft | Typ | BESCHREIBUNG |
|----|----|----|
| **contentType** | Zeichenfolge | Der Medientyp des Inhalts der Anlage. Legen Sie diese Eigenschaft für Mediendateien auf bekannten Medientypen fest, z.B. auf **image/png**, **audio/wav** und **video/mp4**. Legen Sie diese Eigenschaft für Rich Cards auf einen dieser anbieterspezifischen Typen fest:<ul><li>**application/vnd.microsoft.card.adaptive**: Rich Cards, die eine beliebige Kombination von Text, Sprache, Bildern, Schaltflächen und Eingabefeldern enthalten können. Legen Sie die **content**-Eigenschaft auf ein <a href="http://adaptivecards.io/documentation/#create-cardschema" target="_blank">AdaptiveCard</a>-Objekt fest.</li><li>**application/vnd.microsoft.card.animation**: Eine Rich Card, die eine Animation wiedergibt. Legen Sie die **content**-Eigenschaft auf ein [AnimationCard](#animationcard-object)-Objekt fest.</li><li>**application/vnd.microsoft.card.audio**: Eine Rich Card, die Audiodateien wiedergibt. Legen Sie die **content**-Eigenschaft auf ein [AudioCard](#audiocard-object)-Objekt fest.</li><li>**application/vnd.microsoft.card.video**: Eine Rich Card, die Videos wiedergibt. Legen Sie die **content**-Eigenschaft auf ein [VideoCard](#videocard-object)-Objekt fest.</li><li>**application/vnd.microsoft.card.hero**: Eine Hero Card. Legen Sie die **content**-Eigenschaft auf ein [HeroCard](#herocard-object)-Objekt fest.</li><li>**application/vnd.microsoft.card.thumbnail**: Eine Thumbnail Card. Legen Sie die **content**-Eigenschaft auf ein [ThumbnailCard](#thumbnailcard-object)-Objekt fest.</li><li>**application/vnd.microsoft.com.card.receipt**: Eine Receipt Card. Legen Sie die **content**-Eigenschaft auf ein [ReceiptCard](#receiptcard-object)-Objekt fest.</li><li>**application/vnd.microsoft.com.card.signin**: Eine Sign In Card eines Benutzers. Legen Sie die **content**-Eigenschaft auf ein [SignInCard](#signincard-object)-Objekt fest.</li></ul> |
| **contentUrl** | Zeichenfolge | Die URL für den Inhalt der Anlage. Wenn die Anlage z.B. ein Bild ist, legen Sie **contentUrl** auf die URL fest, die den Speicherort des Bilds darstellt. Unterstützte Protokolle sind: HTTP, HTTPS, Datei und Daten. |
| **content** | object | Der Inhalt der Anlage. Wenn die Anlage einer Rich Card ist, legen Sie diese Eigenschaft auf das Rich Card-Objekt fest. Diese Eigenschaft und die **ContentUrl**-Eigenschaft schließen sich gegenseitig aus. |
| **name** | Zeichenfolge | Der Name der Anlage. |
| **thumbnailUrl** | Zeichenfolge | Die URL zu einer Miniaturansicht, die der Kanal verwenden kann, wenn die Verwendung einer anderen, kleineren Form von **content** oder **contentUrl** unterstützt wird. Wenn Sie beispielsweise **contentType** auf **application/word** und **contentUrl** auf den Speicherort des Word-Dokuments festlegen, können Sie eine Miniaturansicht einfügen, die das Dokument darstellt. Der Kanal könnte die Miniaturansicht anstelle des Dokuments anzeigen. Klickt der Benutzer auf das Bild, öffnet der Kanal das Dokument. |

<a href="#objects">Zurück zur Schematabelle</a>

### <a name="attachmentdata-object"></a>AttachmentData-Objekt 
Beschreibt Anlagendaten.

| Eigenschaft | Typ | BESCHREIBUNG |
|----|----|----|
| **name** | Zeichenfolge | Der Name der Anlage. |
| **originalBase64** | Zeichenfolge | Der Anlageninhalt. |
| **thumbnailBase64** | Zeichenfolge | Der Miniaturansichtinhalt der Anlage. |
| **type** | Zeichenfolge | Der Inhaltstyp der Anlage. |

### <a name="attachmentinfo-object"></a>AttachmentInfo-Objekt
Beschreibt eine Anlage.<br/><br/> 

| Eigenschaft | Typ | BESCHREIBUNG |
|----|----|----|
| **name** | Zeichenfolge | Der Name der Anlage. |
| **type** | Zeichenfolge | Der Inhaltstyp der Anlage. |
| **Ansichten** | [AttachmentView](#attachmentview-object)[] | Ein Array aus **AttachmentView**-Objekten, die die für die Anlage verfügbaren Ansichten darstellen. |

<a href="#objects">Zurück zur Schematabelle</a>

### <a name="attachmentview-object"></a>AttachmentView-Objekt
Definiert eine Anlagenansicht.<br/><br/> 

| Eigenschaft | Typ | BESCHREIBUNG |
|----|----|----|
| **viewId** | Zeichenfolge | Die Ansichts-ID. |
| **Größe** | number | Die Größe der Datei. |

<a href="#objects">Zurück zur Schematabelle</a>

<!-- TODO - can't find in swagger file -->
### <a name="attachmentupload-object"></a>AttachmentUpload-Objekt
Definiert eine hochzuladende Anlage.<br/><br/> 

| Eigenschaft | Typ | BESCHREIBUNG |
|----|----|----|
| **type** | Zeichenfolge | Der Inhaltstyp der Anlage. | 
| **name** | Zeichenfolge | Der Name der Anlage. | 
| **originalBase64** | Zeichenfolge | Binäre Daten, die den Inhalt der Originalversion der Datei darstellen. |
| **thumbnailBase64** | Zeichenfolge | Binäre Daten, die den Inhalt der Miniaturansichtversion der Datei darstellen. |

<a href="#objects">Zurück zur Schematabelle</a>

### <a name="audiocard-object"></a>AudioCard-Objekt
Definiert eine Rich Card, die eine Audiodatei wiedergeben kann.<br/><br/> 

| Eigenschaft | Typ | BESCHREIBUNG |
|----|----|----|
| **aspect** | Zeichenfolge | Das Seitenverhältnis der Miniaturansicht, das in der **image**-Eigenschaft angegeben wird. Gültige Werte sind **16:9** und **9:16**. |
| **autoloop** | boolean | Ein Flag, das angibt, ob die Liste der Audiodateien wiedergeben werden soll, nachdem die letzte Datei wiedergeben wurde. Legen Sie diese Eigenschaft auf **TRUE** fest, um die Audiodateien automatisch wiederzugeben, andernfalls auf **FALSE**. Der Standardwert lautet **true**. |
| **autostart** | boolean | Ein Flag, das angibt, ob die Audiodatei automatisch wiedergegeben werden soll, wenn die Rich Card angezeigt wird. Legen Sie diese Eigenschaft auf **TRUE** fest, um die Audiodatei automatisch wiederzugeben, andernfalls auf **FALSE**. Der Standardwert lautet **true**. |
| **buttons** | [CardAction](#cardaction-object)[] | Ein Array von **CardAction**-Objekten, das es dem Benutzer ermöglicht, mindestens eine Aktion auszuführen. Der Kanal bestimmt die Anzahl der Schaltflächen, die Sie angeben können. |
| **image** | [ThumbnailUrl](#thumbnailurl-object) | Ein **ThumbnailUrl**-Objekt, das das auf der Rich Card anzuzeigende Bild angibt. |
| **media** | [MediaUrl](#mediaurl-object)[] | Ein Array von **MediaUrl**-Objekten, das die Liste der Audiodateien angibt, die wiedergegeben werden sollen. |
| **shareable** | boolean | Ein Flag, das angibt, ob die Audiodateien für andere Benutzer freigegeben werden können. Legen Sie diese Eigenschaft auf **TRUE** fest, wenn die Audiodateien freigegeben werden können, andernfalls auf **FALSE**. Der Standardwert lautet **true**. |
| **subtitle** | Zeichenfolge | Der Untertitel, der unter dem Titel der Rich Card angezeigt werden soll. |
| **text** | Zeichenfolge | Eine Beschreibung oder Eingabeaufforderung, die unter dem Titel oder Untertitel der Rich Card angezeigt werden soll. |
| **title** | Zeichenfolge | Der Titel der Rich Card. |
| **value** | object | Zusätzlicher Parameter für diese Rich Card. |

<a href="#objects">Zurück zur Schematabelle</a>

<!-- TODO - can't find in swagger file -->
### <a name="botdata-object"></a>BotData-Objekt
Definiert Zustandsdaten für einen Benutzer, eine Unterhaltung oder einen Benutzer im Kontext einer bestimmten Unterhaltung, die mithilfe des Bot State-Diensts gespeichert werden.<br/><br/>

| Eigenschaft | Typ | BESCHREIBUNG |
|----|----|----|
| **Daten** | object | In einer Anforderung ein JSON-Objekt, das die Eigenschaften und Werte angibt, die mit dem Bot State-Dienst gespeichert werden sollen. In einer Antwort ein JSON-Objekt, das die Eigenschaften und Werte angibt, die mit dem Bot State-Dienst gespeichert wurden. | 
| **eTag** | Zeichenfolge | Der Entitätstagwert, den Sie verwenden können, um Datenparallelität für die Daten zu steuern, die mit dem Bot State-Dienst gespeichert werden. Weitere Informationen finden Sie unter [Verwalten von Zustandsdaten](bot-framework-rest-state.md). | 

<a href="#objects">Zurück zur Schematabelle</a>

### <a name="cardaction-object"></a>CardAction-Objekt
Definiert eine auszuführende Aktion.<br/><br/> 

| Eigenschaft | Typ | BESCHREIBUNG |
|----|----|----|
| **image** | Zeichenfolge | Die URL eines anzuzeigenden Bilds. | 
| **text** | Zeichenfolge | Der Text für die Aktion. |
| **title** | Zeichenfolge | Der Text der Schaltfläche. Gilt nur für die Aktion einer Schaltfläche. |
. Gilt nur für die Aktion einer Schaltfläche. |
| **type** | Zeichenfolge | Der Typ der auszuführenden Aktion. Eine Liste der gültigen Werte finden Sie unter [Hinzufügen von Rich Card-Anlagen zu Nachrichten](bot-framework-rest-connector-add-rich-cards.md). |
| **value** | object | Zusätzlicher Parameter für die Aktion. Der Wert dieser Eigenschaft ist abhängig vom **type** der Aktion. Weitere Informationen finden Sie unter [Hinzufügen von Rich Card-Anlagen zu Nachrichten](bot-framework-rest-connector-add-rich-cards.md). |

<a href="#objects">Zurück zur Schematabelle</a>

### <a name="cardimage-object"></a>CardImage-Objekt
Definiert ein Bild, das auf einer Rich Card angezeigt werden soll.<br/><br/> 

| Eigenschaft | Typ | BESCHREIBUNG |
|----|----|----|
| **alt** | Zeichenfolge | Die Beschreibung des Bilds. Sie sollten die Beschreibung zur Unterstützung der Barrierefreiheit einschließen. |
| **tap** | [CardAction](#cardaction-object) | Ein **CardAction**-Objekt, das die auszuführende Aktion angibt, wenn der Benutzer auf das Bild tippt oder klickt. |
| **url** | Zeichenfolge | Die URL zur Quelle des Bilds oder der base64-Binärdatei des Bilds (z.B. `data:image/png;base64,iVBORw0KGgo...`). |

<a href="#objects">Zurück zur Schematabelle</a>

### <a name="channelaccount-object"></a>ChannelAccount-Objekt
Definiert einen Bot oder ein Benutzerkonto im Kanal.<br/><br/>

| Eigenschaft | Typ | BESCHREIBUNG |
|----|----|----|
| **id** | Zeichenfolge | Eine ID, die den Bot oder Benutzer im Kanal eindeutig identifiziert. |
| **name** | Zeichenfolge | Der Name des Bots oder Benutzers. |

<a href="#objects">Zurück zur Schematabelle</a>

<!--TODO can't find-->
### <a name="conversation-object"></a>Conversation-Objekt
Definiert eine Unterhaltung einschließlich des Bots und der Benutzer, die in der Unterhaltung enthalten sind.<br/><br/> 

| Eigenschaft | Typ | BESCHREIBUNG |
|----|----|----|
| **bot** | [ChannelAccount](#channelaccount-object) | Ein **ChannelAccount**-Objekt, das den Bot identifiziert. |
| **isGroup** | boolean | Ein Flag zum Angeben, ob dies eine Gruppenunterhaltung ist. Legen Sie diesen Wert auf **TRUE** fest, wenn dies eine Gruppenunterhaltung ist, anderenfalls auf **FALSE**. Die Standardeinstellung ist **false**. Um eine Gruppenunterhaltung zu starten, muss der Kanal Gruppenunterhaltungen unterstützen. |
| **members** | [ChannelAccount](#channelaccount-object)[] | Ein Array von **ChannelAccount**-Objekten, die die Teilnehmer der Unterhaltung identifizieren. Diese Liste muss einen einzelnen Benutzer enthalten, es sei denn, **isGroup** ist auf **TRUE** festgelegt. Diese Liste kann weitere Bots enthalten. |
| **topicName** | Zeichenfolge | Der Titel der Unterhaltung. |
| **activity** | [Aktivität](#activity-object) | In einer Anforderung [Konversation erstellen](#create-conversation) ein **Activity**-Objekt, das die erste Nachricht definiert, die in der neuen Unterhaltung veröffentlicht werden soll. |

<a href="#objects">Zurück zur Schematabelle</a>

### <a name="conversationaccount-object"></a>ConversationAccount-Objekt
Definiert eine Unterhaltung in einem Kanal.<br/><br/>

| Eigenschaft | Typ | BESCHREIBUNG |
|----|----|----|
| **id** | Zeichenfolge | Die ID, die die Unterhaltung identifiziert. Die ID ist pro Kanal eindeutig. Wenn der Kanal die Unterhaltung startet, legt er diese ID fest. Andernfalls legt der Bot diese Eigenschaft auf die ID fest, die er in der Antwort erhält, wenn er die Unterhaltung startet (siehe „Starten einer Unterhaltung“). |
| **isGroup** | boolean | Ein Flag zum Angeben, ob die Unterhaltung zu dem Zeitpunkt mehrere Teilnehmer enthält, an dem die Aktivität generiert wurde. Legen Sie diesen Wert auf **TRUE** fest, wenn dies eine Gruppenunterhaltung ist, anderenfalls auf **FALSE**. Die Standardeinstellung ist **false**. |
| **name** | Zeichenfolge | Ein Anzeigename, der zum Identifizieren der Unterhaltung verwendet werden kann. |
| **conversationType** | Zeichenfolge | Gibt die Art der Unterhaltung in Kanälen an, die zwischen verschiedenen Unterhaltungstypen unterscheiden (z.B.: Gruppenunterhaltung, persönliche Unterhaltung). |

<a href="#objects">Zurück zur Schematabelle</a>

### <a name="conversationparameters-object"></a>ConversationParameters-Objekt
Definieren von Parametern zum Erstellen einer neuen Unterhaltung

| Eigenschaft | Typ | BESCHREIBUNG |
|----|----|----|
| **isGroup** | boolean | Gibt an, ob es sich um eine Gruppenunterhaltung handelt. |
| **bot** | [ChannelAccount](#channelaccount-object) | Die Adresse des Bots in der Unterhaltung. |
| **members** | Array | Eine Liste der Teilnehmer, die der Unterhaltung hinzugefügt werden sollen. |
| **topicName** | Zeichenfolge | Der Thementitel einer Unterhaltung. Diese Eigenschaft wird nur verwendet, wenn sie von einem Kanal unterstützt wird. |
| **activity** | [Aktivität](#activity-object) | (Optional) Verwenden Sie diese Aktivität als Anfangsnachricht der Unterhaltung, wenn Sie eine neue Unterhaltung erstellen. |
| **channelData** | object | Die kanalspezifische Nutzlast zum Erstellen der Unterhaltung. |

### <a name="conversationreference-object"></a>ConversationReference-Objekt
Definiert einen bestimmten Zeitpunkt in einer Unterhaltung.<br/><br/>

| Eigenschaft | Typ | BESCHREIBUNG |
|----|----|----|
| **activityId** | Zeichenfolge | Die ID, die die Aktivität eindeutig identifiziert, auf die dieses Objekt verweist. | 
| **bot** | [ChannelAccount](#channelaccount-object) | Ein **ChannelAccount**-Objekt, das den Bot in der Unterhaltung identifiziert, auf die dieses Objekt verweist. |
| **channelId** | Zeichenfolge | Die ID, die die Aktivität in der Unterhaltung eindeutig identifiziert, auf die dieses Objekt verweist. | 
| **Unterhaltung** | [ConversationAccount](#conversationaccount-object) | Ein **ChannelAccount**-Objekt, das die Unterhaltung identifiziert, auf die dieses Objekt verweist. |
| **serviceUrl** | Zeichenfolge | Die URL, die den Dienstendpunkt des Kanals in der Unterhaltung angibt, auf die dieses Objekt verweist. | 
| **user** | [ChannelAccount](#channelaccount-object) | Ein **ChannelAccount**-Objekt, das den Benutzer in der Unterhaltung identifiziert, auf die dieses Objekt verweist. |

<a href="#objects">Zurück zur Schematabelle</a>

### <a name="conversationresourceresponse-object"></a>ConversationResourceResponse-Objekt
Definiert eine Antwort, die eine Ressource enthält.

| Eigenschaft | Typ | BESCHREIBUNG |
|----|----|----|
| **activityId** | Zeichenfolge | Die ID der Aktivität. |
| **id** | Zeichenfolge | Die ID der Ressource. |
| **serviceUrl** | Zeichenfolge | Der Dienstendpunkt. |

### <a name="error-object"></a>Das Error-Objekt.
Definiert einen Fehler.<br/><br/>

| Eigenschaft | Typ | BESCHREIBUNG |
|----|----|----|
| **code** | Zeichenfolge | Fehlercode |
| **message** | Zeichenfolge | Eine Beschreibung des Fehlers. |

<a href="#objects">Zurück zur Schematabelle</a>

### <a name="entity-object"></a>Entity-Objekt
Definiert ein Entitätsobjekt.

| Eigenschaft | Typ | BESCHREIBUNG |
|----|----|----|
| **type** | Zeichenfolge | Der Entitätstyp. Enthält in der Regel Typen aus schema.org. |


### <a name="errorresponse-object"></a>ErrorResponse-Objekt
Definiert eine HTTP-API-Antwort.<br/><br/> 

| Eigenschaft | Typ | BESCHREIBUNG |
|----|----|----|
| **error** | [Fehler](#error-object) | Ein **Error**-Objekt mit Informationen zum Fehler. |

<a href="#objects">Zurück zur Schematabelle</a>

### <a name="fact-object"></a>Fact-Objekt
Definiert ein Schlüssel-Wert-Paar, das einen Fakt enthält.<br/><br/> 

| Eigenschaft | Typ | BESCHREIBUNG |
|----|----|----|
| **key** | Zeichenfolge | Der Name des Fakts. Beispielsweise **Check-in**. Der Schlüssel wird beim Anzeigen des Werts des Fakts als Bezeichnung verwendet. |
| **value** | Zeichenfolge | Der Wert des Fakts. Beispielsweise **10. Oktober 2016**. |

<a href="#objects">Zurück zur Schematabelle</a>

### <a name="geocoordinates-object"></a>Geocoordinates-Objekt
Definiert einen geografischen Standort mithilfe von WSG84-Koordinaten (World Geodetic System).<br/><br/> 

| Eigenschaft | Typ | BESCHREIBUNG |
|----|----|----|
| **elevation** | number | Die Höhe des Standorts. |
| **name** | Zeichenfolge | Der Name des Standorts. |
| **latitude** | number | Der Breitengrad des Standorts. |
| **longitude** | number | Der Längengrad des Standorts. |
| **type** | Zeichenfolge | Der Typ dieses Objekts. Wird immer auf **GeoCoordinates** festgelegt. |

<a href="#objects">Zurück zur Schematabelle</a>

### <a name="herocard-object"></a>HeroCard-Objekt
Definiert eine Rich Card mit einem großen Bild, Titel, Text und Aktionsschaltflächen.<br/><br/> 

| Eigenschaft | Typ | BESCHREIBUNG |
|----|----|----|
| **buttons** | [CardAction](#cardaction-object)[] | Ein Array von **CardAction**-Objekten, das es dem Benutzer ermöglicht, mindestens eine Aktion auszuführen. Der Kanal bestimmt die Anzahl der Schaltflächen, die Sie angeben können. |
| **images** | [CardImage](#cardimage-object)[] | Ein Array von **CardImage**-Objekten, das das auf der Rich Card anzuzeigende Bild angibt. Eine Hero Card enthält nur ein Bild. |
| **subtitle** | Zeichenfolge | Der Untertitel, der unter dem Titel der Rich Card angezeigt werden soll. |
| **tap** | [CardAction](#cardaction-object) | Ein **CardAction**-Objekt, das die auszuführende Aktion angibt, wenn der Benutzer auf die Rich Card tippt oder klickt. Dabei kann es sich um dieselbe Aktion wie bei einer der Schaltflächen oder um eine andere Aktion handeln. |
| **text** | Zeichenfolge | Eine Beschreibung oder Eingabeaufforderung, die unter dem Titel oder Untertitel der Rich Card angezeigt werden soll. |
| **title** | Zeichenfolge | Der Titel der Rich Card. |

<a href="#objects">Zurück zur Schematabelle</a>

<!--TODO can't find-->
### <a name="identification-object"></a>Identification-Objekt
Identifiziert eine Ressource.<br/><br/> 

| Eigenschaft | Typ | BESCHREIBUNG |
|----|----|----|
| **id** | Zeichenfolge | Eine ID, die die Ressource eindeutig identifiziert. |

<a href="#objects">Zurück zur Schematabelle</a>

### <a name="mediaeventvalue-object"></a>MediaEventValue-Objekt 
Zusätzlicher Parameter für Medienereignisse.

| Eigenschaft | Typ | BESCHREIBUNG |
|----|----|----|
| **cardValue** | object | Ein Rückrufparameter, der im Feld **Value** der Media Card angegeben wird, von der dieses Ereignis stammt. |

### <a name="mediaurl-object"></a>MediaUrl-Objekt
Definiert die URL zur Quelle einer Mediendatei.<br/><br/> 

| Eigenschaft | Typ | BESCHREIBUNG |
|----|----|----|
| **profile** | Zeichenfolge | Ein Hinweis, der den Medieninhalt beschreibt. |
| **url** | Zeichenfolge | Die URL zur Quelle der Mediendatei. |

<a href="#objects">Zurück zur Schematabelle</a>

<!--TODO can't find-->
### <a name="mention-object"></a>Mention-Objekt
Definiert einen Benutzer oder Bot, der in der Unterhaltung erwähnt wurde.<br/><br/> 


|          Eigenschaft          |                   Typ                   |                                                                                                                                                                                                                           BESCHREIBUNG                                                                                                                                                                                                                            |
|----------------------------|------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <strong>mentioned</strong> | [ChannelAccount](#channelaccount-object) | Ein <strong>ChannelAccount</strong>-Objekt, das den Benutzer oder Bot angibt, der erwähnt wurde. Beachten Sie, dass einige Kanäle wie Slack Namen pro Unterhaltung zuweisen. Daher ist es möglich, dass der erwähnte Name Ihres Bots (in der Eigenschaft <strong>recipient</strong> der Nachricht) sich von dem Handle unterscheidet, das Sie angegeben haben, als Sie Ihren Bot [registriert](../bot-service-quickstart-registration.md) haben. Allerdings sind die Konto-IDs für beide Angaben identisch. |
|   <strong>text</strong>    |                  Zeichenfolge                  |                                                                                                                         Der Benutzer oder Bot wie in der Unterhaltung erwähnt. Wenn die Nachricht z.B. „@ColorBot pick me a new color“ lautet, würde diese Eigenschaft auf <strong>@ColorBot</strong> festgelegt. Nicht alle Kanäle legen diese Eigenschaft fest.                                                                                                                          |
|   <strong>type</strong>    |                  Zeichenfolge                  |                                                                                                                                                                                                   Der Typ dieses Objekts. Immer auf <strong>Mention</strong> festgelegt.                                                                                                                                                                                                    |

<a href="#objects">Zurück zur Schematabelle</a>

### <a name="messagereaction-object"></a>MessageReaction-Objekt
Definiert eine Reaktion auf eine Nachricht.

| Eigenschaft | Typ | BESCHREIBUNG |
|----|----|----|
| **type** | Zeichenfolge | Der Typ der Reaktion. |

### <a name="place-object"></a>Place-Objekt
Definiert einen Ort, der in der Unterhaltung erwähnt wurde.<br/><br/> 

| Eigenschaft | Typ | BESCHREIBUNG |
|----|----|----|
| **address** | object |  Die Adresse eines Orts. Diese Eigenschaft kann vom Typ `string` oder ein komplexes Objekt vom Typ `PostalAddress` sein. |
| **geo** | [GeoCoordinates](#geocoordinates-object) | Ein **GeoCoordinates**-Objekt, das die geografischen Koordinaten des Orts angibt. |
| **hasMap** | object | Eine Karte für den Ort. Diese Eigenschaft kann vom Typ `string` (URL) oder ein komplexes Objekt vom Typ `Map` sein. |
| **name** | Zeichenfolge | Der Name des Orts. |
| **type** | Zeichenfolge | Der Typ dieses Objekts. Immer auf **Place** festgelegt. |

<a href="#objects">Zurück zur Schematabelle</a>

### <a name="receiptcard-object"></a>ReceiptCard-Objekt
Definiert eine Rich Card, die eine Quittung für einen Kauf enthält.<br/><br/>

| Eigenschaft | Typ | BESCHREIBUNG |
|----|----|----|
| **buttons** | [CardAction](#cardaction-object)[] | Ein Array von **CardAction**-Objekten, das es dem Benutzer ermöglicht, mindestens eine Aktion auszuführen. Der Kanal bestimmt die Anzahl der Schaltflächen, die Sie angeben können. |
| **facts** | [Fact](#fact-object)[] | Ein Array von **Fact**-Objekten, die Informationen zum Kauf angeben. Beispielsweise kann die Liste der Fakten für die Quittung eines Hotelaufenthalts das Eincheckdatum und das Auscheckdatum enthalten. Der Kanal bestimmt die Anzahl der Fakten, die Sie angeben können. |
| **items** | [ReceiptItem](#receiptitem-object)[] | Ein Array von **ReceiptItem**-Objekten, die die gekauften Artikel angeben. |
| **tap** | [CardAction](#cardaction-object) | Ein **CardAction**-Objekt, das die auszuführende Aktion angibt, wenn der Benutzer auf die Rich Card tippt oder klickt. Dabei kann es sich um dieselbe Aktion wie bei einer der Schaltflächen oder um eine andere Aktion handeln. |
| **tax** | Zeichenfolge | Eine als Währung formatierte Zeichenfolge, die die auf den Kauf angewendete Steuer angibt. |
| **title** | Zeichenfolge | Der Titel, der am oberen Rand der Quittung angezeigt wird. |
| **total** | Zeichenfolge | Eine als Währung formatierte Zeichenfolge, die den Gesamtkaufpreis einschließlich aller anwendbaren Steuern angibt. |
| **vat** | Zeichenfolge | Eine als Währung formatierte Zeichenfolge, die die auf den Kaufpreis angewendete Umsatzsteuer angibt. |

<a href="#objects">Zurück zur Schematabelle</a>

### <a name="receiptitem-object"></a>ReceiptItem-Objekt
Definiert eine Position in einer Quittung.<br/><br/> 

| Eigenschaft | Typ | BESCHREIBUNG |
|----|----|----|
| **image** | [CardImage](#cardimage-object) | Ein **CardImage**-Objekt, das die Miniaturansicht angibt, die neben der Position angezeigt werden soll.  |
| **price** | Zeichenfolge | Eine als Währung formatierte Zeichenfolge, die den Gesamtpreis aller erworbenen Einheiten angibt. |
| **quantity** | Zeichenfolge | Eine numerische Zeichenfolge, die die Anzahl der erworbenen Einheiten angibt. |
| **subtitle** | Zeichenfolge | Der Untertitel, der unter dem Titel der Position angezeigt werden soll. |
| **tap** | [CardAction](#cardaction-object) | Ein **CardAction**-Objekt, das die auszuführende Aktion angibt, wenn der Benutzer auf die Position tippt oder klickt. |
| **text** | Zeichenfolge | Eine Beschreibung der Position. |
| **title** | Zeichenfolge | Der Titel der Position. |

<a href="#objects">Zurück zur Schematabelle</a>

### <a name="resourceresponse-object"></a>ResourceResponse-Objekt
Definiert eine Antwort, die eine Ressourcen-ID enthält.<br/><br/>


|      Eigenschaft       |  Typ  |                BESCHREIBUNG                |
|---------------------|--------|-------------------------------------------|
| <strong>id</strong> | Zeichenfolge | Eine ID, die die Ressource eindeutig identifiziert. |

<a href="#objects">Zurück zur Schematabelle</a>

### <a name="signincard-object"></a>SignInCard-Objekt
Definiert eine Rich Card, die es einem Benutzer ermöglicht, sich bei einem Dienst anzumelden.<br/><br/>

| Eigenschaft | Typ | BESCHREIBUNG |
|----|----|----|
| **buttons** | [CardAction](#cardaction-object)[] | Ein Array von **CardAction**-Objekten, das es dem Benutzer ermöglicht, sich bei einem Dienst anzumelden. Der Kanal bestimmt die Anzahl der Schaltflächen, die Sie angeben können. |
| **text** | Zeichenfolge | Eine Beschreibung oder Eingabeaufforderung, die auf der SignInCard enthalten sein soll. |

<a href="#objects">Zurück zur Schematabelle</a>

### <a name="suggestedactions-object"></a>SuggestedActions-Objekt
Definiert die Optionen, unter denen ein Benutzer auswählen kann.<br/><br/> 

| Eigenschaft | Typ | BESCHREIBUNG |
|----|----|----|
| **actions** | [CardAction](#cardaction-object)[] | Ein Array von **CardAction**-Objekten, die die vorgeschlagenen Aktionen definieren. |
| **to** | string[] | Ein Array von Zeichenfolgen, das die IDs der Empfänger enthält, denen die vorgeschlagenen Aktionen angezeigt werden sollen. |

<a href="#objects">Zurück zur Schematabelle</a>

### <a name="thumbnailcard-object"></a>ThumbnailCard-Objekt
Definiert eine Rich Card mit einem Miniaturbild, Titel, Text und Aktionsschaltflächen.<br/><br/>

| Eigenschaft | Typ | BESCHREIBUNG |
|----|----|----|
| **buttons** | [CardAction](#cardaction-object)[] | Ein Array von **CardAction**-Objekten, das es dem Benutzer ermöglicht, mindestens eine Aktion auszuführen. Der Kanal bestimmt die Anzahl der Schaltflächen, die Sie angeben können. |
| **images** | [CardImage](#cardimage-object)[] | Ein Array von **CardImage**-Objekten, das die auf der Rich Card anzuzeigenden Miniaturansichten angibt. Der Kanal bestimmt die Anzahl der Miniaturansichten, die Sie angeben können. |
| **subtitle** | Zeichenfolge | Der Untertitel, der unter dem Titel der Rich Card angezeigt werden soll. |
| **tap** | [CardAction](#cardaction-object) | Ein **CardAction**-Objekt, das die auszuführende Aktion angibt, wenn der Benutzer auf die Rich Card tippt oder klickt. Dabei kann es sich um dieselbe Aktion wie bei einer der Schaltflächen oder um eine andere Aktion handeln. |
| **text** | Zeichenfolge | Eine Beschreibung oder Eingabeaufforderung, die unter dem Titel oder Untertitel der Rich Card angezeigt werden soll. |
| **title** | Zeichenfolge | Der Titel der Rich Card. |

<a href="#objects">Zurück zur Schematabelle</a>

### <a name="thumbnailurl-object"></a>ThumbnailUrl-Objekt
Definiert die URL zur Quelle eines Bilds.<br/><br/> 

| Eigenschaft | Typ | BESCHREIBUNG |
|----|----|----|
| **alt** | Zeichenfolge | Die Beschreibung des Bilds. Sie sollten die Beschreibung zur Unterstützung der Barrierefreiheit einschließen. |
| **url** | Zeichenfolge | Die URL zur Quelle des Bilds oder der base64-Binärdatei des Bilds (z.B. `data:image/png;base64,iVBORw0KGgo...`). |

<a href="#objects">Zurück zur Schematabelle</a>

### <a name="videocard-object"></a>VideoCard-Objekt
Definiert eine Rich Card, die Videos wiedergeben kann.<br/><br/>

| Eigenschaft | Typ | BESCHREIBUNG |
|----|----|----|
| **aspect** | Zeichenfolge | Das Seitenverhältnis eines Videos (Beispiel: 16:9, 4:3).|
| **autoloop** | boolean | Ein Flag, das angibt, ob die Liste der Videos wiedergeben werden soll, nachdem die letzte Datei wiedergeben wurde. Legen Sie diese Eigenschaft auf **TRUE** fest, um die Videos automatisch wiederzugeben, andernfalls auf **FALSE**. Der Standardwert lautet **true**. |
| **autostart** | boolean | Ein Flag, das angibt, ob die Videos automatisch wiedergegeben werden sollen, wenn die Rich Card angezeigt wird. Legen Sie diese Eigenschaft auf **TRUE** fest, um die Videos automatisch wiederzugeben, andernfalls auf **FALSE**. Der Standardwert lautet **true**. |
| **buttons** | [CardAction](#cardaction-object)[] | Ein Array von **CardAction**-Objekten, das es dem Benutzer ermöglicht, mindestens eine Aktion auszuführen. Der Kanal bestimmt die Anzahl der Schaltflächen, die Sie angeben können. |
| **image** | [ThumbnailUrl](#thumbnailurl-object) | Ein **ThumbnailUrl**-Objekt, das das auf der Rich Card anzuzeigende Bild angibt. |
| **media** | [MediaUrl](#mediaurl-object)[] | Ein Array von **MediaUrl**-Objekten, das die Liste der Videos angibt, die wiedergegeben werden sollen. |
| **shareable** | boolean | Ein Flag, das angibt, ob die Videos für andere Benutzer freigegeben werden können. Legen Sie diese Eigenschaft auf **TRUE** fest, wenn die Videos freigegeben werden können, andernfalls auf **FALSE**. Der Standardwert lautet **true**. |
| **subtitle** | Zeichenfolge | Der Untertitel, der unter dem Titel der Rich Card angezeigt werden soll. |
| **text** | Zeichenfolge | Eine Beschreibung oder Eingabeaufforderung, die unter dem Titel oder Untertitel der Rich Card angezeigt werden soll. |
| **title** | Zeichenfolge | Der Titel der Rich Card. |
| **value** | object | Zusätzlicher Parameter für diese Rich Card.|

<a href="#objects">Zurück zur Schematabelle</a>
