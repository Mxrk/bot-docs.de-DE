---
title: Unterstützen der Lokalisierung | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie mit dem Bot Framework SDK für Node.js den Standort des Benutzers bestimmen und die Lokalisierungsfunktion aktivieren.
author: DeniseMak
ms.author: v-demak
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: d592aa8b37e1d73e3cf9003209b985b8ca0f03f8
ms.sourcegitcommit: b15cf37afc4f57d13ca6636d4227433809562f8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/11/2019
ms.locfileid: "54224395"
---
# <a name="support-localization"></a>Unterstützen der Lokalisierung

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

Bot Builder enthält ein umfangreiches Lokalisierungssystem zum Erstellen von Bots, die mit dem Benutzer in mehreren Sprachen kommunizieren können. Alle Eingabeaufforderungen Ihres Bots können mithilfe von in der Verzeichnisstruktur des Bots gespeicherten JSON-Dateien lokalisiert werden. Wenn Sie ein System wie LUIS zum Verarbeiten natürlicher Sprache verwenden, können Sie [LuisRecognizer][LUISRecognizer] mit einem separaten Modell für jede Sprache konfigurieren, die der Bot unterstützt. Das SDK wählt dann automatisch das Modell aus, das dem bevorzugten Gebietsschema des Benutzers entspricht.

## <a name="determine-the-locale-by-prompting-the-user"></a>Bestimmen des Gebietsschemas durch Benutzereingabeaufforderungen
Beim Lokalisieren Ihres Bots müssen Sie zuerst die Möglichkeit zum Identifizieren der bevorzugten Sprache des Benutzers hinzufügen. Das SDK stellt die Methode [session.preferredLocale()][preferredLocal] bereit, um diese Einstellung auf Benutzerbasis abzurufen und zu speichern. Im folgenden Beispiel wird der Benutzer mithilfe eines Dialogs zur Eingabe seiner bevorzugten Sprache aufgefordert. Anschließend wird die Auswahl gespeichert.

``` javascript
bot.dialog('/localePicker', [
    function (session) {
        // Prompt the user to select their preferred locale
        builder.Prompts.choice(session, "What's your preferred language?", 'English|Español|Italiano');
    },
    function (session, results) {
        // Update preferred locale
        var locale;
        switch (results.response.entity) {
            case 'English':
                locale = 'en';
                break;
            case 'Español':
                locale = 'es';
                break;
            case 'Italiano':
                locale = 'it';
                break;
        }
        session.preferredLocale(locale, function (err) {
            if (!err) {
                // Locale files loaded
                session.endDialog(`Your preferred language is now ${results.response.entity}`);
            } else {
                // Problem loading the selected locale
                session.error(err);
            }
        });
    }
]);
```

## <a name="determine-the-locale-by-using-analytics"></a>Bestimmen des Gebietsschemas anhand von Analysen
Die Verwendung eines Diensts wie die [Textanalyse-API](/azure/cognitive-services/cognitive-services-text-analytics-quick-start) ist eine weitere Möglichkeit zum Bestimmen des Gebietsschemas des Benutzers. Die Sprache des Benutzers wird automatisch anhand des Texts der gesendeten Nachricht erkannt.

Der folgende Codeausschnitt veranschaulicht, wie Sie diesen Dienst in Ihren eigenen Bot integrieren können.
``` javascript
var request = require('request');

bot.use({
    receive: function (event, next) {
        if (event.text && !event.textLocale) {
            var options = {
                method: 'POST',
                url: 'https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/languages?numberOfLanguagesToDetect=1',
                body: { documents: [{ id: 'message', text: event.text }]},
                json: true,
                headers: {
                    'Ocp-Apim-Subscription-Key': '<YOUR API KEY>'
                }
            };
            request(options, function (error, response, body) {
                if (!error && body) {
                    if (body.documents && body.documents.length > 0) {
                        var languages = body.documents[0].detectedLanguages;
                        if (languages && languages.length > 0) {
                            event.textLocale = languages[0].iso6391Name;
                        }
                    }
                }
                next();
            });
        } else {
            next();
        }
    }
});
```

Wenn Sie Ihrem Bot den obigen Codeausschnitt hinzugefügt haben, wird durch Aufrufen von [session.preferredLocale()][preferredLocal] automatisch die erkannte Sprache zurückgegeben. Die Suchreihenfolge für `preferredLocale()` lautet wie folgt:
1. Durch Aufrufen von `session.preferredLocale()` gespeichertes Gebietsschema. Dieser Wert wird in `session.userData['BotBuilder.Data.PreferredLocale']` gespeichert.
2. Erkanntes und `session.message.textLocale` zugewiesenes Gebietsschema.
3. Konfiguriertes Standardgebietsschema für den Bot (z.B. Englisch („en“)).

Das Standardgebietsschema des Bots können Sie mit dem entsprechenden Konstruktor konfigurieren:

```javascript
var bot = new builder.UniversalBot(connector, {
    localizerSettings: { 
        defaultLocale: "es" 
    }
});
```

## <a name="localize-prompts"></a>Lokalisieren von Eingabeaufforderungen
Das standardmäßige Lokalisierungssystem für das Bot Framework SDK ist dateibasiert und ermöglicht dem Bot mithilfe von JSON-Dateien, die auf dem Datenträger gespeichert sind, die Unterstützung mehrerer Sprachen. Standardmäßig sucht das Lokalisierungssystem in der Datei **./locale/<IETF TAG>/index.json** nach den Eingabeaufforderungen des Bots. Dabei stellt <IETF TAG> ein gültiges [IETF-Sprachtag][IEFT] für das bevorzugte Gebietsschema dar, für das Eingabeaufforderungen gesucht werden. 

Der folgende Screenshot zeigt die Verzeichnisstruktur für einen Bot, der drei Sprachen unterstützt: Englisch, Italienisch und Spanisch.

![Verzeichnisstruktur für drei Gebietsschemas](../media/locale-dir.png)

Die Struktur der Datei ist eine einfache JSON-Zuordnung von Nachrichten-IDs zu lokalisierten Textzeichenfolgen. Falls der Wert keine Zeichenfolge, sondern ein Array ist, wird beim Abrufen dieses Werts mithilfe von [session.localizer.gettext()][GetText] nach dem Zufallsprinzip eine Eingabeaufforderung aus dem Array ausgewählt. 

Der Bot ruft automatisch die lokalisierte Version einer Nachricht ab, wenn Sie in einem Aufruf von [session.send()](http://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.session#send) anstelle eines sprachspezifischen Texts die Nachrichten-ID übergeben:

```javascript
var bot = new builder.UniversalBot(connector, [
    function (session) {
        session.send("greeting");
        session.send("instructions");
        session.beginDialog('/localePicker');
    },
    function (session) {
        builder.Prompts.text(session, "text_prompt");
    }
]);
```

Intern ruft das SDK [`session.preferredLocale()`][preferredLocale] auf, um das bevorzugte Gebietsschema des Benutzers abzurufen, und verwendet dieses dann in einem Aufruf von [`session.localizer.gettext()`][GetText], um die Nachrichten-ID der lokalisierten Zeichenfolge zuzuordnen.  Manchmal werden Sie den Lokalisierer manuell aufrufen müssen. Beispielsweise werden an [`Prompts.choice()`][promptsChoice] übergebene Enumerationswerte niemals automatisch lokalisiert. Daher müssen Sie möglicherweise vor dem Aufrufen der Eingabeaufforderung manuell eine lokalisierte Liste abrufen:

```javascript
var options = session.localizer.gettext(session.preferredLocale(), "choice_options");
builder.Prompts.choice(session, "choice_prompt", options);
```

Der Standardlokalisierer sucht in mehreren Dateien nach einer Nachrichten-ID. Wenn er keine ID findet (oder wenn keine Lokalisierungsdateien bereitgestellt wurden), gibt er einfach den Text der ID zurück und macht auf diese Weise die Verwendung von Lokalisierungsdateien transparent und optional.  Dateien werden in der folgenden Reihenfolge durchsucht:

1. Die Datei **index.json** unter dem von [`session.preferredLocale()`][preferredLocale] zurückgegebenen Gebietsschema.
2. Wenn das Gebietsschema ein optionales untergeordnetes Tag wie **en-US** enthält, wird das Stammtag von **en** in die Suche einbezogen.
3. Das konfigurierte Standardgebietsschema des Bots.

## <a name="use-namespaces-to-customize-and-localize-prompts"></a>Verwenden von Namespaces zum Anpassen und Lokalisieren von Eingabeaufforderungen
Der Standardlokalisierer unterstützt bei Eingabeaufforderungen das Verwenden von Namespaces, um Konflikte zwischen Nachrichten-IDs zu vermeiden.  Ihr Bot kann Eingabeaufforderungen in Namespaces überschreiben, um die Eingabeaufforderungen aus einem anderen Namespace anzupassen oder neu zu formulieren.  Nutzen Sie diese Funktion, um die im SDK integrierten Nachrichten anzupassen und entweder Unterstützung für weitere Sprachen hinzuzufügen oder einfach die aktuellen Nachrichten im SDK neu zu formulieren.  Sie können z. B. die Standardfehlermeldung des SDK ändern, indem Sie einfach eine Datei namens **BotBuilder.json** zum Gebietsschemaverzeichnis Ihres Bots und anschließend einen Eintrag für die Nachrichten-ID `default_error` hinzufügen:

![BotBuilder.json für Gebietsschema-Namespaces](../media/locale-namespacing.png)


## <a name="additional-resources"></a>Zusätzliche Ressourcen

Informationen zum Lokalisieren einer Erkennung finden Sie unter [Erkennen von Absichten](bot-builder-nodejs-recognize-intent-messages.md).


[LUIS]: https://www.luis.ai/
[IMessage]: http://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.imessage
[IntentRecognizerSetOptions]: https://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.iintentrecognizersetoptions.html
[LUISRecognizer]: https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.luisrecognizer
[LUISSample]: https://aka.ms/v3-js-luisSample
[DisambiguationSample]: https://aka.ms/v3-js-onDisambiguateRoute
[preferredLocal]: https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.session#preferredlocale
[preferredLocale]: https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.session#preferredlocale
[promptsChoice]: https://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.__global.iprompts.html#choice
[GetText]: https://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.ilocalizer.html#gettext
[IEFT]: https://en.wikipedia.org/wiki/IETF_language_tag

