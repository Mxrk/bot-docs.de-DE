---
title: Behandeln von Benutzer- und Unterhaltungsereignissen | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Ereignisse wie das Beitreten eines Benutzers zu einer Unterhaltung mithilfe des Bot Framework SDK für Node.js behandelt werden.
author: DucVo
ms.author: v-ducvo
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 91fd349173dfe469c7b71bc84b8adf9c19e07277
ms.sourcegitcommit: b15cf37afc4f57d13ca6636d4227433809562f8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/11/2019
ms.locfileid: "54223605"
---
# <a name="handle-user-and-conversation-events"></a>Behandeln von Benutzer- und Unterhaltungsereignissen

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

In diesem Artikel wird veranschaulicht, wie Ihr Bot Ereignisse behandeln kann, wie z.B. das Beitreten eines Benutzers zu einer Unterhaltung, das Hinzufügen eines Bots zu einer Kontaktliste oder das Ausgeben von **Goodbye**, wenn ein Bot aus einer Unterhaltung entfernt wird.


## <a name="greet-a-user-on-conversation-join"></a>Begrüßen eines Benutzers beim Beitritt zu einer Unterhaltung
Bot Framework stellt das Ereignis [conversationUpdate][conversationUpdate] bereit, um Ihren Bot zu benachrichtigen, wenn ein Mitglied einer Unterhaltung beitritt oder diese verlässt. Bei Mitgliedern einer Unterhaltung kann es sich um Benutzer oder Bots handeln.

Der folgende Codeausschnitt ermöglicht dem Bot, neue Mitglieder einer Unterhaltung zu begrüßen oder **Goodbye** auszugeben, wenn Mitglieder aus der Unterhaltung entfernt werden.

[!INCLUDE [conversationUpdate sample Node.js](../includes/snippet-code-node-conversationupdate-1.md)]

## <a name="acknowledge-add-to-contacts-list"></a>Bestätigen des Vorgangs zum Hinzufügen zur Kontaktliste

Das Ereignis [contactRelationUpdate][contactRelationUpdate] benachrichtigt Ihren Bot, dass ein Benutzer Sie zu seiner Kontaktliste hinzugefügt hat.

[!INCLUDE [contactRelationUpdate sample Node.js](../includes/snippet-code-node-contactrelationupdate-1.md)]

## <a name="add-a-first-run-dialog"></a>Hinzufügen eines Dialogs beim ersten Ausführen

Da die Ereignisse **conversationUpdate** und **contactRelationUpdate** nicht von allen Kanälen unterstützt werden, besteht eine universelle Möglichkeit zum Begrüßen eines Benutzers, der einer Unterhaltung beigetreten ist, darin, ein Dialog beim ersten Ausführen hinzuzufügen.

Im folgenden Beispiel haben wir eine Funktion hinzugefügt, die den Dialog immer dann auslöst, wenn ein Benutzer neu ist. Sie können die Art und Weise, wie eine Aktion ausgelöst wird, anpassen, indem Sie einen [onFindAction][onFindAction]-Handler für Ihre Aktion bereitstellen. 

[!INCLUDE [first-run sample Node.js](../includes/snippet-code-node-first-run-dialog-1.md)]

Auch die Schritte, die nach der Auslösung einer Aktion ausgeführt werden, können Sie anpassen, indem Sie einen [onSelectAction][onSelectAction]-Handler bereitstellen. Bei Triggeraktionen können Sie einen [onInterrupted][onInterrupted]-Handler bereitstellen, der eine Unterbrechung abfängt, bevor sie stattfindet. Weitere Informationen finden Sie unter [Behandeln von Benutzeraktionen](bot-builder-nodejs-dialog-actions.md).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [conversationUpdate][conversationUpdate]
* [contactRelationUpdate][contactRelationUpdate]

[conversationUpdate]: https://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.iconversationupdate.html
[contactRelationUpdate]: https://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.icontactrelationupdate.html

[onFindAction]: https://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.itriggeractionoptions#onfindaction
[onSelectAction]: https://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.itriggeractionoptions#onselectaction
[onInterrupted]: https://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.itriggeractionoptions#oninterrupted

[SendTyping]: https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.session#sendtyping
[IMessage]: http://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.imessage
[ChatConnector]: https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.chatconnector.html
[session_userData]: https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.session.html#userdata
