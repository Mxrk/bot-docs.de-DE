---
title: Erstellen von Nachrichten mit der Bot Connector-API | Microsoft-Dokumentation
description: Informationen zu häufig verwendeten Nachrichteneigenschaften innerhalb der Bot Connector-API.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
ms.openlocfilehash: 62088ecdf2f8402ec5456eea758f5db994de0cf9
ms.sourcegitcommit: 3bf3dbb1a440b3d83e58499c6a2ac116fe04b2f6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/23/2018
ms.locfileid: "46707316"
---
# <a name="create-messages"></a>Erstellen von Nachrichten

Ihr Bot sendet [Activity][Activity]-Objekte des Typs **message**, um Benutzern Informationen zukommen zu lassen und auch **message**-Aktivitäten von Benutzern zu empfangen. Einige Nachrichten können aus reinem Text bestehen, während andere vielfältigere Inhalte wie [gesprochenen Text](bot-framework-rest-connector-text-to-speech.md), [vorgeschlagene Aktionen](bot-framework-rest-connector-add-suggested-actions.md), [Medienanlagen](bot-framework-rest-connector-add-media-attachments.md), [Rich Cards](bot-framework-rest-connector-add-rich-cards.md) und [kanalspezifische Daten](bot-framework-rest-connector-channeldata.md) enthalten können. In diesem Artikel werden einige der häufig verwendeten Nachrichteneigenschaften beschrieben.

## <a name="message-text-and-formatting"></a>Nachrichtentext und -formatierung

Der Nachrichtentext kann als **reiner Text**, **Markdown** oder im **xml-Format** formatiert werden. Das Standardformat für die `textFormat`-Eigenschaft ist **Markdown**, und Text wird unter Verwendung der Markdown-Formatierungsstandards interpretiert. Der Unterstützungsgrad für Textformate variiert je nach Kanal. Um zu sehen, ob ein Feature, das Sie verwenden möchten, auf dem von Ihnen ausgewählten Kanal unterstützt wird, sehen Sie sich das Feature mit der [Kanalprüfung][ChannelInspector] an. 

Mit der `textFormat`-Eigenschaft des [Activity][Activity]-Objekts kann das Format des Texts angegeben werden. Legen Sie z. B. zum Erstellen einer einfachen Nachricht, die nur reinen Text enthält, die `textFormat`-Eigenschaft des [Activity][Activity]-Objekts auf **plain**, den Inhalt der `text`-Eigenschaft auf den Inhalt der Nachricht und die `locale`-Eigenschaft auf das Gebietsschema des Absenders fest. 

## <a name="attachments"></a>Attachments

Mit der `attachments`-Eigenschaft des [Activity][Activity]-Objekts können einfache Medienanlagen (Bild, Audio, Video, Datei) und Rich Cards gesendet werden. Weitere Informationen finden Sie unter [Hinzufügen von Medienanlagen zu Nachrichten](bot-framework-rest-connector-add-media-attachments.md) und [Hinzufügen von Rich Cards zu Nachrichten](bot-framework-rest-connector-add-rich-cards.md).

## <a name="entities"></a>Entitäten

Die `entities`-Eigenschaft des [Activity][Activity]-Objekts ist ein Array von <a href="http://schema.org/" target="_blank">schema.org</a>-Objekten mit unbestimmtem Ende, die den Austausch von allgemeinen kontextbezogenen Metadaten zwischen dem Kanal und dem Bot ermöglichen.

### <a name="mention-entities"></a>Erwähnungsentitäten

Viele Kanäle unterstützen die Option, dass ein Bot oder Benutzer eine Person im Kontext einer Konversation „erwähnt“. Um einen Benutzer in einer Nachricht zu erwähnen, füllen Sie die `entities`-Eigenschaft der Nachricht mit einem [Mention][Mention]-Objekt. 

### <a name="place-entities"></a>Standortentitäten

Um <a href="https://schema.org/Place" target="_blank">Standortbezogene Informationen</a> in einer Nachricht zu übermitteln, füllen Sie die `entities`-Eigenschaft der Nachricht mit dem [Place][Place]-Objekt. 

## <a name="channel-data"></a>Kanaldaten

Mit der `channelData`-Eigenschaft des [Activity][Activity]-Objekts kann kanalspezifische Funktionalität implementiert werden. Weitere Informationen finden Sie unter [Implementieren von kanalspezifischer Funktionalität](bot-framework-rest-connector-channeldata.md).

## <a name="text-to-speech"></a>Text-to-Speech

Mit der `speak`-Eigenschaft des [Activity][Activity]-Objekts kann der Text angegeben werden, der von Ihrem Bot in einem sprachaktivierten Kanal gesprochen werden soll, und mit der `inputHint`-Eigenschaft des [Activity][Activity]-Objekts kann der Status des Clientmikrofons beeinflusst werden. Weitere Informationen finden Sie unter [Hinzufügen von Spracheingabe zu Nachrichten](bot-framework-rest-connector-text-to-speech.md) und [Hinzufügen von Eingabehinweisen zu Nachrichten](bot-framework-rest-connector-add-input-hints.md).

## <a name="suggested-actions"></a>Vorgeschlagene Aktionen

Mit der `suggestedActions`-Eigenschaft des [Activity][Activity]-Objekts können Schaltflächen bereitgestellt werden, auf die der Benutzer tippen kann, um Eingaben vorzunehmen. Im Gegensatz zu Schaltflächen in Rich Cards (die für den Benutzer auch nach dem Antippen sichtbar und zugänglich bleiben), verschwinden die im Bereich der vorgeschlagenen Aktionen angezeigten Schaltflächen, nachdem der Benutzer eine Auswahl getroffen hat. Weitere Informationen finden Sie unter [Hinzufügen von vorgeschlagenen Aktionen zu Nachrichten](bot-framework-rest-connector-add-suggested-actions.md).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Funktionsvorschau mit Channel Inspector][ChannelInspector]
- [Übersicht über Aktivitäten](bot-framework-rest-connector-activities.md)
- [Senden und Empfangen von Nachrichten](bot-framework-rest-connector-send-and-receive-messages.md)
- [Hinzufügen von Medienanlagen zu Nachrichten](bot-framework-rest-connector-add-media-attachments.md)
- [Hinzufügen von Rich Cards zu Nachrichten](bot-framework-rest-connector-add-rich-cards.md)
- [Hinzufügen von Spracheingabe zu Nachrichten](bot-framework-rest-connector-text-to-speech.md)
- [Hinzufügen von Eingabehinweisen zu Nachrichten](bot-framework-rest-connector-add-input-hints.md)
- [Hinzufügen von vorgeschlagenen Aktionen zu Nachrichten](bot-framework-rest-connector-add-suggested-actions.md)
- [Implementieren von kanalspezifischer Funktionalität](bot-framework-rest-connector-channeldata.md)

[Mention]: bot-framework-rest-connector-api-reference.md#mention-object
[Place]: bot-framework-rest-connector-api-reference.md#place-object
[Activity]: bot-framework-rest-connector-api-reference.md#activity-object
[ChannelInspector]: ../bot-service-channel-inspector.md
[textFormating]: ../bot-service-channel-inspector.md#text-formatting
