---
title: Verwalten von Zustandsdaten | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie mithilfe des Bot Framework SDK für .NET Statusdaten speichern und abrufen.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 12/13/17
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 3ee3af72d1c03faf485a64adb8d9fa2548f5d99d
ms.sourcegitcommit: 3cc768a8e676246d774a2b62fb9c688bbd677700
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/16/2019
ms.locfileid: "54323666"
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

## <a name="handle-concurrency-issues"></a>Beheben von Parallelitätsproblemen

Möglicherweise erhält Ihr Bot eine Fehlerantwort mit dem HTTP-Statuscode **412 – Fehler bei Vorbedingung**, wenn er versucht, Zustandsdaten zu speichern, und eine andere Instanz des Bots die Daten verändert hat. Sie können Ihren Bot wie im folgenden Beispiel dargestellt so konzipieren, dass er dieses Szenario verarbeiten kann.

[!code-csharp[Handle exception saving state](../includes/code/dotnet-state.cs#handleException)]

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Bot Framework troubleshooting guide (Anleitung zur Fehlerbehebung bei Bot Framework)](../bot-service-troubleshoot-general-problems.md)
- <a href="/dotnet/api/?view=botbuilder-3.11.0" target="_blank">Referenz zum Bot Framework SDK für .NET</a>

[Activity]: https://docs.botframework.com/en-us/csharp/builder/sdkreference/dc/d2f/class_microsoft_1_1_bot_1_1_connector_1_1_activity.html
