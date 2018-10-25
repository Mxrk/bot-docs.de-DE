---
title: Senden eines Eingabeindikators | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie den Indikator „Bitte warten“ hinzufügen, um einem Benutzer mitzuteilen, dass ein Bot gerade eine Anforderung mithilfe des Bot Builder SDK für Node.js verarbeitet
author: DeniseMak
ms.author: v-demak
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 738816e2e790dc365e858cb27ee5e58a9d8b6d97
ms.sourcegitcommit: b78fe3d8dd604c4f7233740658a229e85b8535dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2018
ms.locfileid: "50000177"
---
# <a name="send-a-typing-indicator"></a>Senden eines Eingabeindikators 

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

Benutzer erwarten eine zeitnahe Antwort auf ihre Nachrichten. Wenn Ihr Bot eine langdauernde Aufgabe ausführt (z. B. Aufrufen eines Servers oder Ausführen einer Abfrage), ohne dem Benutzer einen Hinweis zu geben, dass der Bot ihn gehört hat, könnte der Benutzer ungeduldig werden und weitere Nachrichten senden oder einfach davon ausgehen, dass der Bot fehlerhaft ist.
Viele Kanäle unterstützen das Senden eines Eingabeindikators, um den Benutzern zu zeigen, dass die Nachricht empfangen wurde und verarbeitet wird.


## <a name="typing-indicator-example"></a>Beispiel für einen Eingabeindikator

Im folgenden Beispiel wird veranschaulicht, wie mithilfe von [session.sendTyping()][SendTyping] ein Eingabeindikator gesendet wird.  Sie können dies mit dem Bot Framework-Emulator testen.


```javascript

// Create bot and default message handler
var bot = new builder.UniversalBot(connector, function (session) {
    session.sendTyping();
    setTimeout(function () {
        session.send("Hello there...");
    }, 3000);
});
```

Eingabeindikatoren sind auch beim Einfügen einer Nachrichtenverzögerung hilfreich, um zu verhindern, dass Nachrichten mit Bildern in falscher Reihenfolge gesendet werden.

Weitere Informationen finden Sie unter [Senden von Rich Cards](bot-builder-nodejs-send-rich-cards.md).


## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [sendTyping][SendTyping]


[SendTyping]: https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.session#sendtyping
[IMessage]: http://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.imessage
