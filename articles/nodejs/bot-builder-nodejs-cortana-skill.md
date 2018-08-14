---
title: Erstellen eines sprachaktivierten Bots mit Cortana-Fähigkeiten | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie mit dem Bot Builder SDK für Node.js einen sprachaktivierten Bot mit Cortana-Fähigkeiten erstellen.
author: DeniseMak
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: a5936f3a622bdc91084ba9a636d8be3b782f9a11
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39303877"
---
# <a name="build-a-speech-enabled-bot-with-cortana-skills"></a>Erstellen eines sprachaktivierten Bots mit Cortana-Fähigkeiten
> [!div class="op_single_selector"]
> - [.NET](../dotnet/bot-builder-dotnet-cortana-skill.md)
> - [Node.js](../nodejs/bot-builder-nodejs-cortana-skill.md)

Das Bot Builder SDK für Node.js ermöglicht Ihnen, einen sprachaktivierten Bot zu erstellen, indem Sie ihn als eine Cortana-Fähigkeit mit dem Cortana-Kanal verbinden. Mit Cortana-Fähigkeiten können Sie über Cortana auf eine Spracheingabe eines Benutzers reagieren und eine Funktion bereitstellen.

> [!TIP]
> Weitere Informationen zur Definition einer Fähigkeit und zu den Einsatzmöglichkeiten finden Sie im [Cortana Skills Kit][CortanaGetStarted].

Das Erstellen einer Cortana-Fähigkeit mit dem Bot Framework erfordert nur sehr wenig Cortana-spezifische Kenntnisse und besteht in erster Linie aus dem Erstellen eines Bots. Einer der wichtigsten Unterschiede zu anderen Bots, die Sie vielleicht schon erstellt haben, besteht darin, dass Cortana sowohl über visuelle als auch Audiokomponenten verfügt. Für die visuelle Komponente stellt Cortana einen Bereich des Zeichenbereichs zum Rendern von Inhalten (z. B. Karten) bereit. Für die Audiokomponente geben Sie Text oder SSML in Ihre Bot-Nachrichten ein, die Cortana dem Benutzer vorliest und so Ihrem Bot eine Stimme verleiht. 

> [!NOTE]
> Cortana ist auf vielen verschiedenen Geräten verfügbar. Einige haben einen Bildschirm, während das bei anderen (z. B. eigenständiger Lautsprecher) möglicherweise nicht der Fall ist. Sie sollten sicherstellen, dass Ihr Bot beide Szenarien verarbeiten kann. Das Überprüfen von Geräteinformationen ist unter [Cortana-spezifische Entitäten][CortanaSpecificEntities] beschrieben.

## <a name="adding-speech-to-your-bot"></a>Hinzufügen von Sprache zu Ihrem Bot

Gesprochene Nachrichten von Ihrem Bot werden als Speech Synthesis Markup Language (SSML) dargestellt. Mit dem Bot Builder SDK können Sie SSML in Ihre Bot-Antworten einbinden, um neben der Anzeige des Bots auch dessen Sprachausgabe zu steuern.

### <a name="sessionsay"></a>session.say

Ihr Bot verwendet anstelle der **session.send**-Methode die **session.say**-Methode, um mit dem Benutzer zu sprechen. Sie enthält optionale Parameter für das Senden von SSML-Ausgabe sowie Anlagen (z. B. Karten). 

Die Methode hat dieses Format:

```session.say(displayText: string, speechText: string, options?: object)```

| Parameter | BESCHREIBUNG |
|------|------|
| **displayText** | Eine Textnachricht, die auf der Cortana-Benutzeroberfläche angezeigt werden soll.|
| **speechText** | Text oder SSML, den bzw. die Cortana dem Benutzer vorliest. |
| **options** | Ein [IMessage][IMessage]-Objekt, das eine Anlage oder einen Eingabehinweis enthalten kann. Eingabehinweise geben an, ob der Bot Eingaben akzeptiert, erwartet oder ignoriert. Kartenanlagen werden im Cortana-Zeichenbereich unter der Information **DisplayText** angezeigt.   |

Mit der **inputHint**-Eigenschaft wird Cortana angegeben, ob Ihr Bot eine Eingabe erwartet. Wenn Sie eine integrierte Eingabeaufforderung verwenden, ist dieser Wert automatisch auf den Standardwert **expectingInput** festgelegt.


| Wert | BESCHREIBUNG |
|------|------|
| **acceptingInput** | Ihr Bot ist passiv bereit für eine Eingabe, wartet jedoch nicht auf eine Antwort. Cortana akzeptiert Eingaben vom Benutzer, wenn der Benutzer die Mikrofontaste gedrückt hält.|
| **expectingInput** | Gibt an, dass der Bot aktiv eine Antwort vom Benutzer erwartet. Cortana wartet auf eine Spracheingabe des Benutzers über das Mikrofon.  |
| **ignoringInput** | Cortana ignoriert die Eingabe. Ihr Bot kann diesen Hinweis senden, wenn er aktiv eine Anforderung verarbeitet und Eingaben von Benutzern ignoriert, bis die Anforderung abgeschlossen ist.  |


Das folgende Beispiel zeigt, wie Cortana Nur-Text oder SSML liest:

```javascript
// Have Cortana read plain text
session.say('This is the text that Cortana displays', 'This is the text that is spoken by Cortana.');

// Have Cortana read SSML
session.say('This is the text that Cortana displays', '<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">This is the text that is spoken by Cortana.</speak>');
```

Dieses Beispiel zeigt, wie Sie Cortana bekannt geben, dass eine Benutzereingabe erwartet wird. Das Mikrofon bleibt geöffnet.
```javascript
// Add an InputHint to let Cortana know to expect user input
session.say('Hi there', 'Hi, what’s your name?', {
    inputHint: builder.InputHint.expectingInput
});
```
<!-- TODO: tip about time limit and batching -->


### <a name="prompts"></a>Eingabeaufforderungen

Zusätzlich zur Verwendung der **session.say()**-Methode können Sie mit den Optionen **speak** und **retrySpeak** auch Text oder SSML an integrierte Eingabeaufforderungen übergeben.  

```javascript
builder.Prompts.text(session, 'text based prompt', {                                    
    speak: 'Cortana reads this out initially',                                               
    retrySpeak: 'This message is repeated by Cortana after waiting a while for user input',  
    inputHint: builder.InputHint.expectingInput                                              
});
```



<!-- TODO: Link to SSML library -->

Um dem Benutzer eine Liste mit Auswahlmöglichkeiten anzuzeigen, verwenden Sie **Prompts.choice**. Die Option **synonyms** ermöglicht mehr Flexibilität beim Erkennen von Benutzeräußerungen. Die Option **value** wird in **results.response.entity** zurückgegeben. Die Option **action** gibt die Bezeichnung an, die Ihr Bot für die Auswahl anzeigt.

**Prompts.Choice** unterstützt ordinale Auswahlmöglichkeiten. Dies bedeutet, dass der Benutzer „die erste“, „der zweite" oder „das dritte“ sagen kann, um ein Element in einer Liste auszuwählen. Beispielsweise gibt bei der folgenden Eingabeaufforderung, wenn der Benutzer Cortana um „die zweite Option“ gebeten hat, die Eingabeaufforderung den Wert 8 zurück.

```javascript
        var choices = [
            { value: '4', action: { title: '4 Sides' }, synonyms: 'four|for|4 sided|4 sides' },
            { value: '8', action: { title: '8 Sides' }, synonyms: 'eight|ate|8 sided|8 sides' },
            { value: '12', action: { title: '12 Sides' }, synonyms: 'twelve|12 sided|12 sides' },
            { value: '20', action: { title: '20 Sides' }, synonyms: 'twenty|20 sided|20 sides' },
        ];
        builder.Prompts.choice(session, 'choose_sides', choices, { 
            speak: speak(session, 'choose_sides_ssml') // use helper function to format SSML
        });
```

Im vorherigen Beispiel wird SSML für die **speak**-Eigenschaft der Eingabeaufforderung mithilfe von Zeichenfolgen formatiert, die in einer lokalisierten Eingabeaufforderungsdatei mit dem folgenden Format gespeichert wurden. 

```json
{
    "choose_sides": "__Number of Sides__",
    "choose_sides_ssml": [
        "How many sides on the dice?",
        "Pick your poison.",
        "All the standard sizes are supported."
    ]
}
```


Eine Hilfsfunktion erstellt dann das erforderliche Stammelement eines SSML-Dokuments (Speech Synthesis Markup Language). 

```javascript

module.exports.speak = function (template, params, options) {
    options = options || {};
    var output = '<speak xmlns="http://www.w3.org/2001/10/synthesis" ' +
        'version="' + (options.version || '1.0') + '" ' +
        'xml:lang="' + (options.lang || 'en-US') + '">';
    output += module.exports.vsprintf(template, params);
    output += '</speak>';
    return output;
}
```

> [!TIP]
> Ein kleines Hilfsprogramm-Modul (ssml.js) zum Erstellen von SSML-basierten Antworten des Bots finden Sie im [RollerSkill-Beispiel](https://github.com/Microsoft/BotBuilder-Samples/tree/master/Node/demo-RollerSkill).
> Es gibt auch einige nützliche SSML-Bibliotheken, die über [npm](https://www.npmjs.com/search?q=ssml) verfügbar sind und das Erstellen ordnungsgemäß formatierter SSML erleichtern.

## <a name="display-cards-in-cortana"></a>Anzeigen von Karten in Cortana

Zusätzlich zu den gesprochenen Antworten kann Cortana auch Karten als Anlagen anzeigen. Cortana unterstützt die folgenden Rich Cards:
* [HeroCard](https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.herocard.html)
* [ReceiptCard](https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.receiptcard.html)
* [ThumbnailCard](https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.thumbnailcard.html)

Wie diese Karten in Cortana aussehen, ist unter [Kartendesign – Bewährte Methoden für Cortana][CardDesign] beschrieben. Ein Beispiel für das Senden einer Rich Card an einen Bot finden Sie unter [Senden von Rich Cards](bot-builder-nodejs-send-rich-cards.md). 

Der folgende Code veranschaulicht das Hinzufügen der **speak**- und **inputHint**-Eigenschaften zu einer Nachricht, die eine Hero Card enthält.

```javascript 

bot.dialog('HelpDialog', function (session) {
    var card = new builder.HeroCard(session)
        .title('help_title')
        .buttons([
            builder.CardAction.imBack(session, 'roll some dice', 'Roll Dice'),
            builder.CardAction.imBack(session, 'play yahtzee', 'Play Yahtzee')
        ]);
    var msg = new builder.Message(session)
        .speak(speak(session, 'I\'m roller, the dice rolling bot. You can say \'roll some dice\''))
        .addAttachment(card)
        .inputHint(builder.InputHint.acceptingInput); // Tell Cortana to accept input
    session.send(msg).endDialog();
}).triggerAction({ matches: /help/i });


/** This helper function builds the required root element of a Speech Synthesis Markup Language (SSML) document. */
module.exports.speak = function (template, params, options) {
    options = options || {};
    var output = '<speak xmlns="http://www.w3.org/2001/10/synthesis" ' +
        'version="' + (options.version || '1.0') + '" ' +
        'xml:lang="' + (options.lang || 'en-US') + '">';
    output += module.exports.vsprintf(template, params);
    output += '</speak>';
    return output;
}

```
## <a name="sample-rollerskill"></a>Beispiel: RollerSkill
Der Code in den folgenden Abschnitten stammt aus einer Cortana-Beispielfähigkeit für das Rollen von Würfeln. Laden Sie den vollständigen Code für den Bot aus dem [BotBuilder-Samples Repository](https://github.com/Microsoft/BotBuilder-Samples/tree/master/Node/demo-RollerSkill) herunter.

Sie rufen die Fähigkeit auf, indem Sie Cortana den [Aufrufnamen][InvocationNameGuidelines] sagen. Bei RollerSkill können Sie, nachdem Sie den [Bot zum Cortana-Kanal hinzugefügt ][CortanaChannel] und ihn als Cortana-Fähigkeit registriert haben, die Fähigkeit aufrufen, indem Sie Cortana „Ask Roller“ oder „Ask Roller to roll dice“ sagen.

### <a name="explore-the-code"></a>Untersuchen des Codes

Das RollerSkill-Beispiel beginnt mit dem Öffnen einer Karte mit einigen Schaltflächen, die dem Benutzer mitteilen, welche Optionen zur Verfügung stehen.

```javascript
/**
 *   Create your bot with a default message handler that receive messages from the user.
 * - This function is be called anytime the user's utterance isn't
 *   recognized by the other handlers in the bot.
 */
var bot = new builder.UniversalBot(connector, function (session) {
    // Just redirect to our 'HelpDialog'.
    session.replaceDialog('HelpDialog');
});

//...

bot.dialog('HelpDialog', function (session) {
    var card = new builder.HeroCard(session)
        .title('help_title')
        .buttons([
            builder.CardAction.imBack(session, 'roll some dice', 'Roll Dice'),
            builder.CardAction.imBack(session, 'play craps', 'Play Craps')
        ]);
    var msg = new builder.Message(session)
        .speak(speak(session, 'help_ssml'))
        .addAttachment(card)
        .inputHint(builder.InputHint.acceptingInput);
    session.send(msg).endDialog();
}).triggerAction({ matches: /help/i });
```

### <a name="prompt-the-user-for-input"></a>Auffordern des Benutzers zur Eingabe

Im folgenden Dialog wird ein benutzerdefiniertes Spiel eingerichtet, das der Bot wiedergeben soll.  Der Benutzer wird gefragt, wie viele Seiten der Würfel haben soll, und anschließend, wie viele gerollt werden sollen. Nachdem die Spielstruktur erstellt wurde, wird sie an einen separaten „PlayGameDialog“ geleitet.

Um den Dialog zu starten, ermöglicht der **triggerAction()**-Handler in diesem Dialog dem Benutzer eine Spracheingabe, z. B. kann der Benutzer „I'd like to roll some dice“ sagen. Zum Abgleich der Benutzereingabe wird ein regulärer Ausdruck verwendet; Sie können aber auch einfach eine [LUIS-Absicht](./bot-builder-nodejs-recognize-intent-luis.md) verwenden. 


```javascript


bot.dialog('CreateGameDialog', [
    function (session) {
        // Initialize game structure.
        // - dialogData gives us temporary storage of this data in between
        //   turns with the user.
        var game = session.dialogData.game = { 
            type: 'custom', 
            sides: null, 
            count: null,
            turns: 0
        };

        var choices = [
            { value: '4', action: { title: '4 Sides' }, synonyms: 'four|for|4 sided|4 sides' },
            { value: '6', action: { title: '6 Sides' }, synonyms: 'six|sex|6 sided|6 sides' },
            { value: '8', action: { title: '8 Sides' }, synonyms: 'eight|8 sided|8 sides' },
            { value: '10', action: { title: '10 Sides' }, synonyms: 'ten|10 sided|10 sides' },
            { value: '12', action: { title: '12 Sides' }, synonyms: 'twelve|12 sided|12 sides' },
            { value: '20', action: { title: '20 Sides' }, synonyms: 'twenty|20 sided|20 sides' },
        ];
        builder.Prompts.choice(session, 'choose_sides', choices, { 
            speak: speak(session, 'choose_sides_ssml') 
        });
    },
    function (session, results) {
        // Store users input
        // - The response comes back as a find result with index & entity value matched.
        var game = session.dialogData.game;
        game.sides = Number(results.response.entity);

        /**
         * Ask for number of dice.
         */
        var prompt = session.gettext('choose_count', game.sides);
        builder.Prompts.number(session, prompt, {
            speak: speak(session, 'choose_count_ssml'),
            minValue: 1,
            maxValue: 100,
            integerOnly: true
        });
    },
    function (session, results) {
        // Store users input
        // - The response is already a number.
        var game = session.dialogData.game;
        game.count = results.response;

        /**
         * Play the game we just created.
         * 
         * replaceDialog() ends the current dialog and start a new
         * one in its place. We can pass arguments to dialogs so we'll pass the
         * 'PlayGameDialog' the game we created.
         */
        session.replaceDialog('PlayGameDialog', { game: game });
    }
]).triggerAction({ matches: [
    /(roll|role|throw|shoot).*(dice|die|dye|bones)/i,
    /new game/i
 ]});
```

### <a name="render-results"></a>Rendern der Ergebnisse

 Dieser Dialog ist die Hauptspielschleife. Der Bot speichert die Spielstruktur in **session.conversationData**, damit der gleiche Würfelsatz erneut gerollt werden kann, wenn der Benutzer sagt „roll again“.

```javascript

bot.dialog('PlayGameDialog', function (session, args) {
    // Get current or new game structure.
    var game = args.game || session.conversationData.game;
    if (game) {
        // Generate rolls
        var total = 0;
        var rolls = [];
        for (var i = 0; i < game.count; i++) {
            var roll = Math.floor(Math.random() * game.sides) + 1;
            if (roll > game.sides) {
                // Accounts for 1 in a million chance random() generated a 1.0
                roll = game.sides;
            }
            total += roll;
            rolls.push(roll);
        }

        // Format roll results
        var results = '';
        var multiLine = rolls.length > 5;
        for (var i = 0; i < rolls.length; i++) {
            if (i > 0) {
                results += ' . ';
            }
            results += rolls[i];
        }

        // Render results using a card
        var card = new builder.HeroCard(session)
            .subtitle(game.count > 1 ? 'card_subtitle_plural' : 'card_subtitle_singular', game)
            .buttons([
                builder.CardAction.imBack(session, 'roll again', 'Roll Again'),
                builder.CardAction.imBack(session, 'new game', 'New Game')
            ]);
        if (multiLine) {
            //card.title('card_title').text('\n\n' + results + '\n\n');
            card.text(results);
        } else {
            card.title(results);
        }
        var msg = new builder.Message(session).addAttachment(card);

        // Determine bots reaction for speech purposes
        var reaction = 'normal';
        var min = game.count;
        var max = game.count * game.sides;
        var score = total/max;
        if (score == 1.0) {
            reaction = 'best';
        } else if (score == 0) {
            reaction = 'worst';
        } else if (score <= 0.3) {
            reaction = 'bad';
        } else if (score >= 0.8) {
            reaction = 'good';
        }

        // Check for special craps rolls
        if (game.type == 'craps') {
            switch (total) {
                case 2:
                case 3:
                case 12:
                    reaction = 'craps_lose';
                    break;
                case 7:
                    reaction = 'craps_seven';
                    break;
                case 11:
                    reaction = 'craps_eleven';
                    break;
                default:
                    reaction = 'craps_retry';
                    break;
            }
        }

        // Build up spoken response
        var spoken = '';
        if (game.turn == 0) {
            spoken += session.gettext('start_' + game.type + '_game_ssml') + ' ';
        } 
        spoken += session.gettext(reaction + '_roll_reaction_ssml');
        msg.speak(ssml.speak(spoken));

        // Increment number of turns and store game to roll again
        game.turn++;
        session.conversationData.game = game;

        /**
         * Send card and bot's reaction to user. 
         */

        msg.inputHint(builder.InputHint.acceptingInput);
        session.send(msg).endDialog();
    } else {
        // User started session with "roll again" so let's just send them to
        // the 'CreateGameDialog'
        session.replaceDialog('CreateGameDialog');
    }
}).triggerAction({ matches: /(roll|role|throw|shoot) again/i });
```

## <a name="next-steps"></a>Nächste Schritte
Wenn Sie einen Bot haben, der lokal ausgeführt oder in der Cloud bereitgestellt wird, können Sie ihn aus Cortana aufrufen. Unter [Testen einer Cortana-Fähigkeit](../bot-service-debug-cortana-skill.md) finden Sie die Schritte, die zum Testen Ihrer Cortana-Fähigkeit erforderlich sind.


## <a name="additional-resources"></a>Zusätzliche Ressourcen
* [Cortana Skills Kit][CortanaGetStarted]
* [Hinzufügen von Spracheingabe zu Nachrichten](bot-builder-nodejs-text-to-speech.md)
* [SSML-Referenz][SSMLRef]
* [Sprachdesign – Bewährte Methoden für Cortana][VoiceDesign]
* [Kartendesign – Bewährte Methoden für Cortana][CardDesign]
* [Cortana Dev Center][CortanaDevCenter]
* [Testen und Debuggen von bewährten Methoden für Cortana][Cortana-TestBestPractice]


[CortanaGetStarted]: /cortana/getstarted
[BFPortal]: https://dev.botframework.com/


[SSMLRef]: https://aka.ms/cortana-ssml
[IMessage]: https://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.imessage.html
[Send]: https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.session#send
[CortanaDevCenter]: https://developer.microsoft.com/en-us/cortana

[CortanaSpecificEntities]: https://aka.ms/lgvcto
[CortanaAuth]: https://aka.ms/vsdqcj

[InvocationNameGuidelines]: https://aka.ms/cortana-invocation-guidelines
[VoiceDesign]: https://aka.ms/cortana-design-voice
[CardDesign]: https://aka.ms/cortana-design-card
[Cortana-Debug]: https://aka.ms/cortana-enable-debug
[Cortana-Publish]: https://aka.ms/cortana-publish


[CortanaTry]: https://aka.ms/try-cortana-bot
[CortanaChannel]: https://aka.ms/bot-cortana-channel
[Cortana-TestBestPractice]: https://aka.ms/cortana-test-best-practice
