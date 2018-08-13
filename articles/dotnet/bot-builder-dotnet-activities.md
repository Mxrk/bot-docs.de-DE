---
title: Übersicht über Aktivitäten | Microsoft-Dokumentation
description: Hier erhalten Sie Informationen zu den verschiedenen Aktivitätstypen, die im Bot Builder SDK für .NET verfügbar sind.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: f7fe3181a4c361b47a7ef6fbdf815b4c495c6f76
ms.sourcegitcommit: 67445b42796d90661afc643c6bb6533e9a662cbc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/06/2018
ms.locfileid: "39574636"
---
# <a name="activities-overview"></a>Übersicht über Aktivitäten

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

[!INCLUDE [Activity concept overview](../includes/snippet-dotnet-concept-activity.md)]

## <a name="activity-types-in-the-bot-builder-sdk-for-net"></a>Aktivitätstypen im Bot Builder SDK für .NET

Die folgenden Aktivitätstypen werden vom Bot Builder SDK für .NET unterstützt.

| Activity.Type | Schnittstelle | BESCHREIBUNG |
|------|------|------|
| [message](#message) | IMessageActivity | Stellt eine Kommunikation zwischen einem Bot und einem Benutzer dar. |
| [conversationUpdate](#conversationupdate) | IConversationUpdateActivity | Gibt an, dass der Bot einer Konversation hinzugefügt wurde, andere Mitglieder hinzugefügt bzw. aus der Konversation entfernt wurden oder Konversationsmetadaten geändert wurden. |
| [contactRelationUpdate](#contactrelationupdate) | IContactRelationUpdateActivity | Gibt an, dass der Bot der Kontaktliste eines Benutzers hinzugefügt oder daraus entfernt wurde. |
| [typing](#typing) | ITypingActivity | Gibt an, dass der Benutzer oder Bot am anderen Ende einer Nachricht eine Antwort erstellt. | 
| [ping](#ping) | – | Stellt einen Versuch zur Ermittlung dar, ob auf den Endpunkt eines Bots zugegriffen werden kann. | 
| [deleteUserData](#deleteuserdata) | – | Gibt einem Bot an, dass ein Benutzer angefordert hat, dass der Bot gespeicherte Benutzerdaten löschen soll. |
| [endOfConversation](#endofconversation) | IEndOfConversationActivity | Gibt das Ende einer Konversation an. |
| [event](#event) | IEventActivity | Stellt eine an einen Bot gesendete Kommunikation dar, die für den Benutzer nicht sichtbar ist. |
| [invoke](#invoke) | IInvokeActivity | Stellt eine an einen Bot gesendete Kommunikation für die Anforderung zur Ausführung eines bestimmten Vorgangs dar. Dieser Aktivitätstyp ist für die interne Verwendung durch Microsoft Bot Framework reserviert. |
| [messageReaction](#messagereaction) | IMessageReactionActivity | Gibt an, dass ein Benutzer auf eine vorhandene Aktivität reagiert hat. Beispiel: Ein Benutzer hat auf die Schaltfläche „Gefällt mir“ einer Nachricht geklickt. |

## <a name="message"></a>Message:

Ihr Bot sendet **message**-Aktivitäten (Nachrichtenaktivitäten), um Informationen an Benutzer zu übermitteln und **message**-Aktivitäten von Benutzern zu erhalten. Einige Nachrichten können aus Nur-Text bestehen, während andere vielfältigere Inhalte wie [gesprochenen Text](bot-builder-dotnet-text-to-speech.md), [vorgeschlagene Aktionen](bot-builder-dotnet-add-suggested-actions.md), [Medienanlagen](bot-builder-dotnet-add-media-attachments.md), [Multimedia-Karten](bot-builder-dotnet-add-rich-card-attachments.md) und [kanalspezifische Daten](bot-builder-dotnet-channeldata.md) enthalten können. Informationen zu häufig verwendeten Nachrichteneigenschaften finden Sie unter [Erstellen von Nachrichten](bot-builder-dotnet-create-messages.md).

## <a name="conversationupdate"></a>conversationUpdate

Ein Bot empfängt eine **conversationUpdate**-Aktivität, wenn er einer Konversation hinzugefügt wurde, andere Mitglieder hinzugefügt bzw. aus einer Konversation entfernt wurden oder Konversationsmetadaten geändert wurden. 

Wenn der Konversation Mitglieder hinzugefügt wurden, enthält die `MembersAdded`-Eigenschaft der Aktivität ein Array von `ChannelAccount`-Objekten zum Identifizieren der neuen Mitglieder. 

Werten Sie aus, ob der Wert `Recipient.Id` für die Aktivität (also die ID Ihres Bots) mit der `Id`-Eigenschaft eines der Konten im Array `MembersAdded` übereinstimmt, um zu ermitteln, ob Ihr Bot der Konversation hinzugefügt wurde (also eines der neuen Mitglieder ist).

Wenn Mitglieder aus der Konversation entfernt wurden, enthält die `MembersRemoved`-Eigenschaft ein Array von `ChannelAccount`-Objekten zum Identifizieren der entfernten Mitglieder. 

> [!TIP]
> Wenn Ihr Bot eine **conversationUpdate**-Aktivität empfängt, die angibt, dass ein Benutzer der Konversation beigetreten ist, können Sie auswählen, ob eine Begrüßungsnachricht an diesen Benutzer gesendet werden soll. 

## <a name="contactrelationupdate"></a>contactRelationUpdate

Ein Bot empfängt eine **contactRelationUpdate**-Aktivität, wenn er der Kontaktliste eines Benutzers hinzugefügt oder daraus entfernt wurde. Der Wert der `Action`-Eigenschaft („add“ | „remove“) der Aktivität gibt an, ob der Bot der Kontaktliste des Benutzers hinzugefügt oder daraus entfernt wurde.

## <a name="typing"></a>typing

Ein Bot empfängt eine **typing**-Aktivität, um anzugeben, dass der Benutzer eine Antwort eingibt. Ein Bot kann eine **typing**-Aktivität senden, um dem Benutzer mitzuteilen, dass er an einer Anforderung arbeitet oder eine Antwort erstellt. 

## <a name="ping"></a>ping

Ein Bot empfängt eine **ping**-Aktivität, um zu ermitteln, ob auf den zugehörigen Endpunkt zugegriffen werden kann. Der Bot sollte mit dem HTTP-Statuscode 200 (OK), 403 (Unzulässig) oder 401 (Nicht autorisiert) antworten.

## <a name="deleteuserdata"></a>deleteUserData

Ein Bot empfängt eine **deleteUserData**-Aktivität, wenn ein Benutzer die Löschung von Daten anfordert, die der Bot zuvor für diesen Benutzer gespeichert hat. Wenn Ihr Bot diesen Aktivitätstyp empfängt, sollte er alle personenbezogenen Informationen (Personally Identifiable Information, PII) löschen, die er zuvor für den Benutzer gespeichert hat, der die Anforderung gesendet hat.

## <a name="endofconversation"></a>endOfConversation 

Ein Bot empfängt eine **endOfConversation**-Aktivität, um anzugeben, dass der Benutzer die Konversation beendet hat. Ein Bot kann eine **endOfConversation**-Aktivität senden, um dem Benutzer mitzuteilen, dass die Konversation beendet wird. 

## <a name="event"></a>event

Ihr Bot kann eine **event**-Aktivität von einem externen Prozess oder Dienst empfangen, der Ihrem Bot Informationen mitteilen möchte, ohne dass diese Informationen für Benutzer sichtbar sind. Der Absender einer **event**-Aktivität erwartet in der Regel vom Bot keine Bestätigung des Empfangs.

## <a name="invoke"></a>invoke

Ihr Bot kann eine **invoke**-Aktivität empfangen, die eine Anforderung zum Ausführen eines bestimmten Vorgangs darstellt. Der Absender einer **invoke**-Aktivität erwartet in der Regel vom Bot eine Bestätigung des Empfangs über eine HTTP-Antwort. Dieser Aktivitätstyp ist für die interne Verwendung durch Microsoft Bot Framework reserviert.

## <a name="messagereaction"></a>messageReaction

Einige Kanäle senden **messageReaction**-Aktivitäten an Ihren Bot, wenn ein Benutzer auf eine vorhandene Aktivität reagiert hat. Beispiel: Ein Benutzer hat auf die Schaltfläche „Gefällt mir“ einer Nachricht geklickt. Die **ReplyToId**-Eigenschaft gibt an, auf welche Aktivität der Benutzer reagiert hat.

Die **messageReaction**-Aktivität kann einer beliebigen Anzahl von **messageReactionTypes** entsprechen, die der Kanal definiert hat. „Gefällt mir“ oder „PlusOne“ sind Beispiele für Reaktionstypen, die ein Kanal senden kann. 

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Senden und Empfangen von Aktivitäten](bot-builder-dotnet-connector.md)
- [Erstellen von Nachrichten](bot-builder-dotnet-create-messages.md)
- <a href="https://docs.botframework.com/en-us/csharp/builder/sdkreference/dc/d2f/class_microsoft_1_1_bot_1_1_connector_1_1_activity.html" target="_blank">Activity-Klasse</a>
