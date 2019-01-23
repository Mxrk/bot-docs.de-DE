---
redirect_url: /bot-framework/bot-builder-prompts
ms.openlocfilehash: d45ec888a0082ee17718c93fc34a3df99431a254
ms.sourcegitcommit: b15cf37afc4f57d13ca6636d4227433809562f8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/11/2019
ms.locfileid: "54225415"
---
<a name="--"></a><!--
---
Titel: Stellen von Fragen an den Benutzer | Microsoft-Dokumentation description: Hier erfahren Sie, wie das Wasserfallmodell im Bot Framework SDK verwendet wird, um den Benutzer zu mehreren Eingaben aufzufordern.
keywords: Wasserfälle, Dialoge, Stellen einer Frage, Eingabeaufforderungen author: v-ducvo ms.author: v-ducvo ms.author: v-ducvo manager: kamrani ms.topic: article ms.service: bot-service ms.subservice: sdk ms.date: 5/10/2018 monikerRange: 'azure-bot-service-4.0'
---

# <a name="ask-the-user-questions"></a>Dem Benutzer Fragen stellen

[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

Im Grunde wird ein Bot für eine Unterhaltung mit einem Benutzer erstellt. Unterhaltungen können [viele Formen](bot-builder-conversations.md) annehmen. Sie können kurz oder komplexer sein, Fragen stellen oder Fragen beantworten. Welche Form die Unterhaltung annimmt, ist von mehreren Faktoren abhängig, aber alle umfassen eine Unterhaltung.

Dieses Tutorial führt Sie durch das Einrichten einer Unterhaltung: vom Stellen einer einfachen Frage bis hin zu einem Bot mit mehreren Durchgängen. In unserem Beispiel geht es darum, einen Tisch zu reservieren, aber Sie können sich auch einen Bot vorstellen, der eine Vielzahl von Dingen durch eine Unterhaltung mit mehreren Durchgängen erledigt, z.B. eine Bestellung aufgeben, FAQs beantworten, Reservierungen vornehmen usw.

Ein interaktiver Chatbot kann auf Benutzereingaben reagieren oder den Benutzer um eine bestimmte Eingabe bitten. In diesem Tutorial wird gezeigt, wie Sie einem Benutzer eine Frage mithilfe der `Prompts`-Bibliothek stellen, die Bestandteil von `Dialogs` ist. [Dialogfelder](../bot-service-design-conversation-flow.md) können als der Container betrachtet werden, der eine Unterhaltungsstruktur definiert. Eingabeaufforderungen innerhalb von Dialogfeldern werden in einem eigenen [Artikel mit Vorgehensweisen](bot-builder-prompts.md) ausführlicher behandelt.

## <a name="prerequisite"></a>Voraussetzung

Der Code in diesem Tutorial baut auf dem **Basisbot** auf, den Sie im Artikel [Erste Schritte](~/bot-service-quickstart.md) erstellt haben.

## <a name="get-the-package"></a>Abrufen des Pakets

# <a name="ctabcstab"></a>[C#](#tab/cstab)

Installieren Sie das Paket **Microsoft.Bot.Builder.Dialogs** aus dem NuGet-Paket-Manager.

# <a name="javascripttabjstab"></a>[JavaScript](#tab/jstab)
Navigieren Sie zum Projektordner Ihres Bots, und installieren Sie das `botbuilder-dialogs`-Paket von npm:

```cmd
npm install --save botbuilder-dialogs@preview
```

---

## <a name="import-package-to-bot"></a>Importieren des Pakets in den Bot

# <a name="ctabcstab"></a>[C#](#tab/cstab)

Fügen Sie Verweise auf Dialogfelder und Eingabeaufforderungen in Ihrem Botcode hinzu.

```cs
// ...
using Microsoft.Bot.Builder.Dialogs;
using Microsoft.Bot.Builder.Prompts;
// ...
```


# <a name="javascripttabjstab"></a>[JavaScript](#tab/jstab)

Öffnen Sie die Datei **app.js**, und schließen Sie die `botbuilder-dialogs`-Bibliothek in den Botcode ein.

```javascript
const botbuilder_dialogs = require('botbuilder-dialogs');
```

---

Dadurch erhalten Sie Zugriff auf die `DialogSet`- und `Prompts`-Bibliothek, die Sie verwenden, um dem Benutzer Fragen zu stellen. `DialogSet` ist einfach eine Sammlung von Dialogfeldern, denen wir die Struktur eines **Wasserfall**musters verleihen. Dies bedeutet nur, dass ein Dialogfeld auf ein anderes folgt.

## <a name="instantiate-a-dialogs-object"></a>Instanziieren eines dialogs-Objekts

Instanziieren Sie ein `dialogs`-Objekt. Sie verwenden dieses Dialogfeldobjekt zum Verwalten des Frage- und Antwortvorgangs.

# <a name="ctabcstab"></a>[C#](#tab/cstab)
Deklarieren Sie eine Membervariable in Ihrer Botklasse, und initialisieren Sie diese im Konstruktor für Ihren Bot. 
```cs
public class MyBot : IBot
{
    private readonly DialogSet dialogs;
    public MyBot()
    {
        dialogs = new DialogSet();
    }
    // The rest of the class definition is omitted here
}
```

# <a name="javascripttabjstab"></a>[JavaScript](#tab/jstab)

```javascript
const dialogs = new botbuilder_dialogs.DialogSet();
```
---

## <a name="define-a-waterfall-dialog"></a>Definieren eines Wasserfalldialogfelds

Um eine Frage zu stellen, benötigen Sie ein **Wasserfall**dialogfeld mit mindestens zwei Schritten. Für dieses Beispiel erstellen Sie ein zweistufiges **Wasserfall**dialogfeld, in dem der erste Schritt den Benutzer nach seinem Namen fragt und der zweite Schritt den Benutzer mit seinem Namen begrüßt. 

# <a name="ctabcstab"></a>[C#](#tab/cstab)

Ändern Sie den Konstruktor Ihres Bots, um das Dialogfeld hinzuzufügen:
```csharp
public MyBot()
{
    dialogs = new DialogSet();
    dialogs.Add("greetings", new WaterfallStep[]
    {
        async (dc, args, next) =>
        {
            // Prompt for the guest's name.
            await dc.Prompt("textPrompt","What is your name?");
        },
        async(dc, args, next) =>
        {
            await dc.Context.SendActivity($"Hi {args["Text"]}!");
            await dc.End();
        }
    });
}
```

# <a name="javascripttabjstab"></a>[JavaScript](#tab/jstab)

```javascript
// Greet user:
// Ask for the user name and then greet them by name.
dialogs.add('greetings',[
    async function (dc){
        await dc.prompt('textPrompt', 'What is your name?');
    },
    async function(dc, results){
        var userName = results;
        await dc.context.sendActivity(`Hello ${userName}!`);
        await dc.end(); // Ends the dialog
    }
]);
```

---

Die Frage wird mithilfe einer `textPrompt`-Methode gestellt, die in der `Prompts`-Bibliothek enthalten ist. Die `Prompts`-Bibliothek bietet eine Reihe von Eingabeaufforderungen, die Ihnen ermöglichen, Benutzer um die Eingabe verschiedener Arten von Informationen zu bitten. Weitere Informationen zu anderen Eingabeaufforderungstypen finden Sie unter [Auffordern von Benutzern zu einer Eingabe](~/v4sdk/bot-builder-prompts.md).

Damit die Eingabeaufforderungen funktionieren, müssen Sie dem `dialogs`-Objekt eine Eingabeaufforderung mit der dialogId `textPrompt` hinzufügen und die Eingabeaufforderung mit dem `TextPrompt()`-Konstruktor erstellen.

# <a name="ctabcstab"></a>[C#](#tab/cstab)

```cs
public MyBot()
{
    dialogs = new DialogSet();
    dialogs.Add("greetings", new WaterfallStep[]
    {
        async (dc, args, next) =>
        {
            // Prompt for the guest's name.
            await dc.Prompt("textPrompt","What is your name?");
        },
        async(dc, args, next) =>
        {
            await dc.Context.SendActivity($"Hi {args["Text"]}!");
            await dc.End();
        }
    });
    // add the prompt, of type TextPrompt
    dialogs.Add("textPrompt", new Builder.Dialogs.TextPrompt());
}

```
Sobald der Benutzer auf die Frage geantwortet hat, ist die Antwort im `args`-Parameter von Schritt 2 enthalten.

# <a name="javascripttabjstab"></a>[JavaScript](#tab/jstab)

```javascript
dialogs.add('textPrompt', new botbuilder_dialogs.TextPrompt());
```
Sobald der Benutzer auf die Frage geantwortet hat, ist die Antwort im `results`-Parameter von Schritt 2 enthalten. In diesem Fall werden die `results` einer lokalen Variablen `userName` zugewiesen. In einem späteren Tutorial zeigen wir Ihnen, wie Sie Benutzereingaben in einem Speicher Ihrer Wahl persistent speichern können.

---


Nachdem Sie nun Ihre `dialogs` definiert haben, um eine Frage zu stellen, müssen Sie das Dialogfeld aufrufen, um den Eingabeaufforderungsvorgang zu starten.

## <a name="start-the-dialog"></a>Starten des Dialogs

# <a name="ctabcstab"></a>[C#](#tab/cstab)

Ändern Sie die Botlogik wie folgt:

```cs

public async Task OnTurn(ITurnContext context)
{
    // We'll cover state later, in the next tutorial
    var state = ConversationState<Dictionary<string, object>>.Get(context);
    var dc = dialogs.CreateContext(context, state);
    if (context.Activity.Type == ActivityTypes.Message)
    {
        await dc.Continue();
        
        if(!context.Responded)
        {
            await dc.Begin("greetings");
        }
    }
}
```

Botlogik wird in der `OnTurn()`-Methode implementiert. Sobald der Benutzer „Hi“ sagt, startet der Bot das `greetings`-Dialogfeld. Der erste Schritt des `greetings`-Dialogfelds fordert den Benutzer zur Eingabe seines Namens auf. Der Benutzer sendet eine Antwort mit seinem Namen als Nachrichtenaktivität, und das Ergebnis wird an Schritt 2 des Wasserfalls über die `dc.Continue()`-Methode gesendet. Im zweite Schritt des Wasserfalls wird (wie von Ihnen definiert) der Benutzer mit seinem Namen begrüßt und das Dialogfeld beendet. 

# <a name="javascripttabjstab"></a>[JavaScript](#tab/jstab)

Ändern Sie die `processActivity()`-Methode Ihres **Basisbots** wie folgt:

```javascript
// Listen for incoming activity 
server.post('/api/messages', (req, res) => {
    adapter.processActivity(req, res, async (context) => {
        const isMessage = (context.activity.type === 'message');
        // State will store all of your information 
        const convo = conversationState.get(context);
        const dc = dialogs.createContext(context, convo);

        if (isMessage) {
            // Check for valid intents
            if(context.activity.text.match(/hi/ig)){
                await dc.begin('greetings');
            }
        }

        if(!context.responded){
            // Continue executing the "current" dialog, if any.
            await dc.continue();

            if(!context.responded && isMessage){
                // Default message
                await context.sendActivity("Hi! I'm a simple bot. Please say 'Hi'.");
            }
        }
    });
});
```

Botlogik wird in der `processActivity()`-Methode implementiert. Sobald der Benutzer „Hi“ sagt, startet der Bot das `greetings`-Dialogfeld. Der erste Schritt des `greetings`-Dialogfelds fordert den Benutzer zur Eingabe seines Namens auf. Der Benutzer sendet eine Antwort mit seinem Namen als `text`-Nachricht der Aktivität. Da die Nachricht nicht mit erwarteten Absichten übereinstimmt und der Bot keine Antwort an den Benutzer gesendet hat, wird das Ergebnis über die `dc.continue()`-Methode an Schritt zwei des Wasserfalls gesendet. Im zweite Schritt des Wasserfalls wird (wie von Ihnen definiert) der Benutzer mit seinem Namen begrüßt und das Dialogfeld beendet. Wenn z.B. Schritt zwei dem Benutzer die Begrüßungsnachricht nicht gesendet hat, wird die `processActivity`-Methode beendet, indem die *Standardnachricht* an den Benutzer gesendet wird.

---



## <a name="define-a-more-complex-waterfall-dialog"></a>Definieren eines komplexeren Wasserfalldialogfelds

Nachdem wir nun beschrieben haben, wie ein Wasserfalldialogfeld funktioniert und wie ein solcher Wasserfall erstellt wird, versuchen wir uns an einem komplexeren Dialogfeld, um einen Tisch zu reservieren.

Um die Tischreservierungsanforderung zu verwalten, müssen Sie ein **Wasserfall**dialogfeld mit vier Schritten definieren. In dieser Unterhaltung verwenden Sie außerdem eine `DatetimePrompt`- und eine `NumberPrompt`-Eingabeaufforderung zusätzlich zur `TextPrompt`-Eingabeaufforderung.



# <a name="ctabcstab"></a>[C#](#tab/cstab)

Beginnen Sie mit der Echobotvorlage, und benennen Sie Ihren Bot in CafeBot um. Fügen Sie ein `DialogSet` und einige statische Membervariablen hinzu.

```cs

namespace CafeBot
{
    public class CafeBot : IBot
    {
        private readonly DialogSet dialogs;

        // Usually, we would save the dialog answers to our state object, which will be covered in a later tutorial.
        // For purpose of this example, let's use the three static variables to store our reservation information.
        static DateTime reservationDate;
        static int partySize;
        static string reservationName;

        // the rest of the class definition is omitted here
        // but is discussed in the rest of this article
    }
}
```

Definieren Sie dann Ihr `reserveTable`-Dialogfeld. Sie können das Dialogfeld im Konstruktor der Botklasse hinzufügen.
```cs
public CafeBot()
{
    dialogs = new DialogSet();

    // Define our dialog
    dialogs.Add("reserveTable", new WaterfallStep[]
    {
        async (dc, args, next) =>
        {
            // Prompt for the guest's name.
            await dc.Context.SendActivity("Welcome to the reservation service.");

            await dc.Prompt("dateTimePrompt", "Please provide a reservation date and time.");
        },
        async(dc, args, next) =>
        {
            var dateTimeResult = ((DateTimeResult)args).Resolution.First();

            reservationDate = Convert.ToDateTime(dateTimeResult.Value);
            
            // Ask for next info
            await dc.Prompt("partySizePrompt", "How many people are in your party?");

        },
        async(dc, args, next) =>
        {
            partySize = (int)args["Value"];

            // Ask for next info
            await dc.Prompt("textPrompt", "Whose name will this be under?");
        },
        async(dc, args, next) =>
        {
            reservationName = args["Text"];
            string msg = "Reservation confirmed. Reservation details - " +
            $"\nDate/Time: {reservationDate.ToString()} " +
            $"\nParty size: {partySize.ToString()} " +
            $"\nReservation name: {reservationName}";
            await dc.Context.SendActivity(msg);
            await dc.End();
        }
    });

     // Add a prompt for the reservation date
     dialogs.Add("dateTimePrompt", new Microsoft.Bot.Builder.Dialogs.DateTimePrompt(Culture.English));
     // Add a prompt for the party size
     dialogs.Add("partySizePrompt", new Microsoft.Bot.Builder.Dialogs.NumberPrompt<int>(Culture.English));
     // Add a prompt for the user's name
     dialogs.Add("textPrompt", new Microsoft.Bot.Builder.Dialogs.TextPrompt());
}
```


# <a name="javascripttabjstab"></a>[JavaScript](#tab/jstab)

Der `reserveTable`-Dialog sieht folgendermaßen aus:

```javascript
// Reserve a table:
// Help the user to reserve a table
var reservationInfo = {};

dialogs.add('reserveTable', [
    async function(dc, args, next){
        await dc.context.sendActivity("Welcome to the reservation service.");

        reservationInfo = {}; // Clears any previous data
        await dc.prompt('dateTimePrompt', "Please provide a reservation date and time.");
    },
    async function(dc, result){
        reservationInfo.dateTime = result[0].value;

        // Ask for next info
        await dc.prompt('partySizePrompt', "How many people are in your party?");
    },
    async function(dc, result){
        reservationInfo.partySize = result;

        // Ask for next info
        await dc.prompt('textPrompt', "Whose name will this be under?");
    },
    async function(dc, result){
        reservationInfo.reserveName = result;
        
        // Reservation confirmation
        var msg = `Reservation confirmed. Reservation details: 
            <br/>Date/Time: ${reservationInfo.dateTime} 
            <br/>Party size: ${reservationInfo.partySize} 
            <br/>Reservation name: ${reservationInfo.reserveName}`;
        await dc.context.sendActivity(msg);
        await dc.end();
    }
]);
```

---

Der Gesprächsfluss des `reserveTable`-Dialogfelds stellt dem Benutzer während der ersten drei Schritte des Wasserfalls 3 Fragen. Schritt vier verarbeitet die Antwort auf die letzte Frage und sendet dem Benutzer die Reservierungsbestätigung.



# <a name="ctabcstab"></a>[C#](#tab/cstab)
Jeder Wasserfallschritt des `reserveTable`-Dialogfelds verwendet eine Eingabeaufforderung, um den Benutzer um die Eingabe von Informationen zu bitten. Der folgende Code wurde verwendet, um die Eingabeaufforderungen dem Dialogfeldsatz hinzuzufügen.

```cs
dialogs.Add("dateTimePrompt", new Microsoft.Bot.Builder.Dialogs.DateTimePrompt(Culture.English));
dialogs.Add("partySizePrompt", new Microsoft.Bot.Builder.Dialogs.NumberPrompt<int>(Culture.English));
dialogs.Add("textPrompt", new Microsoft.Bot.Builder.Dialogs.TextPrompt());
```

# <a name="javascripttabjstab"></a>[JavaScript](#tab/jstab)

Damit dieser Wasserfall funktioniert, müssen Sie dem `dialogs`-Objekt außerdem diese Eingabeaufforderungen hinzufügen:

```javascript
// Define prompts
// Generic prompts
dialogs.add('textPrompt', new botbuilder_dialogs.TextPrompt());
dialogs.add('dateTimePrompt', new botbuilder_dialogs.DatetimePrompt());
dialogs.add('partySizePrompt', new botbuilder_dialogs.NumberPrompt());
```

---

Nun sind Sie bereit, dies in die Botlogik einzubinden.

## <a name="start-the-dialog"></a>Starten des Dialogs

# <a name="ctabcstab"></a>[C#](#tab/cstab)
Ändern Sie `OnTurn` des Bots so, dass der folgende Code enthalten ist:
```cs
public async Task OnTurn(ITurnContext context)
{
    if (context.Activity.Type == ActivityTypes.Message)
    {
        // The type parameter PropertyBag inherits from 
        // Dictionary<string,object>
        var state = ConversationState<Dictionary<string, object>>.Get(context);
        var dc = dialogs.CreateContext(context, state);
        await dc.Continue();

        // Additional logic can be added to enter each dialog depending on the message received
        
        if(!context.Responded)
        {
            if (context.Activity.Text.ToLowerInvariant().Contains("reserve table"))
            {
                await dc.Begin("reserveTable");
            }
            else
            {
                await context.SendActivity($"You said '{context.Activity.Text}'");
            }
        }
    }
}
```


Ändern Sie in **Startup.cs** die Initialisierung der ConversationState-Middleware so, dass eine von `Dictionary<string,object>` abgeleitete Klasse anstelle von `EchoState` verwendet wird.

Gehen Sie beispielsweise in `Configure()` folgendermaßen vor:
```cs
options.Middleware.Add(new ConversationState<Dictionary<string, object>>(dataStore));
```


# <a name="javascripttabjstab"></a>[JavaScript](#tab/jstab)

Um die Benutzerabsicht für diese Anforderung zu erfassen, fügen Sie der `processActivity()`-Methode einige Zeilen Code hinzu. Ändern Sie die `processActivity()`-Methode Ihres Bots wie folgt:

```javascript
// Listen for incoming activity 
server.post('/api/messages', (req, res) => {
    adapter.processActivity(req, res, async (context) => {
        const isMessage = (context.activity.type === 'message');
        // State will store all of your information 
        const convo = conversationState.get(context);
        const dc = dialogs.createContext(context, convo);

        if (isMessage) {
            // Check for valid intents
            if(context.activity.text.match(/hi/ig)){
                await dc.begin('greetings');
            }
            else if(context.activity.text.match(/reserve table/ig)){
                await dc.begin('reserveTable');
            }
        }

        if(!context.responded){
            // Continue executing the "current" dialog, if any.
            await dc.continue();

            if(!context.responded && isMessage){
                // Default message
                await context.sendActivity("Hi! I'm a simple bot. Please say 'Hi' or 'Reserve table'.");
            }
        }
    });
});
```

Zum Zeitpunkt der Ausführung startet der Bot jedes Mal, wenn der Benutzer die Nachricht mit der Zeichenfolge `reserve table` sendet, die `reserveTable`-Unterhaltung.

---



## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial speichert der Bot die Eingabe des Benutzers in Variablen innerhalb des Bots. Wenn Sie diese Informationen speichern oder persistent beibehalten möchten, müssen Sie der Middlewareebene einen Benutzerzustand hinzufügen. Werfen wir im nächsten Tutorial einen genaueren Blick darauf, wie Benutzerzustandsdaten persistent gespeichert werden. 

> [!div class="nextstepaction"]
> [Speichern von Benutzerdaten](bot-builder-tutorial-persist-user-inputs.md)

-->
