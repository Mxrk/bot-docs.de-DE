---
title: Verwalten von Zustandsdaten | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Zustandsdaten mit dem Bot State-Dienst speichern und abrufen.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
ms.openlocfilehash: 1557941d4e5413108ea3ce788f7d5d684252b657
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39298749"
---
# <a name="manage-state-data"></a>Verwalten von Zustandsdaten

Der Bot State-Dienst ermöglicht Ihren Bots, Daten zu speichern und abzurufen, die einem Benutzer, einer Konversation oder einem bestimmten Benutzer innerhalb des Kontexts einer bestimmten Konversation zugeordnet sind. Sie können bis zu 32 KB an Daten für jeden Benutzer bzw. jede Konversation auf einem Kanal und jeden Benutzer im Kontext einer Konversation auf einem Kanal speichern. Zustandsdaten können für viele Zwecke verwendet werden, z.B. zum Bestimmen, wo eine frühere Konversation unterbrochen wurde, oder einfach zur Begrüßung eines zurückkehrenden Benutzers mit seinem Namen. Wenn Sie die Einstellungen eines Benutzers speichern, können Sie diese Informationen zum Anpassen der Konversation beim nächsten Chat verwenden. Sie können beispielsweise den Benutzer auf einen Nachrichtenartikel zu einem Thema, das ihn interessiert, aufmerksam machen oder einen Benutzer benachrichtigen, wenn ein Termin verfügbar wird. 

> [!IMPORTANT]
> Die Bot Framework-Zustandsdienst-API wird nicht für Produktionsumgebungen empfohlen und kann in einem zukünftigen Release veraltet sein. Es wird empfohlen, Ihren Botcode zu aktualisieren, um den In-Memory-Speicher für Testzwecke zu nutzen, oder eine der **Azure-Erweiterungen** für Produktionsbots zu verwenden. Weitere Informationen finden Sie im Thema **Verwalten von Zustandsdaten** für die Implementierung unter [.NET](~/dotnet/bot-builder-dotnet-state.md) bzw. [Node](~/nodejs/bot-builder-nodejs-state.md).

## <a id="concurrency"></a> Datenparallelität

Zur Steuerung der Parallelität von Daten, die mit dem Bot State-Dienst gespeichert wurden, verwendet das Framework das Entitätstag (ETag) für `POST`-Anforderungen. Das Framework verwendet nicht die Standardheader für ETags. Stattdessen wird im Hauptteil der Anforderung und der Antwort das ETag mit der `eTag`-Eigenschaft angegeben. 

Wenn Sie beispielsweise eine `GET`-Anforderung zum Abrufen von Benutzerdaten aus dem Store ausgeben, enthält die Antwort die `eTag`-Eigenschaft. Wenn Sie die Daten ändern und eine `POST`-Anforderung zum Schreiben der aktualisierten Daten in den Speicher ausgeben, sollte Ihre Anforderung die `eTag`-Eigenschaft enthalten, die den gleichen Wert angibt, den Sie zuvor in der `GET`-Antwort erhalten haben. Wenn das in Ihrer `POST`-Anforderung angegebene ETag mit dem aktuellen Wert im Speicher übereinstimmt, speichert der Server die Daten des Benutzers, antwortet mit **HTTP 200 Erfolg** und gibt einen neuen `eTag`-Wert im Hauptteil der Antwort an. Wenn das in Ihrer `POST`-Anforderung angegebene ETag nicht mit dem aktuellen Wert im Speicher übereinstimmt, antwortet der Server mit **HTTP 412 Fehler bei der Vorbedingung**, um anzugeben, dass die Daten des Benutzers im Speicher geändert wurden, seit Sie sie zum letzten Mal gespeichert oder abgerufen haben.

> [!TIP]
> Eine `GET`-Antwort schließt immer eine `eTag`-Eigenschaft ein, Sie müssen die `eTag`-Eigenschaft jedoch nicht in `GET`-Anforderungen angeben. Der `eTag`-Eigenschaftswert `*` (Sternchen) gibt an, dass Sie bisher keine Daten für die angegebene Kombination aus Kanal, Benutzer und Konversation gespeichert haben.
>
> Die Angabe der `eTag`-Eigenschaft in `POST`-Anforderungen ist optional. 
> Wenn für Ihren Bot Parallelität keine Rolle spielt, schließen Sie die `eTag`-Eigenschaft nicht in `POST`-Anforderungen ein. 

## <a id="save-user-data"></a> Speichern von Benutzerdaten

Um Zustandsdaten für einen Benutzer in einem bestimmten Kanal zu speichern, geben Sie diese Anforderung aus:

```http
POST /v3/botstate/{channelId}/users/{userId}
```

Ersetzen Sie in diesem Anforderungs-URI **{channelId}** durch die ID des Kanals und **{userId}** durch die ID des Benutzers in diesem Kanal. Die Eigenschaften `channelId` und `from` in Nachrichten, die Ihr Bot zuvor vom Benutzer erhalten hat, enthalten diese IDs. Sie können diese Werte auch an einem sicheren Ort zwischenspeichern, damit Sie künftig auf die Daten des Benutzers zugreifen können, ohne sie aus einer Nachricht extrahieren zu müssen.

Legen Sie den Text der Anforderung auf ein [BotData][BotData]-Objekt fest, wobei die `data`-Eigenschaft die Daten angibt, die Sie speichern möchten. Legen Sie bei Verwendung von Entitätstags zur [Parallelitätssteuerung](#concurrency) die `eTag`-Eigenschaft auf das ETag fest, dass Sie zum Zeitpunkt der letzten Speicherung oder des letzten Abrufs der Daten des Benutzers (je nachdem, welcher Vorgang vor kürzerer Zeit stattfand) in der Antwort erhalten haben. Wenn Sie keine Entitätstags für die Parallelität verwenden, schließen Sie die `eTag`-Eigenschaft nicht in die Anforderung ein.

> [!NOTE]
> Die `POST`-Anforderung überschreibt Benutzerdaten im Speicher nur dann, wenn das angegebene ETag mit dem ETag des Servers übereinstimmt, oder wenn kein ETag in der Anforderung angegeben ist.

### <a name="request"></a>Anforderung 

Das folgende Beispiel zeigt eine Anforderung, die Daten für einen Benutzer in einem bestimmten Kanal speichert. In dieser Beispielanforderung stellt `https://smba.trafficmanager.net/apis` den Basis-URI dar. Der Basis-URI für Anforderungen, die Ihr Bot ausgibt, kann ein anderer sein. Weitere Informationen zum Festlegen des Basis-URI finden Sie in der [API-Referenz](bot-framework-rest-connector-api-reference.md#base-uri).

```http
POST https://smba.trafficmanager.net/apis/v3/botstate/abcd1234/users/12345678
Authorization: Bearer ACCESS_TOKEN
Content-Type: application/json
```

```json
{
    "data": [
        {
            "trail": "Lake Serene",
            "miles": 8.2,
            "difficulty": "Difficult",
        },
        {
            "trail": "Rainbow Falls",
            "miles": 6.3,
            "difficulty": "Moderate",
        }
    ],
    "eTag": "a1b2c3d4"
}
```

### <a name="response"></a>response 

Die Antwort enthält ein [BotData][BotData]-Objekt mit einem neuen `eTag`-Wert.

## <a name="get-user-data"></a>Abrufen von Benutzerdaten

Um Zustandsdaten abzurufen, die zuvor für den Benutzer in einem bestimmten Kanal gespeichert wurden, geben Sie diese Anforderung aus:

```http
GET /v3/botstate/{channelId}/users/{userId}
```

Ersetzen Sie in diesem Anforderungs-URI **{channelId}** durch die ID des Kanals und **{userId}** durch die ID des Benutzers in diesem Kanal. Die Eigenschaften `channelId` und `from` in Nachrichten, die Ihr Bot zuvor vom Benutzer erhalten hat, enthalten diese IDs. Sie können diese Werte auch an einem sicheren Ort zwischenspeichern, damit Sie künftig auf die Daten des Benutzers zugreifen können, ohne sie aus einer Nachricht extrahieren zu müssen.

### <a name="request"></a>Anforderung

Das folgende Beispiel zeigt eine Anforderung, die zuvor für den Benutzer gespeicherte Daten abruft. In dieser Beispielanforderung stellt `https://smba.trafficmanager.net/apis` den Basis-URI dar. Der Basis-URI für Anforderungen, die Ihr Bot ausgibt, kann ein anderer sein. Weitere Informationen zum Festlegen des Basis-URI finden Sie in der [API-Referenz](bot-framework-rest-connector-api-reference.md#base-uri).

```http
GET https://smba.trafficmanager.net/apis/v3/botstate/abcd1234/users/12345678
Authorization: Bearer ACCESS_TOKEN
Content-Type: application/json
```

### <a name="response"></a>response

Die Antwort enthält ein [BotData][BotData]-Objekt, dessen `data`-Eigenschaft die zuvor für den Benutzer gespeicherten Daten und dessen `eTag`-Eigenschaft das ETag enthält, das Sie in einer nachfolgenden Anforderung zum Speichern der Daten des Benutzers verwenden können. Wenn Sie bisher noch keine Daten für den Benutzer gespeichert haben, ist die `data`-Eigenschaft `null`, und die `eTag`-Eigenschaft enthält ein Sternchen (`*`).

Dieses Beispiel zeigt die Antwort auf die `GET`-Anforderung:

```json
{
    "data": [
        {
            "trail": "Lake Serene",
            "miles": 8.2,
            "difficulty": "Difficult",
        },
        {
            "trail": "Rainbow Falls",
            "miles": 6.3,
            "difficulty": "Moderate",
        }
    ],
    "eTag": "xyz12345"
}
```

## <a name="save-conversation-data"></a>Speichern von Konversationsdaten

Um Zustandsdaten für eine Konversation in einem bestimmten Kanal zu speichern, geben Sie diese Anforderung aus:

```http
POST /v3/botstate/{channelId}/conversations/{conversationId}
```

Ersetzen Sie in diesem Anforderungs-URI **{channelId}** durch die ID des Kanals und **{conversationId}** durch die ID der Konversation. Die Eigenschaften `channelId` und `conversation` in Nachrichten, die Ihr Bot zuvor im Kontext der Konversation gesendet oder empfangen hat, enthalten diese IDs. Sie können diese Werte auch an einem sicheren Ort zwischenspeichern, damit Sie künftig auf die Daten der Konversation zugreifen können, ohne sie aus einer Nachricht extrahieren zu müssen.

Legen Sie den Hauptteil der Anforderung auf ein [BotData][BotData]-Objekt fest, wie unter [Speichern von Benutzerdaten](#save-user-data) beschrieben.

> [!IMPORTANT]
> Da beim Vorgang [Löschen von Benutzerdaten](#delete-user-data) keine Daten gelöscht werden, die mit dem Vorgang **Speichern von Konversationsdaten** gespeichert wurden, dürfen Sie diesen Vorgang NICHT zum Speichern von personenbezogenen Informationen (PII) eines Benutzers verwenden.

### <a name="response"></a>response

Die Antwort enthält ein [BotData][BotData]-Objekt mit einem neuen `eTag`-Wert.

## <a name="get-conversation-data"></a>Abrufen von Konversationsdaten

Um Zustandsdaten abzurufen, die zuvor für eine Konversation in einem bestimmten Kanal gespeichert wurden, geben Sie diese Anforderung aus:

```http
GET /v3/botstate/{channelId}/conversations/{conversationId}
```

Ersetzen Sie in diesem Anforderungs-URI **{channelId}** durch die ID des Kanals und **{conversationId}** durch die ID der Konversation. Die Eigenschaften `channelId` und `conversation` in Nachrichten, die Ihr Bot zuvor im Kontext der Konversation gesendet oder empfangen hat, enthalten diese IDs. Sie können diese Werte auch an einem sicheren Ort zwischenspeichern, damit Sie künftig auf die Daten der Konversation zugreifen können, ohne sie aus einer Nachricht extrahieren zu müssen.

### <a name="response"></a>response

Die Antwort enthält ein [BotData][BotData]-Objekt mit einem neuen `eTag`-Wert.

## <a name="save-private-conversation-data"></a>Speichern von privaten Konversationsdaten

Um Zustandsdaten für einen bestimmten Benutzer im Kontext einer bestimmten Konversation zu speichern, geben Sie diese Anforderung aus:

```http
POST /v3/botstate/{channelId}/conversations/{conversationId}/users/{userId}
```

Ersetzen Sie in diesem Anforderungs-URI **{channelId}** durch die ID des Kanals, **{conversationId}** durch die ID der Konversation und **{userId}** durch die ID des Benutzers in diesem Kanal. Die Eigenschaften `channelId`, `conversation` und `from` in Nachrichten, die Ihr Bot zuvor im Kontext der Konversation vom Benutzer empfangen hat, enthalten diese IDs. Sie können diese Werte auch an einem sicheren Ort zwischenspeichern, damit Sie künftig auf die Daten der Konversation zugreifen können, ohne sie aus einer Nachricht extrahieren zu müssen.

Legen Sie den Hauptteil der Anforderung auf ein [BotData][BotData]-Objekt fest, wie unter [Speichern von Benutzerdaten](#save-user-data) beschrieben.

### <a name="response"></a>response

Die Antwort enthält ein [BotData][BotData]-Objekt mit einem neuen `eTag`-Wert.

## <a name="get-private-conversation-data"></a>Abrufen von privaten Konversationsdaten

Um Zustandsdaten abzurufen, die zuvor für einen Benutzer im Kontext einer bestimmten Konversation gespeichert wurden, geben Sie diese Anforderung aus: 

```http
GET /v3/botstate/{channelId}/conversations/{conversationId}/users/{userId}
```

Ersetzen Sie in diesem Anforderungs-URI **{channelId}** durch die ID des Kanals, **{conversationId}** durch die ID der Konversation und **{userId}** durch die ID des Benutzers in diesem Kanal. Die Eigenschaften `channelId`, `conversation` und `from` in Nachrichten, die Ihr Bot zuvor im Kontext der Konversation vom Benutzer empfangen hat, enthalten diese IDs. Sie können diese Werte auch an einem sicheren Ort zwischenspeichern, damit Sie künftig auf die Daten der Konversation zugreifen können, ohne sie aus einer Nachricht extrahieren zu müssen.

### <a name="response"></a>response

Die Antwort enthält ein [BotData][BotData]-Objekt mit einem neuen `eTag`-Wert.

## <a name="delete-user-data"></a>Löschen von Benutzerdaten

Um Zustandsdaten für einen Benutzer in einem bestimmten Kanal zu löschen, geben Sie diese Anforderung aus:

```http
DELETE /v3/botstate/{channelId}/users/{userId}
```
Ersetzen Sie in diesem Anforderungs-URI **{channelId}** durch die ID des Kanals und **{userId}** durch die ID des Benutzers in diesem Kanal. Die Eigenschaften `channelId` und `from` in Nachrichten, die Ihr Bot zuvor vom Benutzer erhalten hat, enthalten diese IDs. Sie können diese Werte auch an einem sicheren Ort zwischenspeichern, damit Sie künftig auf die Daten des Benutzers zugreifen können, ohne sie aus einer Nachricht extrahieren zu müssen.

> [!IMPORTANT]
> Dieser Vorgang löscht Zustandsdaten, die zuvor für den Benutzer mit einem der Vorgänge [Speichern von Benutzerdaten](#save-user-data) oder [Speichern privater Konversationsdaten](#save-private-conversation-data) gespeichert wurden. Er löscht KEINE Daten, die zuvor mit dem Vorgang [Speichern von Konversationsdaten](#save-conversation-data) gespeichert wurden. Verwenden Sie daher NICHT den Vorgang **Speichern von Konversationsdaten**  zum Speichern personenbezogener Informationen (PII) eines Benutzers.

Ihr Bot sollte den Vorgang **Löschen von Benutzerdaten** ausführen, wenn er eine [Aktivität][Activity] vom Typ [deleteUserData](bot-framework-rest-connector-activities.md#deleteuserdata) oder vom Typ [contactRelationUpdate](bot-framework-rest-connector-activities.md#contactrelationupdate) empfängt, die darauf hindeutet, dass der Bot aus der Kontaktliste des Benutzers entfernt wurde.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Wichtige Begriffe](bot-framework-rest-connector-concepts.md)
- [Übersicht über Aktivitäten](bot-framework-rest-connector-activities.md)

[BotData]: bot-framework-rest-connector-api-reference.md#botdata-object
[Activity]: bot-framework-rest-connector-api-reference.md#activity-object
