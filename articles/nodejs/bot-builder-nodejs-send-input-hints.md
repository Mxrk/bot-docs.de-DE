---
title: Hinzufügen von Eingabehinweisen zu Nachrichten | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie mithilfe des Bot Builder SDK für .NET Eingabehinweise zu Nachrichten hinzufügen.
author: v-ducvo
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 8c09ab3c0f863171697bc8026155003274bfc382
ms.sourcegitcommit: b78fe3d8dd604c4f7233740658a229e85b8535dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2018
ms.locfileid: "49999097"
---
# <a name="add-input-hints-to-messages"></a>Hinzufügen von Eingabehinweisen zu Nachrichten

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

> [!div class="op_single_selector"]
> - [.NET](../dotnet/bot-builder-dotnet-add-input-hints.md)
> - [Node.js](../nodejs/bot-builder-nodejs-send-input-hints.md)
> - [REST](../rest-api/bot-framework-rest-connector-add-input-hints.md)

Durch die Angabe eines Eingabehinweises für eine Nachricht können Sie angeben, ob Ihr Bot Benutzereingaben akzeptiert, erwartet oder ignoriert, nachdem die Nachricht an den Client gesendet wurde. Bei vielen Kanälen können Clients dadurch den Zustand von Benutzereingabe-Steuerelementen entsprechend festlegen. Wenn beispielsweise der Eingabehinweis für eine Nachricht angibt, dass der Bot die Benutzereingabe ignoriert, kann der Client das Mikrofon schließen und das Eingabefeld deaktivieren, um die Eingabe durch den Benutzer zu verhindern.

## <a name="accepting-input"></a>Eingabe wird akzeptiert

Um anzugeben, dass Ihr Bot passiv für eine Eingabe bereit ist, aber keine Antwort vom Benutzer erwartet, legen Sie den Eingabehinweis für die Nachricht auf `builder.InputHint.acceptingInput` fest. Bei vielen Kanälen wird dadurch das Eingabefeld des Clients aktiviert und das Mikrofon geschlossen, das aber weiterhin für den Benutzer verfügbar ist. Cortana öffnet beispielsweise das Mikrofon, um Eingaben vom Benutzer anzunehmen, wenn der Benutzer die Mikrofontaste gedrückt hält. Im folgenden Codebeispiel wird eine Nachricht erstellt, die darauf hinweist, dass der Bot Benutzereingaben akzeptiert.

[!code-javascript[IMessage.speak](../includes/code/node-input-hints.js#InputHintAcceptingInput)]

## <a name="expecting-input"></a>Eingabe wird erwartet

Um anzugeben, dass Ihr Bot eine Antwort vom Benutzer erwartet, legen Sie den Eingabehinweis für die Nachricht auf `builder.InputHint.expectingInput` fest. Bei vielen Kanälen wird dadurch das Eingabefeld des Clients aktiviert und das Mikrofon geöffnet. Mit dem folgenden Codebeispiel erstellen Sie eine Aufforderung, die darauf hinweist, dass der Bot eine Benutzereingabe erwartet.

[!code-javascript[Prompt](../includes/code/node-input-hints.js#InputHintExpectingInput)]

## <a name="ignoring-input"></a>Eingabe wird ignoriert

Um anzugeben, dass Ihr Bot nicht bereit ist, eine Eingabe vom Benutzer zu empfangen, legen Sie den Eingabehinweis für die Nachricht auf `builder.InputHint.ignoringInput` fest. Bei vielen Kanälen wird dadurch das Eingabefeld des Clients deaktiviert und das Mikrofon geschlossen. Bei dem folgenden Codebeispiel wird mit der `session.say()`-Methode eine Nachricht gesendet, die darauf hinweist, dass der Bot Benutzereingaben ignoriert.

[!code-javascript[Session.say()](../includes/code/node-input-hints.js#InputHintIgnoringInput)]

## <a name="default-values-for-input-hint"></a>Standardwerte für den Eingabehinweis

Wenn Sie den Eingabehinweis für eine Nachricht nicht festlegen, legt ihn das Bot Builder SDK automatisch anhand der folgenden Logik fest: 

- Wenn Ihr Bot eine Eingabeaufforderung sendet, gibt der Eingabehinweis für die Nachricht an, dass Ihr Bot eine **Eingabe erwartet**.</li>
- Wenn Ihr Bot eine einzelne Nachricht sendet, gibt der Eingabehinweis für die Nachricht an, dass Ihr Bot eine **Eingabe akzeptiert**.</li>
- Wenn Ihr Bot eine Reihe aufeinanderfolgender Nachrichten sendet, gibt der Eingabehinweis bei allen Nachrichten (mit Ausnahme der letzten Nachricht) an, dass Ihr Bot die **Eingabe ignoriert**. Der Eingabehinweis für die letzte Nachricht in der Reihe gibt jedoch an, dass der Bot eine **Eingabe akzeptiert**.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Hinzufügen von Sprache zu Nachrichten](bot-builder-nodejs-text-to-speech.md)
- [Bot Builder SDK für Node.js – Referenz][SDKReference]

[SDKReference]: https://docs.botframework.com/en-us/node/builder/chat-reference/modules/_botbuilder_d_.html

