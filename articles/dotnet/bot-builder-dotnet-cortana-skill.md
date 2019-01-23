---
title: Erstellen einer Cortana-Funktion mit .NET | Microsoft-Dokumentation
description: Lernen Sie die grundlegenden Konzepte des Erstellens einer Cortana-Funktion im Bot Framework SDK für .NET kennen.
keywords: Bot Framework, Cortana-Funktion, Sprache, .NET, SDK, Hauptkonzepte, Kernkonzepte
author: DeniseMak
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 98fc10a806a4c8d1a4d6563934d92b0e0cdbb771
ms.sourcegitcommit: b15cf37afc4f57d13ca6636d4227433809562f8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/11/2019
ms.locfileid: "54224775"
---
# <a name="build-a-speech-enabled-bot-with-cortana-skills"></a>Erstellen eines sprachaktivierten Bots mit Cortana-Funktionen

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

> [!div class="op_single_selector"]
> - [.NET](../dotnet/bot-builder-dotnet-cortana-skill.md)
> - [Node.js](../nodejs/bot-builder-nodejs-cortana-skill.md)


Das Bot Framework SDK für .NET ermöglicht Ihnen die Erstellung eines sprachaktivierten Bots, indem Sie ihn als eine Cortana-Funktion mit dem Cortana-Kanal verbinden. 


> [!TIP]
> Weitere Informationen zur Definition einer Funktion und zu den Einsatzmöglichkeiten finden Sie im [Cortana Skills Kit][CortanaGetStarted].

Das Erstellen einer Cortana-Funktion mit dem Bot Framework erfordert nur sehr geringe Cortana-spezifische Kenntnisse und besteht in erster Linie aus dem Erstellen eines Bots. Einer der wichtigsten Unterschiede zu anderen Bots, die Sie vielleicht schon erstellt haben, besteht wahrscheinlich darin, dass Cortana sowohl über eine visuelle als auch eine Audiokomponente verfügt. Für die visuelle Komponente stellt Cortana einen Bereich der Canvas zum Rendern von Inhalten (z.B. Karten) bereit. Für die Audiokomponente geben Sie Text oder SSML in Ihre Botnachrichten ein, die Cortana dem Benutzer vorliest und so Ihrem Bot eine Stimme verleiht. 

> [!NOTE]
> Cortana ist auf vielen verschiedenen Geräten verfügbar. Einige haben einen Bildschirm, während das bei anderen (z.B. eigenständiger Lautsprecher) möglicherweise nicht der Fall ist. Sie sollten sicherstellen, dass Ihr Bot beide Szenarien verarbeiten kann. Informationen zum Überprüfen von Geräteinformationen finden Sie unter [Cortana-spezifische Entitäten][CortanaSpecificEntities].

## <a name="adding-speech-to-your-bot"></a>Hinzufügen von Sprache zu Ihrem Bot

Gesprochene Nachrichten von Ihrem Bot werden in der Speech Synthesis Markup Language (SSML) dargestellt. Mit dem Bot Framework SDK können Sie SSML in Ihre Bot-Antworten einbinden, um neben der Anzeige des Bots auch dessen Sprachausgabe zu steuern.  Sie können auch den Status des Mikrofons von Cortana steuern, indem Sie angeben, ob Ihr Bot Benutzereingaben akzeptiert, erwartet oder ignoriert.

Legen Sie die `Speak`-Eigenschaft des `IMessageActivity`-Objekts fest, um eine Nachricht anzugeben, die Cortana sprechen soll. Wenn Sie reinen Text angeben, legt Cortana fest, wie die Wörter indiziert werden. 

```cs
Activity reply = activity.CreateReply("This is the text that Cortana displays."); 
reply.Speak = "This is the text that Cortana will say.";
```

Wenn Sie Tonhöhe, Ton und Betonung genauer steuern möchten, formatieren Sie die `Speak`-Eigenschaft als [Speech Synthesis Markup Language (SSML)](http://www.w3.org/TR/speech-synthesis/).  

Im folgenden Codebeispiel wird angegeben, dass das Wort „Text“ mit mäßiger Betonung gesprochen werden soll:
```cs
Activity reply = activity.CreateReply("This is the text that will be displayed.");
reply.Speak = "<speak version=\"1.0\" xmlns=\"http://www.w3.org/2001/10/synthesis\" xml:lang=\"en-US\">This is the <emphasis level=\"moderate\">text</emphasis> that will be spoken.</speak>";
```


Mit der **InputHint**-Eigenschaft wird in Cortana angegeben, ob Ihr Bot eine Eingabe erwartet. Der Standardwert ist **ExpectingInput** für eine Eingabeaufforderung und **AcceptingInput** für anderen Arten von Antworten.


| Wert | BESCHREIBUNG |
|------|------|
| **AcceptingInput** | Ihr Bot ist passiv bereit für eine Eingabe, wartet jedoch nicht auf eine Antwort. Cortana akzeptiert Eingaben vom Benutzer, wenn der Benutzer die Mikrofontaste gedrückt hält.|
| **ExpectingInput** | Gibt an, dass der Bot aktiv eine Antwort vom Benutzer erwartet. Cortana lauscht auf eine Spracheingabe des Benutzers über das Mikrofon.  |
| **IgnoringInput** | Cortana ignoriert Eingaben. Ihr Bot kann diesen Hinweis senden, wenn er aktiv eine Anforderung verarbeitet und Eingaben von Benutzern ignoriert, bis die Anforderung abgeschlossen ist.  |

<!-- TODO: tip about time limit and batching -->

Dieses Beispiel zeigt, wie Sie Cortana bekannt geben, dass eine Benutzereingabe erwartet wird. Das Mikrofon bleibt geöffnet.
```cs
// Add an InputHint to let Cortana know to expect user input
Activity reply = activity.CreateReply("This is the text that will be displayed."); 
reply.Speak = "This is the text that will be spoken.";
reply.InputHint = InputHints.ExpectingInput;
```



## <a name="display-cards-in-cortana"></a>Anzeigen von Karten in Cortana

Zusätzlich zu den gesprochenen Antworten kann Cortana auch Karten als Anlagen anzeigen. Cortana unterstützt die folgenden Rich Cards:

| Kartentyp | BESCHREIBUNG |
|----|----|
| [HeroCard][heroCard] | Rich Cards, die in der Regel ein einzelnes großes Bild, eine oder mehrere Schaltflächen sowie Text enthalten. |
| [ThumbnailCard][thumbnailCard] | Rich Cards, die in der Regel ein Miniaturbild, eine oder mehrere Schaltflächen sowie Text enthalten. |
| [ReceiptCard][receiptCard] | Rich Cards, die einen Bot enthalten, der Benutzern eine Quittung bereitstellen kann. Sie enthalten in der Regel die Liste der Elemente, die in der Quittung enthalten sein sollen, Steuer- und Gesamtbeträge sowie anderen Text. |
| [SignInCard][signinCard] | Rich Cards, die einen Bot enthalten, der den Benutzer auffordert, sich anzumelden. Sie enthalten in der Regel Text und eine oder mehrere Schaltflächen, auf die der Benutzer klicken kann, um den Anmeldevorgang zu initiieren. |


Wie diese Karten in Cortana aussehen, ist unter [Bewährte Methoden für Kartendesign][CardDesign] beschrieben. Ein Beispiel, wie Sie eine Rich Card in einen Bot verwenden, finden Sie unter [Hinzufügen von Rich Card-Anlagen zu Nachrichten](bot-builder-dotnet-add-rich-card-attachments.md). 

<!--
The following code demonstrates how to add the `Speak` and `InputHint` properties to a message containing a `HeroCard`.
-->


## <a name="sample-rollerskill"></a>Beispiel: RollerSkill
Der Code in den folgenden Abschnitten stammt aus einer Cortana-Beispielfunktion für das Werfen von Würfeln. Laden Sie den vollständigen Code für den Bot aus dem [Repository für Bot Builder-Beispiele](https://github.com/Microsoft/BotBuilder-Samples/) herunter.

Sie rufen die Fähigkeit auf, indem Sie Cortana den [Aufrufnamen][InvocationNameGuidelines] sagen. Bei RollerSkill können Sie die Funktion aufrufen, nachdem Sie dem [Cortana-Kanal den Bot hinzugefügt ][CortanaChannel] und ihn als Cortana-Funktion registriert haben, indem Sie Cortana „Ask Roller“ oder „Ask Roller to roll dice“ sagen.

### <a name="explore-the-code"></a>Untersuchen des Codes



Um die entsprechenden Dialoge aufzurufen, untersuchen die in `RootDispatchDialog.cs` definierten Aktivitätshandler die Eingabe des Benutzers anhand von regulären Ausdrücken auf Übereinstimmung. Beispielsweise wird der Handler im folgenden Beispiel ausgelöst, wenn der Benutzer etwas Ähnliches wie „Ich möchte würfeln“ sagt. Im regulären Ausdruck sind Synonyme enthalten, sodass ähnliche Äußerungen den Dialog auslösen.
```cs
        [RegexPattern("(roll|role|throw|shoot).*(dice|die|dye|bones)")]
        [RegexPattern("new game")]
        [ScorableGroup(1)]
        public async Task NewGame(IDialogContext context, IActivity activity)
        {
            context.Call(new CreateGameDialog(), AfterGameCreated);
        }
```

Im Dialog `CreateGameDialog` wird ein benutzerdefiniertes Spiel eingerichtet, das der Bot spielen soll. Der Benutzer wird mithilfe von `PromptDialog` gefragt, wie viele Seiten der Würfel haben soll, und anschließend, wie viele geworfen werden sollen. Beachten Sie, dass das zum Initialisieren der Aufforderung verwendete `PromptOptions`-Objekt eine `speak`-Eigenschaft für die gesprochene Version der Aufforderung aufweist.

```cs
    [Serializable]
    public class CreateGameDialog : IDialog<GameData>
    {
        public async Task StartAsync(IDialogContext context)
        {
            context.UserData.SetValue<GameData>(Utils.GameDataKey, new GameData());

            var descriptions = new List<string>() { "4 Sides", "6 Sides", "8 Sides", "10 Sides", "12 Sides", "20 Sides" };
            var choices = new Dictionary<string, IReadOnlyList<string>>()
             {
                { "4", new List<string> { "four", "for", "4 sided", "4 sides" } },
                { "6", new List<string> { "six", "sex", "6 sided", "6 sides" } },
                { "8", new List<string> { "eight", "8 sided", "8 sides" } },
                { "10", new List<string> { "ten", "10 sided", "10 sides" } },
                { "12", new List<string> { "twelve", "12 sided", "12 sides" } },
                { "20", new List<string> { "twenty", "20 sided", "20 sides" } }
            };

            var promptOptions = new PromptOptions<string>(
                Resources.ChooseSides,
                choices: choices,
                descriptions: descriptions,
                speak: SSMLHelper.Speak(Utils.RandomPick(Resources.ChooseSidesSSML))); // spoken prompt

            PromptDialog.Choice(context, this.DiceChoiceReceivedAsync, promptOptions);
        }

        private async Task DiceChoiceReceivedAsync(IDialogContext context, IAwaitable<string> result)
        {
            GameData game;
            if (context.UserData.TryGetValue<GameData>(Utils.GameDataKey, out game))
            {
                int sides;
                if (int.TryParse(await result, out sides))
                {
                    game.Sides = sides;
                    context.UserData.SetValue<GameData>(Utils.GameDataKey, game);
                }

                var promptText = string.Format(Resources.ChooseCount, sides);

                var promptOption = new PromptOptions<long>(promptText, choices: null, speak: SSMLHelper.Speak(Utils.RandomPick(Resources.ChooseCountSSML)));

                var prompt = new PromptDialog.PromptInt64(promptOption);
                context.Call<long>(prompt, this.DiceNumberReceivedAsync);
            }
        }

        private async Task DiceNumberReceivedAsync(IDialogContext context, IAwaitable<long> result)
        {
            GameData game;
            if (context.UserData.TryGetValue<GameData>(Utils.GameDataKey, out game))
            {
                game.Count = await result;
                context.UserData.SetValue<GameData>(Utils.GameDataKey, game);
            }

            context.Done(game);
        }
    }
```

`PlayGameDialog` rendert die Ergebnisse durch Anzeige auf einer `HeroCard` und Erstellen einer gesprochenen Nachricht, die mit der `Speak`-Methode ausgegeben werden soll.

```cs
   [Serializable]
    public class PlayGameDialog : IDialog<object>
    {
        private const string RollAgainOptionValue = "roll again";

        private const string NewGameOptionValue = "new game";

        private GameData gameData;

        public PlayGameDialog(GameData gameData)
        {
            this.gameData = gameData;
        }

        public async Task StartAsync(IDialogContext context)
        {
            if (this.gameData == null)
            {
                if (!context.UserData.TryGetValue<GameData>(Utils.GameDataKey, out this.gameData))
                {
                    // User started session with "roll again" so let's just send them to
                    // the 'CreateGameDialog'
                    context.Done<object>(null);
                }
            }

            int total = 0;
            var randomGenerator = new Random();
            var rolls = new List<int>();

            // Generate Rolls
            for (int i = 0; i < this.gameData.Count; i++)
            {
                var roll = randomGenerator.Next(1, this.gameData.Sides);
                total += roll;
                rolls.Add(roll);
            }

            // Format rolls results
            var result = string.Join(" . ", rolls.ToArray());
            bool multiLine = rolls.Count > 5;

            var card = new HeroCard()
            {
                Subtitle = string.Format(
                    this.gameData.Count > 1 ? Resources.CardSubtitlePlural : Resources.CardSubtitleSingular,
                    this.gameData.Count,
                    this.gameData.Sides),
                Buttons = new List<CardAction>()
                {
                    new CardAction(ActionTypes.ImBack, "Roll Again", value: RollAgainOptionValue),
                    new CardAction(ActionTypes.ImBack, "New Game", value: NewGameOptionValue)
                }
            };

            if (multiLine)
            {
                card.Text = result;
            }
            else
            {
                card.Title = result;
            }

            var message = context.MakeMessage();
            message.Attachments = new List<Attachment>()
            {
                card.ToAttachment()
            };

            // Determine bots reaction for speech purposes
            string reaction = "normal";

            var min = this.gameData.Count;
            var max = this.gameData.Count * this.gameData.Sides;
            var score = total / max;
            if (score == 1)
            {
                reaction = "Best";
            }
            else if (score == 0)
            {
                reaction = "Worst";
            }
            else if (score <= 0.3)
            {
                reaction = "Bad";
            }
            else if (score >= 0.8)
            {
                reaction = "Good";
            }

            // Check for special craps rolls
            if (this.gameData.Type == "Craps")
            {
                switch (total)
                {
                    case 2:
                    case 3:
                    case 12:
                        reaction = "CrapsLose";
                        break;
                    case 7:
                        reaction = "CrapsSeven";
                        break;
                    case 11:
                        reaction = "CrapsEleven";
                        break;
                    default:
                        reaction = "CrapsRetry";
                        break;
                }
            }

            // Build up spoken response
            var spoken = string.Empty;
            if (this.gameData.Turns == 0)
            {
                spoken += Utils.RandomPick(Resources.ResourceManager.GetString($"Start{this.gameData.Type}GameSSML"));
            }

            spoken += Utils.RandomPick(Resources.ResourceManager.GetString($"{reaction}RollReactionSSML"));

            message.Speak = SSMLHelper.Speak(spoken);

            // Increment number of turns and store game to roll again
            this.gameData.Turns++;
            context.UserData.SetValue<GameData>(Utils.GameDataKey, this.gameData);

            // Send card and bots reaction to user.
            message.InputHint = InputHints.AcceptingInput;
            await context.PostAsync(message);

            context.Done<object>(null);
        }
    }
```
## <a name="next-steps"></a>Nächste Schritte

Wenn Ihr Bot lokal ausgeführt oder in der Cloud bereitgestellt wird, können Sie ihn aus Cortana aufrufen. Unter [Testen einer Cortana-Funktion](../bot-service-debug-cortana-skill.md) finden Sie die erforderlichen Schritte zum Testen Ihrer Cortana-Funktion.


## <a name="additional-resources"></a>Zusätzliche Ressourcen
* [Cortana Skills Kit][CortanaGetStarted]
* [Hinzufügen von Sprache zu Nachrichten](bot-builder-dotnet-text-to-speech.md)
* [SSML-Referenz][SSMLRef]
* [Sprachdesign – bewährte Methoden für Cortana][VoiceDesign]
* [Kartendesign – bewährte Methoden für Cortana][CardDesign]
* [Cortana Dev Center][CortanaDevCenter]
* [Bewährte Methoden beim Testen und Debuggen für Cortana][Cortana-TestBestPractice]
* <a href="/dotnet/api/?view=botbuilder-3.11.0" target="_blank">Referenz zum Bot Framework SDK für .NET</a>

[CortanaGetStarted]: /cortana/getstarted
[BFPortal]: https://dev.botframework.com/

[SSMLRef]: https://aka.ms/cortana-ssml
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

[heroCard]: /dotnet/api/microsoft.bot.connector.herocard

[thumbnailCard]: /dotnet/api/microsoft.bot.connector.thumbnailcard 

[receiptCard]: /dotnet/api/microsoft.bot.connector.receiptcard 

[signinCard]: /dotnet/api/microsoft.bot.connector.signincard 


