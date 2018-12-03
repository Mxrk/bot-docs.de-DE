---
title: Durchführen von Audioanrufen | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Audioanrufe mit Skype in einem Bot mit Node.js durchführen.
author: v-ducvo
ms.author: v-ducvo
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 12/13/17
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 9251307cb77cfb240e88c7ae44b13fe7e7fa2cdb
ms.sourcegitcommit: b78fe3d8dd604c4f7233740658a229e85b8535dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2018
ms.locfileid: "49998637"
---
# <a name="support-audio-calls-with-skype"></a>Unterstützen von Audioanrufen mit Skype

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

Skype unterstützt ein umfassendes Feature namens Anrufbots.  Nach Aktivierung können Benutzer einen Sprachanruf bei Ihrem Bot durchführen und über interaktive Sprachantworten (IVR) mit ihm interagieren.  Das Bot Builder für Node.js SDK enthält ein spezielles [Aufruf-SDK][calling_sdk], mit dem Entwickler ihrem Chatbot Anruffeatures hinzufügen können.   

Das Anruf-SDK ähnelt stark dem [Chat-SDK][chat_sdk]. Beide verfügen über ähnliche Klassen, verwenden gemeinsame Konstrukte, und Sie können mit dem Chat-SDK sogar Nachrichten an den Benutzer senden, mit dem Sie einen Anruf durchführen.  Die zwei SDKs sind darauf ausgelegt, parallel ausgeführt zu werden. Auch wenn sie ähnlich sind, bestehen jedoch einige wichtige Unterschiede, und Sie sollten im Allgemeinen Klassen aus den beiden Bibliotheken nicht kombinieren.  

## <a name="create-a-calling-bot"></a>Erstellen eines Anrufbots
Der folgende Beispielcode zeigt, dass das Hallo Welt-Programm für einen Anrufbot einem regulären Chatbot stark ähnelt. 

```javascript
var restify = require('restify');
var calling = require('botbuilder-calling');

// Setup Restify Server
var server = restify.createServer();
server.listen(process.env.port || process.env.PORT || 3978, function () {
   console.log(`${server.name} listening to ${server.url}`); 
});

// Create calling bot
var connector = new calling.CallConnector({
    callbackUrl: 'https://<your host>/api/calls',
    appId: '<your bots app id>',
    appPassword: '<your bots app password>'
});
var bot = new calling.UniversalCallBot(connector);
server.post('/api/calls', connector.listen());

// Add root dialog
bot.dialog('/', function (session) {
    session.send('Watson... come here!');
});
```

> [!NOTE]
> Unter [MicrosoftAppID und MicrosoftAppPassword](~/bot-service-manage-overview.md#microsoftappid-and-microsoftapppassword) wird beschrieben, wie Sie die Werte für **AppID** und **AppPassword** für Ihren Bot finden.

Der Emulator unterstützt derzeit keine Tests von Anrufbots. Um Ihren Bot zu testen, müssen Sie die meisten erforderlichen Schritte zum Veröffentlichen Ihres Bots durchlaufen.  Sie müssen für die Interaktion mit dem Bot auch einen Skype-Client verwenden. 

### <a name="enable-the-skype-channel"></a>Aktivieren des Skype-Kanals
[Registrieren Sie Ihren Bot](../bot-service-quickstart-registration.md), und aktivieren Sie den Skype-Kanal. Sie müssen einen Messagingendpunkt bereitstellen, wenn Sie Ihren Bot registrieren. Es wird empfohlen Ihren Anrufbot mit einem Chatbot zu verbinden, daher fügen Sie in dieses Feld normalerweise den Endpunkt des Chatbots ein.  Wenn Sie nur einen Anrufbot registrieren, können Sie einfach Ihren Anrufendpunkt in das Feld einfügen.  

Um das eigentliche Anruffeature zu aktivieren, müssen Sie zum Skype-Kanal für Ihren Bot wechseln und das Anruffeature aktivieren. Dadurch wird ein Feld bereitgestellt, in das Sie den Anrufendpunkt kopieren können. Stellen Sie sicher, dass Sie den https-ngrok-Link für den Hostteil des Anrufendpunkts verwenden.

Während der Registrierung Ihres Bots werden Ihnen eine App-ID und ein Kennwort zugewiesen, die Sie in die Connectoreinstellungen für Ihren Hallo Welt-Bot einfügen sollten. Sie müssen auch den vollständigen Anruflink in die Einstellung „callbackUrl“ einfügen.

### <a name="add-bot-to-contacts"></a>Hinzufügen des Bots zu Kontakten
Auf der Registrierungsseite für Ihren Bot im Entwicklerportal wird die Schaltfläche **add to Skype** (Hinzufügen zu Skype) neben dem Skype-Kanal Ihres Bots angezeigt. Klicken Sie auf die Schaltfläche, um den Bot Ihrer Kontaktliste in Skype hinzuzufügen.  Anschließend können Sie (und alle Benutzer, denen Sie den Link zum Beitreten mitteilen) mit dem Bot kommunizieren.

### <a name="test-your-bot"></a>Testen Ihres Bots
Sie können Ihren Bot mit einem Skype-Client testen. Wenn Sie auf den Kontakteintrag für Ihren Bot klicken, sollte das Anrufsymbol aufleuchten (möglicherweise müssen nach dem Bot suchen, damit es angezeigt wird).  Es kann einige Minuten dauern, bis das Anrufsymbol aufleuchtet, nachdem Sie das Anruffeature einem vorhandenen Bot hinzugefügt haben.  

Wenn Sie die Anrufschaltfläche auswählen, sollte Ihr Bot angewählt werden, und Sie sollten die Sprachausgabe „Watson… come here!“ hören. Anschließend wird aufgelegt.

## <a name="calling-basics"></a>Grundlagen von Anrufen
Mit den Klassen [UniversalCallBot](http://docs.botframework.com/en-us/node/builder/calling-reference/classes/_botbuilder_d_.universalcallbot) und [CallConnector](http://docs.botframework.com/en-us/node/builder/calling-reference/classes/_botbuilder_d_.callconnector) können Sie einen Anrufbot auf sehr ähnliche Weise erstellen wie einen Chatbot. Sie können dem Bot Dialoge hinzufügen, die im Wesentlichen identisch mit [Chatdialogen](bot-builder-nodejs-manage-conversation-flow.md) sind. Sie können Ihrem Bot [Wasserfälle](bot-builder-nodejs-prompts.md) hinzufügen. Es gibt ein Sitzungsobjekt, die [CallSession](http://docs.botframework.com/en-us/node/builder/calling-reference/classes/_botbuilder_d_.callsession)-Klasse, das die zusätzlichen Methoden [answer()](http://docs.botframework.com/en-us/node/builder/calling-reference/classes/_botbuilder_d_.callsession#answer), [hangup()](http://docs.botframework.com/en-us/node/builder/calling-reference/classes/_botbuilder_d_.callsession#hangup) und [reject()](http://docs.botframework.com/en-us/node/builder/calling-reference/classes/_botbuilder_d_.callsession#reject) zum Verwalten des aktuellen Anrufs enthält. Im Allgemeinen müssen Sie sich aber mit diesen Methoden zur Anrufsteuerung nicht auseinandersetzen, da CallSession Logik aufweist, die den Anruf automatisch für Sie verwaltet. Die Sitzung beantwortet den Anruf automatisch, wenn Sie eine Aktion wie das Senden einer Nachricht oder das Aufrufen einer integrierten Eingabeaufforderung durchführen. Der Anruf wird außerdem automatisch beendet/abgewiesen, wenn Sie [endConversation()](http://docs.botframework.com/en-us/node/builder/calling-reference/classes/_botbuilder_d_.callsession#endconversation) aufrufen oder erkannt wird, dass Sie dem Anrufer keine Fragen mehr stellen (Sie also keine integrierte Eingabeaufforderung aufgerufen haben).

Ein weiterer Unterschied zwischen Anruf- und Chatbots besteht darin, dass Chatbots in der Regel Nachrichten, Karten und Tastaturen an einen Benutzer senden, ein Anrufbot hingegen mit Aktionen und Ergebnissen befasst ist. Skype-Anrufbots müssen [Workflows](http://docs.botframework.com/en-us/node/builder/calling-reference/interfaces/_botbuilder_d_.iworkflow) erstellen, die sich aus [Aktionen](http://docs.botframework.com/en-us/node/builder/calling-reference/interfaces/_botbuilder_d_.iaction) zusammensetzen.  Dies ist ein weiterer Aspekt, um den Sie sich in der Praxis kaum kümmern müssen, da das Bot Builder-Anruf-SDK den Großteil für Sie verwaltet. Mit der [CallSession.send()](http://docs.botframework.com/en-us/node/builder/calling-reference/classes/_botbuilder_d_.callsession#send)-Methode können Sie Aktionen oder Zeichenfolgen übergeben, die in [PlayPromptActions](http://docs.botframework.com/en-us/node/builder/calling-reference/classes/_botbuilder_d_.playpromptaction) umgewandelt werden.  Die Sitzung enthält eine Logik für die automatische Batcherstellung, die mehrere Aktionen in einen einzelnen Workflow kombiniert, der an den Anrufdienst übermittelt wird. Sie können daher problemlos mehrmals send() aufrufen.  Außerdem sollten Sie Eingaben des Benutzers über die integrierten [Eingabeaufforderungen](bot-builder-nodejs-prompts.md) des SDK abfragen, da diese alle Ergebnisse verarbeiten.  

[calling_sdk]: http://docs.botframework.com/en-us/node/builder/calling-reference/modules/_botbuilder_d_
[chat_sdk]: http://docs.botframework.com/en-us/node/builder/chat-reference/modules/_botbuilder_d_
