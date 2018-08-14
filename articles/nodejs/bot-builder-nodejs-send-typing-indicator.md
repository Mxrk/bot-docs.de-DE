---
title: Senden eines Eingabeindikators | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie den Indikator „Bitte warten“ hinzufügen, um einem Benutzer mitzuteilen, dass ein Bot gerade eine Anforderung mithilfe des Bot Builder SDK für Node.js verarbeitet
author: DeniseMak
ms.author: v-demak
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: aff2509a426fb42f136fb9d2b4a2df9ec1accda0
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39303912"
---
# <a name="send-a-typing-indicator"></a>Senden eines Eingabeindikators 


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