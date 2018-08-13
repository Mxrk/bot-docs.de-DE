---
title: Hinzufügen von Eingabehinweisen zu Nachrichten | Microsoft Docs
description: Erfahren Sie, wie Sie mithilfe des Bot Builder SDK Eingabehinweise zu Nachrichten hinzufügen.
keywords: Eingabehinweise, Akzeptieren der Eingabe, Erwarten der Eingabe, Ignorieren der Eingabe, Sprache
author: ivorb
ms.author: v-ivorb
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 04/04/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: ac18b57b7c738e1f15776bfb87bfaf27c3b719d8
ms.sourcegitcommit: b45e16cac2febb7034da4ccd3af3bd7e6f430c31
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39469297"
---
# <a name="add-input-hints-to-messages"></a>Hinzufügen von Eingabehinweisen zu Nachrichten

[!INCLUDE [pre-release-label](~/includes/pre-release-label.md)]

Durch die Angabe eines Eingabehinweises für eine Nachricht können Sie angeben, ob Ihr Bot Benutzereingaben akzeptiert, erwartet oder ignoriert, nachdem die Nachricht an den Client gesendet wurde. Bei vielen Kanälen können Clients dadurch den Zustand von Benutzereingabe-Steuerelementen entsprechend festlegen. Wenn der Eingabehinweis für eine Nachricht beispielsweise angibt, dass der Bot die Benutzereingabe ignoriert, kann der Client das Mikrofon schließen und das Eingabefeld deaktivieren, um die Eingabe durch den Benutzer zu verhindern.

Achten Sie darauf, dass die erforderlichen Bibliotheken für Eingabehinweise enthalten sind.

# <a name="ctabcs"></a>[C#](#tab/cs)

```cs
using Microsoft.Bot.Schema;
```

<!--TODO: Remove the following remark after the next release of the NuGet packages.-->

Die **MessageFactory**-Klasse, die in diesen Beispielen verwendet wird, wird im folgenden Namespace definiert.

```cs
using Microsoft.Bot.Builder.Core.Extensions;
```

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)

```javascript
const {InputHints, MessageFactory} = require('botbuilder');
```

---

## <a name="accepting-input"></a>Eingabe wird akzeptiert

Um anzugeben, dass Ihr Bot passiv für eine Eingabe bereit ist, aber keine Antwort vom Benutzer erwartet, legen Sie den Eingabehinweis für die Nachricht auf _Eingabe wird akzeptiert_ fest. Bei vielen Kanälen wird dadurch das Eingabefeld des Clients aktiviert und das Mikrofon geschlossen, das aber weiterhin für den Benutzer verfügbar ist. Cortana öffnet beispielsweise das Mikrofon, um Eingaben vom Benutzer anzunehmen, wenn der Benutzer die Mikrofontaste gedrückt hält. Der folgende Code erstellt eine Nachricht, die angibt, dass der Bot Benutzereingaben akzeptiert.

# <a name="ctabcs"></a>[C#](#tab/cs)

```csharp
var reply = MessageFactory.Text(
    "This is the text that will be displayed.",
    "This is the text that will be spoken.",
    InputHints.AcceptingInput);
await context.SendActivity(reply).ConfigureAwait(false);
```

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)

```javascript
const basicMessage = MessageFactory.text('This is the text that will be displayed.', 'This is the text that will be spoken.', InputHints.AcceptingInput);
await context.sendActivity(basicMessage);
```

---

## <a name="expecting-input"></a>Eingabe wird erwartet

Um anzugeben, dass Ihr Bot eine Antwort vom Benutzer erwartet, legen Sie den Eingabehinweis für die Nachricht auf _Eingabe wird erwartet_ fest. Bei vielen Kanälen wird dadurch das Eingabefeld des Clients aktiviert und das Mikrofon geöffnet. Im folgenden Codebeispiel wird eine Nachricht erstellt, die angibt, dass der Bot eine Benutzereingabe erwartet.

# <a name="ctabcs"></a>[C#](#tab/cs)

```csharp
var reply = MessageFactory.Text(
    "This is the text that will be displayed.",
    "This is the text that will be spoken.",
    InputHints.ExpectingInput);
await context.SendActivity(reply).ConfigureAwait(false);
```

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)

```javascript
const basicMessage = MessageFactory.text('This is the text that will be displayed.', 'This is the text that will be spoken.', InputHints.ExpectingInput);
await context.sendActivity(basicMessage);
```

---

## <a name="ignoring-input"></a>Eingabe wird ignoriert

Um anzugeben, dass Ihr Bot nicht bereit ist, eine Eingabe vom Benutzer zu empfangen, legen Sie den Eingabehinweis für die Nachricht auf _Eingabe wird ignoriert_ fest. Bei vielen Kanälen wird dadurch das Eingabefeld des Clients deaktiviert und das Mikrofon geschlossen. Im folgenden Codebeispiel wird eine Nachricht erstellt, die angibt, dass der Bot Benutzereingaben ignoriert.

# <a name="ctabcs"></a>[C#](#tab/cs)

```csharp
var reply = MessageFactory.Text(
    "This is the text that will be displayed.",
    "This is the text that will be spoken.",
    InputHints.IgnoringInput);
await context.SendActivity(reply).ConfigureAwait(false);
```

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)

```javascript
const basicMessage = MessageFactory.text('This is the text that will be displayed.', 'This is the text that will be spoken.', InputHints.IgnoringInput);
await context.sendActivity(basicMessage);
```

---

## <a name="default-values-for-input-hint"></a>Standardwerte für den Eingabehinweis

Wenn Sie den Eingabehinweis für eine Nachricht nicht festlegen, legt ihn das Bot Builder SDK automatisch anhand der folgenden Logik fest:

- Wenn Ihr Bot eine Eingabeaufforderung sendet, gibt der Eingabehinweis für die Nachricht an, dass Ihr Bot eine **Eingabe erwartet**.</li>
- Wenn Ihr Bot eine einzelne Nachricht sendet, gibt der Eingabehinweis für die Nachricht an, dass Ihr Bot eine **Eingabe akzeptiert**.</li>
- Wenn Ihr Bot eine Reihe aufeinanderfolgender Nachrichten sendet, gibt der Eingabehinweis bei allen Nachrichten (mit Ausnahme der letzten Nachricht) an, dass Ihr Bot die **Eingabe ignoriert**. Der Eingabehinweis für die letzte Nachricht in der Reihe gibt jedoch an, dass der Bot eine **Eingabe akzeptiert**.
