---
title: Verwalten von Zustandsdaten | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie mithilfe des Bot Builder SDK für .NET Zustandsdaten speichern und abrufen.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 12/13/17
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: deb8361a5cca2f37840abb1180c2de571ee08143
ms.sourcegitcommit: b78fe3d8dd604c4f7233740658a229e85b8535dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2018
ms.locfileid: "49999757"
---
# <a name="manage-state-data"></a>Verwalten von Statusdaten

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

> [!div class="op_single_selector"]
> - [.NET](../dotnet/bot-builder-dotnet-state.md)
> - [Node.js](../nodejs/bot-builder-nodejs-state.md)

[!INCLUDE [State concept overview](../includes/snippet-dotnet-concept-state.md)]

## <a name="in-memory-data-storage"></a>In-Memory-Datenspeicher

Der In-Memory-Datenspeicher dient nur zu Testzwecken. Dieser Speicher ist flüchtig und temporär. Die Daten werden jedes Mal gelöscht, wenn der Bot neu gestartet wird. Sie müssen Folgendes tun, um den In-Memory-Speicher zu Testzwecken verwenden zu können: 

Installieren Sie die folgenden NuGet-Pakete: 
- Microsoft.Bot.Builder.Azure
- Autofac.WebApi2

Erstellen Sie für die **Application_Start**-Methode eine neue Instanz des In-Memory-Speichers, und registrieren Sie den neuen Datenspeicher:

```cs
// Global.asax file

var store = new InMemoryDataStore();

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
GlobalConfiguration.Configure(WebApiConfig.Register);

```

Sie können mit dieser Methode Ihren eigenen benutzerdefinierten Datenspeicher festlegen oder eine der *Azure-Erweiterungen* verwenden.

## <a name="manage-custom-data-storage"></a>Verwalten eines benutzerdefinierten Datenspeichers

Im Hinblick auf Leistung und Sicherheit in der Produktionsumgebung können Sie Ihren eigenen Datenspeicher festlegen oder das Implementieren einer der folgenden Datenspeicheroptionen in Betracht ziehen:

1. [Verwalten von Statusdaten mit Cosmos DB](bot-builder-dotnet-state-azure-cosmosdb.md)

2. [Manage state data with Table storage (Verwalten von Zustandsdaten mit Tabellenspeicher)](bot-builder-dotnet-state-azure-table-storage.md)

Der Mechanismus zum Festlegen und Speichern von Daten über das Bot Framework SDK für .NET ist bei diesen beiden [Azure-Erweiterungen](https://www.nuget.org/packages/Microsoft.Bot.Builder.Azure/) und dem In-Memory-Datenspeicher identisch.

## <a name="bot-state-methods"></a>Botzustandsmethoden

In dieser Tabelle sind die Methoden aufgeführt, die Sie zum Verwalten der Zustandsdaten verwenden können.

| Methode | Zuordnung | Ziel |                                                
|----|----|----|
| `GetUserData` | Benutzer | Abrufen von Zustandsdaten, die zuvor auf dem angegebenen Kanal für den Benutzer gespeichert wurden |
| `GetConversationData` | Unterhaltung | Abrufen von Zustandsdaten, die zuvor auf dem angegebenen Kanal für die Konversation gespeichert wurden |
| `GetPrivateConversationData` | Benutzer und Konversation | Abrufen von Zustandsdaten, die zuvor auf dem angegebenen Kanal für den Benutzer in der Konversation gespeichert wurden |
| `SetUserData` | Benutzer | Speichern von Zustandsdaten für den Benutzer auf dem angegebenen Kanal |
| `SetConversationData` | Unterhaltung | Speichern Sie Zustandsdaten für die Konversation auf dem angegebenen Kanal. <br/><br/>**Hinweis**: Da über die `DeleteStateForUser`-Methode keine Daten gelöscht werden, die über die `SetConversationData`-Methode gespeichert wurden, dürfen Sie diese Methode NICHT verwenden, um die personenbezogenen Informationen eines Benutzers zu speichern. |
| `SetPrivateConversationData` | Benutzer und Konversation | Speichern von Zustandsdaten für den Benutzer in der Konversation auf dem angegebenen Kanal |
| `DeleteStateForUser` | Benutzer | Löschen Sie Zustandsdaten für den Benutzer, die zuvor über die Methoden `SetUserData` oder `SetPrivateConversationData` gespeichert wurden. <br/><br/>**Hinweis**: Ihr Bot sollte diese Methode aufrufen, wenn er eine Aktivität vom Typ [deleteUserData](bot-builder-dotnet-activities.md#deleteuserdata) oder vom Typ [contactRelationUpdate](bot-builder-dotnet-activities.md#contactrelationupdate) empfängt, die darauf hindeutet, dass der Bot aus der Kontaktliste des Benutzers entfernt wurde. |

Wenn Ihr Bot mithilfe einer der **Set...Data**-Methoden Zustandsdaten speichert, sind diese in Nachrichten enthalten, die er später in demselben Kontext empfängt. Er kann auf diese Daten mithilfe der **Get...Data**-Methode zugreifen.

## <a name="useful-properties-for-managing-state-data"></a>Nützliche Eigenschaften zum Verwalten von Zustandsdaten

Jedes [Activity][Activity]-Objekt enthält Eigenschaften, die Sie zum Verwalten von Zustandsdaten verwenden.

| Eigenschaft | BESCHREIBUNG | Anwendungsfall |
|----|----|----|
| `From` | Ermittelt einen eindeutigen Benutzer oder Kanal | Speichern und Abrufen von Zustandsdaten, die dem Benutzer zugeordnet sind |
| `Conversation` | Ermittelt eine eindeutige Konversation | Speichern und Abrufen von Zustandsdaten, die der Konversation zugeordnet sind |
| `From` und `Conversation` | Ermittelt einen eindeutigen Benutzer und eine eindeutige Konversation | Speichern und Abrufen von Zustandsdaten, die einem bestimmten Benutzer im Kontext einer bestimmten Konversation zugeordnet sind |

> [!NOTE]
> Sie können diese Eigenschaftswerte sogar als Schlüssel verwenden, wenn Sie Zustandsdaten in Ihrer Datenbank anstatt in Ihrem Bot Framework-Speicher für Zustandsdaten speichern.

## <a id="state-client"></a> Erstellen eines Zustandsclients

Mithilfe des `StateClient`-Objekts können Sie Zustandsdaten mithilfe des Bot Builder SDK für .NET verwalten. Wenn Sie Zugriff auf eine Nachricht haben, die Teil des Kontexts ist, in dem Sie Zustandsdaten verwalten möchten, können Sie einen Zustandsclient erstellen, indem Sie die `GetStateClient`-Methode für das `Activity`-Objekt aufrufen.

[!code-csharp[Get State client](../includes/code/dotnet-state.cs#getStateClient1)]

Wenn Sie keinen Zugriff auf eine Nachricht haben, die Teil des Kontexts ist, in dem Sie Zustandsdaten verwalten möchten, können Sie einen Zustandsclient erstellen, indem Sie nur eine neue Instanz der `StateClient`-Klasse erstellen. In diesem Beispiel stellen `microsoftAppId` und `microsoftAppPassword` Anmeldeinformationen für die Bot Framework-Authentifizierung dar, die Sie für Ihren Bot erhalten, wenn Sie diesen [erstellen](../bot-service-quickstart.md).

> [!NOTE]
> Wie Sie **AppID** und **AppPassword** für Ihren Bot finden, ist unter [MicrosoftAppID und MicrosoftAppPassword](~/bot-service-manage-overview.md#microsoftappid-and-microsoftapppassword) beschrieben.

[!code-csharp[Get State client](../includes/code/dotnet-state.cs#getStateClient2)]

> [!NOTE]
> Der Standardzustandsclient wird in einem zentralen Dienst gespeichert. Für einige Kanäle sollten Sie eine Zustands-API verwenden, die auf dem Kanal selbst gehostet wird, damit Zustandsdaten in einem kompatiblen Speicher gespeichert werden, die der Kanal bereitstellt.

## <a name="get-state-data"></a>Abrufen von Zustandsdaten

Jede **Get...Data**-Methode gibt ein `BotData`-Objekt zurück, das die Zustandsdaten für den angegebene Benutzer und/oder die angegebene Konversation enthält. Rufen Sie die `GetProperty`-Methode auf, um einen bestimmten Eigenschaftswert eines `BotData`-Objekts abzurufen. 

Das folgende Codebeispiel zeigt, wie Sie eine typisierte Eigenschaft aus Benutzerdaten abrufen. 

[!code-csharp[Get state property](../includes/code/dotnet-state.cs#getProperty1)]

Das folgende Codebeispiel zeigt, wie Sie eine Eigenschaft eines komplexen Typs aus Benutzerdaten abrufen.

[!code-csharp[Get state property](../includes/code/dotnet-state.cs#getProperty2)]

Wenn keine Zustandsdaten zum Benutzer und/oder zu der Konversation vorhanden sind, die für einen **Get...Data**-Methodenaufruf angegeben sind, enthält das `BotData`-Objekt, das zurückgegeben wird, die folgenden Eigenschaftswerte: 
- `BotData.Data` = NULL
- `BotData.ETag` = *

## <a name="save-state-data"></a>Speichern von Zustandsdaten

Wenn Sie Zustandsdaten speichern wollen, rufen Sie zunächst das `BotData`-Objekt ab, indem Sie die betreffende **Get...Data**-Methode aufrufen. Aktualisieren Sie dieses dann, indem Sie die `SetProperty`-Methode für sämtliche Eigenschaften aufrufen, die Sie aktualisieren möchten, und speichern Sie dieses anschließend, indem Sie die betreffende **Set...Data**-Methode aufrufen. 

> [!NOTE]
> Sie können bis zu 32 KB an Daten für jeden Benutzer bzw. jede Konversation auf einem Kanal und jeden Benutzer im Kontext einer Konversation auf einem Kanal speichern. 

Das folgende Codebeispiel zeigt, wie Sie eine typisierte Eigenschaft in Benutzerdaten speichern.

[!code-csharp[Set state property](../includes/code/dotnet-state.cs#setProperty1)]

Das folgende Codebeispiel zeigt, wie Sie eine komplexe Typeigenschaft in Benutzerdaten speichern. 

[!code-csharp[Set state property](../includes/code/dotnet-state.cs#setProperty2)]

## <a name="handle-concurrency-issues"></a>Beheben von Parallelitätsproblemen

Möglicherweise erhält Ihr Bot eine Fehlerantwort mit dem HTTP-Statuscode **412 – Fehler bei Vorbedingung**, wenn er versucht, Zustandsdaten zu speichern, und eine andere Instanz des Bots die Daten verändert hat. Sie können Ihren Bot wie im folgenden Beispiel dargestellt so konzipieren, dass er dieses Szenario verarbeiten kann.

[!code-csharp[Handle exception saving state](../includes/code/dotnet-state.cs#handleException)]

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Bot Framework troubleshooting guide (Anleitung zur Fehlerbehebung bei Bot Framework)](../bot-service-troubleshoot-general-problems.md)
- <a href="/dotnet/api/?view=botbuilder-3.11.0" target="_blank">Bot Builder SDK for .NET Reference (Referenz zum Bot Builder SDK für .NET)</a>

[Activity]: https://docs.botframework.com/en-us/csharp/builder/sdkreference/dc/d2f/class_microsoft_1_1_bot_1_1_connector_1_1_activity.html
