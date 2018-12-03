---
title: Verwalten von benutzerdefinierten Statusdaten mit Azure Table Storage | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie mithilfe des Bot Builder SDK für Node.js Statusdaten mit Azure Table Storage speichern und abrufen.
author: DucVo
ms.author: v-ducvo
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 5bf308c440e08cb3c9d4730212fbba3053de459d
ms.sourcegitcommit: b78fe3d8dd604c4f7233740658a229e85b8535dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2018
ms.locfileid: "49998332"
---
# <a name="manage-custom-state-data-with-azure-table-storage-for-nodejs"></a>Verwalten von benutzerdefinierten Statusdaten mit Azure Table Storage für Node.js

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

In diesem Artikel wird beschrieben, wie Sie Azure Table Storage zum Speichern und Verwalten der Statusdaten Ihres Bots implementieren. Der von Bots verwendete standardmäßige Connector State Service ist nicht für die Produktionsumgebung vorgesehen. Sie sollten entweder die in GitHub verfügbaren [Azure-Erweiterungen](https://www.npmjs.com/package/botbuilder-azure) verwenden oder mithilfe einer Datenspeicherplattform Ihrer Wahl einen benutzerdefinierten Statusclient implementieren. Im Folgenden sind einige Gründe für die Verwendung eines benutzerdefinierten Statusspeichers aufgeführt:

- Höherer Durchsatz der Status-API (mehr Kontrolle über die Leistung)
- Niedrigere Latenz bei geografischer Verteilung
- Kontrolle über die Region, in der die Daten gespeichert werden (z.B. „USA, Westen“ statt „USA, Osten“)
- Zugriff auf die tatsächlichen Statusdaten
- Datenbank mit Statusdaten nicht für andere Bots freigegeben
- Speichern von mehr als 32 KB

## <a name="prerequisites"></a>Voraussetzungen

- [Node.js](https://nodejs.org/en/).
- [Bot Framework Channel Emulator](~/bot-service-debug-emulator.md).
- Sie benötigen einen Bot für Node.js. Wenn Sie noch keinen haben, wechseln Sie zu [Erstellen eines Bots](bot-builder-nodejs-quickstart.md). 
- [Storage-Explorer](http://storageexplorer.com/)

## <a name="create-azure-account"></a>Erstellen eines Azure-Kontos
Wenn Sie nicht über ein Azure-Konto verfügen, klicken Sie [hier](https://azure.microsoft.com/en-us/free/), um sich für ein kostenloses Konto zu registrieren.

## <a name="set-up-the-azure-table-storage-service"></a>Einrichten des Azure Table Storage-Diensts
1. Nachdem Sie sich beim Azure-Portal angemeldet haben, können Sie einen neuen Azure Table Storage-Dienst erstellen, indem Sie auf **Neu** klicken. 
2. Suchen Sie nach **Speicherkonto**, über das die Azure-Tabelle implementiert wird. Klicken Sie auf **Erstellen**, um mit der Erstellung des Speicherkontos zu beginnen. 
3. Füllen Sie die Felder aus, und klicken Sie dann am unteren Bildschirmrand auf die Schaltfläche **Erstellen**, um den neuen Speicherdienst bereitzustellen. 
4. Nachdem der neue Speicherdienst bereitgestellt wurde, navigieren Sie zum Speicherkonto, das Sie gerade erstellt haben. Dieses wird auf dem Blatt **Speicherkonten** aufgeführt.
4. Klicken Sie auf **Zugriffsschlüssel**, und kopieren Sie den Schlüssel für die spätere Verwendung. Ihr Bot verwendet den **Namen des Speicherkontos** und den **Schlüssel**, um den Speicherdienst zum Speichern von Statusdaten aufzurufen.

## <a name="install-botbuilder-azure-module"></a>Installieren des Moduls „botbuilder-azure“

Um das Modul `botbuilder-azure` über eine Eingabeaufforderung zu installieren, navigieren Sie zum Verzeichnis für den Bot und führen den folgenden npm-Befehl aus:

```nodejs
npm install --save botbuilder-azure
```

## <a name="modify-your-bot-code"></a>Ändern Ihres Bot-Codes

Um Ihre **Azure Table Storage**-Instanz verwenden zu können, müssen Sie der Datei **app.js** Ihres Bots die folgenden Codezeilen hinzufügen.

1. Machen Sie das neu installierte Modul erforderlich.

   ```javascript
   var azure = require('botbuilder-azure'); 
   ```

2. Konfigurieren Sie die Verbindungseinstellungen für die Verbindung mit Azure.
   ```javascript
   // Table storage
   var tableName = "Table-Name"; // You define
   var storageName = "Table-Storage-Name"; // Obtain from Azure Portal
   var storageKey = "Azure-Table-Key"; // Obtain from Azure Portal
   ```
   Die Werte `storageName` und `storageKay` finden Sie im Menü **Zugriffsschlüssel** Ihrer Azure-Tabelle. Wenn der `tableName` nicht in der Azure-Tabelle vorhanden ist, wird dieser für Sie erstellt.

3. Erstellen Sie mithilfe des Moduls `botbuilder-azure` zwei neue Objekte für die Verbindung mit der Azure-Tabelle. Erstellen Sie zunächst eine Instanz von `AzureTableClient`, welche die Konfigurationseinstellungen für die Verbindung übergibt. Erstellen Sie als Nächstes eine Instanz von `AzureBotStorage`, die das `AzureTableClient`-Objekt übergibt. Beispiel: 

   ```javascript
   var azureTableClient = new azure.AzureTableClient(tableName, storageName, storageKey);

   var tableStorage = new azure.AzureBotStorage({gzipData: false}, azureTableClient);
   ```

4. Geben Sie an, dass Sie anstelle des In-Memory-Speichers Ihre benutzerdefinierte Datenbank verwenden und Sitzungsinformationen zur Datenbank hinzufügen möchten. Beispiel: 

   ```javascript
   var bot = new builder.UniversalBot(connector, function (session) {
        // ... Bot code ...

        // capture session user information
        session.userData = {"userId": session.message.user.id, "jobTitle": "Senior Developer"};

        // capture conversation information  
        session.conversationData[timestamp.toISOString().replace(/:/g,"-")] = session.message.text;

        // save data
        session.save();
   })
   .set('storage', tableStorage);
   ```
Jetzt können Sie den Bot mit dem Emulator testen.

## <a name="run-your-bot-app"></a>Ausführen Ihrer Bot-App

Navigieren Sie von der Befehlszeile zu Ihrem Bot-Verzeichnis, und führen Sie Ihren Bot mit dem folgenden Befehl aus:

```nodejs
node app.js
```

## <a name="connect-your-bot-to-the-emulator"></a>Verbinden Ihres Bots mit dem Emulator

Zu diesem Zeitpunkt wird Ihr Bot lokal ausgeführt. Starten Sie den Emulator, und verbinden Sie dann Ihren Bot vom Emulator aus:

1. Geben Sie <strong>http://localhost:port-number/api/messages</strong> in die Adressleiste des Emulators ein. Dabei entspricht „port-number“ der Portnummer, die in dem Browser angezeigt wird, in dem Ihre Anwendung ausgeführt wird. Sie können die Felder <strong>Microsoft-App-ID</strong> und <strong>Microsoft-App-Kennwort</strong> vorerst leer lassen. Sie erhalten diese Informationen später, wenn Sie [Ihren Bot registrieren](~/bot-service-quickstart-registration.md).
2. Klicken Sie auf **Verbinden**.
3. Testen Sie Ihren Bot, indem Sie ihm eine Nachricht senden. Interagieren Sie mit Ihrem Bot wie gewohnt. Wenn Sie fertig sind, wechseln Sie zum **Storage-Explorer**, und zeigen Sie Ihre gespeicherten Statusdaten an.

## <a name="view-data-in-storage-explorer"></a>Anzeigen von Daten im Storage-Explorer

Um die Statusdaten anzuzeigen, öffnen Sie den **Storage-Explorer**, und stellen Sie unter Verwendung Ihrer Anmeldeinformationen für das Azure-Portal eine Verbindung mit Azure her. Sie können auch direkt eine Verbindung mit der Tabelle herstellen, indem Sie den `storageName` und den `storageKey` verwenden und dann zum `tableName` navigieren. 

![Screenshot des Storage-Explorers mit Tabellenzeilen mit Bot-Daten](~/media/bot-builder-nodejs-state-azure-table-storage/bot-builder-nodejs-state-azure-table-storage-query.png)

Ein Datensatz der Unterhaltung in der Spalte **data** sieht wie folgt aus:

```JSON
{
    "2018-05-15T18-23-48.780Z": "I'm the second user",
    "2018-05-15T18-23-55.120Z": "Do you know what time it is?",
    "2018-05-15T18-24-12.214Z": "I'm looking for information about the new process."
}
```

## <a name="next-step"></a>Nächster Schritt

Da Sie nun die vollständige Kontrolle über die Statusdaten Ihres Bots haben, erfahren Sie als Nächstes, wie Sie diese Daten für eine optimale Verwaltung des Konversationsfluss einsetzen können.

> [!div class="nextstepaction"]
> [Verwalten des Konversationsflusses](bot-builder-nodejs-dialog-manage-conversation-flow.md)
