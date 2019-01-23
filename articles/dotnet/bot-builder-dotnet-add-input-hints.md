---
title: Hinzufügen von Eingabehinweisen zu Nachrichten | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie mithilfe des Bot Framework SDK für .NET Eingabehinweise zu Nachrichten hinzufügen.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: b9b210ad215e091801456750237978babd029696
ms.sourcegitcommit: b15cf37afc4f57d13ca6636d4227433809562f8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/11/2019
ms.locfileid: "54224645"
---
# <a name="add-input-hints-to-messages"></a>Hinzufügen von Eingabehinweisen zu Nachrichten

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

> [!div class="op_single_selector"]
> - [.NET](../dotnet/bot-builder-dotnet-add-input-hints.md)
> - [Node.js](../nodejs/bot-builder-nodejs-send-input-hints.md)
> - [REST](../rest-api/bot-framework-rest-connector-add-input-hints.md)

Durch die Angabe eines Eingabehinweises für eine Nachricht können Sie angeben, ob Ihr Bot Benutzereingaben akzeptiert, erwartet oder ignoriert, nachdem die Nachricht an den Client gesendet wurde. Bei vielen Kanälen können Clients dadurch den Zustand von Benutzereingabe-Steuerelementen entsprechend festlegen. Wenn beispielsweise der Eingabehinweis für eine Nachricht angibt, dass der Bot die Benutzereingabe ignoriert, kann der Client das Mikrofon schließen und das Eingabefeld deaktivieren, um die Eingabe durch den Benutzer zu verhindern.

## <a name="accepting-input"></a>Eingabe wird akzeptiert

Um anzugeben, dass Ihr Bot passiv für eine Eingabe bereit ist, aber keine Antwort vom Benutzer erwartet, legen Sie den Eingabehinweis für die Nachricht auf `InputHints.AcceptingInput` fest. Bei vielen Kanälen wird dadurch das Eingabefeld des Clients aktiviert und das Mikrofon geschlossen, das aber weiterhin für den Benutzer verfügbar ist. Cortana öffnet beispielsweise das Mikrofon, um Eingaben vom Benutzer anzunehmen, wenn der Benutzer die Mikrofontaste gedrückt hält. Im folgenden Codebeispiel wird eine Nachricht erstellt, die darauf hinweist, dass der Bot Benutzereingaben akzeptiert.

```cs
Activity reply = activity.CreateReply("This is the text that will be displayed.");
reply.Speak = "This is the text that will be spoken.";
reply.InputHint = InputHints.AcceptingInput;
await connector.Conversations.ReplyToActivityAsync(reply);
```

## <a name="expecting-input"></a>Eingabe wird erwartet

Um anzugeben, dass Ihr Bot eine Antwort vom Benutzer erwartet, legen Sie den Eingabehinweis für die Nachricht auf `InputHints.ExpectingInput` fest. Bei vielen Kanälen wird dadurch das Eingabefeld des Clients aktiviert und das Mikrofon geöffnet. Im folgenden Codebeispiel wird eine Nachricht erstellt, die angibt, dass der Bot eine Benutzereingabe erwartet.

```cs
Activity reply = activity.CreateReply("This is the text that will be displayed.");
reply.Speak = "This is the text that will be spoken.";
reply.InputHint = InputHints.ExpectingInput;
await connector.Conversations.ReplyToActivityAsync(reply);
```

## <a name="ignoring-input"></a>Eingabe wird ignoriert

Um anzugeben, dass Ihr Bot nicht bereit ist, eine Eingabe vom Benutzer zu empfangen, legen Sie den Eingabehinweis für die Nachricht auf `InputHints.IgnorningInput` fest. Bei vielen Kanälen wird dadurch das Eingabefeld des Clients deaktiviert und das Mikrofon geschlossen. Im folgenden Codebeispiel wird eine Nachricht erstellt, die angibt, dass der Bot Benutzereingaben ignoriert.

```cs
Activity reply = activity.CreateReply("This is the text that will be displayed.");
reply.Speak = "This is the text that will be spoken.";
reply.InputHint = InputHints.IgnoringInput;
await connector.Conversations.ReplyToActivityAsync(reply);
```

## <a name="default-values-for-input-hint"></a>Standardwerte für den Eingabehinweis

Wenn Sie den Eingabehinweis für eine Nachricht nicht festlegen, legt ihn das Bot Framework SDK automatisch anhand der folgenden Logik fest:

- Wenn Ihr Bot eine Eingabeaufforderung sendet, gibt der Eingabehinweis für die Nachricht an, dass Ihr Bot eine **Eingabe erwartet**.</li>
- Wenn Ihr Bot eine einzelne Nachricht sendet, gibt der Eingabehinweis für die Nachricht an, dass Ihr Bot eine **Eingabe akzeptiert**.</li>
- Wenn Ihr Bot eine Reihe aufeinanderfolgender Nachrichten sendet, gibt der Eingabehinweis bei allen Nachrichten (mit Ausnahme der letzten Nachricht) an, dass Ihr Bot die **Eingabe ignoriert**. Der Eingabehinweis für die letzte Nachricht in der Reihe gibt jedoch an, dass der Bot eine **Eingabe akzeptiert**.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Erstellen von Nachrichten](bot-builder-dotnet-create-messages.md)
- [Hinzufügen von Sprache zu Nachrichten](bot-builder-dotnet-text-to-speech.md)
- <a href="https://docs.botframework.com/en-us/csharp/builder/sdkreference/dc/d2f/class_microsoft_1_1_bot_1_1_connector_1_1_activity.html" target="_blank">Activity-Klasse</a>
- <a href="/dotnet/api/microsoft.bot.connector.inputhints" target="_blank">InputHints-Klasse</a>
