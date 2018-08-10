---
title: Extrahieren von typisierten LUIS-Ergebnissen | Microsoft-Dokumentation
description: Es wird beschrieben, wie Sie LUIS zum Extrahieren von Entitäten mit dem Bot Builder SDK verwenden.
keywords: Absichten, Entitäten, LUISGen, Extrahieren
author: DeniseMak
ms.author: v-demak
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 5/16/17
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 6a88b0a7f44f43d0676ba88314fbba7c486e6be4
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39301605"
---
# <a name="extract-intents-and-entities-using-luisgen"></a>Extrahieren von Absichten und Entitäten mit LUISGen

Neben dem Erkennen von Absichten können mit einer LUIS-App auch Entitäten extrahiert werden, bei denen es sich um wichtige Wörter für die Erfüllung der Anforderung eines Benutzers handelt. Im Beispiel mit der Reservierung für ein Restaurant kann die LUIS-App aus der Nachricht des Benutzers ggf. die Anzahl von Personen, das Datum der Reservierung oder den Standort des Restaurants extrahieren. 


Sie können das [LUISGen-Tool](https://github.com/Microsoft/botbuilder-tools/tree/master/LUISGen) zum Generieren von Klassen verwenden, die Ihnen das Extrahieren von Entitäten im Code Ihres Bots für LUIS erleichtern.

Installieren Sie über eine Node.js-Befehlszeile `luisgen` unter dem globalen Pfad.
```
npm install -g luisgen
```

# <a name="ctabcs"></a>[C#](#tab/cs)

## <a name="generate-a-luis-results-class"></a>Generieren einer LUIS-Ergebnisklasse

Laden Sie das [CafeBot-LUIS-Beispiel](https://aka.ms/contosocafebot-luis) herunter, und führen Sie LUISGen im entsprechenden Stammordner aus:

```
luisgen Assets\LU\models\LUIS\cafeLUISModel.json -cs ContosoCafeBot.CafeLUISModel
```

## <a name="examine-the-generated-code"></a>Überprüfen des generierten Codes
Bei diesem Vorgang wird die Datei **cafeLUISModel.cs** generiert, die Sie Ihrem Projekt hinzufügen können. Eine `cafeLuisModel`-Klasse wird bereitgestellt, über die stark typisierte Ergebnisse aus LUIS abgerufen werden können.

Diese Klasse verfügt über eine Enumeration zum Abrufen der Absichten, die in der LUIS-App definiert sind.
```cs
public enum Intent {
    Book_Table, 
    Greeting, 
    None, 
    Who_are_you_intent
};
```
Außerdem verfügt sie über eine `Entities`-Eigenschaft. Da eine Entität in der Nachricht eines Benutzers mehrfach vorkommen kann, definiert die `_Entities`-Klasse ein Array für jeden Entitätstyp. 
```cs
public class _Entities
{
    // Simple entities
    public string[] partySize;

    // Built-in entities
    public Microsoft.Bot.Builder.Ai.LUIS.DateTimeSpec[] datetime;
    public double[] number;

    // Lists
    public string[][] cafeLocation;

    // Instance
    public class _Instance
    {
        public Microsoft.Bot.Builder.Ai.LUIS.InstanceData[] partySize;
        public Microsoft.Bot.Builder.Ai.LUIS.InstanceData[] datetime;
        public Microsoft.Bot.Builder.Ai.LUIS.InstanceData[] number;
        public Microsoft.Bot.Builder.Ai.LUIS.InstanceData[] cafeLocation;
    }
    [JsonProperty("$instance")]
    public _Instance _instance;
}
public _Entities Entities;
```

> [!NOTE]
> Alle Entitätstypen sind Arrays, weil LUIS in der Äußerung eines Benutzers ggf. mehr als eine Entität eines angegebenen Typs erkennen kann. Wenn der Benutzer beispielsweise „make reservations for 5pm tomorrow and 9pm next Saturday“ sagt, wird in den Ergebnissen von `datetime` sowohl „5pm tomorrow“ als auch „9pm next Saturday“ zurückgegeben.
>

|Entität | Typ | Beispiel | Notizen |
|-------|-----|------|---|
|partySize| string[]| Party of `four` (Vier Personen)| Eine einfache Entität erkennt Zeichenfolgen. In diesem Beispiel hat „Entities.partySize[0]“ den Wert `"four"`.
|Datetime| [DateTimeSpec](https://docs.microsoft.com/en-us/dotnet/api/microsoft.bot.builder.ai.luis.datetimespec?view=botbuilder-4.0.0-alpha)[]| reservation for at `9pm tomorrow` (Reservierung für morgen um 21 Uhr)| Jedes **DateTimeSpec**-Objekt verfügt über ein timex-Feld mit den möglichen Werten von Zeiten, die im **timex**-Format angegeben sind. Weitere Informationen zu „timex“ finden Sie hier: http://www.timeml.org/publications/timeMLdocs/timeml_1.2.1.html#timex3.      Weitere Informationen zur Bibliothek, über die die Erkennung durchgeführt wird, finden Sie hier: https://github.com/Microsoft/Recognizers-Text.
|number| double[]| Party of `four` which includes `2` children (Vier Personen, darunter zwei Kinder) | Mit `number` werden alle Zahlenangaben identifiziert, nicht nur die Anzahl von Personen. <br/> In der Äußerung „Party of four which includes 2 children“ hat `Entities.number[0]` den Wert 4 und `Entities.number[1]` den Wert 2.
|cafelocation| string[][] | Reservation at the `Seattle` location (Reservierung in Seattle)| cafeLocation ist eine Listenentität. Dies bedeutet, dass sie erkannte Einträge aus Listen enthält. Es handelt sich um ein Array mit Arrays, falls eine erkannte Entität in mehr als einer Liste enthalten ist. Die Äußerung „Reservierung in Washington“ kann beispielsweise eine Liste für den Staat Washington und für Washington D.C. betreffen.

Die `_Instance`-Eigenschaft stellt [InstanceData](https://docs.microsoft.com/en-us/dotnet/api/microsoft.bot.builder.ai.luis.instancedata?view=botbuilder-4.0.0-alpha) bereit, um weitere Details zu den einzelnen erkannten Entitäten zu liefern.

## <a name="check-intents-in-your-bot"></a>Überprüfen von Absichten in Ihrem Bot
Sehen Sie sich in **CafeBot.cs** den Code unter `OnTurn` an. Sie sehen, wo der Bot LUIS aufruft und Absichten überprüft, um entscheiden zu können, welcher Dialog gestartet werden soll. Die LUIS-Ergebnisse des Aufrufs von [`Recognize`](https://docs.microsoft.com/en-us/dotnet/api/microsoft.bot.builder.ai.luis.luisrecognizer?view=botbuilder-4.0.0-alpha#methods) werden als Argument an den Dialog `BookTable` übergeben.



```cs
if(!context.Responded)
{
    // call LUIS and get results
    LuisRecognizer lRecognizer = createLUISRecognizer();
    // Use the generated class as the type parameter to Recognize()
    cafeLUISModel lResult = await lRecognizer.Recognize<cafeLUISModel>(utterance, ct);
    Dictionary<string,object> lD = new Dictionary<string,object>();
    if(lResult != null) {
        lD.Add("luisResult", lResult);
    }
    
    // top level dispatch
    switch (lResult.TopIntent().intent)
    {
        case cafeLUISModel.Intent.Greeting:
            await context.SendActivity("Hello!");
            if (userState.sendCards) await context.SendActivity(CreateCardResponse(context.Activity, createWelcomeCardAttachment()));
            break;

        case cafeLUISModel.Intent.Book_Table:
            await dc.Begin("BookTable", lD);
            break;

        case cafeLUISModel.Intent.Who_are_you_intent:
            await context.sendActivity("I'm the Contoso Cafe bot.");
            break;

        case cafeLUISModel.Intent.None:
        default:
            await getQnAResult(context);
            break;
    }
}
```

## <a name="extract-entities-in-a-dialog"></a>Extrahieren von Entitäten in einem Dialog

Sehen Sie sich nun `Dialogs/BookTable.cs` an. Der Dialog `BookTable` enthält eine Sequenz mit Wasserfallschritten, mit denen jeweils nach einer Entität in den LUIS-Ergebnissen gesucht wird, die an `args` übergeben werden. Wenn die Entität nicht gefunden wird, wird die Abfrage danach im Dialog übersprungen, indem `next()` aufgerufen wird. Wenn sie gefunden wird, wird im Dialog nachgefragt, und die Antwort des Benutzers auf die Nachfrage wird im nächsten Wasserfallschritt empfangen.

```cs
    Dialogs.Add("BookTable",
        new WaterfallStep[]
        {
            async (dc, args, next) =>
            {
                dc.ActiveDialog.State = new Dictionary<string, object>();
                IDictionary<string,object> state = dc.ActiveDialog.State;

                // add any LUIS entities to active dialog state.
                if(args.ContainsKey("luisResult")) {
                    cafeLUISModel lResult = (cafeLUISModel)args["luisResult"];
                    updateContextWithLUIS(lResult, ref state);
                }
                
                // prompt if we do not already have cafelocation
                if(state.ContainsKey("cafeLocation")) {
                    state["bookingLocation"] = state["cafeLocation"];
                    await next();
                } else {
                    await dc.Prompt("choicePrompt", "Which of our locations would you like?", promptOptions);
                }
            },
            async (dc, args, next) =>
            {
                var state = dc.ActiveDialog.State;
                if(!state.ContainsKey("cafeLocation")) {
                    var choiceResult = (FoundChoice)args["Value"];
                    state["bookingLocation"] = choiceResult.Value;
                }
                bool promptForDateTime = true;
                if(state.ContainsKey("datetime")) {
                    // validate timex
                    var inputdatetime = new string[] {(string)state["datetime"]};
                    var results = evaluateTimeX((string[])inputdatetime);
                    if(results.Count != 0) {
                        var timexResolution = results.First().TimexValue;
                        var timexProperty = new TimexProperty(timexResolution.ToString());
                        var bookingDateTime = $"{timexProperty.ToNaturalLanguage(DateTime.Now)}";
                        state["bookingDateTime"] = bookingDateTime;
                        promptForDateTime = false;
                    }
                }
                // prompt if we do not already have date and time
                if(promptForDateTime) {
                    await dc.Prompt("timexPrompt", "When would you like to arrive? (We open at 4PM.)",
                                    new PromptOptions { RetryPromptString = "We only accept reservations for the next 2 weeks and in the evenings between 4PM - 8PM" });
                } else {
                    await next();
                }                       
                
            },
            async (dc, args, next) =>
            {
                var state = dc.ActiveDialog.State;
                if(!state.ContainsKey("datetime")) { 
                    var timexResult = (TimexResult)args;
                    var timexResolution = timexResult.Resolutions.First();
                    var timexProperty = new TimexProperty(timexResolution.ToString());
                    var bookingDateTime = $"{timexProperty.ToNaturalLanguage(DateTime.Now)}";
                    state["bookingDateTime"] = bookingDateTime;
                }
                // prompt if we already do not have party size
                if(state.ContainsKey("partySize")) {
                    state["bookingGuestCount"] = state["partySize"];
                    await next();
                } else {
                    await dc.Prompt("numberPrompt", "How many in your party?");
                }
            },
            async (dc, args, next) =>
            {
                var state = dc.ActiveDialog.State;
                if(!state.ContainsKey("partySize")) {
                    state["bookingGuestCount"] = args["Value"];
                }

                await dc.Prompt("confirmationPrompt", $"Thanks, Should I go ahead and book a table for {state["bookingGuestCount"].ToString()} guests at our {state["bookingLocation"].ToString()} location for {state["bookingDateTime"].ToString()} ?");
            },
            async (dc, args, next) =>
            {
                var dialogState = dc.ActiveDialog.State;

                // TODO: Verify user said yes to confirmation prompt

                // TODO: book the table! 

                await dc.Context.SendActivity($"Thanks, I have {dialogState["bookingGuestCount"].ToString()} guests booked for our {dialogState["bookingLocation"].ToString()} location for {dialogState["bookingDateTime"].ToString()}.");
            }
        }
    );
}

// This helper method updates dialog state with any LUIS results
private void updateContextWithLUIS(cafeLUISModel lResult, ref IDictionary<string,object> dialogContext) {
    if(lResult.Entities.cafeLocation != null && lResult.Entities.cafeLocation.GetLength(0) > 0) {
        dialogContext.Add("cafeLocation", lResult.Entities.cafeLocation[0][0]);
    }
    if(lResult.Entities.partySize != null && lResult.Entities.partySize.GetLength(0) > 0) {
        dialogContext.Add("partySize", lResult.Entities.partySize[0][0]);
    } else {
        if(lResult.Entities.number != null && lResult.Entities.number.GetLength(0) > 0) {
            dialogContext.Add("partySize", lResult.Entities.number[0]);
        }
    }
    if(lResult.Entities.datetime != null && lResult.Entities.datetime.GetLength(0) > 0) {
        dialogContext.Add("datetime", lResult.Entities.datetime[0].Expressions[0]);
    }
}
```
## <a name="run-the-sample"></a>Ausführen des Beispiels

Öffnen Sie `ContosoCafeBot.sln` in Visual Studio 2017, und führen Sie den Bot aus. Verwenden Sie den [Bot Framework Emulator](https://docs.microsoft.com/en-us/azure/bot-service/bot-service-debug-emulator), um eine Verbindung mit dem Beispiel-Bot herzustellen.

Sagen Sie im Emulator `reserve a table`, um einen Dialog für die Reservierung zu starten.

![Ausführen des Bots](media/how-to-luisgen/run-bot.png)

# <a name="typescripttabjs"></a>[TypeScript](#tab/js)

Laden Sie das [CafeBot-LUIS-Beispiel](https://aka.ms/contosocafebot-typescript-luis-dialogs) herunter, und führen Sie LUISGen im entsprechenden Stammordner aus:

```
luisgen cafeLUISModel.json -ts CafeLUISModel
```

Bei diesem Vorgang wird die Datei **CafeLUISModel.ts** generiert, die Sie Ihrem Projekt hinzufügen können. Sie können ein typisiertes Ergebnis aus der LUIS-Erkennung abrufen, indem Sie die Typen aus der generierten Datei verwenden.


```typescript
// call LUIS and get typed results
await luisRec.recognize(context).then(async (res : any) => 
{    
    // get a typed result
    var typedresult = res as CafeLUISModel;   
    
```

## <a name="pass-the-typed-result-to-a-dialog"></a>Übergeben des typisierten Ergebnisses an einen Dialog

Sehen Sie sich den Code in **luisbot.ts** an. Im `processActivity`-Handler übergibt der Bot das typisierte Ergebnis an einen Dialog.

```typescript
// Listen for incoming requests 
server.post('/api/messages', (req, res) => {
    // Route received request to adapter for processing
    adapter.processActivity(req, res, async (context) => {
        const isMessage = context.activity.type === 'message';

        // Create dialog context 
        const state = conversationState.get(context);
        const dc = dialogs.createContext(context, state);
            
        if (!isMessage) {
            await context.sendActivity(`[${context.activity.type} event detected]`);
        }

        // Check to see if anyone replied. 
        if (!context.responded) {
            await dc.continue();
            // if the dialog didn't send a response
            if (!context.responded && isMessage) {

                
                await luisRec.recognize(context).then(async (res : any) => 
                {    
                    var typedresult = res as CafeLUISModel;                
                    let topIntent = LuisRecognizer.topIntent(res);    
                    switch (topIntent)
                    {
                        case Intents.Book_Table: {
                            await context.sendActivity("Top intent is Book_Table ");                          
                            await dc.begin('reserveTable', typedresult);
                            break;
                        }
                        
                        case Intents.Greeting: {
                            await context.sendActivity("Top intent is Greeting");
                            break;
                        }
    
                        case Intents.Who_are_you_intent: {
                            await context.sendActivity("Top intent is Who_are_you_intent");
                            break;
                        }
                        default: {
                            await dc.begin('default', topIntent);
                            break;
                        }
                    }
    
                }, (err) => {
                    // there was some error
                    console.log(err);
                }
                );                                
            }
        }
    });
});
```

## <a name="check-for-existing-entities-in-a-dialog"></a>Überprüfen auf vorhandene Entitäten in einem Dialog

In **luisbot.ts** wird für den `reserveTable`-Dialog eine `SaveEntities`-Hilfsfunktion aufgerufen, um eine Überprüfung auf Entitäten durchzuführen, die von der LUIS-App erkannt wurden. Wenn die Entitäten gefunden werden, werden sie im Dialogzustand gespeichert. Für jeden Wasserfallschritt im Dialog wird überprüft, ob eine Entität im Dialogzustand gespeichert wurde. Wenn nicht, wird nachgefragt.

```typescript
dialogs.add('reserveTable', [
    async function(dc, args, next){
        var typedresult = args as CafeLUISModel;

        // Call a helper function to save the entities in the LUIS result
        // to dialog state
        await SaveEntities(dc, typedresult);

        await dc.context.sendActivity("Welcome to the reservation service.");
        
        if (dc.activeDialog.state.dateTime) {
            await next();     
        }
        else {
            await dc.prompt('dateTimePrompt', "Please provide a reservation date and time.");
        }
    },
    async function(dc, result, next){
        if (!dc.activeDialog.state.dateTime) {
            // Save the dateTimePrompt result to dialog state
            dc.activeDialog.state.dateTime = result[0].value;
        }

        // If we don't have party size, ask for it next
        if (!dc.activeDialog.state.partySize) {
            await dc.prompt('textPrompt', "How many people are in your party?");
        } else {
            await next();
        }
    },
    async function(dc, result, next){
        if (!dc.activeDialog.state.partySize) {
            dc.activeDialog.state.partySize = result;
        }
        // Ask for the reservation name next
        await dc.prompt('textPrompt', "Whose name will this be under?");
    },
    async function(dc, result){
        dc.activeDialog.state.Name = result;

        // Save data to conversation state
        var state = conversationState.get(dc.context);

        // Copy the dialog state to the conversation state
        state = dc.activeDialog.state;

        // TODO: Add in <br/>Location: ${state.cafeLocation}
        var msg = `Reservation confirmed. Reservation details:             
            <br/>Date/Time: ${state.dateTime} 
            <br/>Party size: ${state.partySize} 
            <br/>Reservation name: ${state.Name}`;
            
        await dc.context.sendActivity(msg);
        await dc.end();
    }
]);
```

Die `SaveEntities`-Hilfsfunktion führt eine Überprüfung auf die Entitäten `datetime` und `partysize` durch. Die Entität `datetime` ist eine [vordefinierte Entität](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/luis-reference-prebuilt-entities#builtindatetimev2).

```typescript
// Helper function that saves any entities found in the LUIS result
// to the dialog state
async function SaveEntities( dc: DialogContext<TurnContext>, typedresult) {
    // Resolve entities returned from LUIS, and save these to state
    if (typedresult.entities)
    {
        console.log(`Entities found.`);
        let datetime = typedresult.entities.datetime;

        if (datetime) {
            // Use the first date or time found in the utterance
            if (datetime[0].timex) {
                timexValues = datetime[0].timex;
                // Datetime results from LUIS are represented in timex format:
                // http://www.timeml.org/publications/timeMLdocs/timeml_1.2.1.html#timex3                                
                // More information on the library which does the recognition can be found here: 
                // https://github.com/Microsoft/Recognizers-Text

                if (datetime[0].type === "datetime") {
                    // To parse timex, here you use the resolve and creator from
                    // @microsoft/recognizers-text-data-types-timex-expression
                    // The second parameter is an array of constraints the results must satisfy
                    var resolution = Resolver.evaluate(
                        // array of timex values to evaluate. There may be more than one: "today at 6" can be 6AM or 6PM.
                        timexValues,
                        // constrain results to times between 4pm and 8pm                        
                        [Creator.evening]);
                    if (resolution[0]) {
                        // toNaturalLanguage takes the current date into account to create a friendly string
                        dc.activeDialog.state.dateTime = resolution[0].toNaturalLanguage(new Date());
                        // You can also use resolution.toString() to format the date/time.
                    } else {
                        // time didn't satisfy constraint.
                        dc.activeDialog.state.dateTime = null;
                    }
                } 
                else  {
                    console.log(`Type ${datetime[0].type} is not yet supported. Provide both the date and the time.`);
                }
            }                                                
        }
        let partysize = typedresult.entities.partySize;
        if (partysize) {
            console.log(`partysize entity detected.${partysize}`);
            // use first partySize entity that was found in utterance
            dc.activeDialog.state.partySize = partysize[0];
        }
        let cafelocation = typedresult.entities.cafeLocation;

        if (cafelocation) {
            console.log(`location entity detected.${cafelocation}`);
            // use first cafeLocation entity that was found in utterance
            dc.activeDialog.state.cafeLocation = cafelocation[0][0];
        }
    } 
}
```

## <a name="run-the-sample"></a>Ausführen des Beispiels

1. Wenn Sie den TypeScript-Compiler noch nicht installiert haben, können Sie diesen Befehl verwenden:

```
npm install --global typescript
```

2. Installieren Sie Abhängigkeiten vor dem Ausführen des Bots, indem Sie `npm install` im Stammverzeichnis des Beispiels ausführen:

```
npm install
```

3. Erstellen Sie über das Stammverzeichnis das Beispiel mithilfe von `tsc`. `luisbot.js` wird generiert.

4. Führen Sie `luisbot.js` im Verzeichnis `lib` aus.

5. Verwenden Sie den [Bot Framework Emulator](https://docs.microsoft.com/en-us/azure/bot-service/bot-service-debug-emulator), um das Beispiel auszuführen.

6. Sagen Sie im Emulator `reserve a table`, um einen Dialog für die Reservierung zu starten.

![Ausführen des Bots](media/how-to-luisgen/run-bot.png)

---


## <a name="additional-resources"></a>Zusätzliche Ressourcen

Weitere Hintergrundinformationen zu LUIS finden Sie unter [Language Understanding](./bot-builder-concept-luis.md).


## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Kombinieren von LUIS und QnA mit dem Dispatch-Tool](./bot-builder-tutorial-dispatch.md)


