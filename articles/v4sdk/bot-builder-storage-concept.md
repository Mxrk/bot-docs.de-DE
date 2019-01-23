---
redirect_url: /bot-framework/bot-builder-howto-v4-state
ms.openlocfilehash: a0d2b1295be1271e827d617ad09878ee8cfcd356
ms.sourcegitcommit: b15cf37afc4f57d13ca6636d4227433809562f8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/11/2019
ms.locfileid: "54223915"
---
<a name="--"></a><!--
---
Titel: Zustand und Speicher | Microsoft-Dokumentation description: Hier erfahren Sie mehr über Status-Manager, Konversationszustand und Benutzerstatus innerhalb des Bot Framework SDK.
keywords: LUIS, Konversationszustand, Benutzerstatus, Speicher, Statusverwaltung author: DeniseMak ms.author: v-demak manager: kamrani ms.topic: article ms.service: bot-service ms.subservice: sdk ms.date: 02/15/2018 monikerRange: 'azure-bot-service-4.0'
---

# <a name="state-and-storage"></a>Zustand und Speicher
[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

Wesentlich für gute Botentwicklung ist, den Unterhaltungskontext nachzuverfolgen, damit sich der Bot beispielsweise an Antworten auf bereits gestellte Fragen erinnert.
Je nachdem, wofür Ihr Bot verwendet wird, müssen Sie möglicherweise auch den Status verfolgen oder Informationen über die Lebensdauer einer Unterhaltung hinaus speichern.
Der *Status* eines Bots ist eine Information, die er sich merkt, um auf eingehende Nachrichten angemessen zu reagieren. Das Bot Framework SDK stellt Klassen zum Speichern und Abrufen von Zustandsdaten als Objekt bereit, das einem Benutzer oder einer Konversation zugeordnet ist.

* **Unterhaltungseigenschaften** helfen Ihrem Bot, die aktuelle Unterhaltung mit dem Benutzer nachzuverfolgen. Wenn Ihr Bot eine Reihe von Schritten ausführen oder zwischen Unterhaltungsthemen wechseln muss, können Sie mit den Unterhaltungseigenschaften diese Schritte verwalten oder das aktuelle Thema nachverfolgen. Da die Konversationseigenschaften den Zustand der aktuellen Konversation wiedergeben, löschen Sie sie in der Regel am Ende einer Konversation, wenn der Bot eine Aktivität zum _Beenden der Konversation_ empfängt.
* **Benutzereigenschaften** können für viele Zwecke verwendet werden, z.B. um festzustellen, an welcher Stelle eine frühere Unterhaltung unterbrochen wurde, oder um einen wiederkehrenden Benutzer mit seinem Namen zu begrüßen. Wenn Sie die Einstellungen eines Benutzers speichern, können Sie diese Informationen zum Anpassen der Unterhaltung beim nächsten Chat verwenden. Sie können beispielsweise den Benutzer auf einen Nachrichtenartikel zu einem Thema, das ihn interessiert, aufmerksam machen oder ihn benachrichtigen, wenn ein Termin verfügbar wird. Sie sollten die Daten löschen, wenn der Bot eine Aktivität zum _Löschen der Benutzerdaten_ empfängt.

Sie können [Speicher](bot-builder-howto-v4-storage.md) verwenden, um aus einem beständigen Speicher zu lesen und in den Speicher zu schreiben. So kann Ihr Bot beispielsweise gemeinsame Ressourcen aktualisieren, Antworten oder Abstimmungen aufzeichnen oder historische Wetterdaten lesen. Genauso wie eine App den Speicher für ihre Zwecke nutzt, kann Ihr Bot eine Unterhaltung mit Ihrem Benutzer verwenden.

<!-- 
*Conversation state* pertains to the current conversation that the user is having with your bot. When the conversation ends, your bot deletes this data.

You can also store *user state* that persists after a conversation ends. For example, if you store a user's preferences, you can use that information to customize the conversation the next time you chat. For example, you might alert the user to a news article about a topic that interests her, or alert a user when an appointment becomes available. 
-->

<!-- You should generally avoid saving state using a global variable or function closures.
Doing so will create issues when you want to scale out your bot. Instead, use the conversation state and user state middleware that the BotBuilder SDK provides --> 

<!--
## Types of underlying storage

The SDK provides bot state manager middleware to persist conversation and user state. State can be accessed using the bot's context. This state manager can use Azure Table Storage, file storage, or memory storage as the underlying data storage. You can also create your own storage components for your bot.

Bots built using Azure Table Storage can be designed to be stateless and scalable across multiple compute nodes.

> [!NOTE] 
> File and memory storage won't scale across nodes.

## Writing directly to storage

You can also use the Bot Framework SDK to read and write data directly to storage, without using middleware or without using the bot context. This can be appropriate to data that your bot uses, that comes from a source outside your bot's conversation flow.

For example, let's say your bot allows the user to ask for the weather report, and your bot retrieves the weather report for a specified date, by reading it from an external database. The content of the weather database isn't dependent on user information or the conversation context, so you could just read it directly from storage instead of using the state manager.  See [How to write directly to storage](bot-builder-howto-v4-storage.md) for an example.

## Next steps

Next, lets get into how activities are processed, in depth, and how we respond to them.

> [!div class="nextstepaction"]
> [Activity Processing](bot-builder-concept-activity-processing.md)

## Additional resources

- [How to save state](bot-builder-howto-v4-state.md)
- [How to write directly to storage](bot-builder-howto-v4-storage.md)

-->
