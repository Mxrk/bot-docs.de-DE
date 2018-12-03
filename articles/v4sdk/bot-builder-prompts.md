---
title: Erfassen von Benutzereingaben mit einer Dialogaufforderung | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Benutzer mithilfe der Dialogebibliothek im Bot Builder SDK zur Eingabe auffordern.
keywords: Eingabeaufforderungen, Eingabeaufforderung, Benutzereingabe, Dialoge, AttachmentPrompt, ChoicePrompt, ConfirmPrompt, DatetimePrompt, NumberPrompt, TextPrompt, erneute Eingabeaufforderung, Validierung
author: JonathanFingold
ms.author: v-jofing
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 11/21/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 1c69b438c739ac9c47d40e53f1300a4773fc1a1d
ms.sourcegitcommit: 6cb37f43947273a58b2b7624579852b72b0e13ea
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/22/2018
ms.locfileid: "52288789"
---
# <a name="gather-user-input-using-a-dialog-prompt"></a>Erfassen von Benutzereingaben mit einer Dialogaufforderung

[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

Das Sammeln von Informationen durch das Stellen von Fragen ist eine der Hauptvorgehensweisen, mit denen ein Bot mit Benutzern interagiert. Die Dialogbibliothek (*dialogs*) vereinfacht das Stellen von Fragen und das Überprüfen der Antworten, um sicherzustellen, dass sie einen bestimmten Datentyp aufweisen oder benutzerdefinierte Validierungsregeln erfüllen. In diesem Thema wird ausführlich beschrieben, wie Sie Eingabeaufforderungen für einen Wasserfalldialog erstellen und aufrufen.

## <a name="prerequisites"></a>Voraussetzungen
- Der Code in diesem Artikel basiert auf dem Beispiel für die Dialogaufforderung (dialog-prompt). Sie benötigen eine Kopie des Beispiels im [C#](https://aka.ms/dialog-prompt-cs)- oder [JS](https://aka.ms/dialog-prompt-js)-Format.
- Sie müssen über Grundkenntnisse für die [Dialogbibliothek](bot-builder-concept-dialog.md) verfügen und mit der [Verwaltung von Konversationen](bot-builder-dialog-manage-conversation-flow.md) vertraut sein. 
- [Bot Framework Emulator](https://github.com/Microsoft/BotFramework-Emulator) für die Durchführung von Tests.

## <a name="about-prompt-types"></a>Informationen zu Eingabeaufforderungstypen

Im Hintergrund sind Eingabeaufforderungen ein aus zwei Schritten bestehender Dialog. Im ersten Schritt fordert die Eingabeaufforderungen den Benutzer zur Eingabe auf. Im zweiten Schritt wird der gültige Wert zurückgegeben oder der Vorgang mit einer erneuten Eingabeaufforderung neu gestartet. Die Dialogebibliothek enthält viele einfache Eingabeaufforderungen, die jeweils zum Anfordern einer anderen Art von Antwort verwendet werden. Die einfachen Eingabeaufforderungen können Eingaben in natürlicher Sprache interpretieren (z. B. „zehn“ oder „ein Dutzend“ für eine Zahl oder „morgen“ oder „Freitag um 10 Uhr“ für eine Datums- und Zeitangabe).

| Prompt | BESCHREIBUNG | Rückgabe |
|:----|:----|:----|
| _Anlageneingabeaufforderung_ | Fordert den Benutzer auf, eine oder mehrere Anlagen anzufügen (beispielsweise ein Dokument oder Bild). | Eine Sammlung von _attachment_-Objekten. |
| _Auswahleingabeaufforderung_ | Fordert den Benutzer auf, eine Auswahl in einer Reihe von Optionen zu treffen. | Ein _found choice_-Objekt. |
| _Bestätigungseingabeaufforderung_ | Fordert den Benutzer zur Bestätigung auf. | Ein boolescher Wert. |
| _Datums-/Uhrzeiteingabeaufforderung_ | Fordert den Benutzer auf, ein Datum und eine Uhrzeit anzugeben. | Eine Sammlung von Objekten für die _Datums-/Uhrzeitauflösung_. |
| _Zahleneingabeaufforderung_ | Fordert den Benutzer auf, eine Zahl einzugeben. | Ein numerischer Wert. |
| _Texteingabeaufforderung_ | Fordert den Benutzer auf, einen allgemeinen Text einzugeben. | Eine Zeichenfolge. |

Um einen Benutzer zur Eingabe aufzufordern, definieren Sie eine Eingabeaufforderung mit einer der integrierten Klassen (beispielsweise _text prompt_), und fügen Sie diese dann Ihrem Dialogsatz hinzu. Eingabeaufforderungen verfügen über feste IDs, die innerhalb eines Dialogsatzes eindeutig sein müssen. Sie können ein benutzerdefiniertes Validierungssteuerelement für jede Eingabeaufforderung verwenden, und für einige Eingabeaufforderungen können Sie ein _Standardgebietsschema_ angeben. 

### <a name="prompt-locale"></a>Gebietsschema der Eingabeaufforderung

Das Gebietsschema wird verwendet, um das sprachspezifische Verhalten der Eingabeaufforderungen **choice**, **confirm**, **date-time** und **number** zu bestimmen. Wenn für den Kanal eine _locale_-Eigenschaft angegeben wurde, wird für alle Eingaben des Benutzers dieser Wert verwendet. Wenn das _Standardgebietsschema_ der Eingabeaufforderung beim Aufrufen des Konstruktors der Eingabeaufforderung angegeben oder zu einem späteren Zeitpunkt festgelegt wird, wird andernfalls dieses Gebietsschema verwendet. Falls keine dieser Angaben vorhanden ist, wird Englisch („en-us“) als Gebietsschema genutzt. Hinweis: Das Gebietsschema ist ein zwei-, drei- oder vierstelliger ISO 639-Code, der eine Sprache oder eine Sprachfamilie darstellt.

## <a name="using-prompts"></a>Verwenden von Eingabeaufforderungen

Ein Dialog kann eine Eingabeaufforderung nur dann verwenden, wenn der Dialog und die Eingabeaufforderung im selben Dialogsatz enthalten sind. Sie können die gleiche Eingabeaufforderung in mehreren Schritten in einem Dialog und in mehreren Dialogen in demselben Dialogsatz verwenden. Bei der Initialisierung ordnen Sie einer Eingabeaufforderung jedoch eine benutzerdefinierte Validierung zu. Um eine andere Validierung für den gleichen Eingabeaufforderungstyp zu verwenden, benötigen Sie mehrere Instanzen des Eingabeaufforderungstyps mit jeweils eigenem Validierungscode.

### <a name="define-a-state-property-accessor-for-the-dialog-state"></a>Definieren eines Zustandseigenschaftenaccessors für den Dialogzustand

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Im Beispiel zur Dialogeingabeaufforderung, das in diesem Artikel verwendet wird, wird der Benutzer zum Eingeben von Reservierungsinformationen aufgefordert. Zum Verwalten der Anzahl von Personen und des Datums definieren wir in der Datei „DialogPromptBot.cs“ eine innere Klasse für die Reservierungsinformationen.

```csharp
public class Reservation
{
    public int Size { get; set; }

    public string Date { get; set; }
}
```

Als Nächstes fügen wir einen Zustandseigenschaftenaccessor für die Reservierungsdaten hinzu.

```csharp
public class DialogPromptBotAccessors
{
    public DialogPromptBotAccessors(ConversationState conversationState)
    {
        ConversationState = conversationState ?? throw new ArgumentNullException(nameof(conversationState));
    }

    public static string DialogStateAccessorKey { get; } = "DialogPromptBotAccessors.DialogState";
    public static string ReservationAccessorKey { get; } = "DialogPromptBotAccessors.Reservation";

    public IStatePropertyAccessor<DialogState> DialogStateAccessor { get; set; }
    public IStatePropertyAccessor<DialogPromptBot.Reservation> ReservationAccessor { get; set; }

    public ConversationState ConversationState { get; }
}
```

In „Startup.cs“ aktualisieren wir die `ConfigureServices`-Methode, um die Accessors festzulegen.

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
            DialogStateAccessor = conversationState.CreateProperty<DialogState>(DialogPromptBotAccessors.DialogStateAccessorKey),
            ReservationAccessor = conversationState.CreateProperty<DialogPromptBot.Reservation>(DialogPromptBotAccessors.ReservationAccessorKey),
        };

        return accessors;
    });
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Für JavaScript sind keine Änderungen am HTTP-Dienstcode erforderlich. Wir können die Datei „index.js“ daher unverändert verwenden.

In „bot.js“ fügen wir die `require`-Anweisungen ein, die für den Bot mit der Dialogeingabeaufforderung benötigt werden. 
```javascript
const { ActivityTypes } = require('botbuilder');
const { DialogSet, WaterfallDialog, NumberPrompt, DateTimePrompt, ChoicePrompt, DialogTurnStatus } = require('botbuilder-dialogs');
```

Fügen Sie Bezeichner für die Zustandseigenschaftenaccessoren, Dialoge und Eingabeaufforderungen hinzu.

```javascript
// Define identifiers for state property accessors.
const DIALOG_STATE_ACCESSOR = 'dialogStateAccessor';
const RESERVATION_ACCESSOR = 'reservationAccessor';

// Define identifiers for dialogs and prompts.
const RESERVATION_DIALOG = 'reservationDialog';
const PARTY_SIZE_PROMPT = 'partySizePrompt';
const LOCATION_PROMPT = 'locationPrompt';
const RESERVATION_DATE_PROMPT = 'reservationDatePrompt';
```

---

### <a name="create-a-dialog-set-and-prompts"></a>Erstellen eines Dialogsatzes und von Eingabeaufforderungen

In der Regel erstellen Sie Eingabeaufforderungen beim Initialisieren des Bots und fügen diese dem Dialogsatz hinzu. Der Dialogsatz kann dann die ID der Eingabeaufforderung auflösen, wenn der Bot Eingaben vom Benutzer empfängt.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Definieren Sie in der `DialogPromptBot`-Klasse Bezeichner für Dialoge, Eingabeaufforderungen und den Dialogsatz.
```csharp
private const string ReservationDialog = "reservationDialog";
private const string PartySizePrompt = "partyPrompt";
private const string LocationPrompt = "locationPrompt";
private const string ReservationDatePrompt = "reservationDatePrompt";

private readonly DialogSet _dialogSet;
```

Erstellen Sie im Konstruktor des Bots den Dialogsatz, und fügen Sie die Eingabeaufforderungen sowie den Reservierungsdialog hinzu. Wir fügen die benutzerdefinierte Validierung ein, wenn wir die Eingabeaufforderungen erstellen, und implementieren die Validierungsfunktionen später.

```csharp
// The following code creates prompts and adds them to an existing dialog set. The DialogSet contains all the dialogs that can 
// be used at runtime. The prompts also references a validation method is not shown here.

public DialogPromptBot(DialogPromptBotAccessors accessors, ILoggerFactory loggerFactory)
{
   // ...

    // Create the dialog set and add the prompts, including custom validation.
    _dialogSet = new DialogSet(_accessors.DialogStateAccessor);
    _dialogSet.Add(new NumberPrompt<int>(PartySizePrompt, PartySizeValidatorAsync));
    _dialogSet.Add(new ChoicePrompt(LocationPrompt));
    _dialogSet.Add(new DateTimePrompt(ReservationDatePrompt, DateValidatorAsync));

    // Define the steps of the waterfall dialog and add it to the set.
    WaterfallStep[] steps = new WaterfallStep[]
    {
        PromptForPartySizeAsync,
        PromptForLocationAsync,
        PromptForReservationDateAsync,
        AcknowledgeReservationAsync,
    };
    _dialogSet.Add(new WaterfallDialog(ReservationDialog, steps));


}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Erstellen Sie im Konstruktor Zustandseigenschaftenaccessoren. 

```javascript
constructor(conversationState) {
    // Creates our state accessor properties.
    this.dialogStateAccessor = conversationState.createProperty(DIALOG_STATE_ACCESSOR);
    this.reservationAccessor = conversationState.createProperty(RESERVATION_ACCESSOR);
    this.conversationState = conversationState;
    // ...
    }
```

Erstellen Sie als Nächstes den Dialogsatz, und fügen Sie die Eingabeaufforderungen, einschließlich der benutzerdefinierten Validierung, hinzu.

```javascript
    // ...
    this.dialogSet = new DialogSet(this.dialogStateAccessor);
    this.dialogSet.add(new NumberPrompt(PARTY_SIZE_PROMPT, this.partySizeValidator));
    this.dialogSet.add(new ChoicePrompt (LOCATION_PROMPT));
    this.dialogSet.add(new DateTimePrompt(RESERVATION_DATE_PROMPT, this.dateValidator));
    // ...
```

Definieren Sie anschließend die Schritte des Wasserfalldialogs, und fügen Sie ihn dem Satz hinzu.
```javascript
    // ...
    this.dialogSet.add(new WaterfallDialog(RESERVATION_DIALOG, [
    this.promptForPartySize.bind(this),
    this.promptForLocation.bind(this),
    this.promptForReservationDate.bind(this),
    this.acknowledgeReservation.bind(this),
 ]));
}
```

---

### <a name="implement-dialog-steps"></a>Implementieren der Dialogschritte

In der Hauptdatei des Bots implementieren wir unsere einzelnen Schritte des Wasserfalldialogs. Nachdem eine Eingabeaufforderung hinzugefügt wurde, können wir sie in einem Schritt eines Wasserfalldialogs aufrufen und das Ergebnis der Eingabeaufforderung im folgenden Dialogschritt abrufen. Um eine Eingabeaufforderung in einem Wasserfallschritt aufzurufen, rufen Sie die _prompt_-Methode des _Wasserfallschritt-Kontextobjekts_ auf. Der erste Parameter ist die ID der zu verwendenden Eingabeaufforderung, und der zweite Parameter enthält die Optionen für die Eingabeaufforderung (z. B. den Text, mit dem der Benutzer zur Eingabe aufgefordert wird).     

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

In der Datei „DialogPromptBot.cs“ implementieren wir die Schritte `PromptForPartySizeAsync`, `PromptForLocationAsync`, `PromptForReservationDateAsync` und `AcknowledgeReservationAsync` des Wasserfalldialogs.

Hier sind nur `PromptForPartySizeAsync` und `PromptForLocationAsync` dargestellt, wobei es sich um zwei aufeinander folgende Schrittdelegaten eines Wasserfalldialogs handelt.

```csharp
private async Task<DialogTurnResult> PromptForPartySizeAsync(WaterfallStepContext stepContext)
{
    // Prompt for the party size. The result of the prompt is returned to the next step of the waterfall.
    // If the input is not valid, the prompt is restarted, causing it to reprompt for input
    // and this set of steps is repeated next turn. Otherwise, the prompt ends and returns a _dialog turn result_ object 
    // to the parent dialog. Control passes to the next step of your waterfall dialog, with the result of the prompt 
    // available in the waterfall step context's _result_ property.
    return await stepContext.PromptAsync(
        PartySizePrompt,
        new PromptOptions
        {
            Prompt = MessageFactory.Text("How many people is the reservation for?"),
            RetryPrompt = MessageFactory.Text("How large is your party?"),
        },
        cancellationToken);
}

private async Task<DialogTurnResult> PromptForLocationAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
{
    // Record the party size information in the current dialog state.
    int size = (int)stepContext.Result;
    stepContext.Values["size"] = size;

    return await stepContext.PromptAsync(
        "locationPrompt",
        new PromptOptions
        {
            Prompt = MessageFactory.Text("Please choose a location."),
            RetryPrompt = MessageFactory.Text("Sorry, please choose a location from the list."),
            Choices = ChoiceFactory.ToChoices(new List<string> { "Redmond", "Bellevue", "Seattle" }),
        },
        cancellationToken);
}
```
Im obigen Beispiel wird eine Auswahleingabeaufforderung mit allen drei Eigenschaften veranschaulicht. Die `PromptForLocationAsync`-Methode wird als Schritt in einem Wasserfalldialog verwendet, und der Dialogsatz enthält sowohl den Wasserfalldialog als auch eine Auswahleingabeaufforderung mit der ID `locationPrompt`.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Hier sind `PARTY_SIZE_PROMPT` und `LOCATION_PROMPT` die IDs der Eingabeaufforderungen und `promptForPartySize` und `promptForLocation` zwei aufeinander folgende Schrittfunktionen eines Wasserfalldialogs.

```javascript
async promptForPartySize(stepContext) {
    // Prompt for the party size. The result of the prompt is returned to the next step of the waterfall.
    return await stepContext.prompt(
        PARTY_SIZE_PROMPT, {
            prompt: 'How many people is the reservation for?',
            retryPrompt: 'How large is your party?'
        });
}

async promptForLocation(stepContext) {
    // Prompt for location
    return await stepContext.prompt(
        LOCATION_PROMPT, 'Select a location.', ['Redmond', 'Bellevue', 'Seattle']
    );
}
```

---

Der zweite Parameter der _prompt_-Methode akzeptiert ein _PromptOptions_-Objekt mit den folgenden Eigenschaften.

| Eigenschaft | BESCHREIBUNG |
| :--- | :--- |
| _prompt_ | Die erste Aktivität, die an den Benutzer gesendet wird, um ihn zur Eingabe aufzufordern. |
| _retryPrompt_ | Die Aktivität, die an den Benutzer gesendet wird, wenn seine erste Eingabe nicht überprüft werden konnte. |
| _choices_ | Eine Liste mit Optionen, aus denen der Benutzer bei einer Auswahleingabeaufforderung wählen kann. |

Im Allgemeinen sind die prompt- und retryPrompt-Eigenschaften Aktivitäten, sie werden jedoch in verschiedenen Programmiersprachen unterschiedlich behandelt.

Sie sollten die erste Eingabeaufforderungsaktivität, die an den Benutzer gesendet werden soll, immer angeben.

Die Angabe einer erneuten Eingabeaufforderung ist sinnvoll, wenn die Benutzereingabe nicht überprüft werden kann, weil sie in einem Format vorliegt, das die Eingabeaufforderung nicht analysieren kann (z.B. „morgen“ für eine Zahleneingabeaufforderung), oder weil die Eingabe ein Validierungskriterium nicht erfüllt. Wenn in diesem Fall keine erneute Eingabeaufforderung angegeben wurde, verwendet die Eingabeaufforderung die erste Eingabeaufforderungsaktivität, um den Benutzer erneut zur Eingabe aufzufordern.

Bei einer Auswahleingabeaufforderung sollten Sie immer die Liste der verfügbaren Optionen bereitstellen.



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

### <a name="implement-validation-code"></a>Implementieren des Codes für die Validierung

**Validierungssteuerelement für die Anzahl von Personen**

Wir beschränken Reservierungen auf Gruppen von 6 bis 20 Personen.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
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

**Validierung des Datums und der Uhrzeit**

In der Validierung für das Reservierungsdatum beschränken wir Reservierungen auf Zeiträume, die mindestens eine Stunde in der Zukunft liegen. Wir übernehmen die erste Auflösung, die unseren Kriterien entspricht, und löschen den Rest. Der unten angegebene Validierungscode ist nicht erschöpfend und funktioniert am besten für Eingaben, deren Analyse ein Datum und eine Uhrzeit ergibt. Mit dem Code werden einige Optionen für die Validierung einer Datums-/Uhrzeiteingabeaufforderung veranschaulicht. Ihre Implementierung hängt von den Informationen ab, die Sie vom Benutzer erfassen möchten.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
// Validates whether the reservation date is appropriate.
// Reservations must be made at least an hour in advance.
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

Die Datums-/Uhrzeiteingabeaufforderung gibt eine Liste oder ein Array der möglichen _Datums-/Uhrzeitauflösungen_ zurück, die der Benutzereingabe entsprechen. Beispielsweise kann 9:00 sowohl 9: 00 Uhr als auch 21: 00 Uhr bedeuten, und „Sonntag“ ist ebenfalls mehrdeutig. Darüber hinaus kann eine Datums-/Uhrzeitauflösung ein Datum, eine Uhrzeit, eine Datums- und Uhrzeitangabe oder einen Bereich darstellen. Die Datums-/Uhrzeiteingabeaufforderung verwendet [Microsoft/Recognizers-Text](https://github.com/Microsoft/Recognizers-Text), um die Eingabe des Benutzers zu analysieren.

### <a name="update-the-turn-handler"></a>Aktualisieren des Turn-Handlers

Aktualisieren Sie den Turn-Handler des Bots, um den Dialog zu starten und einen Rückgabewert des Dialogs zu akzeptieren, wenn dieser abgeschlossen wird. Hierbei setzen wir voraus, dass der Benutzer mit einem Bot interagiert, der Bot einen aktiven Wasserfalldialog enthält und im nächsten Schritt des Dialogs eine Eingabeaufforderung verwendet wird.

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

      
**Behandeln von Eingabeaufforderungsergebnissen**

Wozu Sie das Ergebnis der Eingabeaufforderung verwenden, hängt davon ab, warum Sie die Informationen vom Benutzer angefordert haben. Beispiele für Optionen:

* Verwenden Sie die Informationen zum Steuern des Dialogflusses (z.B. wenn ein Benutzer auf eine Bestätigungs- oder Auswahleingabeaufforderung reagiert).
* Speichern Sie die Informationen im Zustand des Dialogs zwischen (beispielsweise das Festlegen eines Werts in der _values_-Eigenschaft des Wasserfallschrittkontexts), und geben Sie die erfassten Informationen dann bei der Beendigung des Dialogs zurück.
* Speichern Sie die Informationen im Botzustand. Dazu müssen Sie den Dialog so entwerfen, dass er Zugriff auf die Zustandseigenschaftenaccessoren des Bots hat. 

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
        case ActivityTypes.EndOfConversation:
        case ActivityTypes.DeleteUserData:
            break;
        default:
            break;
    }
}
```

---



Sie können ähnliche Verfahren verwenden, um Antworten auf Eingabeaufforderungen für beliebige Eingabeaufforderungstypen zu überprüfen.

## <a name="test-your-bot"></a>Testen Ihres Bots
1. Führen Sie das Beispiel lokal auf Ihrem Computer aus. Wenn Sie eine Anleitung benötigen, helfen Ihnen die INFODATEIEN für [C#](https://aka.ms/dialog-prompt-cs) bzw. [JS](https://aka.ms/dialog-prompt-js) weiter.
2. Starten Sie den Emulator, und senden Sie Nachrichten wie unten dargestellt, um den Bot zu testen.

![Beispiel für das Testen der Dialogeingabeaufforderung](~/media/emulator-v4/test-dialog-prompt.png)

## <a name="additional-resources"></a>Zusätzliche Ressourcen
Wenn Sie eine Eingabeaufforderung direkt aus Ihrem Turn-Handler aufrufen möchten, hilft Ihnen das Beispiel _prompt-validations_ im [C#](https://aka.ms/cs-prompt-validation-sample)- bzw. [JS](https://aka.ms/js-prompt-validation-sample)-Format weiter.

Die Dialogbibliothek enthält auch eine _OAuth-Eingabeaufforderung_ zum Abrufen eines _OAuth-Tokens_, mit dem im Auftrag des Benutzers auf eine andere Anwendung zugegriffen wird. Weitere Informationen zur Authentifizierung finden Sie unter [Hinzufügen von Authentifizierung](bot-builder-authentication.md) zu Ihrem Bot.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erstellen eines erweiterten Konversationsablaufs mithilfe von Branches und Schleifen](bot-builder-dialog-manage-complex-conversation-flow.md)
