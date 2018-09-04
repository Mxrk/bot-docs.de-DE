---
title: Auffordern von Benutzern zur Eingabe mithilfe der Dialogbibliothek | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Benutzer mithilfe der Dialogbibliothek im Bot Builder SDK für Node.js zur Eingabe auffordern.
keywords: Eingabeaufforderungen, Dialogfelder, AttachmentPrompt, ChoicePrompt, ConfirmPrompt, DatetimePrompt, NumberPrompt, TextPrompt, erneute Eingabeaufforderung, Überprüfung
author: v-ducvo
ms.author: v-ducvo
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 4/10/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 0b238ed510fd1d6fda82734af373f344b0dc28e3
ms.sourcegitcommit: 2dc75701b169d822c9499e393439161bc87639d2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/24/2018
ms.locfileid: "42905364"
---
# <a name="prompt-users-for-input-using-the-dialogs-library"></a>Auffordern von Benutzern zur Eingabe mithilfe der Dialogbibliothek

[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

Bots sammeln ihre Informationen häufig, indem sie dem Benutzer Fragen stellen. Sie können dem Benutzer einfach eine Standardnachricht senden, indem Sie ihn mithilfe der _send activity_-Methode des [Turn](bot-builder-concept-activity-processing.md#turn-context)-Kontextobjekts zur Eingabe einer Zeichenfolge auffordern. Das Bot Builder SDK enthält jedoch eine **Dialogbibliothek**, mit der Sie nach verschiedenen Typen von Informationen fragen können. In diesem Thema wird erläutert, wie ein Benutzer mit **Eingabeaufforderungen** um eine Eingabe gebeten wird.

In diesem Artikel wird beschrieben, wie Eingabeaufforderungen in einem Dialogfeld verwendet werden. Informationen zur Verwendung von Dialogen im Allgemeinen finden Sie im Thema zur [Verwendung von Dialogen zum Verwalten eines einfachen Konversationsflusses](bot-builder-dialog-manage-conversation-flow.md).

## <a name="prompt-types"></a>Eingabeaufforderungstypen

Die Dialogfeldbibliothek bietet eine Vielzahl verschiedener Arten von Eingabeaufforderungen, die eine jeweils andere Art von Antwort anfordern.

| Prompt | BESCHREIBUNG |
|:----|:----|
| **AttachmentPrompt** | Der Benutzer wird aufgefordert, eine Anlage anzugeben, z.B. ein Dokument oder ein Bild. |
| **ChoicePrompt** | Der Benutzer wird aufgefordert, aus einer Sammlung von Optionen auszuwählen. |
| **ConfirmPrompt** | Der Benutzer aufgefordert, seine Aktion zu bestätigen. |
| **DatetimePrompt** | Der Benutzer wird aufgefordert, eine Datums-/Uhrzeitangabe einzugeben. Benutzer können mithilfe von natürlicher Sprache antworten, z.B. „morgen um 20: 00 Uhr“ oder „Freitag um 10 Uhr“. Das Bot Framework SDK verwendet die vordefinierte LUIS `builtin.datetimeV2`-Entität. Weitere Informationen finden Sie unter [builtin.datetimev2](https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-prebuilt-entities#builtindatetimev2). |
| **NumberPrompt** | Der Benutzer wird aufgefordert, eine Zahl einzugeben. Der Benutzer kann z.B. mit „10“ oder „zehn“ antworten. Wenn die Antwort z.B. „zehn“ ist, konvertiert die Eingabeaufforderung die Antwort in eine Zahl und gibt `10` als Ergebnis zurück. |
| **TextPrompt** | Der Benutzer wird aufgefordert, eine Textzeichenfolge einzugeben. |

## <a name="add-references-to-prompt-library"></a>Hinzufügen von Verweisen zur Eingabeaufforderungsbibliothek

Sie können die **Dialogfelder**bibliothek abrufen, indem Sie Ihrem Bot das Paket **dialogs** hinzufügen. Dialoge werden im Thema zur [Verwendung von Dialogen zum Verwalten eines einfachen Konversationsflusses](bot-builder-dialog-manage-conversation-flow.md) ausführlich behandelt. Wir verwenden aber Dialoge für unsere Eingabeaufforderungen.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Installieren Sie das Paket **Microsoft.Bot.Builder.Dialogs** von NuGet.

Fügen Sie dann einen Verweis auf die Bibliothek aus Ihrem Botcode hinzu.

```cs
using Microsoft.Bot.Builder.Dialogs;
```

Sie können ein Dialogfeld als eine Klasse oder inline als Eigenschaft in Ihrer Botcodedatei definieren.

Der Code in diesem Artikel wurde für ein Dialogfeld geschrieben, das als eine Klasse definiert ist.
In den folgenden Beispielen wird davon ausgegangen, dass Sie Code zum Konstruktor des Dialogfelds hinzufügen.

Der Hauptfluss des Dialogfelds ist seine Sammlung von Schritten. Ihm muss eine ID zugewiesen werden. Ihr Bot verwendet diese ID, um das Dialogfeld abzurufen. Daher wird empfohlen, diese als eine Konstante bereitzustellen.

```cs
public class MyDialog : DialogSet
{
    public const string Name = "mainDialog";

    public MyDialog()
    {
        // Define your dialog's prompts and steps here.
    }
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Installieren Sie das Paket für die Dialogfelder von npm:

```cmd
npm install --save botbuilder-dialogs@preview
```

Um **Dialogfelder** in Ihrem Bot zu verwenden, schließen Sie es in den Botcode ein.

Fügen Sie Folgendes in der Datei „app.js“ hinzu.

```javascript
const {DialogSet} = require("botbuilder-dialogs");
const dialogs = new DialogSet();
```

---

## <a name="prompt-the-user"></a>Auffordern des Benutzers zu einer Eingabe

Um einen Benutzer zur Eingabe aufzufordern, können Sie Ihrem Dialogfeld eine Eingabeaufforderung hinzufügen. Sie können z.B. eine Aufforderung vom Typ **TextPrompt** definieren und dieser eine Dialogfeld-ID **TextPrompt** zuweisen:

Sobald ein Eingabeaufforderungs-Dialogfeld hinzugefügt wurde, können Sie dieses in einem einfachen zweistufigen Wasserfalldialogfeld oder mehrere Eingabeaufforderungen zusammen in einem mehrstufigen Wasserfall verwenden. Ein *Wasserfall*-Dialogfeld ist einfach eine Möglichkeit, eine Abfolge von Schritten zu definieren. Weitere Informationen finden Sie im Abschnitt zum [Verwenden von Dialogen](bot-builder-dialog-manage-conversation-flow.md#using-dialogs-to-guide-the-user-through-steps) unter [Verwalten eines einfachen Konversationsflusses mit Dialogen](bot-builder-dialog-manage-conversation-flow.md).

Im ersten Durchgang fordert das Dialogfeld den Benutzer zur Eingabe seines Namens auf, im zweiten Durchgang verarbeitet das Dialogfeld die Benutzereingaben als Antwort auf die Eingabeaufforderung.

Beispielsweise fordert das folgende Dialogfeld den Benutzer zur Eingabe seines Namens auf begrüßt ihn dann mit seinem Namen:

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Jede Eingabeaufforderung, die Sie in Ihrem Dialogfeld verwenden, erhält auch einen Namen, der vom Dialogfeld oder Ihrem Bot verwendet wird, um auf die Eingabeaufforderung zuzugreifen. In allen diesen Beispielen werden die Eingabeaufforderungs-IDs als Konstanten bereitgestellt.

Ein Aufruf der Methode **Prompt** oder **End** des Dialogfeldkontexts signalisiert dem Dialogfeld das Ende des Schritts. Das Dialogfeld wird ohne diese Anweisungen nicht ordnungsgemäß ausgeführt.

```csharp
/// <summary>Defines a simple greeting dialog that asks for the user's name.</summary>
public class MyDialog : DialogSet
{
    /// <summary>The ID of the main dialog in the set.</summary>
    public const string Name = "mainDialog";

    /// <summary>Defines the IDs of the prompts in the set.</summary>
    public struct Inputs
    {
        /// <summary>The ID of the text prompt.</summary>
        public const string Text = "textPrompt";
    }

    /// <summary>Defines the prompts and steps of the dialog.</summary>
    public MyDialog()
    {
        Add(Inputs.Text, new TextPrompt());
        Add(Name, new WaterfallStep[]
        {
            // Each step takes in a dialog context, arguments, and the next delegate.
            async (dc, args, next) =>
            {
                // Prompt for the user's name.
                await dc.Prompt(Inputs.Text, "What is your name?");
            },
            async(dc, args, next) =>
            {
                var user = (string)args["Text"];
                await dc.Context.SendActivity($"Hi {user}!");
                await dc.End();
            }
        });
    }
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
const {TextPrompt} = require("botbuilder-dialogs");
```

```javascript
// Greet user:
// Ask for the user name and then greet them by name.
dialogs.add('textPrompt', new TextPrompt());
dialogs.add('greetings', [
    async function (dc){
        await dc.prompt('textPrompt', 'What is your name?');
    },
    async function(dc, userName){
        await dc.context.sendActivity(`Hi ${userName}!`);
        await dc.end();
    }
]);
```

---

> [!NOTE]
> Um ein Dialogfeld zu starten, rufen Sie einen Dialogfeldkontext ab und verwenden dessen _begin_-Methode. Weitere Informationen finden Sie im Thema zur [Verwendung von Dialogen zum Verwalten eines einfachen Konversationsflusses](./bot-builder-dialog-manage-conversation-flow.md).

## <a name="reusable-prompts"></a>Wiederverwendbare Eingabeaufforderungen

Eine Eingabeaufforderung kann mit dem gleichen Typ von Eingabeaufforderung wiederverwendet werden, um unterschiedliche Informationen abzufragen. Im obigen Beispielcode wurde z.B. eine Texteingabeaufforderung definiert und verwendet, um den Benutzer zur Eingabe seines Namens aufzufordern. Wenn Sie möchten, können Sie z.B. die gleiche Eingabeaufforderung verwenden, um den Benutzer auch nach einer anderen Textzeichenfolge zu fragen, etwa „Wo arbeiten Sie?“.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```cs
/// <summary>Defines a simple greeting dialog that asks for the user's name and place of work.</summary>
public class MyDialog : DialogSet
{
    /// <summary>The ID of the main dialog in the set.</summary>
    public const string Name = "mainDialog";

    /// <summary>Defines the IDs of the prompts in the set.</summary>
    public struct Inputs
    {
        /// <summary>The ID of the text prompt.</summary>
        public const string Text = "textPrompt";
    }

    /// <summary>Defines the prompts and steps of the dialog.</summary>
    public MyDialog()
    {
        Add(Inputs.Text, new TextPrompt());
        Add(Name, new WaterfallStep[]
        {
            async (dc, args, next) =>
            {
                // Prompt for the user's name.
                await dc.Prompt(Inputs.Text, "What is your name?");
            },
            async(dc, args, next) =>
            {
                var user = (string)args["Text"];

                // Ask them where they work.
                await dc.Prompt(Inputs.Text, $"Hi {user}! Where do you work?");
            },
            async(dc, args, next) =>
            {
                var workplace = (string)args["Text"];

                await dc.Context.SendActivity($"{workplace} is a cool place!");
                await dc.End();
            }
        });
    }
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
// Greet user:
// Ask for the user name and then greet them by name.
// Ask them where they work.
dialogs.add('textPrompt', new botbuilder_dialogs.TextPrompt());
dialogs.add('greetings',[
    async function (dc){
        await dc.prompt('textPrompt', 'What is your name?');
    },
    async function(dc, userName){
        await dc.context.sendActivity(`Hi ${userName}!`);

        // Ask them where they work.
        await dc.prompt('textPrompt', 'Where do you work?');
    },
    async function(dc, workPlace){
        await dc.context.sendActivity(`${workPlace} is a cool place!`);

        await dc.end();
    }
]);
```

---

Wenn Sie die Eingabeaufforderung mit dem erwarteten Wert koppeln möchten, nach dem die Eingabeaufforderung fragt, können Sie jeder Eingabeaufforderung eine eindeutige *DialogId* zuweisen. Dann wird ein Dialogfeld mit einer eindeutigen ID hinzugefügt. Durch Verwenden unterschiedlicher IDs können Sie auch mehrere **Eingabeaufforderungs**-Dialogfelder des gleichen Typs erstellen. Beispielsweise könnten Sie zwei **TextPrompt**-Dialogfelder für das Beispiel oben erstellen:

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```cs
/// <summary>The ID of the main dialog in the set.</summary>
public const string Name = "mainDialog";

/// <summary>Defines the IDs of the prompts in the set.</summary>
public struct Inputs
{
    /// <summary>The ID of the name prompt.</summary>
    public const string Name = "namePrompt";

    /// <summary>The ID of the work prompt.</summary>
    public const string Work = "workPrompt";
}

/// <summary>Defines the prompts and steps of the dialog.</summary>
public MyDialog()
{
    Add(Inputs.Name, new TextPrompt());
    Add(Inputs.Work, new TextPrompt());
    Add(Name, new WaterfallStep[]
    {
        async (dc, args, next) =>
        {
            // Prompt for the user's name.
            await dc.Prompt(Inputs.Name, "What is your name?");
        },
        async(dc, args, next) =>
        {
            var user = (string)args["Text"];

            // Ask them where they work.
            await dc.Prompt(Inputs.Work, $"Hi {user}! Where do you work?");
        },
        async(dc, args, next) =>
        {
            var workplace = (string)args["Text"];

            await dc.Context.SendActivity($"{workplace} is a cool place!");
            await dc.End();
        }
    });
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
dialogs.add('namePrompt', new TextPrompt());
dialogs.add('workPlacePrompt', new TextPrompt());
```

---

Aus Gründen der Wiederverwendbarkeit von Code würde das Definieren einer einzelnen `textPrompt`-Eingabeaufforderung für alle diese Eingabeaufforderungen funktionieren, weil sie eine Textzeichenfolge als Antwort anfordern. Die Möglichkeit, Dialogfelder zu benennen, ist jedoch nützlich, wenn Sie die Eingabe der Eingabeaufforderung überprüfen müssen. In diesem Fall können die Eingabeaufforderungen **TextPrompt** verwenden, aber jede einzelne sucht nach einem anderen Satz von Werten. Werfen wir einen Blick darauf, wie Sie Antworten auf eine Eingabeaufforderung mit einer `NumberPrompt`-Eingabeaufforderung überprüfen können.

## <a name="specify-prompt-options"></a>Angeben von Eingabeaufforderungsoptionen

Wenn Sie eine Eingabeaufforderung in einem Dialogfeldschritt verwenden, können Sie auch Eingabeaufforderungsoptionen angeben, z.B. eine Zeichenfolge für eine erneute Eingabeaufforderung.

Die Angabe einer erneuten Eingabeaufforderung ist dann sinnvoll, wenn die Benutzereingabe eine Eingabeaufforderung nicht erfüllen kann, entweder weil sie in einem Format vorliegt, das die Eingabeaufforderung nicht analysieren kann, z.B. „morgen“ für eine Zahleneingabeaufforderung, oder weil die Eingabe ein Überprüfungskriterium nicht erfüllt.

> [!TIP]
> Wenn Sie eine Zahleneingabeaufforderung erstellen, müssen Sie die Eingabekultur angeben, die verwendet wird. Die Zahleneingabeaufforderung kann eine Vielzahl von Eingaben interpretieren, z.B. „12“ oder „ein Viertel“ sowie „12“ und „0,25“. Die Eingabekultur unterstützt die Eingabeaufforderung dabei, die Eingabe des Benutzers richtig interpretieren zu können.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Eingabekulturen werden in einer zusätzlichen Bibliothek definiert.

```csharp
using Microsoft.Bot.Builder.Dialogs;
using Microsoft.Recognizers.Text;
```

Der folgende Code würde einer vorhandenen Dialogfeldsammlung **dialogs** eine Zahleneingabeaufforderung hinzufügen.

```csharp
dialogs.Add("numberPrompt", new NumberPrompt<int>(Culture.English));
```

In einem Dialogfeldschritt würde der folgende Code den Benutzer zur Eingabe auffordern und eine erneute Eingabeaufforderung bereitstellen, die verwendet wird, wenn die Eingabe nicht als Zahl interpretiert werden kann.

```csharp
await dc.Prompt("numberPrompt", "How many people are in your party?", new PromptOptions()
{
    RetryPromptString = "Sorry, please specify the number of people in your party."
});
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
const {NumberPrompt} = require("botbuilder-dialogs");
```

```javascript
await dc.prompt('numberPrompt', 'How many people in your party?', { retryPrompt: `Sorry, please specify the number of people in your party.` })
```

```javascript
dialogs.add('numberPrompt', new NumberPrompt());
```

---

Insbesondere die Auswahleingabeaufforderung erfordert einige zusätzliche Informationen: die Liste der Optionen, die für den Benutzer verfügbar sind.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Dieses Beispiel verwendet Typen aus den folgenden Namespaces.

```csharp
using Microsoft.Bot.Builder.Core.Extensions;
using Microsoft.Bot.Builder.Dialogs;
using Microsoft.Bot.Builder.Prompts.Choices;
using Microsoft.Bot.Schema;
using Microsoft.Recognizers.Text;
using System.Collections.Generic;
```


Wenn wir die **ChoicePrompt**-Eingabeaufforderung verwenden, um den Benutzer zu bitten, zwischen mehreren Optionen auszuwählen, müssen wir die Eingabeaufforderung mit dieser Sammlung von Optionen versehen, die in einem **ChoicePromptOptions**-Objekt bereitgestellt werden. Hier verwenden wir die **ChoiceFactory**, um eine Liste mit Optionen in ein geeignetes Format zu konvertieren.

Wir verwenden zudem eine **SuggestedActions**-Aktivität als erneute Eingabeaufforderung und Möglichkeit, die Eingabeoptionen erneut für den Benutzer bereitzustellen.


```csharp
/// <summary>Defines a dialog that asks for a choice of color.</summary>
public class MyDialog : DialogSet
{
    /// <summary>The ID of the main dialog in the set.</summary>
    public const string Name = "mainDialog";

    /// <summary>Defines the IDs of the prompts in the set.</summary>
    public struct Inputs
    {
        /// <summary>The ID of the color prompt.</summary>
        public const string Color = "colorPrompt";
    }

    /// <summary>The available colors to choose from.</summary>
    public List<string> Colors = new List<string> { "Green", "Blue" };

    /// <summary>Defines the prompts and steps of the dialog.</summary>
    public MyDialog()
    {
        Add(Inputs.Color, new ChoicePrompt(Culture.English));
        Add(Name, new WaterfallStep[]
        {
            async (dc, args, next) =>
            {
                // Prompt for a color. A choice prompt requires that you specify choice options.
                await dc.Prompt(Inputs.Color, "Please make a choice.", new ChoicePromptOptions()
                {
                    Choices = ChoiceFactory.ToChoices(Colors),
                    RetryPromptActivity =
                        MessageFactory.SuggestedActions(Colors, "Please choose a color.") as Activity
                });
            },
            async(dc, args, next) =>
            {
                var color = (FoundChoice)args["Value"];

                await dc.Context.SendActivity($"You chose {color.Value}.");
                await dc.End();
            }
        });
    }
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
const {ChoicePrompt} = require("botbuilder-dialogs");
```

```javascript
dialogs.add('choicePrompt', new ChoicePrompt());
```

```javascript
// A choice prompt requires that you specify choice options.
const list = ['green', 'blue'];
await dc.prompt('choicePrompt', 'Please make a choice', list, {retryPrompt: 'Please choose a color.'});
```

---

## <a name="validate-a-prompt-response"></a>Überprüfen der Antwort auf eine Eingabeaufforderung

Sie können die Antwort auf eine Eingabeaufforderung vor der Rückgabe des gültigen Werts an den nächsten Schritt des **Wasserfalls** überprüfen. Um beispielsweise eine **NumberPrompt**-Eingabeaufforderung in einem Bereich von Zahlen zwischen **6** und **20** zu überprüfen, können Sie Überprüfungslogik verwenden, die dem folgenden Beispiel ähnelt:

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```cs
using Microsoft.Bot.Builder.Dialogs;
using Microsoft.Recognizers.Text;
using PromptStatus = Microsoft.Bot.Builder.Prompts.PromptStatus;
```

```cs
/// <summary>Defines a dialog that asks for the number of people in a party.</summary>
public class MyDialog : DialogSet
{
    /// <summary>The ID of the main dialog in the set.</summary>
    public const string Name = "mainDialog";

    /// <summary>Defines the IDs of the prompts in the set.</summary>
    public struct Inputs
    {
        /// <summary>The ID of the party size prompt.</summary>
        public const string Size = "parytySize";
    }

    /// <summary>Defines the prompts and steps of the dialog.</summary>
    public MyDialog()
    {
        // Include a validation function for the party size prompt.
        Add(Inputs.Size, new NumberPrompt<int>(Culture.English, async (context, result) =>
        {
            if (result.Value < 6 || result.Value > 20)
            {
                result.Status = PromptStatus.OutOfRange;
            }
        }));
        Add(Name, new WaterfallStep[]
        {
            async (dc, args, next) =>
            {
                // Prompt for the party size.
                await dc.Prompt(Inputs.Size, "How many people are in your party?", new PromptOptions()
                {
                    RetryPromptString = "Please specify party size between 6 and 20."
                });
            },
            async(dc, args, next) =>
            {
                var size = (int)args["Value"];

                await dc.Context.SendActivity($"Okay, {size} people!");
                await dc.End();
            }
        });
    }
}
```

Die Überprüfung kann auch in einer eigenen privaten Methode gekapselt und auf diese Weise hinzugefügt werden.

```cs
/// <summary>Validates input for the partySize prompt.</summary>
/// <param name="context">The context object for the current turn of the bot.</param>
/// <param name="result">The recognition result from the prompt.</param>
/// <returns>An updated recognition result.</returns>
private static async Task PartySizeValidator(ITurnContext context, Int32Result result)
{
    if (result.Value < 6 || result.Value > 20)
    {
        result.Status = PromptStatus.OutOfRange;
    }
}
```

Geben Sie in Ihrem Dialogfeld die Methode zum Überprüfen der Eingabe an.

```cs
// Include a validation function for the party size prompt.
Add(Inputs.Size, new NumberPrompt<int>(Culture.English, PartySizeValidator));
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
// Customized prompts with validations
// A number prompt with validation for valid party size within a range.
dialogs.add('partySizePrompt', new botbuilder_dialogs.NumberPrompt( async (context, value) => {
    try {
        if(value < 6 ){
            throw new Error('Party size too small.');
        }
        else if(value > 20){
            throw new Error('Party size too big.')
        }
        else {
            return value; // Return the valid value
        }
    } catch (err) {
        await context.sendActivity(`${err.message} <br/>Please provide a valid number between 6 and 20.`);
        return undefined;
    }
}));
```

---

Wenn Sie eine **DatetimePrompt**-Antwort für ein Datum und eine Uhrzeit in der Zukunft überprüfen möchten, können Sie eine ähnliche Überprüfungslogik verwenden:

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```cs
using Microsoft.Bot.Builder;
using Microsoft.Bot.Builder.Dialogs;
using Microsoft.Recognizers.Text;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using DateTimeResult = Microsoft.Bot.Builder.Prompts.DateTimeResult;
using PromptStatus = Microsoft.Bot.Builder.Prompts.PromptStatus;
```

```cs
/// <summary>Validates input for the reservationTime prompt.</summary>
/// <param name="context">The context object for the current turn of the bot.</param>
/// <param name="result">The recognition result from the prompt.</param>
/// <returns>An updated recognition result.</returns>
private static async Task TimeValidator(ITurnContext context, DateTimeResult result)
{
    if (result.Resolution.Count == 0)
    {
        await context.SendActivity("Sorry, I did not recognize the time that you entered.");
        result.Status = PromptStatus.NotRecognized;
    }

    // Find any recognized time that is not in the past.
    var now = DateTime.Now;
    DateTime time = default(DateTime);
    var resolution = result.Resolution.FirstOrDefault(
        res => DateTime.TryParse(res.Value, out time) && time > now);

    if (resolution != null)
    {
        // If found, keep only that result.
        result.Resolution.Clear();
        result.Resolution.Add(resolution);
    }
    else
    {
        // Otherwise, flag the input as out of range.
        await context.SendActivity("Please enter a time in the future, such as \"tomorrow at 9am\"");
        result.Status = PromptStatus.OutOfRange;
    }
}
```

```csharp
Add(Inputs.Time, new DateTimePrompt(Culture.English, TimeValidator));
```

Weitere Beispiele finden Sie in unserem [Beispielrepository](https://github.com/Microsoft/botbuilder-dotnet).

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```JavaScript
// A date and time prompt with validation for date/time in the future.
dialogs.add('dateTimePrompt', new botbuilder_dialogs.DatetimePrompt( async (context, values) => {
    try {
        if (values.length < 0) { throw new Error('missing time') }
        if (values[0].type !== 'datetime') { throw new Error('unsupported type') }
        const value = new Date(values[0].value);
        if (value.getTime() < new Date().getTime()) { throw new Error('in the past') }
        return value;
    } catch (err) {
        await context.sendActivity(`Please enter a valid time in the future like "tomorrow at 9am".`);
        return undefined;
    }
}));
```

Weitere Beispiele finden Sie in unserem [Beispielrepository](https://github.com/Microsoft/botbuilder-js).

---

> [!TIP]
> Eingabeaufforderungen für Datum und Uhrzeit können in unterschiedliche Datumsangaben aufgelöst werden, wenn der Benutzer eine mehrdeutige Antwort gibt. Je nachdem, wofür Sie diese Angaben verwenden, möchten Sie vielleicht alle Auflösungen überprüfen, die das Eingabeaufforderungsergebnis liefert, nicht nur die erste Auflösung.

Sie können ähnliche Verfahren verwenden, um Antworten auf Eingabeaufforderungen für beliebige Eingabeaufforderungstypen zu überprüfen.

## <a name="save-user-data"></a>Speichern von Benutzerdaten

Wenn Benutzer zu einer Eingabe aufgefordert werden, bestehen mehrere Optionen für die Verarbeitung dieser Eingabe. Beispielsweise können Sie die Eingabe nutzen und verwerfen, Sie können sie in einer globalen Variablen speichern, Sie können sie in einem flüchtigen oder speicherinternen Speichercontainer speichern, Sie können sie in einer Datei speichern, oder Sie können sie in einer externen Datenbank speichern. Weitere Informationen zum Speichern von Benutzerdaten finden Sie unter [Verwalten von Benutzerdaten](bot-builder-howto-v4-state.md).

## <a name="next-steps"></a>Nächste Schritte

Da Sie jetzt wissen, wie ein Benutzer zu einer Eingabe aufgefordert wird, können Sie den Botcode und die Benutzeroberfläche verbessern, indem Sie verschiedene Gesprächsflüsse über Dialogfelder verwalten.


