---
title: Erfassen von Benutzereingaben mit der Dialogebibliothek | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Benutzer mithilfe der Dialogebibliothek im Bot Builder SDK zur Eingabe auffordern.
keywords: Eingabeaufforderungen, Dialogfelder, AttachmentPrompt, ChoicePrompt, ConfirmPrompt, DatetimePrompt, NumberPrompt, TextPrompt, erneute Eingabeaufforderung, Überprüfung
author: JonathanFingold
ms.author: v-jofing
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 11/02/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 150d5f0a68d897ac278026a7cf36609aca05bb80
ms.sourcegitcommit: 984705927561cc8d6a84f811ff24c8c71b71c76b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2018
ms.locfileid: "50965718"
---
# <a name="use-dialog-library-to-gather-user-input"></a>Erfassen von Benutzereingaben mit der Dialogebibliothek

[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

Das Sammeln von Informationen durch das Stellen von Fragen ist eine der Hauptvorgehensweisen, mit denen ein Bot mit Benutzern interagiert. Dies ist auch direkt möglich, indem die _send activity_-Methode des [turn context](~/v4sdk/bot-builder-basics.md#defining-a-turn)-Objekts verwendet wird und dann die nächste eingehende Nachricht als Antwort verarbeitet wird. Das Bot Builder SDK stellt jedoch eine [Dialogebibliothek](bot-builder-concept-dialog.md) bereit, die Methoden enthält, mit denen das Stellen von Fragen vereinfacht und sichergestellt werden kann, dass die Antwort mit einem bestimmten Datentyp übereinstimmt oder benutzerdefinierte Validierungsregeln erfüllt. In diesem Thema wird ausführlich beschrieben, wie Sie dies erreichen, indem Sie einen Benutzer mithilfe von Eingabeaufforderungsobjekten zur Eingabe von Informationen auffordern.

In diesem Artikel wird beschrieben, wie Sie Eingabeaufforderungen erstellen und in einem Dialog aufrufen.
Informationen dazu, wie Sie Benutzer ohne Dialoge zur Eingabe auffordern, finden Sie unter [Erstellen eigener Eingabeaufforderungen zum Erfassen von Benutzereingaben](bot-builder-primitive-prompts.md).
Informationen zur Verwendung von Dialogen im Allgemeinen finden Sie unter [Verwenden von Dialogen zum Verwalten eines einfachen Konversationsflusses](bot-builder-dialog-manage-conversation-flow.md).

## <a name="prompt-types"></a>Eingabeaufforderungstypen

Im Hintergrund sind Eingabeaufforderungen ein aus zwei Schritten bestehender Dialog. Im ersten Schritt fordert die Eingabeaufforderungen den Benutzer zur Eingabe auf. Im zweiten Schritt wird der gültige Wert zurückgegeben oder der Vorgang mit einer erneuten Eingabeaufforderung neu gestartet.

Die Dialogebibliothek enthält viele einfache Eingabeaufforderungen, die jeweils zum Anfordern einer anderen Art von Antwort verwendet werden.

| Prompt | BESCHREIBUNG | Rückgabe |
|:----|:----|:----|
| _Anlageneingabeaufforderung_ | Fordert den Benutzer auf, eine oder mehrere Anlagen anzufügen (beispielsweise ein Dokument oder Bild). | Eine Sammlung von _attachment_-Objekten. |
| _Auswahleingabeaufforderung_ | Fordert den Benutzer auf, eine Auswahl in einer Reihe von Optionen zu treffen. | Ein _found choice_-Objekt. |
| _Bestätigungseingabeaufforderung_ | Fordert den Benutzer zur Bestätigung auf. | Ein boolescher Wert. |
| _Datums-/Uhrzeiteingabeaufforderung_ | Fordert den Benutzer auf, ein Datum und eine Uhrzeit anzugeben. | Eine Sammlung von Objekten für die _Datums-/Uhrzeitauflösung_. |
| _Zahleneingabeaufforderung_ | Fordert den Benutzer auf, eine Zahl einzugeben. | Ein numerischer Wert. |
| _Texteingabeaufforderung_ | Fordert den Benutzer auf, einen allgemeinen Text einzugeben. | Eine Zeichenfolge. |

Die Bibliothek enthält auch eine _OAuth-Eingabeaufforderung_ zum Abrufen eines _OAuth-Tokens_, mit dem im Auftrag des Benutzers auf eine andere Anwendung zugegriffen wird. Weitere Informationen zur Authentifizierung finden Sie unter [Hinzufügen von Authentifizierung zu Ihrem Bot](bot-builder-authentication.md).

Die einfachen Eingabeaufforderungen können Eingaben in natürlicher Sprache interpretieren (z. B. „zehn“ oder „ein Dutzend“ für eine Zahl oder „morgen“ oder „Freitag um 10 Uhr“ für eine Datums- und Zeitangabe).

## <a name="using-prompts"></a>Verwenden von Eingabeaufforderungen

Ein Dialog kann eine Eingabeaufforderung nur dann verwenden, wenn der Dialog und die Eingabeaufforderung im selben Dialogsatz enthalten sind.

1. Definieren Sie einen Zustandseigenschaftenaccessor für Ihren Dialogzustand.
1. Erstellen Sie einen Dialogsatz.
1. Erstellen Sie Ihre Eingabeaufforderungen, und fügen Sie sie dem Dialogsatz hinzu.
1. Erstellen Sie einen Dialog, in dem Ihre Eingabeaufforderungen verwendet werden, und fügen Sie ihn dem Dialogsatz hinzu.
1. Fügen Sie im Dialog Aufrufe der Eingabeaufforderungen und zum Abrufen der Ergebnisse der Eingabeaufforderung hinzu.

In diesem Artikel wird erläutert, wie Sie Ihre Eingabeaufforderungen erstellen und über einen Wasserfalldialog aufrufen.
Weitere Informationen zu Dialogen im Allgemeinen finden Sie im Artikel zur [Dialogebibliothek](bot-builder-concept-dialog.md).
Eine Beschreibung eines vollständigen Bots, der Dialoge und Eingabeaufforderungen verwendet, finden Sie unter [Verwalten eines einfachen Konversationsflusses mit Dialogen](bot-builder-dialog-manage-conversation-flow.md).

Sie können die gleiche Eingabeaufforderung in mehreren Schritten in einem Dialog und in mehreren Dialogen in demselben Dialogsatz verwenden.
Bei der Initialisierung ordnen Sie einer Eingabeaufforderung jedoch eine benutzerdefinierte Validierung zu.
Wenn eine andere Validierung für den gleichen Eingabeaufforderungstyp erforderlich ist, benötigen Sie daher mehrere Instanzen des Eingabeaufforderungstyps mit jeweils eigenem Validierungscode.

### <a name="create-a-prompt"></a>Erstellen einer Eingabeaufforderung

Um einen Benutzer zur Eingabe aufzufordern, definieren Sie eine Eingabeaufforderung mit einer der integrierten Klassen (beispielsweise _text prompt_), und fügen Sie diese dann Ihrem Dialogsatz hinzu.

* Die Eingabeaufforderung hat eine feste ID. (Bezeichner müssen innerhalb eines Dialogsatzes eindeutig sein.)
* Die Eingabeaufforderung kann über ein benutzerdefiniertes Validierungssteuerelement verfügen. (Siehe [Benutzerdefinierte Validierung](#custom-validation).)
* Bei einigen Eingabeaufforderungen können Sie ein _Standardgebietsschema_ angeben.

In der Regel werden Eingabeaufforderungen beim Initialisieren des Bots erstellt und dem Dialogsatz hinzugefügt. Der Dialogsatz kann dann die ID der Eingabeaufforderung auflösen, wenn der Bot Eingaben vom Benutzer empfängt.

Der folgende Code erstellt beispielsweise zwei Texteingabeaufforderungen und fügt sie einem vorhandenen Dialogsatz hinzu. Die zweite Texteingabeaufforderung verweist auf eine Validierungsmethode, die hier nicht gezeigt wird.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Hier enthält `_dialogs` einen vorhandenen Dialogsatz, und `NameValidator` ist eine Validierungsmethode.

```csharp
_dialogs.Add(new TextPrompt("nickNamePrompt"));
_dialogs.Add(new TextPrompt("namePrompt", NameValidator));
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Hier enthält `this.dialogs` einen vorhandenen Dialogsatz, und `NameValidator` ist eine Validierungsfunktion.

```javascript
this.dialogs.add(new TextPrompt('nickNamePrompt'));
this.dialogs.add(new TextPrompt('namePrompt', NameValidator));
```

---

#### <a name="locales"></a>Locales

Das Gebietsschema wird verwendet, um das sprachspezifische Verhalten der Eingabeaufforderungen **choice**, **confirm**, **date-time** und **number** zu bestimmen. Dabei gilt für jede Eingabe des Benutzers Folgendes:

* Wenn vom Kanal eine _locale_-Eigenschaft in der Nachricht des Benutzers angegeben wurde, wird dieses Gebietsschema verwendet.
* Wenn das _Standardgebietsschema_ der Eingabeaufforderung beim Aufrufen des Konstruktors der Eingabeaufforderung angegeben oder zu einem späteren Zeitpunkt festgelegt wird, wird andernfalls dieses Gebietsschema verwendet.
* Andernfalls wird Englisch („en-us“) als Gebietsschema verwendet.

> [!NOTE]
> Das Gebietsschema ist ein zwei-, drei- oder vierstelliger ISO 639-Code, der eine Sprache oder eine Sprachfamilie darstellt.

### <a name="call-a-prompt-from-a-waterfall-dialog"></a>Aufrufen einer Eingabeaufforderung über einen Wasserfalldialog

Nachdem eine Eingabeaufforderung hinzugefügt wurde, können Sie sie in einem Schritt eines Wasserfalldialogs aufrufen und das Ergebnis der Eingabeaufforderung im folgenden Dialogschritt abrufen.
Um eine Eingabeaufforderung in einem Wasserfallschritt aufzurufen, rufen Sie die _prompt_-Methode des _Wasserfallschritt-Kontextobjekts_ auf. Der erste Parameter ist die ID der zu verwendenden Eingabeaufforderung, und der zweite Parameter enthält die Optionen für die Eingabeaufforderung (z. B. den Text, mit dem der Benutzer zur Eingabe aufgefordert wird).

Angenommen, der Benutzer interagiert mit einem Bot, der Bot enthält einen aktiven Wasserfalldialog, und der nächste Schritt im Dialog verwendet eine Eingabeaufforderung.

1. Wenn der Benutzer eine Nachricht an den Bot sendet, geschieht Folgendes:
   1. Der Turn-Handler des Bots erstellt einen Dialogkontext und ruft die _continue_-Methode auf.
   1. Die Steuerung wird an den nächsten Schritt im aktiven Dialog übergeben, bei dem es sich in diesem Fall um Ihren Wasserfalldialog handelt.
   1. Der Schritt ruft die _prompt_-Methode des Wasserfallschrittkontexts auf, um den Benutzer zur Eingabe aufzufordern.
   1. Der Wasserfallschrittkontext pusht die Eingabeaufforderung in den Stapel und startet ihn.
   1. Die Eingabeaufforderung sendet eine Aktivität an den Benutzer, um ihn zur Eingabe aufzufordern.
1. Wenn der Benutzer die nächste Nachricht an den Bot sendet, geschieht Folgendes:
   1. Der Turn-Handler des Bots erstellt einen Dialogkontext und ruft die _continue_-Methode auf.
   1. Die Steuerung wird an den nächsten Schritt im aktiven Dialog übergeben, bei dem es sich um den nächsten Turn der Eingabeaufforderung handelt.
   1. Die Eingabeaufforderung überprüft die Eingabe des Benutzers.
      * Wenn die Eingabe nicht gültig ist, wird die Eingabeaufforderung neu gestartet, wodurch der Benutzer erneut zur Eingabe aufgefordert wird. Diese Schritte werden im nächsten Turn wiederholt.
      * Andernfalls wird die Eingabeaufforderung beendet und ein _DialogTurnResult_-Objekt an den übergeordneten Dialog zurückgegeben. Die Steuerung wird an den nächsten Schritt des Wasserfalldialogs übergeben, wobei das Ergebnis der Eingabeaufforderung in der _result_-Eigenschaft des Wasserfallschrittkontexts verfügbar ist.

<!--
> [!NOTE]
> A waterfall step delegate takes a _waterfall step context_ parameter and returns a _dialog turn result_.
> A prompt's result is contained within the prompt's return value (a dialog turn result object) when it ends.
> The waterfall dialog provides the return value in the waterfall step context parameter when it calls the next waterfall step.
-->

Wenn eine Eingabeaufforderung einen Wert zurückgibt, wird die _result_-Eigenschaft des Wasserfallschrittkontexts auf den Rückgabewert der Eingabeaufforderung festgelegt.

Dieses Beispiel zeigt Teile der zwei aufeinander folgenden Wasserfallschritte. Im ersten Teil wird die Eingabeaufforderung verwendet, um den Benutzer zur Eingabe seines Namens aufzufordern. Im zweiten Teil wird der Rückgabewert der Eingabeaufforderung abgerufen.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Hier ist `name` die ID einer Texteingabeaufforderung, und `NameStepAsync` und `GreetingStepAsync` sind zwei aufeinander folgende Schrittdelegate eines Wasserfalldialogs.

```csharp
private static async Task<DialogTurnResult> NameStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
{
    // ...

    // Prompt for the user's name.
    return await stepContext.PromptAsync(
        "name",
         new PromptOptions { Prompt = MessageFactory.Text("Please enter your name.") },
         cancellationToken);
}

private static async Task<DialogTurnResult> GreetingStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
{
    // Get the user's name from the prompt result.
    string name = (string)stepContext.Result;
    await stepContext.Context.SendActivityAsync(
        MessageFactory.Text($"Pleased to meet you, {name}."),
         cancellationToken);

    // ...
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Hier ist `name` die ID einer Texteingabeaufforderung, und `nameStep` und `greetingStep` sind zwei aufeinander folgende Schrittfunktionen eines Wasserfalldialogs.

```javascript
async nameStep(step) {
    // ...

    return await step.prompt('name', 'Please enter your name.');
}

async greetingStep(step) {
    // Get the user's name from the prompt result.
    const name = step.result;
    await step.context.sendActivity(`Pleased to meet you, ${name}.`);

    // ...
}
```

---

### <a name="call-a-prompt-from-the-bots-turn-handler"></a>Aufrufen einer Eingabeaufforderung über den Turn-Handler des Bots

Es ist möglich, eine Eingabeaufforderung direkt über den Turn-Handler aufzurufen, indem Sie die _prompt_-Methode des Dialogkontexts verwenden.
Hierzu müssten Sie die _ContinueDialog_-Methode des Dialogkontexts beim nächsten Turn aufrufen und den Rückgabewert (ein _DialogTurnResult_-Objekt) überprüfen. Ein Beispiel dazu finden Sie im Beispiel für die Validierung von Eingabeaufforderungen ([C#](https://aka.ms/cs-prompt-validation-sample) | [JS](https://aka.ms/js-prompt-validation-sample)). Einen alternativen Ansatz finden Sie unter [Erstellen eigener Eingabeaufforderungen zum Erfassen von Benutzereingaben](bot-builder-primitive-prompts.md).

## <a name="prompt-options"></a>Eingabeaufforderungsoptionen

Der zweite Parameter der _prompt_-Methode akzeptiert ein _PromptOptions_-Objekt mit den folgenden Eigenschaften.

| Eigenschaft | BESCHREIBUNG |
| :--- | :--- |
| _prompt_ | Die erste Aktivität, die an den Benutzer gesendet wird, um ihn zur Eingabe aufzufordern. |
| _retryPrompt_ | Die Aktivität, die an den Benutzer gesendet wird, wenn seine erste Eingabe nicht überprüft werden konnte. |
| _choices_ | Eine Liste mit Optionen, aus denen der Benutzer bei einer Auswahleingabeaufforderung wählen kann. |

Im Allgemeinen sind die prompt- und retryPrompt-Eigenschaften Aktivitäten, sie werden jedoch in verschiedenen Programmiersprachen unterschiedlich behandelt.

Sie sollten die erste Eingabeaufforderungsaktivität, die an den Benutzer gesendet werden soll, immer angeben.

Die Angabe einer erneuten Eingabeaufforderung ist sinnvoll, wenn die Benutzereingabe nicht überprüft werden kann, weil sie in einem Format vorliegt, das die Eingabeaufforderung nicht analysieren kann (z. B. „morgen“ für eine Zahleneingabeaufforderung), oder weil die Eingabe ein Validierungskriterium nicht erfüllt. Wenn in diesem Fall keine erneute Eingabeaufforderung angegeben wurde, verwendet die Eingabeaufforderung die erste Eingabeaufforderungsaktivität, um den Benutzer erneut zur Eingabe aufzufordern.

Bei einer Auswahleingabeaufforderung sollten Sie immer die Liste der verfügbaren Optionen bereitstellen.

Das folgende Beispiel veranschaulicht eine Auswahleingabeaufforderung mit allen drei Eigenschaften. Die _FavoriteColor_-Methode wird als Schritt in einem Wasserfalldialog verwendet, und der Dialogsatz enthält sowohl den Wasserfalldialog als auch eine Auswahleingabeaufforderung mit der ID `colorChoice`.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
private static async Task<DialogTurnResult> FavoriteColorAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
{
    // ...

    return await stepContext.PromptAsync(
        "colorChoice",
        new PromptOptions {
            Prompt = MessageFactory.Text("Please choose a color."),
            RetryPrompt = MessageFactory.Text("Sorry, please choose a color from the list."),
            Choices = ChoiceFactory.ToChoices(new List<string> { "blue", "green", "red" }),
        },
        cancellationToken);
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Im JavaScript SDK können Sie eine Zeichenfolge für die `prompt`- und `retryPrompt`-Eigenschaften angeben. Die Eingabeaufforderung konvertiert diese Nachrichtenaktivitäten für Sie.

```javascript
async favoriteColor(step) {
    // ...

    return await step.prompt('colorChoice', {
        prompt: 'Please choose a color:',
        retryPrompt: 'Sorry, please choose a color from the list.',
        choices: [ 'red', 'green', 'blue' ]
    });
}
```

---

## <a name="custom-validation"></a>Benutzerdefinierte Validierung

Sie können die Antwort auf eine Eingabeaufforderung vor der Rückgabe des Werts an den nächsten Schritt des **Wasserfalls** überprüfen. Eine Validierungsfunktion verfügt über einen Parameter für den _Validierungskontext der Eingabeaufforderung_ und gibt einen booleschen Wert zurück, der angibt, ob die Validierung der Eingabe erfolgreich war.

Der Validierungskontext der Eingabeaufforderung enthält die folgenden Eigenschaften:

| Eigenschaft | BESCHREIBUNG |
| :--- | :--- |
| _Context_ | Der aktuelle Turn-Kontext für den Bot. |
| _Recognized_ | Ein _Ergebnis der Eingabeaufforderungserkennung_, das Informationen zu der von der Erkennung verarbeiteten Benutzereingabe enthält. |

Das Ergebnis der Eingabeaufforderungserkennung weist die folgenden Eigenschaften auf:

| Eigenschaft | BESCHREIBUNG |
| :--- | :--- |
| _Erfolgreich_ | Gibt an, ob die Erkennung die Eingabe analysieren konnte. |
| _Wert_ | Der Rückgabewert der Erkennung. Bei Bedarf kann der Validierungscode diesen Wert ändern. |

### <a name="setup"></a>Einrichtung

Vor dem Hinzufügen des Codes für die Validierung müssen einige Einrichtungsschritte ausgeführt werden.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Definieren Sie in der **CS**-Datei Ihres Bots eine innere Klasse für Reservierungsinformationen.

```csharp
public class Reservation
{
    public int Size { get; set; }

    public string Date { get; set; }
}
```

Fügen Sie in **BotAccessors.cs** einen Zustandseigenschaftenaccessor für die Reservierungsdaten hinzu.

```csharp
public class BotAccessors
{
    public BotAccessors(ConversationState conversationState)
    {
        ConversationState = conversationState ?? throw new ArgumentNullException(nameof(conversationState));
    }

    public static string DialogStateAccessorKey { get; } = "BotAccessors.DialogState";
    public static string ReservationAccessorKey { get; } = "BotAccessors.Reservation";

    public IStatePropertyAccessor<DialogState> DialogStateAccessor { get; set; }
    public IStatePropertyAccessor<ReservationBot.Reservation> ReservationAccessor { get; set; }

    public ConversationState ConversationState { get; }
}
```

Aktualisieren Sie `ConfigureServices` in **Startup.cs**, um die Accessoren festzulegen.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ...

    // Create and register state accesssors.
    // Acessors created here are passed into the IBot-derived class on every turn.
    services.AddSingleton<BotAccessors>(sp =>
    {
        // ...

        // Create the custom state accessor.
        // State accessors enable other components to read and write individual properties of state.
        var accessors = new BotAccessors(conversationState)
        {
            DialogStateAccessor = conversationState.CreateProperty<DialogState>(BotAccessors.DialogStateAccessorKey),
            ReservationAccessor = conversationState.CreateProperty<ReservationBot.Reservation>(BotAccessors.ReservationAccessorKey),
        };

        return accessors;
    });
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Für JavaScript sind keine Änderungen am HTTP-Dienstcode erforderlich. Wir können die Datei **index.js** daher unverändert verwenden.

Aktualisieren Sie in **bot.js** die require-Anweisungen, und fügen Sie Bezeichner für die Zustandseigenschaftenaccessoren hinzu.

```javascript
const { ActivityTypes } = require('botbuilder');
const { DialogSet, WaterfallDialog, NumberPrompt, DateTimePrompt, DialogTurnStatus } = require('botbuilder-dialogs');

// Define identifiers for our state property accessors.
const DIALOG_STATE_ACCESSOR = 'dialogStateAccessor';
const RESERVATION_ACCESSOR = 'reservationAccessor';
```

---

Fügen Sie in Ihrer BOT-Datei Bezeichner für die hier verwendeten Dialoge und Eingabeaufforderungen hinzu.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
// Define identifiers for our dialogs and prompts.
private const string ReservationDialog = "reservationDialog";
private const string PartySizePrompt = "partyPrompt";
private const string ReservationDatePrompt = "reservationDatePrompt";
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
// Define identifiers for our dialogs and prompts.
const RESERVATION_DIALOG = 'reservationDialog';
const PARTY_SIZE_PROMPT = 'partySizePrompt';
const RESERVATION_DATE_PROMPT = 'reservationDatePrompt';
```

---

### <a name="define-the-prompts-and-dialogs"></a>Definieren der Eingabeaufforderungen und Dialoge

Erstellen Sie im Konstruktorcode des Bots den Dialogsatz, und fügen Sie die Eingabeaufforderungen sowie den Reservierungsdialog hinzu.
Die benutzerdefinierte Validierung fügen Sie beim Erstellen der Eingabeaufforderungen hinzu. Als Nächstes implementieren Sie die Validierungsfunktionen.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
public ReservationBot(BotAccessors accessors, ILoggerFactory loggerFactory)
{
    // ...
    _accessors = accessors ?? throw new System.ArgumentNullException(nameof(accessors));

    // Create the dialog set and add the prompts, including custom validation.
    _dialogSet = new DialogSet(_accessors.DialogStateAccessor);
    _dialogSet.Add(new NumberPrompt<int>(PartySizePrompt, PartySizeValidatorAsync));
    _dialogSet.Add(new DateTimePrompt(ReservationDatePrompt, DateValidatorAsync));

    // Define the steps of the waterfall dialog and add it to the set.
    WaterfallStep[] steps = new WaterfallStep[]
    {
        PromptForPartySizeAsync,
        PromptForReservationDateAsync,
        AcknowledgeReservationAsync,
    };
    _dialogSet.Add(new WaterfallDialog(ReservationDialog, steps));
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
constructor(conversationState) {
    // Creates our state accessor properties.
    // See https://aka.ms/about-bot-state-accessors to learn more about the bot state and state accessors.
    this.dialogStateAccessor = conversationState.createProperty(DIALOG_STATE_ACCESSOR);
    this.reservationAccessor = conversationState.createProperty(RESERVATION_ACCESSOR);
    this.conversationState = conversationState;

    // Create the dialog set and add the prompts, including custom validation.
    this.dialogSet = new DialogSet(this.dialogStateAccessor);
    this.dialogSet.add(new NumberPrompt(PARTY_SIZE_PROMPT, partySizeValidator));
    this.dialogSet.add(new DateTimePrompt(RESERVATION_DATE_PROMPT, dateValidator));

    // Define the steps of the waterfall dialog and add it to the set.
    this.dialogSet.add(new WaterfallDialog(RESERVATION_DIALOG, [
        this.promptForPartySize.bind(this),
        this.promptForReservationDate.bind(this),
        this.acknowledgeReservation.bind(this),
    ]));
}
```

---

### <a name="implement-validation-code"></a>Implementieren des Codes für die Validierung

Implementieren Sie das Validierungssteuerelement für die Gruppengröße. Wir beschränken Reservierungen auf Gruppen von 6 bis 20 Personen.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
/// <summary>Validates whether the party size is appropriate to make a reservation.</summary>
/// <param name="promptContext">The validation context.</param>
/// <param name="cancellationToken">A cancellation token that can be used by other objects
/// or threads to receive notice of cancellation.</param>
/// <returns>A task that represents the work queued to execute.</returns>
/// <remarks>Reservations can be made for groups of 6 to 20 people.
/// If the task is successful, the result indicates whether the input was valid.</remarks>
private async Task<bool> PartySizeValidatorAsync(
    PromptValidatorContext<int> promptContext,
    CancellationToken cancellationToken)
{
    // Check whether the input could be recognized as an integer.
    if (!promptContext.Recognized.Succeeded)
    {
        await promptContext.Context.SendActivityAsync(
            "I'm sorry, I do not understand. Please enter the number of people in your party.",
            cancellationToken: cancellationToken);
        return false;
    }

    // Check whether the party size is appropriate.
    int size = promptContext.Recognized.Value;
    if (size < 6 || size > 20)
    {
        await promptContext.Context.SendActivityAsync(
            "Sorry, we can only take reservations for parties of 6 to 20.",
            cancellationToken: cancellationToken);
        return false;
    }

    return true;
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
async partySizeValidator(promptContext) {
    // Check whether the input could be recognized as an integer.
    if (!promptContext.recognized.succeeded) {
        await promptContext.context.sendActivity(
            "I'm sorry, I do not understand. Please enter the number of people in your party.");
        return false;
    }
    if (promptContext.recognized.value % 1 != 0) {
        await promptContext.context.sendActivity(
            "I'm sorry, I don't understand fractional people.");
        return false;
    }
    // Check whether the party size is appropriate.
    var size = promptContext.recognized.value;
    if (size < 6 || size > 20) {
        await promptContext.context.sendActivity(
            'Sorry, we can only take reservations for parties of 6 to 20.');
        return false;
    }

    return true;
}
```

---

Die Datums-/Uhrzeiteingabeaufforderung gibt eine Liste oder ein Array der möglichen _Datums-/Uhrzeitauflösungen_ zurück, die der Benutzereingabe entsprechen. Beispielsweise kann 9:00 sowohl 9: 00 Uhr als auch 21: 00 Uhr bedeuten, und „Sonntag“ ist ebenfalls mehrdeutig. Darüber hinaus kann eine Datums-/Uhrzeitauflösung ein Datum, eine Uhrzeit, eine Datums- und Uhrzeitangabe oder einen Bereich darstellen. Die Datums-/Uhrzeiteingabeaufforderung verwendet [Microsoft/Recognizers-Text](https://github.com/Microsoft/Recognizers-Text), um die Eingabe des Benutzers zu analysieren.

Implementieren Sie die Validierung für das Reservierungsdatum. Wir beschränken Reservierungen auf mindestens eine Stunde nach der aktuellen Uhrzeit. Wir übernehmen die erste Auflösung, die unseren Kriterien entspricht, und löschen den Rest.

Dieser Validierungscode ist nicht vollständig. Er eignet sich am besten für Eingaben, die in Bezug auf ein Datum und eine Uhrzeit analysiert werden. Der Code veranschaulicht einige der Optionen für die Validierung einer Datums-/Uhrzeiteingabeaufforderung. Ihre Implementierung hängt von den Informationen ab, die Sie vom Benutzer erfassen möchten.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
/// <summary>Validates whether the reservation date is appropriate.</summary>
/// <param name="promptContext">The validation context.</param>
/// <param name="cancellationToken">A cancellation token that can be used by other objects
/// or threads to receive notice of cancellation.</param>
/// <returns>A task that represents the work queued to execute.</returns>
/// <remarks>Reservations must be made at least an hour in advance.
/// If the task is successful, the result indicates whether the input was valid.</remarks>
private async Task<bool> DateValidatorAsync(
    PromptValidatorContext<IList<DateTimeResolution>> promptContext,
    CancellationToken cancellationToken = default(CancellationToken))
{
    // Check whether the input could be recognized as an integer.
    if (!promptContext.Recognized.Succeeded)
    {
        await promptContext.Context.SendActivityAsync(
            "I'm sorry, I do not understand. Please enter the date or time for your reservation.",
            cancellationToken: cancellationToken);
        return false;
    }

    // Check whether any of the recognized date-times are appropriate,
    // and if so, return the first appropriate date-time.
    DateTime earliest = DateTime.Now.AddHours(1.0);
    DateTimeResolution value = promptContext.Recognized.Value.FirstOrDefault(v =>
        DateTime.TryParse(v.Value ?? v.Start, out DateTime time) && DateTime.Compare(earliest,time) <= 0);
    if (value != null)
    {
        promptContext.Recognized.Value.Clear();
        promptContext.Recognized.Value.Add(value);
        return true;
    }

    await promptContext.Context.SendActivityAsync(
            "I'm sorry, we can't take reservations earlier than an hour from now.",
            cancellationToken: cancellationToken);
    return false;
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
async dateValidator(promptContext) {
// Check whether the input could be recognized as an integer.
if (!promptContext.recognized.succeeded) {
    await promptContext.context.sendActivity(
        "I'm sorry, I do not understand. Please enter the date or time for your reservation.");
    return false;
}

// Check whether any of the recognized date-times are appropriate,
// and if so, return the first appropriate date-time.
const earliest = Date.now() + (60 * 60 * 1000);
let value = null;
promptContext.recognized.value.forEach(candidate => {
    // TODO: update validation to account for time vs date vs date-time vs range.
    const time = new Date(candidate.value || candidate.start);
    if (earliest < time.getTime()) {
        value = candidate;
    }
});
if (value) {
    promptContext.recognized.value = [value];
    return true;
}

await promptContext.context.sendActivity(
    "I'm sorry, we can't take reservations earlier than an hour from now.");
return false;
}
```

---

### <a name="implement-the-dialog-steps"></a>Implementieren der Dialogschritte

Verwenden Sie die Eingabeaufforderungen, die wir dem Dialogsatz hinzugefügt haben. Wir haben den Eingabeaufforderungen bei ihrer Erstellung im Konstruktor des Bots eine Validierung hinzugefügt. Wenn die Eingabeaufforderung den Benutzer erstmals zur Eingabe auffordert, sendet sie die _prompt_-Aktivität aus den bereitgestellten Optionen. Wenn die Validierung fehlschlägt, sendet die Eingabeaufforderung die _retryPrompt_-Aktivität, um den Benutzer zur Eingabe anderer Informationen aufzufordern.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
/// <summary>First step of the main dialog: prompt for party size.</summary>
/// <param name="stepContext">The context for the waterfall step.</param>
/// <param name="cancellationToken">A cancellation token that can be used by other objects
/// or threads to receive notice of cancellation.</param>
/// <returns>A task that represents the work queued to execute.</returns>
/// <remarks>If the task is successful, the result contains information from this step.</remarks>
private async Task<DialogTurnResult> PromptForPartySizeAsync(
    WaterfallStepContext stepContext,
    CancellationToken cancellationToken = default(CancellationToken))
{
    // Prompt for the party size. The result of the prompt is returned to the next step of the waterfall.
    return await stepContext.PromptAsync(
        PartySizePrompt,
        new PromptOptions
        {
            Prompt = MessageFactory.Text("How many people is the reservation for?"),
            RetryPrompt = MessageFactory.Text("How large is your party?"),
        },
        cancellationToken);
}

/// <summary>Second step of the main dialog: record the party size and prompt for the
/// reservation date.</summary>
/// <param name="stepContext">The context for the waterfall step.</param>
/// <param name="cancellationToken">A cancellation token that can be used by other objects
/// or threads to receive notice of cancellation.</param>
/// <returns>A task that represents the work queued to execute.</returns>
/// <remarks>If the task is successful, the result contains information from this step.</remarks>
private async Task<DialogTurnResult> PromptForReservationDateAsync(
    WaterfallStepContext stepContext,
    CancellationToken cancellationToken = default(CancellationToken))
{
    // Record the party size information in the current dialog state.
    int size = (int)stepContext.Result;
    stepContext.Values["size"] = size;

    // Prompt for the reservation date. The result of the prompt is returned to the next step of the waterfall.
    return await stepContext.PromptAsync(
        ReservationDatePrompt,
        new PromptOptions
        {
            Prompt = MessageFactory.Text("Great. When will the reservation be for?"),
            RetryPrompt = MessageFactory.Text("What time should we make your reservation for?"),
        },
        cancellationToken);
}

/// <summary>Third step of the main dialog: return the collected party size and reservation date.</summary>
/// <param name="stepContext">The context for the waterfall step.</param>
/// <param name="cancellationToken">A cancellation token that can be used by other objects
/// or threads to receive notice of cancellation.</param>
/// <returns>A task that represents the work queued to execute.</returns>
/// <remarks>If the task is successful, the result contains information from this step.</remarks>
private async Task<DialogTurnResult> AcknowledgeReservationAsync(
    WaterfallStepContext stepContext,
    CancellationToken cancellationToken = default(CancellationToken))
{
    // Retrieve the reservation date.
    DateTimeResolution resolution = (stepContext.Result as IList<DateTimeResolution>).First();
    string time = resolution.Value ?? resolution.Start;

    // Send an acknowledgement to the user.
    await stepContext.Context.SendActivityAsync(
        "Thank you. We will confirm your reservation shortly.",
        cancellationToken: cancellationToken);

    // Return the collected information to the parent context.
    Reservation reservation = new Reservation
    {
        Date = time,
        Size = (int)stepContext.Values["size"],
    };
    return await stepContext.EndDialogAsync(reservation, cancellationToken);
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
async promptForPartySize(stepContext) {
    // Prompt for the party size. The result of the prompt is returned to the next step of the waterfall.
    return await stepContext.prompt(
        PARTY_SIZE_PROMPT, {
            prompt: 'How many people is the reservation for?',
            retryPrompt: 'How large is your party?'
        });
}

async promptForReservationDate(stepContext) {
    // Record the party size information in the current dialog state.
    stepContext.values.size = stepContext.result;

    // Prompt for the reservation date. The result of the prompt is returned to the next step of the waterfall.
    return await stepContext.prompt(
        RESERVATION_DATE_PROMPT, {
            prompt: 'Great. When will the reservation be for?',
            retryPrompt: 'What time should we make your reservation for?'
        });
}

async acknowledgeReservation(stepContext) {
    // Retrieve the reservation date.
    const resolution = stepContext.result[0];
    const time = resolution.value || resolution.start;

    // Send an acknowledgement to the user.
    await stepContext.context.sendActivity(
        'Thank you. We will confirm your reservation shortly.');

    // Return the collected information to the parent context.
    return await stepContext.endDialog({ date: time, size: stepContext.values.size });
}
```

---

### <a name="update-the-turn-handler"></a>Aktualisieren des Turn-Handlers

Aktualisieren Sie den Turn-Handler des Bots, um den Dialog zu starten und einen Rückgabewert des Dialogs zu akzeptieren, wenn dieser abgeschlossen wird.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
public async Task OnTurnAsync(ITurnContext turnContext, CancellationToken cancellationToken = default(CancellationToken))
{
    switch (turnContext.Activity.Type)
    {
        // On a message from the user:
        case ActivityTypes.Message:

            // Get the current reservation info from state.
            Reservation reservation = await _accessors.ReservationAccessor.GetAsync(
                turnContext, () => null, cancellationToken);

            // Generate a dialog context for our dialog set.
            DialogContext dc = await _dialogSet.CreateContextAsync(turnContext, cancellationToken);

            if (dc.ActiveDialog is null)
            {
                // If there is no active dialog, check whether we have a reservation yet.
                if (reservation is null)
                {
                    // If not, start the dialog.
                    await dc.BeginDialogAsync(ReservationDialog, null, cancellationToken);
                }
                else
                {
                    // Otherwise, send a status message.
                    await turnContext.SendActivityAsync(
                        $"We'll see you {reservation.Date}.",
                        cancellationToken: cancellationToken);
                }
            }
            else
            {
                // Continue the dialog.
                DialogTurnResult dialogTurnResult = await dc.ContinueDialogAsync(cancellationToken);

                // If the dialog completed this turn, record the reservation info.
                if (dialogTurnResult.Status is DialogTurnStatus.Complete)
                {
                    reservation = (Reservation)dialogTurnResult.Result;
                    await _accessors.ReservationAccessor.SetAsync(
                        turnContext,
                        reservation,
                        cancellationToken);

                    // Send a confirmation message to the user.
                    await turnContext.SendActivityAsync(
                        $"Your party of {reservation.Size} is confirmed for {reservation.Date}.",
                        cancellationToken: cancellationToken);
                }
            }

            // Save the updated dialog state into the conversation state.
            await _accessors.ConversationState.SaveChangesAsync(turnContext, false, cancellationToken);
            break;

        // Handle other incoming activity types as appropriate to your bot.
        default:
            await turnContext.SendActivityAsync($"{turnContext.Activity.Type} event detected");
            break;
    }
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
async onTurn(turnContext) {
    switch (turnContext.activity.type) {
        case ActivityTypes.Message:
            // Get the current reservation info from state.
            const reservation = await this.reservationAccessor.get(turnContext, null);

            // Generate a dialog context for our dialog set.
            const dc = await this.dialogSet.createContext(turnContext);

            if (!dc.activeDialog) {
                // If there is no active dialog, check whether we have a reservation yet.
                if (!reservation) {
                    // If not, start the dialog.
                    await dc.beginDialog(RESERVATION_DIALOG);
                }
                else {
                    // Otherwise, send a status message.
                    await turnContext.sendActivity(
                        `We'll see you ${reservation.date}.`);
                }
            }
            else {
                // Continue the dialog.
                const dialogTurnResult = await dc.continueDialog();

                // If the dialog completed this turn, record the reservation info.
                if (dialogTurnResult.status === DialogTurnStatus.complete) {
                    await this.reservationAccessor.set(
                        turnContext,
                        dialogTurnResult.result);

                    // Send a confirmation message to the user.
                    await turnContext.sendActivity(
                        `Your party of ${dialogTurnResult.result.size} is ` +
                        `confirmed for ${dialogTurnResult.result.date}.`);
                }
            }

            // Save the updated dialog state into the conversation state.
            await this.conversationState.saveChanges(turnContext, false);
            break;
        default:
            break;
    }
}
```

---

Weitere Beispiele finden Sie in unserem [Beispielrepository](https://aka.ms/bot-samples-readme).

Sie können ähnliche Verfahren verwenden, um Antworten auf Eingabeaufforderungen für beliebige Eingabeaufforderungstypen zu überprüfen.

## <a name="handling-prompt-results"></a>Behandeln von Eingabeaufforderungsergebnissen

Wozu Sie das Ergebnis der Eingabeaufforderung verwenden, hängt davon ab, warum Sie die Informationen vom Benutzer angefordert haben. Beispiele für Optionen:

* Verwenden Sie die Informationen zum Steuern des Dialogflusses (z. B. wenn ein Benutzer auf eine Bestätigungs- oder Auswahleingabeaufforderung reagiert).
* Speichern Sie die Informationen im Zustand des Dialogs zwischen (beispielsweise das Festlegen eines Werts in der _values_-Eigenschaft des Wasserfallschrittkontexts), und geben Sie die erfassten Informationen dann bei der Beendigung des Dialogs zurück.
* Speichern Sie die Informationen im Botzustand. Dazu müssen Sie den Dialog so entwerfen, dass er Zugriff auf die Zustandseigenschaftenaccessoren des Bots hat.

Unter „Zusätzliche Ressourcen“ finden Sie Themen und Beispiele zu diesen Szenarien.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Verwalten eines einfachen Konversationsflusses](bot-builder-dialog-manage-conversation-flow.md)
* [Verwalten komplexer Konversationsabläufe mit Dialogen](bot-builder-dialog-manage-complex-conversation-flow.md)
* [Erstellen integrierter Dialoge](bot-builder-compositcontrol.md)
* [Dauerhaftes Speichern von Daten in Dialogen](bot-builder-tutorial-persist-user-inputs.md)
* Beispiel für **Eingabeaufforderungen mit mehreren Turns** ([C#](https://aka.ms/cs-multi-prompts-sample) | [JS](https://aka.ms/js-multi-prompts-sample))

## <a name="next-steps"></a>Nächste Schritte

Da Sie jetzt wissen, wie ein Benutzer zu einer Eingabe aufgefordert wird, können Sie den Botcode und die Benutzeroberfläche verbessern, indem Sie verschiedene Gesprächsflüsse über Dialogfelder verwalten.

> [!div class="nextstepaction"]
> [Verwalten komplexer Konversationsabläufe mit Dialogen](bot-builder-dialog-manage-complex-conversation-flow.md)
