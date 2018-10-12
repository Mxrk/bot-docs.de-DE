---
title: Entwerfen der Benutzeroberfläche | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie einen Bot entwerfen, der mithilfe von umfangreichen Benutzersteuerelement, Funktionen zum Verstehen natürlicher Sprache und Spracheingabe eine ansprechende Benutzeroberfläche bietet.
keywords: übersicht, design, benutzeroberfläche, benutzerfreundlichkeit, umfangreiche benutzersteuerelemente
author: matvelloso
ms.author: mateusv
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 09/20/2018
ms.openlocfilehash: 94882202eca48a4c662f0ffa32a80065953f13fa
ms.sourcegitcommit: 3cb288cf2f09eaede317e1bc8d6255becf1aec61
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/27/2018
ms.locfileid: "47389789"
---
# <a name="design-the-user-experience"></a>Entwerfen der Benutzeroberfläche

Sie können Bots mit einer Vielzahl von Features erstellen, beispielsweise Text, Schaltflächen, Bildern, im Karussell- oder Listenformat angezeigten Rich Cards usw. Letztlich bestimmt jedoch jeder Kanal (beispielsweise Facebook, Slack und Skype), wie Features von seinen Messaging-Clients gerendert werden. Auch wenn ein Feature von mehreren Kanälen unterstützt wird, kann jeder Kanal das Feature etwas unterschiedlich rendern. Wenn eine Nachricht Features enthält, die ein Kanal nicht systemintern unterstützt, versucht der Kanal möglicherweise, Nachrichteninhalte als Text oder statisches Bild abwärts zu rendern, was erhebliche Auswirkungen auf die Darstellung der Nachricht auf dem Client haben kann. In einigen Fällen unterstützt ein Kanal ein bestimmtes Feature möglicherweise überhaupt nicht. GroupMe-Clients können beispielsweise keinen Eingabeindikators anzeigen.

## <a name="rich-user-controls"></a>Umfangreiche Benutzersteuerelemente

Bei **umfangreicheren Benutzersteuerelementen** handelt es sich um geläufige UI-Steuerelemente wie Schaltflächen, Bilder, Karusselle und Menüs, die der Bot dem Benutzer anzeigt. Mit diesen Elementen kann der Benutzer eine Auswahl treffen und seine Absichten übermitteln. Ein Bot kann entweder mehrere UI-Steuerelementen verwenden, um eine App zu imitieren, oder sogar als eingebettetes Element in einer App ausgeführt werden. Wenn ein Bot in eine App oder eine Website eingebettet wird, kann er beinahe jedes UI-Steuerelement darstellen, indem er die Funktionen der App nutzt, die er hostet. 

Jahrzehntelang haben Anwendungs- und Websiteentwickler UI-Steuerelemente eingesetzt, um es Benutzern zu ermöglichen, mit ihren Anwendungen zu interagieren, und diese Steuerelemente eignen sich jetzt auch ausgezeichnet für Bots. Beispielsweise sind Schaltflächen eine ausgezeichnete Möglichkeit, den Benutzer eine einfache Auswahl treffen zu lassen. Es ist einfacher und schneller, wenn der Benutzer auf die Schaltfläche **Hotels** klickt, wenn er nach Hotels sucht, als wenn er das Wort eingeben muss. Dies gilt insbesondere für mobile Geräte, denn die Menschen tippen lieber nur auf das Display als Wörter einzugeben.

## <a name="cards"></a>Karten

Mithilfe von Karten können Sie den Benutzern verschiedene visuelle Nachrichten, Audionachrichten und/oder auswählbare Nachrichten anzeigen, wodurch der Konversationsfluss verbessert wird. Wenn ein Benutzer eine Auswahl aus verschiedenen festgelegten Elementen treffen muss, können Sie ein Kartenkarussell anzeigen lassen, in dem jede Karte ein Bild, eine Beschreibung und eine Schaltfläche für eine einzelne Auswahl enthält. Wenn der Benutzer für ein einzelnes Element zwischen verschiedenen Möglichkeiten wählen kann, können Sie ein kleines Einzelbild und mehrere Schaltflächen mit verschiedenen Optionen verwenden. Wurden weitere Informationen zu einem Thema angefordert? Karten können mithilfe von Audio- oder Videoausgaben detaillierte Informationen liefern oder z.B. eine Rechnung anzeigen, auf der die gekauften Artikel aufgelistet werden. Karten können auf verschiedene Weisen verwendet werden, um die Kommunikation zwischen einem Benutzer und einem Bot zu vereinfachen. Welche Karte Sie verwenden ist abhängig von den Anforderungen der jeweiligen Anwendung. Wir wollen uns nun im Detail mit Karten, deren Funktionsweise und der empfohlenen Verwendungsweise beschäftigen. 

Microsoft Bot Service-Karten sind programmierbare Objekte, die standardisierte Sammlungen von umfangreichen Benutzersteuerelementen enthalten, die von vielen verschiedenen Kanälen erkannt werden. In der folgenden Tabelle werden die verschiedenen verfügbaren Karten und bewährten Verwendungsmethoden für die einzelnen Kartentypen aufgeführt.

| Kartentyp | Beispiel | BESCHREIBUNG |
| ---- | ---- | ---- |
| AdaptiveCard | ![AdaptiveCard-Bild](./media/adaptive-card.png) | Ein offenes Format für den Kartenaustausch, das als JSON-Objekt gerendert wird. Wird in der Regel für die Bereitstellung von Karten auf mehreren Kanälen verwendet. Karten passen sich an den jeweiligen Hostkanal an. |
| AnimationCard | ![AnimationCard-Bild](./media/animation-card1.png) | Eine Karte, die animierte GIFs oder kurze Videos abspielen kann. |
| AudioCard | ![AudioCard-Bild](./media/audio-card.png) | Eine Karte, die eine Audiodatei abspielen kann. |
| HeroCard | ![HeroCard-Bild](./media/hero-card1.png) | Eine Karte, die ein einzelnes großes Bild, mindestens eine Schaltflächen sowie Text enthält. Wird in der Regel verwendet, um eine potenzielle Benutzerauswahl optisch hervorzuheben. |
| ThumbnailCard | ![ThumbnailCard-Bild](./media/thumbnail-card.png) | Eine Karte, die ein Miniaturbild, mindestens eine Schaltfläche sowie Text enthält. Wird in der Regel verwendet, um Schaltflächen für eine potenzielle Benutzerauswahl optisch hervorzuheben. |
| ReceiptCard | ![ReceiptCard-Bild](./media/receipt-card1.png) | Eine Karte, über die ein Bot Rechnungen für Benutzer ausstellen kann. Sie enthält in der Regel eine Liste der Elemente, die in der Rechnung enthalten sein sollen, Steuer- und Gesamtbeträge sowie anderen Text. |
| SignInCard | ![SignInCard-Bild](./media/sign-in-card.png) | Eine Karte, über die ein Bot den Benutzer auffordern kann, sich anzumelden. Sie enthält in der Regel Text und mindestens eine Schaltfläche, auf die der Benutzer klicken kann, um den Anmeldevorgang zu initiieren. |
| SuggestedAction | ![Bild der SuggestedAction-Karte](./media/suggested-actions.png) | Zeigt dem Benutzer verschiedene CardActions an, aus denen dieser auswählen kann. Diese Karte wird nicht mehr angezeigt, wenn eine der vorgeschlagenen Aktionen ausgewählt wird. |
| VideoCard | ![VideoCard-Bild](./media/video-card.png) | Eine Karte, die Videos abspielen kann. Wird in der Regel verwendet, um eine URL zu öffnen und ein verfügbares Video zu streamen. |
| CardCarousel | ![CardCarousel-Bild](./media/card-carousel.png) | Eine Sammlung von Karten, durch die horizontal gescrollt werden und aus denen der Benutzer auswählen kann.|

Durch Karten müssen Sie nur einmal einen Bot entwerfen, der dann auf verschiedenen Kanälen eingesetzt werden kann. Allerdings werden nicht alle Kartentypen auf allen verfügbaren Kanälen unterstützt. 

Weitere Informationen zum Hinzufügen von Karten zu Ihrem Bot finden Sie in den Abschnitten [Add rich card media attachments (Hinzufügen von Rich Card-Medienanlagen)](v4sdk/bot-builder-howto-add-media-attachments.md) und [Add suggested actions to messages (Hinzufügen von vorgeschlagenen Aktionen zu Nachrichten)](v4sdk/bot-builder-howto-add-suggested-actions.md). Unter folgenden Links finden Sie darüber hinaus Beispielcode: Karten: [C#](https://aka.ms/bot-cards-sample-code-cs)/[JS](https://aka.ms/bot-cards-sample-code-js), adaptive Karten: [C#](https://aka.ms/bot-adaptive-cards-sample-code)/[JS](https://aka.ms/bot-adaptive-cards-js-sample-code), Anlagen: [C#](https://aka.ms/bot-attachments-sample-code)/[JS](https://aka.ms/bot-attachments-js-sample-code), vorgeschlagene Aktionen: [C#](https://aka.ms/bot-suggested-actions-code)/[JS](https://aka.ms/bot-suggested-actions-js-code)



Sie können für Ihren Bot auch die üblichen Benutzeroberflächenelemente verwenden, da auch diese viele nützliche Funktionen bieten. Wie [zuvor](~/bot-service-design-principles.md#designing-a-bot) bereits erwähnt sollte Ihr Bot so konzipiert sein, dass er ein Problem eines Benutzers auf die schnellste und einfachste Weise löst. Sie sollten nicht als erstes Funktionen zum Verstehen natürlicher Sprache integrieren, da dies häufig unnötig ist und den Vorgang verkompliziert.

> [!TIP]
> Setzen Sie stattdessen zunächst die mindestens erforderlichen UI-Steuerelemente ein, mit denen der Bot das Problem des Benutzers lösen kann, und fügen Sie andere Elemente später hinzu, wenn diese Steuerelemente nicht mehr ausreichen.


## <a name="text-and-natural-language-understanding"></a>Text und Verstehen natürlicher Sprache

Ein Bot kann **Texteingaben** von Benutzern akzeptieren und versuchen, diese Eingaben mithilfe von regulären Ausdrücken für Übereinstimmungen oder APIs zum **Verstehen natürlicher Sprache** (z.B. <a href="https://www.luis.ai" target="_blank">LUIS</a>) zu analysieren. Es ist vom Typ der Benutzereingabe abhängig, ob sich APIs zum Verstehen natürlicher Sprache eignen oder nicht.

Manchmal **beantwortet ein Benutzer ggf. eine bestimmte Frage**. Wenn der Bot beispielsweise die Frage „Wie heißen Sie?“ stellt, kann der Benutzer entweder nur seinen Namen („John“) eingeben oder mit dem Satz „Ich heiße John.“ antworten.

Wenn genaue Fragen gestellt werden, schränkt das die Anzahl der möglichen Antworten an den Bot ein. Dadurch muss die Logik weniger komplex ausfallen, die nötig ist, um die Antwort zu analysieren und zu verstehen. Nehmen wir z.B. die Frage „Wie geht es Ihnen?“, auf die es eine Vielzahl möglicher Antworten gibt. Es ist sehr kompliziert, die verschiedenen möglichen Antworten auf eine solche Frage verstehen zu wollen.

Im Gegensatz dazu gibt es auf genauere Fragen wie „Haben Sie Schmerzen – Ja/Nein“ und „Wo haben Sie Schmerzen? – Brust/Kopf/Arm/Bein“ auch weniger Antwortmöglichkeiten, die ein Bot analysieren und verstehen muss, sodass es nicht nötig ist, Funktionen zum Verstehen natürlicher Sprache zu implementieren. 

> [!TIP]
> Sie sollten, wenn möglich, immer genaue Fragen stellen, bei denen es nicht notwendig ist, Funktionen zum Verstehen natürlicher Sprache zu integrieren, um die Antwort zu analysieren. Dadurch muss ihr Bot weniger komplex sein, und es ist wahrscheinlicher, dass er den Benutzer versteht.

  
Teilweise kann es auch dazu kommen, dass ein Benutzer einen **genauen Befehl eingibt**. Beispielsweise kann ein DevOps-Bot, über den Entwickler virtuelle Computer verwalten, so konzipiert sein, dass er genaue Befehle wie „/STOP VM XYZ“ oder „/START VM XYZ“ akzeptiert. Wenn ein Bot Befehle wie diese akzeptiert, wird so die Benutzerfreundlichkeit verbessert, da die Syntax einfach und das erwartete Ergebnis jedes Befehls klar definiert ist. Außerdem müssen dem Bot keine Funktionen zum Verstehen natürlicher Sprache hinzugefügt werden, da die Eingabe des Benutzers ganz leicht mithilfe von regulären Ausdrücken analysiert werden kann. 

> [!TIP]
> Wenn Sie einen Bot so konzipieren, dass dieser genaue Befehle vom Benutzer verlangt, verbessert das die Benutzerfreundlichkeit, sodass keine Funktionen zum Verstehen natürlicher Sprache erforderlich sind.

  
Benutzer können Bots, die auf einer *Wissensdatenbank* basieren, oder *Frage-Antwort*-Bots **allgemeine Fragen stellen**. Stellen Sie sich beispielsweise einen Bot vor, der auf der Grundlage der Inhalte von Tausenden von Dokumenten Fragen beantworten kann. Bei <a href="https://qnamaker.ai" target="_blank">QnA Maker</a> und <a href="https://azure.microsoft.com/en-us/services/search/" target="_blank">Azure Search</a> handelt es sich um Technologien, die genau für ein solches Szenario konzipiert sind. Weitere Informationen finden Sie unter [Design knowledge bots (Entwerfen von Bots, die auf Wissensdatenbanken basieren)](bot-service-design-pattern-knowledge-base.md).

> [!TIP]
> Wenn Sie einen Bot entwerfen, der Fragen auf der Grundlage von (un)strukturierten Daten aus Datenbanken, Dokumenten oder von Webseiten beantwortet, sollten Sie Technologien verwenden, die auf dieses Szenario ausgerichtet sind. Sie sollten in diesem Zusammenhang nicht versuchen, mit Funktionen zum Verstehen natürlicher Sprache zu arbeiten.

  
In anderen Szenarios kann es sein, dass ein Benutzer **einfache Anforderungen eingibt, die auf natürlicher Sprache basieren**. Solche Anforderungen können z.B. wie folgt lauten: „Ich will eine Pizza mit Salami.“ oder „Gibt es im Umkreis von 3 Meilen ein vegetarisches Restaurant, das noch geöffnet ist?“. APIs zum Verstehen natürlicher Sprache (z.B. [LUIS.ai](https://www.luis.ai)) eignen sich gut in solchen Szenarios. 

Wenn Sie diese APIs verwenden, kann Ihr Bot die wichtigsten Komponenten des vom Benutzer eingegeben Texts extrahieren und so ermitteln, welche Absichten dieser hat. Wenn Sie Funktionen zum Verstehen natürlicher Sprache in Ihren Bot implementieren, sollten Sie im Hinblick auf die in der Benutzereingabe enthaltenen Details realistisch sein. 

![Beispieleingaben](./media/bot-service-design-user-experience/buy-house.png)

> [!TIP]
> Wenn Sie Sprachmodelle erstellen, sollten Sie nicht davon ausgehen, dass Benutzer bereits in ihrer ersten Abfrage alle erforderlichen Informationen angeben. Konzipieren Sie Ihren Bot so, dass dieser genau die Informationen anfordert, die er benötigt, indem er dem Benutzer genaue Fragen stellt, ggf. sogar mehrere. 

  
## <a name="speech"></a>Spracheingabe

Ein Bot kann **Spracheingaben** bzw. -ausgaben verwenden, um mit den Benutzern zu kommunizieren. Wenn ein Bot darauf ausgerichtet ist, Geräte ohne Tastatur oder Bildschirm zu unterstützen, kann nur mithilfe von Spracheingaben bzw- ausgaben mit dem Benutzer kommuniziert werden. 

## <a name="choosing-between-rich-user-controls-text-and-natural-language-and-speech"></a>Wählen zwischen umfangreichen Benutzersteuerelementen, Text und natürlicher Sprache und Spracheingaben

Genauso wie Menschen bei der Kommunikation verschiedene Gesten, Stimmhöhen und Symbole verwenden, kann ein Bot mithilfe von umfangreichen Benutzersteuerelementen, Text (teilweise einschließlich natürlicher Sprache) und Spracheingaben mit Benutzern kommunizieren. Diese Kommunikationsmethoden können alle zusammen verwendet werden. Sie müssen sich nicht für eine entscheiden. 

Angenommen, es handelt sich um einen „Kochbot“, der Benutzer beim Kochen unterstützt, indem er möglicherweise Anweisungen erteilt, indem er ein Video abspielt oder verschiedene Bilder anzeigt, um zu erklären, wie der Benutzer vorgehen muss. Manche Benutzer bevorzugen es, mithilfe von Spracheingaben zur nächsten Seite des Rezepts zu blättern oder dem Bot Fragen zu stellen. Andere berühren lieber den Gerätebildschirm, anstatt mithilfe von Spracheingaben mit dem Bot zu kommunizieren. Wenn Sie Ihren Bot erstellen, sollten Sie Benutzeroberflächenelemente in diesen integrieren, die die verschiedenen Möglichkeiten unterstützen, über die Benutzer mit dem Bot interagieren können. Dabei sollten die genauen Anwendungsfälle berücksichtigt werden, die unterstützt werden sollen. 

