---
title: Entwerfen von Wissens-Bots | Microsoft-Dokumentation
description: Lernen Sie verschiedene Möglichkeiten zum Entwerfen eines Wissens-Bots kennen, der als Reaktion auf eine Eingabe oder Abfrage des Benutzers Informationen sucht und zurückgibt.
author: matvelloso
ms.author: mateusv
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: cognitive-services
ms.date: 12/13/2017
ms.openlocfilehash: 6820815f251c38c59391f1e0e7719e52a375ed48
ms.sourcegitcommit: b15cf37afc4f57d13ca6636d4227433809562f8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/11/2019
ms.locfileid: "54224905"
---
# <a name="design-knowledge-bots"></a>Entwerfen von Wissens-Bots

Ein Wissens-Bot kann so entworfen werden, dass er Informationen zu praktisch jedem Thema bereitstellt. Beispielsweise kann ein Wissens-Bot Fragen zu Veranstaltungen beantworten, wie z.B. „Welche Veranstaltungen zu Bots finden bei dieser Konferenz statt?“, „Wann gibt es die nächste Reggae-Show?“ oder „Wer ist Tame Impala?“ Ein anderer Bot kann IT-bezogene Fragen beantworten, z.B. „Wie aktualisiere ich mein Betriebssystem?“ oder „Wo kann ich mein Kennwort zurücksetzen?“ Noch ein anderer Bot kann Fragen zu Kontakten beantworten, z.B. „Wer ist Klaus Buzov?“ oder „Wie lautet die E-Mail-Adresse von Laura Becker?“ 

Unabhängig vom jeweiligen Anwendungsfall, für den ein Wissens-Bot konzipiert ist, ist das grundlegende Ziel immer das gleiche: Suchen und Zurückgeben der vom Benutzer angeforderten Informationen durch Nutzung von Datenbeständen, z.B. relationaler Daten in einer SQL-Datenbank, JSON-Daten in einem nicht relationalen Speicher oder PDF-Dateien in einem Dokumentspeicher. 

## <a name="search"></a>Suchen,

Die Suchfunktion kann ein wertvolles Tool in einem Bot sein. 

Mithilfe einer „Fuzzysuche“ kann ein Bot Informationen zurückgeben, die wahrscheinlich relevant für die Frage des Benutzers sind, ohne dass vom Benutzer eine genau Eingabe erforderlich ist. Wenn der Benutzer beispielsweise einen Wissens-Bot für Musik nach Informationen zu „Impala“ (anstelle von „Tame Impala“) fragt, kann der Bot mit Informationen antworten, die höchstwahrscheinlich für diese Eingabe relevant sind.

![Dialogstruktur](~/media/bot-service-design-pattern-knowledge-base/fuzzySearch2.png)

Suchbewertungen geben den Grad der Zuverlässigkeit für die Ergebnisse einer bestimmten Suche an und ermöglichen es einem Bot, die Ergebnisse entsprechend zu sortieren oder sogar die Kommunikation auf Grundlage des Zuverlässigkeitsgrads anzupassen. Wenn der Zuverlässigkeitsgrad hoch ist, kann die Antwort des Bots z.B. folgendermaßen lauten: „Hier ist die Veranstaltung, die Ihrer Suche am besten entspricht:“

![Dialogstruktur](~/media/bot-service-design-pattern-knowledge-base/searchScore2.png)

Wenn der Zuverlässigkeitsgrad niedrig ist, kann die Antwort des Bots lauten: „Hmm... haben Sie nach einer dieser Veranstaltungen gesucht?“

![Dialogstruktur](~/media/bot-service-design-pattern-knowledge-base/searchScore1.png)

### <a name="using-search-to-guide-a-conversation"></a>Verwenden der Suche zum Führen einer Konversation

Wenn Ihr Ziel für das Erstellen eines Bots das Bereitstellen von Basisfunktionalität einer Suchmaschine ist, benötigen Sie vielleicht gar keinen Bot. Was bietet eine Konversationsoberfläche, das eine typische Suchmaschine in einem Webbrowser den Benutzern nicht bieten kann? 

Wissens-Bots sind in der Regel dann am effektivsten, wenn sie zum Führen einer Konversation entworfen werden. Eine Konversation besteht aus einem wechselseitigen Austausch zwischen Benutzer und Bot, der dem Bot Gelegenheit bietet, klärende Fragen zu stellen, Optionen anzubieten und Ergebnisse zu prüfen, und das auf eine Weise, die bei einer einfachen Suche nicht möglich ist. Beispielsweise führt der folgende Bot einen Benutzer durch eine Konversation, mit der ein Dataset facettiert und gefiltert wird, bis die Informationen gefunden sind, nach denen der Benutzer sucht.

![Dialogstruktur](~/media/bot-service-design-pattern-knowledge-base/guidedConvo1.png)

![Dialogstruktur](~/media/bot-service-design-pattern-knowledge-base/guidedConvo2.png)

![Dialogstruktur](~/media/bot-service-design-pattern-knowledge-base/guidedConvo3.png)

![Dialogstruktur](~/media/bot-service-design-pattern-knowledge-base/guidedConvo4.png)

Durch Verarbeitung der Benutzereingabe in jedem Schritt und Anzeige der jeweiligen Optionen führt der Bot den Benutzer zu den gesuchten Informationen. Nachdem der Bot diese Informationen bereitgestellt hat, kann er auch Anleitungen dazu geben, wie in Zukunft auf effizientere Weise nach ähnlichen Informationen gesucht werden kann. 

![Dialogstruktur](~/media/bot-service-design-pattern-knowledge-base/Training.png)

### <a name="azure-search"></a>Azure Search

Mithilfe von <a href="https://azure.microsoft.com/en-us/services/search/" target="_blank">Azure Search</a> können Sie einen effizienten Suchindex erstellen, der von einem Bot mühelos durchsucht, facettiert und gefiltert werden kann. Nehmen wir als Beispiel einen Suchindex, der über das Azure-Portal erstellt wurde.

![Dialogstruktur](~/media/bot-service-design-pattern-knowledge-base/search3.PNG)

Sie möchten auf alle Eigenschaften des Datenspeichers zugreifen können und legen deshalb jede Eigenschaft als „abrufbar“ fest. Sie möchten Musiker anhand des Namens finden können und legen deshalb die Eigenschaft **Name** als „suchbar“ fest. Zum Schluss möchten Sie Epochen von Musikern facettieren und filtern können und kennzeichnen daher die Eigenschaft **Eras** (Epochen) als „facettenreich“ und „filterbar“. 

Durch Facettieren werden die Werte ermittelt, die im Datenspeicher für eine bestimmte Eigenschaft vorhanden sind, zusammen mit der Größenordnung der einzelnen Werte. Der folgende Screenshot zeigt beispielsweise, dass fünf verschiedene Epochen im Datenspeicher vorhanden sind:

![Dialogstruktur](~/media/bot-service-design-pattern-knowledge-base/facet.png)

Durch das Filtern wiederum werden nur die angegebenen Instanzen einer bestimmten Eigenschaft ausgewählt. Sie könnten die Ergebnisse oben z.B. so filtern, dass nur die Elemente enthalten sind, bei denen **Era** den Wert „Romantic“ aufweist. 

> [!NOTE]
> Sehen Sie sich <a href="https://github.com/ryanvolum/AzureSearchBot" target="_blank">einen Beispielbot</a> als ein umfassendes Beispiel für einen Wissens-Bots an, der mit Azure Document DB, Azure Search und Microsoft Bot Framework erstellt wurde.
> 
> Der Einfachheit halber enthält das obige Beispiel einen Suchindex, der über das Azure-Portal erstellt wurde. 
> Indizes können auch programmgesteuert erstellt werden.

## <a name="qna-maker"></a>QnA Maker

Einige Wissens-Bots haben einfach zum Ziel, häufig gestellte Fragen (FAQs) zu beantworten. 
<a href="https://www.microsoft.com/cognitive-services/en-us/qnamaker" target="_blank">QnA Maker</a> ist ein leistungsfähiges Tool, das speziell für diesen Anwendungsfall entworfen wurde. QnA Maker verfügt über die integrierte Möglichkeit, Fragen und Antworten von einer vorhandenen Website mit häufig gestellten Fragen zu extrahieren, und ermöglicht Ihnen außerdem, eine eigene benutzerdefinierte Liste von Fragen und Antworten manuell zu konfigurieren. QnA Maker kann natürliche Sprache verarbeiten, sodass auch Antworten auf Fragen bereitgestellt werden können, die etwas anders formuliert sind als erwartet. Die Möglichkeit zum Verstehen der Sprachsemantik ist jedoch nicht vorhanden. Das Tool kann beispielsweise nicht feststellen, dass ein Welpe eine Art von Hund ist. 

Mithilfe der QnA Maker-Weboberfläche können Sie eine Wissensdatenbank mit drei Frage-und-Antwort-Paaren konfigurieren: 

![Dialogstruktur](~/media/bot-service-design-pattern-knowledge-base/KnowledgeBaseConfig.png)

Anschließend können Sie diese testen, indem Sie eine Reihe von Fragen stellen: 

![Dialogstruktur](~/media/bot-service-design-pattern-knowledge-base/exampleQnAConvo.png)

Der Bot beantwortet die Fragen korrekt, die den in der Wissensdatenbank konfigurierten Fragen direkt zugeordnet werden. Die Antwort auf die Frage „Kann ich meinen Tee mitbringen?“ ist jedoch falsch, da diese Frage von der Struktur her der Frage „Kann ich meinen Wodka mitbringen“ am ähnlichsten ist. QnA Maker gibt eine falsche Antwort, weil die Bedeutung von Wörtern grundsätzlich nicht verstanden wird. Das Tool weiß nicht, dass „Tee“ ein alkoholfreies Getränk ist. Daher lautet die Antwort „Alkohol ist nicht erlaubt“.

> [!TIP]
> Erstellen Sie Frage-und-Antwort-Paare, und testen und trainieren Sie dann Ihren Bot mithilfe der Schaltfläche „Überprüfen“ unter der Konversation, um eine alternative Antwort für jede falsch gegebene Antwort auszuwählen. 

## <a name="luis"></a>LUIS

Einige Wissens-Bots erfordern Funktionen zur Verarbeitung natürlicher Sprache (Natural Language Processing, NLP), damit sie Nachrichten eines Benutzers analysieren und so die Absicht des Benutzers ermitteln können. 
[Language Understanding (LUIS)](https://www.luis.ai) bietet eine schnelle und effektive Möglichkeit, um Bots diese NLP-Funktionen hinzuzufügen. Mit LUIS können Sie vorhandene, vordefinierte Modelle aus Bing und Cortana verwenden, wenn diese Ihren Anforderungen entsprechen, und auch eigene spezielle Modelle erstellen. 

Beim Arbeiten mit großen Datasets, ist es nicht unbedingt sinnvoll, ein NLP-Modell mit jeder Variation einer Entität zu trainieren. In einem Bot zur Wiedergabe von Musik kann ein Benutzer beispielsweise „Spiel Reggae“, „Spiel Bob Marley“ oder „Spiel One Love“ als Nachricht angeben. Obwohl ein Bot jede dieser Nachrichten der Absicht „PlayMusic“ zuordnen kann, ohne mit jedem Künstler, Genre und Titelnamen trainiert zu sein, könnte ein NLP-Modell nicht feststellen, ob die Entität ein Genre, Künstler oder Titel ist. Durch Verwendung eines NLP-Modells zum Erkennen der generischen Entität vom Typ „Musik“ kann der Bot den Datenspeicher nach dieser Entität durchsuchen und von dort aus fortfahren. 

## <a name="combining-search-qna-maker-andor-luis"></a>Kombinieren von Search, QnA Maker und/oder LUIS

Search, QnA Maker und LUIS sind jeweils für sich leistungsstarke Tools, doch können sie auch kombiniert werden, um Wissens-Bots zu erstellen, die über mehrere dieser Funktionen verfügen.

### <a name="luis-and-search"></a>LUIS und Search

In dem Botbeispiel für Musikfestivals [weiter oben](#search) führt der Bot die Konversation durch Anzeigen von Schaltflächen für die Auflistung. Dieser Bot könnte aber auch mithilfe von LUIS natürliche Sprache verstehen, damit Absichten und Entitäten in Fragen erkannt werden, wie z.B. „Welche Art von Musik macht Romit Girdhar?“ Der Bot könnte dann eine Suche in einem Azure Search-Index nach dem Namen dieses Musikers ausführen. 
 
Es wäre nicht machbar, das Modell mit jedem möglichen Musikernamen zu trainieren, da es so viele mögliche Werte gibt, doch könnten Sie genügend repräsentative Beispiele für LUIS angeben, damit die vorliegende Entität richtig erkannt wird.  Sie könnten beispielsweise Ihr Modell trainieren, indem Sie Beispiele für Musiker angeben: 

![Dialogstruktur](~/media/bot-service-design-pattern-knowledge-base/answerGenre.png)
![Dialogstruktur](~/media/bot-service-design-pattern-knowledge-base/answerGenreOneWord.png)

Wenn Sie dieses Modell mit neuen Äußerungen testen, wie z.B. „Welche Art von Musik machen die Beatles?“, kann LUIS erfolgreich die Absicht „AnswerGenre“ und die Entität „die Beatles“ erkennen. Wenn Sie aber eine längere Frage stellen, z.B. „Welche Art von Musik macht The Devil Makes Three“ erkennt LUIS „the devil“ als Entität.

![Dialogstruktur](~/media/bot-service-design-pattern-knowledge-base/devilMakesThreeScore.png)

Durch Trainieren des Modells mit Beispielentitäten, die für das zugrunde liegende Dataset repräsentativ sind, können Sie die Genauigkeit des Sprachverständnisses bei Ihrem Bot erhöhen. 

> [!TIP]
> Im Allgemeinen ist es besser für das Modell, sich durch Erkennen zu vieler Wörter bei der Entitätserkennung zu irren (z.B. „Bitte Niklas Gasper“ bei der Äußerung „Bitte Niklas Gasper anrufen“ erkennen), als wenn zu wenig Wörter erkannt werden (z.B. „Niklas“ bei der Äußerung „Bitte Niklas Gasper anrufen“). Der Suchindex ignoriert nicht relevante Wörter wie „Bitte“ im Ausdruck „Bitte Niklas Gasper“. 

### <a name="luis-and-qna-maker"></a>LUIS und QnA Maker

Bei einigen Wissens-Bots wird möglicherweise QnA Maker verwendet, um grundlegende Fragen zu beantworten, in Kombination mit LUIS, um Absichten zu ermitteln, Entitäten zu extrahieren und ausführlichere Dialoge aufzurufen. Nehmen wir als Beispiel einen einfachen Bot für ein IT-Helpdesk. Bei diesem Bot wird QnA Maker verwendet, um grundlegende Fragen zu Windows oder Outlook zu beantworten, doch müssen möglicherweise auch Szenarien wie das Zurücksetzen von Kennwörtern ermöglicht werden, die eine Absichtserkennung und eine wechselseitige Kommunikation zwischen Benutzer und Bot erfordern. Es gibt mehrere Möglichkeiten, wie ein Bot eine Kombination aus LUIS und QnA Maker implementieren kann:

1. Gleichzeitiges Aufrufen von QnA Maker und LUIS und Bereitstellen von Antworten für den Benutzer anhand von Informationen aus dem ersten Tool, das ein Ergebnis mit einem bestimmten Schwellenwert zurückgibt. 
2. Anfängliches Aufrufen von LUIS, und wenn keine Absicht einen bestimmten Schwellenwert erreicht, d.h. eine Absicht vom Typ „None“ ausgelöst wird, Aufrufen von QnA Maker. Alternative: Erstellen einer LUIS-Absicht für QnA Maker und Bereitstellen von QnA-Beispielfragen für das LUIS-Modell, die „QnAIntent“ zugeordnet sind. 
3. Anfängliches Aufrufen von QnA Maker, und wenn keine Antwort einen bestimmten Schwellenwert erreicht, anschließendes Aufrufen von LUIS. 

Das Bot Framework SDK bietet integrierte Unterstützung für LUIS und QnA Maker. Dadurch können Sie mithilfe von LUIS und/oder QnA Maker Dialoge auslösen oder Fragen automatisch beantworten, ohne benutzerdefinierte Aufrufe der beiden Tools implementieren zu müssen. Weitere Informationen finden Sie unter [Verwenden von mehreren LUIS- und QnA-Modellen](https://docs.microsoft.com/en-us/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0).

> [!TIP]
> Wenn Sie eine Kombination von LUIS, QnA Maker und/oder Azure Search implementieren, testen Sie Eingaben mit jedem der Tools, um den Schwellenwert für die einzelnen Modelle zu bestimmen. LUIS, QnA Maker und Azure Search generieren jeweils Bewertungen anhand verschiedener Kriterien, sodass die mit diesen Tools generierten Bewertungen nicht direkt vergleichbar sind. Darüber hinaus werden Bewertungen in LUIS und QnA Maker normalisiert. Eine bestimmte Bewertung kann in einem LUIS-Modell als „gut“, in einem anderen jedoch ganz anders angesehen werden. 

## <a name="sample-code"></a>Beispielcode

- Ein Beispiel, das die Erstellung eines einfachen Wissens-Bots mit dem Bot Framework SDK für .NET veranschaulicht, finden Sie auf GitHub im <a href="https://aka.ms/qna-with-appinsights" target="_blank">Beispiel für einen Wissens-Bot</a>. 
<!-- TODO: Do not have a current bot sample to work with this
- For a sample that shows how to create more complex knowledge bots using the Bot Framework SDK for .NET, see the <a href="https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/demo-Search" target="_blank">Search-powered Bots sample</a> in GitHub.
-->

[qnamakerTemplate]: https://docs.botframework.com/en-us/azure-bot-service/templates/qnamaker/#navtitle
