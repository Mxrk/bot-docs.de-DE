---
title: Verwalten eines Unterhaltungsflusses mit Dialogen | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie einen Unterhaltungsfluss mit Dialogen im Bot Builder SDK für Node.js verwalten.
keywords: Unterhaltungsfluss, Dialoge, Eingabeaufforderungen, Wasserfälle, Dialogsatz
author: v-ducvo
ms.author: v-ducvo
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 5/8/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 99184ba71072c159c598c7f68289c42a51926795
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39302205"
---
# <a name="manage-conversation-flow-with-dialogs"></a>Verwalten des Unterhaltungsflusses mit Dialogen
[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]


Das Verwalten des Unterhaltungsflusses ist eine wesentliche Aufgabe beim Erstellen von Bots. Mit dem Bot Builder SDK können Sie den Unterhaltungsfluss mit **Dialogen** verwalten.

Ein Dialog ist wie eine Funktion in einem Programm. Er dient in der Regel einem bestimmten Vorgang und kann so oft aufgerufen werden, wie er benötigt wird. Sie können mehrere Dialoge verketten, um jeden Unterhaltungsfluss zu behandeln, den Ihr Bot abwickeln soll. Die **Dialogebibliothek** im Bot Builder SDK enthält integrierte Funktionen wie z.B. **Eingabeaufforderungen** und **Wasserfälle**, um Ihnen das Verwalten des Unterhaltungsflusses über Dialoge zu erleichtern. Die Bibliothek der Eingabeaufforderungen enthält mehrere Eingabeaufforderungen, die Sie verwenden können, um Benutzer um verschiedene Arten von Informationen zu bitten. Der Wasserfälle bieten Ihnen eine Möglichkeit, mehrere Schritte in einer Sequenz zu kombinieren.

Dieser Artikel zeigt Ihnen, wie Sie ein Dialogeobjekt erstellen und Eingabeaufforderungen sowie Wasserfallschritte einem Dialogsatz hinzufügen, um sowohl einfache als auch komplexe Unterhaltungsflüsse zu verwalten. 

## <a name="install-the-dialogs-library"></a>Installieren der Dialogebibliothek

# <a name="ctabcsharp"></a>[C#](#tab/csharp)
Um Dialoge zu verwenden, installieren Sie das `Microsoft.Bot.Builder.Dialogs`NuGet-Paket für Ihr Projekt oder Ihre Projektmappe.
Dann verweisen Sie mit using-Anweisungen in Ihren Codedateien auf die Dialogebibliothek. Beispiel: 

```csharp
using Microsoft.Bot.Builder.Dialogs;
```

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)
Die `botbuilder-dialogs`-Bibliothek kann von NPM heruntergeladen werden. Um die `botbuilder-dialogs`-Bibliothek zu installieren, führen Sie den folgenden NPM-Befehl aus:

```cmd
npm install --save botbuilder-dialogs
```

Um **Dialoge** in Ihrem Bot zu verwenden, schließen Sie sie in den Botcode ein. Beispiel: 

**app.js**

```javascript
const botbuilder_dialogs = require('botbuilder-dialogs');
```
---

## <a name="create-a-dialog-stack"></a>Erstellen eines Dialogstapels

Um Dialoge zu verwenden, müssen Sie zuerst einen *Dialogsatz* erstellen.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Die `Microsoft.Bot.Builder.Dialogs`-Bibliothek bietet eine `DialogSet`-Klasse.
Einem Dialogsatz können Sie benannte Dialoge und Sätze von Dialogen hinzufügen und dann später mit dem Namen auf sie zugreifen.

```csharp
IDialog dialog = null;
// Initialize dialog.

DialogSet dialogs = new DialogSet();
dialogs.Add("dialog name", dialog);
```

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)

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

Der Name des Dialog (z.B. `addTwoNumbers`) muss innerhalb der einzelnen Dialogsätze eindeutig sein. Sie können in einem Satz beliebig viele Dialoge definieren.

Die Dialogebibliothek definiert die folgenden Dialoge:
-   Einen **Eingabeaufforderungsdialog**, in dem mindestens zwei Funktionen genutzt werden – eine, um den Benutzer zur Eingabe aufzufordern, und die andere zum Verarbeiten der Eingabe.
    Sie können diese mithilfe des **Wasserfallmodells** verbinden.
-   Ein **Wasserfalldialog** definiert eine Sequenz von _Wasserfallschritten_, die der Reihe nach ausgeführt werden.
    Ein Wasserfalldialog kann aus einem einzigen Schritt bestehen; in diesem Fall kann er als einfacher Ein-Schritt-Dialog betrachtet werden.

## <a name="create-a-single-step-dialog"></a>Erstellen eines Ein-Schritt-Dialogs

Ein-Schritt-Dialoge können zum Erfassen von Unterhaltungsflüssen mit einem Umlauf nützlich sein. In diesem Beispiel wird ein Bot erstellt, der erkennen kann, ob der Benutzer etwas wie „1 + 2“ sagt, und einen `addTwoNumbers`-Dialog startet, um mit „1 + 2 = 3“ zu antworten. 

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Werte werden `IDictionary<string,object>`-Eigenschaftenbehältern übergeben und von Dialogen ebenso zurückgegeben.

Verwenden Sie zum Erstellen eines einfachen Dialogs in einem Dialogsatz die `Add`-Methode. Im Folgenden wird ein Ein-Schritt-Wasserfall mit dem Namen `addTwoNumbers` hinzugefügt.

Dieser Schritt setzt voraus, dass die übergebenen Dialogargumente `first`- und `second`-Eigenschaften enthalten, die die hinzuzufügenden Zahlen darstellen.

Beginnen Sie mit der EchoBot-Vorlage. Fügen Sie anschließend Code in Ihre Botklasse ein, um den Dialog im Konstruktor hinzuzufügen.
```csharp
public class EchoBot : IBot
{
    private DialogSet _dialogs;

    public EchoBot()
    {
        _dialogs = new DialogSet();
        _dialogs.Add("addTwoNumbers", new WaterfallStep[]
        {              
            async (dc, args, next) =>
            {
                double sum = (double)args["first"] + (double)args["second"];
                await dc.Context.SendActivity($"{args["first"]} + {args["second"]} = {sum}");
                await dc.End();
            }
        });
    }

    // The rest of the class definition is omitted here but would include OnTurn()
}

```

### <a name="pass-arguments-to-the-dialog"></a>Übergeben von Argumenten an den Dialog

Um den Dialog von der `OnTurn`-Methode Ihres Bots aus aufzurufen, ändern Sie `OnTurn` so, dass Folgendes enthalten ist:
```cs
public async Task OnTurn(ITurnContext context)
{
    // This bot is only handling Messages
    if (context.Activity.Type == ActivityTypes.Message)
    {
        // Get the conversation state from the turn context
        var state = context.GetConversationState<EchoState>();

        // create a dialog context
        var dialogCtx = _dialogs.CreateContext(context, state);

        // Bump the turn count. 
        state.TurnCount++;

        await dialogCtx.Continue();
        if (!context.Responded)
        {
            // Call a helper function that identifies if the user says something 
            // like "2 + 3" or "1.25 + 3.28" and extract the numbers to add            
            if (TryParseAddingTwoNumbers(context.Activity.Text, out double first, out double second))
            { 
                var dialogArgs = new Dictionary<string, object>
                {
                    ["first"] = first,
                    ["second"] = second
                };                        
                await dialogCtx.Begin("addTwoNumbers", dialogArgs);
            }
            else
            {
                // Echo back to the user whatever they typed.
                await context.SendActivity($"Turn: {state.TurnCount}. You said '{context.Activity.Text}'");
            }
        }
    }
}
```

Fügen Sie der Botklasse die Hilfsfunktion hinzu. Die Hilfsfunktion verwendet nur einen einfachen regulären Ausdruck, um zu erkennen, ob die Benutzernachricht eine Anforderung zum Addieren von 2 Zahlen ist.

```cs
// Recognizes if the message is a request to add 2 numbers, in the form: number + number, 
// where number may have optionally have a decimal point.: 1 + 1, 123.99 + 45, 0.4+7. 
// For the sake of simplicity it doesn't handle negative numbers or numbers like 1,000 that contain a comma.
// If you need more robust number recognition, try System.Recognizers.Text
public bool TryParseAddingTwoNumbers(string message, out double first, out double second)
{
    // captures a number with optional -/+ and optional decimal portion
    const string NUMBER_REGEXP = "([-+]?(?:[0-9]+(?:\\.[0-9]+)?|\\.[0-9]+))";
    // matches the plus sign with optional spaces before and after it
    const string PLUSSIGN_REGEXP = "(?:\\s*)\\+(?:\\s*)";
    const string ADD_TWO_NUMBERS_REGEXP = NUMBER_REGEXP + PLUSSIGN_REGEXP + NUMBER_REGEXP;
    var regex = new Regex(ADD_TWO_NUMBERS_REGEXP);
    var matches = regex.Matches(message);
    var succeeded = false;
    first = 0;
    second = 0;
    if (matches.Count == 0)
    {
        succeeded = false;
    }
    else
    {
        var matched = matches[0];
        if ( System.Double.TryParse(matched.Groups[1].Value, out first) 
            && System.Double.TryParse(matched.Groups[2].Value, out second))
        {
            succeeded = true;
        } 
    }
    return succeeded;
}
```

Wenn Sie die EchoBot-Vorlage verwenden, ändern Sie die `EchoState`-Klasse in **EchoState.cs** wie folgt:

```cs
/// <summary>
/// Class for storing conversation state.
/// This bot only stores the turn count in order to echo it to the user
/// </summary>
public class EchoState: Dictionary<string, object>
{
    private const string TurnCountKey = "TurnCount";
    public EchoState()
    {
        this[TurnCountKey] = 0;            
    }

    public int TurnCount
    {
        get { return (int)this[TurnCountKey]; }
        set { this[TurnCountKey] = value; }
    }
}
```

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)

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
        if (isMessage) {
            const state = conversationState.get(context);
            const count = state.count === undefined ? state.count = 0 : ++state.count;

            // create a dialog context
            const dc = dialogs.createContext(context, state);

            // MatchesAdd2Numbers checks if the message matches a regular expression
            // and if it does, returns an array of the numbers to add
            var numbers = await MatchesAdd2Numbers(context.activity.text); 
            if (numbers != null && numbers.length >=2 )
            {    
                await dc.begin('addTwoNumbers', numbers);
            }
            else {
                // Just echo back the user's message if they're not adding numbers
                return context.sendActivity(`Turn ${count}: You said "${context.activity.text}"`); 
            }           
        } else {
            return context.sendActivity(`[${context.activity.type} event detected]`);
        }
        if (!context.responded) {
            await dc.continue();
            // if the dialog didn't send a response
            if (!context.responded && isMessage) {
                await dc.context.sendActivity(`Hi! I'm the add 2 numbers bot. Say something like "what's 1+2?"`);
            }
        }
    });
});
```

Fügen Sie die Hilfsfunktion **app.js** hinzu. Die Hilfsfunktion verwendet nur einen einfachen regulären Ausdruck, um zu erkennen, ob die Benutzernachricht eine Anforderung zum Addieren von 2 Zahlen ist. Bei Übereinstimmung mit dem regulären Ausdruck wird ein Array zurückgegeben, das die zu addierenden Zahlen enthält.

```javascript
async function MatchesAdd2Numbers(message) {
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

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

### <a name="create-a-composite-dialog"></a>Erstellen eines zusammengesetzten Dialogs

Die folgenden Codeausschnitte stammen aus dem [Microsoft.Bot.Samples.Dialog.Prompts](https://github.com/Microsoft/botbuilder-dotnet/tree/master/samples/MIcrosoft.Bot.Samples.Dialog.Prompts)-Beispielcode im botbuilder-dotnet-Repository.

In „Startup.cs“:
1.  Benennen Sie Ihren Bot in `DialogContainerBot` um.
1.  Verwenden Sie ein einfaches Wörterbuch als Eigenschaftenbehälter für den Konversationsstatus für den Bot.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddBot<DialogContainerBot>(options =>
    {
        options.CredentialProvider = new ConfigurationCredentialProvider(Configuration);
        options.Middleware.Add(new ConversationState<Dictionary<string, object>>(new MemoryStorage()));
    });
}
```

Benennen Sie Ihren `EchoBot` in `DialogContainerBot` um.

Definieren Sie in `DialogContainerBot.cs` eine Klasse für einen Profildialog.

```csharp
public class ProfileControl : DialogContainer
{
    public ProfileControl()
        : base("fillProfile")
    {
        Dialogs.Add("fillProfile", 
            new WaterfallStep[]
            {
                async (dc, args, next) =>
                {
                    dc.ActiveDialog.State = new Dictionary<string, object>();
                    await dc.Prompt("textPrompt", "What's your name?");
                },
                async (dc, args, next) =>
                {
                    dc.ActiveDialog.State["name"] = args["Value"];
                    await dc.Prompt("textPrompt", "What's your phone number?");
                },
                async (dc, args, next) =>
                {
                    dc.ActiveDialog.State["phone"] = args["Value"];
                    await dc.End(dc.ActiveDialog.State);
                }
            }
        );
        Dialogs.Add("textPrompt", new Builder.Dialogs.TextPrompt());
    }
}
```

Deklarieren Sie anschließend innerhalb der Definition des Bots ein Feld für den Hauptdialog des Bots, und initialisieren Sie es im Konstruktor des Bots.
Der Hauptdialog des Bots enthält den Profildialog.

```csharp
private DialogSet _dialogs;

public DialogContainerBot()
{
    _dialogs = new DialogSet();

    _dialogs.Add("getProfile", new ProfileControl());
    _dialogs.Add("firstRun",
        new WaterfallStep[]
        {
            async (dc, args, next) =>
            {
                    await dc.Context.SendActivity("Welcome! We need to ask a few questions to get started.");
                    await dc.Begin("getProfile");
            },
            async (dc, args, next) =>
            {
                await dc.Context.SendActivity($"Thanks {args["name"]} I have your phone number as {args["phone"]}!");
                await dc.End();
            }
        }
    );
}
```

In der `OnTurn`-Methode des Bots:
-   Begrüßen Sie den Benutzer zu Beginn der Unterhaltung.
-   Initialisieren Sie den Hauptdialog und _setzen_ Sie ihn fort, wenn wir eine Nachricht vom Benutzer erhalten.

    Wenn der Dialog keine Antwort generiert hat, gehen Sie davon aus, dass er früher abgeschlossen wurde oder noch nicht begonnen hat, und _beginnen_ Sie ihn mit Angabe des Namens des Dialogs im Satz, mit dem begonnen werden soll.

```csharp
public async Task OnTurn(ITurnContext turnContext)
{
    try
    {
        switch (turnContext.Activity.Type)
        {
            case ActivityTypes.ConversationUpdate:
                foreach (var newMember in turnContext.Activity.MembersAdded)
                {
                    if (newMember.Id != turnContext.Activity.Recipient.Id)
                    {
                        await turnContext.SendActivity("Hello and welcome to the Composite Control bot.");
                    }
                }
                break;

            case ActivityTypes.Message:
                var state = ConversationState<Dictionary<string, object>>.Get(turnContext);
                var dc = _dialogs.CreateContext(turnContext, state);

                await dc.Continue();

                if (!turnContext.Responded)
                {
                    await dc.Begin("firstRun");
                }

                break;
        }
    }
    catch (Exception e)
    {
        await turnContext.SendActivity($"Exception: {e.Message}");
    }
}

```

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)

### <a name="create-a-dialog-with-waterfall-steps"></a>Erstellen eines Dialogs mit Wasserfallschritten

Eine Unterhaltung besteht aus einem Austausch mehrerer Nachrichten zwischen einem Benutzer und einem Bot. Wenn das Ziel des Bots darin besteht, den Benutzer durch eine Reihe von Schritten zu leiten, können Sie anhand eines **Wasserfallmodells** die Schritte der Unterhaltung definieren.

Ein **Wasserfall** ist eine spezifische Implementierung eines Dialogs, der am häufigsten dazu verwendet wird, Informationen vom Benutzer zu sammeln oder den Benutzer durch eine Reihe von Aufgaben zu leiten. Die Aufgaben werden als Array von Funktionen implementiert, bei dem das Ergebnis der ersten Funktion als Argument an die nächste Funktion übergeben wird usw. Jede Funktion stellt normalerweise einen Schritt im gesamten Prozess dar. Bei jedem Schritt [fordert der Bot den Benutzer zur Eingabe auf](bot-builder-prompts.md), wartet auf eine Antwort und übergibt das Ergebnis dann an den nächsten Schritt.

Das folgende Codebeispiel definiert z.B. drei Funktionen in einem Array, das die drei Schritte eines **Wasserfalls** darstellt:

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

Die Signatur für einen **Wasserfallschritt** lautet wie folgt:

| Parameter | BESCHREIBUNG |
| ---- | ----- |
| `context` | Der Dialogkontext. |
| `args` | Optional, enthält in dem Schritt übergebene Argumente. |
| `next` | Optional, eine Methode, die Ihnen erlaubt, mit dem nächsten Wasserfallschritt fortzufahren. Sie können beim Aufrufen dieser Methode ein *args*-Argument angeben, sodass Sie Argumente an den nächsten Wasserfallschritt übergeben können. |

Jeder Schritt muss vor der Rückgabe eine der folgenden Methoden aufrufen: *next()*, *dialogs.prompt()*, *dialogs.end()*, *dialogs.begin()* oder *Promise.resolve()*; andernfalls bleibt der Bot an diesem Schritt hängen. Wenn also eine Funktion keine dieser Methoden zurückgibt, wird immer dann, wenn der Benutzer eine Nachricht an den Bot sendet, jede Benutzereingabe dazu führen, dass dieser Schritt erneut ausgeführt wird.

Wenn Sie das Ende des Wasserfalls erreicht haben, sollten Sie mit der `end()`-Methode zurückkehren, damit der Dialog vom Stapel geholt werden kann. Weitere Informationen finden Sie im Abschnitt [Beenden eines Dialogs](#end-a-dialog). Ebenso muss der Wasserfallschritt, damit das Fortfahren von einem Schritt zum nächsten möglich ist, entweder mit einer Eingabeaufforderung enden oder explizit die `next()`-Funktion zum Fortsetzen des Wasserfalls aufrufen. 

### <a name="start-a-dialog"></a>Starten eines Dialogs

Um einen Dialog zu starten, übergeben Sie die *dialogId* der Methode `begin()`, `prompt()` oder `replace()`. Die **begin**-Methode verschiebt den Dialog auf die oberste Position des Stapels, während die **replace**-Methode den aktuellen Dialog vom Stapel entfernt und den ersetzenden Dialog auf den Stapel verschiebt.

So starten Sie einen Dialog ohne Argumente:

```javascript
// Start the 'greetings' dialog.
await dc.begin('greetings');
```

So starten Sie einen Dialog mit Argumenten:

```javascript
// Start the 'greetings' dialog with the 'userName' passed in. 
await dc.begin('greetings', userName);
```

So starten Sie einen **Eingabeaufforderungsdialog**:

```javascript
// Start a 'choicePrompt' dialog with choices passed in as an array of colors to choose from.
await dc.prompt('choicePrompt', `choice: select a color`, ['red', 'green', 'blue']);
```

Je nach Art der Aufforderung, die Sie starten, kann die Argumentsignatur der Eingabeaufforderung unterschiedlich sein. Die **DialogSet.prompt**-Methode ist eine Hilfsmethode. Diese Methode akzeptiert Argumente und konstruiert die entsprechenden Optionen für die Eingabeaufforderung; anschließend ruft sie die **begin**-Methode auf, um den Eingabeaufforderungsdialog zu starten.

So ersetzen Sie einen Dialog auf dem Stapel:

```javascript
// End the current dialog and start the 'mainMenu' dialog.
await dc.replace('mainMenu'); // Can optionally passed in an 'args' as the second argument.
```

Weitere Informationen zur Verwendung der **replace()**-Methode finden Sie in den folgenden Abschnitten [Wiederholen eines Dialogs](#repeat-a-dialog) und [Dialogschleifen](#dialog-loops).

## <a name="end-a-dialog"></a>Beenden eines Dialogs

Beenden Sie einen Dialog, indem Sie ihn vom Stapel entfernen und er ein optionales Ergebnis an den übergeordneten Dialog zurückgibt. Die **Dialog.resume()**-Methode des übergeordneten Dialogs wird mit dem zurückgegebenen Ergebnis aufgerufen.

Die `end()`-Methode sollte explizit am Ende des Dialogs aufgerufen werden; erforderlich ist dies jedoch nicht, da der Dialog automatisch vom Stapel entfernt wird, wenn Sie das Ende des Wasserfalls erreichen.

So beenden Sie einen Dialog:

```javascript
// End the current dialog by popping it off the stack
await dc.end();
```

So beenden Sie einen Dialog mit optionalen, an den übergeordneten Dialog übergebenen Argumenten:

```javascript
// End the current dialog and pass a result to the parent dialog
await dc.end(result);
```

Alternativ können Sie den Dialog auch durch Rückgabe einer nicht aufgelösten Zusage beenden:

```javascript
await Promise.resolve();
```

Beim Aufruf von `Promise.resolve()` wird der Dialog beendet und vom Stapel entfernt. Diese Methode ruft jedoch nicht den übergeordneten Dialog auf, um die Ausführung fortzusetzen. Nach dem Aufruf von `Promise.resolve()` wird die Ausführung beendet, und wenn der Benutzer eine Nachricht an den Bot sendet, fährt der Bot dort fort, wo der übergeordnete Dialog aufgehört hat. Dies ist in der Regel kein benutzerfreundliches Ende eines Dialogs. Sie sollten einen Dialog entweder mit `end()` oder `replace()` beenden, damit Ihr Bot weiter mit dem Benutzer interagieren kann.

### <a name="clear-the-dialog-stack"></a>Löschen des Dialogstapels

Wenn Sie alle Dialoge vom Stapel entfernen möchten, können Sie den Dialogstapel durch Aufruf der `dc.endAll()`-Methode löschen.

```javascript
// Pop all dialogs from the current stack.
await dc.endAll();
```

### <a name="repeat-a-dialog"></a>Wiederholen eines Dialogs

Verwenden Sie zum Wiederholen eines Dialogs die `dialogs.replace()`-Methode.

```javascript
// End the current dialog and start the 'mainMenu' dialog.
await dc.replace('mainMenu'); 
```

Wenn Sie standardmäßig das Hauptmenü anzeigen möchten, können Sie mit den folgenden Schritten einen `mainMenu`-Dialog erstellen:

```javascript
// Display a menu and ask user to choose a menu item. Direct user to the item selected.
dialogs.add('mainMenu', [
    async function(dc){
        await dc.context.sendActivity("Welcome to Contoso Hotel and Resort.");
        await dc.prompt('choicePrompt', "How may we serve you today?", ['Order Dinner', 'Reserve a table']);
    },
    async function(dc, result){
        if(result.value.match(/order dinner/ig)){
            await dc.begin('orderDinner');
        }
        else if(result.value.match(/reserve a table/ig)){
            await dc.begin('reserveTable');
        }
        else {
            // Repeat the menu
            await dc.replace('mainMenu');
        }
    },
    async function(dc, result){
        // Start over
        await dc.endAll().begin('mainMenu');
    }
]);

dialogs.add('choicePrompt', new botbuilder_dialogs.ChoicePrompt());
```

Dieser Dialog verwendet einen `ChoicePrompt` zur Anzeige des Menüs und wartet darauf, dass der Benutzer eine Option auswählt. Wenn der Benutzer entweder `Order Dinner` oder `Reserve a table` auswählt, wird der Dialog für die jeweilige Wahl gestartet, und wenn diese Aufgabe abgeschlossen ist, wird der Dialog im letzten Schritt nicht einfach beendet, sondern wiederholt sich selbst.

### <a name="dialog-loops"></a>Dialogschleifen

Eine weitere Möglichkeit zum Verwenden der `replace()`-Methode ist die Schleifenemulation. Betrachten Sie dieses Szenario als Beispiel. Wenn Sie dem Benutzer ermöglichen möchten, mehrere Artikel aus dem Menü einem Warenkorb hinzuzufügen, können Sie einen Schleifendurchlauf durch die Menüoptionen durchführen, bis der Benutzer bestellt.

```javascript
// Order dinner:
// Help user order dinner from a menu

var dinnerMenu = {
    choices: ["Potato Salad - $5.99", "Tuna Sandwich - $6.89", "Clam Chowder - $4.50", 
        "More info", "Process order", "Cancel", "Help"],
    "Potato Salad - $5.99": {
        Description: "Potato Salad",
        Price: 5.99
    },
    "Tuna Sandwich - $6.89": {
        Description: "Tuna Sandwich",
        Price: 6.89
    },
    "Clam Chowder - $4.50": {
        Description: "Clam Chowder",
        Price: 4.50
    }

}

// The order cart
var orderCart = {
    orders: [],
    total: 0,
    clear: function(dc) {
        this.orders = [];
        this.total = 0;
        dc.context.activity.conversation.orderCart = null;
    }
};

dialogs.add('orderDinner', [
    async function (dc){
        await dc.context.sendActivity("Welcome to our Dinner order service.");
        orderCart.clear(dc); // Clears the cart.

        await dc.begin('orderPrompt'); // Prompt for orders
    },
    async function (dc, result) {
        if(result == "Cancel"){
            await dc.end();
        }
        else { 
            await dc.prompt('numberPrompt', "What is your room number?");
        }
    },
    async function(dc, result){
        await dc.context.sendActivity(`Thank you. Your order will be delivered to room ${result} within 45 minutes.`);
        await dc.end();
    }
]);

// Helper dialog to repeatedly prompt user for orders
dialogs.add('orderPrompt', [
    async function(dc){
        await dc.prompt('choicePrompt', "What would you like?", dinnerMenu.choices);
    },
    async function(dc, choice){
        if(choice.value.match(/process order/ig)){
            if(orderCart.orders.length > 0) {
                // Process the order
                // ...
                await dc.end();
            }
            else {
                await dc.context.sendActivity("Your cart was empty. Please add at least one item to the cart.");
                // Ask again
                await dc.replace('orderPrompt');
            }
        }
        else if(choice.value.match(/cancel/ig)){
            orderCart.clear(context);
            await dc.context.sendActivity("Your order has been canceled.");
            await dc.end(choice.value);
        }
        else if(choice.value.match(/more info/ig)){
            var msg = "More info: <br/>Potato Salad: contains 330 calaries per serving. <br/>"
                + "Tuna Sandwich: contains 700 calaries per serving. <br/>" 
                + "Clam Chowder: contains 650 calaries per serving."
            await dc.context.sendActivity(msg);
            
            // Ask again
            await dc.replace('orderPrompt');
        }
        else if(choice.value.match(/help/ig)){
            var msg = `Help: <br/>To make an order, add as many items to your cart as you like then choose the "Process order" option to check out.`
            await dc.context.sendActivity(msg);
            
            // Ask again
            await dc.replace('orderPrompt');
        }
        else {
            var choice = dinnerMenu[choice.value];

            // Only proceed if user chooses an item from the menu
            if(!choice){
                await dc.context.sendActivity("Sorry, that is not a valid item. Please pick one from the menu.");
                
                // Ask again
                await dc.replace('orderPrompt');
            }
            else {
                // Add the item to cart
                orderCart.orders.push(choice);
                orderCart.total += dinnerMenu[choice.value].Price;

                await dc.context.sendActivity(`Added to cart: ${choice.value}. <br/>Current total: $${orderCart.total}`);

                // Ask again
                await dc.replace('orderPrompt');
            }
        }
    }
]);

// Define prompts
// Generic prompts
dialogs.add('textPrompt', new botbuilder_dialogs.TextPrompt());
dialogs.add('numberPrompt', new botbuilder_dialogs.NumberPrompt());
dialogs.add('dateTimePrompt', new botbuilder_dialogs.DatetimePrompt());
dialogs.add('choicePrompt', new botbuilder_dialogs.ChoicePrompt());

```

Der obige Beispielcode zeigt, dass der Hauptdialog `orderDinner` einen Hilfsdialog mit dem Namen `orderPrompt` verwendet, um die Benutzerauswahl zu verarbeiten. Der `orderPrompt`-Dialog zeigt das Menü an, fordert den Benutzer auf, einen Artikel auszuwählen und dem Warenkorb hinzuzufügen, und fordert erneut auf. So kann der Benutzer seiner Bestellung mehrere Artikel hinzufügen. Die Dialogschleife wird solange ausgeführt, bis der Benutzer `Process order` oder `Cancel` auswählt. An diesem Punkt wird die Ausführung wieder dem übergeordneten Dialog übergeben (z.B.: `orderDinner`). Der `orderDinner`-Dialog erledigt einige abschließende Dinge, wenn der Benutzer die Bestellung aufgeben möchte; andernfalls endet er und gibt die Ausführung an den übergeordneten Dialog zurück (z.B.: `mainMenu`). Der `mainMenu`-Dialog wiederum setzt die Ausführung des letzten Schritts fort, die einfach im erneuten Anzeigen der Hauptmenüoptionen besteht.

---

## <a name="next-steps"></a>Nächste Schritte

Da Sie nun erfahren haben, wie Sie **Dialoge**, **Eingabeaufforderungen** und **Wasserfälle** zum Verwalten des Unterhaltungsflusses nutzen können, werfen wir nun einen Blick darauf, wie wir unsere Dialoge in modulare Vorgänge aufschlüsseln können, anstatt alle zusammen in das `dialogs`-Objekt der Hauptbotlogik zu werfen.

> [!div class="nextstepaction"]
> [Erstellen modularer Botlogik mit zusammengesetzten Steuerelementen](bot-builder-compositcontrol.md)