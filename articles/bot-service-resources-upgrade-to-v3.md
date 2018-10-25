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
ms.openlocfilehash: 6ee7120536d42257dde2ed1411df32d807268e33
ms.sourcegitcommit: b78fe3d8dd604c4f7233740658a229e85b8535dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2018
ms.locfileid: "50000017"
---
# <a name="upgrade-your-bot-to-bot-framework-api-v3"></a>Durchführen eines Upgrades für Ihren Bot auf die Bot Framework-API v3

Auf der Build 2016 hat Microsoft Microsoft Bot Framework und die erste Iteration der Bot Framework Connector-API zusammen mit dem Bot Builder und Bot Framework Connector SDK angekündigt. Seitdem haben wir Ihr Feedback gesammelt und arbeiten aktiv an der Verbesserung der REST-API und den SDKs.

Im Juli 2016 wurde die Bot Framework-API v3 veröffentlicht, während die Bot Framework-API v1 als veraltet markiert wurde. Bots, die auf die API v1 zurückgriffen, funktionierten im Dezember 2016 in Skype und am 23. Februar 2017 in allen verbleibenden Kanälen nicht mehr. Wenn Sie einen Bot mit der API v1 erstellt haben und diesen wieder funktionsfähig machen wollen, müssen Sie mithilfe der Anweisungen in diesem Artikel ein Upgrade auf die API v3 durchführen. Um sicherzustellen, dass Sie den Upgradeprozess zweifelsohne verstanden haben, gehen Sie diesen Artikel vollständig durch, bevor Sie die ersten Schritte tätigen. 

## <a name="step-1-get-your-app-id-and-password-from-the-bot-framework-portal"></a>Schritt 1: Abrufen Ihrer App-ID und Ihres Kennwort über das Bot Framework-Portal

Melden Sie sich beim [Bot Framework-Portal](https://dev.botframework.com/) an, klicken Sie auf **Meine Bots**, und wählen Sie dann Ihren Bot aus, um das zugehörige Dashboard zu öffnen. Klicken Sie anschließend auf den Link **EINSTELLUNGEN**, der sich ungefähr in der oberen rechten Ecke der Seite befindet. 

Überprüfen Sie im Abschnitt **Konfiguration** der Seite mit den Einstellungen den Inhalt des Felds **App-ID**, und fahren Sie mit den nächsten Schritten fort. Diese hängen davon ab, ob das Feld **App-ID** bereits aufgefüllt ist.

### <a name="case-1-app-id-field-is-already-populated"></a>Fall 1: Das Feld „App-ID“ ist bereits ausgefüllt.

Wenn das Feld **App-ID** bereits aufgefüllt ist, führen Sie die folgenden Schritte durch:

1. Klicken Sie auf **Microsoft-App-ID und -Kennwort verwalten**.  
![Konfiguration](~/media/upgrade/manage-app-id.png)

2. Klicken Sie auf **Neues Kennwort generieren**.  
![Neues Kennwort generieren](~/media/upgrade/generate-new-password.png)

3. Kopieren und speichern Sie das neue Kennwort zusammen mit der MSA-App-ID. Sie benötigen diese Werte später noch.  
![Neues Kennwort](~/media/upgrade/new-password-generated.png)

### <a name="case-2-app-id-field-is-empty"></a>Fall 2: Das Feld „App-ID“ ist leer.

Wenn das Feld **App-ID** leer ist, führen Sie die folgenden Schritte durch:

1. Klicken Sie auf **Microsoft-App-ID und -Kennwort erstellen**.  
   ![App-ID und Kennwort erstellen](~/media/upgrade/generate-appid-and-password.png)
   > [!IMPORTANT]
   > Aktivieren Sie noch nicht das Optionsfeld **Version 3.0**. Dieser Vorgang erfolgt später, nachdem Sie [Ihren Bot-Code aktualisiert](#update-code) haben.</div>

2. Klicken Sie auf **Zum Fortfahren Kennwort generieren**.  
   ![App-Kennwort generieren](~/media/upgrade/generate-a-password-to-continue.png)

3. Kopieren und speichern Sie das neue Kennwort zusammen mit der MSA-App-ID. Sie benötigen diese Werte später noch.  
   ![Neues Kennwort](~/media/upgrade/new-password-generated.png)

4. Klicken Sie auf **Abschließen und zu Bot Framework zurückkehren**.  
   ![Abschließen und zum Portal zurückkehren](~/media/upgrade/finish-and-go-back-to-bot-framework.png)

5. Scrollen Sie wieder auf der Seite mit den Bot-Einstellungen im Bot Framework-Portal auf der Seite nach unten, und klicken Sie auf **Änderungen speichern**.  
   ![Änderungen speichern](~/media/upgrade/save-changes.png)

## <a id="update-code"></a> Schritt 2: Aktualisieren des Bot-Codes auf Version 3.0

Um Ihren Bot-Code auf Version 3.0 zu aktualisieren, führen Sie folgende Schritte durch:

1. Aktualisieren Sie auf die neueste Version des [Bot Builder SDK](https://github.com/Microsoft/BotBuilder) für Ihre Bot-Sprache.
2. Aktualisieren Sie gemäß den nachfolgenden Anweisungen Ihren Code, um die erforderlichen Änderungen anzuwenden.
3. Verwenden Sie den [Bot Framework Channel Emulator](~/bot-service-debug-emulator.md), um Ihren Bot lokal und dann in der Cloud zu testen.

In den folgenden Abschnitten werden die wichtigsten Unterschiede zwischen API v1 und API v3 beschrieben. Nachdem Sie Ihren Code in API v3 aktualisiert haben, können Sie den Upgradevorgang abschließen, indem Sie im Bot Framework-Portal [Ihre Bot-Einstellungen aktualisieren](#step-3).

### <a name="botbuilder-and-connector-are-now-one-sdk"></a>Bot Builder und Connector sind nun ein SDK

Anstatt mithilfe mehrerer NuGet-Pakete (oder npm-Module) separate SDKs für Bot Builder und Connector installieren zu müssen, können Sie jetzt beide Bibliotheken in einem einzigen Bot Builder SDK abrufen:

- Bot Builder SDK für .NET: NuGet-Paket `Microsoft.Bot.Builder`
- Bot Builder SDK für Node.js: npm-Modul `botbuilder`

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

## <a id="step-3"></a> Schritt 3: Aktualisieren der Bot-Einstellungen im Bot Framework-Portal

Nachdem Sie ein Upgrade für Ihren Bot-Code auf die API v3 durchgeführt und diese in der Cloud bereitgestellt haben, schließen Sie den Upgradeprozess anhand der folgenden Schritte ab: 

1. Melden Sie sich beim [Bot Framework-Portal](https://dev.botframework.com/) an.

2. Klicken Sie auf **Meine Bots**, und wählen Sie Ihren Bot aus, um das zugehörige Dashboard zu öffnen. 

3. Klicken Sie auf den Link **EINSTELLUNGEN**, der sich ungefähr in der oberen rechten Ecke der Seite befindet. 

4. Fügen Sie unter **Version 3.0** im Abschnitt **Konfiguration** den Endpunkt Ihres Bots in das Feld **Messagingendpunkt** ein.  
![Konfiguration in Version 3](~/media/upgrade/paste-new-v3-enpoint-url.png)

5. Aktivieren Sie das Optionsfeld **Version 3.0**.  
![Aktivieren von Version 3.0](~/media/upgrade/switch-to-v3-endpoint.png)

6. Scrollen Sie auf der Seite nach unten, und klicken Sie auf **Änderungen speichern**.  
![Änderungen speichern](~/media/upgrade/save-changes.png)