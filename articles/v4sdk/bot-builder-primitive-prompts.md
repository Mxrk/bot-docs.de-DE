---
title: Verwalten eines Konversationsablaufs mit einfachen Eingabeaufforderungen | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie einen Konversationsablauf mit einfachen Eingabeaufforderungen im Bot Builder SDK verwalten.
keywords: Konversationsablauf, Eingabeaufforderungen
author: v-ducvo
ms.author: v-ducvo
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 7/20/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 1514e1bcafc87be9e8449382bca7aa156e512ed9
ms.sourcegitcommit: e8c513d3af5f0c514cadcbcd0a737a7393405afa
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/20/2018
ms.locfileid: "40228345"
---
# <a name="prompt-users-for-input-using-your-own-prompts"></a>Verwenden eigener Eingabeaufforderungen für Benutzer
[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

Eine Konversation zwischen einem Bot und einem Benutzer umfasst in der Regel die Anforderung von Benutzerinformationen, die Analyse der Benutzerantwort und eine entsprechende Reaktion auf die Antwort. Im Thema [Auffordern von Benutzern zur Eingabe](bot-builder-prompts.md) wird erläutert, wie Sie Benutzer mithilfe der Bibliothek **Dialogs** zu einer Eingabe auffordern. Die Bibliothek **Dialogs** verfolgt unter anderem die aktuelle Konversation und die aktuell gestellte Frage nach. Darüber hinaus stellt sie Methoden zum Anfordern verschiedener Arten von Informationen wie Text, Zahlen oder Datums-/Uhrzeitangaben bereit. 

In bestimmten Situationen ist die integrierte Bibliothek **Dialogs** unter Umständen nicht die richtige Lösung für Ihren Bot. Für einfache Bots ist **Dialogs** möglicherweise zu aufwendig, nicht flexibel genug oder nicht für das geeignet, was Sie mit dem Bot erreichen möchten. In diesen Fällen können Sie die Bibliothek weglassen und Ihre eigene Aufforderungslogik implementieren. In diesem Thema erfahren Sie, wie Sie einen einfachen *Echobot* einrichten und eine Konversation mit eigenen Eingabeaufforderungen verwalten.

## <a name="track-prompt-states"></a>Nachverfolgen von Eingabeaufforderungszuständen

In einer durch Eingabeaufforderungen gesteuerten Konversation müssen Sie die aktuelle Position in der Konversation sowie die aktuell gestellte Frage nachverfolgen. Dazu müssen im Code mehrere Flags verwaltet werden. 

Sie können beispielsweise einige Eigenschaften erstellen, die Sie nachverfolgen möchten. 

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Hier befindet sich eine geschachtelte Benutzerprofilklasse in den Eingabeaufforderungsinformationen, sodass diese gemeinsam im [Zustand](bot-builder-howto-v4-state.md) des Bots gespeichert werden können.

```csharp
// Where user information will be stored
public class UserProfile
{
    public string userName = null;
    public string workPlace = null;
    public int age = 0;
}

// Flags to keep track of our prompts, and the 
// user profile object for this conversation
public class PromptInformation
{
    public string topicTitle = null;
    public string prompt = null;
    public UserProfile userProfile = null;
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Mithilfe dieser Zustände wird lediglich nachverfolgt, bei welchem Thema und welcher Eingabeaufforderung wir uns aktuell befinden. Damit diese Flags nach der Bereitstellung in der Cloud erwartungsgemäß funktionieren, werden sie im [Konversationszustand](bot-builder-howto-v4-state.md) gespeichert. Platzieren Sie den folgenden Code in der Hauptlogik Ihres Bots:

**app.js**
```javascript
// Define a topicStates object if it doesn't exist in the convoState.
if(!convo.topicStates){
    convo.topicStates = {
        "topicTitle": undefined, // Current conversation topic in progress
        "prompt": undefined      // Current prompt state in progress - indicate what question is being asked.
    };
}
```

---

## <a name="manage-a-topic-of-conversation"></a>Verwalten eines Konversationsthemas

In einer sequenziellen Konversation, bei der beispielsweise Informationen von einem Benutzer erfasst werden, müssen Sie wissen, wann der Benutzer die Konversation begonnen hat und wo er sich in der Konversation befindet. Das können Sie im zentralen Durchlaufhandler des Bots nachverfolgen, indem Sie die oben definierten Eingabeaufforderungsflags festlegen und überprüfen und anschließend entsprechend reagieren. Das folgende Beispiel zeigt, wie Sie unter Verwendung dieser Flags im Rahmen der Konversation die Profilinformationen des Benutzers ermitteln.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
// Get our current state, as defined above
var convoState = context.GetConversationState<PromptInformation>();

if (convoState.userProfile == null)
{
    await context.SendActivity("Welcome new user, please fill out your profile information.");
    convoState.topicTitle = "profileTopic"; // Start the userProfile topic
    convoState.userProfile = new UserProfile();
}

// Start or continue a conversation if we are in one
if (convoState.topicTitle == "profileTopic")
{
    if (convoState.userProfile.userName == null && convoState.prompt == null)
    {
        convoState.prompt = "askName";
        await context.SendActivity("What is your name?");
    }
    else if (convoState.prompt == "askName")
    {
        // Save the user's response
        convoState.userProfile.userName = context.Activity.Text;

        // Ask next question
        convoState.prompt = "askAge";
        await context.SendActivity("How old are you?");
    }
    else if (convoState.prompt == "askAge")
    {
        // Save user's response
        if (!Int32.TryParse(context.Activity.Text, out convoState.userProfile.age))
        {
            // Failed to convert to int
            await context.SendActivity("Failed to convert string to int");
        }
        else
        {
            // Ask next question
            convoState.prompt = "workPlace";
            await context.SendActivity("Where do you work?");
        }

    }
    else if (convoState.prompt == "workPlace")
    {
        // Save user's response
        convoState.userProfile.workPlace = context.Activity.Text;

        // Done
        convoState.topicTitle = null; // Reset conversation flag
        convoState.prompt = null;     // Reset the prompt flag
        
        await context.SendActivity("Thank you. Your profile is complete.");
    }
    else // Somehow our flags got inappropriately set
    {
        await context.SendActivity("Looks like something went wrong, let's start over");
        convoState.userProfile = null;
        convoState.prompt = null;
        convoState.topicTitle = null;
    }
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

**app.js**
```javascript
server.post('/api/messages', (req, res) => {
    adapter.processActivity(req, res, async (context) => {
        const isMessage = (context.activity.type === 'message');
        // State will store all of your information 
        const convo = conversationState.get(context);

        // Defined flags to manage the conversation flow and prompt states
        // convo.topicStates.topicTitle - Current conversation topic in progress
        // convo.topicStates.prompt - Current prompt state in progress - indicate what question is being asked.
        
        if (isMessage) {
            // Define a topicStates object if it doesn't exist in the convoState.
            if(!convo.topicStates){
                convo.topicStates = {
                    "topicTitle": undefined, // Current conversation topic in progress
                    "prompt": undefined      // Current prompt state in progress - indicate what question is being asked.
                };
            }
            
            // If user profile is not defined then define it.
            if(!convo.userProfile){
                
                await context.sendActivity(`Welcome new user, please fill out your profile information.`);
                convo.topicStates.topicTitle = "profileTopic"; // Start the userProfile topic
                convo.userProfile = { // Define the user's profile object
                    "userName": undefined,
                    "age": undefined,
                    "workPlace": undefined
                }; 
            }

            // Start or continue a conversation if we are in one
            if(convo.topicStates.topicTitle == "profileTopic"){
                if(!convo.userProfile.userName && !convo.topicStates.prompt){
                    convo.topicStates.prompt = "askName";
                    await context.sendActivity("What is your name?");
                }
                else if(convo.topicStates.prompt == "askName"){
                    // Save the user's response
                    convo.userProfile.userName = context.activity.text; 

                    // Ask next question
                    convo.topicStates.prompt = "askAge";
                    await context.sendActivity("How old are you?");
                }
                else if(convo.topicStates.prompt == "askAge"){
                    // Save user's response
                    convo.userProfile.age = context.activity.text;

                    // Ask next question
                    convo.topicStates.prompt = "workPlace";
                    await context.sendActivity("Where do you work?");
                }
                else if(convo.topicStates.prompt == "workPlace"){
                    // Save user's response
                    convo.userProfile.workPlace = context.activity.text;

                    // Done
                    convo.topicStates.topicTitle = undefined; // Reset conversation flag
                    convo.topicStates.prompt = undefined;     // Reset the prompt flag
                    await context.sendActivity("Thank you. Your profile is complete.");
                }
            }

            // Check for valid intents
            else if(context.activity.text && context.activity.text.match(/hi/ig)){
                await context.sendActivity(`Hi ${convo.userProfile.userName}.`);
            }
            else {
                // Default message
                await context.sendActivity("Hi. I'm the Contoso bot.");
            }
        }

    });
});

```

---

Der obige Beispielcode überprüft, ob im Arbeitsspeicher das Profil eines Benutzers definiert ist. Falls das nicht der Fall ist und es sich um einen neuen Benutzer handelt, legen Sie das Flag fest, um das Thema automatisch zu starten. Danach sehen Sie, wie Sie sich weiter durch die Konversation bewegen können, indem Sie das Flag `prompt` auf den Wert der aktuell gestellten Frage festlegen. Ist dieses Flag auf den korrekten Wert festgelegt, erfasst die Bedingungsklausel in jedem Durchlauf die jeweilige Antwort des Benutzers und verarbeitet sie entsprechend. 

Nachdem Sie alle Informationen erfasst haben, müssen diese Flags wieder zurückgesetzt werden, damit der Bot nicht in einer Schleife hängen bleibt. Diese einfache Struktur können Sie bei Bedarf für die Verwaltung komplexerer Konversationsabläufe erweitern, indem Sie einfach weitere Flags definieren oder die Konversation auf der Grundlage der Benutzereingabe verzweigen.

## <a name="manage-multiple-topics-of-conversations"></a>Verwalten mehrerer Konversationsthemen

Wenn Sie mit einem einzelnen Konversationsthema zurechtkommen, können Sie die Botlogik für die Verarbeitung mehrerer Konversationsthemen erweitern. Zur Verarbeitung mehrerer Konversationsthemen können Sie genau wie bei einem einzelnen Konversationsthema einfach Bedingungen überprüfen, die ein bestimmtes Thema auslösen, und dann den entsprechenden Pfad verfolgen.

Das obige Beispiel lässt sich etwa für die Verwendung weiterer Funktionen und Themen umgestalten – beispielsweise um einen Tisch zu reservieren oder Essen zu bestellen. Den Flags für Benutzerprofil und Themenzustand können nach Bedarf weitere Informationen hinzugefügt werden, um die Konversation nachzuverfolgen.

Zur besseren Verwaltbarkeit mehrerer Konversationsthemen kann beispielsweise ein Hauptmenü bereitgestellt werden. Durch [empfohlene Aktionen](bot-builder-howto-add-suggested-actions.md) können Sie dem Benutzer Themenoptionen anbieten, mit denen er sich dann ausführlicher beschäftigen kann. Auch kann es hilfreich sein, Code auf unabhängige Funktionen aufzuteilen, um dem Konversationsablauf leichter folgen zu können.

## <a name="validate-user-input"></a>Validieren von Benutzereingaben

Die Bibliothek **Dialogs** enthält integrierte Überprüfungsmöglichkeiten für Benutzereingaben. Eine solche Überprüfung ist aber auch mit unseren eigenen Eingabeaufforderungen möglich. Wenn wir beispielsweise nach dem Alter des Benutzers fragen, möchten wir natürlich sicherstellen, dass wir eine Zahl erhalten und nicht den Namen des Benutzers.

Das Analysieren von Zahlen oder Datums-/Uhrzeitangaben ist eine komplexe Angelegenheit, die den Rahmen dieses Themas sprengen würde. Zum Glück gibt es jedoch eine Bibliothek, die wir nutzen können. Für die Analyse dieser Informationen verwenden wir die [Texterkennungsbibliothek von Microsoft](https://github.com/Microsoft/Recognizers-Text). Dieses Paket steht über NuGet zur Verfügung oder kann aus dem Repository herunterladen werden. Darüber hinaus ist es in der Bibliothek **Dialogs** enthalten (die hier jedoch nicht verwendet wird).

Diese Bibliothek ist besonders hilfreich beim Analysieren von Gemeinsprache oder komplexen Antworten zu Informationen wie Datum, Uhrzeit oder Telefonnummern. In diesem Beispiel wird lediglich eine Zahl für die Größe einer Essensgesellschaft überprüft. Das Konzept lässt sich jedoch für wesentlich detailliertere Überprüfungen erweitern. Wenn Sie nicht mit dieser Bibliothek vertraut sind, informieren Sie sich auf der Website über ihren Funktionsumfang.

In diesem Beispiel wird nur die Verwendung von `RecognizeNumber` gezeigt. Ausführliche Informationen zur Verwendung anderer Erkennungsmethoden aus der Bibliothek finden Sie in der [Dokumentation des Repositorys](https://github.com/Microsoft/Recognizers-Text/blob/master/README.md).

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Fügen Sie die Erkennungsbibliothek Ihren using-Anweisungen hinzu, um die Bibliothek zu verwenden.

```csharp
using Microsoft.Recognizers.Text.Number;
using Microsoft.Recognizers.Text;
using System.Linq; // Used to get the first result from the recognizer
```

Definieren Sie anschließend eine Funktion für die eigentliche Überprüfung.

```csharp
private async Task<bool> ValidatePartySize(ITurnContext context, string value)
{
    try
    {
        // Recognize the input as a number. This works for responses such as
        // "twelve" as well as "12"
        var result = NumberRecognizer.RecognizeNumber(input, Culture.English);

        // Attempt to convert the Recognizer result to an integer
        Int32.TryParse(result.First().Text, out int partySize);
        
        if (partySize < 6)
        {
            throw new Exception("Party size too small.");
        }
        else if (partySize > 20)
        {
            throw new Exception("Party size too big.");
        }

        // If we got through this, the number is valid
        return true;
    }
    catch (Exception)
    {
        await context.SendActivity("Error with your party size. < br /> Please specify a number between 6 - 20.");
        return false;
    }
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Wenn Sie die Erkennungsbibliothek verwenden möchten, müssen Sie sie in **app.js** voraussetzen:

```javascript
// Required packages for this bot
var Recognizers = require('@microsoft/recognizers-text-suite');
```

Definieren Sie anschließend eine Funktion für die eigentliche Überprüfung.

```javascript
// Support party size between 6 and 20 only
async function validatePartySize(context, input){
    try {
        // Recognize the input as a number. This works for responses such as
        // "twelve" as well as "12"
        var result = Recognizers.recognizeNumber(input, Recognizers.Culture.English);
        var value = parseInt(results[0].resolution.value);

        if(value < 6) {
            throw new Error(`Party size too small.`);
        }
        else if(value > 20){
            throw new Error(`Party size too big.`);
        }
        return true; // Return the valid value
    }
    catch (err){
        await context.sendActivity(`${err.message} <br/>Please specify a number between 6 - 20.`);
        return false;
    }
}
```

---

Rufen Sie die Überprüfungsfunktion innerhalb des zu überprüfenden Eingabeaufforderungsschritts auf, bevor Sie mit der nächsten Eingabeaufforderung fortfahren. Ist die Überprüfung nicht erfolgreich, wiederholen Sie die Frage.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
if (convoState.prompt == "partySize")
{
    if (await ValidatePartySize(context, context.Activity.Text))
    {
        // Save user's response in our state, ReservationInfo, which 
        // is a new class we've added to our state
        convoState.ReservationInfo.partySize = context.Activity.Text;

        // Ask next question
        convoState.prompt = "reserveName";
        await context.SendActivity("Who's name will this be under?");
    }
    else
    {
        // Ask again
        await context.SendActivity("How many people are in your party?");
    }
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

**app.js**

```javascript
// ...
if(convo.topicStates.prompt == "partySize"){
    if(await validatePartySize(context, context.activity.text)){
        // Save user's response
        convo.reservationInfo.partySize = context.activity.text;
        
        // Ask next question
        topicStates.prompt = "reserveName";
        await context.sendActivity("Who's name will this be under?");
    }
    else {
        // Ask again
        await context.sendActivity("How many people are in your party?");
    }
}
// ...
```

---

## <a name="next-step"></a>Nächster Schritt

Sie wissen nun, wie Sie Eingabeaufforderungszustände selbst verwalten. Als Nächstes erfahren Sie, wie Sie die Bibliothek **Dialogs** verwenden, um Benutzer zu einer Eingabe aufzufordern.

> [!div class="nextstepaction"]
> [Auffordern von Benutzern zur Eingabe](bot-builder-prompts.md)
