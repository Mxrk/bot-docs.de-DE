---
title: Übersicht über Aktivitäten | Microsoft-Dokumentation
description: Hier erhalten Sie Informationen zu den verschiedenen Aktivitätstypen, die im Bot Connector-Dienst verfügbar sind.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
ms.openlocfilehash: cf8da2240df7edbb6ea8c858829e71089b7e72cb
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39304152"
---
# <a name="activities-overview"></a>Übersicht über Aktivitäten

Der Bot Connector-Dienst tauscht durch Übergeben eines [Activity][Activity]-Objekts Informationen zwischen Bot und Kanal (Benutzer) aus. Der am häufigsten verwendete Aktivitätstyp ist **message**, aber es gibt andere Aktivitätstypen, die für die Kommunikation verschiedener Arten von Informationen an einen Bot oder Kanal verwendet werden können. 

## <a name="activity-types-in-the-bot-connector-service"></a>Aktivitätstypen im Bot Connector-Dienst

Die folgenden Aktivitätstypen werden vom Bot Connector-Dienst unterstützt.

| Aktivitätstyp | BESCHREIBUNG |
|------|------|------|
| Message: | Stellt eine Kommunikation zwischen einem Bot und einem Benutzer dar. |
| conversationUpdate | Gibt an, dass der Bot einer Konversation hinzugefügt wurde, andere Mitglieder hinzugefügt bzw. aus der Konversation entfernt wurden oder Konversationsmetadaten geändert wurden. |
| contactRelationUpdate | Gibt an, dass der Bot der Kontaktliste eines Benutzers hinzugefügt oder daraus entfernt wurde. |
| typing | Gibt an, dass der Benutzer oder Bot am anderen Ende einer Nachricht eine Antwort erstellt. | 
| ping | Stellt einen Versuch zur Ermittlung dar, ob auf den Endpunkt eines Bots zugegriffen werden kann. | 
| deleteUserData | Gibt einem Bot an, dass ein Benutzer angefordert hat, dass der Bot gespeicherte Benutzerdaten löschen soll. |
| endOfConversation | Gibt das Ende einer Konversation an. |

## <a name="message"></a>Message:

Ihr Bot sendet **message**-Aktivitäten (Nachrichtenaktivitäten), um Informationen an Benutzer zu übermitteln und **message**-Aktivitäten von Benutzern zu erhalten. Einige Nachrichten enthalten möglicherweise reinen Text, während andere möglicherweise reichhaltigere Inhalte wie z. B. [Medienanlagen](bot-framework-rest-connector-add-media-attachments.md), [Schaltflächen und Karten](bot-framework-rest-connector-add-rich-cards.md) oder [kanalspezifische Daten](bot-framework-rest-connector-channeldata.md) enthalten. Informationen zu häufig verwendeten Nachrichteneigenschaften finden Sie unter [Erstellen von Nachrichten](bot-framework-rest-connector-create-messages.md). Weitere Informationen zum Senden und Empfangen von Nachrichten finden Sie unter [Senden und Empfangen von Nachrichten](bot-framework-rest-connector-send-and-receive-messages.md). 

## <a name="conversationupdate"></a>conversationUpdate

Ein Bot empfängt eine **conversationUpdate**-Aktivität, wenn er einer Konversation hinzugefügt wurde, andere Mitglieder hinzugefügt bzw. aus einer Konversation entfernt wurden oder Konversationsmetadaten geändert wurden. 

Wenn der Konversation Mitglieder hinzugefügt wurden, identifiziert die `addedMembers`-Eigenschaft der Aktivität die neuen Mitglieder. Wenn der Konversation Mitglieder hinzugefügt wurden, identifiziert die `removedMembers`-Eigenschaft der Aktivität die neuen Mitglieder. 

> [!TIP]
> Wenn Ihr Bot eine **conversationUpdate**-Aktivität empfängt, die angibt, dass ein Benutzer der Konversation beigetreten ist, können Sie auswählen, mit einer Begrüßungsnachricht an diesen Benutzer zu antworten. 

## <a name="contactrelationupdate"></a>contactRelationUpdate

Ein Bot empfängt eine **contactRelationUpdate**-Aktivität, wenn er der Kontaktliste eines Benutzers hinzugefügt oder daraus entfernt wurde. Der Wert der `action`-Eigenschaft („add“ | „remove“) der Aktivität gibt an, ob der Bot der Kontaktliste des Benutzers hinzugefügt oder daraus entfernt wurde.

## <a name="typing"></a>typing

Ein Bot empfängt eine **typing**-Aktivität, um anzugeben, dass der Benutzer eine Antwort eingibt. Ein Bot kann eine **typing**-Aktivität senden, um dem Benutzer mitzuteilen, dass er an einer Anforderung arbeitet oder eine Antwort erstellt. 

## <a name="ping"></a>ping

Ein Bot empfängt eine **ping**-Aktivität, um zu ermitteln, ob auf den zugehörigen Endpunkt zugegriffen werden kann. Der Bot sollte mit dem HTTP-Statuscode 200 (OK), 403 (Unzulässig) oder 401 (Nicht autorisiert) antworten.

## <a name="deleteuserdata"></a>deleteUserData

Ein Bot empfängt eine **deleteUserData**-Aktivität, wenn ein Benutzer die Löschung von Daten anfordert, die der Bot zuvor für diesen Benutzer gespeichert hat. Wenn Ihr Bot diesen Aktivitätstyp empfängt, sollte er alle personenbezogenen Informationen (Personally Identifiable Information, PII) löschen, die er zuvor für den Benutzer gespeichert hat, der die Anforderung gesendet hat.

## <a name="endofconversation"></a>endOfConversation 

Ein Bot empfängt eine **endOfConversation**-Aktivität, um anzugeben, dass der Benutzer die Konversation beendet hat. Ein Bot kann eine **endOfConversation**-Aktivität senden, um dem Benutzer mitzuteilen, dass die Konversation beendet wird. 

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Erstellen von Nachrichten](bot-framework-rest-connector-create-messages.md)
- [Senden und Empfangen von Nachrichten](bot-framework-rest-connector-send-and-receive-messages.md)

[Activity]: bot-framework-rest-connector-api-reference.md#activity-object