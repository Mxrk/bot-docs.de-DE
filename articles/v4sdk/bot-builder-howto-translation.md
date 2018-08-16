---
title: Übersetzen der Benutzereingabe, um einen mehrsprachige Bot bereitzustellen | Microsoft Docs
description: Informationen zum automatischen Übersetzen der Benutzereingabe in die native Sprache Ihres Bots und zum Rückübersetzen in die Sprache des Benutzers.
keywords: Übersetzung, übersetzen, mehrsprachig, Microsoft Translator
author: DeniseMak
ms.author: v-demak
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 04/06/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 13139755989afccd85b2e09267dc42619ec1f83c
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39301517"
---
# <a name="translate-user-input-to-make-your-bot-multilingual"></a>Übersetzen von Benutzereingaben zur Bereitstellung eines mehrsprachigen Bots

[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

Mit [Microsoft Translator](https://www.microsoft.com/en-us/translator/) können Nachrichten automatisch in die Sprache Ihres Bots übersetzt werden. Optional lässt sich die Antwort des Bots zurück in die Benutzersprache übersetzen. Wenn Sie Ihren Bot mit dem Übersetzungsfeature ausstatten, können Sie deutlich mehr Benutzer ansprechen. Der Programmkern des Bots muss dabei nicht verändert werden.
<!-- 
- [Get a Text Services key](#get-a-text-services-key)
- [Installing Packages](#installing-packages)
- [Combine translation with LUIS or QnA Maker](#using-luis)
- [Combine translation with QnA Maker](#using-qna-maker)
-->

In diesem Tutorial wird zur Übersetzung der Dienst Microsoft Translator verwendet und einem einfachen Bot hinzugefügt.

## <a name="get-a-text-services-key"></a>Abrufen eines Textdienstschlüssels

Zunächst benötigen Sie zur Verwendung des Textübersetzungsdiensts einen Schlüssel. Sie können einen [Schlüssel für die kostenlose Testversion](https://www.microsoft.com/en-us/translator/trial.aspx#get-started) im Azure-Portal abrufen.

## <a name="installing-packages"></a>Pakete werden installiert.

Stellen Sie sicher, dass Sie über die Pakete verfügen, die zum Hinzufügen von Übersetzungsfeatures zu Ihrem Bot erforderlich sind.

# <a name="ctabcs"></a>[C#](#tab/cs)

[Fügen Sie einen Verweis](https://docs.microsoft.com/en-us/nuget/tools/package-manager-ui) auf die Vorabversion der folgenden NuGet-Pakete hinzu:

* `Microsoft.Bot.Builder.Integration.AspNet.Core`
* `Microsoft.Bot.Builder.Ai.Translation` (erforderlich für die Übersetzung)

Wenn Sie vorhaben, die Übersetzung mit Language Understanding (LUIS) zu kombinieren, fügen Sie auch einen Verweis auf Folgendes hinzu:

* `Microsoft.Bot.Builder.Ai.Luis` (für LUIS erforderlich)

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)

Jeder dieser Dienste kann Ihrem Bot mit dem Paket „botbuilder-ai“ hinzugefügt werden. Sie können Ihrem Projekt dieses Paket über npm hinzufügen:
* `npm install --save botbuilder@preview`
* `npm install --save botbuilder-ai@preview`

---

## <a name="configure-translation"></a>Konfigurieren der Übersetzung

Sie können Ihren Bot nun so konfigurieren, dass er den Übersetzungsdienst bei jeder vom Benutzer empfangenen Nachricht aufruft. Hierzu müssen Sie den Dienst einfach dem Middlewarestapel Ihres Bots hinzufügen. Die Middleware verwendet das Übersetzungsergebnis zum Ändern der Benutzernachricht mithilfe des Kontextobjekts.


# <a name="ctabcs"></a>[C#](#tab/cs)

Beginnen Sie mit dem EchoBot-Beispiel im v4 SDK, und passen Sie die `ConfigureServices`-Methode in der Datei `Startup.cs` so an, dass `TranslationMiddleware` zu Ihrem Bot hinzugefügt wird. Dadurch wird Ihr Bot so konfiguriert, dass jede vom Benutzer empfange Nachricht übersetzt wird. <!--, by simply adding it to your bot's middleware set. The middleware stores the translation results on the context object. -->
-   Aktualisieren Sie Ihre using-Anweisungen.
-   Fügen Sie der `ConfigureServices`-Methode die Übersetzungsmiddleware hinzu.

    Im folgenden Codeausschnitt wurden zur Vereinfachung die meisten Kommentare und der Middlewarecode zur Ausnahmebehandlung entfernt.

**Startup.cs**
```csharp
using System;
using System.Collections.Generic;
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Bot.Builder.Ai.Translation;
using Microsoft.Bot.Builder.BotFramework;
using Microsoft.Bot.Builder.Core.Extensions;
using Microsoft.Bot.Builder.Integration.AspNet.Core;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Logging;
```
```csharp
// This method gets called by the runtime. Use this method to add services to the container.
public void ConfigureServices(IServiceCollection services)
{
    services.AddBot<EchoBot>(options =>
    {
        options.CredentialProvider = new ConfigurationCredentialProvider(Configuration);
        var middleware = options.Middleware;
        // Add translation middleware
        // The first parameter is a list of languages the bot recognizes
        middleware.Add(new TranslationMiddleware(new string[] { "en" }, "<YOUR MICROSOFT TRANSLATOR TEXT API KEY>", false));

    });
}


```

> [!TIP] 
> Das Bot Builder SDK erkennt die Sprache des Benutzers automatisch anhand der Nachricht, die er gerade gesendet hat. Um diese Funktionalität zu überschreiben, können Sie zusätzliche Rückrufparameter angeben, um Ihre eigene Logik zur Erkennung und Änderung der Sprache des Benutzers hinzuzufügen.  



Sehen Sie sich den Code in `EchoBot.cs` an, durch den „You sent“ und die Benutzeraussage gesendet wird:

```cs
using Microsoft.Bot.Builder;
using Microsoft.Bot.Schema;
using System.Threading.Tasks;

namespace Microsoft.Bot.Samples
{
    public class EchoBot : IBot
    {
        public EchoBot() { }

        public async Task OnTurn(ITurnContext context)
        {
            switch (context.Activity.Type)
            {
                case ActivityTypes.Message:
                // echo back the user's input.
                    await context.SendActivity($"You sent '{context.Activity.Text}'");


                case ActivityTypes.ConversationUpdate:
                    foreach (var newMember in context.Activity.MembersAdded)
                    {
                        if (newMember.Id != context.Activity.Recipient.Id)
                        {
                            await context.SendActivity("Hello and welcome to the echo bot.");
                        }
                    }
                    break;
            }
        }
        
    }
}
```

Wenn Sie Übersetzungsmiddleware hinzufügen, gibt ein optionaler Parameter an, ob Antworten zurück in die Sprache des Benutzers übersetzt werden sollen. In `Startup.cs` wurde `false` angegeben, damit nur die Benutzernachrichten in die Sprache des Bots übersetzt werden.

```cs
// The first parameter is a list of languages the bot recognizes
// The second parameter is your API key
// The third parameter specifies whether to translate the bot's replies back to the user's language
middleware.Add(new TranslationMiddleware(new string[] { "en" }, "<YOUR MICROSOFT TRANSLATOR TEXT API KEY>", false));
```

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)

Fügen Sie folgenden Code in die Datei „app.js“ ein, um die Übersetzungsmiddleware mit einem Echobot einzurichten:

```javascript
const { BotFrameworkAdapter, MemoryStorage, ConversationState } = require('botbuilder');
const { LanguageTranslator, LocaleConverter } = require('botbuilder-ai');
const restify = require('restify');

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

// Add language translator middleware
const languageTranslator = new LanguageTranslator({
    translatorKey: "xxxxxx",
    noTranslatePatterns: new Set(),
    nativeLanguages: ['en'] 
});
adapter.use(languageTranslator);


// Listen for incoming requests 
server.post('/api/messages', (req, res) => {
    // Route received request to adapter for processing
    adapter.processActivity(req, res, async (context) => {
        if (context.activity.type === 'message') {
            await context.sendActivity(`You said "${context.activity.text}"`);
        } else {
            await context.sendActivity(`[${context.activity.type} event detected]`);
        }
    });
});
```

---

## <a name="run-the-bot-and-see-translated-input"></a>Ausführen des Bots und Anzeigen der übersetzten Eingabe

Führen Sie den Bot aus, und geben Sie einige Nachrichten in anderen Sprachen ein. Der Bot übersetzt die Benutzernachricht und zeigt diese in seiner Antwort an.

![Bot erkennt die Sprache und übersetzt die Eingabe](./media/how-to-bot-translate/bot-detects-language-translates-input.png)




## <a name="invoke-logic-in-the-bots-native-language"></a>Aufrufen von Logik in der nativen Sprache des Bots

Fügen Sie nun Logik hinzu, die überprüft, ob englische Wörter vorhanden sind. Wenn der Benutzer „help“ oder „cancel“ in einer anderen Sprache eingibt, übersetzt der Bot dies ins Englische, und die Logik, die nach den englischen Wörtern „help“ oder „cancel“ sucht, wird aufgerufen.

# <a name="ctabcs"></a>[C#](#tab/cs)
Aktualisieren Sie in `EchoBot.cs` die `case`-Anweisung für Nachrichtenaktivitäten in der Methode `OnTurn` Ihres Bots.
```cs
case ActivityTypes.Message:
    // check the message text before calling context.SendActivity
    var messagetext = context.Activity.Text.Trim().ToLower();
    if (string.Equals("help", messagetext))
    {
        await context.SendActivity("You asked for help.");
    }
```

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)
```javascript
if (context.activity.type === 'message') {
    // check the message text before calling context.sendActivity
    var messagetext = context.activity.text.trim().toLowerCase();
    if(messagetext === "help"){
        await context.sendActivity("you said help");
    }
}
```

---

![Bot erkennt „Hilfe“ auf Französisch](./media/how-to-bot-translate/bot-detects-help-french.png)



## <a name="translate-replies-back-to-the-users-language"></a>Zurückübersetzen von Antworten in die Sprache des Benutzers

Sie können auch Antworten in die Sprache des Benutzers zurückübersetzen, indem Sie den letzten Konstruktorparameter auf `true` festlegen.

# <a name="ctabcs"></a>[C#](#tab/cs)
Aktualisieren Sie in `Startup.cs` die folgende Codezeile der `ConfigureServices`-Methode.
```cs
// Use language recognition to detect the user's language from their message, instead of providing helper callbacks.
// Last parameter indicates that we'll translate replies back to the user's language
middleware.Add(new TranslationMiddleware(new string[] { "en" }, "TRANSLATION-SUBSCRIPTION-KEY", true));
```

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)
```javascript
// Use language recognition to detect the user's language from their message, instead of providing helper callbacks.
// Last parameter indicates that we'll translate replies back to the user's language
const languageTranslator = new LanguageTranslator({
    translatorKey: "xxxxxx",
    noTranslatePatterns: new Set(),
    nativeLanguages: ['en'],
    translateBackToUserLanguage: true
});
adapter.use(languageTranslator);
```

---

## <a name="run-the-bot-to-see-replies-in-the-users-language"></a>Ausführen des Bots, um Antworten in der Sprache des Benutzers anzuzeigen

Führen Sie den Bot aus, und geben Sie einige Nachrichten in anderen Sprachen ein. Die Sprache des Benutzers wird erkannt und die Antwort übersetzt.

![Bot erkennt die Sprache und übersetzt die Antwort](./media/how-to-bot-translate/bot-detects-language-translates-response.png)


## <a name="adding-logic-for-detecting-or-changing-the-user-language"></a>Hinzufügen von Logik zum Erkennen oder Ändern der Benutzersprache

Anstatt das Bot Builder SDK die Sprache des Benutzers automatisch erkennen zu lassen, können Sie einen Rückruf bereitstellen, um Ihre eigene Logik zur Bestimmung der Sprache des Benutzers oder zur Bestimmung, wann sich die Sprache des Benutzers geändert hat, hinzuzufügen.


Im folgenden Beispiel überprüft der `CheckUserChangedLanguage`-Rückruf, ob eine bestimmte Benutzernachricht vorhanden ist, um die Sprache zu ändern. 

# <a name="ctabcs"></a>[C#](#tab/cs)
Fügen Sie der Übersetzungsmiddleware in `Startup.cs` einen Rückruf hinzu.
```csharp
middleware.Add(new TranslationMiddleware(new string[] { "en" },
     "<YOUR MICROSOFT TRANSLATOR TEXT API KEY>", null,
     TranslatorLocaleHelper.GetActiveLanguage,
     TranslatorLocaleHelper.CheckUserChangedLanguage));
```
Fügen Sie die Datei `TranslatorLocalHelper.cs` hinzu, und kopieren Sie folgenden Code in die TranslatorLocalHelper-Klassendefinition.
```csharp
    class CurrentUserState
    {
        public string Language { get; set; }
    }

    public static void SetLanguage(ITurnContext context, string language) =>
        context.GetConversationState<CurrentUserState>().Language = language;

    public static bool IsSupportedLanguage(string language) =>
        _supportedLanguages.Contains(language);

    public static async Task<bool> CheckUserChangedLanguage(ITurnContext context)
    {
        bool changeLang = false;
        
        // use a specific message from user to change language
        if (context.Activity.Type == ActivityTypes.Message)
        {
            var messageActivity = context.Activity.AsMessageActivity();
            if (messageActivity.Text.ToLower().StartsWith("set my language to"))
            {
                changeLang = true;
            }
            if (changeLang)
            {
                var newLang = messageActivity.Text.ToLower().Replace("set my language to", "").Trim();
                if (!string.IsNullOrWhiteSpace(newLang)
                        && IsSupportedLanguage(newLang))
                {
                    SetLanguage(context, newLang);
                    await context.SendActivity($@"Changing your language to {newLang}");
                }
                else
                {
                    await context.SendActivity($@"{newLang} is not a supported language.");
                }
                // return true to intercept message from further processesing
                return true;
            }
        }

        return false;
    }

    public static string GetActiveLanguage(ITurnContext context)
    {

        if (context.Activity.Type == ActivityTypes.Message
            && context.GetConversationState<CurrentUserState>() != null
            && context.GetConversationState<CurrentUserState>().Language != null)
        {
            return context.GetConversationState<CurrentUserState>().Language;
        }

        return "en";
    }

```

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)
```javascript
// When the user inputs 'set my language to fr'
// The bot will automatically change all text to French
async function setUserLanguage(context) {
    let state = conversationState.get(context)
    if (context.activity.text.toLowerCase().startsWith('set my language to')) {
        state.language = context.activity.text.toLowerCase().replace('set my language to', '').trim();
        await context.sendActivity(`Setting your language to ${state.language}`);
        return Promise.resolve(true);
    } else {
        return Promise.resolve(false);
    }
}

// Add language translator middleware
const languageTranslator = new LanguageTranslator({
    translatorKey: "<YOUR MICROSOFT TRANSLATOR TEXT API KEY>",
    noTranslatePatterns: new Set(),
    nativeLanguages: ['en'],
    setUserLanguage: setUserLanguage,
    translateBackToUserLanguage: true
});
adapter.use(languageTranslator);

// Listen for incoming activity 
server.post('/api/messages', (req, res) => {
    // Route received activity to adapter for processing
    adapter.processActivity(req, res, async (context) => {
        if (context.activity.type != 'message') {
            await context.sendActivity(`[${context.activity.type} event detected]`);
        } else {
            await context.sendActivity(`You said: ${context.activity.text}`)
        }
    });
});

```

---

## <a name="combining-luis-or-qna-with-translation"></a>Kombinieren von LUIS oder QnA mit dem Übersetzungsfeature

Wenn Sie das Übersetzungsfeature mit anderen Diensten in Ihrem Bot kombinieren (etwa LUIS oder QnA Maker), fügen Sie zuerst die Übersetzungsmiddleware hinzu, sodass die Nachrichten übersetzt werden, bevor sie an eine andere Middleware weitergeleitet werden, die die native Sprache des Bots erwartet.

# <a name="ctabcs"></a>[C#](#tab/cs)
```cs
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton(_ => Configuration);
    services.AddBot<AiBot>(options =>
    {
        options.CredentialProvider = new ConfigurationCredentialProvider(Configuration);
        var middleware = options.Middleware;

        // Add translation middleware
        // The first parameter is a list of languages the bot recognizes. Input in these language won't be translated.
        middleware.Add(new TranslationMiddleware(new string[] { "en" }, "<YOUR MICROSOFT TRANSLATOR TEXT API KEY>", false));

        // Add LUIS middleware after translation middleware to pass translated messages to LUIS
        middleware.Add(
            new LuisRecognizerMiddleware(
                new LuisModel("luisAppId", "subscriptionId", new Uri("luisModelBaseUrl"))));
    });
}
```

In Ihrem Botcode basieren die LUIS-Ergebnisse auf Eingaben, die bereits in die native Sprache des Bots übersetzt wurden. Versuchen Sie, den Botcode so zu ändern, dass er die Ergebnisse einer LUIS-App überprüft:

```cs
public async Task OnTurn(ITurnContext context)
{
    switch (context.Activity.Type)
    {
        case ActivityTypes.Message:
            // check LUIS results
            var luisResult = context.Services.Get<RecognizerResult>(LuisRecognizerMiddleware.LuisRecognizerResultKey);
            (string key, double score) topItem = luisResult.GetTopScoringIntent();
            await context.SendActivity($"The top intent was: '{topItem.key}', with score {topItem.score}");

            await context.SendActivity($"You sent '{context.Activity.Text}'");

        case ActivityTypes.ConversationUpdate:
            foreach (var newMember in context.Activity.MembersAdded)
            {
                if (newMember.Id != context.Activity.Recipient.Id)
                {
                    await context.SendActivity("Hello and welcome to the echo bot.");
                }
            }
            break;
        }
    }  
}          
```

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)
```javascript
// Add language translator middleware
const languageTranslator = new LanguageTranslator({
    translatorKey: "xxxxxx",
    noTranslatePatterns: new Set(),
    nativeLanguages: ['en'],
    translateBackToUserLanguage: false
});
adapter.use(languageTranslator);

// Add Luis recognizer middleware
const luisRecognizer = new LuisRecognizer({
    appId: "xxxxxx",
    subscriptionKey: "xxxxxxx"
});
adapter.use(luisRecognizer);


// Listen for incoming requests 
server.post('/api/messages', (req, res) => {
    // Route received request to adapter for processing
    adapter.processActivity(req, res, async (context) => {
        if (context.activity.type != 'message') {
            await context.sendActivity(`[${context.activity.type} event detected]`);
        } else {
            let results = luisRecognizer.get(context);
            for (const intent in results.intents) {
                await context.sendActivity(`intent: ${intent.toString()} : ${results.intents[intent.toString()]}`)
            }
        }
    });
});
```

---

## <a name="bypass-translation-for-specified-patterns"></a>Umgehen der Übersetzung für angegebene Muster
Möglicherweise gibt es bestimmte Wörter, die Ihr Bot nicht übersetzen soll, z.B. Eigennamen. Sie können reguläre Ausdrücke bereitstellen, um Muster anzugeben, die nicht übersetzt werden sollen. Wenn der Benutzer z.B. „Mein Name ist...“ in einer anderen als der nativen Sprache Ihres Bots sagt, und Sie seinen Namen nicht übersetzen möchten, können Sie dies durch ein Muster festlegen.

# <a name="ctabcs"></a>[C#](#tab/cs)
In „Startup.cs“
```cs
// Pattern representing input to not translate
Dictionary<string, List<string>> patterns = new Dictionary<string, List<string>>();
// single pattern for fr language, to avoid translating what follows "my name is"
patterns.Add("fr", new List<string> { "mon nom est (.+)" });

middleware.Add(new TranslationMiddleware(new string[] { "en" },
    "<YOUR API KEY>", patterns,
    TranslatorLocaleHelper.GetActiveLanguage,
    TranslatorLocaleHelper.CheckUserChangedLanguage));

```
<!-- TODO: ADD more explanation (both of these callbacks are run every time), fix image by debugging regex for l'etat -->

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)
```javascript
// Add language translator middleware
const languageTranslator = new LanguageTranslator({
    translatorKey: "<YOUR API KEY>",
    // single pattern for fr language, to avoid translating what follows "my name is"
    noTranslatePatterns: new Set("fr", "/mon nom est (.+)/"),
    nativeLanguages: ['en'],
    translateBackToUserLanguage: false,
    
});
adapter.use(languageTranslator);

```

---

![Bot umgeht die Übersetzung mithilfe eines Musters](./media/how-to-bot-translate/bot-no-translate-name-fr.png)

## <a name="localize-dates"></a>Lokalisieren von Datumsangaben

Wenn Sie Datumsangaben lokalisieren müssen, können Sie `LocaleConverterMiddleware` hinzufügen. Wenn Sie beispielsweise wissen, dass Ihr Bot Datumsangaben im Format `MM/DD/YYYY` erwartet und Benutzer in anderen Gebietsschemas Datumsangaben im Format `DD/MM/YYYY` eingeben, kann die Gebietsschema-Konvertierungsmiddleware Datumsangaben automatisch in das von Ihrem Bot erwartete Format konvertieren.

> [!NOTE]
> Die Gebietsschema-Konvertierungsmiddleware ist dafür konzipiert, nur Datumsangaben zu konvertieren. Sie kennt die Ergebnisse der Übersetzungsmiddleware nicht. Wenn Sie Übersetzungsmiddleware verwenden, achten Sie darauf, wie Sie diese mit der Gebietsschemakonvertierung kombinieren. Die Übersetzungsmiddleware übersetzt einige Datumsangaben im Textformat zusammen mit anderen Texteingaben, aber sie übersetzt keine Datumsangaben.

Auf dem folgenden Bild ist beispielsweise zu sehen, wie ein Bot die Benutzereingabe nach der Übersetzung aus dem Englischen ins Französische zurücksendet. Er verwendet `TranslationMiddleware` ohne `LocaleConverterMiddleware`.

![Bot, der Datumsangaben übersetzt, ohne diese zu konvertieren](./media/how-to-bot-translate/locale-date-before.png)

Im folgenden Beispiel ist derselbe Bot zu sehen. Dieser verwendet nun jedoch `LocaleConverterMiddleware`.

![Bot, der Datumsangaben übersetzt, ohne diese zu konvertieren](./media/how-to-bot-translate/locale-date-after.png)

Die Gebietsschemakonvertierung unterstützt die Gebietsschemas Englisch, Französisch, Deutsch und Chinesisch. <!-- TODO: ADD DETAIL ABOUT SUPPORTED LOCALES -->

# <a name="ctabcs"></a>[C#](#tab/cs)
In „Startup.cs“
```cs
// Add locale converter middleware
middleware.Add(new LocaleConverterMiddleware(
    TranslatorLocaleHelper.GetActiveLocale,
    TranslatorLocaleHelper.CheckUserChangedLocale,
     "en-us", LocaleConverter.Converter));
```
In „TranslatorLocaleHelper.cs“
```cs
// TranslatorLocaleHelper.cs
public static async Task<bool> CheckUserChangedLocale(ITurnContext context)
{
    bool changeLocale = false;//logic implemented by developper to make a signal for language changing 
    //use a specific message from user to change language
    if (context.Activity.Type == ActivityTypes.Message)
    {
        var messageActivity = context.Activity.AsMessageActivity();
        if (messageActivity.Text.ToLower().StartsWith("set my locale to"))
        {
            changeLocale = true;
        }
        if (changeLocale)
        {
            var newLocale = messageActivity.Text.ToLower().Replace("set my locale to", "").Trim(); //extracted by the user using user state 
            if (!string.IsNullOrWhiteSpace(newLocale)
                    && IsSupportedLanguage(newLocale))
            {
                SetLocale(context, newLocale);
                await context.SendActivity($@"Changing your language to {newLocale}");
            }
            else
            {
                await context.SendActivity($@"{newLocale} is not a supported locale.");
            }
            //intercepts message
            return true;
        }
    }

    return false;
}
public static string GetActiveLocale(ITurnContext context)
{
    if (currentLocale != null)
    {
        //the user has specified a different locale so update the bot state
        if (context.GetConversationState<CurrentUserState>() != null
            && currentLocale != context.GetConversationState<CurrentUserState>().Locale)
        {
            SetLocale(context, currentLocale);
        }
    }
    if (context.Activity.Type == ActivityTypes.Message
        && context.GetConversationState<CurrentUserState>() != null && context.GetConversationState<CurrentUserState>().Locale != null)
    {
        return context.GetConversationState<CurrentUserState>().Locale;
    }

    return "en-us";
}
```

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)

<!-- this snippet only works if the user doesn't actually try to change their locale.  Emailed Mostafa about the issue 
It should change the locale after you type in 'set my locale to....' -->

```javascript
// Delegates for getting and setting user locale
function getUserLocale(context) {
    const state = conversationState.get(context)
    if (state.locale == undefined) {
        return 'en-us';
    } else {
        return state.locale;
    }
}

// Function to change the user's locale.
async function setUserLocale(context) {
    let state = conversationState.get(context)
    if (context.activity.text.toLowerCase().startsWith('set my locale to')) {        
        state.locale = context.activity.text.toLowerCase().replace('set my locale to', '').trim();
        await context.sendActivity(`Setting your locale to ${state.locale}`);
        return Promise.resolve(true);
    } else {
        return Promise.resolve(false);
    }
}

// Add locale converter middleware
// Will convert input to fr-fr
const localeConverter = new LocaleConverter({
    toLocale: 'fr-fr',
    setUserLocale: setUserLocale,
    getUserLocale: getUserLocale
});
adapter.use(localeConverter);


// Listen for incoming requests 
server.post('/api/messages', (req, res) => {
    // Route received request to adapter for processing
    adapter.processActivity(req, res, async (context) => {
        if (context.activity.type != 'message') {
            await context.sendActivity(`[${context.activity.type} event detected]`);
        } else {
            await context.sendActivity(`The message that you sent was:  ${context.activity.text}`)
        }
    });
});

```

---
