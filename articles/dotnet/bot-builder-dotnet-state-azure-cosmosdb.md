---
title: Verwalten von benutzerdefinierten Statusdaten mit Azure Cosmos DB | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie mithilfe des Bot Builder SDK für .NET Statusdaten mit Azure Cosmos DB speichern und abrufen.
author: kaiqb
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 5951df2fa7e89cce03bd8b7f9404585f720a9247
ms.sourcegitcommit: 3bf3dbb1a440b3d83e58499c6a2ac116fe04b2f6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/23/2018
ms.locfileid: "46707306"
---
# <a name="manage-custom-state-data-with-azure-cosmos-db-for-net"></a>Verwalten von benutzerdefinierten Statusdaten mit Azure Cosmos DB für .NET

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

In diesem Artikel wird beschrieben, wie Sie Azure Cosmos DB zum Speichern und Verwalten der Statusdaten Ihres Bots implementieren. Der von Bots verwendete standardmäßige Connector State Service ist nicht für die Produktionsumgebung vorgesehen. Sie sollten entweder die in GitHub verfügbaren [Azure-Erweiterungen](https://github.com/Microsoft/BotBuilder-Azure) verwenden oder mithilfe einer Datenspeicherplattform Ihrer Wahl einen benutzerdefinierten Statusclient implementieren. Im Folgenden sind einige Gründe für die Verwendung eines benutzerdefinierten Statusspeichers aufgeführt:
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
 - [Bot Framework-Emulator](~/bot-service-debug-emulator.md)
 
## <a name="create-azure-account"></a>Erstellen eines Azure-Kontos
Wenn Sie noch kein Azure-Konto haben, klicken Sie [hier](https://azure.microsoft.com/en-us/free/), um sich für ein kostenloses Konto zu registrieren.

## <a name="set-up-the-azure-cosmos-db-database"></a>Einrichten der Azure Cosmos DB-Datenbank
1. Nachdem Sie sich beim Azure-Portal angemeldet haben, können Sie eine neue *Azure Cosmos DB*-Datenbank erstellen, indem Sie auf **Neu** klicken. 
2. Klicken Sie auf **Datenbanken**. 
3. Suchen Sie nach **Azure Cosmos DB**, und klicken Sie auf **Erstellen**.
4. Füllen Sie die Felder aus. Wählen Sie für das Feld **API** den Eintrag **SQL (DocumentDB)** aus. Nachdem Sie alle Felder ausgefüllt haben, klicken Sie am unteren Bildschirmrand auf die Schaltfläche **Erstellen**, um die neue Datenbank bereitzustellen. 
5. Navigieren Sie nach der Bereitstellung zu Ihrer neuen Datenbank. Klicken Sie auf **Zugriffsschlüssel**, um nach Schlüsseln und Verbindungszeichenfolgen zu suchen. Ihr Bot verwendet diese Informationen, um den Speicherdienst zum Speichern von Statusdaten aufzurufen.

## <a name="install-nuget-packages"></a>Installieren von NuGet-Paketen
1. Öffnen Sie ein vorhandenes C#-Botprojekt, oder erstellen Sie in Visual Studio mithilfe der Botvorlage ein neues Botprojekt. 
2. Installieren Sie die folgenden NuGet-Pakete:
   - Microsoft.Bot.Builder.Azure
   - Autofac.WebApi2

## <a name="add-connection-string"></a>Verbindungszeichenfolge hinzufügen 
Fügen Sie in der Datei „Web.config“ die folgenden Einträge hinzu:
```XML
<add key="DocumentDbUrl" value="Your DocumentDB URI"/>
<add key="DocumentDbKey" value="Your DocumentDB Key"/>
```
Ersetzen Sie den Wert durch Ihren URI und Primärschlüssel aus Ihrer Azure Cosmos DB. Speichern Sie die Datei „Web.config“.

## <a name="modify-your-bot-code"></a>Ändern Ihres Botcodes
Fügen Sie zum Verwenden des **Azure Cosmos DB**-Speichers der Datei **Global.asax.cs** Ihres Bots die folgenden Codezeilen in der **Application_Start()**-Methode hinzu.

```cs
using System;
using Autofac;
using System.Web.Http;
using System.Configuration;
using Microsoft.Bot.Connector;
using Microsoft.Bot.Builder.Azure;
using Microsoft.Bot.Builder.Dialogs;
using Microsoft.Bot.Builder.Dialogs.Internals;

namespace SampleApp
{
    public class WebApiApplication : System.Web.HttpApplication
    {
        protected void Application_Start()
        {
            GlobalConfiguration.Configure(WebApiConfig.Register);
            var uri = new Uri(ConfigurationManager.AppSettings["DocumentDbUrl"]);
            var key = ConfigurationManager.AppSettings["DocumentDbKey"];
            var store = new DocumentDbBotDataStore(uri, key);

            Conversation.UpdateContainer(
                        builder =>
                        {
                            builder.Register(c => store)
                                .Keyed<IBotDataStore<BotData>>(AzureModule.Key_DataStore)
                                .AsSelf()
                                .SingleInstance();

                            builder.Register(c => new CachingBotDataStore(store, CachingBotDataStoreConsistencyPolicy.ETagBasedConsistency))
                                .As<IBotDataStore<BotData>>()
                                .AsSelf()
                                .InstancePerLifetimeScope();

                        });

        }
    }
}
```

Speichern Sie die Datei „Global.asax.cs“. Jetzt können Sie den Bot mit dem Emulator testen.

## <a name="run-your-bot-app"></a>Ausführen Ihrer Bot-App
Führen Sie Ihren Bot in Visual Studio aus. Der von Ihnen hinzugefügte Code erstellt die benutzerdefinierte **BotData**-Tabelle in Azure.

## <a name="connect-your-bot-to-the-emulator"></a>Verbinden Ihres Bots mit dem Emulator
Zu diesem Zeitpunkt wird Ihr Bot lokal ausgeführt. Starten Sie als Nächstes den Emulator, und verbinden Sie dann Ihren Bot im Emulator:
1. Geben Sie in die Adressleiste die Zeichenfolge http://localhost:port-number/api/messages ein, wobei „port-number“ der Portnummer entspricht, die in dem Browser angezeigt wird, in dem Ihre Anwendung ausgeführt wird. Sie können die Felder <strong>Microsoft-App-ID</strong> und <strong>Microsoft-App-Kennwort</strong> vorerst leer lassen. Sie erhalten diese Informationen später, wenn Sie [Ihren Bot registrieren](~/bot-service-quickstart-registration.md).
2. Klicken Sie auf **Verbinden**. 
3. Testen Sie Ihren Bot, indem Sie einige Nachrichten im Emulator eingeben. 

## <a name="view-state-data-on-azure-portal"></a>Anzeigen von Statusdaten im Azure-Portal
Wenn Sie die Statusdaten anzeigen möchten, melden Sie sich bei Ihrem Azure-Portal an, und navigieren Sie zu Ihrer Datenbank. Klicken Sie auf **Daten-Explorer (Vorschau)**, um zu überprüfen, ob die Statusinformationen von Ihrem Bot gespeichert wurden. 

## <a name="next-steps"></a>Nächste Schritte
In diesem Artikel haben Sie Cosmos DB zum Speichern und Verwalten der Daten Ihres Bots verwendet. Als Nächstes erfahren Sie, wie Sie den Konversationsfluss mithilfe von Dialogen modellieren.

> [!div class="nextstepaction"]
> [Verwalten des Konversationsflusses](bot-builder-dotnet-manage-conversation-flow.md)

## <a name="additional-resources"></a>Zusätzliche Ressourcen
Wenn Sie noch nicht mit den im obigen Code verwendeten Steuerungsumkehrcontainern (Inversion of Control, IoC) und Dependency Injection-Mustern vertraut sind, besuchen Sie die [Autofac](http://autofac.readthedocs.io/en/latest/)-Website, um entsprechende Informationen darüber zu erhalten. 

Sie können auch ein [Beispiel](https://github.com/Microsoft/BotBuilder-Azure/tree/master/CSharp/Samples/DocumentDb) von GitHub herunterladen, um weitere Informationen zur Verwendung von Cosmos DB zum Verwalten des Status zu erhalten. 
