# <a name="implement-channel-specific-functionality"></a>Implementieren von kanalspezifischer Funktionalität

Einige Kanäle stellen Funktionen bereit, die nicht implementiert werden können, indem nur Nachrichtentext und Anlagen verwendet werden. Um kanalspezifische Funktionen zu implementieren, können Sie native Metadaten an einen Kanal in der _channel data_-Eigenschaft des Aktivitätsobjekts übergeben. Beispielsweise kann Ihr Bot die „channel data“-Eigenschaft verwenden, um Telegram anzuweisen, einen Sticker zu senden, oder um Office 365 anzuweisen, eine E-Mail zu senden.

In diesem Artikel wird beschrieben, wie Sie die „channel data“-Eigenschaft einer Nachrichtenaktivität verwenden, um diese kanalspezifische Funktionalität zu implementieren:

| Kanal | Funktionalität |
|----|----|
| E-Mail | Senden und Empfangen einer E-Mail mit Text, Betreff und wichtigen Metadaten |
| Puffer | Senden originalgetreuer Slack-Nachrichten |
| Facebook | Natives Senden von Facebook-Benachrichtigungen |
| Telegram | Ausführen von Telegram-spezifischen Aktionen, z.B. Freigeben einer Sprachnachricht oder eines Stickers |
| Kik | Senden und Empfangen nativer Kik-Nachrichten |

> [!NOTE]
> Der Wert der „channel data“-Eigenschaft eines Aktivitätsobjekts ist ein JSON-Objekt.
> Deswegen zeigen die Beispiele in diesem Artikel das erwartete Format der JSON-Eigenschaft `channelData` in verschiedenen Szenarien.
> Verwenden Sie die (.NET-)Klasse `JObject`, um ein JSON-Objekt mit .NET zu erstellen.

## <a name="create-a-custom-email-message"></a>Erstellen einer benutzerdefinierten E-Mail-Nachricht

Um eine E-Mail-Nachricht zu erstellen, legen Sie die „channel data“-Eigenschaft des Aktivitätsobjekts auf ein JSON-Objekt fest, das diese Eigenschaften enthält:

| Eigenschaft | BESCHREIBUNG |
|----|----|
| bccRecipients | Eine durch ein Semikolon (;) getrennte Zeichenfolge von E-Mail-Adressen, die in das Feld „Bcc“ (Blind Carbon Copy) der Nachricht eingefügt werden sollen. |
| ccRecipients | Eine durch ein Semikolon (;) getrennte Zeichenfolge von E-Mail-Adressen, die in das Feld „Cc“ (Carbon Copy) der Nachricht eingefügt werden sollen. |
| htmlBody | Ein HTML-Dokument, das den E-Mail-Nachrichtentext angibt. Weitere Informationen zu den unterstützten HTML-Elementen und -Attributen finden Sie in der Dokumentation des Kanals. |
| importance | Die Wichtigkeit der E-Mail. Gültige Werte sind **Hoch**, **Normal** und **Niedrig**. Der Standardwert ist **Normal**. |
| subject | Der E-Mail-Betreff. Weitere Informationen zu Feldanforderungen finden Sie in der Dokumentation des Kanals. |
| toRecipients | Eine durch ein Semikolon (;) getrennte Zeichenfolge von E-Mail-Adressen, die in das Feld „An“ der Nachricht eingefügt werden sollen. |

> [!NOTE]
> Nachrichten, die Ihr Bot von Benutzern über den E-Mail-Kanal erhält, enthalten ggf. eine „channel data“-Eigenschaft, die mit einem JSON-Objekt wie dem oben beschriebenen gefüllt ist.

Dieser Codeausschnitt zeigt ein Beispiel für die `channelData`-Eigenschaft für eine benutzerdefinierte E-Mail-Nachricht.

```json
"channelData": {
    "type": "message",
    "locale": "en-Us",
    "channelID": "email",
    "from": { "id": "mybot@mydomain.com", "name": "My bot"},
    "recipient": { "id": "joe@otherdomain.com", "name": "Joe Doe"},
    "conversation": { "id": "123123123123", "topic": "awesome chat" },
    "channelData":
    {
        "htmlBody": "<html><body style = /"font-family: Calibri; font-size: 11pt;/" >This is more than awesome.</body></html>",
        "subject": "Super awesome message subject",
        "importance": "high",
        "ccRecipients": "Yasemin@adatum.com;Temel@adventure-works.com"
    }
}
```

## <a name="create-a-full-fidelity-slack-message"></a>Erstellen einer originalgetreuen Slack-Nachricht

Um eine originalgetreue Slack-Nachricht zu erstellen, setzen Sie die „channel data“-Eigenschaft des Aktivitätsobjekts auf ein JSON-Objekt, das <a href="https://api.slack.com/docs/messages" target="_blank">Slack-Nachrichten</a>, <a href="https://api.slack.com/docs/message-attachments" target="_blank">Slack-Anlagen</a> und/oder <a href="https://api.slack.com/docs/message-buttons" target="_blank">Slack-Schaltflächen</a> festlegt.

> [!NOTE]
> Um Schaltflächen in Slack-Nachrichten zu unterstützen, müssen Sie **interaktive Nachrichten** aktivieren, wenn Sie [Ihren Bot mit dem Slack-Kanal verbinden](../bot-service-manage-channels.md).

Dieser Codeausschnitt zeigt ein Beispiel für die `channelData`-Eigenschaft für eine benutzerdefinierte Slack-Nachricht.

```json
"channelData": {
   "text": "Now back in stock! :tada:",
   "attachments": [
        {
            "title": "The Further Adventures of Slackbot",
            "author_name": "Stanford S. Strickland",
            "author_icon": "https://api.slack.com/img/api/homepage_custom_integrations-2x.png",
            "image_url": "http://i.imgur.com/OJkaVOI.jpg?1"
        },
        {
            "fields": [
                {
                    "title": "Volume",
                    "value": "1",
                    "short": true
                },
                {
                    "title": "Issue",
                    "value": "3",
                    "short": true
                }
            ]
        },
        {
            "title": "Synopsis",
            "text": "After @episod pushed exciting changes to a devious new branch back in Issue 1, Slackbot notifies @don about an unexpected deploy..."
        },
        {
            "fallback": "Would you recommend it to customers?",
            "title": "Would you recommend it to customers?",
            "callback_id": "comic_1234_xyz",
            "color": "#3AA3E3",
            "attachment_type": "default",
            "actions": [
                {
                    "name": "recommend",
                    "text": "Recommend",
                    "type": "button",
                    "value": "recommend"
                },
                {
                    "name": "no",
                    "text": "No",
                    "type": "button",
                    "value": "bad"
                }
            ]
        }
    ]
}
```

Wenn ein Benutzer auf eine Schaltfläche in einer Slack-Nachricht klickt, erhält der Bot eine Antwortnachricht, in der die „channel data“-Eigenschaft mit einem JSON-Objekt vom Typ `payload` gefüllt ist.
Das `payload`-Objekt gibt den Inhalt der ursprünglichen Nachricht an und identifiziert die angeklickte Schaltfläche sowie den Benutzer, der diese angeklickt hat.

Dieser Codeausschnitt zeigt ein Beispiel für die `channelData`-Eigenschaft in der Nachricht, die ein Bot empfängt, wenn ein Benutzer auf eine Schaltfläche in der Slack-Nachricht klickt.

```json
"channelData": {
    "payload": {
        "actions": [
            {
                "name": "recommend",
                "value": "yes"
            }
        ],
        . . .
        "original_message": "{…}",
        "response_url": "https://hooks.slack.com/actions/..."
    }
}
```

Ihr Bot kann auf diese Nachricht normal antworten oder seine Antwort direkt an den Endpunkt posten, der durch die `response_url`-Eigenschaft des `payload`-Objekts angegeben ist.
Weitere Informationen dazu, wann und wie Sie eine Antwort an `response_url` posten können, finden Sie im Artikel zu <a href="https://api.slack.com/docs/message-buttons" target="_blank">Slack-Schaltflächen</a>.

Sie können mit dem folgenden Code dynamische Schaltflächen erstellen:
```cs
private async Task DemoButtonsAsync(IDialogContext context)
        {
            var reply = context.MakeMessage();

            string s = @"{
                ""text"": ""Would you like to play a game ? "",
                ""attachments"": [
                    {
                        ""text"": ""Choose a game to play!"",
                        ""fallback"": ""You are unable to choose a game"",
                        ""callback_id"": ""wopr_game"",
                        ""color"": ""#3AA3E3"",
                        ""attachment_type"": ""default"",
                        ""actions"": [
                            {
                                ""name"": ""game"",
                                ""text"": ""Chess"",
                                ""type"": ""button"",
                                ""value"": ""chess""
                            },
                            {
                                ""name"": ""game"",
                                ""text"": ""Falken's Maze"",
                                ""type"": ""button"",
                                ""value"": ""maze""
                            },
                            {
                                ""name"": ""game"",
                                ""text"": ""Thermonuclear War"",
                                ""style"": ""danger"",
                                ""type"": ""button"",
                                ""value"": ""war"",
                                ""confirm"": {
                                    ""title"": ""Are you sure?"",
                                    ""text"": ""Wouldn't you prefer a good game of chess?"",
                                    ""ok_text"": ""Yes"",
                                    ""dismiss_text"": ""No""
                                }
                            }
                        ]
                    }
                ]
            }";

            reply.Text = null;
            reply.ChannelData = JObject.Parse(s);
            await context.PostAsync(reply);
            context.Wait(MessageReceivedAsync);
        }
```

Verwenden Sie zum Erstellen interaktiver Menüs den folgenden Code:
```cs
private async Task DemoMenuAsync(IDialogContext context)
        {
            var reply = context.MakeMessage();

            string s = @"{
                ""text"": ""Would you like to play a game ? "",
                ""response_type"": ""in_channel"",
                ""attachments"": [
                    {
                        ""text"": ""Choose a game to play"",
                        ""fallback"": ""If you could read this message, you'd be choosing something fun to do right now."",
                        ""color"": ""#3AA3E3"",
                        ""attachment_type"": ""default"",
                        ""callback_id"": ""game_selection"",
                        ""actions"": [
                            {
                                ""name"": ""games_list"",
                                ""text"": ""Pick a game..."",
                                ""type"": ""select"",
                                ""options"": [
                                    {
                                        ""text"": ""Hearts"",
                                        ""value"": ""menu_id_hearts""
                                    },
                                    {
                                        ""text"": ""Bridge"",
                                        ""value"": ""menu_id_bridge""
                                    },
                                    {
                                        ""text"": ""Checkers"",
                                        ""value"": ""menu_id_checkers""
                                    },
                                    {
                                        ""text"": ""Chess"",
                                        ""value"": ""menu_id_chess""
                                    },
                                    {
                                        ""text"": ""Poker"",
                                        ""value"": ""menu_id_poker""
                                    },
                                    {
                                        ""text"": ""Falken's Maze"",
                                        ""value"": ""menu_id_maze""
                                    },
                                    {
                                        ""text"": ""Global Thermonuclear War"",
                                        ""value"": ""menu_id_war""
                                    }
                                ]
                            }
                        ]
                    }
                ]
            }";

            reply.Text = null;
            reply.ChannelData = JObject.Parse(s);
            await context.PostAsync(reply);
            context.Wait(MessageReceivedAsync);
        }
```

## <a name="create-a-facebook-notification"></a>Erstellen einer Facebook-Benachrichtigung

Um eine Facebook-Nachricht zu erstellen, legen Sie die „channel data“-Eigenschaft des Aktivitätsobjekts auf ein JSON-Objekt fest, das diese Eigenschaften angibt:

| Eigenschaft | BESCHREIBUNG |
|----|----|
| notification_type | Der Typ der Benachrichtigung (z.B. **REGULAR**, **SILENT_PUSH**, **NO_PUSH**)
| attachment | Eine Anlage, die ein Bild, ein Video oder einen anderen Multimediatyp angibt bzw. eine auf Vorlagen basierende Anlage, z.B. eine Bestätigung |

> [!NOTE]
> Ausführliche Informationen zum Format und Inhalt der Eigenschaften `notification_type` und `attachment` finden Sie in der <a href="https://developers.facebook.com/docs/messenger-platform/send-api-reference#guidelines" target="_blank">Dokumentation zur Facebook-API</a>. 

Dieser Codeausschnitt zeigt ein Beispiel der `channelData`-Eigenschaft für eine Facebook-Anlage vom Typ „Bestätigung“.

```json
"channelData": {
    "notification_type": "NO_PUSH",
    "attachment": {
        "type": "template"
        "payload": {
            "template_type": "receipt",
            . . .
        }
    }
}
```

## <a name="create-a-telegram-message"></a>Erstellen einer Telegram-Nachricht

Um eine Nachricht zu erstellen, die Telegram-spezifische Aktionen implementiert, z.B. die Freigabe einer Sprachnotiz oder eines Stickers, legen Sie die „channel data“-Eigenschaft des Aktivitätsobjekts auf ein JSON-Objekt fest, das diese Eigenschaften angibt: 

| Eigenschaft | BESCHREIBUNG |
|----|----|
| method | Die aufzurufende Telegram-Bot-API-Methode |
| Parameter | Die Parameter der angegebenen Methode |

Die folgenden Telegram-Methoden werden unterstützt: 

- answerInlineQuery
- editMessageCaption
- editMessageReplyMarkup
- editMessageText
- forwardMessage
- kickChatMember
- sendAudio
- sendChatAction
- sendContact
- sendDocument
- sendLocation
- sendMessage
- sendPhoto
- sendSticker
- sendVenue
- sendVideo
- sendVoice
- unbanChateMember

Weitere Informationen zu diesen Telegram-Methoden und ihren Parametern finden Sie in der <a href="https://core.telegram.org/bots/api#available-methods" target="_blank">Telegram-Bot-API-Dokumentation</a>.

> [!NOTE]
> <ul><li>Der <code>chat_id</code>-Parameter gilt für alle Telegram-Methoden. Wenn Sie <code>chat_id</code> nicht als Parameter angeben, stellt das Framework die ID für die Sie bereit.</li>
> <li>Anstatt Dateiinhalte inline zu übergeben, geben Sie die Datei über eine URL und einen Medientyp an, wie im Beispiel unten gezeigt.</li>
> <li>In jeder Nachricht, die Ihr Bot vom Telegram-Kanal empfängt, enthält die <code>ChannelData</code>-Eigenschaft die Nachricht, die Ihr Bot zuvor gesendet hat.</li></ul>

Dieser Codeausschnitt zeigt ein Beispiel für eine `channelData`-Eigenschaft, die eine einzelne Telegram-Methode angibt.

```json
"channelData": {
    "method": "sendSticker",
    "parameters": {
        "sticker": {
            "url": "https://domain.com/path/gif",
            "mediaType": "image/gif",
        }
    }
}
```

Dieser Codeausschnitt zeigt ein Beispiel für eine `channelData`-Eigenschaft, die ein Telegram-Methodenarray angibt.

```json
"channelData": [
    {
        "method": "sendSticker",
        "parameters": {
            "sticker": {
                "url": "https://domain.com/path/gif",
                "mediaType": "image/gif",
            }
        }
    },
    {
        "method": "sendMessage",
        "parameters": {
            "text": "<b>This message is HTML formatted.</b>",
            "parse_mode": "HTML"
        }
    }
]
```

## <a name="create-a-native-kik-message"></a>Erstellen einer nativen Kik-Nachricht

Um eine native Kik-Nachricht zu erstellen, legen Sie die „channel data“-Eigenschaft des Aktivitätsobjekts auf ein JSON-Objekt fest, das diese Eigenschaft angibt:

| Eigenschaft | BESCHREIBUNG |
|----|----|
| Cloud an das Gerät | Ein Array von Kik-Nachrichten. Weitere Informationen zum Kik-Nachrichtenformat finden Sie im Artikel zu <a href="https://dev.kik.com/#/docs/messaging#message-formats" target="_blank">Kik-Nachrichtenformaten</a>. |

Dieser Codeausschnitt zeigt ein Beispiel für die `channelData`-Eigenschaft für eine native Kik-Nachricht.

```json
"channelData": {
    "messages": [
        {
            "chatId": "c6dd8165…",
            "type": "link",
            "to": "kikhandle",
            "title": "My Webpage",
            "text": "Some text to display",
            "url": "http://botframework.com",
            "picUrl": "http://lorempixel.com/400/200/",
            "attribution": {
                "name": "My App",
                "iconUrl": "http://lorempixel.com/50/50/"
            },
            "noForward": true,
            "kikJsData": {
                    "key": "value"
                }
        }
    ]
}
```

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Entitäten und Aktivitätstypen](../bot-service-activities-entities.md)
- [Bot Framework-Aktivitätsschema](https://github.com/Microsoft/BotBuilder/blob/hub/specs/botframework-activity/botframework-activity.md)
