---
title: Hinzufügen von Sprache zu Nachrichten | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie mithilfe des Bot Framework SDK für Node.js Nachrichten Sprache hinzufügen.
author: v-ducvo
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: f7e68b9ab6ef1fca189108ed4117c0ab17f4d9f2
ms.sourcegitcommit: b15cf37afc4f57d13ca6636d4227433809562f8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/11/2019
ms.locfileid: "54224295"
---
# <a name="add-speech-to-messages"></a>Hinzufügen von Sprache zu Nachrichten

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

> [!div class="op_single_selector"]
> - [.NET](../dotnet/bot-builder-dotnet-text-to-speech.md)
> - [Node.js](../nodejs/bot-builder-nodejs-text-to-speech.md)
> - [REST](../rest-api/bot-framework-rest-connector-text-to-speech.md)

Wenn Sie einen Bot für einen sprachaktivierten Kanal wie Cortana erstellen, können Sie Nachrichten erstellen, die den Text angeben, der von Ihrem Bot gesprochen werden soll. Sie können auch versuchen, den Status des Mikrofons des Clients zu beeinflussen, indem Sie einen [Eingabehinweis](bot-builder-nodejs-send-input-hints.md) angeben, um festzulegen, ob Ihr Bot Benutzereingaben akzeptiert, erwartet oder ignoriert.

## <a name="specify-text-to-be-spoken-by-your-bot"></a>Angeben des vom Bot zu sprechenden Texts

Mit dem Bot Framework SDK für Node.js stehen Ihnen mehrere Möglichkeiten zum Angeben des Texts zur Verfügung, der von Ihrem Bot in einem sprachaktivierten Kanal gesprochen werden soll. Sie können die Eigenschaft `IMessage.speak` festlegen und die Nachricht mit der Methode `session.send()` senden, die Nachricht mit der Methode `session.say()` senden (Parameter übergeben, die Anzeigetext, Sprachtext und Optionen angeben) oder die Nachricht mit einer integrierten Eingabeaufforderung senden (Optionen `speak` und `retrySpeak` angeben).

### <a id="message-speak"></a> IMessage.speak 

Wenn Sie eine Nachricht erstellen, die mit der Methode `session.send()` gesendet wird, legen Sie die Eigenschaft `speak` fest, um den Text anzugeben, der von Ihrem Bot gesprochen werden soll. Im folgenden Codebeispiel wird eine Nachricht erstellt, die den zu sprechenden Text festlegt und angibt, dass der Bot [Benutzereingaben akzeptiert](bot-builder-nodejs-send-input-hints.md).

[!code-javascript[IMessage.speak](../includes/code/node-text-to-speech.js#IMessageSpeak)]

### <a id="session-say"></a> session.say()

Als Alternative zu `session.send()` können Sie die Methode `session.say()` aufrufen, um eine Nachricht zu erstellen und zu senden, die den zu sprechenden Text sowie den anzuzeigenden Text und andere Optionen angibt. Die Methode ist folgendermaßen definiert:

`session.say(displayText: string, speechText: string, options?: object)`

| Parameter | BESCHREIBUNG |
|----|----|
| `displayText` | Der Text, der angezeigt werden soll. |
| `speechText` | Der Text (Nur-Text- oder <a href="https://msdn.microsoft.com/en-us/library/hh378377(v=office.14).aspx" target="_blank">SSML</a>-Format), der gesprochen werden soll. |
| `options` | Ein [IMessage][IMessage]-Objekt, das eine Anlage oder einen [Eingabehinweis](bot-builder-nodejs-send-input-hints.md) enthalten kann. |

Im folgenden Codebeispiel wird eine Nachricht gesendet, die den anzuzeigenden Text und den zu sprechenden Text festlegt und angibt, dass der Bot [Benutzereingaben ignoriert](bot-builder-nodejs-send-input-hints.md).

[!code-javascript[Session.say()](../includes/code/node-text-to-speech.js#SessionSay)]

### <a id="prompt-options"></a> Eingabeaufforderungsoptionen

Wenn Sie eine der integrierten Eingabeaufforderungen verwenden, können Sie die Optionen `speak` und `retrySpeak` festlegen, um den Text anzugeben, der von Ihrem Bot gesprochen werden soll. Im folgenden Codebeispiel wird eine Eingabeaufforderung erstellt, die den anzuzeigenden Text, den zuerst zu sprechenden Text und den Text angibt, der nach einer Weile des Wartens auf Benutzereingaben gesprochen werden soll. Dieser Bot [erwartet eine Benutzereingabe](bot-builder-nodejs-send-input-hints.md) und verwendet die [SSML](#ssml)-Formatierung, um anzugeben, dass das Wort „sicher“ mit mäßiger Betonung gesprochen werden soll.

[!code-javascript[Prompt](../includes/code/node-text-to-speech.js#Prompt)]

## <a id="ssml"></a> Speech Synthesis Markup Language (SSML)

Um den von Ihrem Bot zu sprechenden Text anzugeben, können Sie entweder eine Nur-Text-Zeichenfolge oder eine als Speech Synthesis Markup Language (SSML) formatierte Zeichenfolge verwenden. Bei SSML handelt es sich um eine XML-basierte Markupsprache, mit der Sie verschiedene Eigenschaften der Sprache Ihres Bots steuern können, z.B. Stimme, Geschwindigkeit, Lautstärke, Aussprache, Tonhöhe und mehr. Ausführliche Informationen zu SSML finden Sie in der <a href="https://msdn.microsoft.com/en-us/library/hh378377(v=office.14).aspx" target="_blank">Referenz zu Speech Synthesis Markup Language</a>.

> [!TIP]
> Verwenden Sie eine <a href="https://www.npmjs.com/search?q=ssml" target="_blank">SSML-Bibliothek</a>, um korrekt formatierte SSML zu erstellen.

## <a name="input-hints"></a>Eingabehinweise

Wenn Sie eine Nachricht in einem sprachaktivierten Kanal senden, können Sie versuchen, den Status des Mikrofons des Clients zu beeinflussen, indem Sie auch einen Eingabehinweis einschließen, um anzugeben, ob Ihr Bot Benutzereingaben akzeptiert, erwartet oder ignoriert. Weitere Informationen finden Sie unter [Hinzufügen von Eingabehinweisen zu Nachrichten](bot-builder-nodejs-send-input-hints.md).

## <a name="sample-code"></a>Beispielcode 

Ein vollständiges Beispiel, das die Erstellung eines sprachaktivierten Bots mit dem Bot Framework SDK für .NET veranschaulicht, finden Sie auf GitHub im <a href="https://github.com/Microsoft/BotBuilder-Samples/tree/master/Node/demo-RollerSkill" target="_blank">Roller-Beispiel</a>.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- <a href="https://msdn.microsoft.com/en-us/library/hh378377(v=office.14).aspx" target="_blank">Speech Synthesis Markup Language (SSML)</a>
- <a href="https://github.com/Microsoft/BotBuilder-Samples/tree/master/Node/demo-RollerSkill" target="_blank">Rolle-Beispiel (GitHub)</a>
- [Referenz zum Bot Framework SDK für Node.js][SDKReference]

[SDKReference]: https://docs.botframework.com/en-us/node/builder/chat-reference/modules/_botbuilder_d_.html

[Message]: https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.message

[IMessage]: http://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.imessage
