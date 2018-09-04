---
title: Verwalten eines einfachen Konversationsflusses mit Dialogen | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie einen einfachen Konversationsfluss mit Dialogen im Bot Builder SDK für Node.js verwalten.
keywords: einfacher Konversationsfluss, Dialoge, Eingabeaufforderungen, Wasserfälle, Dialogsatz
author: v-ducvo
ms.author: v-ducvo
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 8/2/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 77162601f542e6faa8908bc71abc971eb99fcc93
ms.sourcegitcommit: 1abc32353c20acd103e0383121db21b705e5eec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/21/2018
ms.locfileid: "42756387"
---
# <a name="manage-simple-conversation-flow-with-dialogs"></a>Verwalten eines einfachen Konversationsflusses mit Dialogen

[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

Mit der Dialogbibliothek können Sie sowohl einfache als auch komplexe Konversationsabläufe verwalten. Bei einfachen Konversationsflüssen beginnt der Benutzer mit dem ersten Schritt eines *Wasserfalls* und durchläuft alle weiteren Schritte bis zum letzten Schritt, wo die Konversation dann endet. Dialoge können auch für [komplexe Konversationsflüsse](~/v4sdk/bot-builder-dialog-manage-complex-conversation-flow.md) eingesetzt werden, bei denen sich Teile des Dialogs verzweigen und Schleifen bilden können.

<!-- TODO: We need a dialogs conceptual topic to link to, so we can reference that here, in place of describing what they are and what their features are in a how-to topic. -->

<!-- TODO: This paragraph belongs in a conceptual topic. --> Ein Dialog ist wie eine Funktion in einem Programm. Er dient in der Regel dazu, einen spezifischen Vorgang in einer bestimmten Reihenfolge auszuführen, und kann so oft aufgerufen werden, wie er benötigt wird. Durch die Verwendung von Dialogen kann der Bot-Entwickler den Konversationsfluss steuern. Sie können mehrere Dialoge verketten, um jeden Unterhaltungsablauf zu behandeln, den Ihr Bot abwickeln soll. Die **Dialogbibliothek** im Bot Builder SDK enthält integrierte Funktionen wie _Eingabeaufforderungen_ und _Wasserfalldialoge_, um Ihnen die Verwaltung des Konversationsflusses zu erleichtern. Mit Eingabeaufforderungen können Sie Benutzer zur Eingabe verschiedener Arten von Informationen auffordern. Sie können ein Wasserfall verwenden, um mehrere Schritte in einer Sequenz zu kombinieren.

In diesem Artikel verwenden wir _Dialogsätze_, um einen Konversationsfluss zu erstellen, der sowohl Eingabeaufforderungen als auch Wasserfallschritte enthält. Wir haben zwei Beispieldialoge. Der erste ist ein Dialog mit einem Schritt, der einen Vorgang ausführt, der keine Benutzereingaben erfordert. Der zweite ist ein Dialog mit mehreren Schritten, in dem der Benutzer zur Eingabe einiger Informationen aufgefordert wird.

## <a name="install-the-dialogs-library"></a>Installieren der Dialogebibliothek

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Wir beginnen mit einer einfachen EchoBot-Vorlage. Entsprechende Anweisungen finden Sie in der [Schnellstartanleitung für .NET](~/dotnet/bot-builder-dotnet-quickstart.md).

Um Dialoge zu verwenden, installieren Sie das `Microsoft.Bot.Builder.Dialogs`NuGet-Paket für Ihr Projekt oder Ihre Projektmappe.
Verweisen Sie anschließend in using-Anweisungen Ihrer Codedateien auf die Dialogbibliothek.

```csharp
using Microsoft.Bot.Builder.Dialogs;
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Die `botbuilder-dialogs`-Bibliothek kann von NPM heruntergeladen werden. Um die `botbuilder-dialogs`-Bibliothek zu installieren, führen Sie den folgenden NPM-Befehl aus:

```cmd
npm install --save botbuilder-dialogs@preview
```

Um **Dialoge** in Ihrem Bot zu verwenden, fügen Sie in Ihrer Datei **app.js** Folgendes hinzu.

```javascript
const botbuilder_dialogs = require('botbuilder-dialogs');
```

---

## <a name="create-a-dialog-stack"></a>Erstellen eines Dialogstapels

In diesem ersten Beispiel erstellen wir einen Dialog mit einem Schritt, der zwei Zahlen addieren und das Ergebnis anzeigen kann.

Um Dialoge zu verwenden, müssen Sie zuerst einen *Dialogsatz* erstellen.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Die `Microsoft.Bot.Builder.Dialogs`-Bibliothek bietet eine `DialogSet`-Klasse.
Erstellen Sie eine Klasse namens **AdditionDialog**, und fügen Sie anschließend die erforderlichen using-Anweisungen hinzu.
Sie können benannte Dialoge und Sätze von Dialogen einem Dialogsatz hinzufügen und dann später anhand des Namens auf sie zugreifen.

```csharp
using Microsoft.Bot.Builder.Dialogs;
```

Leiten Sie die Klasse von **DialogSet** ab, und definieren Sie die IDs und Schlüssel zur Identifizierung der Dialoge und Eingabeinformationen für diesen Dialogsatz.

```csharp
/// <summary>Defines a simple dialog for adding two numbers together.</summary>
public class AdditionDialog : DialogSet
{
    /// <summary>The ID of the main dialog in the set.</summary>
    public const string Main = "additionDialog";

    /// <summary>Defines the IDs of the input arguments.</summary>
    public struct Inputs
    {
        public const string First = "first";
        public const string Second = "second";
    }
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Die `botbuilder-dialogs`-Bibliothek bietet eine `DialogSet`-Klasse.
Die **DialogSet**-Klasse definiert einen **Dialogstapel** und bietet Ihnen eine einfache Benutzeroberfläche zum Verwalten des Stapels.
Das Bot Builder SDK implementiert den Stapel als Array.

So erstellen Sie einen **Dialogsatz**:

```javascript
const dialogs = new botbuilder_dialogs.DialogSet();
```

Der obige Aufruf erstellt einen **Dialogsatz** mit einem standardmäßigen **Dialogstapel** mit dem Namen `dialogStack`.
Wenn Sie Ihren Stapel benennen möchten, können Sie ihn als Parameter an **DialogSet()** übergeben. Beispiel: 

```javascript
const dialogs = new botbuilder_dialogs.DialogSet("myStack");
```

---

Beim Erstellen eines Dialogs wird nur die Dialogdefinition dem Satz hinzugefügt. Der Dialog wird erst dann ausgeführt, wenn er durch den Aufruf einer _begin_- oder _replace_-Methode auf den Dialogstapel geschoben wird.

Der Name des Dialog (z.B. `addTwoNumbers`) muss innerhalb der einzelnen Dialogsätze eindeutig sein. Sie können in einem Satz beliebig viele Dialoge definieren. Wenn Sie mehrere Dialogsätze erstellen möchten, die nahtlos zusammenarbeiten, gehen Sie wie unter [Erstellen modularer Botlogik](bot-builder-compositcontrol.md) beschrieben vor.

Die Dialogebibliothek definiert die folgenden Dialoge:

* Einen **Eingabeaufforderungsdialog**, in dem mindestens zwei Funktionen genutzt werden – eine, um den Benutzer zur Eingabe aufzufordern, und die andere zum Verarbeiten der Eingabe. Sie können diese mithilfe des **Wasserfallmodells** verbinden.
* Ein **Wasserfalldialog** definiert eine Sequenz von _Wasserfallschritten_, die der Reihe nach ausgeführt werden. Ein Wasserfalldialog kann aus einem einzigen Schritt bestehen; in diesem Fall kann er als einfacher Ein-Schritt-Dialog betrachtet werden.

## <a name="create-a-single-step-dialog"></a>Erstellen eines Ein-Schritt-Dialogs

Ein-Schritt-Dialoge können zum Erfassen von Unterhaltungsflüssen mit einem Umlauf nützlich sein. In diesem Beispiel wird ein Bot erstellt, der erkennen kann, ob der Benutzer etwas wie „1 + 2“ sagt, und einen `addTwoNumbers`-Dialog startet, um mit „1 + 2 = 3“ zu antworten.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Werte werden `IDictionary<string,object>`-Eigenschaftenbehältern übergeben und von Dialogen ebenso zurückgegeben.

Verwenden Sie zum Erstellen eines einfachen Dialogs in einem Dialogsatz die `Add`-Methode. Im Folgenden wird ein Ein-Schritt-Wasserfall mit dem Namen `addTwoNumbers` hinzugefügt.

Dieser Schritt setzt voraus, dass die übergebenen Dialogargumente `first`- und `second`-Eigenschaften enthalten, die die hinzuzufügenden Zahlen darstellen.

Fügen Sie der **AdditionDialog**-Klasse den folgenden Konstruktor hinzu.

```csharp
/// <summary>Defines the steps of the dialog.</summary>
public AdditionDialog()
{
    Add(Main, new WaterfallStep[]
    {
        async (dc, args, next) =>
        {
            // Get the input from the arguments to the dialog and add them.
            var x =(double)args[Inputs.First];
            var y =(double)args[Inputs.Second];
            var sum = x + y;

            // Display the result to the user.
            await dc.Context.SendActivity($"{x} + {y} = {sum}");

            // End the dialog.
            await dc.End();
        }
    });
}
```

### <a name="pass-arguments-to-the-dialog"></a>Übergeben von Argumenten an den Dialog

Aktualisieren Sie in Ihrem Bot-Code die using-Anweisungen.

```cs
using Microsoft.Bot;
using Microsoft.Bot.Builder;
using Microsoft.Bot.Builder.Core.Extensions;
using Microsoft.Bot.Schema;
using System.Collections.Generic;
using System.Text.RegularExpressions;
using System.Threading.Tasks;
```

Fügen Sie der Klasse für den Additionsdialog eine statische Eigenschaft hinzu.

```cs
private static AdditionDialog AddTwoNumbers { get; } = new AdditionDialog();
```

Um den Dialog von der `OnTurn`-Methode Ihres Bots aus aufzurufen, ändern Sie `OnTurn` so, dass Folgendes enthalten ist:

```cs
public async Task OnTurn(ITurnContext context)
{
    // Handle any message activity from the user.
    if (context.Activity.Type is ActivityTypes.Message)
    {
        // Get the conversation state from the turn context.
        var conversationState = context.GetConversationState<ConversationData>();

        // Generate a dialog context for the addition dialog.
        var dc = AddTwoNumbers.CreateContext(context, conversationState.DialogState);

        // Call a helper function that identifies if the user says something
        // like "2 + 3" or "1.25 + 3.28" and extract the numbers to add.
        if (TryParseAddingTwoNumbers(context.Activity.Text, out double first, out double second))
        {
            // Start the dialog, passing in the numbers to add.
            var args = new Dictionary<string, object>
            {
                [AdditionDialog.Inputs.First] = first,
                [AdditionDialog.Inputs.Second] = second
            };
            await dc.Begin(AdditionDialog.Main, args);
        }
        else
        {
            // Echo back to the user whatever they typed.
            await context.SendActivity($"You said '{context.Activity.Text}'");
        }
    }
}
```

Fügen Sie der Bot-Klasse eine Hilfsfunktion **TryParseAddingTwoNumbers** hinzu. Die Hilfsfunktion verwendet nur einen einfachen regulären Ausdruck, um zu erkennen, ob die Benutzernachricht eine Anforderung zum Addieren von 2 Zahlen ist.

```cs
// Recognizes if the message is a request to add 2 numbers, in the form: number + number,
// where number may have optionally have a decimal point.: 1 + 1, 123.99 + 45, 0.4+7.
// For the sake of simplicity it doesn't handle negative numbers or numbers like 1,000 that contain a comma.
// If you need more robust number recognition, try System.Recognizers.Text
public static bool TryParseAddingTwoNumbers(string message, out double first, out double second)
{
    // captures a number with optional -/+ and optional decimal portion
    const string NUMBER_REGEXP = "([-+]?(?:[0-9]+(?:\\.[0-9]+)?|\\.[0-9]+))";

    // matches the plus sign with optional spaces before and after it
    const string PLUSSIGN_REGEXP = "(?:\\s*)\\+(?:\\s*)";

    const string ADD_TWO_NUMBERS_REGEXP = NUMBER_REGEXP + PLUSSIGN_REGEXP + NUMBER_REGEXP;

    var regex = new Regex(ADD_TWO_NUMBERS_REGEXP);
    var matches = regex.Matches(message);

    first = 0;
    second = 0;
    if (matches.Count > 0)
    {
        var matched = matches[0];
        if (double.TryParse(matched.Groups[1].Value, out first)
            && double.TryParse(matched.Groups[2].Value, out second))
        {
            return true;
        }
    }
    return false;
}
```

Wenn Sie die EchoBot-Vorlage verwenden, ändern Sie den Namen der **EchoState**-Klasse in **ConversationData**, und ändern Sie sie so, dass Folgendes enthalten ist.

```cs
using System.Collections.Generic;

/// <summary>
/// Class for storing conversation state.
/// </summary>
public class ConversationData
{
    /// <summary>Property for storing dialog state.</summary>
    public Dictionary<string, object> DialogState { get; set; } = new Dictionary<string, object>();
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Beginnen Sie mit der in [Erstellen eines Bots mit dem Bot Builder SDK v4 (Vorschau) für JavaScript](../javascript/bot-builder-javascript-quickstart.md) beschriebenen JS-Vorlage. Fügen Sie in **app.js** eine Anweisung hinzu, die `botbuilder-dialogs` erforderlich macht.

```js
const {DialogSet} = require('botbuilder-dialogs');
```

Fügen Sie in **app.js** den folgenden Code hinzu, der einen einfachen Dialog mit dem Namen `addTwoNumbers` definiert, der zum `dialogs`-Satz gehört:

```javascript
const dialogs = new DialogSet("myDialogStack");

// Show the sum of two numbers.
dialogs.add('addTwoNumbers', [async function (dc, numbers){
        var sum = Number.parseFloat(numbers[0]) + Number.parseFloat(numbers[1]);
        await dc.context.sendActivity(`${numbers[0]} + ${numbers[1]} = ${sum}`);
        await dc.end();
    }]
);
```

Ersetzen Sie den Code in **app.js** für die Verarbeitung der eingehenden Aktivität durch das Folgende. Der Bot ruft eine Hilfsfunktion auf, um festzustellen, ob die eingehende Nachricht wie eine Anforderung zum Addieren von zwei Zahlen aussieht. Wenn dies der Fall ist, übergibt er die Zahlen im Argument an `DialogContext.begin`.

```js
// Listen for incoming activity 
server.post('/api/messages', (req, res) => {
    // Route received activity to adapter for processing
    adapter.processActivity(req, res, async (context) => {
        const isMessage = context.activity.type === 'message';
        // State will store all of your information
        const convoState = conversationState.get(context);
        const dc = dialogs.createContext(context, convoState);

        if (isMessage) {
            // TryParseAddingTwoNumbers checks if the message matches a regular expression
            // and if it does, returns an array of the numbers to add
            var numbers = await TryParseAddingTwoNumbers(context.activity.text); 
            if (numbers != null && numbers.length >=2 )
            {
                await dc.begin('addTwoNumbers', numbers);
            }
            else {
                // Just echo back the user's message if they're not adding numbers
                const count = (convoState.count === undefined ? convoState.count = 0 : ++convoState.count);
                return context.sendActivity(`Turn ${count}: You said "${context.activity.text}"`);
            }
        }
        else {
            return context.sendActivity(`[${context.activity.type} event detected]`);
        }

        if (!context.responded) {
            await dc.continue();
            // if the dialog didn't send a response
            if (!context.responded && isMessage) {
                await dc.context.sendActivity(`Hi! I'm the add 2 numbers bot. Say something like "What's 2+3?"`);
            }
        }
    });
});

```

Fügen Sie die Hilfsfunktion **app.js** hinzu. Die Hilfsfunktion verwendet nur einen einfachen regulären Ausdruck, um zu erkennen, ob die Benutzernachricht eine Anforderung zum Addieren von 2 Zahlen ist. Bei Übereinstimmung mit dem regulären Ausdruck wird ein Array zurückgegeben, das die zu addierenden Zahlen enthält.

```javascript
async function TryParseAddingTwoNumbers(message) {
    const ADD_NUMBERS_REGEXP = /([-+]?(?:[0-9]+(?:\.[0-9]+)?|\.[0-9]+))(?:\s*)\+(?:\s*)([-+]?(?:[0-9]+(?:\.[0-9]+)?|\.[0-9]+))/i;
    let matched = ADD_NUMBERS_REGEXP.exec(message);
    if (!matched) {
        // message wasn't a request to add 2 numbers
        return null;
    }
    else {
        var numbers = [matched[1], matched[2]];
        return numbers;
    }
}
```

---

### <a name="run-the-bot"></a>Ausführen des Bots

Versuchen Sie, den Bot im Bot Framework-Emulator auszuführen, und sagen Sie etwas wie „What's 1+1?“ zu ihm.

![Ausführen des Bots](./media/how-to-dialogs/bot-output-add-numbers.png)

## <a name="using-dialogs-to-guide-the-user-through-steps"></a>Verwendung von Dialogen, um den Benutzer durch Schritte zu führen

Im nächsten Beispiel erstellen wir einen Dialog mit mehreren Schritten, um den Benutzer zur Eingabe von Informationen aufzufordern.

### <a name="create-a-dialog-with-waterfall-steps"></a>Erstellen eines Dialogs mit Wasserfallschritten

Ein **Wasserfall** ist eine spezifische Implementierung eines Dialogs, der am häufigsten dazu verwendet wird, Informationen vom Benutzer zu sammeln oder den Benutzer durch eine Reihe von Aufgaben zu leiten. Die Aufgaben werden als Array von Funktionen implementiert, bei dem das Ergebnis der ersten Funktion als Argument an die nächste Funktion übergeben wird usw. Jede Funktion stellt normalerweise einen Schritt im gesamten Prozess dar. Bei jedem Schritt [fordert der Bot den Benutzer zur Eingabe auf](bot-builder-prompts.md), wartet auf eine Antwort und übergibt das Ergebnis dann an den nächsten Schritt.

Das folgende Codebeispiel definiert beispielsweise drei Funktionen in einem Array, das die drei Schritte eines **Wasserfalls** darstellt. Nach jeder Eingabeaufforderung bestätigt der Bot die Eingabe des Benutzers, er speichert die Eingabe jedoch nicht. Wenn Sie Benutzereingaben beibehalten möchten, finden Sie unter [Speichern von Benutzerdaten](bot-builder-tutorial-persist-user-inputs.md) weitere Details.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Im Folgenden sehen Sie einen Konstruktor für einen Begrüßungsdialog, bei dem **GreetingDialog** von **DialogSet** abgeleitet wird, **Inputs.Text** die für das **TextPrompt**-Objekt verwendete ID enthält und **Main** die ID für den Begrüßungsdialog selbst enthält.

```csharp
public GreetingDialog()
{
    // Include a text prompt.
    Add(Inputs.Text, new TextPrompt());

    // Define the dialog logic for greeting the user.
    Add(Main, new WaterfallStep[]
    {
        async (dc, args, next) =>
        {
            // Ask for their name.
            await dc.Prompt(Inputs.Text, "What is your name?");
        },
        async (dc, args, next) =>
        {
            // Get the prompt result.
            var name = args["Text"] as string;

            // Acknowledge their input.
            await dc.Context.SendActivity($"Hi, {name}!");

            // Ask where they work.
            await dc.Prompt(Inputs.Text, "Where do you work?");
        },
        async (dc, args, next) =>
        {
            // Get the prompt result.
            var work = args["Text"] as string;

            // Acknowledge their input.
            await dc.Context.SendActivity($"{work} is a fun place.");

            // End the dialog.
            await dc.End();
        }
    });
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
// Greet user:
// Ask for the user name and then greet them by name.
// Ask them where they work.
dialogs.add('greetings',[
    async function (dc){
        await dc.prompt('textPrompt', 'What is your name?');
    },
    async function(dc, results){
        var userName = results;
        await dc.context.sendActivity(`Hi ${userName}!`);
        await dc.prompt('textPrompt', 'Where do you work?');
    },
    async function(dc, results){
        var workPlace = results;
        await dc.context.sendActivity(`${workPlace} is a fun place.`);
        await dc.end(); // Ends the dialog
    }
]);

dialogs.add('textPrompt', new botbuilder_dialogs.TextPrompt());
```

---

Die Signatur für einen **Wasserfallschritt** lautet wie folgt:

| Parameter | BESCHREIBUNG |
| :---- | :----- |
| `dc` | Der Dialogkontext. |
| `args` | Optional. Enthält die im Schritt übergebenen Argumente. |
| `next` | Optional. Eine Methode, die es Ihnen erlaubt, ohne Eingabeaufforderung mit dem nächsten Schritt des Wasserfalls fortzufahren. Sie können beim Aufrufen dieser Methode ein *args*-Argument angeben. Dies ermöglicht es Ihnen, Argumente an den nächsten Wasserfallschritt zu übergeben. |

Jeder Schritt muss vor der Rückgabe eine der folgenden Methoden aufrufen: *next()*-Delegat oder eine der Dialogkontextmethoden *begin*, *end*, *prompt* oder *replace*. Andernfalls bleibt der Bot in diesem Schritt hängen. Wird eine Funktion nicht mit einer dieser Methoden beendet, führen folglich alle Benutzereingaben zur erneuten Ausführung dieses Schritts, wenn der Benutzer eine Nachricht an den Bot sendet.

Wenn Sie das Ende des Wasserfalls erreicht haben, sollten Sie bei der Rückgabe die _end_-Methode aufrufen, damit der Dialog aus dem Stapel entfernt werden kann. Weitere Informationen finden Sie unten im Abschnitt [Beenden eines Dialogs](#end-a-dialog). Ebenso muss der Wasserfallschritt, damit das Fortfahren von einem Schritt zum nächsten möglich ist, entweder mit einer Eingabeaufforderung enden oder explizit den _next_-Delegat aufrufen, um den Wasserfall fortzusetzen.

## <a name="start-a-dialog"></a>Starten eines Dialogs

Um einen Dialog zu starten, übergeben Sie die zu startende *dialogId* an die Methode _begin_, _prompt_ oder _replace_ des Dialogkontexts. Die _begin_-Methode verschiebt den Dialog an den Anfang des Stapels, während die _replace_-Methode den aktuellen Dialog aus dem Stapel entfernt und den ersetzenden Dialog in den Stapel verschiebt.

So starten Sie einen Dialog ohne Argumente:

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
// Start the greetings dialog.
await dc.Begin("greetings");
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
// Start the 'greetings' dialog.
await dc.begin('greetings');
```

---

So starten Sie einen Dialog mit Argumenten:

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
// Start the greetings dialog, passing in a property bag.
await dc.Begin("greetings", args);
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
// Start the 'greetings' dialog with the 'userName' passed in.
await dc.begin('greetings', userName);
```

---

So starten Sie einen **Eingabeaufforderungsdialog**:

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Hier enthält **Inputs.Text** die ID einer **TextPrompt**, die im selben Dialogsatz enthalten ist.

```csharp
// Ask a user for their name.
await dc.Prompt(Inputs.Text, "What is your name?");
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
// Ask a user for their name.
await dc.prompt('textPrompt', "What is your name?");
```

---

Je nach Art der Aufforderung, die Sie starten, kann die Argumentsignatur der Eingabeaufforderung unterschiedlich sein. Die **DialogSet.prompt**-Methode ist eine Hilfsmethode. Diese Methode akzeptiert Argumente und konstruiert die entsprechenden Optionen für die Eingabeaufforderung; anschließend ruft sie die **begin**-Methode auf, um den Eingabeaufforderungsdialog zu starten. Weitere Informationen zu Eingabeaufforderungen finden Sie unter [Auffordern von Benutzern zur Eingabe](bot-builder-prompts.md).

## <a name="end-a-dialog"></a>Beenden eines Dialogs

Die _end_-Methode beendet einen Dialog, indem sie ihn aus dem Stapel entfernt und ein optionales Ergebnis an den übergeordneten Dialog zurückgibt.

Es ist eine bewährte Methode, die _end_-Methode explizit am Ende des Dialogs aufzurufen. Erforderlich ist dies jedoch nicht, da der Dialog automatisch aus dem Stapel entfernt wird, wenn Sie das Ende des Wasserfalls erreichen.

So beenden Sie einen Dialog:

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
// End the current dialog by popping it off the stack.
await dc.End();
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
// End the current dialog by popping it off the stack
await dc.end();
```

---

Um einen Dialog zu beenden und Informationen an den übergeordneten Dialog zurückzugeben, fügen Sie ein Eigenschaftbehälterargument hinzu.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
// End the current dialog and return information to the parent dialog.
await dc.end(new Dictionary<string, object>
    {
        ["property1"] = value1,
        ["property2"] = value2
    });
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
// End the current dialog and pass a result to the parent dialog
await dc.end({
    "property1": value1,
    "property2": value2
});
```

---

## <a name="clear-the-dialog-stack"></a>Löschen des Dialogstapels

Wenn Sie alle Dialoge aus dem Stapel entfernen möchten, können Sie den Dialogstapel durch Aufruf der _end all_-Methode löschen.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
// Pop all dialogs from the current stack.
await dc.EndAll();
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
// Pop all dialogs from the current stack.
await dc.endAll();
```

---

## <a name="repeat-a-dialog"></a>Wiederholen eines Dialogs

Verwenden Sie zum Wiederholen eines Dialogs die _replace_-Methode. Die *replace*-Methode des Dialogkontexts entfernt den aktuellen Dialog aus dem Stapel, verschiebt den ersetzenden Dialog an den Anfang des Stapels und startet diesen Dialog. Dies ist eine hervorragende Möglichkeit zum Behandeln [komplexer Konversationsflüsse](~/v4sdk/bot-builder-dialog-manage-complex-conversation-flow.md) und eine gute Technik zum Verwalten von Menüs.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
// End the current dialog and start the main menu dialog.
await dc.Replace("mainMenu");
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
// End the current dialog and start the 'mainMenu' dialog.
await dc.replace('mainMenu');
```

---

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun wissen, wie Sie einfache Konversationsflüsse verwalten, werden wir nun einen Blick darauf werfen, wie Sie die _replace_-Methode zum Behandeln komplexer Konversationsflüsse nutzen können.

> [!div class="nextstepaction"]
> [Verwalten komplexer Konversationsabläufe mit Dialogen](bot-builder-dialog-manage-complex-conversation-flow.md)
