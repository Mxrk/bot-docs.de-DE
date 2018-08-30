---
title: Speichern von Benutzerdaten | Microsoft-Dokumentation
description: Erfahren Sie mehr zum Speichern von Benutzerstatusdaten im Speicher des Bot Builder SDK.
keywords: speichern von benutzerdaten, speicher, konversationsdaten
author: v-ducvo
ms.author: v-ducvo
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 4/23/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 28377a1e611464012df28d3edf78d1cf01351345
ms.sourcegitcommit: 44f100a588ffda19c275b118f4f97029f12d1449
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/26/2018
ms.locfileid: "42928368"
---
# <a name="persist-user-data"></a>Speichern von Benutzerdaten

[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

Wenn der Bot eine Benutzereingabe anfordert, möchten Sie wahrscheinlich, dass einige der Informationen in einem Speicher beibehalten werden. Das Bot Builder SDK erlaubt es Ihnen, Benutzereingaben mit *In-Memory-Speicher*, *File Storage* oder Datenbankspeicher wie *CosmosDB* oder *SQL* zu speichern, wobei lokale Speichertypen hauptsächlich zum Testen oder Erstellen von Prototypen verwendet werden und die späteren Speichertypen am besten für Produktionsbots geeignet sind.

Dieses Tutorial zeigt Ihnen, wie Sie Ihr Speicherobjekt und Benutzereingaben im Speicherobjekt speichern, sodass sie beibehalten werden können. 

> [!NOTE]
> Unabhängig davon, für welchen Speichertyp Sie sich entscheiden, ist das Verfahren für die Einbindung und die Speicherung der Daten gleich. Dieses Tutorial verwendet den `FileStorage` als Speicher zum Speichern der Daten.
> Weitere Informationen zum Zustand und den anderen Speichertypen finden Sie unter [Verwalten des Konversations- und Benutzerzustands](bot-builder-howto-v4-state.md).

## <a name="prequisite"></a>Voraussetzung 

Dieses Tutorial baut auf dem Tutorial [Dem Benutzer Fragen stellen](bot-builder-tutorial-waterfall.md) auf. In diesem Tutorial erstellen Sie einen Bot, der den Benutzer nach drei Informationen zu seiner Tischreservierung in Ihrem Restaurant fragt. Die Eingabe des Benutzers wurde jedoch nicht gespeichert. Dieses Tutorial fügt dem Bot die Persistenz der Datenspeicherung hinzu.

## <a name="add-storage-to-middleware-layer"></a>Hinzufügen von Speicher zur Middlewareebene

Das Bot Builder V4 SDK verwaltet den Zustand und den Speicher über eine Zustands-Manager-Middleware. Die Middleware stellt eine Abstraktionebene bereit, mit der Sie unabhängig vom Typ des zugrunde liegenden Speichers mithilfe eines einfachen Schlüsselwertsspeichers auf Eigenschaften zugreifen können. Der Zustands-Manager übernimmt das Schreiben von Daten in den Speicher und das Verwalten der Parallelität, unabhängig davon, ob der zugrunde liegende Speichertyp In-Memory, File Storage oder Azure-Tabellenspeicher ist. In diesem Tutorial verwenden wir `FileStorage` zum Speichern der Benutzereingaben.

Der `FileStorage`-Anbieter ist im `bot-builder`-Paket enthalten. Das Beispiel, mit dem Sie begonnen haben, verwendet den `MemoryStorage`-Anbieter. Dieser Speichertyp verwendet den flüchtigen *In-Memory* des Bots, der beim Neustarten des Bots gelöscht wird. Die `FileStorage`-Bibliothek verhält sich so ähnlich wie eine Datenbank. Das heißt, diese Bibliothek schreibt die Speicherinformationen in eine Datei auf Ihrem lokalen Computer. Sie können angeben, wohin diese Datei gespeichert werden soll, damit Sie sie später einsehen können.

Um den `FileStorage` zu verwenden, suchen Sie in Ihrem Bot die Anweisung, in der das `conversationState`-Objekt definiert ist, und aktualisieren Sie es, um einen `new botbuilder.FileStorage("c:/temp")` zu erstellen. Darüber hinaus können Sie den Speicherort definieren, in den diese Speicherdatei geschrieben werden sollen. Auf diese Weise können Sie sie leicht finden, um den Inhalt dessen zu überprüfen, was erhalten geblieben ist.

# <a name="ctabcstab"></a>[C#](#tab/cstab)
```cs
var storage = new FileStorage("c:/temp");

// These two classes are simply Dictionaries to store state
options.Middleware.Add(new ConversationState<MyBot.convoState>(storage));
options.Middleware.Add(new UserState<MyBot.userState>(storage));
```

Das Bot Builder SDK bieten drei Zustandsobjekte mit unterschiedlichen Bereichen, aus denen Sie auswählen können.

| State (Zustand) | Bereich | BESCHREIBUNG |
| ---- | ---- | ---- |
| `dc.ActiveDialog.State` | dialog | Für die Schritte im Dialog „Wasserfall“ verfügbarer Zustand |
| `ConversationState` | conversation | Für die aktuelle Konversation verfügbarer Status |
| `UserState` | user | Für mehrere Konversationen verfügbarer Status |

# <a name="javascripttabjstab"></a>[JavaScript](#tab/jstab)

**app.js**
```javascript
// Storage
const storage = new FileStorage("c:/temp");
const convoState = new ConversationState(storage);
const userState  = new UserState(storage);
adapter.use(new BotStateSet(convoState, userState));
```

`BotStateSet` kann sowohl `ConversationState` als auch `UserState` gleichzeitig verwalten. Wenn die Benutzerdaten gespeichert werden sollen, können Sie daraus auswählen. Das Bot Builder SDK bieten drei Zustandsobjekte mit unterschiedlichen Bereichen, aus denen Sie auswählen können.

| State (Zustand) | Bereich | BESCHREIBUNG |
| ---- | ---- | ---- |
| `dc.activeDialog.state` | dialog | Für die Schritte im Dialog „Wasserfall“ verfügbarer Zustand |
| `ConversationState` | conversation | Für die aktuelle Konversation verfügbarer Status |
| `UserState` | user | Für mehrere Konversationen verfügbarer Status |

---
 

## <a name="persist-state"></a>Beibehalten des Status

Sie können für Ihren Bot in jeden der drei Zustandsorte schreiben, je nachdem, was Sie speichern und wie lange die Daten beibehalten werden müssen.  

# <a name="ctabcstab"></a>[C#](#tab/cstab)

Die *Zustands-Manager-Middleware* verarbeitet den Schreibvorgang für Sie am Ende eines jeden Durchlaufs. Sie müssen also lediglich die Daten in Ihrem Bot dem gewünschten Zustandsobjekt zuweisen. In diesem Beispiel verwenden wir `dc.ActiveDialog.State` zum Nachverfolgen der Benutzereingabe für unsere Reservierung. Auf diese Weise können Sie die Benutzereingaben nicht in einer globalen Variable speichern, sondern in einem temporären Zustandsobjektbereich des Dialogs. Dieses Objekt existiert nur, solange der Dialog aktiv ist; wenn Sie es länger beibehalten wollen, müssen Sie es in eines der anderen Zustandsobjekte übertragen. In diesem Fall weisen wir die Reservierung `msg` dem Konversationszustand im letzten Schritt von „Wasserfall“ zu.

```cs
dialogs.Add("reserveTable", new WaterfallStep[]
{
    async (dc, args, next) =>
    {
        // Prompt for the guest's name.
        await dc.Context.SendActivity("Welcome to the reservation service.");

        dc.ActiveDialog.State = new Dictionary<string, object>();

        await dc.Prompt("dateTimePrompt", "Please provide a reservation date and time.");
    },
    async(dc, args, next) =>
    {
        var dateTimeResult = ((DateTimeResult)args).Resolution.First();

        dc.ActiveDialog.State["date"] = Convert.ToDateTime(dateTimeResult.Value);
        
        // Ask for next info
        await dc.Prompt("partySizePrompt", "How many people are in your party?");

    },
    async(dc, args, next) =>
    {
        dc.ActiveDialog.State["partySize"] = (int)args["Value"];

        // Ask for next info
        await dc.Prompt("textPrompt", "Who's name will this be under?");
    },
    async(dc, args, next) =>
    {
        dc.ActiveDialog.State["name"] = args["Text"];
        string msg = "Reservation confirmed. Reservation details - " +
        $"\nDate/Time: {dc.ActiveDialog.State["date"].ToString()} " +
        $"\nParty size: {dc.ActiveDialog.State["partySize"].ToString()} " +
        $"\nReservation name: {dc.ActiveDialog.State["name"]}";

        var convo = ConversationState<convoState>.Get(dc.Context);

        // In production, you may want to store something more helpful
        convo[$"{dc.ActiveDialog.State["name"]} reservation"] = msg;

        await dc.Context.SendActivity(msg);
        await dc.End();
    }
});
```

# <a name="javascripttabjstab"></a>[JavaScript](#tab/jstab)

Die *Zustands-Manager-Middleware* verarbeitet den Schreibvorgang in die Datei für Sie am Ende eines jeden Durchlaufs. Sie müssen also lediglich die Daten in Ihrem Bot dem gewünschten Zustandsobjekt zuweisen. In diesem Beispiel verwenden wir `dc.activeDialog.state` zum Nachverfolgen der Benutzereingabe in einem `reservervationInfo`-Objekt. Auf diese Weise können Sie die Benutzereingaben nicht in einer globalen Variable speichern, sondern in einem temporären Zustandsobjektbereich des Dialogs. Da dieses Objekt nur existiert, solange der Dialog aktiv ist; wenn Sie es beibehalten wollen, müssen Sie es in eines der anderen Zustandsobjekte übertragen. In diesem Fall weisen wir die `reservationInfo` dem `convo`-Zustand im letzten Schritt von „Wasserfall“ zu.

```javascript
// Reserve a table:
// Help the user to reserve a table

dialogs.add('reserveTable', [
    async function(dc, args, next){
        await dc.context.sendActivity("Welcome to the reservation service.");

        dc.activeDialog.state.reservationInfo = {}; // Clears any previous data
        await dc.prompt('dateTimePrompt', "Please provide a reservation date and time.");
    },
    async function(dc, result){
        dc.activeDialog.state.reservationInfo.dateTime = result[0].value;

        // Ask for next info
        await dc.prompt('partySizePrompt', "How many people are in your party?");
    },
    async function(dc, result){
        dc.activeDialog.state.reservationInfo.partySize = result;

        // Ask for next info
        await dc.prompt('textPrompt', "Who's name will this be under?");
    },
    async function(dc, result){
        dc.activeDialog.state.reservationInfo.reserveName = result;
        
        // Persist data
        var convo = conversationState.get(dc.context);; // conversationState.get(dc.context);
        convo.reservationInfo = dc.activeDialog.state.reservationInfo;

        // Confirm reservation
        var msg = `Reservation confirmed. Reservation details: 
            <br/>Date/Time: ${dc.activeDialog.state.reservationInfo.dateTime} 
            <br/>Party size: ${dc.activeDialog.state.reservationInfo.partySize} 
            <br/>Reservation name: ${dc.activeDialog.state.reservationInfo.reserveName}`;
            
        await dc.context.sendActivity(msg);
        await dc.end();
    }
]);
```

---

Nun sind Sie bereit, dies in die Botlogik einzubinden.

## <a name="start-the-dialog"></a>Starten des Dialogs

Sie müssen an dieser Stelle keine Änderungen am Code vornehmen. Führen Sie einfach den Bot aus und senden Sie die Nachricht, um die `reserveTable`-Konversation zu starten.

## <a name="check-file-storage-content"></a>Überprüfen des Dateispeicherinhalts

Nachdem Sie den Bot ausgeführt und die `reserveTable`-Konversation durchlaufen haben, suchen Sie die Informationen, die in einer Datei an dem von Ihnen angegebenen Ort gespeichert sind (z.B.: „C:/temp“). Dem Dateinamen ist entweder „conversation!“ oder „user!“ vorangestellt. Es kann helfen, die Dateien nach Datum zu sortieren, damit Sie die aktuelle Datei leichter finden können.

## <a name="next-steps"></a>Nächste Schritte

Da Sie jetzt wissen, wie Sie Benutzereingaben speichern, können Sie sich ansehen, welche Art von Eingaben Sie vom Benutzer über die Aufforderungsbibliothek anfordern können.

> [!div class="nextstepaction"]
> [Auffordern der Benutzer zu Eingaben](~/v4sdk/bot-builder-prompts.md)
