---
title: Entitäten und Aktivitätstypen | Microsoft-Dokumentation
description: Entitäten und Aktivitätstypen
keywords: Mention-Entitäten, Aktivitätstypen, Nutzen von Entitäten
author: ivorb
ms.author: v-ivorb
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 03/01/2018
ms.openlocfilehash: d329fcbe5b4a34cb3e9c1fbf0160c5248020a508
ms.sourcegitcommit: cb0b70d7cf1081b08eaf1fddb69f7db3b95b1b09
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2018
ms.locfileid: "51332964"
---
# <a name="entities-and-activity-types"></a>Entitäten und Aktivitätstypen

Entitäten sind Teil einer Aktivität und stellen zusätzliche Informationen über die Aktivität oder Konversation bereit.

[!include[Entity boilerplate](includes/snippet-entity-boilerplate.md)]

## <a name="entities"></a>Entitäten

Die Eigenschaft *entities* einer Nachricht ist ein Array von <a href="http://schema.org/" target="_blank">schema.org</a>-Objekten mit unbestimmtem Ende, was den Austausch von allgemeinen kontextbezogenen Metadaten zwischen dem Kanal und dem Bot ermöglicht.

### <a name="mention-entities"></a>Erwähnungsentitäten

Viele Kanäle unterstützen die Option, dass ein Bot oder Benutzer eine Person im Kontext einer Konversation „erwähnt“.
Füllen Sie die Entitätseigenschaft der Nachricht mit einem *Mention*-Objekt, um einen Benutzer in einer Nachricht zu erwähnen.
Das Mention-Objekt enthält die folgenden Eigenschaften:

| Eigenschaft | BESCHREIBUNG |
|----|----|
| Typ | Typ der Entität („Mention“) |
| Mentioned | ChannelAccount-Objekt, das angibt, welcher Benutzer erwähnt wurde | 
| Text | Text in der Eigenschaft *activity.text*, der die Erwähnung selbst darstellt (kann leer oder NULL sein) |

In diesem Codebeispiel wird veranschaulicht, wie eine Mention-Entität der Entitätensammlung hinzugefügt wird.

# <a name="ctabcs"></a>[C#](#tab/cs)
[!code-csharp[set Mention](includes/code/dotnet-create-messages.cs#setMention)]

> [!TIP]
> Beim Versuch, die Absicht des Benutzers zu ermitteln, sollte der Bot möglicherweise den Abschnitt der Nachricht ignorieren, in dem er erwähnt wird. Rufen Sie die `GetMentions`-Methode auf, und werten Sie die `Mention`-Objekte in der zurückgegeben Antwort aus.

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)
```javascript
var entity = context.activity.entities;

const mention = {
    type: "Mention",
    text: "@johndoe",
    mentioned: {
        name: "John Doe",
        id: "UV341235"
    }
}

entity = [mention];
```

---

### <a name="place-objects"></a>Place-Objekte

<a href="https://schema.org/Place" target="_blank">Standortbezogene Informationen</a> können in einer Nachricht übermittelt werden, indem Sie die Entitätseigenschaft der Nachricht entweder mit einem *Place*-Objekt oder einem *GeoCoordinates*-Objekt füllen.

Das Place-Objekt enthält die folgenden Eigenschaften:

| Eigenschaft | BESCHREIBUNG |
|----|----|
| Typ | Typ der Entität („Place“) |
| Adresse | Beschreibung oder PostalAddress-Objekt (in der Zukunft) |
| geografischer Raum | GeoCoordinates |
| HasMap | URL zu einer Karte oder einem Map-Objekt (in der Zukunft) |
| NAME | Der Name des Orts |

Das GeoCoordinates-Objekt enthält die folgenden Eigenschaften:

| Eigenschaft | BESCHREIBUNG |
|----|----|
| Typ | Typ der Entität („GeoCoordinates“) |
| NAME | Der Name des Orts |
| Längengrad | Längengrad des Standorts (<a href="https://en.wikipedia.org/wiki/World_Geodetic_System" target="_blank">WGS 84</a>) |
| Längengrad | Breitengrad des Standorts (<a href="https://en.wikipedia.org/wiki/World_Geodetic_System" target="_blank">WGS 84</a>) |
| Elevation | Die Höhe des Standorts (<a href="https://en.wikipedia.org/wiki/World_Geodetic_System" target="_blank">WGS 84</a>) |

In diesem Codebeispiel wird veranschaulicht, wie eine Place-Entität der Entitätensammlung hinzugefügt wird:

# <a name="ctabcs"></a>[C#](#tab/cs)
[!code-csharp[set GeoCoordinates](includes/code/dotnet-create-messages.cs#setGeoCoord)]

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)
```javascript
var entity = context.activity.entities;

const place = {
    elavation: 100,
    type: "GeoCoordinates",
    name : "myPlace",
    latitude: 123,
    longitude: 234
};

entity = [place];

```

---

### <a name="consume-entities"></a>Nutzen von Entitäten

# <a name="ctabcs"></a>[C#](#tab/cs)

Verwenden Sie entweder das `dynamic`-Schlüsselwort oder stark typisierte Klassen, um Entitäten zu nutzen.

In diesem Codebeispiel wird veranschaulicht, wie Sie das `dynamic`-Schlüsselwort zum Verarbeiten einer Entität in der `Entities`-Eigenschaft einer Nachricht verwenden:

[!code-csharp[examine entity using dynamic keyword](includes/code/dotnet-create-messages.cs#examineEntity1)]

In diesem Codebeispiel wird veranschaulicht, wie Sie eine stark typisierte Klasse zum Verarbeiten einer Entität in der `Entities`-Eigenschaft einer Nachricht verwenden:

[!code-csharp[examine entity using typed class](includes/code/dotnet-create-messages.cs#examineEntity2)]

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)

In diesem Codebeispiel wird gezeigt, wie Sie eine Entität in der `entity`-Eigenschaft einer Nachricht verarbeiten:

```javascript
if (entity[0].type === "GeoCoordinates" && entity[0].latitude > 34) {
    // do something
}
```

---

## <a name="activity-types"></a>Aktivitätstypen

In diesem Codebeispiel wird gezeigt, wie Sie eine Aktivität vom Typ **Message** verarbeiten:

# <a name="ctabcs"></a>[C#](#tab/cs)

```cs
if (context.Activity.Type == ActivityTypes.Message){
    // do something
}
```

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)

```js
if(context.activity.type === 'message'){
    // do something
}
```

---

Abgesehen vom gängigsten Typ **Message** können Aktivitäten viele verschiedene Typen aufweisen. Es gibt mehrere Aktivitätstypen:

| Activity.Type | Schnittstelle | BESCHREIBUNG |
|-----|-----|-----|
| [message](#message) | IMessageActivity (C#) <br> Activity (JS) | Stellt eine Kommunikation zwischen einem Bot und einem Benutzer dar. |
| [contactRelationUpdate](#contactrelationupdate) | IContactRelationUpdateActivity (C#) <br> Activity (JS) | Gibt an, dass der Bot der Kontaktliste eines Benutzers hinzugefügt oder aus ihr entfernt wurde. |
| [conversationUpdate](#conversationupdate) | IConversationUpdateActivity (C#) <br> Activity (JS) | Gibt an, dass der Bot einer Konversation hinzugefügt wurde, dass andere Mitglieder hinzugefügt bzw. aus der Konversation entfernt wurden oder dass Konversationsmetadaten geändert wurden. |
| [deleteUserData](#deleteuserdata) | – | Gibt einem Bot an, dass ein Benutzer angefordert hat, dass der Bot gespeicherte Benutzerdaten löschen soll. |
| [endOfConversation](#endofconversation) | IEndOfConversationActivity (C#) <br> Activity (JS) | Gibt das Ende einer Konversation an. |
| [event](#event) | IEventActivity (C#) <br> Activity (JS) | Stellt eine an einen Bot gesendete Kommunikation dar, die für den Benutzer nicht sichtbar ist. |
| [installationUpdate](#installationupdate) | IInstallationUpdateActivity (C#) <br> Activity (JS) | Stellt die Installation oder Deinstallation eines Bots in einer Organisationseinheit (z.B. einen Kundenmandanten oder einem „Team“) eines Kanals dar. |
| [invoke](#invoke) | IInvokeActivity (C#) <br> Activity (JS) | Stellt eine an einen Bot gesendete Kommunikation für die Anforderung der Ausführung eines bestimmten Vorgangs dar. Dieser Aktivitätstyp ist für die interne Verwendung durch Microsoft Bot Framework reserviert. |
| [messageReaction](#messagereaction) | IMessageReactionActivity (C#) <br> Activity (JS) | Gibt an, dass ein Benutzer auf eine vorhandene Aktivität reagiert hat. Wenn ein Benutzer beispielsweise auf die Schaltfläche „Gefällt mir“ einer Nachricht geklickt hat. |
| [typing](#typing) | ITypingActivity (C#) <br> Activity (JS) | Gibt an, dass der Benutzer oder Bot am anderen Ende einer Nachricht eine Antwort erstellt. |

## <a name="message"></a>Message:

<!-- Only the last link is different. -->

::: moniker range="azure-bot-service-3.0"

Ihr Bot sendet Nachrichtenaktivitäten, um Informationen an Benutzer zu übermitteln und Nachrichtenaktivitäten von Benutzern zu erhalten.
Einige Nachrichten können aus Nur-Text bestehen, während andere vielfältigere Inhalte wie gesprochenen Text, [vorgeschlagene Aktionen](v4sdk/bot-builder-howto-add-suggested-actions.md), [Medienanlagen](v4sdk/bot-builder-howto-add-media-attachments.md), [Rich Cards](v4sdk/bot-builder-howto-add-media-attachments.md#send-a-hero-card) und [kanalspezifische Daten](~/dotnet/bot-builder-dotnet-channeldata.md) enthalten können.

::: moniker-end

::: moniker range="azure-bot-service-4.0"

Ihr Bot sendet Nachrichtenaktivitäten, um Informationen an Benutzer zu übermitteln und Nachrichtenaktivitäten von Benutzern zu erhalten.
Einige Nachrichten können aus Nur-Text bestehen, während andere vielfältigere Inhalte wie gesprochenen Text, [vorgeschlagene Aktionen](v4sdk/bot-builder-howto-add-suggested-actions.md), [Medienanlagen](v4sdk/bot-builder-howto-add-media-attachments.md), [Rich Cards](v4sdk/bot-builder-howto-add-media-attachments.md#send-a-hero-card) und [kanalspezifische Daten](~/v4sdk/bot-builder-channeldata.md) enthalten können.

::: moniker-end

## <a name="contactrelationupdate"></a>contactRelationUpdate

Ein Bot empfängt ein contactRelationUpdateActivity-Objekt, wenn er der Kontaktliste eines Benutzers hinzugefügt oder aus ihr entfernt wird. Der Wert der Action-Eigenschaft (add/remove) der Aktivität gibt an, ob der Bot der Kontaktliste des Benutzers hinzugefügt oder aus ihr entfernt wurde.

## <a name="conversationupdate"></a>conversationUpdate

Ein Bot empfängt ein conversationUpdateActivity-Objekt, wenn er einer Konversation hinzugefügt wurde, andere Mitglieder hinzugefügt bzw. aus einer Konversation entfernt wurden oder Konversationsmetadaten geändert wurden.

Wenn der Konversation Mitglieder hinzugefügt wurden, enthält die Eigenschaft „members added“ der Aktivität ein Array von ChannelAccount-Objekten zum Identifizieren der neuen Mitglieder.

Werten Sie aus, ob der Wert der Empfänger-ID für die Aktivität (also die ID Ihres Bots) mit der ID-Eigenschaft eines der Konten im Array „members added“ übereinstimmt, um zu ermitteln, ob Ihr Bot der Konversation hinzugefügt wurde (bzw. eines der neuen Mitglieder ist).

Wenn Mitglieder aus der Konversation entfernt wurden, enthält die Eigenschaft „members removed“ ein Array aus ChannelAccount-Objekten zum Identifizieren entfernter Mitglieder.

> [!TIP]
> Wenn Ihr Bot ein conversationUpdateActivity-Objekt empfängt, das angibt, dass ein Benutzer der Konversation beigetreten ist, können Sie auswählen, ob eine Begrüßungsnachricht an den Benutzer gesendet werden soll.

## <a name="deleteuserdata"></a>deleteUserData

Ein Bot empfängt ein deleteUserDataActivity-Objekt, wenn ein Benutzer die Löschung von Daten anfordert, die der Bot zuvor für diesen Benutzer gespeichert hat. Wenn Ihr Bot diese Art von Aktivität empfängt, sollte er alle personenbezogenen Informationen löschen, die er zuvor für den Benutzer gespeichert hat, der die Anforderung gesendet hat.

## <a name="endofconversation"></a>endOfConversation

Ein Bot empfängt ein endOfConversationActivity-Objekt, um anzugeben, dass der Benutzer die Konversation beendet hat. Ein Bot kann ein endOfConversationActivity-Objekt senden, um dem Benutzer mitzuteilen, dass die Konversation beendet wird.

## <a name="event"></a>event

Ihr Bot kann ein eventActivity-Objekt von einem externen Prozess oder Dienst empfangen, der Ihrem Bot Informationen mitteilen möchte, ohne dass diese Informationen für Benutzer sichtbar sind. Der Absender eines eventActivity-Objekts erwartet in der Regel keine Bestätigung des Empfangs.

## <a name="installationupdate"></a>installationUpdate

installationUpdateActivity-Objekte stellen die Installation oder Deinstallation eines Bots in einer Organisationseinheit (z.B. einem Kundenmandanten oder einem „Team“) eines Kanals dar. Im Allgemeinen stellen sie nicht das Hinzufügen oder Entfernen eines Kanals dar. Kanäle können installationActivity-Objekte senden, wenn ein Bot einem Mandanten, Team oder einer anderen Organisationseinheit im Kanal hinzugefügt oder daraus entfernt wird. Kanäle sollten keine installationActivity-Objekte senden, wenn der Bot in einen Kanal installiert oder aus einem Kanal entfernt wird.

## <a name="invoke"></a>invoke

Ihr Bot kann ein invokeActivity-Objekt empfangen, das eine Anforderung darstellt, damit er einen bestimmten Vorgang ausführt.
Der Absender eines invokeActivity-Objekts erwartet in der Regel eine Bestätigung des Empfangs über eine HTTP-Antwort.
Dieser Aktivitätstyp ist für die interne Verwendung durch Microsoft Bot Framework reserviert.

## <a name="messagereaction"></a>messageReaction

Einige Kanäle senden messageReactionActivity-Objekte an Ihren Bot, wenn ein Benutzer auf eine vorhandene Aktivität reagiert. Beispiel: Ein Benutzer hat auf die Schaltfläche „Gefällt mir“ einer Nachricht geklickt. Die replyToId-Eigenschaft gibt an, auf welche Aktivität der Benutzer reagiert hat.

Das messageReactionActivity-Objekt kann einer beliebigen Anzahl von messageReaction-Typen entsprechen, die der Kanal definiert. „Gefällt mir“ oder „PlusOne“ sind Beispiele für Reaktionen, die ein Kanal möglicherweise sendet.

## <a name="typing"></a>typing

Ein Bot empfängt ein typingActivity-Objekt, um anzugeben, dass der Benutzer eine Antwort eingibt.
Ein Bot kann ein typingActivity-Objekt senden, um dem Benutzer mitzuteilen, dass er an einer Anforderung arbeitet oder eine Antwort erstellt.

::: moniker range="azure-bot-service-3.0"

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- <a href="https://docs.botframework.com/en-us/csharp/builder/sdkreference/dc/d2f/class_microsoft_1_1_bot_1_1_connector_1_1_activity.html" target="_blank">Activity-Klasse</a>
::: moniker-end
