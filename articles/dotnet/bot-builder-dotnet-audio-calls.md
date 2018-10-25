---
title: Durchführen von Audioanrufen mit Skype | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie mithilfe des Bot Builder SDK für .NET Audioanrufe über Skype durchführen.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 0d0489c23cd24a7323ba0160d5e8e5e914be3011
ms.sourcegitcommit: b78fe3d8dd604c4f7233740658a229e85b8535dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2018
ms.locfileid: "49998849"
---
# <a name="conduct-audio-calls-with-skype"></a>Durchführen von Audioanrufen mit Skype

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

[!INCLUDE [Introduction to conducting audio calls](../includes/snippet-audio-call-intro.md)]

Die Architektur eines Bots, der Audioanrufe unterstützt, ist der eines Standardbots sehr ähnlich. In den folgenden Codebeispielen wird veranschaulicht, wie die Unterstützung von Audioanrufen über Skype mit dem Bot Builder SDK für .NET aktiviert wird. 

## <a name="enable-support-for-audio-calls"></a>Aktivieren der Unterstützung für Audioanrufe

Definieren Sie den `CallingController`, um einen Bot zum Unterstützen von Audioanrufen zu aktivieren.

```cs
[BotAuthentication]
[RoutePrefix("api/calling")]
public class CallingController : ApiController
{
    public CallingController() : base()
    {
        CallingConversation.RegisterCallingBot(callingBotService => new IVRBot(callingBotService));
    }

    [Route("callback")]
    public async Task<HttpResponseMessage> ProcessCallingEventAsync()
    {
        return await CallingConversation.SendAsync(this.Request, CallRequestType.CallingEvent);
    }

    [Route("call")]
    public async Task<HttpResponseMessage> ProcessIncomingCallAsync()
    {
        return await CallingConversation.SendAsync(this.Request, CallRequestType.IncomingCall);
    }
}
```

> [!NOTE]
> Neben dem `CallingController`, der Audioanrufe unterstützt, kann ein Bot auch einen `MessagesController` zum Unterstützen von Nachrichten enthalten. Durch die Bereitstellung beider Optionen ermöglichen Sie den Benutzern die Interaktion mit dem Bot auf die von ihnen bevorzugte Weise. <!-- docs on MessagesController are where? -->

##  <a name="answer-the-call"></a>Antwort beantworten

Die Aufgabe `ProcessIncomingCallAsync` wird ausgeführt, wenn ein Benutzer über Skype einen Anruf an diesen Bot initiiert.
Der Konstruktor registriert die `IVRBot`-Klasse, die über einen vordefinierten Handler für das `incomingCallEvent` verfügt.

Die erste Aktion im Workflow sollte bestimmen, ob der Bot den eingehenden Anruf annimmt oder ablehnt. Dieser Workflow weist den Bot an, den eingehenden Anruf anzunehmen und dann eine Begrüßungsnachricht wiederzugeben. 

```cs
private Task OnIncomingCallReceived(IncomingCallEvent incomingCallEvent)
{
    this.callStateMap[incomingCallEvent.IncomingCall.Id] = new CallState(incomingCallEvent.IncomingCall.Participants);

    incomingCallEvent.ResultingWorkflow.Actions = new List<ActionBase>
    {
        new Answer { OperationId = Guid.NewGuid().ToString() },
        GetPromptForText(WelcomeMessage)
    };

    return Task.FromResult(true);
}
```

## <a name="after-the-bot-answers"></a>Nach der Annahme des Bots

Wenn der Bot den Anruf annimmt, weisen im Workflow angegebene nachfolgende Aktionen die **Skype Bot Platform for Calling** an, eine Eingabeaufforderung wiederzugeben, Audio aufzuzeichnen, Sprache zu erkennen oder Ziffern einer Wähltastatur zu erfassen. Die letzte Aktion des Workflows könnte das Beenden des Anrufs sein. 

In diesem Codebeispiel wird ein Handler definiert, der nach Abschluss der Willkommensnachricht ein Menü einrichtet.

```cs
private Task OnPlayPromptCompleted(PlayPromptOutcomeEvent playPromptOutcomeEvent)
{
    var callState = this.callStateMap[playPromptOutcomeEvent.ConversationResult.Id];
    SetupInitialMenu(playPromptOutcomeEvent.ResultingWorkflow);
    return Task.FromResult(true);
}
```

Die `CreateIvrOptions`-Methode definiert das Menü, das dem Benutzer angezeigt wird.

```cs
private static Recognize CreateIvrOptions(string textToBeRead, int numberOfOptions, bool includeBack)
{
    if (numberOfOptions > 9)
    {
        throw new Exception("too many options specified");
    }

    var choices = new List<RecognitionOption>();

    for (int i = 1; i <= numberOfOptions; i++)
    {
        choices.Add(new RecognitionOption { Name = Convert.ToString(i), DtmfVariation = (char)('0' + i) });
    }

    if (includeBack)
    {
        choices.Add(new RecognitionOption { Name = "#", DtmfVariation = '#' });
    }

    var recognize = new Recognize
    {
        OperationId = Guid.NewGuid().ToString(),
        PlayPrompt = GetPromptForText(textToBeRead),
        BargeInAllowed = true,
        Choices = choices
    };

    return recognize;
}
```

Die `RecognitionOption`-Klasse definiert sowohl die gesprochene Antwort als auch die entsprechende Zweitonmehrfrequenz-Variation (Dual-Tone Multi-Frequency, DTMF). Mittels DTMF kann der Benutzer antworten, indem er die entsprechenden Ziffern über die Zehnertastatur eingibt, statt zu sprechen.

Die `OnRecognizeCompleted`-Methode verarbeitet die Auswahl des Benutzers, und der Eingabeparameter `recognizeOutcomeEvent` enthält den Wert der Benutzerauswahl.

```cs
private Task OnRecognizeCompleted(RecognizeOutcomeEvent recognizeOutcomeEvent)
{
    var callState = this.callStateMap[recognizeOutcomeEvent.ConversationResult.Id];
    ProcessMainMenuSelection(recognizeOutcomeEvent, callState);
    return Task.FromResult(true);
}
```

## <a name="support-natural-language"></a>Unterstützung von natürlicher Sprache
Der Bot kann auch für die Unterstützung von Antworten in natürlicher Sprache konzipiert werden. Die **Bing-Spracheingabe-API** ermöglicht dem Bot das Erkennen von Wörtern in der gesprochenen Antwort des Benutzers.

```cs
private async Task OnRecordCompleted(RecordOutcomeEvent recordOutcomeEvent)
{
    recordOutcomeEvent.ResultingWorkflow.Actions = new List<ActionBase>
    {
        GetPromptForText(EndingMessage),
        new Hangup { OperationId = Guid.NewGuid().ToString() }
    };

    // Convert the audio to text
    if (recordOutcomeEvent.RecordOutcome.Outcome == Outcome.Success)
    {
        var record = await recordOutcomeEvent.RecordedContent;
        string text = await this.GetTextFromAudioAsync(record);

        var callState = this.callStateMap[recordOutcomeEvent.ConversationResult.Id];

        await this.SendSTTResultToUser("We detected the following audio: " + text, callState.Participants);
    }

    recordOutcomeEvent.ResultingWorkflow.Links = null;
    this.callStateMap.Remove(recordOutcomeEvent.ConversationResult.Id);
}
```

## <a name="sample-code"></a>Beispielcode

Ein vollständiges Beispiel, das die Unterstützung von Audioanrufen über Skype mit dem Bot Builder SDK für .NET veranschaulicht, finden Sie in GitHub im <a href="https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/skype-CallingBot" target="_blank">Skype-CallingBot-Beispiel</a>.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- <a href="/dotnet/api/?view=botbuilder-3.11.0" target="_blank">Referenz zum Bot Builder SDK für .NET</a>
- <a href="https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/skype-CallingBot" target="_blank">Skype-CallingBot-Beispiel (GitHub)</a>
