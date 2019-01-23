---
title: Wiederverwenden von Dialogen |Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie Ihre Botlogik mithilfe des Dialogcontainers im Bot Framework SDK für Node.js und C# modularisieren können.
keywords: zusammengesetztes Steuerelement, modulare Botlogik
author: v-ducvo
ms.author: v-ducvo
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 11/08/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 3f4b2dd49b738132affd19fea8fd5dbfbd6ff99e
ms.sourcegitcommit: b15cf37afc4f57d13ca6636d4227433809562f8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/11/2019
ms.locfileid: "54224565"
---
# <a name="reuse-dialogs"></a>Wiederverwenden von Dialogen

[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

Stellen Sie sich vor, Sie erstellen einen Hotelbot, der mehrere Aufgaben übernimmt, z.B. die Begrüßung des Benutzers, die Reservierung eines Tischs für das Abendessen, die Bestellung von Speisen, das Einstellen eines Alarms, die Anzeige des aktuellen Wetters und vieles mehr. Sie können jede dieser Aufgaben innerhalb Ihres Bots mit einem Dialogobjekt durchführen. Dies kann aber dazu führen, dass Ihr Dialogcode zu umfangreich und überladen ist.

Sie können Teile Ihres Konversationsflusses in Komponentendialoge umwandeln, um einen großen Dialogsatz in besser verwaltbare Teile zu unterteilen. Auf diese Weise kann Ihr Code optimiert und das Debuggen vereinfacht werden. Außerdem können mehrere Teams gleichzeitig am Bot arbeiten.
Sie können auch eine Bibliothek mit Komponentendialogen erstellen, die für andere Dialogsätze und Bots wiederverwendet werden kann.

In diesem Beispiel erstellen wir einen Hotelbot, in dem Komponentendialoge für die Vorgänge Einchecken, Weckruf und Tischreservierung kombiniert sind.

Dieser Artikel basiert auf den Informationen zum Verwalten von [einfachen](bot-builder-dialog-manage-conversation-flow.md) und [komplexen](bot-builder-dialog-manage-complex-conversation-flow.md) Konversationsflüssen.

## <a name="create-your-project"></a>Erstellen Ihres Projekts

Wir beginnen mit der Echobot-Vorlage und binden die Dialogbibliothek in das Projekt ein.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Wir beginnen mit der **EchoBot**-Vorlage. Entsprechende Anweisungen finden Sie in der [Schnellstartanleitung für .NET](../dotnet/bot-builder-dotnet-sdk-quickstart.md).

Um Dialoge zu verwenden, installieren Sie das `Microsoft.Bot.Builder.Dialogs`NuGet-Paket für Ihr Projekt oder Ihre Projektmappe.
Verweisen Sie anschließend in using-Anweisungen Ihrer Codedateien auf die Dialogbibliothek.

Benennen Sie die Datei **EchoBotWithCounter.cs** in **HotelBot.cs** und die Klasse in **HotelBot** um.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Wir beginnen mit der **Echo**-Vorlage. Entsprechende Anweisungen finden Sie in der [Schnellstartanleitung für JavaScript](../javascript/bot-builder-javascript-quickstart.md).

Die `botbuilder-dialogs`-Bibliothek kann von npm heruntergeladen werden. Um die `botbuilder-dialogs`-Bibliothek zu installieren, führen Sie den folgenden npm-Befehl aus:

```cmd
npm install --save botbuilder-dialogs
```

---

## <a name="managing-state"></a>Verwalten des Zustands

Es gibt viele Möglichkeiten, um Zustandsverwaltung für einen Bot einzurichten, der zusammengesetzte Dialogfelder verwendet. In diesem Bot gibt jeder Komponentendialog ein Objekt als Dialogergebnis zurück. Im aufrufenden Kontext werden die zurückgegebenen Werte verwaltet. Weitere Informationen zur Zustandsverwaltung finden Sie unter [Speichern des Zustands mit Unterhaltungs- und Benutzereigenschaften](bot-builder-howto-v4-state.md).

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Mit jedem Dialog werden einige Informationen gesammelt, und über den Turn-Handler des Bots oder das Hauptmenü werden diese Informationen im Benutzerzustand gespeichert. Wir definieren Komponentendialoge für das Einchecken, Reservieren eines Tischs und Festlegen eines Weckrufs. Es wird jeweils ein Objekt einer entsprechenden Klasse zurückgegeben. Fügen Sie die folgenden Klassen Ihrem Projekt jeweils als neues C#-Klassenmodul hinzu.

```csharp
/// <summary>
/// User state information.
/// </summary>
public class UserInfo
{
    public GuestInfo Guest { get; set; }
    public TableInfo Table { get; set; }
    public WakeUpInfo WakeUp { get; set; }
}

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
```

Benennen Sie **EchoBotAccessors.cs** in **BotAccessors.cs** um, und aktualisieren Sie den Klassennamen in **BotAccessors**.

Aktualisieren Sie die Datei anschließend mit dem folgenden Code. Wir benötigen Accessoren sowohl für den Dialogzustand als auch für die gesammelten Informationen zum Benutzer.

```csharp
using System;
using Microsoft.Bot.Builder;
using Microsoft.Bot.Builder.Dialogs;

/// <summary>
/// Contains the state objects and the state property accessors for the bot.
/// </summary>
public class BotAccessors
{
    // The property accessor keys to use.
    public const string UserInfoAccessorName = "HotelBot.UserInfo";
    public const string DialogStateAccessorName = "HotelBot.DialogState";

    /// <summary>
    /// Initializes a new instance of the <see cref="BotAccessors"/> class.
    /// Contains the <see cref="ConversationState"/> and associated <see cref="IStatePropertyAccessor{T}"/>.
    /// </summary>
    /// <param name="conversationState">The state object that stores the counter.</param>
    public BotAccessors(ConversationState conversationState, UserState userState)
    {
        ConversationState = conversationState ?? throw new ArgumentNullException(nameof(conversationState));
        UserState = userState ?? throw new ArgumentNullException(nameof(userState));
    }

    /// <summary>Gets or sets the state property accessor for the user information we're tracking.</summary>
    /// <value>Accessor for user information.</value>
    public IStatePropertyAccessor<UserInfo> UserInfoAccessor { get; set; }

    /// <summary>Gets or sets the state property accessor for the dialog state.</summary>
    /// <value>Accessor for the dialog state.</value>
    public IStatePropertyAccessor<DialogState> DialogStateAccessor { get; set; }

    /// <summary>Gets the conversation state for the bot.</summary>
    /// <value>The conversation state for the bot.</value>
    public ConversationState ConversationState { get; }

    /// <summary>Gets the user state for the bot.</summary>
    /// <value>The user state for the bot.</value>
    public UserState UserState { get; }
}
```

Aktualisieren Sie in der Datei **Startup.cs** den Code in der `ConfigureServices`-Methode, mit der der Zustand und die Zustandseigenschaftenaccessoren des Bots eingerichtet werden.

```csharp
using Microsoft.Bot.Builder.Dialogs;

public void ConfigureServices(IServiceCollection services)
{
    services.AddBot<HotelBot>(options =>
    {
        //...

        // The Memory Storage used here is for local bot debugging only. When the bot
        // is restarted, everything stored in memory will be gone.
        IStorage dataStore = new MemoryStorage();

        // Create conversation and user state objects.
        options.State.Add(new ConversationState(dataStore));
        options.State.Add(new UserState(dataStore));
    });

    // Create and register state accessors.
    // Accessors created here are passed into the IBot-derived class on every turn.
    services.AddSingleton<BotAccessors>(sp =>
    {
        var options = sp.GetRequiredService<IOptions<BotFrameworkOptions>>().Value;
        var conversationState = options.State.OfType<ConversationState>().FirstOrDefault();
        var userState = options.State.OfType<UserState>().FirstOrDefault();

        // Create the custom state accessor.
        // State accessors enable other components to read and write individual properties of state.
        var accessors = new BotAccessors(conversationState, userState)
        {
            UserInfoAccessor = userState.CreateProperty<UserInfo>(BotAccessors.UserInfoAccessorName),
            DialogStateAccessor = conversationState.CreateProperty<DialogState>(BotAccessors.DialogStateAccessorName),
        };

        return accessors;
    });
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Mit jedem Dialog werden einige Informationen gesammelt, und über den Turn-Handler des Bots oder das Hauptmenü werden diese Informationen im Benutzerzustand gespeichert. Wir definieren Komponentendialoge für das Einchecken, Reservieren eines Tischs und Festlegen eines Weckrufs. Es wird jeweils ein Objekt mit den entsprechenden Eigenschaften zurückgegeben. Wir aggregieren diese Eigenschaften im Benutzerzustand des Bots.

Aktualisieren Sie in der Datei **bot.js** Ihren Botkonstruktor, um Zustandseigenschaftenaccessoren zum Nachverfolgen des Benutzerzustands und des Dialogzustands zu erstellen.

```javascript
// Define the identifiers for our state property accessors.
const DIALOG_STATE_PROPERTY = 'dialogStatePropertyAccessor';
const USER_INFO_PROPERTY = 'userInfoPropertyAccessor';

constructor(conversationState, userState) {
    // Record the conversation and user state management objects.
    this.conversationState = conversationState;
    this.userState = userState;

    // Create our state property accessors.
    this.dialogStateAccessor = conversationState.createProperty(DIALOG_STATE_PROPERTY);
    this.userInfoAccessor = userState.createProperty(USER_INFO_PROPERTY);
}
```

Aktualisieren Sie in der Datei **index.js** die importierten Klassen aus der `botbuilder`-Bibliothek und den Code, mit dem die Zustandsobjekte und Ihr Bot erstellt werden:

```javascript
// Import required bot services.
const { BotFrameworkAdapter, MemoryStorage, ConversationState, UserState } = require('botbuilder');
```

```javascript
// Define state store for your bot.
const memoryStorage = new MemoryStorage();

// Create conversation and user state with in-memory storage provider.
const conversationState = new ConversationState(memoryStorage);
const userState = new UserState(memoryStorage);

// Create the bot.
const myBot = new MyBot(conversationState, userState);
```

---

## <a name="define-the-check-in-component-dialog"></a>Definieren des Dialogs für das Einchecken

Zunächst starten wir mit einem einfachen Eincheckdialogfeld, das den Benutzer nach seinem Namen und dem gewünschten Zimmertyp fragt. Wir erstellen eine `CheckInDialog`-Klasse, mit der `ComponentDialog` erweitert wird. Diese Klasse verfügt über einen Konstruktor, mit dem der Name des Stammdialogs definiert wird. Wir definieren ihn als Wasserfalldialog mit drei Schritten.

Dies sind die Schritte für den Dialog zum Einchecken.

1. Fragen nach dem Namen des Gasts.
1. Fragen nach dem gewünschten Zimmertyp.
1. Senden einer Bestätigungsnachricht und Beenden des Dialogfelds.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Fügen Sie Ihrem Projekt eine `CheckInDialog`-Klasse hinzu, indem Sie den unten angegebenen Code verwenden.

Im gesamten Dialog kann in ein lokales Zustandsobjekt geschrieben werden, auf das über die `Values`-Eigenschaft des Schrittkontexts zugegriffen werden kann. Nachdem das Dialogfeld abgeschlossen wurde, wird das lokale Zustandsobjekt verworfen. Daher geben wir einen Wert aus dem Dialog zurück, der die Informationen zum Gast enthält.

```csharp
using System.Threading;
using System.Threading.Tasks;
using Microsoft.Bot.Builder;
using Microsoft.Bot.Builder.Dialogs;

public class CheckInDialog : ComponentDialog
{
    private const string GuestKey = nameof(CheckInDialog);
    private const string TextPrompt = "textPrompt";

    // You can start this from the parent using the dialog's ID.
    public CheckInDialog(string id)
        : base(id)
    {
        InitialDialogId = Id;

        // Define the prompts used in this conversation flow.
        AddDialog(new TextPrompt(TextPrompt));

        // Define the conversation flow using a waterfall model.
        WaterfallStep[] waterfallSteps = new WaterfallStep[]
        {
            NameStepAsync,
            RoomStepAsync,
            FinalStepAsync,
        };
        AddDialog(new WaterfallDialog(Id, waterfallSteps));
    }

    private static async Task<DialogTurnResult> NameStepAsync(
        WaterfallStepContext step,
        CancellationToken cancellationToken = default(CancellationToken))
    {
        // Clear the guest information and prompt for the guest's name.
        step.Values[GuestKey] = new GuestInfo();
        return await step.PromptAsync(
            TextPrompt,
            new PromptOptions
            {
                Prompt = MessageFactory.Text("What is your name?"),
            },
            cancellationToken);
    }

    private static async Task<DialogTurnResult> RoomStepAsync(
        WaterfallStepContext step,
        CancellationToken cancellationToken = default(CancellationToken))
    {
        // Save the name and prompt for the room number.
        string name = step.Result as string;
        ((GuestInfo)step.Values[GuestKey]).Name = name;
        return await step.PromptAsync(
            TextPrompt,
            new PromptOptions
            {
                Prompt = MessageFactory.Text($"Hi {name}. What room will you be staying in?"),
            },
            cancellationToken);
    }

    private static async Task<DialogTurnResult> FinalStepAsync(
        WaterfallStepContext step,
        CancellationToken cancellationToken = default(CancellationToken))
    {
        // Save the room number and "sign off".
        string room = step.Result as string;
        ((GuestInfo)step.Values[GuestKey]).Room = room;

        await step.Context.SendActivityAsync(
            "Great, enjoy your stay!",
            cancellationToken: cancellationToken);

        // End the dialog, returning the guest info.
        return await step.EndDialogAsync(
            (GuestInfo)step.Values[GuestKey],
            cancellationToken);
    }
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Erstellen Sie die Datei **checkInDialog.js**, und fügen Sie sie einer `CheckInDialog`-Klasse hinzu, mit der `ComponentDialog` erweitert wird.

Im gesamten Dialog kann in ein lokales Zustandsobjekt geschrieben werden, auf das über die `values`-Eigenschaft des Schrittkontexts zugegriffen werden kann. Nachdem das Dialogfeld abgeschlossen wurde, wird das lokale Zustandsobjekt verworfen. Daher geben wir einen Wert aus dem Dialog zurück, der die Informationen zum Gast enthält.

```JavaScript
const { ComponentDialog, TextPrompt, WaterfallDialog } = require('botbuilder-dialogs');

class CheckInDialog extends ComponentDialog {
    constructor(dialogId) {
        super(dialogId);

        // ID of the child dialog that should be started anytime the component is started.
        this.initialDialogId = dialogId;

        // Define the prompts used in this conversation flow.
        this.addDialog(new TextPrompt('textPrompt'));

        // Define the conversation flow using a waterfall model.
        this.addDialog(new WaterfallDialog(dialogId, [
            async function (step) {
                // Clear the guest information and prompt for the guest's name.
                step.values.guestInfo = {};
                return await step.prompt('textPrompt', "What is your name?");
            },
            async function (step) {
                // Save the name and prompt for the room number.
                step.values.guestInfo.userName = step.result;
                return await step.prompt('textPrompt', `Hi ${step.result}. What room will you be staying in?`);
            },
            async function (step) {
                // Save the room number and "sign off".
                step.values.guestInfo.roomNumber = step.result;
                await step.context.sendActivity(`Great! Enjoy your stay in room ${step.result}!`);

                // End the dialog, returning the guest info.
                return await step.endDialog(step.values.guestInfo);
            }
        ]));
    }
}

exports.CheckInDialog = CheckInDialog;
```

---

## <a name="define-the-reserve-table-and-wake-up-component-dialogs"></a>Definieren der Komponentendialoge für Tischreservierung und Weckruf

Ein Vorteil der Verwendung eines Komponentendialogs ist die Möglichkeit, unterschiedliche Dialoge zusammen zu verwenden. Da jedes `DialogSet`-Objekt eine exklusive Sammlung von `dialogs` verwaltet, kann das Freigeben von oder Angeben von Querverweisen für `dialogs` nicht auf einfache Weise ausgeführt werden. Hier kommt das Komponentendialogfeld ins Spiel. Sie können komplexe bzw. komplizierte Aspekte des Konversationsflusses in Komponentendialogen kapseln, um die Verwaltung und Wartung von Dialogen zu vereinfachen. Wir erstellen nun die anderen beiden Komponentendialoge: einen mit der Frage, welchen Tisch der Benutzer für das Abendessen reservieren möchte, und einen zum Erstellen eines Weckrufs. Wir verwenden wieder eine separate Klasse für jeden Dialog, und mit jedem Dialog wird das Hauptelement `ComponentDialog` erweitert.

Wir entwerfen die Dialoge so, dass Gastinformationen in den Dialogoptionen akzeptiert werden, wenn sie gestartet werden.

Dies sind die Schritte für den Dialog zum Reservieren eines Tischs:

1. Fragen nach dem zu reservierenden Tisch.
1. Senden einer Bestätigungsnachricht und Abschließen des Dialogs, Zurückgeben der Tischnummer

Dies sind die Schritte für den Dialog zum Festlegen der Weckzeit:

1. Fragen nach der gewünschten Weckzeit, die festgelegt werden soll.
1. Senden einer Bestätigungsnachricht und Abschließen des Dialogs, Zurückgeben der Weckzeit

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Fügen Sie Ihrem Projekt eine `ReserveTableDialog`-Klasse hinzu, indem Sie den unten angegebenen Code verwenden.

Wir erhalten den Namen des Gasts aus dem Objekt mit den Optionen, das beim Starten des Dialogs übergeben wird. Wir geben dann einen Wert aus dem Dialog zurück, der dieses Mal die Tischnummer enthält.

```csharp
using System.Threading;
using System.Threading.Tasks;
using Microsoft.Bot.Builder;
using Microsoft.Bot.Builder.Dialogs;
using Microsoft.Bot.Builder.Dialogs.Choices;

public class ReserveTableDialog : ComponentDialog
{
    private const string TablePrompt = "choicePrompt";

    public ReserveTableDialog(string id)
        : base(id)
    {
        InitialDialogId = Id;

        // Define the prompts used in this conversation flow.
        AddDialog(new ChoicePrompt(TablePrompt));

        // Define the conversation flow using a waterfall model.
        WaterfallStep[] waterfallSteps = new WaterfallStep[]
        {
                TableStepAsync,
                FinalStepAsync,
        };
        AddDialog(new WaterfallDialog(Id, waterfallSteps));
    }

    private static async Task<DialogTurnResult> TableStepAsync(
        WaterfallStepContext step,
        CancellationToken cancellationToken = default(CancellationToken))
    {
        string greeting = step.Options is GuestInfo guest
                && !string.IsNullOrWhiteSpace(guest?.Name)
                ? $"Welcome {guest.Name}" : "Welcome";

        string prompt = $"{greeting}, How many diners will be at your table?";
        string[] choices = new string[] { "1", "2", "3", "4", "5", "6" };
        return await step.PromptAsync(
            TablePrompt,
            new PromptOptions
            {
                Prompt = MessageFactory.Text(prompt),
                Choices = ChoiceFactory.ToChoices(choices),
            },
            cancellationToken);
    }

    private static async Task<DialogTurnResult> FinalStepAsync(
        WaterfallStepContext step,
        CancellationToken cancellationToken = default(CancellationToken))
    {
        // ChoicePrompt returns a FoundChoice object.
        string table = (step.Result as FoundChoice).Value;

        // Send a confirmation message.
        await step.Context.SendActivityAsync(
            $"Sounds great;  we will reserve a table for you for {table} diners.",
            cancellationToken: cancellationToken);

        // End the dialog, returning the table info.
        return await step.EndDialogAsync(
            new TableInfo { Number = table },
            cancellationToken);
    }
}
```

Fügen Sie Ihrem Projekt eine `SetAlarmDialog`-Klasse hinzu, indem Sie den unten angegebenen Code verwenden.

Wir erhalten die Zimmernummer des Gasts aus dem Objekt mit den Optionen, das beim Starten des Dialogs übergeben wird. Anschließend geben wir einen Wert aus dem Dialog zurück, der die Uhrzeit enthält, für die ein Weckruf festgelegt werden soll.

```csharp
using System.Collections.Generic;
using System.Linq;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.Bot.Builder;
using Microsoft.Bot.Builder.Dialogs;

public class SetAlarmDialog : ComponentDialog
{
    private const string AlarmPrompt = "dateTimePrompt";

    public SetAlarmDialog(string id)
        : base(id)
    {
        InitialDialogId = Id;

        // Define the prompts used in this conversation flow.
        // Ideally, we'd add validation to this prompt.
        AddDialog(new DateTimePrompt(AlarmPrompt));

        // Define the conversation flow using a waterfall model.
        WaterfallStep[] waterfallSteps = new WaterfallStep[]
        {
                AlarmStepAsync,
                FinalStepAsync,
        };

        AddDialog(new WaterfallDialog(Id, waterfallSteps));
    }

    private static async Task<DialogTurnResult> AlarmStepAsync(
        WaterfallStepContext step,
        CancellationToken cancellationToken = default(CancellationToken))
    {
        string greeting = step.Options is GuestInfo guest
                && !string.IsNullOrWhiteSpace(guest?.Name)
                ? $"Hi {guest.Name}" : "Hi";

        string prompt = $"{greeting}. When would you like your alarm set for?";
        return await step.PromptAsync(
            AlarmPrompt,
            new PromptOptions { Prompt = MessageFactory.Text(prompt) },
            cancellationToken);
    }

    private static async Task<DialogTurnResult> FinalStepAsync(
        WaterfallStepContext step,
        CancellationToken cancellationToken = default(CancellationToken))
    {
        // Ambiguous responses can generate multiple results.
        var resolution = (step.Result as IList<DateTimeResolution>)?.FirstOrDefault();

        // Time ranges have a start and no value.
        var alarm = resolution.Value ?? resolution.Start;
        string roomNumber = (step.Options as GuestInfo)?.Room;

        // Send a confirmation message.
        await step.Context.SendActivityAsync(
            $"Your alarm is set to {alarm} for room {roomNumber}.",
            cancellationToken: cancellationToken);

        // End the dialog, returning the alarm info.
        return await step.EndDialogAsync(
            new WakeUpInfo { Time = alarm },
            cancellationToken);
    }
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Erstellen Sie die Datei **reserveTableDialog.js**, und fügen Sie sie einer `ReserveTableDialog`-Klasse hinzu, mit der `ComponentDialog` erweitert wird.

Wir erhalten den Namen des Gasts aus dem Objekt mit den Optionen, das beim Starten des Dialogs übergeben wird. Wir geben dann einen Wert aus dem Dialog zurück, der dieses Mal die Tischnummer enthält.

```JavaScript
const { ComponentDialog, ChoicePrompt, WaterfallDialog } = require('botbuilder-dialogs');

class ReserveTableDialog extends ComponentDialog {
    constructor(dialogId) {
        super(dialogId);

        // ID of the child dialog that should be started anytime the component is started.
        this.initialDialogId = dialogId;

        // Define the prompts used in this conversation flow.
        this.addDialog(new ChoicePrompt('choicePrompt'));

        // Define the conversation flow using a waterfall model.
        this.addDialog(new WaterfallDialog(dialogId, [
            async function (step) {
                // Welcome the user and ask for their table preference.
                const greeting = step.options && step.options.userName ? `Welcome ${step.options.userName}` : `Welcome`;

                const promptOptions = {
                    prompt: `${greeting}, How many diners will be at your table?`,
                    reprompt: 'That was not a valid choice, please select a table size between 1 and 6 guests.',
                    choices: ['1', '2', '3', '4', '5', '6']
                };
                return await step.prompt('choicePrompt', promptOptions);
            },
            async function (step) {
                const choice = step.result;

                // Send a confirmation message.
                const tableNumber = choice.value;
                await step.context.sendActivity(`Sounds great, we will reserve a table for you for ${tableNumber} diners.`);

                // End the dialog, returning the table info.
                return await step.endDialog({ table: tableNumber });
            }
        ]));
    }
}

exports.ReserveTableDialog = ReserveTableDialog;
```

Erstellen Sie die Datei **setAlarmDialog.js**, und fügen Sie sie einer `SetAlarmDialog`-Klasse hinzu, mit der `ComponentDialog` erweitert wird.

Wir erhalten die Zimmernummer des Gasts aus dem Objekt mit den Optionen, das beim Starten des Dialogs übergeben wird. Anschließend geben wir einen Wert aus dem Dialog zurück, der die Uhrzeit enthält, für die ein Weckruf festgelegt werden soll.

```JavaScript
const { ComponentDialog, DateTimePrompt, WaterfallDialog } = require('botbuilder-dialogs');

class SetAlarmDialog extends ComponentDialog {
    constructor(dialogId) {
        super(dialogId);

        // ID of the child dialog that should be started anytime the component is started.
        this.initialDialogId = dialogId;

        // Define the prompts used in this conversation flow.
        this.dialogs.add(new DateTimePrompt('datePrompt'));

        this.dialogs.add(new WaterfallDialog(dialogId, [
            async function (step) {
                step.values.wakeUp = {};
                if (step.options && step.options.roomNumber) {
                    step.values.roomNumber = step.options.roomNumber;
                }

                const greeting = step.options && step.options.userName ? `Hi ${step.options.userName}` : `Hi`;
                return await step.prompt('datePrompt', `${greeting}. What time would you like your alarm to be set?`);
            },
            async function (step) {
                // Ambiguous responses can generate multiple results.
                const resolution = step.result[0];

                // Time ranges have a start and no value.
                const alarm = resolution.value ? resolution.value : resolution.start;
                const roomNumber = step.values.roomNumber;

                // Send a confirmation message.
                await step.context.sendActivity(`Your alarm is set to ${alarm} for room ${roomNumber}.`);

                // End the dialog, returning the alarm info.
                return await step.endDialog({ alarm: alarm });
            }]));

        // Defining the prompt used in this conversation flow
    }
}

exports.SetAlarmDialog = SetAlarmDialog;
```

---

## <a name="add-the-component-dialogs-to-the-bot"></a>Hinzufügen der Komponentendialoge zum Bot

Nachdem wir die drei Komponentendialoge definiert haben, können wir sie im Bot einsetzen.

- Alle drei Dialogfelder werden dem Hauptdialogfeldsatz unseres Bots hinzugefügt.
- Wenn eine neue Unterhaltung gestartet wird, gibt es kein aktives Dialogfeld, und die on-turn-Logik des Bots übernimmt die Steuerung.
- Falls wir nicht über Gastinformationen zum Benutzer verfügen, starten wir den Dialog für das Einchecken.
- Nachdem wir die Gastinformationen erhalten haben, übernimmt der Hauptdialog, und der Benutzer kann den Dialog für die Tischreservierung oder das Festlegen der Weckzeit wiederholt starten.

Wir aktualisieren die Logik für den on-turn-Handler des Bots.

1. Abrufen des Benutzerzustands
1. Fortsetzen aller aktiven Dialoge
1. Wenn ein Dialog diesen Turn abgeschlossen hat, sollte es der Dialog für das Einchecken sein.
   1. Speichern der Gastinformationen
   1. Starten des Hauptdialogs
1. Wenn der Bot noch keine Nachricht an den Benutzer gesendet hat, wird kein aktiver Dialog ausgeführt.
    1. Wenn Sie nicht über die Gastinformationen verfügen: Starten des Dialogs für das Einchecken
    1. Andernfalls: Starten des Hauptdialogs
1. Speichern aller Zustandsänderungen, die ggf. vorgenommen wurden

Dies sind die Schritte für den Hauptdialog.

1. Den Gast fragen, was er möchte: einen Tisch reservieren oder einen Weckruf festlegen.
1. Starten des entsprechenden untergeordneten Dialogs oder Senden der Nachricht _Eingabe nicht verstanden_ und erneutes Beginnen mit dem ersten Schritt
1. Verarbeiten des Rückgabewerts aus dem untergeordneten Dialog und Neustarten des Hauptdialogs

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Aktualisieren Sie in der Datei **HotelBot.cs** die using-Anweisungen.

```csharp
using System.Collections.Generic;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.Bot.Builder;
using Microsoft.Bot.Builder.Dialogs;
using Microsoft.Bot.Schema;
using Microsoft.Extensions.Logging;
```

Aktualisieren Sie den Initialisierungscode, und definieren Sie den Dialogsatz und den Hauptdialog des Bots.

```csharp
// Define the IDs for the dialogs in the bot's dialog set.
private const string MainDialogId = "mainDialog";
private const string CheckInDialogId = "checkInDialog";
private const string TableDialogId = "tableDialog";
private const string AlarmDialogId = "alarmDialog";

// Define the dialog set for the bot.
private readonly DialogSet _dialogs;

// Define the state accessors and the logger for the bot.
private readonly BotAccessors _accessors;
private readonly ILogger _logger;

/// <summary>
/// Initializes a new instance of the <see cref="HotelBot"/> class.
/// </summary>
/// <param name="accessors">Contains the objects to use to manage state.</param>
/// <param name="loggerFactory">A <see cref="ILoggerFactory"/> that is hooked to the Azure App Service provider.</param>
public HotelBot(BotAccessors accessors, ILoggerFactory loggerFactory)
{
    if (loggerFactory == null)
    {
        throw new System.ArgumentNullException(nameof(loggerFactory));
    }

    _logger = loggerFactory.CreateLogger<HotelBot>();
    _logger.LogTrace($"{nameof(HotelBot)} turn start.");
    _accessors = accessors ?? throw new System.ArgumentNullException(nameof(accessors));

    // Define the steps of the main dialog.
    WaterfallStep[] steps = new WaterfallStep[]
    {
        MenuStepAsync,
        HandleChoiceAsync,
        LoopBackAsync,
    };

    // Create our bot's dialog set, adding a main dialog and the three component dialogs.
    _dialogs = new DialogSet(_accessors.DialogStateAccessor)
        .Add(new WaterfallDialog(MainDialogId, steps))
        .Add(new CheckInDialog(CheckInDialogId))
        .Add(new ReserveTableDialog(TableDialogId))
        .Add(new SetAlarmDialog(AlarmDialogId));
}

private static async Task<DialogTurnResult> MenuStepAsync(
    WaterfallStepContext stepContext,
    CancellationToken cancellationToken = default(CancellationToken))
{
    // Present the user with a set of "suggested actions".
    List<string> menu = new List<string> { "Reserve Table", "Wake Up" };
    await stepContext.Context.SendActivityAsync(
        MessageFactory.SuggestedActions(menu, "How can I help you?"),
        cancellationToken: cancellationToken);
    return Dialog.EndOfTurn;
}

private async Task<DialogTurnResult> HandleChoiceAsync(
    WaterfallStepContext stepContext,
    CancellationToken cancellationToken = default(CancellationToken))
{
    // Get the user's info. (Since the type factory is null, this will throw if state does not yet have a value for user info.)
    UserInfo userInfo = await _accessors.UserInfoAccessor.GetAsync(stepContext.Context, null, cancellationToken);

    // Check the user's input and decide which dialog to start.
    // Pass in the guest info when starting either of the child dialogs.
    string choice = (stepContext.Result as string)?.Trim()?.ToLowerInvariant();
    switch (choice)
    {
        case "reserve table":
            return await stepContext.BeginDialogAsync(TableDialogId, userInfo.Guest, cancellationToken);

        case "wake up":
            return await stepContext.BeginDialogAsync(AlarmDialogId, userInfo.Guest, cancellationToken);

        default:
            // If we don't recognize the user's intent, start again from the beginning.
            await stepContext.Context.SendActivityAsync(
                "Sorry, I don't understand that command. Please choose an option from the list.");
            return await stepContext.ReplaceDialogAsync(MainDialogId, null, cancellationToken);
    }
}

private async Task<DialogTurnResult> LoopBackAsync(
    WaterfallStepContext stepContext,
    CancellationToken cancellationToken = default(CancellationToken))
{
    // Get the user's info. (Because the type factory is null, this will throw if state does not yet have a value for user info.)
    UserInfo userInfo = await _accessors.UserInfoAccessor.GetAsync(stepContext.Context, null, cancellationToken);

    // Process the return value from the child dialog.
    switch (stepContext.Result)
    {
        case TableInfo table:
            // Store the results of the reserve-table dialog.
            userInfo.Table = table;
            await _accessors.UserInfoAccessor.SetAsync(stepContext.Context, userInfo, cancellationToken);
            break;
        case WakeUpInfo alarm:
            // Store the results of the set-wake-up-call dialog.
            userInfo.WakeUp = alarm;
            await _accessors.UserInfoAccessor.SetAsync(stepContext.Context, userInfo, cancellationToken);
            break;
        default:
            // We shouldn't get here, since these are no other branches that get this far.
            break;
    }

    // Restart the main menu dialog.
    return await stepContext.ReplaceDialogAsync(MainDialogId, null, cancellationToken);
}
```

Aktualisieren Sie außerdem den Turn-Handler des Bots, damit der Dialogsatz verwendet wird.

```csharp
public async Task OnTurnAsync(ITurnContext turnContext, CancellationToken cancellationToken = default(CancellationToken))
{
    if (turnContext.Activity.Type == ActivityTypes.Message)
    {
        // Establish dialog state from the conversation state.
        DialogContext dc = await _dialogs.CreateContextAsync(turnContext, cancellationToken);

        // Get the user's info.
        UserInfo userInfo = await _accessors.UserInfoAccessor.GetAsync(turnContext, () => new UserInfo(), cancellationToken);

        // Continue any current dialog.
        DialogTurnResult dialogTurnResult = await dc.ContinueDialogAsync();

        // Process the result of any complete dialog.
        if (dialogTurnResult.Status is DialogTurnStatus.Complete)
        {
            switch (dialogTurnResult.Result)
            {
                case GuestInfo guestInfo:
                    // Store the results of the check-in dialog.
                    userInfo.Guest = guestInfo;
                    await _accessors.UserInfoAccessor.SetAsync(turnContext, userInfo, cancellationToken);
                    break;
                default:
                    // We shouldn't get here, since the main dialog is designed to loop.
                    break;
            }
        }

        // Every dialog step sends a response, so if no response was sent,
        // then no dialog is currently active.
        else if (!turnContext.Responded)
        {
            if (string.IsNullOrEmpty(userInfo.Guest?.Name))
            {
                // If we don't yet have the guest's info, start the check-in dialog.
                await dc.BeginDialogAsync(CheckInDialogId, null, cancellationToken);
            }
            else
            {
                // Otherwise, start our bot's main dialog.
                await dc.BeginDialogAsync(MainDialogId, null, cancellationToken);
            }
        }

        // Save the new turn count into the conversation state.
        await _accessors.ConversationState.SaveChangesAsync(turnContext, false, cancellationToken);
        await _accessors.UserState.SaveChangesAsync(turnContext, false, cancellationToken);
    }
    else
    {
        await turnContext.SendActivityAsync($"{turnContext.Activity.Type} event detected");
    }
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

In der Botdatei **bot.js** müssen wir nicht nur die zu verwendenden Klassen des SDK importieren, sondern auch die Klassen, die wir für unsere Komponentendialoge definiert haben.

```JavaScript
const { ActivityTypes, MessageFactory } = require('botbuilder');
const { DialogSet, WaterfallDialog, Dialog, DialogTurnStatus } = require('botbuilder-dialogs');

// Import our component dialogs.
const { CheckInDialog } = require("./checkInDialog");
const { ReserveTableDialog } = require("./reserveTableDialog");
const { SetAlarmDialog } = require("./setAlarmDialog");
```

Darüber hinaus müssen wir einen Dialogsatz erstellen und diesem alle zu verwendenden Dialoge hinzufügen.

Wir definieren die Wasserfallschritte des Hauptdialogs nicht inline, sondern als Funktionen in der Klasse. Wir verwenden `bind()` in diesen Funktionen, damit `this` in der Funktion richtig aufgelöst wird.

Hier ist unser aktualisierter Botkonstruktor angegeben.

```JavaScript
constructor(conversationState, userState) {
    // Record the conversation and user state management objects.
    this.conversationState = conversationState;
    this.userState = userState;

    // Create our state property accessors.
    this.dialogStateAccessor = conversationState.createProperty(DIALOG_STATE_PROPERTY);
    this.userInfoAccessor = userState.createProperty(USER_INFO_PROPERTY);

    // Create our bot's dialog set, adding a main dialog and the three component dialogs.
    this.dialogs = new DialogSet(this.dialogStateAccessor)
        .add(new CheckInDialog('checkInDialog'))
        .add(new ReserveTableDialog('reserveTableDialog'))
        .add(new SetAlarmDialog('setAlarmDialog'))
        .add(new WaterfallDialog('mainDialog', [
            this.promptForChoice.bind(this),
            this.startChildDialog.bind(this),
            this.saveResult.bind(this)
    ]));
}
```

Fügen Sie unterhalb des Botkonstruktors den folgenden Code hinzu, mit dem Schritte für den Hauptdialog implementiert werden.

```JavaScript
async promptForChoice(step) {
    const menu = ["Reserve Table", "Wake Up"];
    await step.context.sendActivity(MessageFactory.suggestedActions(menu, 'How can I help you?'));
    return Dialog.EndOfTurn;
}

async startChildDialog(step) {
    // Get the user's info.
    const user = await this.userInfoAccessor.get(step.context);
    // Check the user's input and decide which dialog to start.
    // Pass in the guest info when starting either of the child dialogs.
    switch (step.result) {
        case "Reserve Table":
            return await step.beginDialog('reserveTableDialog', user.guestInfo);
            break;
        case "Wake Up":
            return await step.beginDialog('setAlarmDialog', user.guestInfo);
            break;
        default:
            await step.context.sendActivity("Sorry, I don't understand that command. Please choose an option from the list.");
            return await step.replaceDialog('mainDialog');
            break;
    }
}

async saveResult(step) {
    // Process the return value from the child dialog.
    if (step.result) {
        const user = await this.userInfoAccessor.get(step.context);
        if (step.result.table) {
            // Store the results of the reserve-table dialog.
            user.table = step.result.table;
        } else if (step.result.alarm) {
            // Store the results of the set-wake-up-call dialog.
            user.alarm = step.result.alarm;
        }
        await this.userInfoAccessor.set(step.context, user);
    }
    // Restart the main menu dialog.
    return await step.replaceDialog('mainDialog'); // Show the menu again
}
```

Jetzt aktualisieren wir den Turn-Handler des Bots:

```JavaScript
async onTurn(turnContext) {
    if (turnContext.activity.type === ActivityTypes.Message) {
        const user = await this.userInfoAccessor.get(turnContext, {});
        const dc = await this.dialogs.createContext(turnContext);
        const dialogTurnResult = await dc.continueDialog();
        if (dialogTurnResult.status === DialogTurnStatus.complete) {
            user.guestInfo = dialogTurnResult.result;
            await this.userInfoAccessor.set(turnContext, user);
            await dc.beginDialog('mainDialog');
        } else if (!turnContext.responded) {
            if (!user.guestInfo) {
                await dc.beginDialog('checkInDialog');
            } else {
                await dc.beginDialog('mainDialog');
            }
        }
        // Save state changes
        await this.conversationState.saveChanges(turnContext);
        await this.userState.saveChanges(turnContext);
    }
}
```

---

Wie Sie sehen können, werden die Komponentendialoge dem Hauptdialog des Bots auf ähnliche Weise hinzugefügt, wie dies bei [Eingabeaufforderungen](bot-builder-prompts.md) für einen Dialog der Fall ist. Sie können dem Hauptdialogfeld beliebig viele untergeordnete Dialogfelder wie gewünscht hinzufügen. Jedes Modul fügt zusätzliche Funktionen und Dienste hinzu, die der Bot Ihren Benutzern anbieten kann.

## <a name="next-steps"></a>Nächste Schritte

Da Sie nun wissen, wie Sie Komponentendialoge verwenden, sehen wir uns als Nächstes an, wie Language Understanding (LUIS) Ihren Bot beim Treffen der Entscheidung, wann die Dialoge gestartet werden sollen, unterstützen kann.

> [!div class="nextstepaction"]
> [Verwenden von LUIS für Language Understanding](./bot-builder-howto-v4-luis.md)
