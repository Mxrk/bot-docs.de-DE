---
title: Verwalten von benutzerdefinierten Statusdaten mit Azure Table Storage | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie mithilfe des Bot Builder SDK für .NET Statusdaten mit Azure Table Storage speichern und abrufen.
author: kaiqb
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: e2d8e6a5a390a27b61b11ad22f07ce0ab95f1686
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39303917"
---
# <a name="manage-custom-state-data-with-azure-table-storage-for-net"></a>Verwalten von benutzerdefinierten Statusdaten mit Azure Table Storage für .NET
In diesem Artikel wird beschrieben, wie Sie Azure Table Storage zum Speichern und Verwalten der Statusdaten Ihres Bots implementieren. Der von Bots verwendete standardmäßige Connector State Service ist nicht für die Produktionsumgebung vorgesehen. Sie sollten entweder die in GitHub verfügbaren [Azure-Erweiterungen](https://github.com/Microsoft/BotBuilder-Azure) verwenden oder mithilfe einer Datenspeicherplattform Ihrer Wahl einen benutzerdefinierten Statusclient implementieren. Im Folgenden sind einige Gründe für die Verwendung eines benutzerdefinierten Statusspeichers aufgeführt:
 - Höherer Durchsatz der Status-API (mehr Kontrolle über die Leistung)
 - Niedrigere Latenz für die geografische Verteilung
 - Kontrolle über den Speicherort der Daten
 - Zugriff auf die tatsächlichen Statusdaten
 - Speichern von mehr als 32 KB Daten

## <a name="prerequisites"></a>Voraussetzungen
Sie benötigen Folgendes:
 - [Microsoft Azure-Konto](https://azure.microsoft.com/en-us/free/)
 - [Visual Studio 2015 (oder höher)](https://www.visualstudio.com/)
 - [Bot Builder Azure-NuGet-Paket](https://www.nuget.org/packages/Microsoft.Bot.Builder.Azure/)
 - [Autofac Web Api2-NuGet-Paket](https://www.nuget.org/packages/Autofac.WebApi2/)
 - [Bot Framework-Emulator](https://emulator.botframework.com/)
 - [Azure Storage-Explorer](http://storageexplorer.com/)
 
## <a name="create-azure-account"></a>Erstellen eines Azure-Kontos
Wenn Sie nicht über ein Azure-Konto verfügen, klicken Sie [hier](https://azure.microsoft.com/en-us/free/), um sich für ein kostenloses Konto zu registrieren.

## <a name="set-up-the-azure-table-storage-service"></a>Einrichten des Azure Table Storage-Diensts
1. Nachdem Sie sich beim Azure-Portal angemeldet haben, können Sie einen neuen Azure-Tabellenspeicherdienst erstellen, indem Sie auf **Neu** klicken. 
2. Suchen Sie nach **Speicherkonto**, über das die Azure-Tabelle implementiert wird. 
3. Füllen Sie die Felder aus, und klicken Sie dann am unteren Bildschirmrand auf die Schaltfläche **Erstellen**, um den neuen Speicherdienst bereitzustellen. Nach der Bereitstellung des neuen Speicherdiensts werden die für Sie verfügbaren Funktionen und Optionen angezeigt.
4. Wählen Sie links die Registerkarte **Zugriffsschlüssel** aus, und kopieren Sie die Verbindungszeichenfolge zur späteren Verwendung. Ihr Bot verwendet diese Verbindungszeichenfolge, um den Speicherdienst zum Speichern von Statusdaten aufzurufen.

## <a name="install-nuget-packages"></a>Installieren von NuGet-Paketen
1. Öffnen Sie ein vorhandenes C#-Botprojekt, oder erstellen Sie in Visual Studio mithilfe der C#-Botvorlage ein neues Botprojekt. 
2. Installieren Sie die folgenden NuGet-Pakete:
   - Microsoft.Bot.Builder.Azure
   - Autofac.WebApi2

## <a name="add-connection-string"></a>Verbindungszeichenfolge hinzufügen 
Fügen Sie in Ihrer Datei „Web.config“ den folgenden Eintrag hinzu: 
```XML
  <connectionStrings>
    <add name="StorageConnectionString"
    connectionString="YourConnectionString"/>
  </connectionStrings>
```
Ersetzen Sie „YourConnectionString“ durch die zuvor gespeicherte Verbindungszeichenfolge für den Azure-Tabellenspeicher. Speichern Sie die Datei „Web.config“.

## <a name="modify-your-bot-code"></a>Ändern Ihres Botcodes
Fügen Sie der Datei „Global.asax.cs“ die folgenden `using`-Anweisungen hinzu:
```cs
using Autofac;
using System.Configuration;
using Microsoft.Bot.Connector;
using Microsoft.Bot.Builder.Azure;
using Microsoft.Bot.Builder.Dialogs;
using Microsoft.Bot.Builder.Dialogs.Internals;
```
Erstellen Sie in der Methode `Application_Start()` eine Instanz der `TableBotDataStore`-Klasse. Die `TableBotDataStore`-Klasse implementiert die `IBotDataStore<BotData>`-Schnittstelle. Mit der `IBotDataStore`-Schnittstelle können Sie die Connector State Service-Standardverbindung außer Kraft setzen.
 ```cs
 var store = new TableBotDataStore(ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString);
 ```
Registrieren Sie den Dienst wie unten dargestellt:
 ```cs
 Conversation.UpdateContainer(
            builder =>
            {
                builder.Register(c => store)
                          .Keyed<IBotDataStore<BotData>>(AzureModule.Key_DataStore)
                          .AsSelf()
                          .SingleInstance();

                builder.Register(c => new CachingBotDataStore(store,
                           CachingBotDataStoreConsistencyPolicy
                           .ETagBasedConsistency))
                           .As<IBotDataStore<BotData>>()
                           .AsSelf()
                           .InstancePerLifetimeScope();

                
            });
 ```
Speichern Sie die Datei „Global.asax.cs“.

## <a name="run-your-bot-app"></a>Ausführen Ihrer Bot-App
Führen Sie Ihren Bot in Visual Studio aus. Der von Ihnen hinzugefügte Code erstellt die benutzerdefinierte **BotData**-Tabelle in Azure.

## <a name="connect-your-bot-to-the-emulator"></a>Verbinden Ihres Bots mit dem Emulator
Zu diesem Zeitpunkt wird Ihr Bot lokal ausgeführt. Starten Sie als Nächstes den Emulator, und verbinden Sie dann Ihren Bot im Emulator:
1. Geben Sie in die Adressleiste die Zeichenfolge http://localhost:port-number/api/messages ein, wobei „port-number“ der Portnummer entspricht, die in dem Browser angezeigt wird, in dem Ihre Anwendung ausgeführt wird. Sie können die Felder <strong>Microsoft-App-ID</strong> und <strong>Microsoft-App-Kennwort</strong> vorerst leer lassen. Sie erhalten diese Informationen später, wenn Sie [Ihren Bot registrieren](~/bot-service-quickstart-registration.md).
2. Klicken Sie auf **Verbinden**. 
3. Testen Sie Ihren Bot, indem Sie einige Nachrichten im Emulator eingeben. 

## <a name="view-data-in-azure-table-storage"></a>Anzeigen von Daten in Azure Table Storage
Um die Statusdaten anzuzeigen, öffnen Sie **Storage-Explorer**, und stellen Sie unter Verwendung Ihrer Anmeldeinformationen für das Azure-Portal eine Verbindung mit Azure her. Sie können auch direkt eine Verbindung mit der Tabelle herstellen, indem Sie den Speichernamen und den Speicherschlüssel verwenden und dann zum Tabellennamen navigieren.  

## <a name="next-steps"></a>Nächste Schritte
In diesem Artikel haben Sie Azure Table Storage zum Speichern und Verwalten von Daten Ihres Bots implementiert. Als Nächstes erfahren Sie, wie Sie den Konversationsfluss mithilfe von Dialogen modellieren.

> [!div class="nextstepaction"]
> [Verwalten des Konversationsflusses](bot-builder-dotnet-manage-conversation-flow.md)


## <a name="additional-resources"></a>Zusätzliche Ressourcen

Wenn Sie noch nicht mit den im obigen Code verwendeten Steuerungsumkehrcontainern (Inversion of Control, IoC) und Dependency Injection-Mustern vertraut sind, besuchen Sie die [Autofac](http://autofac.readthedocs.io/en/latest/)-Website, um entsprechende Informationen darüber zu erhalten. 

Sie können auch ein vollständiges [Azure Table Storage](https://github.com/Microsoft/BotBuilder-Azure/tree/master/CSharp/Samples/AzureTable)-Beispiel von GitHub herunterladen.
