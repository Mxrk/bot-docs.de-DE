---
title: Verwenden von QnA Maker zum Beantworten von Fragen | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie QnA Maker in Ihrem Bot verwenden können.
keywords: Fragen und Antworten, QnA, häufig gestellte Fragen, FAQ, Middleware
author: ivorb
ms.author: v-ivorb
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: cognitive-services
ms.date: 10/08/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 3d488cc2bb61ef460ed45707596cb7db9e6c23e8
ms.sourcegitcommit: b78fe3d8dd604c4f7233740658a229e85b8535dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2018
ms.locfileid: "49999079"
---
# <a name="use-qna-maker-to-answer-questions"></a>Verwenden von QnA Maker zum Beantworten von Fragen

[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

Sie können den QnA Maker-Dienst verwenden, um Ihrem Bot Unterstützung für Fragen und Antworten hinzuzufügen. Eine der Grundvoraussetzungen beim Erstellen Ihres eigenen QnA Maker-Diensts ist es, ihn mit Fragen und Antworten zu versehen. In vielen Fällen sind die Fragen und Antworten bereits in Inhalten wie FAQs oder anderer Dokumentation enthalten. In anderen Fällen möchten Sie Ihre Antworten auf Fragen auf eine natürlichere, verständlichere Art und Weise anpassen.

## <a name="prerequisites"></a>Voraussetzungen
- Erstellen eines [QnA Maker](https://www.qnamaker.ai/)-Kontos
- Herunterladen des QnA Maker-Beispiels [[C#](https://aka.ms/cs-qna) | [JavaScript](https://aka.ms/js-qna-sample)]

## <a name="create-a-qna-maker-service-and-publish-a-knowledge-base"></a>Erstellen eines QnA Maker-Diensts und Veröffentlichen einer Wissensdatenbank

Befolgen Sie nach dem Erstellen des QnA Maker-Kontos die Anleitung zum Erstellen eines [QnA Maker-Diensts](https://docs.microsoft.com/en-us/azure/cognitive-services/qnamaker/how-to/set-up-qnamaker-service-azure) und einer [Wissensdatenbank](https://docs.microsoft.com/en-us/azure/cognitive-services/qnamaker/quickstarts/create-publish-knowledge-base). 

Nach dem Veröffentlichen Ihrer Wissensdatenbank müssen Sie sich die folgenden Werte notieren, um Ihren Bot programmgesteuert mit der Wissensdatenbank verbinden zu können.
- Wählen Sie auf der [QnA Maker](https://www.qnamaker.ai/)-Website Ihre Wissensdatenbank aus.
- Wählen Sie bei geöffneter Wissensdatenbank die Option **Einstellungen**. Notieren Sie sich den angezeigten Wert für _Dienstname_ als <Ihren_Wissensdatenbank-Namen>.
- Scrollen Sie nach unten zu den **Bereitstellungsdetails**, und notieren Sie sich die folgenden Werte:
   - POST /knowledgebases/<Ihre_Wissensdatenbank-ID>/generateAnswer
   - Host: https://<Ihr_Hostname>.azurewebsites.net/qnamaker
   - Autorisierung: EndpointKey <Ihr_Endpunktschlüssel>

## <a name="installing-packages"></a>Installieren von Paketen

Bevor wir zur Programmierung kommen, stellen Sie sicher, dass Sie über die Pakete verfügen, die für QnA Maker erforderlich sind.

# <a name="ctabcs"></a>[C#](#tab/cs)

Fügen Sie Ihrem Bot das folgende [NuGet-Paket](https://docs.microsoft.com/en-us/nuget/tools/package-manager-ui) hinzu.

* `Microsoft.Bot.Builder.AI.QnA`

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)

Die QnA Maker-Features sind im `botbuilder-ai`-Paket enthalten. Sie können Ihrem Projekt dieses Paket über npm hinzufügen:

```shell
npm install --save botbuilder-ai
```

---

## <a name="using-cli-tools-to-update-your-bot-configuration"></a>Verwenden der CLI-Tools zum Aktualisieren Ihrer BOT-Konfiguration

Eine andere Methode zum Beschaffen der Werte für den Zugriff auf die Wissensdatenbank ist die Verwendung der Bot Builder CLI-Tools [qnamaker](https://aka.ms/botbuilder-tools-qnaMaker) und [msbot](https://aka.ms/botbuilder-tools-msbot-readme), um Metadaten zu Ihrer Wissensdatenbank abzurufen und Ihrer BOT-Datei hinzuzufügen.

1. Öffnen Sie ein Terminal oder eine Eingabeaufforderung, und navigieren Sie zum Stammverzeichnis für Ihr Botprojekt.
2. Führen Sie `qnamaker init` aus, um eine QnA Maker-Ressourcendatei (**.qnamakerrc**) zu erstellen. Sie werden zum Eingeben eines QnA Maker-Abonnementschlüssels aufgefordert.
3. Führen Sie den folgenden Befehl aus, um Ihre Metadaten herunterzuladen und der Konfigurationsdatei Ihres Bots hinzuzufügen.

    ```shell
    qnamaker get kb --kbId <your-kb-id> --msbot | msbot connect qna --stdin [--secret <your-secret>]
    ```
Wenn Sie Ihre Konfigurationsdatei verschlüsselt haben, müssen Sie Ihren geheimen Schlüssel angeben, um die Datei zu aktualisieren.

## <a name="using-qna-maker"></a>Verwenden von QnA Maker
Ein Verweis auf QnA Maker wird zuerst beim Initialisieren des Bots hinzugefügt. Dies ermöglicht das Aufrufen in unserer Botlogik.

# <a name="ctabcs"></a>[C#](#tab/cs)
Öffnen Sie das QnA Maker-Beispiel, das Sie zuvor heruntergeladen haben. Dieser Code wird von uns je nach Bedarf geändert.
Fügen Sie `BotConfiguration.bot` zuerst die Informationen hinzu, die zum Zugreifen auf Ihre Wissensdatenbank erforderlich sind, z.B. Hostname, Endpunktschlüssel und Wissensdatenbank-ID (KbId). Dies sind die Werte, die Sie aus den **Einstellungen** Ihrer Wissensdatenbank in QnA Maker gespeichert haben.

```json
{
  "name": "QnABotSample",
  "services": [
    {
      "type": "endpoint",
      "name": "development",
      "endpoint": "http://localhost:3978/api/messages",
      "appId": "",
      "id": "1",
      "appPassword": ""
    },
    {
      "type": "qna",
      "name": "QnABot",
      "KbId": "<YOUR_KNOWLEDGE_BASE_ID>",
      "Hostname": "https://<YOUR_HOSTNAME>.azurewebsites.net/qnamaker",
      "EndpointKey": "<YOUR_ENDPOINT_KEY>"
    }
  ],
  "version": "2.0",
  "padlock": ""
}
```

Als Nächstes erstellen wir eine QnA Maker-Instanz in `Startup.cs`. Hierbei werden die oben erwähnten Informationen aus der Datei `BotConfiguration.bot` abgerufen. Diese Zeichenfolgen können für Testzwecke auch hartcodiert werden.

```csharp
private static BotServices InitBotServices(BotConfiguration config)
{
    var qnaServices = new Dictionary<string, QnAMaker>();
    foreach (var service in config.Services)
    {
        switch (service.Type)
        {
            case ServiceTypes.QnA:
            {
                // Create a QnA Maker that is initialized and suitable for passing
                // into the IBot-derived class (QnABot).
                var qna = (QnAMakerService)service;
                if (qna == null)
                {
                    throw new InvalidOperationException("The QnA service is not configured correctly in your '.bot' file.");
                }

                if (string.IsNullOrWhiteSpace(qna.KbId))
                {
                    throw new InvalidOperationException("The QnA KnowledgeBaseId ('kbId') is required to run this sample. Please update your '.bot' file.");
                }

                if (string.IsNullOrWhiteSpace(qna.EndpointKey))
                {
                    throw new InvalidOperationException("The QnA EndpointKey ('endpointKey') is required to run this sample. Please update your '.bot' file.");
                }

                if (string.IsNullOrWhiteSpace(qna.Hostname))
                {
                    throw new InvalidOperationException("The QnA Host ('hostname') is required to run this sample. Please update your '.bot' file.");
                }

                var qnaEndpoint = new QnAMakerEndpoint()
                {
                    KnowledgeBaseId = qna.KbId,
                    EndpointKey = qna.EndpointKey,
                    Host = qna.Hostname,
                };

                var qnaMaker = new QnAMaker(qnaEndpoint);
                qnaServices.Add(qna.Name, qnaMaker);
                break;
            }
        }
    }
    var connectedServices = new BotServices(qnaServices);
    return connectedServices;
}
```

Anschließend müssen wir diese QnAMaker-Instanz für Ihren Bot bereitstellen. Öffnen Sie `QnABot.cs`, und fügen Sie am Anfang der Datei den folgenden Code hinzu. Wenn Sie auf Ihre eigene Wissensdatenbank zugreifen, sollten Sie die unten dargestellte Begrüßungsnachricht (_welcome_) ändern, damit Ihre Benutzer nützliche erste Informationen erhalten.

```csharp
public class QnABot : IBot
{
    public static readonly string QnAMakerKey = "QnABot";
    private const string WelcomeText = @"This bot will introduce you to QnA Maker.
                                         Ask a quesiton to get started.";
    private readonly BotServices _services;
    public QnABot(BotServices services)
    {
        _services = services ?? throw new System.ArgumentNullException(nameof(services));
        Console.WriteLine($"{_services}");
        if (!_services.QnAServices.ContainsKey(QnAMakerKey))
        {
            throw new System.ArgumentException($"Invalid configuration. Please check your '.bot' file for a QnA service named '{QnAMakerKey}'.");
        }
    }
}
```

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)
Öffnen Sie das QnA Maker-Beispiel, das Sie zuvor heruntergeladen haben. Dieser Code wird von uns je nach Bedarf geändert.
In unserem Beispiel befindet sich der Startcode in der Datei **index.js** und der Code für die Botlogik in der Datei **bot.js**. Zusätzliche Konfigurationsinformationen sind in der Datei **qnamaker.bot** enthalten.

Nachdem Sie die Anleitungen zum Erstellen Ihrer Wissensdatenbank und zum Aktualisieren Ihrer **BOT**-Datei befolgt haben, sollte die Datei **qnamaker.bot** einen Diensteintrag für Ihre QnA Maker-Wissensdatenbank enthalten.

```json
{
    "name": "qnamaker",
    "description": "",
    "services": [
        {
            "type": "endpoint",
            "name": "development",
            "id": "1",
            "appId": "",
            "appPassword": "",
            "endpoint": "http://localhost:3978/api/messages"
        },
        {
            "type": "qna",
            "name": "<YOUR_KB_NAME>",
            "kbId": "<YOUR_KNOWLEDGE_BASE_ID>",
            "endpointKey": "<YOUR_ENDPOINT_KEY>",
            "hostname": "https://<YOUR_HOSTNAME>.azurewebsites.net/qnamaker",
            "id": "221"
        }
    ],
    "padlock": "",
    "version": "2.0"
}
```

In der Datei **index.js** lesen wir die Konfigurationsinformationen ein, um den QnA Maker-Dienst zu generieren und den Bot zu initialisieren.

Aktualisieren Sie den Wert von `QNA_CONFIGURATION` auf den Namen Ihrer Wissensdatenbank, der in Ihrer Konfigurationsdatei angezeigt wird.

```js
// QnA Maker knowledge base name as specified in .bot file.
const QNA_CONFIGURATION = '<YOUR_KB_NAME>';

// Get endpoint and QnA Maker configurations by service name.
const endpointConfig = botConfig.findServiceByNameOrId(BOT_CONFIGURATION);
const qnaConfig = botConfig.findServiceByNameOrId(QNA_CONFIGURATION);

// Map the contents to the required format for `QnAMaker`.
const qnaEndpointSettings = {
    knowledgeBaseId: qnaConfig.kbId,
    endpointKey: qnaConfig.endpointKey,
    host: qnaConfig.hostname
};

// Create adapter...

// Create the QnAMakerBot.
let bot;
try {
    bot = new QnAMakerBot(qnaEndpointSettings, {});
} catch (err) {
    console.error(`[botInitializationError]: ${ err }`);
    process.exit();
}
```

Anschließend erstellen wir den HTTP-Server und lauschen auf eingehende Anforderungen, mit denen Aufrufe unserer Botlogik generiert werden.

```js
// Create HTTP server.
let server = restify.createServer();
server.listen(process.env.port || process.env.PORT || 3978, function() {
    console.log(`\n${ server.name } listening to ${ server.url }.`);
    console.log(`\nGet Bot Framework Emulator: https://aka.ms/botframework-emulator.`);
    console.log(`\nTo talk to your bot, open qnamaker.bot file in the emulator.`);
});

// Listen for incoming requests.
server.post('/api/messages', (req, res) => {
    adapter.processActivity(req, res, async (turnContext) => {
        await bot.onTurn(turnContext);
    });
});
```

---

## <a name="calling-qna-maker-from-your-bot"></a>Aufrufen von QnA Maker aus Ihrem Bot

# <a name="ctabcs"></a>[C#](#tab/cs)

Wenn Ihr Bot eine Antwort von QnAMaker benötigt, können Sie `GetAnswersAsync()` über Ihren Botcode aufrufen, um je nach aktuellem Kontext die richtige Antwort zu erhalten. Wenn Sie auf Ihre eigene Wissensdatenbank zugreifen, sollten Sie die unten dargestellte Meldung _no answers_ (Keine Antworten) ändern, damit Ihre Benutzer nützliche Informationen erhalten.

```csharp
// Check QnA Maker model
var response = await _services.QnAServices[QnAMakerKey].GetAnswersAsync(turnContext);
if (response != null && response.Length > 0)
{
    await turnContext.SendActivityAsync(response[0].Answer, cancellationToken: cancellationToken);
}
else
{
    var msg = @"No QnA Maker answers were found. This example uses a QnA Maker Knowledge Base that focuses on smart light bulbs.
                To see QnA Maker in action, ask the bot questions like 'Why won't it turn on?' or 'I need help'.";
    await turnContext.SendActivityAsync(msg, cancellationToken: cancellationToken);
}

    /// ...
```

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)

In der Datei **bot.js** übergeben wir die Eingabe des Benutzers an die `generateAnswer`-Methode des QnA Maker-Diensts, um Antworten von der Wissensdatenbank zu erhalten. Wenn Sie auf Ihre eigene Wissensdatenbank zugreifen, sollten Sie die unten dargestellten Meldungen _no answers_ und _welcome_ ändern, damit Ihre Benutzer nützliche Informationen erhalten.

```javascript
const { ActivityTypes, TurnContext } = require('botbuilder');
const { QnAMaker, QnAMakerEndpoint, QnAMakerOptions } = require('botbuilder-ai');

/**
 * A simple bot that responds to utterances with answers from QnA Maker.
 * If an answer is not found for an utterance, the bot responds with help.
 */
class QnAMakerBot {
    /**
     * The QnAMakerBot constructor requires one argument (`endpoint`) which is used to create an instance of `QnAMaker`.
     * @param {QnAMakerEndpoint} endpoint The basic configuration needed to call QnA Maker. In this sample the configuration is retrieved from the .bot file.
     * @param {QnAMakerOptions} config An optional parameter that contains additional settings for configuring a `QnAMaker` when calling the service.
     */
    constructor(endpoint, qnaOptions) {
        this.qnaMaker = new QnAMaker(endpoint, qnaOptions);
    }

    /**
     * Every conversation turn for our QnAMakerBot will call this method.
     * @param {TurnContext} turnContext Contains all the data needed for processing the conversation turn.
     */
    async onTurn(turnContext) {
        // By checking the incoming Activity type, the bot only calls QnA Maker in appropriate cases.
        if (turnContext.activity.type === ActivityTypes.Message) {
            // Perform a call to the QnA Maker service to retrieve matching Question and Answer pairs.
            const qnaResults = await this.qnaMaker.generateAnswer(turnContext.activity.text);

            // If an answer was received from QnA Maker, send the answer back to the user.
            if (qnaResults[0]) {
                await turnContext.sendActivity(qnaResults[0].answer);

            // If no answers were returned from QnA Maker, reply with help.
            } else {
                await turnContext.sendActivity('No QnA Maker answers were found. This example uses a QnA Maker Knowledge Base that focuses on smart light bulbs. To see QnA Maker in action, ask the bot questions like "Why won\'t it turn on?" or "I need help."');
            }

        // If the Activity is a ConversationUpdate, send a greeting message to the user.
        } else if (turnContext.activity.type === ActivityTypes.ConversationUpdate &&
                   turnContext.activity.recipient.id !== turnContext.activity.membersAdded[0].id) {
            await turnContext.sendActivity('Welcome to the QnA Maker sample! Ask me a question and I will try to answer it.');

        // Respond to all other Activity types.
        } else if (turnContext.activity.type !== ActivityTypes.ConversationUpdate) {
            await turnContext.sendActivity(`[${ turnContext.activity.type }]-type activity detected.`);
        }
    }
}

module.exports.QnAMakerBot = QnAMakerBot;
```

---

Stellen Sie Ihrem Bot Fragen, um die Antworten von Ihrem QnA Maker-Dienst anzuzeigen. Weitere Informationen zum Testen und Veröffentlichen Ihres QnA-Diensts finden Sie im QnA Maker-Artikel [Testen Ihrer Wissensdatenbank](https://docs.microsoft.com/en-us/azure/cognitive-services/qnamaker/how-to/test-knowledge-base).

## <a name="next-steps"></a>Nächste Schritte

QnA Maker kann mit anderen Cognitive Services kombiniert werden, um Ihren Bot noch leistungsfähiger zu machen. Das Dispatch-Tool bietet eine Methode zum Kombinieren von QnA und Language Understanding (LUIS) in Ihrem Bot.

> [!div class="nextstepaction"]
> [Kombinieren von LUIS- und QnA-Diensten mit dem Dispatch-Tool](./bot-builder-tutorial-dispatch.md)
