---
title: Durchführen eines Upgrades für Ihren Bot auf die Bot Framework-API v3 | Microsoft-Dokumentation
description: In diesem Artikel erfahren Sie, wie Sie für Ihren Bot ein Upgrade auf die Bot Framework-API v3 durchführen.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 12/13/2017
ms.openlocfilehash: 8d9b2ea2e2133c86428b537427433f9dd15216ee
ms.sourcegitcommit: b15cf37afc4f57d13ca6636d4227433809562f8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/11/2019
ms.locfileid: "54225945"
---
# <a name="upgrade-your-bot-to-bot-framework-api-v3"></a>Durchführen eines Upgrades für Ihren Bot auf die Bot Framework-API v3

Auf der Build 2016 hat Microsoft Microsoft Bot Framework und die erste Iteration der Bot Framework Connector-API zusammen mit dem Bot Builder und Bot Framework Connector SDK angekündigt. Seitdem haben wir Ihr Feedback gesammelt und arbeiten aktiv an der Verbesserung der REST-API und den SDKs.

Im Juli 2016 wurde die Bot Framework-API v3 veröffentlicht, während die Bot Framework-API v1 als veraltet markiert wurde. Bots, die auf die API v1 zurückgriffen, funktionierten im Dezember 2016 in Skype und am 23. Februar 2017 in allen verbleibenden Kanälen nicht mehr. Wenn Sie einen Bot mit der API v1 erstellt haben und diesen wieder funktionsfähig machen wollen, müssen Sie mithilfe der Anweisungen in diesem Artikel ein Upgrade auf die API v3 durchführen. Um sicherzustellen, dass Sie den Upgradeprozess zweifelsohne verstanden haben, gehen Sie diesen Artikel vollständig durch, bevor Sie die ersten Schritte tätigen. 

## <a name="step-1-get-your-app-id-and-password-from-the-bot-framework-portal"></a>Schritt 1: Abrufen Ihrer App-ID und Ihres Kennwort über das Bot Framework-Portal

Melden Sie sich beim [Bot Framework-Portal](https://dev.botframework.com/) an, klicken Sie auf **Meine Bots**, und wählen Sie dann Ihren Bot aus, um das zugehörige Dashboard zu öffnen. Klicken Sie als Nächstes auf den Link **EINSTELLUNGEN**, der sich links auf der Seite unter **Bot Management** (Botverwaltung) befindet. 

Sehen Sie sich auf der Seite mit den Einstellungen im Abschnitt **Konfiguration** den Inhalt des Felds **Microsoft-App-ID** an, und fahren Sie mit den nächsten Schritten fort.

<!-- TODO: Remove this 
### Case 1: App ID field is already populated

If the **App ID** field is already populated, complete these steps:
-->

1. Klicken Sie auf **Microsoft-App-ID und -Kennwort verwalten**.  
![Konfiguration](./media/upgrade/manage-app-id.png)

2. Klicken Sie auf **Neues Kennwort generieren**.  
![Neues Kennwort generieren](./media/upgrade/generate-new-password.png)

3. Kopieren und speichern Sie das neue Kennwort zusammen mit der MSA-App-ID. Sie benötigen diese Werte später noch.  
![Neues Kennwort](./media/upgrade/new-password-generated.png)

Eine andere Methode zum Abrufen Ihrer **Microsoft-App-ID und des Kennworts** finden Sie in [dieser Anleitung](https://blog.botframework.com/2018/07/03/find-your-azure-bots-appid-and-appsecret/).

<!-- TODO: These steps are no longer valid. AppID will always be generated, confirmed with Support Engineers
### Case 2: App ID field is empty

If the **App ID** field is empty, complete these steps:

1. Click **Create Microsoft App ID and password**.  
   ![Create App ID and password](~/media/upgrade/generate-appid-and-password.png)
   > [!IMPORTANT]
   > Do not select the **Version 3.0** radio button yet. You will do this later, after you have [updated your bot code](#update-code).</div>

2. Click **Generate a password to continue**.  
   ![Generate app password](~/media/upgrade/generate-a-password-to-continue.png)

3. Copy and save the new password along with the MSA App Id; you will need these values in the future.  
   ![New password](~/media/upgrade/new-password-generated.png)

4. Click **Finish and go back to Bot Framework**.  
   ![Finish and go back to Portal](~/media/upgrade/finish-and-go-back-to-bot-framework.png)

5. Back on the bot settings page in the Bot Framework Portal, scroll to the bottom of the page and click **Save changes**.  
   ![Save changes](~/media/upgrade/save-changes.png)
-->

## <a id="update-code"></a> Schritt 2: Aktualisieren des Botcodes auf Version 4.0

V1-Bots sind nicht mehr kompatibel. Um Ihren Bot zu aktualisieren, müssen Sie stattdessen einen neuen Bot unter Version 3 erstellen. Falls Sie Ihren alten Code beibehalten möchten, müssen Sie ihn manuell migrieren.

Die einfachste Lösung besteht darin, Ihren Bot mit dem neuen [SDK](https://docs.microsoft.com/en-us/azure/bot-service/?view=azure-bot-service-4.0) neu zu erstellen und bereitzustellen. 

Führen Sie die folgenden Schritte aus, wenn Sie Ihren alten Code beibehalten möchten:

1. Erstellen Sie eine neue Bot-Anwendung.
2. Kopieren Sie Ihren alten Code in Ihre neue Bot-Anwendung.
3. Aktualisieren Sie das SDK über den Nuget-Paket-Manager auf die neueste Version.
4. Beheben Sie alle angezeigten Fehler, und verweisen Sie auf das neue [SDK](https://docs.microsoft.com/en-us/azure/bot-service/?view=azure-bot-service-4.0).
5. Stellen Sie Ihren Bot in Azure bereit, indem Sie [diese Anleitung](https://docs.microsoft.com/en-us/azure/bot-service/bot-builder-howto-deploy-azure?view=azure-bot-service-4.0) befolgen.

<!-- TODO: Remove outdated code 
To update your bot code to version 3.0, complete these steps:

1. Update to the latest version of the [Bot Framework SDK](https://github.com/Microsoft/BotBuilder) for your bot's language.
2. Update your code to apply the necessary changes, according the guidance below.
3. Use the [Bot Framework Emulator](~/bot-service-debug-emulator.md) to test your bot locally and then in the cloud.

The following sections describe the key differences between API v1 and API v3. After you have updated your code to API v3, you can finish the upgrade process by [updating your bot settings](#step-3) in the Bot Framework Portal.
-->

### <a name="botbuilder-and-connector-are-now-one-sdk"></a>Bot Builder und Connector sind nun ein SDK

Anstatt mithilfe mehrerer NuGet-Pakete (oder npm-Module) separate SDKs für Bot Builder und Connector installieren zu müssen, können Sie jetzt beide Bibliotheken in einem einzigen Bot Framework SDK abrufen:

- Bot Framework SDK für .NET: `Microsoft.Bot.Builder` NuGet-Paket
- Bot Framework SDK für Node.js: `botbuilder` NPM-Modul

Das eigenständige SDK `Microsoft.Bot.Connector` gilt ab sofort als veraltet und wird nicht mehr gewartet.

### <a name="message-is-now-activity"></a>„Message“ wurde durch „Activity“ ersetzt

Das Objekt `Message` wurde in der API v3 durch das Objekt `Activity` ersetzt. Der am häufigsten verwendete Aktivitätstyp ist **message**, aber es gibt andere Aktivitätstypen, die für die Kommunikation verschiedener Arten von Informationen an einen Bot oder Kanal verwendet werden können. Weitere Informationen zu Nachrichten finden Sie unter [Erstellen von Nachrichten](~/dotnet/bot-builder-dotnet-create-messages.md) und [Senden und Empfangen von Aktivitäten](~/dotnet/bot-builder-dotnet-connector.md).

### <a name="activity-types--events"></a>Aktivitätstypen und -ereignisse

Einige Ereignisse wurden in API v3 umbenannt und/oder umgestaltet. Darüber hinaus wurde eine neue `ActivityTypes`-Enumeration zum Connector hinzugefügt, um die Notwendigkeit zur Beibehaltung bestimmter Aktivitätstypen zu beseitigen.

- Der Aktivitätstyp `conversationUpdate` ersetzt mit einer einzelnen Methode die Aktivität „Bot/Benutzer zu/aus Unterhaltung hinzugefügt/entfernt“.
- Durch den neuen Aktivitätstyp `typing` wird Ihrem Bot die Angabe ermöglicht, dass er eine Antwort kompiliert, und signalisiert, wenn der Benutzer eine Antwort eingibt.
- Durch den neuen Aktivitätstyp `contactRelationUpdate` wird Ihrem Bot signalisiert, ob er der Kontaktliste eines Benutzers hinzugefügt oder aus dieser entfernt wurde.

Wenn Ihr Bot eine `conversationUpdate`-Aktivität empfängt, geben die Eigenschaften `MembersRemoved` und `MembersAdded` an, wer der Unterhaltung hinzugefügt oder aus dieser entfernt wurde. Wenn Ihr Bot eine `contactRelationUpdate`-Aktivität empfängt, gibt die Eigenschaft `Action` an, ob der Benutzer dem Bot hinzugefügt oder der Bot aus seiner Kontaktliste entfernt wurde. Weitere Informationen zu Aktivitätstypen finden Sie unter [Übersicht über Aktivitäten](~/dotnet/bot-builder-dotnet-activities.md).

### <a name="addressing-messages"></a>Adressieren von Nachrichten

Stellen, an denen Informationen zu Absender, Empfänger und Kanal in einer Nachricht angegeben sind, wurden in API v3 geringfügig geändert:

|Feld in API v1 | Feld in API v3|
|--------|--------|
| `From`-Objekt | `From`-Objekt |
| `To`-Objekt | `Recipient`-Objekt |
| `ChannelConversationID`-Eigenschaft | `Conversation`-Objekt|
| `ChannelId`-Eigenschaft | `ChannelId`-Eigenschaft |

Weitere Informationen zum Adressieren von Nachrichten finden Sie unter [Senden und Empfangen von Aktivitäten](~/dotnet/bot-builder-dotnet-connector.md).

### <a name="sending-replies"></a>Senden von Antworten

In der Bot Framework-API v3 werden alle Antworten an den Benutzer asynchron über eine separat initiierte HTTP-Anforderung gesendet und bei eingehenden Nachrichten an den Bot nicht mehr inline mit der HTTP-POST-Methode. Da keine Nachricht inline an den Benutzer über den Connector zurückgegeben wird, lautet der Rückgabetyp der POST-Methode Ihres Bots `HttpResponseMessage`. Dies bedeutet, dass Ihr Bot die Zeichenfolge, die an den Benutzer gesendet werden soll, nicht synchron zurückgibt, sondern an einer beliebigen Stelle in Ihrem Code eine Antwortnachricht sendet. Die Antwort erfolgt somit nicht als Reaktion auf eine eingehende POST-Anforderung. Bei den folgenden beiden Methoden wird eine Nachricht an eine Unterhaltung gesendet:

- `SendToConversation`
- `ReplyToConversation`

Die `SendToConversation`-Methode fügt die angegebene Nachricht an das Ende der Unterhaltung an, während die `ReplyToConversation`-Methode (für Unterhaltungen, die dies unterstützen) die angegebene Meldung als direkte Antwort auf eine vorherige Nachricht in der Unterhaltung hinzufügt. Weitere Informationen zu diesen Methoden finden Sie unter [Senden und Empfangen von Aktivitäten](~/dotnet/bot-builder-dotnet-connector.md).

### <a name="starting-conversations"></a>Starten von Unterhaltungen

In der Bot Framework-API v3 können Sie eine Unterhaltung starten, indem Sie entweder die neue Methode `CreateDirectConversation` zum Starten einer privaten Unterhaltung mit einem einzelnen Benutzer oder die neue Methode `CreateConversation` zum Starten einer Gruppenunterhaltung mit mehreren Benutzern verwenden. Weitere Informationen zum Starten von Unterhaltungen finden Sie unter [Senden und Empfangen von Aktivitäten](~/dotnet/bot-builder-dotnet-connector.md#start-a-conversation).

### <a name="attachments-and-options"></a>Anlagen und Optionen

Mit der Bot Framework-API v3 wird eine zuverlässigere Implementierung für Anlagen und Karten eingeführt. Der Typ `Options` wird nicht mehr in API v3 unterstützt und wurde stattdessen durch Karten ersetzt. Weitere Informationen zum Hinzufügen von Anlagen zu Nachrichten mit .NET finden Sie unter [Hinzufügen von Medienanlagen zu Nachrichten](~/dotnet/bot-builder-dotnet-add-media-attachments.md) und [Hinzufügen von umfassenden Kartenanlagen zu Nachrichten](~/dotnet/bot-builder-dotnet-add-rich-card-attachments.md).

### <a name="bot-data-storage-bot-state"></a>Bot-Datenspeicher (Bot State)

In der Bot Framework-API v1 wurde die API für die Verwaltung von Bot State-Daten mit der Messaging-API zusammengelegt. In der Bot Framework-API v3 handelt es sich um separate APIs. Ab sofort müssen Sie den Bot State-Dienst verwenden, um Statusdaten zu erhalten (statt davon auszugehen, dass diese im Objekt `Message` enthalten sind) und Statusdaten zu speichern (statt als Bestandteil des Objekts `Message` zu übergeben). Informationen zum Verwalten von Bot-Statusdaten mit dem Bot State-Dienst finden Sie unter [Verwalten von Statusdaten](~/dotnet/bot-builder-dotnet-state.md).

> [!IMPORTANT]
> Die Bot Framework State-Dienst-API wird nicht für Produktionsumgebungen empfohlen und kann in einem zukünftigen Release veraltet sein. Es wird empfohlen, dass Sie Ihren Bot-Code aktualisieren, um den In-Memory-Speicher für Testzwecke zu nutzen, oder eine der **Azure-Erweiterungen** für Produktions-Bots verwenden. Weitere Informationen finden Sie im Thema **Verwalten von Statusdaten** für [.NET](~/dotnet/bot-builder-dotnet-state.md)- bzw. [Node.js](~/nodejs/bot-builder-nodejs-state.md)-Implementierungen.

### <a name="webconfig-changes"></a>Änderungen an „Web.config“

Die Bot Framework-API v1 hat die Authentifizierungseigenschaften mit diesen Schlüsseln in **Web.config** gespeichert:

- `AppID`
- `AppSecret`

Die Bot Framework-API v3 speichert die Authentifizierungseigenschaften mit diesen Schlüsseln in **Web.config**:

- `MicrosoftAppID`
- `MicrosoftAppPassword`

## <a id="step-3"></a> Schritt 3: Bereitstellen Ihres Update-Bots in Azure

Nachdem Sie Ihren Bot-Code auf API v3 aktualisiert haben, können Sie den Bot einfach in Azure bereitstellen, indem Sie [diese Anleitung](https://docs.microsoft.com/en-us/azure/bot-service/bot-builder-howto-deploy-azure?view=azure-bot-service-4.0) befolgen. Da V1 nicht mehr unterstützt wird, wird für alle Bots automatisch Version 3 der API verwendet, wenn diese unter den Azure-Diensten bereitgestellt werden.

<!-- TODO: Documentation set for removal 
1. Sign in to the [Bot Framework Portal](https://dev.botframework.com/).

2. Click **My bots** and select your bot to open its dashboard. 

3. Click the **SETTINGS** link that is located near the top-right corner of the page. 

4. Under **Version 3.0** within the **Configuration** section, paste your bot's endpoint into the **Messaging endpoint** field.  
![Version 3 configuration](~/media/upgrade/paste-new-v3-enpoint-url.png)

5. Select the **Version 3.0** radio button.  
![Select version 3.0](~/media/upgrade/switch-to-v3-endpoint.png)

6. Scroll to the bottom of the page and click **Save changes**.  
![Save changes](~/media/upgrade/save-changes.png)
-->