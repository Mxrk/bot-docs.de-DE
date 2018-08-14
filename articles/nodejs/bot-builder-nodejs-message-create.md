---
title: Erstellen von Nachrichten | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Nachrichten mit dem Bot Builder SDK für Node.js erstellen.
author: v-ducvo
ms.author: v-ducvo
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 09/7/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: e7dfb72f69202011c4fda06c3d55e0afa8d3d045
ms.sourcegitcommit: dcbc8ad992a3e242a11ebcdf0ee99714d919a877
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/30/2018
ms.locfileid: "39352909"
---
# <a name="create-messages"></a>Erstellen von Nachrichten
Die Kommunikation zwischen den Bot und dem Benutzer erfolgt über Nachrichten. Ihr Bot sendet Nachrichtenaktivitäten, um dem Benutzer Informationen mitzuteilen, und auf die gleiche Weise empfängt er auch Nachrichtenaktivitäten vom Benutzer. Einige Nachrichten können aus Nur-Text bestehen, während andere vielfältigere Inhalte wie gesprochenen Text, vorgeschlagene Aktionen, Medienanlagen, Rich Cards und kanalspezifische Daten enthalten können.

Dieser Artikel beschreibt einige der häufig verwendeten Nachrichtenmethoden, mit denen Sie Ihre Benutzererfahrung optimieren können.

## <a name="default-message-handler"></a>Standardnachrichtenhandler

Das Bot Builder SDK für Node.js enthält einen Standardnachrichtenhandler, der in das [`session`](https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.session.html)-Objekt integriert ist. Dieser Nachrichtenhandler ermöglicht das Senden und Empfangen von Textnachrichten zwischen Bot und Benutzer.

### <a name="send-a-text-message"></a>Senden einer Textnachricht

Das Senden einer Textnachricht mit dem Standardnachrichtenhandler ist einfach. Rufen Sie einfach `session.send` auf, und übergeben Sie eine **Zeichenfolge**.

Dieses Beispiel zeigt, wie Sie eine Textnachricht zur Begrüßung des Benutzers senden.
```javascript
session.send("Good morning.");
```

Dieses Beispiel zeigt, wie Sie eine Textnachricht mit der JavaScript-Zeichenfolgenvorlage senden.
```javascript
var msg = `You ordered: ${order.Description} for a total of $${order.Price}.`;
session.send(msg); //msg: "You ordered: Potato Salad for a total of $5.99."
```

### <a name="receive-a-text-message"></a>Empfangen einer Textnachricht

Wenn ein Benutzer dem Bot eine Nachricht sendet, empfängt der Bot die Nachricht über die `session.message`-Eigenschaft.

Dieses Beispiel zeigt die Vorgehensweise beim Zugriff auf die Nachricht des Benutzers.
```javascript
var userMessage = session.message.text;
```

## <a name="customizing-a-message"></a>Anpassen einer Nachricht

Um mehr Kontrolle über die Textformatierung Ihrer Nachrichten zu haben, können Sie ein benutzerdefiniertes [`message`](https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.message.html)-Objekt erstellen und die erforderlichen Eigenschaften vor dem Senden an den Benutzer festlegen.

Dieses Beispiel zeigt, wie Sie ein benutzerdefiniertes `message`-Objekt erstellen und die `text`-, `textFormat`- und `textLocale`-Eigenschaft festlegen.

```javascript
var customMessage = new builder.Message(session)
    .text("Hello!")
    .textFormat("plain")
    .textLocale("en-us");
session.send(customMessage);
```

Wenn Sie nicht über das `session`-Objekt im Bereich verfügen, können Sie mit der `bot.send`-Methode eine formatierte Nachricht an den Benutzer senden.

Mithilfe der `textFormat`-Eigenschaft einer Nachricht kann das Format des Texts angegeben werden. Die `textFormat`-Eigenschaft kann auf **plain**, **markdown** oder **xml** festgelegt werden. Der Standardwert für `textFormat` ist **markdown**. 

Eine Liste der üblicherweise unterstützten Textformatierungen finden Sie unter [Textformatierung](../bot-service-channel-inspector.md#text-formatting). Um sicherzustellen, dass die Features, die Sie verwenden möchten, vom Zielkanal unterstützt werden, zeigen Sie die Features mit der [Kanalprüfung](../bot-service-channel-inspector.md) in der Vorschau an.

## <a name="message-property"></a>Nachrichteneigenschaft

Das [`Message`](https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.message.html)-Objekt verfügt über eine interne **data**-Eigenschaft, die zum Verwalten der zu sendenden Nachricht verwendet wird. Andere Eigenschaften legen Sie über die verschiedenen Methoden fest, die dieses Objekt für Sie verfügbar macht. 

## <a name="message-methods"></a>Nachrichtenmethoden

Nachrichteneigenschaften werden durch die Methoden des Objekts festgelegt und abgerufen. In der folgenden Tabelle finden Sie eine Liste der Methoden, die Sie aufrufen können, um die verschiedenen **Nachrichten**-Eigenschaften festzulegen/abzurufen.

| Methode | BESCHREIBUNG |
| ---- | ---- | 
| [`addAttachment(attachment:AttachmentType)`](https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.message.html#addattachment) | Fügt eine Anlage zu einer Nachricht hinzu.|
| [`addEntity(obj:Object)`](https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.message.html#addentity) | Fügt eine Entität zur Nachricht hinzu. |
| [`address(adr:IAddress)`](https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.message.html#address) | Adressroutinginformationen für die Nachricht. Um einem Benutzer eine proaktiven Nachricht zu senden, speichern Sie die Adresse der Nachricht im userData-Behälter. |
| [`attachmentLayout(style:string)`](https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.message.html#attachmentlayout) | Hinweis für Clients zum Layout mehrerer Anlagen. Der Standardwert ist „list“. |
| [`attachments(list:AttachmentType)`](https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.message.html#attachments) | Eine Liste von Karten oder Bildern, die dem Benutzer gesendet werden sollen. |
| [`compose(prompts:string[], ...args:any[])`](https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.message.html#compose) | Erstellt eine komplexe und zufällige Antwort an den Benutzer. |
| [`entities(list:Object[])`](https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.message.html#entities) | Strukturierte Objekte, die an den Bot oder den Benutzer übergeben werden. |
| [`inputHint(hint:string)`](https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.message.html#inputhint) | Hinweis für den Benutzer, ob der Bot weitere oder keine weiteren Eingaben erwartet. Durch die integrierten Eingabeaufforderungen wird bei ausgehenden Nachrichten dieser Wert automatisch ausgefüllt. |
| [`localTimeStamp((optional)time:string)`](https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.message.html#localtimestamp) | Sendezeit der Nachricht (Ortszeit) (festgelegt von Client und Bot; Beispiel: 2016-09-23T13:07:49.4714686-07:00.) |
| [`originalEvent(event:any)`](https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.message.html#originalevent) | Nachricht im ursprünglichen/systemeigenen Format des Kanals für eingehende Nachrichten. |
| [`sourceEvent(map:ISourceEventMap)`](https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.message.html#sourceevent) | Kann für ausgehende Nachrichten verwendet werden, um quellenspezifische Ereignisdaten wie benutzerdefinierte Anlagen zu übergeben. |
| [`speak(ssml:TextType, ...args:any[])`](https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.message.html#speak) | Legt *Speech Synthesis Markup Language (SSML)* für das Sprachfeld der Nachricht fest. Dieses wird dem Benutzer auf unterstützten Geräten vorgelesen. |
| [`suggestedActions(suggestions:ISuggestedActions `&#124;` IIsSuggestedActions)`](https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.message.html#suggestedactions) | Optional vorgeschlagene Aktionen, die an den Benutzer gesendet werden sollen. Vorgeschlagene Aktionen werden nur auf den Kanälen angezeigt, die vorgeschlagene Aktionen unterstützen. |
| [`summary(text:TextType, ...argus:any[])`](https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.message.html#summary) | Text, der als Fallback sowie als kurze Beschreibung des Nachrichteninhalts angezeigt wird (z. B. Liste der kürzlichen Unterhaltungen). |
| [`text(text:TextType, ...args:any[])`](https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.message.html#text) | Legt den Nachrichtentext fest. |
| [`textFormat(style:string)`](https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.message.html#textformat) | Legt das Textformat fest. Das Standardformat ist **markdown**. |
| [`textLocale(locale:string)`](https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.message.html#textlocale) | Legt die Zielsprache der Nachricht fest. |
| [`toMessage()`](https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.message.html#tomessage) | Ruft den JSON-Code für die Nachricht ab. |
| [`composePrompt(session:Session, prompts:string[], args?:any[])`](https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.message.html#composeprompt-1) | Kombiniert ein Array von Eingabeaufforderungen in eine einzelne lokalisierte Eingabeaufforderung, und füllt dann optional die Leerstellen der Eingabeaufforderungsvorlagen mit den übergebenen Argumenten auf. |
| [`randomPrompt(prompts:TextType)`](https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.message.html#randomprompt) | Ruft eine zufällige Eingabeaufforderung aus dem Array der **Eingabeaufforderungen* ab, das übergeben wird. |

## <a name="next-step"></a>Nächster Schritt

> [!div class="nextstepaction"]
> [Senden und Empfangen von Anlagen](bot-builder-nodejs-send-receive-attachments.md)

