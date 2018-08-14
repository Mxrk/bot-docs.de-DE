---
title: Verwalten von benutzerdefinierten Statusdaten mit Azure Cosmos DB | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie mithilfe des Bot Builder SDK für Node.js Statusdaten mit Azure Cosmos DB speichern und abrufen.
author: DucVo
ms.author: v-ducvo
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 9d3e1c315399ce3cadc6371ceb93055c836590a6
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39304109"
---
# <a name="manage-custom-state-data-with-azure-cosmos-db-for-nodejs"></a>Verwalten von benutzerdefinierten Statusdaten mit Azure Cosmos DB für Node.js

In diesem Artikel wird beschrieben, wie Sie Cosmos DB-Speicher zum Speichern und Verwalten der Statusdaten Ihres Bots implementieren. Der von Bots verwendete standardmäßige Connector State Service ist nicht für die Produktionsumgebung vorgesehen. Sie sollten entweder die in GitHub verfügbaren [Azure-Erweiterungen](https://www.npmjs.com/package/botbuilder-azure) verwenden oder mithilfe einer Datenspeicherplattform Ihrer Wahl einen benutzerdefinierten Statusclient implementieren. Im Folgenden sind einige Gründe für die Verwendung eines benutzerdefinierten Statusspeichers aufgeführt:

- Höherer Durchsatz der Status-API (mehr Kontrolle über die Leistung)
- Niedrigere Latenz bei geografischer Verteilung
- Kontrolle über den Speicherort der Daten (z. B. USA, Westen statt USA, Osten)
- Zugriff auf die tatsächlichen Statusdaten
- Datenbank mit Statusdaten nicht für andere Bots freigegeben
- Speichern von mehr als 32 KB

## <a name="prerequisites"></a>Voraussetzungen

- [Node.js](https://nodejs.org/en/).
- [Bot Framework-Emulator](~/bot-service-debug-emulator.md)
- Sie benötigen einen Bot für Node.js. Wenn Sie noch keinen haben, wechseln Sie zu [Erstellen eines Bots](bot-builder-nodejs-quickstart.md). 

## <a name="create-azure-account"></a>Erstellen eines Azure-Kontos
Wenn Sie noch kein Azure-Konto haben, klicken Sie [hier](https://azure.microsoft.com/en-us/free/), um sich für ein kostenloses Konto zu registrieren.

## <a name="set-up-the-azure-cosmos-db-database"></a>Einrichten der Azure Cosmos DB-Datenbank
1. Nachdem Sie sich beim Azure-Portal angemeldet haben, können Sie eine neue *Azure Cosmos DB*-Datenbank erstellen, indem Sie auf **Neu** klicken. 
2. Klicken Sie auf **Datenbanken**. 
3. Suchen Sie nach **Azure Cosmos DB**, und klicken Sie auf **Erstellen**.
4. Füllen Sie die Felder aus. Wählen Sie für das Feld **API** den Eintrag **SQL (DocumentDB)** aus. Nachdem Sie alle Felder ausgefüllt haben, klicken Sie am unteren Bildschirmrand auf die Schaltfläche **Erstellen**, um die neue Datenbank bereitzustellen. 
5. Navigieren Sie nach der Bereitstellung zu Ihrer neuen Datenbank. Klicken Sie auf **Zugriffsschlüssel**, um nach Schlüsseln und Verbindungszeichenfolgen zu suchen. Ihr Bot verwendet diese Informationen, um den Speicherdienst zum Speichern von Statusdaten aufzurufen.

## <a name="install-botbuilder-azure-module"></a>Installieren des Moduls „botbuilder-azure“

Um das Modul `botbuilder-azure` von einer Befehlszeile zu installieren, navigieren Sie zum Verzeichnis für den Bot und führen den folgenden npm-Befehl aus:

```nodejs
npm install --save botbuilder-azure
```

## <a name="modify-your-bot-code"></a>Ändern Ihres Botcodes

Um Ihre **Azure Cosmos DB**-Datenbank verwenden zu können, müssen Sie der Datei **app.js** Ihres Bots die folgenden Codezeilen hinzufügen.

1. Machen Sie das neu installierte Modul erforderlich.

   ```javascript
   var azure = require('botbuilder-azure'); 
   ```

2. Konfigurieren Sie die Verbindungseinstellungen für die Verbindung zu Azure.
   ```javascript
   var documentDbOptions = {
       host: 'Your-Azure-DocumentDB-URI', 
       masterKey: 'Your-Azure-DocumentDB-Key', 
       database: 'botdocs',   
       collection: 'botdata'
   };
   ```
   Die Werte `host` und `masterKey` finden Sie im Menü **Keys** (Schlüssel) Ihrer Datenbank. Wenn die Einträge `database` und `collection` in der Azure-Datenbank nicht vorhanden sind, werden sie erstellt.

3. Erstellen Sie mithilfe des Moduls `botbuilder-azure` zwei neue Objekte für die Verbindung mit der Azure-Datenbank. Erstellen Sie zunächst eine Instanz von `DocumentDBClient`, welche die Konfigurationseinstellungen für die Verbindung übergibt (oben definiert als `documentDbOptions`). Erstellen Sie als Nächstes eine Instanz von `AzureBotStorage`, die das `DocumentDBClient`-Objekt übergibt. Beispiel: 
   ```javascript
   var docDbClient = new azure.DocumentDbClient(documentDbOptions);

   var cosmosStorage = new azure.AzureBotStorage({ gzipData: false }, docDbClient);
   ```

4. Geben Sie an, dass Sie anstelle des In-Memory-Speichers Ihre benutzerdefinierte Datenbank verwenden möchten. Beispiel: 

   ```javascript
   var bot = new builder.UniversalBot(connector, function (session) {
        // ... Bot code ...
   })
   .set('storage', cosmosStorage);
   ```

Jetzt können Sie den Bot mit dem Emulator testen.

## <a name="run-your-bot-app"></a>Ausführen Ihrer Bot-App

Navigieren Sie von der Befehlszeile zu Ihrem Bot Verzeichnis, und führen Sie Ihren Bot mit dem folgenden Befehl aus:

```nodejs
node app.js
```

## <a name="connect-your-bot-to-the-emulator"></a>Verbinden Ihres Bots mit dem Emulator

Zu diesem Zeitpunkt wird Ihr Bot lokal ausgeführt. Starten Sie den Emulator, und verbinden Sie dann Ihren Bot vom Emulator aus:

1. Geben Sie  <strong>in die Adressleiste des Emulators ein. Dabei entspricht http://localhost:port-number/api/messagesport-number</strong> der Portnummer, die in dem Browser angezeigt wird, in dem Ihre Anwendung ausgeführt wird. Sie können die Felder <strong>Microsoft App ID</strong> (Microsoft-App-ID) und <strong>Microsoft App Password</strong> (Microsoft-App-Kennwort) vorerst leer lassen. Sie erhalten diese Informationen später, wenn Sie [Ihren Bot registrieren](~/bot-service-quickstart-registration.md).
2. Klicken Sie auf **Verbinden**.
3. Testen Sie Ihren Bot, indem Sie ihm eine Nachricht senden. Interagieren Sie mit Ihrem Bot wie gewohnt. Wechseln Sie, wenn Sie fertig sind, zum **Storage-Explorer**, und zeigen Sie Ihre gespeicherten Statusdaten an.

## <a name="view-state-data-on-azure-portal"></a>Anzeigen von Statusdaten im Azure-Portal

Wenn Sie die Statusdaten anzeigen möchten, melden Sie sich bei Ihrem Azure-Portal an, und navigieren Sie zu Ihrer Datenbank. Klicken Sie auf **Daten-Explorer (Vorschau)**, um zu überprüfen, ob die Statusinformationen von Ihrem Bot gespeichert wurden.

## <a name="next-step"></a>Nächster Schritt

Da Sie nun die vollständige Kontrolle über die Statusdaten Ihres Bots haben, erfahren Sie als Nächstes, wie Sie diese Daten für eine optimale Verwaltung des Konversationsflusses einsetzen können.

> [!div class="nextstepaction"]
> [Verwalten des Konversationsflusses](bot-builder-nodejs-dialog-manage-conversation-flow.md)