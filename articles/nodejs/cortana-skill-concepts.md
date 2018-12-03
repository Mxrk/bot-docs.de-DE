---
title: Erstellen einer Cortana-Funktion mit Node.js | Microsoft-Dokumentation
description: Lernen Sie die grundlegenden Konzepte des Erstellens einer Cortana-Funktion im Bot Builder SDK für Node.js kennen.
keywords: Bot-Framework, Cortana-Funktion, Sprache, Node.js, Bot Builder, SDK, Schlüsselkonzepte, Kernkonzepte
author: DeniseMak
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 99254c5f2de8b524212d8f6a268dbc37c128ab5e
ms.sourcegitcommit: b78fe3d8dd604c4f7233740658a229e85b8535dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2018
ms.locfileid: "49996473"
---
# <a name="key-concepts-for-building-a-bot-for-cortana-skills-using-nodejs"></a>Schlüsselkonzepte zum Erstellen eines Bots für Cortana-Funktionen mit Node.js
 
[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

> [!NOTE]
> Dieser Artikel enthält vorläufigen Inhalt und wird aktualisiert.

In diesem Artikel werden die Schlüsselkonzepte des Erstellens einer Cortana-Funktion im Bot Builder SDK für Node.js vorgestellt. 

## <a name="what-is-a-cortana-skill"></a>Was ist eine Cortana-Funktion?
Eine Cortana-Funktion ist ein Bot, den Sie mit einem Cortana-Client wie dem in Windows 10 integrierten aufrufen können. Der Benutzer startet den Bot durch Sprechen einiger Schlüsselwörter oder Ausdrücke, die mit dem Bot verknüpft sind. Konfigurieren Sie im Bot-Frameworkportal, welche Schlüsselwörter zum Starten Ihres Bots verwendet werden. 

Cortana kann als sprachaktivierter Kanal betrachtet werden, der neben Textkonversation auch Sprachnachrichten senden und empfangen kann. Ein Bot, der als Cortana-Funktion veröffentlicht wird, sollte sowohl für Sprache als auch Text entworfen werden. Das Bot-Framework stellt Methoden zur Angabe der Markupsprache für Sprachsynthese (Speech Synthesis Markup Language, SSML) zum Definieren gesprochener Nachrichten bereit, die Ihr Bot sendet.

## <a name="acknowledge-user-utterances"></a>Bestätigen von Benutzeräußerungen 

<!-- Establishing conversational understanding -->
<!-- Placeholder: In this section, describe how you have to write your speech to sound natural -->


Wenn Sie einen sprachaktivierten Bot erstellen, sollten Sie versuchen, eine gemeinsame Basis und gegenseitiges Verständnis in der Konversation zu gewährleisten. Der Bot sollte eine gemeinsame Basis mit den Benutzeräußerungen schaffen, indem er signalisiert, dass der Benutzer gehört und verstanden wurde.

Benutzer sind verwirrt, wenn ein System keine gemeinsame Basis mit ihren Äußerungen zeigt. Die folgende Konversation kann z.B. etwas verwirrend sein, wenn der Bot fragt: „What's next?“:

```
Agent: Did you want to review some more of your profile?

User: No.

Agent: What's next?
```

Wenn der Bot ein „Okay“ als Bestätigung hinzugefügt wird, wirkt dies auf den Benutzer freundlicher:

```
Agent: Did you want to review some more of your profile?

User: No.

Agent: **Okay**, what's next?
```


Grad der gemeinsamen Basis, von schwach bis stark:
1. Fortgesetzte Aufmerksamkeit
2. Nächster relevanter Beitrag
3. Bestätigung: Minimale Antwort oder Fortsetzung: „Ja“, „Aha“, „OK“, „toll“
4. Veranschaulichen: Signalisieren des Verständnisses durch Neuformuliergung, Vervollständigung.
5. Hervorheben: Wiederholen des Gesamten oder eines Teils.

#### <a name="acknowledgement-and-next-relevant-contribution"></a>Bestätigung und nächster relevanter Beitrag
Benutzer: ... Ich muss im Mai verreisen.
Agent: **Und**, an welchem Tag im Mai möchten Sie reisen?
Benutzer: OK, ich muss vom 12. bis zum 15. dort sein.
Agent: **Und**, welche Stadt ist Ihr Flugziel?

#### <a name="grounding-by-demonstration"></a>Schaffen der gemeinsamen Basis durch Veranschaulichen
Benutzer: ... Ich muss im Mai verreisen.
Agent: Und, **an welchem Tag** im Mai möchten Sie reisen?
Benutzer: OK, ich muss vom 12. bis zum 15. dort sein.
Agent: **Und**, welche Stadt ist Ihr Flugziel?


### <a name="closure"></a>Abschluss

Der Bot, der eine Aktion ausführt, sollte einen Beweis der erfolgreichen Leistung liefern.
Es ist auch wichtig, Fehler oder Verständnis anzuzeigen. 
* Nonverbaler Abschluss: Wenn Sie die Schaltfläche eines Aufzugs drücken, leuchtet sie auf.
Prozess in zwei Schritten:
* Präsentation 
* Zustimmung


### <a name="differences-in-content-presentation"></a>Unterschiede in der Inhaltsdarstellung
Wenn Sie Ihren sprachaktivierten Bot entwerfen, beachten Sie, dass der gesprochene Dialog häufig nicht mit der Textnachricht identisch ist, die Ihr Bot sendet.
<!-- If there are differences in what the bot will say, in the text vs the speak fields of a prompt or in a waterfall, for example, discuss them here.

## Speech

You bot uses the **session.say** method to speak to the user. The speak method has three overloads:
* If you pass only one parameter to **session.say**, it can be a text parameter.
* If you pass two parameters to **session.say**, it can take text and SSML.
* If you pass three parameters, the third parameter takes an options structure that specifies all the options you can pass to build an **IMessage** object.

```javascript
var bot = new builder.UniversalBot(connector, function (session) {
    session.say("Hello... I'm a decision making bot.'.", 
        ssml.speak("Hello. I can help you answer all of life's tough questions."));
    session.replaceDialog('rootMenu');
});

```
## Speech in messages

The **IMessage** object provides a **speak** property for SSML. It can be used to play a .wav file.

The **inputHint** property helps indicate to Cortana whether your bot is expecting input. If you're using a built-in prompt, this value is automatically set to the default of **expectingInput**.

The **inputHint** property can take the following values: 
* **expectingInput**: Indicates that the bot is actively expecting a response from the user. Cortana listens for the user to speak into the microphone.
* **acceptingInput**: Indicates that the bot is passively ready for input but is not waiting on a response. Cortana accepts input from the user if the user holds down the microphone button.
* **ignoringInput**: Cortana is ignoring input. Your bot may send this hint if it is actively processing a request and will ignore input from users until the request is complete.

Prompts can take a `speak:` or `retrySpeak` option.

```javascript
        builder.Prompts.choice(session, "Decision Options", choices, {
            listStyle: builder.ListStyle.button,
            speak: ssml.speak("How would you like me to decide?")
        });
```

Prompts.number has *ordinal support*, meaning that you can say "the last", "the first", "the next-to-last" to choose an item in a list.




## Using synonyms

<!-- Axl Rose example -->     
```javascript   
         var choices = [
            { 
                value: 'flipCoinDialog',
                action: { title: "Flip A Coin" },
                synonyms: 'toss coin|flip quarter|toss quarter'
            },
            {
                value: 'rollDiceDialog',
                action: { title: "Roll Dice" },
                synonyms: 'roll die|shoot dice|shoot die'
            },
            {
                value: 'magicBallDialog',
                action: { title: "Magic 8-Ball" },
                synonyms: 'shake ball'
            },
            {
                value: 'quit',
                action: { title: "Quit" },
                synonyms: 'exit|stop|end'
            }
        ];
        builder.Prompts.choice(session, "Decision Options", choices, {
            listStyle: builder.ListStyle.button,
            speak: ssml.speak("How would you like me to decide?")
        });
```


## <a name="configuring-your-bot"></a>Konfigurieren Ihres Bots

## <a name="prompts"></a>Eingabeaufforderungen


## <a name="additional-resources"></a>Zusätzliche Ressourcen

[CortanaGetstarted]: /cortana/getstarted
[SSMLRef]: https://msdn.microsoft.com/en-us/library/hh378377(v=office.14).aspx
