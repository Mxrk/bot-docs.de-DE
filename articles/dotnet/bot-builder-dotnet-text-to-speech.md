---
title: Hinzufügen von Sprache zu Nachrichten | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie mithilfe des Bot Builder SDK für .NET Nachrichten Sprache hinzufügen.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: a8bc0b68b3dfa63ba4e91103c57d4fac60ddca79
ms.sourcegitcommit: 67445b42796d90661afc643c6bb6533e9a662cbc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/06/2018
ms.locfileid: "39574996"
---
# <a name="add-speech-to-messages"></a>Hinzufügen von Spracheingabe zu Nachrichten

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

> [!div class="op_single_selector"]
> - [.NET](../dotnet/bot-builder-dotnet-text-to-speech.md)
> - [Node.js](../nodejs/bot-builder-nodejs-text-to-speech.md)
> - [REST](../rest-api/bot-framework-rest-connector-text-to-speech.md)

Wenn Sie einen Bot für einen sprachaktivierten Kanal wie Cortana erstellen, können Sie Nachrichten erstellen, die den Text angeben, der von Ihrem Bot gesprochen werden soll. Sie können auch versuchen, den Status des Mikrofons des Clients zu beeinflussen, indem Sie einen [Eingabehinweis](bot-builder-dotnet-add-input-hints.md) angeben, um festzulegen, ob Ihr Bot Benutzereingaben akzeptiert, erwartet oder ignoriert.

## <a name="specify-text-to-be-spoken-by-your-bot"></a>Angeben des von Ihrem Bot zu sprechenden Texts

Mit dem Bot Builder SDK für .NET stehen Ihnen mehrere Möglichkeiten zum Angeben des Texts zur Verfügung, der von Ihrem Bot in einem sprachfähigen Kanal gesprochen werden soll. Sie können die `Speak`-Eigenschaft der [Nachricht][IMessageActivity] festlegen, die Methode `IDialogContext.SayAsync()` aufrufen oder die Eingabeaufforderungsoptionen `speak` und `retrySpeak` angeben, wenn Sie eine Nachricht mit einer integrierten Eingabeaufforderung senden.

### <a id="message-speak"></a> IMessageActivity.Speak

Wenn Sie eine [Nachricht][IMessageActivity] erstellen und ihre Eigenschaften festlegen, können Sie die `Speak`-Eigenschaft der Nachricht festlegen, um den Text anzugeben, der von Ihrem Bot gesprochen werden soll. Im folgenden Codebeispiel wird eine Nachricht erstellt, die den anzuzeigenden Text und den zu sprechenden Text festlegt und angibt, dass der Bot [Benutzereingaben akzeptiert](bot-builder-dotnet-add-input-hints.md).

[!code-csharp[Set speak property](../includes/code/dotnet-text-to-speech.cs#Speak1)]

### <a id="say-async"></a> IDialogContext.SayAsync()

Wenn Sie [Dialoge](bot-builder-dotnet-dialogs.md) verwenden, können Sie die Methode `SayAsync()` aufrufen, um eine Nachricht zu erstellen und zu senden, die den zu sprechenden Text sowie den anzuzeigenden Text und andere Optionen angibt. Im folgenden Codebeispiel wird eine Nachricht erstellt, die den anzuzeigenden Text und den zu sprechenden Text angibt.

[!code-csharp[Call SayAsync()](../includes/code/dotnet-text-to-speech.cs#Speak2)]

### <a id="prompt-options"></a> Eingabeaufforderungsoptionen

Wenn Sie eine der integrierten Eingabeaufforderungen verwenden, können Sie die Optionen `speak` und `retrySpeak` festlegen, um den Text anzugeben, der von Ihrem Bot gesprochen werden soll. Im folgenden Codebeispiel wird eine Eingabeaufforderung erstellt, die den anzuzeigenden Text, den zuerst zu sprechenden Text und den Text angibt, der nach einer Weile des Wartens auf Benutzereingaben gesprochen werden soll. Dabei wird die [SSML](#ssml)-Formatierung verwendet, um anzugeben, dass das Wort „sicher“ mit mäßiger Betonung gesprochen werden soll.

[!code-csharp[Set Prompt options](../includes/code/dotnet-text-to-speech.cs#Speak3)]

## <a id="ssml"></a> Speech Synthesis Markup Language (SSML)

Um den von Ihrem Bot zu sprechenden Text anzugeben, können Sie entweder eine Nur-Text-Zeichenfolge oder eine als Speech Synthesis Markup Language (SSML) formatierte Zeichenfolge verwenden. Bei SSML handelt es sich um eine XML-basierte Markupsprache, mit der Sie verschiedene Eigenschaften der Sprache Ihres Bots steuern können, wie z. B. Stimme, Geschwindigkeit, Lautstärke, Aussprache, Tonhöhe und mehr. Ausführliche Informationen zu SSML finden Sie unter <a href="https://msdn.microsoft.com/en-us/library/hh378377(v=office.14).aspx" target="_blank">Referenz zu Speech Synthesis Markup Language</a>.

## <a name="input-hints"></a>Eingabehinweise

Wenn Sie eine Nachricht in einem sprachaktivierten Kanal senden, können Sie versuchen, den Status des Mikrofons des Clients zu beeinflussen, indem Sie auch einen Eingabehinweis einschließen, um anzugeben, ob Ihr Bot Benutzereingaben akzeptiert, erwartet oder ignoriert. Weitere Informationen finden Sie unter [Hinzufügen von Eingabehinweisen zu Nachrichten](bot-builder-dotnet-add-input-hints.md).

## <a name="sample-code"></a>Beispielcode 

Ein vollständiges Beispiel, das die Erstellung eines sprachfähigen Bots mit dem Bot Builder SDK für .NET veranschaulicht, finden Sie in GitHub im <a href="https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/demo-RollerSkill" target="_blank">RollerSkill-Beispiel</a>.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Erstellen von Nachrichten](bot-builder-dotnet-create-messages.md)
- [Hinzufügen von Eingabehinweisen zu Nachrichten](bot-builder-dotnet-add-input-hints.md)
- <a href="https://msdn.microsoft.com/en-us/library/hh378377(v=office.14).aspx" target="_blank">Speech Synthesis Markup Language (SSML)</a>
- <a href="https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/demo-RollerSkill" target="_blank">RollerSkill-Beispiel (GitHub)</a>
- <a href="https://docs.botframework.com/en-us/csharp/builder/sdkreference/dc/d2f/class_microsoft_1_1_bot_1_1_connector_1_1_activity.html" target="_blank">Activity-Klasse</a>
- <a href="/dotnet/api/microsoft.bot.connector.imessageactivity" target="_blank">IMessageActivity-Schnittstelle</a>
- <a href="/dotnet/api/microsoft.bot.builder.dialogs.internals.dialogcontext" target="_blank">DialogContext-Klasse</a>
- <a href="/dotnet/api/microsoft.bot.builder.dialogs.internals.prompt-2" target="_blank">Prompt-Klasse</a>

[IMessageActivity]: /dotnet/api/microsoft.bot.connector.imessageactivity

