---
title: 'Azure Bot Service-Tutorial: Beantworten von Fragen per Bot | Microsoft-Dokumentation'
description: In diesem Tutorial wird beschrieben, wie Sie QnA Maker in Ihrem Bot zum Beantworten von Fragen verwenden.
keywords: QnA Maker, Frage und Antwort, Wissensdatenbank
author: JonathanFingold
ms.author: v-jofing
manager: kamrani
ms.topic: tutorial
ms.service: bot-service
ms.subservice: sdk
ms.date: 01/15/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: b1c34531ee60b2ce9037f42e4f5a7093501cf83a
ms.sourcegitcommit: bdb981c0b11ee99d128e30ae0462705b2dae8572
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/17/2019
ms.locfileid: "54360957"
---
# <a name="tutorial-use-qna-maker-in-your-bot-to-answer-questions"></a>Tutorial: Verwenden von QnA Maker in Ihrem Bot zum Beantworten von Fragen

Sie können den QnA Maker-Dienst und eine Wissensdatenbank verwenden, um für Ihren Bot die Unterstützung von Fragen und Antworten hinzuzufügen. Beim Erstellen Ihrer Wissensdatenbank fügen Sie auch Fragen und Antworten ein (Seeding).

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen eines QnA Maker-Diensts und einer Wissensdatenbank
> * Hinzufügen von Wissensdatenbank-Informationen zu Ihrer BOT-Datei
> * Aktualisieren Ihres Bots zum Abfragen der Wissensdatenbank
> * Erneutes Veröffentlichen Ihres Bots

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

* Der im [vorherigen Tutorial](bot-builder-tutorial-basic-deploy.md) erstellte Bot. Wir fügen dem Bot ein Feature für Fragen und Antworten hinzu.
* Es ist hilfreich, wenn Sie mit QnA Maker bereits etwas vertraut sind. Wir nutzen das QnA Maker-Portal, um die Wissensdatenbank, die für den Bot verwendet wird, zu erstellen, zu trainieren und zu veröffentlichen.

Die Voraussetzungen für das vorherige Tutorial sollten Sie bereits kennen:

[!INCLUDE [deployment prerequisites snippet](~/includes/deploy/snippet-prerequisite.md)]

## <a name="sign-in-to-qna-maker-portal"></a>Anmelden am QnA Maker-Portal

<!-- This and the next step are close duplicates of what's in the QnA How-To -->

Melden Sie sich mit Ihren Azure-Anmeldeinformationen am [QnA Maker-Portal](https://qnamaker.ai/) an.

## <a name="create-a-qna-maker-service-and-knowledge-base"></a>Erstellen eines QnA Maker-Diensts und einer Wissensdatenbank

Wir importieren eine vorhandene Wissensdatenbank-Definition aus dem QnA Maker-Beispiel im Repository [Microsoft/BotBuilder-Samples](https://github.com/Microsoft/BotBuilder-Samples).

1. Klonen oder kopieren Sie das Repository mit den Beispielen auf Ihren Computer.
1. **Erstellen Sie eine Wissensdatenbank**, indem Sie das QnA Maker-Portal verwenden.
   1. Erstellen Sie einen QnA-Dienst, falls dies erforderlich ist. (Sie können einen vorhandenen QnA Maker-Dienst verwenden oder für dieses Tutorial einen neuen erstellen.) Eine ausführlichere QnA Maker-Anleitung finden Sie unter [Erstellen eines QnA Maker-Diensts](https://docs.microsoft.com/en-us/azure/cognitive-services/qnamaker/how-to/set-up-qnamaker-service-azure) und [Erstellen, Trainieren und Veröffentlichen der QnA Maker-Wissensdatenbank](https://docs.microsoft.com/en-us/azure/cognitive-services/qnamaker/quickstarts/create-publish-knowledge-base).
   1. Verbinden Sie Ihren QnA-Dienst mit Ihrer Wissensdatenbank.
   1. Geben Sie Ihrer Wissensdatenbank einen Namen.
   1. Verwenden Sie die Datei **BotBuilder-Samples\samples\csharp_dotnetcore\11.qnamaker\CognitiveModels\smartLightFAQ.tsv** aus dem Repository mit den Beispielen, um Ihre Wissensdatenbank mit Daten zu füllen.
   1. Klicken Sie auf **Wissensdatenbank erstellen**, um die Wissensdatenbank zu erstellen.
1. Führen Sie für Ihre Wissensdatenbank das **Speichern und Trainieren** durch.
1. **Veröffentlichen** Sie Ihre Wissensdatenbank.

   Die Wissensdatenbank kann jetzt für Ihren Bot verwendet werden. Notieren Sie sich die Wissensdatenbank-ID, den Endpunktschlüssel und den Hostnamen. Sie benötigen diese Angaben für den nächsten Schritt.

## <a name="add-knowledge-base-information-to-your-bot-file"></a>Hinzufügen von Wissensdatenbank-Informationen zu Ihrer BOT-Datei

Fügen Sie Ihrer BOT-Datei die Informationen hinzu, die für den Zugriff auf Ihre Wissensdatenbank erforderlich sind.

1. Öffnen Sie die BOT-Datei in einem Editor.
1. Fügen Sie dem Array `services` das Element `qna` hinzu.

    ```json
    {
        "type": "qna",
        "name": "<your-knowledge-base-name>",
        "kbId": "<your-knowledge-base-id>",
        "hostname": "<your-qna-service-hostname>",
        "endpointKey": "<your-knowledge-base-endpoint-key>",
        "subscriptionKey": "<your-azure-subscription-key>",
        "id": "<a-unique-id>"
    }
    ```

    | Feld | Wert |
    |:----|:----|
    | type | Muss `qna` lauten. Hiermit wird angegeben, dass dieser Diensteintrag eine QnA-Wissensdatenbank beschreibt. |
    | name | Der Name, den Sie Ihrer Wissensdatenbank zugewiesen haben. |
    | kbId | Die Wissensdatenbank-ID, die im QnA Maker-Portal für Sie generiert wurde. |
    | hostname | Die Host-URL, die im QnA Maker-Portal generiert wurde. Verwenden Sie die vollständige URL, die mit `https://` beginnt und auf `/qnamaker` endet. |
    | endpointKey | Der Endpunktschlüssel, der im QnA Maker-Portal für Sie generiert wurde. |
    | subscriptionKey | Die ID für das Abonnement, das Sie beim Erstellen des QnA Maker-Diensts in Azure verwendet haben. |
    | id | Eine eindeutige ID, die nicht bereits für einen der anderen Dienste in Ihrer BOT-Datei verwendet wird, z.B. „201“. |

1. Speichern Sie Ihre Änderungen.

## <a name="update-your-bot-to-query-the-knowledge-base"></a>Aktualisieren Ihres Bots zum Abfragen der Wissensdatenbank

Aktualisieren Sie den Initialisierungscode zum Laden der Dienstinformationen für Ihre Wissensdatenbank.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

1. Fügen Sie Ihrem Projekt das NuGet-Paket **Microsoft.Bot.Builder.AI.QnA** hinzu.
1. Benennen Sie Ihre Klasse, mit der **IBot** implementiert wird, in `QnaBot` um.
1. Benennen Sie die Klasse, die die Accessoren für Ihren Bot enthält, in `QnaBotAccessors` um.
1. Fügen Sie in der Datei **Startup.cs** die unten angegebenen Namespaceverweise hinzu.
    ```csharp
    using System.Collections.Generic;
    using System.Linq;
    using Microsoft.Bot.Builder.AI.QnA;
    using Microsoft.Bot.Builder.Integration;
    ```
1. Ändern Sie außerdem die **ConfigureServices**-Methode, um die in der **BOT**-Datei definierten Wissensdatenbanken zu initialisieren und zu registrieren. Beachten Sie, dass diese ersten Zeilen aus dem Text des Aufrufs `services.AddBot<QnaBot>(options =>` nach oben verschoben wurden.
    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        var secretKey = Configuration.GetSection("botFileSecret")?.Value;
        var botFilePath = Configuration.GetSection("botFilePath")?.Value;

        // Loads .bot configuration file and adds a singleton that your Bot can access through dependency injection.
        var botConfig = BotConfiguration.Load(botFilePath ?? @".\jfEchoBot.bot", secretKey);
        services.AddSingleton(sp => botConfig ?? throw new InvalidOperationException($"The .bot config file could not be loaded. ({botConfig})"));

        // Initialize the QnA knowledge bases for the bot.
        services.AddSingleton(sp => {
            var qnaServices = new List<QnAMaker>();
            foreach (var qnaService in botConfig.Services.OfType<QnAMakerService>())
            {
                qnaServices.Add(new QnAMaker(qnaService));
            }
            return qnaServices;
        });

        services.AddBot<QnaBot>(options =>
        {
            // Retrieve current endpoint.
            // ...
        });

        // Create and register state accessors.
        // ...
    }
    ```
1. Fügen Sie in der Datei **QnaBot.cs** die unten angegebenen Namespaceverweise hinzu.
    ```csharp
    using System.Collections.Generic;
    using Microsoft.Bot.Builder.AI.QnA;
    ```
1. Fügen Sie eine `_qnaServices`-Eigenschaft hinzu, und initialisieren Sie sie im Konstruktor des Bots.
    ```csharp
    private readonly List<QnAMaker> _qnaServices;

    /// ...
    public QnaBot(QnaBotAccessors accessors, List<QnAMaker> qnaServices, ILoggerFactory loggerFactory)
    {
        // ...
        _qnaServices = qnaServices;
    }
    ```
1. Ändern Sie den Turn-Handler, um die registrierten Wissensdatenbanken anhand der Eingabe des Benutzers abzufragen. Wenn Ihr Bot eine Antwort von QnAMaker benötigt, können Sie `GetAnswersAsync` über Ihren Botcode aufrufen, um je nach aktuellem Kontext die richtige Antwort zu erhalten. Wenn Sie auf Ihre eigene Wissensdatenbank zugreifen, sollten Sie die unten dargestellte Meldung _no answers_ (Keine Antworten) ändern, damit Ihre Benutzer nützliche Informationen erhalten.
    ```csharp
    public async Task OnTurnAsync(ITurnContext turnContext, CancellationToken cancellationToken = default(CancellationToken))
    {
        if (turnContext.Activity.Type == ActivityTypes.Message)
        {
            foreach(var qnaService in _qnaServices)
            {
                var response = await qnaService.GetAnswersAsync(turnContext);
                if (response != null && response.Length > 0)
                {
                    await turnContext.SendActivityAsync(
                        response[0].Answer,
                        cancellationToken: cancellationToken);
                    return;
                }
            }

            var msg = "No QnA Maker answers were found. This example uses a QnA Maker knowledge base that " +
                "focuses on smart light bulbs. Ask the bot questions like 'Why won't it turn on?' or 'I need help'.";

            await turnContext.SendActivityAsync(msg, cancellationToken: cancellationToken);
        }
        else
        {
            await turnContext.SendActivityAsync($"{turnContext.Activity.Type} event detected");
        }
    }
    ```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

1. Öffnen Sie ein Terminal oder eine Eingabeaufforderung mit dem Stammverzeichnis für Ihr Projekt.
1. Fügen Sie Ihrem Projekt das npm-Paket **botbuilder-ai** hinzu.
    ```shell
    npm i botbuilder-ai
    ```
1. Fügen Sie in der Datei **index.js** die unten angegebene require-Anweisung hinzu.
    ```javascript
    const { QnAMaker } = require('botbuilder-ai');
    ```
1. Lesen Sie die Konfigurationsinformationen ein, um die QnA Maker-Dienste zu generieren.
    ```javascript
    // Read bot configuration from .bot file.
    // ...

    // Initialize the QnA knowledge bases for the bot.
    // Assume each QnA entry in the .bot file is well defined.
    const qnaServices = [];
    botConfig.services.forEach(s => {
        if (s.type == 'qna') {
            const endpoint = {
                knowledgeBaseId: s.kbId,
                endpointKey: s.endpointKey,
                host: s.hostname
            };
            const options = {};
            qnaServices.push(new QnAMaker(endpoint, options));
        }
    });

    // Get bot endpoint configuration by service name
    // ...
    ```
1. Aktualisieren Sie die Botkonstruktion, damit die QnA-Dienste übergeben werden.
    ```javascript
    // Create the bot.
    const myBot = new MyBot(qnaServices);
    ```
1. Fügen Sie in der Datei **bot.js** einen Konstruktor hinzu.
    ```javascript
    constructor(qnaServices) {
        this.qnaServices = qnaServices;
    }
    ```
1. Aktualisieren Sie Ihren Turn-Handler außerdem so, dass Ihre Wissensdatenbank abgefragt wird, um eine Antwort zu erhalten.
    ```javascript
    async onTurn(turnContext) {
        if (turnContext.activity.type === ActivityTypes.Message) {
            for (let i = 0; i < this.qnaServices.length; i++) {
                // Perform a call to the QnA Maker service to retrieve matching Question and Answer pairs.
                const qnaResults = await this.qnaServices[i].getAnswers(turnContext);

                // If an answer was received from QnA Maker, send the answer back to the user and exit.
                if (qnaResults[0]) {
                    await turnContext.sendActivity(qnaResults[0].answer);
                    return;
                }
            }
            // If no answers were returned from QnA Maker, reply with help.
            await turnContext.sendActivity('No QnA Maker answers were found. '
                + 'This example uses a QnA Maker Knowledge Base that focuses on smart light bulbs. '
                + `Ask the bot questions like "Why won't it turn on?" or "I need help."`);
        } else {
            await turnContext.sendActivity(`[${ turnContext.activity.type } event detected]`);
        }
    }
    ```

---

### <a name="test-the-bot-locally"></a>Lokales Testen des Bots

Ihr Bot sollte nun dazu in der Lage sein, einige Fragen zu beantworten. Führen Sie den Bot lokal aus, und öffnen Sie ihn im Emulator.

![Testen des QnA-Beispiels](~/media/emulator-v4/qna-test-bot.png)

## <a name="re-publish-your-bot"></a>Erneutes Veröffentlichen Ihres Bots

Ihr Bot kann jetzt erneut veröffentlicht werden.

[!INCLUDE [publish snippet](~/includes/deploy/snippet-publish.md)]

### <a name="test-the-published-bot"></a>Testen des veröffentlichten Bots

Nachdem Sie den Bot veröffentlicht haben, sollten Sie ein oder zwei Minuten warten, damit Azure den Bot aktualisieren und starten kann.

1. Verwenden Sie den Emulator, um den Produktionsendpunkt für Ihren Bot zu testen, oder das Azure-Portal, um den Bot im Webchat zu testen.

   In beiden Fällen sollte das gleiche Verhalten wie beim lokalen Testen zu beobachten sein.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

<!-- In the first tutorial, we should tell them to use a new resource group, so that it is easy to clean up resources. We should also mention in this step in the first tutorial not to clean up resources if they are continuing with the sequence. -->

Falls Sie diese Anwendung nicht weiterverwenden möchten, sollten Sie die zugehörigen Ressourcen wie folgt löschen:

1. Öffnen Sie im Azure-Portal die Ressourcengruppe für Ihren Bot.
1. Klicken Sie auf **Ressourcengruppe löschen**, um die Gruppe und alle darin enthaltenen Ressourcen zu löschen.
1. Geben Sie im Bestätigungsbereich den Ressourcengruppennamen ein, und klicken Sie auf **Löschen**.

## <a name="next-steps"></a>Nächste Schritte

Informationen zum Hinzufügen von Features zu Ihrem Bot finden Sie in den Artikeln, die im Abschnitt zur Vorgehensweise bei der Entwicklung angegeben sind.
> [!div class="nextstepaction"]
> [Schaltfläche „Nächste Schritte“](bot-builder-howto-send-messages.md)
