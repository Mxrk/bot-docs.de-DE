---
title: Verwenden von QnA Maker | Microsoft Docs
description: Erfahren Sie, wie Sie QnA Maker in Ihrem Bot verwenden können.
keywords: Fragen und Antworten, QnA, häufig gestellte Fragen, FAQ, Middleware
author: ivorb
ms.author: v-ivorb
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 03/13/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 78bc2c849a2c1900da33c7419693a7ff84c43cb0
ms.sourcegitcommit: dcbc8ad992a3e242a11ebcdf0ee99714d919a877
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/30/2018
ms.locfileid: "39352949"
---
# <a name="how-to-use-qna-maker"></a>Verwenden von QnA Maker

Um Ihrem Bot Unterstützung für einfache Fragen und Antworten hinzuzufügen, können Sie den [QnA Maker](https://qnamaker.ai/)-Dienst verwenden.

Eine der Grundvoraussetzungen beim Schreiben Ihres eigenen QnA Maker-Diensts ist es, ihn mit Fragen und Antworten zu versehen. In vielen Fällen sind die Fragen und Antworten bereits in Inhalten wie FAQs oder anderer Dokumentation enthalten. In anderen Fällen möchten Sie Ihre Antworten auf Fragen auf eine natürlichere, verständlichere Art und Weise anpassen. 

## <a name="create-a-qna-maker-service"></a>Erstellen eines QnA Maker-Diensts
Zunächst erstellen Sie ein Konto und melden Sie unter [QnA Maker](https://qnamaker.ai/) an. Navigieren Sie dann zu **Erstellen einer Wissensdatenbank**. Klicken Sie auf **QnA-Dienst erstellen**, und befolgen Sie die Anweisungen zum Erstellen eines Azure QnA-Diensts.

![Abbildung 1 zu QnA](media/QnA_1.png)

Sie werden an [QnA Maker erstellen](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) weitergeleitet. Füllen Sie das Formular aus, und klicken Sie auf **Erstellen**.

![Abbildung 2 zu QnA](media/QnA_2.png)
 
Nachdem Sie Ihren QnA-Dienst im Azure-Portal erstellt haben, werden unter der Überschrift „Ressourcenverwaltung“ Schlüssel angezeigt, die Sie ignorieren können. Fahren Sie mit Schritt 2 fort, um eine Verbindung mit Ihrem Azure QnA-Dienst herzustellen. Aktualisieren Sie die Seite, um den soeben erstellten Azure-Dienst auszuwählen, und geben Sie den Namen Ihrer Wissensdatenbank ein.

![Abbildung 3 zu QnA](media/QnA_3.png)

Klicken Sie auf **Wissensdatenbank erstellen**. Geben Sie Ihre eigenen Fragen und Antworten ein, oder kopieren Sie die folgenden Beispiele. 

![Abbildung 4 zu QnA](media/QnA_4.png)

Alternativ können Sie auch **Wissensdatenbank mit Daten auffüllen** auswählen und eine Datei oder URL angeben. Eine Beispielquelldatei zum Generieren eines einfachen QnA Maker-Diensts finden Sie [hier](https://aka.ms/qna-tsv).

Nach dem Hinzufügen neuer QnA-Paare oder dem Auffüllen Ihrer Wissensdatenbank mit Daten klicken Sie auf **Speichern und trainieren**. Sobald Sie fertig sind, klicken Sie auf der Registerkarte **VERÖFFENTLICHEN** auf **Veröffentlichen**.

Um den QnA-Dienst mit Ihrem Bot zu verbinden, benötigen Sie die HTTP-Anforderungszeichenfolge, die die Wissensdatenbank-ID und den QnA Maker-Abonnementschlüssel enthält. Kopieren Sie die HTTP-Beispielanforderung aus dem Veröffentlichungsergebnis. 

![Abbildung 5 zu QnA](media/QnA_5.png)

## <a name="installing-packages"></a>Installieren von Paketen

Bevor wir zur Programmierung kommen, stellen Sie sicher, dass Sie über die Pakete verfügen, die für QnA Maker erforderlich sind.

# <a name="ctabcs"></a>[C#](#tab/cs)

[Fügen Sie einen Verweis](https://docs.microsoft.com/en-us/nuget/tools/package-manager-ui) auf die v4-Vorabversion der folgenden NuGet-Pakete hinzu:

* `Microsoft.Bot.Builder.Ai.QnA`

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)

Jeder dieser Dienste kann Ihrem Bot mit dem botbuilder-ai-Paket hinzugefügt werden. Sie können Ihrem Projekt dieses Paket über npm hinzufügen:

* `npm install --save botbuilder@preview`
* `npm install --save botbuilder-ai@preview`

---


## <a name="using-qna-maker"></a>Verwenden von QnA Maker

QnA Maker wird zuerst als Middleware hinzugefügt. Dann können wir die Ergebnisse in unserer Botlogik verwenden.

# <a name="ctabcs"></a>[C#](#tab/cs)

Aktualisieren Sie die `ConfigureServices`-Methode in Ihrer Datei `Startup.cs`, um ein `QnAMakerMiddleware`-Objekt hinzuzufügen. Sie können Ihren Bot so konfigurieren, dass er Ihre Wissensdatenbank auf jede von einem Benutzer empfangene Nachricht überprüft, indem Sie sie einfach zum Middlewarestapel Ihres Bots hinzufügen.


```csharp
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Bot.Builder.Ai.Qna;
using Microsoft.Bot.Builder.BotFramework;
using Microsoft.Bot.Builder.Core.Extensions;
using Microsoft.Bot.Builder.Integration.AspNet.Core;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Logging;
using System;

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton(_ => Configuration);
    services.AddBot<AiBot>(options =>
    {
        options.CredentialProvider = new ConfigurationCredentialProvider(Configuration);

        var endpoint = new QnAMakerEndpoint
        {
           KnowledgeBaseId = "YOUR-KB-ID",
           // Get the Host from the HTTP request example at https://www.qnamaker.ai
           // For GA services: https://<Service-Name>.azurewebsites.net/qnamaker
           // For Preview services: https://westus.api.cognitive.microsoft.com/qnamaker/v2.0           
           Host = "YOUR-HTTP-REQUEST-HOST",
           EndpointKey = "YOUR-QNA-MAKER-SUBSCRIPTION-KEY"
        };
        options.Middleware.Add(new QnAMakerMiddleware(endpoint));
    });
}
```



Bearbeiten Sie den Code in der Datei „EchoBot.cs“ so, dass `OnTurn` eine Fallbacknachricht sendet, falls die QnA Maker-Middleware keine Antwort auf die Frage des Benutzers gesendet hat:

```csharp
using System.Threading.Tasks;
using Microsoft.Bot;
using Microsoft.Bot.Builder;
using Microsoft.Bot.Schema;

namespace Bot_Builder_Echo_Bot_QnA
{
    public class EchoBot : IBot
    {    
        public async Task OnTurn(ITurnContext context)
        {
            // This bot is only handling Messages
            if (context.Activity.Type == ActivityTypes.Message)
            {             
                if (!context.Responded)
                {
                    // QnA didn't send the user an answer
                    await context.SendActivity("Sorry, I couldn't find a good match in the KB.");

                }
            }
        }
    }
}
```


Einen Beispielbot finden Sie im [QnA Maker-Beispiel](https://aka.ms/qna-cs-bot-sample).

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)

Zunächst müssen Sie die [QnAMaker](https://github.com/Microsoft/botbuilder-js/tree/master/doc/botbuilder-ai/classes/botbuilder_ai.qnamaker.md)-Klasse als erforderlich erklären/importieren:

```js
const { QnAMaker } = require('botbuilder-ai');
```

Erstellen Sie einen `QnAMaker`, indem Sie ihn mit einer Zeichenfolge basierend auf der HTTP-Anforderung für den QnA-Dienst initialisieren. Sie können eine Beispielanforderung für den Dienst aus dem [QnA Maker-Portal](https://qnamaker.ai) unter „Einstellungen“ > „Bereitstellungsdetails“ kopieren.


Das Format der Zeichenfolge variiert abhängig davon, ob Ihr QnA Maker-Dienst die allgemein verfügbare oder die Vorschauversion von QnA Maker verwendet. Kopieren Sie die HTTP-Beispielanforderung, und rufen Sie die Wissensdatenbank-ID, den Abonnementschlüssel und den Host ab, die verwendet werden sollen, wenn Sie den `QnAMaker` initialisieren.

<!--
**Preview**
```js
const qnaEndpointString = 
    // Replace xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx with your knowledge base ID
    "POST /knowledgebases/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/generateAnswer\r\n" + 
    "Host: https://westus.api.cognitive.microsoft.com/qnamaker/v2.0\r\n" +
    // Replace xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx with your QnAMaker subscription key
    "Ocp-Apim-Subscription-Key: xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx\r\n"
const qna = new QnAMaker(qnaEndpointString);
```

**GA**
```js
const qnaEndpointString = 
    // Replace xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx with your knowledge base ID
    "POST /knowledgebases/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/generateAnswer\r\n" + 
    // Replace <Service-Name> to match the Azure URL where your service is hosted
    "Host: https://<Service-Name>.azurewebsites.net/qnamaker\r\n" +
    // Replace xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx with your QnAMaker subscription key
    "Authorization: EndpointKey xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx\r\n"
const qna = new QnAMaker(qnaEndpointString);
```
-->
**Vorschau**
```js
const qna = new QnAMaker(
    {
        knowledgeBaseId: '<KNOWLEDGE-BASE-ID>',
        endpointKey: '<QNA-SUBSCRIPTION-KEY>',
        host: 'https://westus.api.cognitive.microsoft.com/qnamaker/v2.0'
    },
    {
        // set this to true to send answers from QnA Maker
        answerBeforeNext: true
    }
);
```
**ALLGEMEINE VERFÜGBARKEIT**
```js
const qna = new QnAMaker(
    {
        knowledgeBaseId: '<KNOWLEDGE-BASE-ID>',
        endpointKey: '<QNA-SUBSCRIPTION-KEY>',
        host: 'https://<Service-Name>.azurewebsites.net/qnamaker'
    },
    {
        answerBeforeNext: true
    }
);
```
Sie können Ihren Bot so konfigurieren, dass er QNA Maker automatisch aufruft, indem Sie ihn einfach zum Middlewarestapel Ihres Bots hinzufügen:

```js
// Add QnA Maker as middleware
adapter.use(qna);

// Listen for incoming requests 
server.post('/api/messages', (req, res) => {
    // Route received request to adapter for processing
    adapter.processActivity(req, res, async (context) => {
        // If `!context.responded`, that means an answer wasn't found for the user's utterance.
        // In this case, we send the user a fallback message.
        if (context.activity.type === 'message' && !context.responded) {
            await context.sendActivity('No QnA Maker answers were found.');
        } else if (context.activity.type !== 'message') {
            await context.sendActivity(`[${context.activity.type} event detected]`);
        }
    });
});
```

Die Initialisierung von `answerBeforeNext` mit `true` bei der Initialisierung von `QnAMaker` bedeutet, dass die QnA Maker-Middleware automatisch antwortet, wenn sie eine Antwort findet, bevor sie zur Hauptlogik Ihres Bots in `processActivity` gelangt. Wenn Sie stattdessen `answerBeforeNext` auf `false` festlegen, ruft der Bot QnA Maker nur auf, nachdem die gesamte Hauptlogik Ihres Bots in `processActivity` ausgeführt wurde, und nur dann, wenn Ihr Bot dem Benutzer nicht geantwortet hat. 

## <a name="calling-qna-maker-without-using-middleware"></a>Aufrufen von QnA Maker ohne Verwendung von Middleware

Im vorherigen Beispiel bedeutet die `adapter.use(qna);`-Anweisung, dass QnA als Middleware ausgeführt wird und somit auf jede Nachricht antwortet, die der Bot empfängt. Um mehr Kontrolle darüber zu erhalten, wie und wann QnA Maker aufgerufen wird, können Sie `qna.answer()` direkt aus der Logik Ihres Bots heraus aufrufen, anstatt dieses Element als Middleware zu installieren.

Entfernen Sie die `adapter.use(qna);`-Anweisung, und verwenden Sie den folgenden Code, um QnA Maker direkt aufzurufen.

```js
// Listen for incoming activity 
server.post('/api/messages', (req, res) => {
    // Route received activity to adapter for processing
    adapter.processActivity(req, res, async (context) => {
        if (context.activity.type === 'message') {
            var handled = await qna.answer(context)
                if (!handled) {
                    await context.sendActivity(`I'm sorry. I didn't understand.`);
                }
        }
    });
});
```

Eine weitere Möglichkeit zum Anpassen von QnA Maker ist `qna.generateAnswer()`. Diese Methode ermöglicht das Abrufen weiterer Details zu den Antworten von QnA Maker.


```js
// Listen for incoming activity 
server.post('/api/messages', (req, res) => {
    // Route received activity to adapter for processing
    adapter.processActivity(req, res, async (context) => {
        if (context.activity.type === 'message') {
            // Get all the answers QnA Maker finds
            var results = await qna.generateAnswer(context.activity.text);
                if (results && results.length > 0) {
                    await context.sendActivity(results[0].answer);
                } else {
                    await context.sendActivity(`I don't know.`);
                }
    
        }
    });
});
```
---

Stellen Sie Ihrem Bot Fragen, um die Antworten von Ihrem QnA Maker-Dienst anzuzeigen.

![Abbildung 6 zu QnA](media/QnA_6.png)



## <a name="next-steps"></a>Nächste Schritte

QnA Maker kann mit anderen Cognitive Services kombiniert werden, um Ihren Bot noch leistungsfähiger zu machen. Das Dispatch-Tool bietet eine Methode zum Kombinieren von QnA und Language Understanding (LUIS) in Ihrem Bot.

> [!div class="nextstepaction"]
> [Kombinieren von LUIS-Apps und QnA-Diensten mit dem Dispatch-Tool](./bot-builder-tutorial-dispatch.md)
