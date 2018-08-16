---
title: Vorschau auf Bot-Features mit dem Kanalinspektor | Microsoft-Dokumentation
description: Erfahren Sie über den Kanalinspektor, wie verschiedene Bot Framework-Features in verschiedenen Kanälen aussehen und funktionieren.
keyword: bot, preview features, channel, display, Channel Inspector, Text formatting, markdown, XML
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
ms.openlocfilehash: def3f811704af2e2a22612ba5755eb5dbd2d8044
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39301813"
---
# <a name="preview-bot-features-with-the-channel-inspector"></a>Vorschau auf Bot-Features mit dem Kanalinspektor

Bot Framework ermöglicht Ihnen das Erstellen von Bots mit einer Vielzahl von Features wie z.B. Text, Schaltflächen, Bildern, umfassenden Informationen, im Karussell- oder Listenformat angezeigten Rich Cards und vieles mehr. Letztendlich steuert jedoch jeder Kanal individuell, wie Features von seinen Messaging-Clients gerendert werden.

Auch wenn ein Feature von mehreren Kanälen unterstützt wird, kann jeder Kanal das Feature etwas unterschiedlich rendern. Wenn eine Nachricht Features enthält, die ein Kanal nicht systemintern unterstützt, versucht der Kanal möglicherweise, Nachrichteninhalte als Text oder statisches Bild abwärts zu rendern, was erhebliche Auswirkungen auf die Darstellung der Nachricht auf dem Client haben kann. In einigen Fällen unterstützt ein Kanal ein bestimmtes Feature möglicherweise überhaupt nicht. GroupMe-Clients können beispielsweise keinen Eingabeindikators anzeigen.

## <a name="the-channel-inspector"></a>Der Kanalinspektor

Der [Kanalinspektor][inspector] wird erstellt, um Ihnen eine Vorschau auf die Darstellung und Funktionsweise verschiedener Bot Framework-Features in verschiedenen Kanälen zu bieten. Wenn Sie wissen, wie Features von verschiedenen Kanälen gerendert werden, können Sie Ihren Bot so entwerfen, dass er in den Kanälen, über die er kommuniziert, eine herausragende Benutzererfahrung bietet. Der Kanalinspektor bietet auch eine hervorragende Möglichkeit, um sich über Bot Framework-Features zu informieren und diese visuell zu erkunden.

> [!NOTE]
> Rich Cards sind ein Entwicklungsstandard für den Bot-Informationsaustausch, um eine konsistente Anzeige über mehrere Kanäle hinweg sicherzustellen. Weitere Informationen zu Rich Cards finden Sie in der [.NET][netcard]- oder [Node.js][nodecard]-Dokumentation.

## <a name="text-formatting"></a>Textformatierung

Textformatierung kann die visuelle Darstellung Ihrer Textnachrichten verbessern. Neben **Nur-Text** kann Ihr Bot auch Nachrichten mit **Markdown**- oder **XML**-Formatierung an Kanäle senden, die sie unterstützen. In den folgenden Tabellen sind einige der gängigsten Textformatierungen in **Markdown** und **XML** aufgelistet. Die einzelnen Kanäle unterstützen möglicherweise mehr oder weniger Textformatierungen als hier aufgeführt. Im [Kanalinspektor][inspector] können Sie überprüfen, ob ein Feature, das Sie verwenden möchten, auf einem Ihrer Zielkanäle unterstützt wird.

### <a name="markdown-text-format"></a>Markdown-Textformat

Diese Formate können unterstützt werden, wenn `textFormat` auf **Markdown** festgelegt ist:  

| Style | Markdown | Beispiel |
| ---- | ---- | ---- |
| fett | \*\*text\*\* | **text** |
| kursiv | \*text\* | *text* |
| Header (1–5) | # H1 | # H1 |
| durchgestrichen | \~\~text\~\~ | ~~text~~ |
| horizontale Trennlinie | ---- | ---- |
| unsortierte Liste | \* Text |  <ul><li>text</li></ul> |
| sortierte Liste | 1. Text | 1. Text |
| vorformatierter Text | \`text\` | `text`  |
| Blockzitat | \> Text | <blockquote>text</blockquote> |
| Hyperlink | \[Bing](http://www.bing.com) | [Bing](http://www.bing.com) |
| Bildlink| !\[Ente](http://aka.ms/Fo983c) | ![Ente](http://aka.ms/Fo983c) |

> [!NOTE]
> HTML-Tags in **Markdown** werden in Webchat-Kanälen von Microsoft Bot Framework nicht unterstützt. Wenn Sie HTML-Tags in **Markdown** verwenden müssen, können Sie diese in einem [Direct Line](~/bot-service-channel-connect-directline.md)-Kanal rendern, der sie unterstützt. Alternativ können Sie die folgenden HTML-Tags verwenden, indem Sie `textFormat` auf **XML** festlegen und Ihren Bot mit dem Skype-Kanal verbinden.

### <a name="xml-text-format"></a>XML-Textformat

Diese Formate können unterstützt werden, wenn `textFormat` auf **XML** festgelegt ist:

|      Style      |                       XML                       |                   Beispiel                   |
|-----------------|-------------------------------------------------|---------------------------------------------|
|      fett       |                 \<b\>Text\</b\>                 |            <strong>text</strong>            |
|     kursiv      |                 \<i\>Text\</i\>                 |                <em>text</em>                |
|    unterstrichen    |                 \<u\>Text\</u\>                 |                 <u>text</u>                 |
|  durchgestrichen  |                 \<s\>Text\</s\>                 |                 <s>text</s>                 |
|    Hyperlink    |   \<a href="http://www.bing.com"\>Bing\</a\>    |   <a href="http://www.bing.com">Bing</a>    |
|    Absatz    |                 \<p\>Text\</p\>                 |                 <p>text</p>                 |
|   Zeilenumbruch    |                     \<br/\>                     |             Zeile 1 <br/>Zeile 2              |
| horizontale Trennlinie |                     \<hr/\>                     |                    <hr/>                    |
|  Header (1–4)   |                \<h1\>Text\</h1\>                |             <h1>Überschrift 1</h1>              |
|      code       |           \<code\>Codeblock\</code\>           |           <code>code block</code>           |
|      image      | \<img src="<http://aka.ms/Fo983c>" alt="Ente"\> | <img src="http://aka.ms/Fo983c" alt="Duck"> |

> [!NOTE]
> Das `textFormat` **XML** wird nur vom Skype-Kanal unterstützt.

## <a name="preview-features-across-various-channels"></a>Vorschau auf Features in verschiedenen Kanälen

Um anzuzeigen, wie ein Kanal ein bestimmtes Feature rendert, wechseln Sie zum [Kanalinspektor][inspector] und wählen den Kanal in der **Kanalliste** und das Feature in der **Featureliste** aus. Um anzuzeigen, wie eine Hero-Karte von Skype gerendert wird, legen Sie den **Kanal** auf *Skype* und das **Feature** auf *HeroCard* fest.

![Kanalinspektor zeigt Skype-Kanal und Hero-Karte an](~/media/bot-service-channel-inspector.png)

Der Kanalinspektor zeigt eine Vorschau auf das ausgewählte Feature an, wie es vom angegebenen Kanal gerendert wird. Der Abschnitt mit den **Hinweisen** Abschnitt enthält wichtige Informationen über Nachrichteneinschränkungen und/oder Anzeigeänderungen. Beispielsweise unterstützen einige Typen von Rich Cards nur ein Bild, und einige Features werden möglicherweise für bestimmte Kanäle abwärts gerendert.

> [!NOTE]
> Der Kanalinspektor unterstützt derzeit die folgenden Kanäle:
> * Cortana
> * E-Mail
> * Facebook
> * GroupMe
> * Kik
> * Skype
> * Skype for Business
> * Puffer
> * sms
> * Microsoft Teams
> * Telegram
> * WeChat
> * Web Chat
>
> Weitere Kanäle können künftig hinzugefügt werden.

## <a name="features-that-can-be-previewed"></a>Features mit verfügbarer Vorschau

Der Kanalinspektor ermöglicht derzeit die Vorschau auf folgende Features.

|Feature | BESCHREIBUNG|
| --- | ----|
| Adaptive Karten | Eine Karte, die eine beliebige Kombination von Text, Sprache, Bildern, Schaltflächen und Eingabefeldern enthalten kann. |
| Schaltflächen| Schaltflächen, auf die der Benutzer klicken kann. Schaltflächen werden auf der Konversations-Canvas mit der zugehörigen Nachricht angezeigt. |
| Karussell| Eine kompakte, bildlauffähige horizontale Liste von Karten. Verwenden Sie für ein vertikales Layout eine Liste.|
| ChannelData| Eine Möglichkeit zur Übergabe von Metadaten für den Zugriff auf kanalspezifische Funktionalität, die über Karten, Text und Anlagen hinausgeht.|
| Codesample| Ein mehrzeiliger, vorformatierter Textblock, der zum Anzeigen von Computercode entwickelt wurde.|
| DirectMessage| Eine Nachricht, die an ein einzelnes Mitglied einer Gruppenkonversation gesendet wird.
| Dokument| Senden und Empfangen von Nicht-Medienanlagen. |
| Emoji| Anzeigen von unterstützten Emojis.
| GroupChat| Bot sendet Nachrichten, um an Gruppenkonversationen teilzunehmen. |
| HeroCard| Eine formatierte Anlage, die in der Regel ein einzelnes großes Bild, eine Tippaktion und Schaltflächen (optional) zusammen mit beschreibendem Textinhalt enthält. |
| Bilder| Anzeige von Bildanlagen. |
| Listenlayout| Eine vertikale Liste von Karten. Für ein horizontales, bildlauffähiges Layout verwenden Sie Karussell.|
| Standort| Freigeben des physischen Standorts des Benutzers für den Bot. |
| Markdown| Rendert mit Markdown formatierten Text.|
| Mitglieder| Teilt die Liste der Mitglieder in der Konversation während eines Gruppenchats mit dem Bot. |
| Quittungskarte| Zeigt eine Bestätigung für den Benutzer an. |
| Anmeldekarte| Fordert den Benutzer zur Eingabe von Anmeldeinformationen für die Authentifizierung auf.|
| Vorgeschlagene Aktionen | Als Schaltflächen bereitgestellte Aktionen, die der Benutzer antippen kann, um Eingaben vorzunehmen. |
| Miniaturbildkarte| Eine formatierte Anlage, die ein einzelnes kleines Bild (Miniaturbild), eine Tippaktion und Schaltflächen (optional) zusammen mit beschreibendem Textinhalt enthält. |
| Eingabe| Zeigt einen Eingabeindikator an. Dies ist hilfreich, um den Benutzer zu informieren, dass der Bot noch funktioniert, aber eine Aktion im Hintergrund ausführt.|
| Video| Zeigt Videoanlagen und Steuerelemente für die Wiedergabe an.|

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Kanalinspektor][inspector]
* Rich Cards in [Node.js][nodecard] und [.NET][netcard]
* Medienanlagen in [Node.js][nodemedia] und [.NET][netmedia]
* Empfohlene Aktionen in [Node.js] [ nodebutton] und [.NET][netbutton]

[inspector]: https://docs.botframework.com/en-us/channel-inspector/channels/Skype/

[syntax]: https://daringfireball.net/projects/markdown/syntax

[netcard]: ~/dotnet/bot-builder-dotnet-add-rich-card-attachments.md
[nodecard]: ~/nodejs/bot-builder-nodejs-send-rich-cards.md

[netmedia]: ~/dotnet/bot-builder-dotnet-add-media-attachments.md
[nodemedia]: ~/nodejs/bot-builder-nodejs-send-receive-attachments.md

[netbutton]: ~/dotnet/bot-builder-dotnet-add-suggested-actions.md
[nodebutton]: ~/nodejs/bot-builder-nodejs-send-suggested-actions.md
