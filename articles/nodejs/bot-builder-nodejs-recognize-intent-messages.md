---
title: Erkennen von Absichten anhand des Nachrichteninhalts | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie mithilfe von regulären Ausdrücken oder durch Überprüfen des Nachrichteninhalts die Absicht des Benutzers erkennen.
author: DeniseMak
ms.author: v-demak
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 82541c4ac0848c3e995ab3ad1ed874436072fe63
ms.sourcegitcommit: b78fe3d8dd604c4f7233740658a229e85b8535dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2018
ms.locfileid: "49998082"
---
# <a name="recognize-user-intent-from-message-content"></a>Erkennen von Absichten anhand des Nachrichteninhalts

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

Wenn Ihr Bot eine Nachricht von einem Benutzer empfängt, kann Ihr Bot mit einer **Erkennung** die Nachricht überprüfen und die Absicht bestimmen. Die Absicht stellt eine Zuordnung von Nachrichten zu den aufzurufenden Dialogen bereit. In diesem Artikel wird erläutert, wie Absichten mithilfe von regulären Ausdrücken oder durch Überprüfen des Nachrichteninhalts erkannt werden. Ein Bot kann z. B. mithilfe von regulären Ausdrücken überprüfen, ob eine Nachricht das Wort „Hilfe“ enthält, und einen Hilfedialog aufrufen. Ein Bot kann auch die Eigenschaften der Benutzernachricht überprüfen, um beispielsweise festzustellen, ob der Benutzer ein Bild statt Text gesendet hat, und einen Bildverarbeitungsdialog aufrufen. 

> [!NOTE]
> Informationen zum Erkennen von Absichten mit LUIS finden Sie unter [Erkennen von Absichten und Entitäten mit LUIS](bot-builder-nodejs-recognize-intent-luis.md) 


## <a name="use-the-built-in-regular-expression-recognizer"></a>Verwenden der integrierten Erkennung mit regulären Ausdrücken
Mithilfe von [RegExpRecognizer][RegExpRecognizer] können Sie mit einem regulären Ausdruck die Absicht des Benutzers erkennen. Sie können mehrere Ausdrücke an die Erkennung übergeben, um mehrere Sprachen zu unterstützen. 

> [!TIP]
> Weitere Informationen zum Lokalisieren Ihres Bots finden Sie unter [Unterstützen der Lokalisierung](bot-builder-nodejs-localization.md).

Mit dem folgenden Code wird eine Erkennung mit regulären Ausdrücken namens `CancelIntent` erstellt und Ihrem Bot hinzugefügt. Die Erkennung in diesem Beispiel stellt reguläre Ausdrücke für die Gebietsschemas `en_us` und `ja_jp` bereit. 

[!code-js[Add a regular expression recognizer (JavaScript)](../includes/code/node-regex-recognizer.js#addRegexRecognizer)]

Sobald die Erkennung Ihrem Bot hinzugefügt wurde, fügen Sie dem Dialog eine [TriggerAction][triggerAction] an, die der Bot aufrufen soll, wenn die Erkennung die Absicht erkennt. Verwenden Sie die [matches][matches]-Option, um den Namen der Absicht anzugeben, wie im folgenden Code gezeigt:

[!code-js[Map the CancelIntent recognizer to a cancel dialog (JavaScript)](../includes/code/node-regex-recognizer.js#bindCancelDialogToRegexRecognizer)]

Absichtserkennungen sind global, was bedeutet, dass die Erkennung für jede Nachricht ausgeführt wird, die vom Benutzer empfangen wird. Wenn eine Erkennung eine Absicht erkennt, die mit einer `triggerAction` an einen Dialog gebunden ist, kann sie eine Unterbrechung des derzeit aktiven Dialogs auslösen. Das Zulassen und Verarbeiten von Unterbrechungen ist ein flexibles Konzept, das die tatsächlichen Aktivitäten der Benutzer berücksichtigt.

> [!TIP] 
> Informationen zur Funktionsweise einer `triggerAction` mit Dialogen finden Sie unter [Verwalten des Konversationsflusses](bot-builder-nodejs-manage-conversation-flow.md). Unter [Behandeln von Benutzeraktionen](bot-builder-nodejs-dialog-actions.md) erfahren Sie mehr über die verschiedenen Aktionen, die Sie einer erkannten Absicht zuordnen können.

## <a name="register-a-custom-intent-recognizer"></a>Registrieren einer benutzerdefinierten Absichtserkennung
Sie können auch eine benutzerdefinierte Erkennung implementieren. In diesem Beispiel wird eine einfache Erkennung hinzugefügt, die darauf achtet, ob der Benutzer „Help“ (Hilfe) oder „goodbye“ (Auf Wiedersehen) sagt. Sie können aber auch eine Erkennung hinzufügen, die eine komplexere Verarbeitung übernimmt und z. B. erkennt, wenn der Benutzer ein Bild sendet. 


[!code-js[Add a custom recognizer (JavaScript)](../includes/code/node-howto-recognize-intent.js#addCustomRecognizer)]

Sobald Sie eine Erkennung registriert haben, können Sie diese mit einer `matches`-Klausel einer Aktion zuordnen.

[!code-js[Bind intents to actions (JavaScript)](../includes/code/node-howto-recognize-intent.js#bindIntentsToActions)]

## <a name="disambiguate-between-multiple-intents"></a>Vermeiden der Mehrdeutigkeit bei mehreren Absichten

Ihr Bot kann mehr als eine Erkennung registrieren. Beachten Sie, dass die benutzerdefinierte Erkennung im Beispiel auch jeder Absicht einen numerischen Wert (score) zuweist. Dies geschieht, da Ihr Bot möglicherweise über mehrere Erkennungen verfügt und das Bot Builder SDK eine integrierte Logik bereitstellt, um Mehrdeutigkeiten zwischen den von mehreren Erkennungen zurückgegebenen Absichten zu vermeiden. Der einer Absicht zugewiesene Wert liegt in der Regel zwischen 0.0 und 1.0, doch möglicherweise definiert eine benutzerdefinierte Erkennung eine Absicht mit einem Wert größer als 1.1, um sicherzustellen, dass die Absicht stets von der Mehrdeutigkeitsvermeidungslogik des Bot Builder SDK ausgewählt wird. 

Standardmäßig werden Erkennungen parallel ausgeführt, aber Sie können in [IIntentRecognizerSetOptions][IntentRecognizerSetOptions] mit „RecognizeOrder“ die Erkennungsreihenfolge festlegen, sodass der Prozess beendet wird, sobald Ihr Bot eine Erkennung mit dem Punktwert 1.0 findet.

Das Bot Builder SDK enthält ein [Beispiel][DisambiguationSample], das veranschaulicht, wie durch die Implementierung von [IDisambiguateRouteHandler][IDisambiguateRouteHandler] die benutzerdefinierte Mehrdeutigkeitsvermeidungslogik in Ihrem Bot implementiert wird.

## <a name="next-steps"></a>Nächste Schritte
Die Logik zum Verwenden von regulären Ausdrücken und zum Überprüfen von Nachrichteninhalten kann recht komplex werden, vor allem wenn das Ende des Konversationsflusses beim Bot unbestimmt ist. Damit Ihr Bot eine größere Anzahl von Text und Spracheingaben von Benutzern verarbeiten kann, können Sie einen Absichtserkennungsdienst wie [LUIS][LUIS] verwenden, um dem Bot ein natürliches Sprachverständnis hinzuzufügen.

> [!div class="nextstepaction"]
> [Erkennen von Absichten und Entitäten mit LUIS](bot-builder-nodejs-recognize-intent-luis.md)


[LUIS]: https://www.luis.ai/

[triggerAction]: https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.dialog.html#triggeraction

[matches]: https://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.itriggeractionoptions.html#matches

[node-js-bot-how-to]: bot-builder-nodejs-recognize-intent-luis.md

[LUISAzureDocs]: /azure/cognitive-services/LUIS/Home

[IMessage]: http://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.imessage

[IntentRecognizerSetOptions]: https://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.iintentrecognizersetoptions.html

[LuisRecognizer]: https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.luisrecognizer

[LUISSample]: https://aka.ms/v3-js-luisSample

[LUISConcepts]: https://docs.botframework.com/en-us/node/builder/guides/understanding-natural-language/

[DisambiguationSample]: https://aka.ms/v3-js-onDisambiguateRoute

[IDisambiguateRouteHandler]: https://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.idisambiguateroutehandler.html

[RegExpRecognizer]: https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.regexprecognizer.html

[AlarmBot]: https://aka.ms/v3-js-luisSample
