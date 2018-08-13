---
title: Erstellen modularer Botlogik unter Verwendung des Dialogfeldcontainers | Microsoft Docs
description: Erfahren Sie, wie Sie Ihre Botlogik mithilfe des Dialogfeldcontainers im Bot Builder SDK für Node.js und C# modularisieren können.
keywords: zusammengesetztes Steuerelement, modulare Botlogik
author: v-ducvo
ms.author: v-ducvo
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 4/27/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 2441a32167618ebb08e6a43d68d74076c3351d8f
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39303920"
---
# <a name="create-modular-bot-logic-with-a-dialog-container"></a>Erstellen modularer Botlogik mit einem Dialogfeldcontainer

[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

Stellen Sie sich vor, Sie erstellen einen Hotelbot, der mehrere Aufgaben übernimmt, z.B. die Begrüßung des Benutzers, die Reservierung eines Tischs für das Abendessen, die Bestellung von Speisen, das Einstellen eines Alarms, die Anzeige des aktuellen Wetters und vieles mehr. Sie können jede dieser Aufgaben innerhalb Ihres Bots mit einem Dialogfeldobjekt erledigen, aber das kann dazu führen, dass Ihr Dialogfeldcode in der Hauptdatei Ihres Bots zu groß und überladen wird.

Sie können dieses Problem mit Modularisierung in Angriff nehmen. Modularisierung optimiert Ihren Code und erleichtert das Debuggen. Darüber hinaus können Sie ihn in Abschnitte gliedern, damit mehrere Teams gleichzeitig am Bot arbeiten können. Wir können einen Bot erstellen, der mehrere Gesprächsflüsse verwaltet, indem wir diese mit einem Dialogfeldcontainer in Komponenten zerlegen. Wir erstellen einige grundlegende Gesprächsflüsse und zeigen, wie sie über einen Dialogfeldcontainer miteinander kombiniert werden können.

In diesem Beispiel erstellen wir einen Hotelbot, der Eincheck-, Weckruf- und Tischreservierungsmodule kombiniert.

## <a name="managing-state"></a>Verwalten des Zustands

Es gibt viele Möglichkeiten, um Zustandsverwaltung für einen Bot einzurichten, der zusammengesetzte Dialogfelder verwendet. In diesen Bot zeigen wir Ihnen eine dieser Möglichkeiten.

Weitere Informationen zur Zustandsverwaltung finden Sie unter [Speichern des Zustands mit Unterhaltungs- und Benutzereigenschaften](bot-builder-howto-v4-state.md).

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Jedes Dialogfeld sammelt einige Informationen. Diese Informationen werden im Benutzerzustand gespeichert. Wir definieren eine Klasse für jedes Dialogfeld und verwenden diese Klassen als Eigenschaften in unserer Benutzerzustandsklasse.

```csharp
/// <summary>
/// State information associated with the check-in dialog.
/// </summary>
public class GuestInfo
{
    public string Name { get; set; }
    public string Room { get; set; }
}

/// <summary>
/// State information associated with the reserve-table dialog.
/// </summary>
public class TableInfo
{
    public string Number { get; set; }
}

/// <summary>
/// State information associated with the wake-up call dialog.
/// </summary>
public class WakeUpInfo
{
    public string Time { get; set; }
}

/// <summary>
/// User state information.
/// </summary>
public class UserInfo
{
    public GuestInfo Guest { get; set; }
    public TableInfo Table { get; set; }
    public WakeUpInfo WakeUp { get; set; }
}
```

In einem Botdurchlauf richtet die festgelegte `CreateContext`-Methode des Dialogfelds den Dialogfeldzustand ein.
Die Methode nimmt den Durchlaufkontext und ein Zustandsobjekt als Parameter an.

Für Dialogfelder muss dieses Zustandsobjekt die `IDictionary<string, object>`-Schnittstelle implementieren. Da dieser Bot nur den Unterhaltungszustand verwendet, um den Dialogfeldzustand zu speichern, kann unsere Unterhaltungszustandsklasse ein einfaches Wörterbuch sein.

```csharp
using System.Collections.Generic;

/// <summary>
/// Conversation state information.
/// We are also using this directly for dialog state, which needs an <see cref="IDictionary{string, object}"/> object.
/// </summary>
public class ConversationInfo : Dictionary<string, object> { }
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Zum Nachverfolgen der Benutzereingabe übergeben wir aus dem übergeordneten Dialogfeld ein `userState`-Objekt als Dialogfeldparameter. In jeder Dialogfeldklasse wird das Dialogfeld in den Konstruktor integriert, sodass Sie Informationen in `userState` speichern können. In diesem Dialogfeld können wir in ein lokales Zustandsobjekt schreiben, das als Eigenschaft für das `dc.activeDialog.state`-Objekt definiert ist, wenn der Benutzer Informationen eingibt. Nachdem das Dialogfeld abgeschlossen wurde, wird das lokale Zustandsobjekt verworfen. Aus diesem Grund speichern wir das lokale Zustandsobjekt im `userState` des übergeordneten Objekts, der Informationen zum Benutzer unterhaltungsübergreifend persistent speichert. 

---

## <a name="define-a-modular-check-in-dialog"></a>Definieren einen modularen Eincheckdialogfelds

Zunächst starten wir mit einem einfachen Eincheckdialogfeld, das den Benutzer nach seinem Namen und dem gewünschten Zimmertyp fragt. Um diese Aufgabe zu modularisieren, erstellen wir eine `CheckIn`-Klasse, die `DialogContainer` erweitert. Diese Klasse verfügt über einen Konstruktor, der den Namen des Stammdialogfelds definiert, das als ein *Wasserfall* mit drei Schritten definiert ist. Die Signatur und Erstellung des Dialogfeldobjekts ist identisch mit einem Standardwasserfall.

**Schritte des Eincheckdialogfelds**

1. Fragen nach dem Namen des Gasts.
1. Fragen nach dem gewünschten Zimmertyp.
1. Senden einer Bestätigungsnachricht und Beenden des Dialogfelds.

Weitere Informationen zu Dialogfeldern und Wasserfällen finden Sie unter [Verwenden von Dialogfeldern zum Verwalten von Gesprächsflüssen](bot-builder-dialog-manage-conversation-flow.md).

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Die `CheckIn`-Klasse verfügt über einen privaten Konstruktor, der die Schritte für unser Eincheckdialogfeld definiert, eine einzelne Instanz erstellt und diese in einer statischen `Instance`-Eigenschaft bereitstellt.

In diesem Dialogfeld kann in ein lokales Zustandsobjekt geschrieben werden, auf das über eine Eigenschaft des Dialogfeldkontexts (`dc.ActiveDialog.State`) zugegriffen werden kann. Nachdem das Dialogfeld abgeschlossen wurde, wird das lokale Zustandsobjekt verworfen. Aus diesem Grund speichern wir das lokale Zustandsobjekt im `userState` des Bots, der Informationen zum Benutzer unterhaltungsübergreifend persistent speichert.

Weitere Informationen zur Zustandsverwaltung finden Sie unter [Speichern des Zustands mit Unterhaltungs- und Benutzereigenschaften](bot-builder-howto-v4-state.md). 

**CheckIn.cs**
```csharp
using Microsoft.Bot.Builder.Core.Extensions;
using Microsoft.Bot.Builder.Dialogs;
using Microsoft.Recognizers.Text;

namespace HotelBot
{
    public class CheckIn : DialogContainer
    {
        public const string Id = "checkIn";

        private const string GuestKey = nameof(CheckIn);

        public static CheckIn Instance { get; } = new CheckIn();

        // You can start this from the parent using the dialog's ID.
        private CheckIn() : base(Id)
        {
            // Define the conversation flow using a waterfall model.
            this.Dialogs.Add(Id, new WaterfallStep[]
            {
                async (dc, args, next) =>
                {
                    // Clear the guest information and prompt for the guest's name.
                    dc.ActiveDialog.State[GuestKey] = new GuestInfo();
                    await dc.Prompt("textPrompt", "What is your name?");
                },
                async (dc, args, next) =>
                {
                    // Save the name and prompt for the room number.
                    var name = args["Value"] as string;

                    var guestInfo = dc.ActiveDialog.State[GuestKey];
                    ((GuestInfo)guestInfo).Name = name;

                    await dc.Prompt("numberPrompt",
                        $"Hi {name}. What room will you be staying in?");
                },
                async (dc, args, next) =>
                {
                    // Save the room number and "sign off".
                    var room = (string)args["Text"];

                    var guestInfo = dc.ActiveDialog.State[GuestKey];
                    ((GuestInfo)guestInfo).Room = room;

                    await dc.Context.SendActivity("Great, enjoy your stay!");

                    // Save dialog state to user state and end the dialog.
                    var userState = UserState<UserInfo>.Get(dc.Context);
                    userState.Guest = (GuestInfo)guestInfo;

                    await dc.End();
                }
            });

            // Define the prompts used in this conversation flow.
            this.Dialogs.Add("textPrompt", new TextPrompt());
            this.Dialogs.Add("numberPrompt", new NumberPrompt<int>(Culture.English));
        }
    }
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

**checkIn.js**
```JavaScript
const { DialogContainer, DialogSet, TextPrompt, NumberPrompt } = require('botbuilder-dialogs');

class CheckIn extends DialogContainer {
    constructor(userState) {
        // Dialog ID of 'checkIn' will start when class is called in the parent
        super('checkIn');

        // Defining the conversation flow using a waterfall model
        this.dialogs.add('checkIn', [
            async function (dc) {
                // Create a new local guestInfo state object
                dc.activeDialog.state.guestInfo = {};
                await dc.context.sendActivity("What is your name?");
            },
            async function (dc, name){
                // Save the name 
                dc.activeDialog.state.guestInfo.userName = name;
                await dc.prompt('numberPrompt', `Hi ${name}. What room will you be staying in?`);
            },
            async function (dc, room){
                // Save the room number
                dc.activeDialog.state.guestInfo.room = room
                await dc.context.sendActivity(`Great! Enjoy your stay!`);

                // Save dialog's state object to the parent's state object
                const user = userState.get(dc.context);
                user.guestInfo = dc.activeDialog.state.guestInfo;
                await dc.end();
            }
        ]);
        // Defining the prompt used in this conversation flow
        this.dialogs.add('textPrompt', new TextPrompt());
        this.dialogs.add('numberPrompt', new NumberPrompt());
    }
}
exports.CheckIn = CheckIn;
```

---

## <a name="define-modular-reserve-table-and-wake-up-dialogs"></a>Definieren von modularen Tischreservierungs- und Weckrufdialogfeldern

Ein Vorteil der Verwendung eines Dialogfeldcontainers ist die Möglichkeit, Dialogfelder miteinander zu kombinieren. Da jedes `DialogSet`-Objekt eine exklusive Sammlung von `dialogs` verwaltet, kann das Freigeben von oder Angeben von Querverweisen für `dialogs` nicht auf einfache Weise ausgeführt werden. Hier kommt der Dialogfeldcontainer in‘s Spiel. Mithilfe von Dialogfeldcontainern können Sie ein zusammengesetztes Dialogfeld erstellen, das die Verwaltung des Dialogfeldflusses über Dialogfelder hinweg erleichtert. Zur Veranschaulichung erstellen wir zwei weitere Dialogfelder: eines, das den Benutzer fragt, welchen Tisch er für das Abendessen reservieren möchte, und eines zum Erstellen eines Weckrufs. In diesem Fall verwenden wir erneut eine separate Klasse für jedes Dialogfeld, und jedes Dialogfeld erweitert `DialogContainer`.

**Schritte im Tischreservierungs-Dialogfeld**

1. Fragen nach dem zu reservierenden Tisch.
1. Senden einer Bestätigungsnachricht und Beenden des Dialogfelds.

**Schritte im Weckrufdialogfeld**

1. Fragen nach der gewünschten Weckzeit, die festgelegt werden soll.
1. Senden einer Bestätigungsnachricht und Beenden des Dialogfelds.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

**ReserveTable.cs**
```csharp
using Microsoft.Bot.Builder.Core.Extensions;
using Microsoft.Bot.Builder.Dialogs;
using Microsoft.Recognizers.Text;
using System.Linq;
using Choice = Microsoft.Bot.Builder.Prompts.Choices.Choice;
using FoundChoice = Microsoft.Bot.Builder.Prompts.Choices.FoundChoice;

namespace HotelBot
{
    public class ReserveTable : DialogContainer
    {
        public const string Id = "reserveTable";

        private const string TableKey = "Table";

        public static ReserveTable Instance { get; } = new ReserveTable();

        private ReserveTable() : base(Id)
        {
            this.Dialogs.Add(Id, new WaterfallStep[]
            {
                async (dc, args, next) =>
                {
                    // Clear the table information and prompt for the table number.
                    dc.ActiveDialog.State[TableKey] = new TableInfo();

                    var guestInfo = UserState<UserInfo>.Get(dc.Context).Guest;

                    var prompt = $"Welcome {guestInfo.Name}, " +
                        $"which table would you like to reserve?";
                    var choices = new string[] { "1", "2", "3", "4", "5", "6" };
                    await dc.Prompt("choicePrompt", prompt,
                        new ChoicePromptOptions
                        {
                            Choices = choices.Select(s => new Choice { Value = s }).ToList()
                        });
                },
                async (dc, args, next) =>
                {
                    // Save the table number and "sign off".
                    var table = (args["Value"] as FoundChoice).Value;

                    var tableInfo = dc.ActiveDialog.State[TableKey];
                    ((TableInfo)tableInfo).Number = table;

                    await dc.Context.SendActivity(
                        $"Sounds great; we will reserve table number {table} for you.");

                    // Save dialog state to user state and end the dialog.
                    var userState = UserState<UserInfo>.Get(dc.Context);
                    userState.Table = (TableInfo)tableInfo;

                    await dc.End();
                }
            });

            // Define the prompts used in this conversation flow.
            this.Dialogs.Add("choicePrompt", new ChoicePrompt(Culture.English));
        }
    }
}
```

**WakeUp.cs**
```csharp
using Microsoft.Bot.Builder.Core.Extensions;
using Microsoft.Bot.Builder.Dialogs;
using Microsoft.Recognizers.Text;
using System.Collections.Generic;
using System.Linq;
using DateTimeResolution = Microsoft.Bot.Builder.Prompts.DateTimeResult.DateTimeResolution;

namespace HotelBot
{
    public class WakeUp : DialogContainer
    {
        public const string Id = "wakeUp";

        private const string WakeUpKey = "WakeUp";

        public static WakeUp Instance { get; } = new WakeUp();

        private WakeUp() : base(Id)
        {
            this.Dialogs.Add(Id, new WaterfallStep[]
            {
                async (dc, args, next) =>
                {
                    // Clear the wake up call information and prompt for the alarm time.
                   dc.ActiveDialog.State[WakeUpKey] = new WakeUpInfo();

                    var guestInfo = UserState<UserInfo>.Get(dc.Context).Guest;

                    await dc.Prompt("datePrompt", $"Hi {guestInfo.Name}, " +
                        $"what time would you like your alarm set for?");
                },
                async (dc, args, next) =>
                {
                    // Save the alarm time and "sign off".
                    var resolution = (List<DateTimeResolution>)args["Resolution"];

                    var wakeUp = dc.ActiveDialog.State[WakeUpKey];
                    ((WakeUpInfo)wakeUp).Time = resolution?.FirstOrDefault()?.Value;

                    var userState = UserState<UserInfo>.Get(dc.Context);
                    await dc.Context.SendActivity(
                        $"Your alarm is set to {((WakeUpInfo)wakeUp).Time}" +
                        $" for room {userState.Guest.Room}.");

                    // Save dialog state to user state and end the dialog.
                    userState.WakeUp = (WakeUpInfo)wakeUp;

                    await dc.End();
                }
            });

            // Define the prompts used in this conversation flow.
            this.Dialogs.Add("datePrompt", new DateTimePrompt(Culture.English));
        }
    }
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

**reserveTable.js**
```JavaScript
const { DialogContainer, DialogSet, ChoicePrompt } = require('botbuilder-dialogs');

class ReserveTable extends DialogContainer {
    constructor(userState) {
        // Dialog ID of 'reserve_table' will start when class is called in the parent
        super('reserve_table'); 

        // Defining the conversation flow using a waterfall model
        this.dialogs.add('reserve_table', [
            async function (dc, args) {
                // Get the user state from context
                const user = userState.get(dc.context);

                // Create a new local reserveTable state object
                dc.activeDialog.state.reserveTable = {};

                const prompt = `Welcome ${user.guestInfo.userName}, which table would you like to reserve?`;
                const choices = ['1', '2', '3', '4', '5', '6'];
                await dc.prompt('choicePrompt', prompt, choices);
            },
            async function(dc, choice){
                // Save the table number
                dc.activeDialog.state.reserveTable.tableNumber = choice.value;
                await dc.context.sendActivity(`Sounds great, we will reserve table number ${choice.value} for you.`);
                
                // Get the user state from context
                const user = userState.get(dc.context);
                // Persist dialog's state object to the parent's state object
                user.reserveTable = dc.activeDialog.state.reserveTable;

                // End the dialog
                await dc.end();
            }
        ]);

        // Defining the prompt used in this conversation flow
        this.dialogs.add('choicePrompt', new ChoicePrompt());
    }
}
exports.ReserveTable = ReserveTable;
```

**wakeUp.js**
```JavaScript
const { DialogContainer, DialogSet, DatetimePrompt } = require('botbuilder-dialogs');

class WakeUp extends DialogContainer {
    constructor(userState) {
        // Dialog ID of 'wakeup' will start when class is called in the parent
        super('wakeUp');

        this.dialogs.add('wakeUp', [
            async function (dc, args) {
                // Get the user state from context
                const user = userState.get(dc.context); 

                // Create a new local reserveTable state object
                dc.activeDialog.state.wakeUp = {};  
                             
                await dc.prompt('datePrompt', `Hello, ${user.guestInfo.userName}. What time would you like your alarm to be set?`);
            },
            async function (dc, time){
                // Get the user state from context
                const user = userState.get(dc.context);

                // Save the time
                dc.activeDialog.state.wakeUp.time = time[0].value

                await dc.context.sendActivity(`Your alarm is set to ${time[0].value} for room ${user.guestInfo.room}`);
                
                // Save dialog's state object to the parent's state object
                user.wakeUp = dc.activeDialog.state.wakeUp;

                // End the dialog
                await dc.end();
            }]);

        // Defining the prompt used in this conversation flow
        this.dialogs.add('datePrompt', new DatetimePrompt());
    }
}
exports.WakeUp = WakeUp;
```

---

## <a name="add-modular-dialogs-to-a-bot"></a>Hinzufügen von modularen Dialogfeldern zu einem Bot

Die Hauptdatei des Bots verknüpft diese drei modularisierten Dialogfelder in einem Bot.

- Alle drei Dialogfelder werden dem Hauptdialogfeldsatz unseres Bots hinzugefügt.
- Die Tischreservierungs- und Weckrufdialogfelder werden in den Gesprächsfluss des Hauptdialogfelds integriert.
- Wenn eine neue Unterhaltung gestartet wird, gibt es kein aktives Dialogfeld, und die on-turn-Logik des Bots übernimmt die Steuerung.

**On-turn-Handler des Bots**

Sobald sich der Botdurchlauf außerhalb eines aktiven Dialogfelds befindet, wird der Benutzerzustand überprüft.
1. Wenn der Bot bereits über die Informationen des Gasts verfügt, wird sein Hauptdialogfeld gestartet.
1. Andernfalls wird das untergeordnete Eincheckdialogfeld des Hauptdialogfelds gestartet.

**Schritte des Hauptdialogfelds**

1. Den Gast fragen, was er möchte: einen Tisch reservieren oder einen Weckruf festlegen.
1. Starten des entsprechenden untergeordneten Dialogfelds oder Senden einer Nachricht mit dem Inhalt _Eingabe nicht verstanden_ und Fortfahren mit dem nächsten Schritt.
1. Zurückspringen zum Anfang dieses Dialogfelds.


# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Der Dialogfeldfluss wird mithilfe der `Continue`-Methode des Dialogfeldkontexts aktualisiert. Diese Methode führt den nächsten Schritt des Wasserfalls im Dialogfeldstapel aus.

**HotelBot.cs**
```csharp
using System.Collections.Generic;
using System.Threading.Tasks;
using Microsoft.Bot;
using Microsoft.Bot.Builder;
using Microsoft.Bot.Builder.Core.Extensions;
using Microsoft.Bot.Builder.Dialogs;
using Microsoft.Bot.Schema;

namespace HotelBot
{
    public class HotelBot : IBot
    {
        private const string MainMenuId = "mainMenu";

        private DialogSet _dialogs { get; } = ComposeMainDialog();

        /// <summary>
        /// Every Conversation turn for our bot calls this method. 
        /// </summary>
        /// <param name="context">The current turn context.</param>        
        public async Task OnTurn(ITurnContext context)
        {
            if (context.Activity.Type is ActivityTypes.Message)
            {
                // Get the user and conversation state from the turn context.
                var userInfo = UserState<UserInfo>.Get(context);
                var conversationInfo = ConversationState<ConversationInfo>.Get(context);

                // Establish dialog state from the conversation state.
                var dc = _dialogs.CreateContext(context, conversationInfo);

                // Continue any current dialog.
                await dc.Continue();

                // Every turn sends a response, so if no response was sent,
                // then there no dialog is currently active.
                if (!context.Responded)
                {
                    // If we don't yet have the guest's info, start the check-in dialog.
                    if (string.IsNullOrEmpty(userInfo?.Guest?.Name))
                    {
                        await dc.Begin(CheckIn.Id);
                    }
                    // Otherwise, start our bot's main dialog.
                    else
                    {
                        await dc.Begin(MainMenuId);
                    }
                }
            }
        }

        /// <summary>
        /// Composes a main dialog for our bot.
        /// </summary>
        /// <returns>A new main dialog.</returns>
        private static DialogSet ComposeMainDialog()
        {
            var dialogs = new DialogSet();

            dialogs.Add(MainMenuId, new WaterfallStep[]
            {
                async (dc, args, next) =>
                {
                    var menu = new List<string> { "Reserve Table", "Wake Up" };
                    await dc.Context.SendActivity(MessageFactory.SuggestedActions(menu, "How can I help you?"));
                },
                async (dc, args, next) =>
                {
                    // Decide which dialog to start.
                    var result = (args["Activity"] as Activity)?.Text?.Trim().ToLowerInvariant();
                    switch (result)
                    {
                        case "reserve table":
                            await dc.Begin(ReserveTable.Id);
                            break;
                        case "wake up":
                            await dc.Begin(WakeUp.Id);
                            break;
                        default:
                            await dc.Context.SendActivity("Sorry, I don't understand that command. Please choose an option from the list below.");
                            await next();
                            break;
                    }
                },
                async (dc, args, next) =>
                {
                    // Show the main menu again.
                    await dc.Replace(MainMenuId);
                }
            });

            // Add our child dialogs.
            dialogs.Add(CheckIn.Id, CheckIn.Instance);
            dialogs.Add(ReserveTable.Id, ReserveTable.Instance);
            dialogs.Add(WakeUp.Id, WakeUp.Instance);

            return dialogs;
        }
    }
}
```

Schließlich müssen wir die `ConfigureServices`-Methode der `StartUp`-Klasse so aktualisieren, dass eine Verbindung mit unserem Bot hergestellt und Zustandsmiddleware hinzugefügt wird.

**Startup.cs**
```csharp
// This method gets called by the runtime. Use this method to add services to the container.
public void ConfigureServices(IServiceCollection services)
{
    services.AddBot<HotelBot>(options =>
    {
        options.CredentialProvider = new ConfigurationCredentialProvider(Configuration);

        options.Middleware.Add(new CatchExceptionMiddleware<Exception>(async (context, exception) =>
        {
            await context.TraceActivity($"{nameof(HotelBot)} Exception", exception);
            await context.SendActivity("Sorry, it looks like something went wrong!");
        }));

        // Add state management middleware for conversation and user state.
        var path = Path.Combine(Path.GetTempPath(), nameof(HotelBot));
        if (!Directory.Exists(path))
        {
            Directory.CreateDirectory(path);
        }
        IStorage storage = new FileStorage(path);

        options.Middleware.Add(new ConversationState<ConversationInfo>(storage));
        options.Middleware.Add(new UserState<UserInfo>(storage));
    });
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Der Dialogfeldfluss wird mithilfe der `continue`-Methode des Dialogfeldkontexts aktualisiert. Diese Methode führt den nächsten Schritt des Wasserfalls im Dialogfeldstapel aus.

**app.js**
```JavaScript
const {BotFrameworkAdapter, FileStorage, ConversationState, UserState, BotStateSet, MessageFactory} = require("botbuilder");
const {DialogSet} = require("botbuilder-dialogs");
const restify = require("restify");
var azure = require('botbuilder-azure'); 

// Create server
let server = restify.createServer();
server.listen(process.env.port || process.env.PORT || 3978, function () {
    console.log(`${server.name} listening to ${server.url}`);
});

// Create adapter
const adapter = new BotFrameworkAdapter({
    appId: process.env.MICROSOFT_APP_ID,
    appPassword: process.env.MICROSOFT_APP_PASSWORD
});

//Memory Storage
const storage = new FileStorage("c:/temp");
// ConversationState lasts for the entirety of a conversation then gets disposed of
const convoState = new ConversationState(storage);

// UserState persists information about the user across all of the conversations you have with that user
const userState  = new UserState(storage);

adapter.use(new BotStateSet(convoState, userState));

// Listen for incoming requests 
server.post('/api/messages', (req, res) => {
    adapter.processActivity(req, res, async (context) => {
        const isMessage = context.activity.type === 'message';

        // State will store all of your information 
        const convo = convoState.get(context);
        const user = userState.get(context); // userState will not be used in this example

        const dc = dialogs.createContext(context, convo);
        // Continue the current dialog if one is currently active
        await dc.continue(); 

        // Default action
        if (!context.responded && isMessage) {

            // Getting the user info from the state
            const userinfo = userState.get(dc.context); 
            // If guest info is undefined prompt the user to check in
            if(!userinfo.guestInfo){
                await dc.begin('checkInPrompt');
            }else{
                await dc.begin('mainMenu'); 
            }           
        }
    });
});

const dialogs = new DialogSet();
dialogs.add('mainMenu', [
    async function (dc, args) {
        const menu = ["Reserve Table", "Wake Up"];
        await dc.context.sendActivity(MessageFactory.suggestedActions(menu));    
    },
    async function (dc, result){
        // Decide which module to start
        switch(result){
            case "Reserve Table":
                await dc.begin('reservePrompt');
                break;
            case "Wake Up":
                await dc.begin('wakeUpPrompt');
                break;
            default:
                await dc.context.sendActivity("Sorry, i don't understand that command. Please choose an option from the list below.");
                break;            
        }
    },
    async function (dc, result){
        await dc.replace('mainMenu'); // Show the menu again
    }

]);

// Importing the dialogs 
const checkIn = require("./checkIn");
dialogs.add('checkInPrompt', new checkIn.CheckIn(userState));

const reserve_table = require("./reserveTable");
dialogs.add('reservePrompt', new reserve_table.ReserveTable(userState));

const wake_up = require("./wake_up");
dialogs.add('wakeUpPrompt', new wake_up.WakeUp(userState));
```

---
<!-- TODO: These dialogs are not fully modularized, as there are cross dependencies:
    - Importantly, the dialogs need to know details about the bot's user state.
-->

Wie Sie sehen können, werden die modularen Dialogfelder dem Hauptdialogfeld des Bots auf ähnliche Weise wie [Eingabeaufforderungen](bot-builder-prompts.md) einem Dialogfeld hinzugefügt. Sie können dem Hauptdialogfeld beliebig viele untergeordnete Dialogfelder wie gewünscht hinzufügen. Jedes Modul fügt zusätzliche Funktionen und Dienste hinzu, die der Bot Ihren Benutzern anbieten kann.

## <a name="next-steps"></a>Nächste Schritte

Da Sie nun wissen, wie Sie Ihre Botlogik durch die Verwendung von Dialogfeldern modularisieren können, sehen wir uns an, wie Language Understanding (LUIS) verwendet wird, damit Ihr Bot entscheiden kann, wann er die Dialogfelder startet.

> [!div class="nextstepaction"]
> [Verwenden von LUIS für Language Understanding](./bot-builder-howto-v4-luis.md)