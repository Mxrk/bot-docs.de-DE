---
title: Verwenden von QnA Maker zum Beantworten von Fragen | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie QnA Maker in Ihrem Bot verwenden können.
keywords: Fragen und Antworten, QnA, häufig gestellte Fragen, FAQ, QnA Maker
author: ivorb
ms.author: v-ivorb
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: cognitive-services
ms.date: 11/19/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 0708244b9f9e4859ba069ed463cef83a0ecdf20d
ms.sourcegitcommit: b9482670285295a2af0dfbb8f4b7e543c1c10542
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/13/2018
ms.locfileid: "53327156"
---
# <a name="use-qna-maker-to-answer-questions"></a>Verwenden von QnA Maker zum Beantworten von Fragen

[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

Sie können den QnA Maker-Dienst verwenden, um Ihrem Bot Unterstützung für Fragen und Antworten hinzuzufügen. Eine der Grundvoraussetzungen beim Erstellen Ihres eigenen QnA Maker-Diensts ist es, ihn mit Fragen und Antworten zu versehen. In vielen Fällen sind die Fragen und Antworten bereits in Inhalten wie FAQs oder anderer Dokumentation enthalten. In anderen Fällen möchten Sie Ihre Antworten auf Fragen auf eine natürlichere, verständlichere Art und Weise anpassen.

In diesem Thema erstellen wir eine Wissensdatenbank und nutzen sie in einem Bot.

## <a name="prerequisites"></a>Voraussetzungen
- [QnA Maker](https://www.qnamaker.ai/)-Konto
- Der Code in diesem Artikel basiert auf dem **QnA Maker**-Beispiel. Sie benötigen eine Kopie des Beispiels im [C#](https://aka.ms/cs-qna)- oder [JS](https://aka.ms/js-qna-sample)-Format.
- [Bot Framework-Emulator](https://github.com/Microsoft/BotFramework-Emulator/blob/master/README.md#download)
- Sie müssen mit den [Bot-Grundlagen](bot-builder-basics.md), [QnA Maker](https://docs.microsoft.com/en-us/azure/cognitive-services/qnamaker/overview/overview) und der [BOT](bot-file-basics.md)-Datei vertraut sein.

## <a name="create-a-qna-maker-service-and-publish-a-knowledge-base"></a>Erstellen eines QnA Maker-Diensts und Veröffentlichen einer Wissensdatenbank
1. Zuerst müssen Sie einen [QnA Maker-Dienst](https://docs.microsoft.com/en-us/azure/cognitive-services/qnamaker/how-to/set-up-qnamaker-service-azure) erstellen.
1. Als Nächstes erstellen Sie eine Wissensdatenbank mit der Datei `smartLightFAQ.tsv`, die im Ordner „CognitiveModels“ des Projekts enthalten ist. Die Schritte zum Erstellen, Trainieren und Veröffentlichen Ihrer QnA Maker-[Wissensdatenbank](https://docs.microsoft.com/en-us/azure/cognitive-services/qnamaker/quickstarts/create-publish-knowledge-base) sind in der QnA Maker-Dokumentation aufgeführt. Geben Sie der Wissensdatenbank beim Ausführen dieser Schritte den Namen `qna`, und verwenden Sie die Datei `smartLightFAQ.tsv`, um die Wissensdatenbank mit Daten zu füllen.

## <a name="obtain-values-to-connect-to-your-connect-your-bot-to-the-knowledge-base"></a>Beschaffen der Werte, mit denen Sie Ihren Bot mit der Wissensdatenbank verbinden können
1. Wählen Sie auf der [QnA Maker](https://www.qnamaker.ai/)-Website Ihre Wissensdatenbank aus.
1. Wählen Sie bei geöffneter Wissensdatenbank die Option **Einstellungen**. Notieren Sie sich den angezeigten Wert für _Dienstname_ als <Ihren_Wissensdatenbank-Namen>.
1. Scrollen Sie nach unten zu den **Bereitstellungsdetails**, und notieren Sie sich die folgenden Werte:
   - POST /knowledgebases/<Ihre_Wissensdatenbank-ID>/generateAnswer
   - Host: https://<Ihr_Hostname>.azurewebsites.net/qnamaker
   - Autorisierung: EndpointKey <Ihr_Endpunktschlüssel>

## <a name="update-the-bot-file"></a>Aktualisieren der Bot-Datei
Fügen Sie `qnamaker.bot` zuerst die Informationen hinzu, die zum Zugreifen auf Ihre Wissensdatenbank erforderlich sind, z.B. Hostname, Endpunktschlüssel und Wissensdatenbank-ID (KbId). Dies sind die Werte, die Sie aus den **Einstellungen** Ihrer Wissensdatenbank in QnA Maker gespeichert haben.

```json
{
  "name": "qnamaker",
  "services": [
    {
      "type": "endpoint",
      "name": "development",
      "endpoint": "http://localhost:3978/api/messages",
      "appId": "",
      "appPassword": ""
      "id": "25",
    
    },
    {
      "type": "qna",
      "name": "QnABot",
      "KbId": "<YOUR_KNOWLEDGE_BASE_ID>",
      "subscriptionKey": "<Your_Azure_Subscription_Key>", // Used when creating your QnA service.
      "endpointKey": "<Your_Recorded_Endpoint_Key>",
      "hostname": "<Your_Recorded_Hostname>",
      "id": "117"
    }
  ],
  "padlock": "",
   "version": "2.0"
}
```

# <a name="ctabcs"></a>[C#](#tab/cs)
Als Nächstes initialisieren wir eine neue Instanz der BotService-Klasse in „BotServices.cs“, mit der die obigen Informationen aus Ihrer BOT-Datei abgerufen werden. Der externe Dienst wird mit der BotConfiguration-Klasse konfiguriert.

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

In „QnABot.cs“ stellen wir für den Bot dann diese QnAMaker-Instanz bereit. Wenn Sie auf Ihre eigene Wissensdatenbank zugreifen, sollten Sie die unten dargestellte Begrüßungsnachricht (_welcome_) ändern, damit Ihre Benutzer nützliche erste Informationen erhalten.

```csharp
public class QnABot : IBot
{
    public static readonly string QnAMakerKey = "QnABot";
    private const string WelcomeText = @"This bot will introduce you to QnA Maker.
                                         Ask a question to get started.";
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

In unserem Beispiel befindet sich der Startcode in der Datei **index.js** und der Code für die Botlogik in der Datei **bot.js**. Zusätzliche Konfigurationsinformationen sind in der Datei **qnamaker.bot** enthalten.

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

## <a name="test-the-bot"></a>Testen des Bots

Führen Sie das Beispiel lokal auf Ihrem Computer aus. Wenn Sie eine Anleitung benötigen, helfen Ihnen die Infodateien zum [C#](https://github.com/Microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore/11.qnamaker)- bzw. [JS](https://github.com/Microsoft/BotBuilder-Samples/blob/master/samples/javascript_nodejs/11.qnamaker/README.md)-Beispiel weiter.

Senden Sie im Emulator wie unten dargestellt eine Nachricht an den Bot.

![Testen des QnA-Beispiels](~/media/emulator-v4/qna-test-bot.png)


## <a name="next-steps"></a>Nächste Schritte

QnA Maker kann mit anderen Cognitive Services kombiniert werden, um Ihren Bot noch leistungsfähiger zu machen. Das Dispatch-Tool bietet eine Methode zum Kombinieren von QnA und Language Understanding (LUIS) in Ihrem Bot.

> [!div class="nextstepaction"]
> [Kombinieren von LUIS- und QnA-Diensten mit dem Dispatch-Tool](./bot-builder-tutorial-dispatch.md)
