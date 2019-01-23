---
title: Erstellen eigener Eingabeaufforderungen zum Erfassen von Benutzereingaben | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie einen Konversationsfluss mit einfachen Eingabeaufforderungen im Bot Framework SDK verwalten.
keywords: Konversationsablauf, Eingabeaufforderungen, Konversationszustand, Benutzerzustand, benutzerdefinierte Eingabeaufforderungen
author: JonathanFingold
ms.author: v-jofing
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 11/20/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 2e591f19f7df8fa6281573c0ac7f1330d95f4c53
ms.sourcegitcommit: b15cf37afc4f57d13ca6636d4227433809562f8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/11/2019
ms.locfileid: "54225435"
---
# <a name="create-your-own-prompts-to-gather-user-input"></a>Erstellen eigener Eingabeaufforderungen zum Erfassen von Benutzereingaben

[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

Eine Konversation zwischen einem Bot und einem Benutzer umfasst in der Regel die Anforderung von Benutzerinformationen, die Analyse der Benutzerantwort und eine entsprechende Reaktion auf die Antwort.

Ihr Bot sollte den Kontext einer Konversation nachverfolgen, damit er das Verhalten verwalten und sich Antworten auf vorherige Fragen merken kann. Der *Zustand* eines Bots ist eine Information, die nachverfolgt wird, um auf eingehende Nachrichten angemessen reagieren zu können.

## <a name="prerequisites"></a>Voraussetzungen

- Der Code in diesem Artikel basiert auf dem Beispiel zum Thema **Auffordern von Benutzern zur Eingabe**. Sie benötigen eine Kopie des Beispiels im [C#](https://aka.ms/cs-primitive-prompt-sample)- oder [JS](https://aka.ms/js-primitive-prompt-sample)-Format.
- Sie müssen wissen, wie der [Zustand verwaltet](bot-builder-concept-state.md) wird und [Benutzer- und Konversationsdaten gespeichert](bot-builder-howto-v4-state.md) werden.
- Einen [Bot Framework Emulator](https://aka.ms/Emulator-wiki-getting-started) zum lokalen Testen des Bots.

## <a name="about-the-sample-code"></a>Informationen zum Beispielcode

In diesem Artikel stellen wir dem Benutzer einige Fragen, überprüfen einige Antworten und speichern die Eingabe.
Wir verwenden den Turn-Handler des Bots und die Eigenschaften für den Benutzer- und Konversationszustand, um den Ablauf der Konversation und die Erfassung der Eingabe zu verwalten.

1. Definieren und Konfigurieren des Zustands
1. Verwenden von Zustandseigenschaften zum Steuern der Konversation
   1. Aktualisieren Sie den Turn-Handler des Bots.
   1. Implementieren Sie eine Hilfsmethode zum Verwalten der Sammlung mit den Benutzerdaten.
   1. Implementieren Sie die Validierungsmethoden für die Benutzereingabe.

## <a name="define-and-configure-state"></a>Definieren und Konfigurieren des Zustands

Wir müssen unseren Bot so konfigurieren, dass die folgenden Informationen nachverfolgt werden:

- Der Name, das Alter und das gewählte Datum. Diese Angaben definieren wir im Benutzerzustand.
- Was wir den Benutzer gerade gefragt haben. Dies definieren wir im Konversationszustand.

Da wir nicht planen, diesen Bot bereitzustellen, konfigurieren wir sowohl für den Benutzer- als auch für den Konversationszustand die Nutzung des _Arbeitsspeichers_. Unten sind einige wichtige Aspekte des Konfigurationscodes beschrieben.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Wir definieren die folgenden Typen.

- Eine `UserProfile`-Klasse für die Benutzerinformationen, die vom Bot gesammelt werden.
- Eine `ConversationFlow`-Klasse zum Nachverfolgen der Informationen zur Stelle, an der wir uns in der Konversation befinden.
- Eine innere `ConversationFlow.Question`-Enumeration, mit der wir nachverfolgen, wo wir uns in der Konversation befinden.
- Eine `CustomPromptBotAccessors`-Klasse, in der wir die Informationen zur Zustandsverwaltung bündeln können.

Die BotAccessor-Klasse enthält unsere Zustandsverwaltung und Zustandseigenschaftenaccessor-Objekte und wird per Abhängigkeitsinjektion in ASP.NET Core an den Bot übergeben. In unserem Bot zeichnen wir die Informationen zu den Zustandseigenschaftenaccessoren auf, die Sie jeweils erhalten, wenn der Bot für einen Durchlauf erstellt wird.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Wir erstellen die Objekte für die Zustandsverwaltung und übergeben sie beim Erstellen des Bots.
In unserem Bot definieren wir Bezeichner für die Zustandseigenschaften und zum Nachverfolgen der derzeitigen Stelle in der Konversation. Anschließend zeichnen wir die Objekte für die Zustandsverwaltung auf und erstellen die Zustandseigenschaftenaccessoren im Konstruktor des Bots.

---

## <a name="use-state-properties-to-direct-the-conversation"></a>Verwenden von Zustandseigenschaften zum Steuern der Konversation

Nachdem wir unsere Zustandseigenschaften konfiguriert haben, können wir sie in unserem Bot verwenden.

- Wir definieren den [Turn-Handler](#the-bots-turn-handler) für das Zugreifen auf den Zustand und das Aufrufen der Hilfsmethode.
- Wir implementieren eine [Hilfsmethode](#filling-out-the-user-profile) zum Verwalten der Sammlung mit dem Benutzerprofil.
- Wir implementieren [Validierungsmethoden](#parse-and-validate-input) zum Analysieren und Überprüfen der Benutzereingabe.

### <a name="the-bots-turn-handler"></a>Turn-Handler des Bots

Wir verwenden die Zustandseigenschaftenaccessoren, um die Zustandseigenschaften aus dem Turn-Kontext abzurufen.
Wenn wir das Benutzerprofil ausfüllen müssen, rufen wir unsere Hilfsmethode auf und speichern dann alle Zustandsänderungen.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
public async Task OnTurnAsync(ITurnContext turnContext, CancellationToken cancellationToken = default(CancellationToken))
{
   if (turnContext.Activity.Type == ActivityTypes.Message)
    {
        // Get the state properties from the turn context.
        ConversationFlow flow = await _accessors.ConversationFlowAccessor.GetAsync(turnContext, () => new ConversationFlow());
        UserProfile profile = await _accessors.UserProfileAccessor.GetAsync(turnContext, () => new UserProfile());

        await FillOutUserProfileAsync(flow, profile, turnContext);

        // Update state and save changes.
        await _accessors.ConversationFlowAccessor.SetAsync(turnContext, flow);
        await _accessors.ConversationState.SaveChangesAsync(turnContext);

        await _accessors.UserProfileAccessor.SetAsync(turnContext, profile);
        await _accessors.UserState.SaveChangesAsync(turnContext);
    }
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
// The bot's turn handler.
async onTurn(turnContext) {
    // This bot listens for message activities.
    if (turnContext.activity.type === ActivityTypes.Message) {
        // Get the state properties from the turn context.
        const flow = await this.conversationFlow.get(turnContext, { lastQuestionAsked: question.none });
        const profile = await this.userProfile.get(turnContext, {});

        await MyBot.fillOutUserProfile(flow, profile, turnContext);

        // Update state and save changes.
        await this.conversationFlow.set(turnContext, flow);
        await this.conversationState.saveChanges(turnContext);

        await this.userProfile.set(turnContext, profile);
        await this.userState.saveChanges(turnContext);
    }
}
```

---

### <a name="filling-out-the-user-profile"></a>Ausfüllen des Benutzerprofils

Zunächst sammeln wir die Informationen. Hierbei wird jeweils eine ähnliche Oberfläche bereitgestellt.

- Der Rückgabewert gibt an, ob die Eingabe eine gültige Antwort auf die Frage ist.
- Wenn die Validierung erfolgreich war, wird ein analysierter und normalisierter Wert erzeugt, der gespeichert werden kann.
- Falls für die Validierung ein Fehler auftritt, wird eine Nachricht erzeugt, über die der Bot erneut nach den Informationen fragen kann.

 Im [nächsten Abschnitt](#parse-and-validate-input) definieren wir die Hilfsmethoden zum Analysieren und Überprüfen der Benutzereingabe.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
private static async Task FillOutUserProfileAsync(ConversationFlow flow, UserProfile profile, ITurnContext turnContext)
{
    string input = turnContext.Activity.Text?.Trim();
    string message;
    switch (flow.LastQuestionAsked)
    {
        case ConversationFlow.Question.None:
            await turnContext.SendActivityAsync("Let's get started. What is your name?");
            flow.LastQuestionAsked = ConversationFlow.Question.Name;
            break;
        case ConversationFlow.Question.Name:
            if (ValidateName(input, out string name, out message))
            {
                profile.Name = name;
                await turnContext.SendActivityAsync($"Hi {profile.Name}.");
                await turnContext.SendActivityAsync("How old are you?");
                flow.LastQuestionAsked = ConversationFlow.Question.Age;
                break;
            }
            else
            {
                await turnContext.SendActivityAsync(message ?? "I'm sorry, I didn't understand that.");
                break;
            }
        case ConversationFlow.Question.Age:
            if (ValidateAge(input, out int age, out message))
            {
                profile.Age = age;
                await turnContext.SendActivityAsync($"I have your age as {profile.Age}.");
                await turnContext.SendActivityAsync("When is your flight?");
                flow.LastQuestionAsked = ConversationFlow.Question.Date;
                break;
            }
            else
            {
                await turnContext.SendActivityAsync(message ?? "I'm sorry, I didn't understand that.");
                break;
            }
        case ConversationFlow.Question.Date:
            if (ValidateDate(input, out string date, out message))
            {
                profile.Date = date;
                await turnContext.SendActivityAsync($"Your cab ride to the airport is scheduled for {profile.Date}.");
                await turnContext.SendActivityAsync($"Thanks for completing the booking {profile.Name}.");
                await turnContext.SendActivityAsync($"Type anything to run the bot again.");
                flow.LastQuestionAsked = ConversationFlow.Question.None;
                profile = new UserProfile();
                break;
            }
            else
            {
                await turnContext.SendActivityAsync(message ?? "I'm sorry, I didn't understand that.");
                break;
            }
    }
}


```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
// Manages the conversation flow for filling out the user's profile.
static async fillOutUserProfile(flow, profile, turnContext) {
    const input = turnContext.activity.text;
    let result;
    switch (flow.lastQuestionAsked) {
        // If we're just starting off, we haven't asked the user for any information yet.
        // Ask the user for their name and update the conversation flag.
        case question.none:
            await turnContext.sendActivity("Let's get started. What is your name?");
            flow.lastQuestionAsked = question.name;
            break;

        // If we last asked for their name, record their response, confirm that we got it.
        // Ask them for their age and update the conversation flag.
        case question.name:
            result = this.validateName(input);
            if (result.success) {
                profile.name = result.name;
                await turnContext.sendActivity(`I have your name as ${profile.name}.`);
                await turnContext.sendActivity('How old are you?');
                flow.lastQuestionAsked = question.age;
                break;
            } else {
                // If we couldn't interpret their input, ask them for it again.
                // Don't update the conversation flag, so that we repeat this step.
                await turnContext.sendActivity(
                    result.message || "I'm sorry, I didn't understand that.");
                break;
            }

        // If we last asked for their age, record their response, confirm that we got it.
        // Ask them for their date preference and update the conversation flag.
        case question.age:
            result = this.validateAge(input);
            if (result.success) {
                profile.age = result.age;
                await turnContext.sendActivity(`I have your age as ${profile.age}.`);
                await turnContext.sendActivity('When is your flight?');
                flow.lastQuestionAsked = question.date;
                break;
            } else {
                // If we couldn't interpret their input, ask them for it again.
                // Don't update the conversation flag, so that we repeat this step.
                await turnContext.sendActivity(
                    result.message || "I'm sorry, I didn't understand that.");
                break;
            }

        // If we last asked for a date, record their response, confirm that we got it,
        // let them know the process is complete, and update the conversation flag.
        case question.date:
            result = this.validateDate(input);
            if (result.success) {
                profile.date = result.date;
                await turnContext.sendActivity(`Your cab ride to the airport is scheduled for ${profile.date}.`);
                await turnContext.sendActivity(`Thanks for completing the booking ${profile.name}.`);
                await turnContext.sendActivity('Type anything to run the bot again.');
                flow.lastQuestionAsked = question.none;
                profile = {};
                break;
            } else {
                // If we couldn't interpret their input, ask them for it again.
                // Don't update the conversation flag, so that we repeat this step.
                await turnContext.sendActivity(
                    result.message || "I'm sorry, I didn't understand that.");
                break;
            }
    }
}
```

---

### <a name="parse-and-validate-input"></a>Analysieren und Überprüfen der Eingabe

Wir verwenden die folgenden Kriterien, um die Eingabe zu überprüfen.

- Der **Name** darf keine leere Zeichenfolge sein. Wir führen die Normalisierung durch, indem wir die Leerstellen entfernen.
- Das Alter (**age**) muss zwischen 18 und 120 liegen. Wir führen die Normalisierung durch, indem wir eine ganze Zahl zurückgeben.
- Beim Datum (**date**) muss es sich um ein Datum bzw. einen Zeitpunkt handeln, der mindestens einen Stunde in der Zukunft liegt.
  Wir führen die Normalisierung durch, indem wir nur den Datumsteil der analysierten Eingabe zurückgeben.

> [!NOTE]
> Für die Eingabe des Alters und des Datums nutzen wir die [Microsoft/Recognizers-Text](https://github.com/Microsoft/Recognizers-Text/)-Bibliotheken, um die erste Analyse durchzuführen.
> Hier ist zwar Beispielcode angegeben, aber wir gehen nicht auf die Funktionsweise der Bibliotheken für die Texterkennung ein. Dies ist nur eine von mehreren Möglichkeiten, um die Eingabe zu analysieren.
> Weitere Informationen zu diesen Bibliotheken finden Sie in der **INFODATEI** des Repositorys.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Fügen Sie Ihrem Bot die folgenden Validierungsmethoden hinzu.

```csharp
private static bool ValidateName(string input, out string name, out string message)
{
    name = null;
    message = null;

    if (string.IsNullOrWhiteSpace(input))
    {
        message = "Please enter a name that contains at least one character.";
    }
    else
    {
        name = input.Trim();
    }

    return message is null;
}

private static bool ValidateAge(string input, out int age, out string message)
{
    age = 0;
    message = null;

    // Try to recognize the input as a number. This works for responses such as "twelve" as well as "12".
    try
    {
        // Attempt to convert the Recognizer result to an integer. This works for "a dozen", "twelve", "12", and so on.
        // The recognizer returns a list of potential recognition results, if any.
        List<ModelResult> results = NumberRecognizer.RecognizeNumber(input, Culture.English);
        foreach (var result in results)
        {
            // result.Resolution is a dictionary, where the "value" entry contains the processed string.
            if (result.Resolution.TryGetValue("value", out object value))
            {
                age = Convert.ToInt32(value);
                if (age >= 18 && age <= 120)
                {
                    return true;
                }
            }
        }

        message = "Please enter an age between 18 and 120.";
    }
    catch
    {
        message = "I'm sorry, I could not interpret that as an age. Please enter an age between 18 and 120.";
    }

    return message is null;
}

private static bool ValidateDate(string input, out string date, out string message)
{
    date = null;
    message = null;

    // Try to recognize the input as a date-time. This works for responses such as "11/14/2018", "9pm", "tomorrow", "Sunday at 5pm", and so on.
    // The recognizer returns a list of potential recognition results, if any.
    try
    {
        List<ModelResult> results = DateTimeRecognizer.RecognizeDateTime(input, Culture.English);

        // Check whether any of the recognized date-times are appropriate,
        // and if so, return the first appropriate date-time. We're checking for a value at least an hour in the future.
        DateTime earliest = DateTime.Now.AddHours(1.0);
        foreach (ModelResult result in results)
        {
            // result.Resolution is a dictionary, where the "values" entry contains the processed input.
            var resolutions = result.Resolution["values"] as List<Dictionary<string, string>>;
            foreach (var resolution in resolutions)
            {
                // The processed input contains a "value" entry if it is a date-time value, or "start" and
                // "end" entries if it is a date-time range.
                if (resolution.TryGetValue("value", out string dateString)
                    || resolution.TryGetValue("start", out dateString))
                {
                    if (DateTime.TryParse(dateString, out var candidate)
                        && earliest < candidate)
                    {
                        date = candidate.ToShortDateString();
                        return true;
                    }
                }
            }
        }

        message = "I'm sorry, please enter a date at least an hour out.";
    }
    catch
    {
        message = "I'm sorry, I could not interpret that as an appropriate date. Please enter a date at least an hour out.";
    }

    return false;
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Fügen Sie Ihrem Bot die folgenden Validierungsmethoden hinzu.

```javascript
// Validates name input. Returns whether validation succeeded and either the parsed and normalized
// value or a message the bot can use to ask the user again.
static validateName(input) {
    const name = input && input.trim();
    return name != undefined
        ? { success: true, name: name }
        : { success: false, message: 'Please enter a name that contains at least one character.' };
};

// Validates age input. Returns whether validation succeeded and either the parsed and normalized
// value or a message the bot can use to ask the user again.
static validateAge(input) {

    // Try to recognize the input as a number. This works for responses such as "twelve" as well as "12".
    try {
        // Attempt to convert the Recognizer result to an integer. This works for "a dozen", "twelve", "12", and so on.
        // The recognizer returns a list of potential recognition results, if any.
        const results = Recognizers.recognizeNumber(input, Recognizers.Culture.English);
        let output;
        results.forEach(function (result) {
            // result.resolution is a dictionary, where the "value" entry contains the processed string.
            const value = result.resolution['value'];
            if (value) {
                const age = parseInt(value);
                if (!isNaN(age) && age >= 18 && age <= 120) {
                    output = { success: true, age: age };
                    return;
                }
            }
        });
        return output || { success: false, message: 'Please enter an age between 18 and 120.' };
    } catch (error) {
        return {
            success: false,
            message: "I'm sorry, I could not interpret that as an age. Please enter an age between 18 and 120."
        };
    }
}

// Validates date input. Returns whether validation succeeded and either the parsed and normalized
// value or a message the bot can use to ask the user again.
static validateDate(input) {
    // Try to recognize the input as a date-time. This works for responses such as "11/14/2018", "today at 9pm", "tomorrow", "Sunday at 5pm", and so on.
    // The recognizer returns a list of potential recognition results, if any.
    try {
        const results = Recognizers.recognizeDateTime(input, Recognizers.Culture.English);
        const now = new Date();
        const earliest = now.getTime() + (60 * 60 * 1000);
        let output;
        results.forEach(function (result) {
            // result.resolution is a dictionary, where the "values" entry contains the processed input.
            result.resolution['values'].forEach(function (resolution) {
                // The processed input contains a "value" entry if it is a date-time value, or "start" and
                // "end" entries if it is a date-time range.
                const datevalue = resolution['value'] || resolution['start'];
                // If only time is given, assume it's for today.
                const datetime = resolution['type'] === 'time'
                    ? new Date(`${now.toLocaleDateString()} ${datevalue}`)
                    : new Date(datevalue);
                if (datetime && earliest < datetime.getTime()) {
                    output = { success: true, date: datetime.toLocaleDateString() };
                    return;
                }
            });
        });
        return output || { success: false, message: "I'm sorry, please enter a date at least an hour out." };
    } catch (error) {
        return {
            success: false,
            message: "I'm sorry, I could not interpret that as an appropriate date. Please enter a date at least an hour out."
        };
    }
}
```

---

## <a name="test-the-bot-locally"></a>Lokales Testen des Bots
1. Führen Sie das Beispiel lokal auf Ihrem Computer aus. Wenn Sie eine Anleitung benötigen, helfen Ihnen die INFODATEIEN zum [C#](https://aka.ms/cs-primitive-prompt-sample)- bzw. [JS](https://aka.ms/js-primitive-prompt-sample)-Beispiel weiter.
1. Führen Sie den Test wie unten gezeigt mit dem Emulator durch.

![primitive-prompts](media/primitive-prompts.png)

## <a name="additional-resources"></a>Zusätzliche Ressourcen

In der [Dialogbibliothek](bot-builder-concept-dialog.md) werden Klassen bereitgestellt, mit denen viele Aspekte der Verwaltung von Konversationen automatisiert werden. 

## <a name="next-step"></a>Nächster Schritt

> [!div class="nextstepaction"]
> [Implementieren eines sequenziellen Konversationsablaufs](bot-builder-dialog-manage-conversation-flow.md)
