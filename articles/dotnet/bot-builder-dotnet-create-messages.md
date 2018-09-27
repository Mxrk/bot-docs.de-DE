---
title: Erstellen von Nachrichten mit dem Bot Builder SDK für .NET | Microsoft-Dokumentation
description: Erfahren Sie mehr zu häufig verwendeten Nachrichteneigenschaften innerhalb des Bot Builder SDK für .NET.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: c35e651f674d65728ac93a815cc7116515790f53
ms.sourcegitcommit: 3bf3dbb1a440b3d83e58499c6a2ac116fe04b2f6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/23/2018
ms.locfileid: "46707876"
---
# <a name="create-messages"></a>Erstellen von Nachrichten

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

Ihr Bot sendet [Aktivitäten](bot-builder-dotnet-activities.md) für **Nachrichten**, um dem Benutzer Informationen mitzuteilen, und auf die gleiche Weise empfängt er auch **Nachrichtenaktivitäten** vom Benutzer. Einige Nachrichten können aus Nur-Text bestehen, während andere vielfältigere Inhalte wie [gesprochenen Text](bot-builder-dotnet-text-to-speech.md), [vorgeschlagene Aktionen](bot-builder-dotnet-add-suggested-actions.md), [Medienanlagen](bot-builder-dotnet-add-media-attachments.md), [Rich Cards](bot-builder-dotnet-add-rich-card-attachments.md) und [kanalspezifische Daten](bot-builder-dotnet-channeldata.md) enthalten können. 

In diesem Artikel werden einige der häufig verwendeten Nachrichteneigenschaften beschrieben.

## <a name="customizing-a-message"></a>Anpassen einer Nachricht

Um mehr Kontrolle über die Textformatierung Ihrer Nachrichten zu haben, können Sie mit dem [Aktivität](https://docs.botframework.com/en-us/csharp/builder/sdkreference/dc/d2f/class_microsoft_1_1_bot_1_1_connector_1_1_activity.html)-Objekt eine benutzerdefinierte Nachricht erstellen und die erforderlichen Eigenschaften vor dem Senden an den Benutzer festlegen.

Dieses Beispiel zeigt, wie Sie ein benutzerdefiniertes `message`-Objekt erstellen und die `Text`-, `TextFormat`- und `Local`-Eigenschaft festlegen.

[!code-csharp[Set message properties](../includes/code/dotnet-create-messages.cs#setBasicProperties)]

Mithilfe der `TextFormat`-Eigenschaft einer Nachricht kann das Format des Texts angegeben werden. Die `TextFormat`-Eigenschaft kann auf **plain**, **markdown** oder **xml** festgelegt werden. Der Standardwert für `TextFormat` ist **markdown**. 

## <a name="attachments"></a>Attachments

Mit der `Attachments`-Eigenschaft der Nachrichtenaktivität können einfache Medienanlagen (Bild, Audio, Video, Datei) und Rich Cards gesendet und empfangen werden. Weitere Informationen finden Sie unter [Hinzufügen von Medienanlagen zu Nachrichten](bot-builder-dotnet-add-media-attachments.md) und [Hinzufügen von Rich Cards zu Nachrichten](bot-builder-dotnet-add-rich-card-attachments.md).

## <a name="entities"></a>Entitäten

Die `Entities`-Eigenschaft einer Nachricht ist ein Array von <a href="http://schema.org/" target="_blank">schema.org</a>-Objekten mit unbestimmtem Ende, was den Austausch von allgemeinen kontextbezogenen Metadaten zwischen dem Kanal und dem Bot ermöglicht.

### <a name="mention-entities"></a>Erwähnungsentitäten

Viele Kanäle unterstützen die Option, dass ein Bot oder Benutzer eine Person im Kontext einer Konversation „erwähnt“. Um einen Benutzer in einer Nachricht zu erwähnen, füllen Sie die `Entities`-Eigenschaft der Nachricht mit einem `Mention`-Objekt. Das `Mention`-Objekt enthält die folgenden Eigenschaften: 

| Eigenschaft | BESCHREIBUNG | 
|----|----|
| Typ | Typ der Entität („Mention“) | 
| Mentioned | `ChannelAccount`-Objekt, das angibt, welcher Benutzer erwähnt wurde | 
| Text | Text in der `Activity.Text`-Eigenschaft, der die Erwähnung selbst darstellt (kann leer oder NULL sein) |

In diesem Codebeispiel wird veranschaulicht, wie eine `Mention`-Entität der `Entities`-Sammlung hinzugefügt wird.

[!code-csharp[set Mention](../includes/code/dotnet-create-messages.cs#setMention)]

> [!TIP]
> Beim Versuch, die Absicht des Benutzers zu ermitteln, sollte der Bot möglicherweise den Abschnitt der Nachricht ignorieren, in dem er erwähnt wird. Rufen Sie die `GetMentions`-Methode auf, und werten Sie die `Mention`-Objekte in der zurückgegeben Antwort aus.

### <a name="place-objects"></a>Place-Objekte

<a href="https://schema.org/Place" target="_blank">Standortbezogene Informationen</a> können in einer Nachricht übermittelt werden, indem Sie die `Entities`-Eigenschaft der Nachricht entweder mit einem `Place`-Objekt oder einem `GeoCoordinates`-Objekt füllen. 

Das `Place`-Objekt enthält die folgenden Eigenschaften:

| Eigenschaft | BESCHREIBUNG | 
|----|----|
| Typ | Typ der Entität („Place“) |
| Adresse | Beschreibung oder `PostalAddress`-Objekt (in der Zukunft) | 
| geografischer Raum | GeoCoordinates | 
| HasMap | URL zu einer Karte oder einem `Map`-Objekt (in der Zukunft) |
| NAME | Der Name des Orts |

Das `GeoCoordinates`-Objekt enthält die folgenden Eigenschaften:

| Eigenschaft | BESCHREIBUNG | 
|----|----|
| Typ | Typ der Entität („GeoCoordinates“) |
| NAME | Der Name des Orts |
| Längengrad | Längengrad des Standorts (<a href="https://en.wikipedia.org/wiki/World_Geodetic_System" target="_blank">WGS 84</a>) | 
| Breitengrad | Breitengrad des Standorts (<a href="https://en.wikipedia.org/wiki/World_Geodetic_System" target="_blank">WGS 84</a>) | 
| Elevation | Die Höhe des Standorts (<a href="https://en.wikipedia.org/wiki/World_Geodetic_System" target="_blank">WGS 84</a>) | 

In diesem Codebeispiel wird veranschaulicht, wie eine `Place`-Entität der `Entities`-Sammlung hinzugefügt wird:

[!code-csharp[set GeoCoordinates](../includes/code/dotnet-create-messages.cs#setGeoCoord)]

### <a name="consume-entities"></a>Nutzen von Entitäten

Verwenden Sie entweder das `dynamic`-Schlüsselwort oder stark typisierte Klassen, um Entitäten zu nutzen.

In diesem Codebeispiel wird veranschaulicht, wie Sie das `dynamic`-Schlüsselwort zum Verarbeiten einer Entität in der `Entities`-Eigenschaft einer Nachricht verwenden:

[!code-csharp[examine entity using dynamic keyword](../includes/code/dotnet-create-messages.cs#examineEntity1)]

In diesem Codebeispiel wird veranschaulicht, wie Sie eine stark typisierte Klasse zum Verarbeiten einer Entität in der `Entities`-Eigenschaft einer Nachricht verwenden:

[!code-csharp[examine entity using typed class](../includes/code/dotnet-create-messages.cs#examineEntity2)]

## <a name="channel-data"></a>Kanaldaten

Mit der `ChannelData`-Eigenschaft einer Nachrichtenaktivität kann kanalspezifische Funktionalität implementiert werden. Weitere Informationen finden Sie unter [Implementieren von kanalspezifischer Funktionalität](bot-builder-dotnet-channeldata.md).

## <a name="text-to-speech"></a>Text-to-Speech

Die `Speak`-Eigenschaft einer Nachrichtenaktivität kann verwendet werden, um den Text anzugeben, der von Ihrem Bot auf einem sprachaktivierten Kanal gesprochen werden soll. Die `InputHint`-Eigenschaft einer Nachrichtenaktivität kann verwendet werden, um den Zustand des Mikrofons und des Eingabefeldes des Clients zu steuern (falls vorhanden). Weitere Informationen finden Sie unter [Hinzufügen von Spracheingabe zu Nachrichten](bot-builder-dotnet-text-to-speech.md).

## <a name="suggested-actions"></a>Vorgeschlagene Aktionen

Mit der `SuggestedActions`-Eigenschaft einer Nachrichtenaktivität können Schaltflächen bereitgestellt werden, auf die der Benutzer tippen kann, um Eingaben vorzunehmen. Im Gegensatz zu Schaltflächen in Rich Cards (die für den Benutzer auch nach dem Antippen sichtbar und zugänglich bleiben), verschwinden die im Bereich der vorgeschlagenen Aktionen angezeigten Schaltflächen, nachdem der Benutzer eine Auswahl getroffen hat. Weitere Informationen finden Sie unter [Hinzufügen von vorgeschlagenen Aktionen zu Nachrichten](bot-builder-dotnet-add-suggested-actions.md).

## <a name="next-steps"></a>Nächste Schritte

Ein Bot und ein Benutzer können einander Nachrichten senden. Wenn die Nachricht komplexer ist, kann Ihr Bot in einer Nachricht eine Rich Card an den Benutzer senden. Rich Cards decken zahlreiche Präsentations- und Interaktionsszenarien ab, die häufig in den meisten Bots benötigt werden.

> [!div class="nextstepaction"]
> [Senden einer Rich Card in einer Nachricht](bot-builder-dotnet-add-rich-card-attachments.md)

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Übersicht über Aktivitäten](bot-builder-dotnet-activities.md)
- [Senden und Empfangen von Aktivitäten](bot-builder-dotnet-connector.md)
- [Hinzufügen von Medienanlagen zu Nachrichten](bot-builder-dotnet-add-media-attachments.md)
- [Hinzufügen von Rich Cards zu Nachrichten](bot-builder-dotnet-add-rich-card-attachments.md)
- [Hinzufügen von Sprache zu Nachrichten](bot-builder-dotnet-text-to-speech.md)
- [Hinzufügen von vorgeschlagenen Aktionen zu Nachrichten](bot-builder-dotnet-add-suggested-actions.md)
- [Implementieren von kanalspezifischer Funktionalität](bot-builder-dotnet-channeldata.md)
- <a href="https://docs.botframework.com/en-us/csharp/builder/sdkreference/dc/d2f/class_microsoft_1_1_bot_1_1_connector_1_1_activity.html" target="_blank">Aktivitätsklasse</a>
- <a href="/dotnet/api/microsoft.bot.connector.imessageactivity" target="_blank">IMessageActivity-Schnittstelle</a>

