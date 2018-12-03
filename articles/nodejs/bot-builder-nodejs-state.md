---
title: Verwalten von Statusdaten | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie mithilfe des Bot Builder SDK für Node.js Statusdaten speichern und abrufen.
author: DucVo
ms.author: v-ducvo
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 519f188a29db2b9b37061ee0eff5361a63d2acac
ms.sourcegitcommit: b78fe3d8dd604c4f7233740658a229e85b8535dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2018
ms.locfileid: "49999987"
---
# <a name="manage-state-data"></a>Verwalten von Statusdaten

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

> [!div class="op_single_selector"]
> - [.NET](../dotnet/bot-builder-dotnet-state.md)
> - [Node.js](../nodejs/bot-builder-nodejs-state.md)

[!INCLUDE [State concept overview](../includes/snippet-dotnet-concept-state.md)]

## <a name="in-memory-data-storage"></a>In-Memory-Datenspeicher

Der In-Memory-Datenspeicher dient nur zu Testzwecken. Dieser Speicher ist flüchtig und temporär. Die Daten werden jedes Mal gelöscht, wenn der Bot neu gestartet wird. Um den In-Memory-Speicher zu Testzwecken verwenden zu können, müssen Sie zwei Dinge tun. Erstellen Sie zuerst eine neue Instanz des In-Memory-Speichers:

```javascript
var inMemoryStorage = new builder.MemoryBotStorage();
```

Legen Sie ihn anschließend beim Erstellen des **UniversalBot** auf den Bot fest:

```javascript
var inMemoryStorage = new builder.MemoryBotStorage();
var bot = new builder.UniversalBot(connector, [..waterfall steps..])
                    .set('storage', inMemoryStorage); // Register in-memory storage 
```

Sie können mit dieser Methode Ihren eigenen benutzerdefinierten Datenspeicher festlegen oder eine der *Azure-Erweiterungen* verwenden.

## <a name="manage-custom-data-storage"></a>Verwalten eines benutzerdefinierten Datenspeichers

Im Hinblick auf Leistung und Sicherheit in der Produktionsumgebung können Sie Ihren eigenen Datenspeicher festlegen oder das Implementieren einer der folgenden Datenspeicheroptionen in Betracht ziehen:

1. [Verwalten von Statusdaten mit Cosmos DB](bot-builder-nodejs-state-azure-cosmosdb.md)

2. [Verwalten von Statusdaten mit Table Storage](bot-builder-nodejs-state-azure-table-storage.md)

Der Mechanismus zum Festlegen und Beibehalten der Daten über das Bot Framework-SDK für Node.js ist bei diesen beiden [Azure-Erweiterungen](https://www.npmjs.com/package/botbuilder-azure) und dem In-Memory-Datenspeicher identisch.

## <a name="storage-containers"></a>Speichercontainer

Im Bot Builder SDK für Node.js macht das `session`-Objekt die folgenden Eigenschaften zum Speichern von Statusdaten verfügbar.

| Eigenschaft | Zuordnung | BESCHREIBUNG |
| ---- | ---- | ---- |
| [`userData`][userDataURL] | Benutzer | Enthält Daten, die für den Benutzer im angegebenen Kanal gespeichert werden. Diese Daten werden über mehrere Unterhaltungen beibehalten. |
| [`privateConversationData`][privateConversationDataURL] | Unterhaltung | Enthält Daten, die für den Benutzer im Zusammenhang mit einer bestimmten Unterhaltung im angegebenen Kanal gespeichert werden. Dies sind private Daten des aktuellen Benutzers, die nur für die aktuelle Unterhaltung beibehalten werden. Die Eigenschaft wird gelöscht, wenn die Unterhaltung endet oder explizit `endConversation` aufgerufen wird. |
| [`conversationData`][conversationDataURL] | Unterhaltung | Enthält Daten, die im Zusammenhang mit einer bestimmten Unterhaltung im angegebenen Kanal gespeichert werden. Diese Daten sind für alle Benutzer freigegeben, die sich an der Unterhaltung beteiligen, und werden nur für die aktuelle Unterhaltung beibehalten. Die Eigenschaft wird gelöscht, wenn die Unterhaltung endet oder explizit `endConversation` aufgerufen wird. |
| [`dialogData`][dialogDataURL] | Dialog | Enthält Daten, die nur für den aktuellen Dialog gespeichert werden. Jeder Dialog verwaltet eine eigene Kopie dieser Eigenschaft. Die Eigenschaft wird gelöscht, wenn der Dialog aus dem Dialogstapel entfernt wird. |

Diese vier Eigenschaften entsprechen den vier Datenspeichercontainern, die zum Speichern von Daten verwendet werden können. Welche Eigenschaft Sie zum Speichern der Daten verwenden, hängt von der entsprechenden Zuordnung und dem Typ der zu speichernden Daten sowie von der Aufbewahrungsdauer der Daten ab. Wenn Sie beispielsweise Benutzerdaten speichern müssen, die über mehrere Unterhaltungen hinweg verfügbar sind, sollten Sie die `userData`-Eigenschaft in Betracht ziehen. Wenn Sie temporär lokale Variablenwerte im Rahmen eines Dialogs speichern müssen, sollten Sie die `dialogData`-Eigenschaft verwenden. Wenn Sie temporär Daten speichern müssen, die in mehreren Dialogen zugänglich sein müssen, ziehen Sie die `conversationData`-Eigenschaft in Betracht.

## <a name="data-persistence"></a>Datenpersistenz

Standardmäßig sind Daten, die mit den Eigenschaften `userData`, `privateConversationData` und `conversationData` gespeichert werden, so festgelegt, dass sie nach dem Ende der Unterhaltung beibehalten werden. Wenn die Daten im `userData`-Container nicht beibehalten werden sollen, legen Sie das `persistUserData`-Flag auf **false** fest. Wenn die Daten im `conversationData`-Container nicht beibehalten werden sollen, legen Sie das `persistConversationData`-Flag auf **false** fest. 

```javascript
// Do not persist userData
bot.set(`persistUserData`, false);

// Do not persist conversationData
bot.set(`persistConversationData`, false);
```

> [!NOTE]
> Für den `privateConversationData`-Container kann die Datenpersistenz nicht deaktiviert werden; hier werden die Daten immer beibehalten.

## <a name="set-data"></a>Festlegen von Daten

Sie können einfache JavaScript-Objekte speichern, indem Sie diese direkt in einem Speichercontainer speichern. Bei einem komplexen Objekt wie `Date` sollten Sie eine Konvertierung in `string` in Betracht ziehen. Statusdaten werden nämlich serialisiert und im JSON-Format gespeichert. Die folgenden Codebeispiele zeigen, wie primitive Daten, ein Array, eine Objektzuordnung und ein komplexes `Date`-Objekt gespeichert werden. 

**Speichern von primitiven Daten**

```javascript
session.userData.userName = "Kumar Sarma";
session.userData.userAge = 37;
session.userData.hasChildren = true;
```

**Speichern eines Arrays**

```javascript
session.userData.profile = ["Kumar Sarma", "37", "true"];
```

**Speichern einer Objektzuordnung**

```javascript
session.userData.about = {
    "Profile": {
        "Name": "Kumar Sarma",
        "Age": 37,
        "hasChildren": true
    },
    "Job": {
        "Company": "Contoso",
        "StartDate": "June 8th, 2010",
        "Title": "Developer"
    }
}
```
**Speichern von Datum und Uhrzeit** 

Ein komplexes JavaScript-Objekt sollten Sie vor dem Speichern in einem Speichercontainer in einen String konvertieren. 

```javascript 
var startDate = builder.EntityRecognizer.resolveTime([results.response]); 

// Date as string: "2017-08-23T05:00:00.000Z" 
session.userdata.start = startDate.toISOString(); 
``` 

### <a name="saving-data"></a>Speichern von Daten

Daten, die in den einzelnen Speichercontainern erstellt werden, bleiben im Arbeitsspeicher, bis der Container gespeichert wird. Das Bot Builder SDK für Node.js sendet zu speichernde Daten in Batches an den `ChatConnector`-Dienst, wenn Nachrichten gesendet werden. Wenn Sie die Daten, die sich in den Speichercontainern befinden, ohne das Senden einer Nachricht speichern möchten, können Sie manuell die [`save`](https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.session.html#save)-Methode aufrufen. Wenn Sie die `save`-Methode nicht aufrufen, bleiben die in den Speichercontainern vorhandenen Daten Bestandteil der Batchverarbeitung.

```javascript
session.userData.favoriteColor = "Red";
session.userData.about.job.Title = "Senior Developer"; 
session.save();
```

## <a name="get-data"></a>Datensammlung

Wenn Sie auf die in einem bestimmten Speichercontainer gespeicherten Daten zugreifen möchten, verweisen Sie einfach auf die entsprechende Eigenschaft. Die folgenden Codebeispiele zeigen, wie Sie auf Daten zugreifen, die zuvor als primitive Daten, Array, Objektzuordnung und komplexes Date-Objekt gespeichert wurden.

**Zugreifen auf primitive Daten**

```javascript
var userName = session.userData.userName;
var userAge = session.userData.userAge;
var hasChildren = session.userData.hasChildren;
```

**Zugreifen auf Arrays**

```javascript
var userProfile = session.userData.userProfile;

session.send("User Profile:");
for(int i = 0; i < userProfile.length, i++){
    session.send(userProfile[i]);
}
```

**Zugreifen auf Objektzuordnungen**

```javascript
var about = session.userData.about;

session.send("User %s works at %s.", about.Profile.Name, about.Job.Company);
```

**Zugreifen auf Date-Objekte** 

Rufen Sie Datumsdaten als String ab, und konvertieren Sie diesen dann in ein JavaScript-Date-Objekt. 

```javascript 
// startDate as a JavaScript Date object. 
var startDate = new Date(session.userdata.start); 
``` 

## <a name="delete-data"></a>Löschen von Daten

Standardmäßig werden Daten, die im `dialogData`-Container gespeichert sind, gelöscht, wenn ein Dialog aus dem Dialogstapel entfernt wird. Ebenso werden Daten, die im `conversationData`-Container und `privateConversationData`-Container gespeichert sind, gelöscht, wenn die `endConversation`-Methode wird aufgerufen. Im `userData`-Container gespeicherte Daten müssen Sie jedoch explizit löschen.

Wenn Sie die in einem Speichercontainer gespeicherten Daten explizit löschen möchten, setzen Sie einfach den Container zurück, wie im folgenden Codebeispiel gezeigt. 

```javascript
// Clears data stored in container.
session.userData = {}; 
session.privateConversationData = {};
session.conversationData = {};
session.dialogData = {};
```

Sie dürfen nie einen Datencontainer auf `null` festlegen oder ihn aus dem `session`-Objekt entfernen, da dies beim nächsten Zugriff auf den Container Fehler verursacht. Auch sollten Sie nach dem manuellen Bereinigen eines Containers im Speicher manuell `session.save();` aufrufen, um alle zugehörigen Daten zu löschen, die vorher beibehalten wurden.

## <a name="next-steps"></a>Nächste Schritte

Da Sie nun die Verwaltung von Benutzerstatusdaten kennengelernt haben, erfahren Sie als Nächstes, wie Sie diese Daten für eine optimale Verwaltung des Konversationsflusses einsetzen können.

> [!div class="nextstepaction"]
> [Verwalten des Konversationsflusses](bot-builder-nodejs-dialog-manage-conversation-flow.md)

## <a name="additional-resources"></a>Zusätzliche Ressourcen
- [Auffordern der Benutzer zu Eingaben](bot-builder-nodejs-dialog-prompt.md)

[userDataURL]: https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.session.html#userdata
[conversationDataURL]: https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.session.html#conversationdata
[privateConversationDataURL]: https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.session.html#privateconversationdata
[dialogDataURL]: https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.session.html#dialogdata

[ChatConnector]: https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.chatconnector.html
