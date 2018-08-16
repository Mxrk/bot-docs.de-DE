---
title: Hinzufügen von künstlicher Intelligenz zu Bots mit Cognitive Services | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Ihren Bots mit Microsoft Cognitive Services künstliche Intelligenz hinzufügen können, um sie benutzerfreundlicher und ansprechender zu gestalten.
keywords: Sprachverständnis, Extraktion von Wissen, Spracherkennung, Websuche
author: RobStand
ms.author: rstand
manager: rstand
ms.topic: article
ms.prod: bot-framework
ms.date: 12/17/2017
ms.openlocfilehash: 2a558694cb96dd199894627146947ae1e7345183
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39304093"
---
# <a name="add-intelligence-to-bots-with-cognitive-services"></a>Hinzufügen von künstlicher Intelligenz zu Bots mit Cognitive Services

Mit Microsoft Cognitive Services haben Sie Zugang zu einer wachsenden Sammlung leistungsfähiger KI-Algorithmen, die von Experten in den Bereichen maschinelles Sehen, Spracherkennung, Verarbeitung natürlicher Sprache, Extraktion von Wissen und Websuche entwickelt wurden. Die Dienste vereinfachen eine Vielzahl von Aufgaben, die auf künstlicher Intelligenz basieren, und bieten Ihnen die Möglichkeit, modernste KI-Technologien mit nur wenigen Codezeilen schnell zu Ihren Bots hinzuzufügen. Die APIs können in die meisten modernen Sprachen und Plattformen integriert werden. Zudem werden die APIs ständig verbessert, lernen hinzu und werden intelligenter, sodass sie immer dem neuesten Stand entsprechen. 

Intelligente Bots reagieren so, als könnten sie die Welt auf gleiche Weise wie Menschen sehen. Sie ermitteln Informationen und extrahieren Wissen aus verschiedenen Quellen, um nützliche Antworten bereitzustellen. Das Beste aber ist, dass sie mit wachsender Erfahrung dazulernen und so ihre Fähigkeiten kontinuierlich verbessern. 

## <a name="language-understanding"></a>Sprachverständnis

Die Interaktion zwischen Benutzern und Bots erfolgt zumeist in freier Form, sodass Bots Sprache auf natürliche Weise und kontextabhängig verstehen müssen. Die Cognitive Service Language-APIs bieten leistungsstarke Sprachmodelle, um festzustellen, was Benutzer wollen, um Konzepte und Entitäten in einem bestimmten Satz zu erkennen, und um schließlich Ihren Bots zu erlauben, mit der entsprechenden Aktion zu reagieren. Die fünf APIs unterstützen verschiedene Textanalysefunktionen wie Rechtschreibprüfung, Stimmungserkennung, Sprachmodellierung sowie Extraktion präziser und umfassender Einblicke aus Text. 

Cognitive Services bietet die folgenden APIs für das Sprachverständnis:

- <a href="https://www.microsoft.com/cognitive-services/en-us/language-understanding-intelligent-service-luis" target="_blank">Language Understanding Intelligent Service (LUIS)</a> kann natürliche Sprache mithilfe vorgefertigter oder vom Benutzer trainierter Sprachmodelle verarbeiten. Weitere Informationen finden Sie unter [Language Understanding für Bots](v4sdk/bot-builder-concept-luis.md).

- Die <a href="https://www.microsoft.com/cognitive-services/en-us/text-analytics-api" target="_blank">Textanalyse-API</a> kann die Stimmung, Schlüsselwörter, Themen und die Sprache aus Text erkennen.

- Die <a href="https://www.microsoft.com/cognitive-services/en-us/bing-spell-check-api" target="_blank">Bing-Rechtschreibprüfungs-API</a> bietet leistungsstarke Funktionen für die Rechtschreibprüfung und kann den Unterschied zwischen Namen, Markennamen und Jargon erkennen.

- Die <a href="https://docs.microsoft.com/en-us/azure/machine-learning/studio/text-analytics-module-tutorial" target ="_blank">Textanalysemodelle in Azure Machine Learning Studio</a> ermöglichen es Ihnen, Textanalysemodelle wie Lemmatisierung und Textvorverarbeitung zu erstellen und zu operationalisieren. Diese Modelle können Ihnen helfen, Probleme beispielsweise bei der Klassifizierung von Dokumenten oder Stimmungsanalyse zu beheben.

Erfahren Sie mehr über [Language Understanding ][language] mit Microsoft Cognitive Services.

## <a name="knowledge-extraction"></a>Extraktion von Wissen

Cognitive Services bietet vier Wissens-APIs, mit denen Sie benannte Entitäten oder Ausdrücken in unstrukturiertem Text erkennen, personalisierte Empfehlungen hinzufügen, Vorschläge für die automatische Vervollständigung basierend auf natürlicher Interpretation von Benutzerabfragen bereitstellen und wissenschaftliche Arbeiten sowie andere Recherchequellen wie einen personalisierten Dienst für häufig gestellte Fragen durchsuchen können.

- Der <a href="https://www.microsoft.com/cognitive-services/en-us/entity-linking-intelligence-service" target="_blank">Entity Linking-Analysedienst</a> versieht unstrukturierten Text mit den relevanten Entitäten, die im Text erwähnt werden. Je nach Kontext kann sich dasselbe Wort oder derselbe Ausdruck auf unterschiedliche Dinge beziehen. Dieser Dienst versteht den Kontext des bereitgestellten Texts und erkennt die einzelnen Entitäten im Text.    

- Der <a href="https://www.microsoft.com/cognitive-services/en-us/knowledge-exploration-service" target="_blank">Knowledge Exploration Service</a> interpretiert Benutzerabfragen in natürlicher Sprache und gibt mit Anmerkungen versehene Interpretationen zurück, die eine umfassende Suche und automatische Vervollständigung durch Vorhersage der Benutzereingabe ermöglicht. Sofortige Vorschläge für die Abfragevervollständigung und vorhersagbare Abfrageoptimierungen basieren auf den eigenen Daten und anwendungsspezifischen Grammatiken, um den Benutzer das Ausführen schneller Abfragen zu ermöglichen.    

- Die <a href="https://www.microsoft.com/cognitive-services/en-us/academic-knowledge-api" target="_blank">Academic Knowledge-API</a> gibt wissenschaftliche Forschungsarbeiten, Autoren, Journale, Konferenzen, Themen und Universitäten aus <a href="https://www.microsoft.com/en-us/research/project/microsoft-academic-graph/" target="_blank">Microsoft Academic Graph</a> zurück. Die Academic Knowledge-API wurde als ein domänenspezifisches Beispiel des Knowledge Exploration Service erstellt und bietet eine Wissensdatenbank über ein diagrammähnliches Dialogfeld mit Suchfunktionen für über mehrere Hundert Millionen foschungsbezogene Entitäten. Suchen Sie nach einem Thema, einem Professor, einer Universität oder einer Konferenz, und die API stellt relevante Veröffentlichungen und zugehörige Entitäten bereit. Die Grammatik unterstützt auch natürliche Abfragen wie „Arbeiten von Michael Jordan zu Machine Learning nach 2010“.

- <a href="https://qnamaker.ai" target="_blank">QnA Maker</a> ist ein kostenloser, benutzerfreundlicher, REST-API- und webbasierter Dienst, mit dem KI für das Beantworten von Benutzerfragen in Form einer natürlichen Konversation trainiert wird. Durch eine optimierte Logik für maschinelles Lernen und die Fähigkeit zur Integration branchenweit führender Sprachverarbeitung führt QnA Maker teilweise strukturierte Daten wie Frage-und-Antwort-Paare in eindeutigen, hilfreichen Antworten zusammen.

Erfahren Sie mehr über die [Extraktion von Wissen][knowledge] mit Microsoft Cognitive Services.

## <a name="speech-recognition-and-conversion"></a>Spracherkennung und -umwandlung

Mithilfe der Spracherkennungs-APIs können Sie erweiterte Spracherkennungsfunktionen zu Ihrem Bot hinzufügen, die branchenweit führende Algorithmen zur Umwandlung von Sprache in Text und Text in Sprache sowie zur Sprechererkennung nutzen. Die Spracherkennungs-APIs verwenden integrierte Sprach- und Akustikmodelle, die eine Vielzahl von Szenarien mit hoher Genauigkeit abdecken. 

Für Anwendungen, die eine weitere Anpassung erfordern, können Sie den Custom Recognition Intelligent Service (CRIS) verwenden. Hiermit können Sie die Sprach- und Akustikmodelle des Spracherkennungsmoduls kalibrieren, indem Sie es genau an das Vokabular der Anwendung oder sogar den Sprachstil Ihrer Benutzer anpassen.

In Cognitive Services stehen drei Spracherkennungs-APIs zum Verarbeiten oder Synthetisieren von Sprache zur Verfügung:

- Die <a href="https://www.microsoft.com/cognitive-services/en-us/speech-api" target="_blank">Bing-Spracheingabe-API</a> bietet Funktionen für die Umwandlung von Sprache in Text und Text in Sprache.
- Mit dem <a href="https://www.microsoft.com/cognitive-services/en-us/custom-recognition-intelligent-service-cris" target="_blank">Custom Recognition Intelligent Service (CRIS)</a> können Sie benutzerdefinierte Spracherkennungsmodelle erstellen, um die Umwandlung von Sprache in Text genau an das Vokabular einer Anwendung oder den Sprachstil eines Benutzers anzupassen.
- Die <a href="https://www.microsoft.com/cognitive-services/en-us/speaker-recognition-api" target="_blank">Sprechererkennungs-API</a> ermöglicht die Identifikation und Überprüfung des Sprechers anhand der Stimme.

Die folgenden Ressourcen bieten zusätzliche Informationen zum Hinzufügen der Spracherkennung zu Ihrem Bot.

* [Übersichtsvideo zu Botkonversationen für Apps](https://channel9.msdn.com/events/Build/2017/P4114)
* [Microsoft.Bot.Client-Bibliothek für UWP- oder Xamarin-Apps](https://aka.ms/BotClient)
* [Beispiel einer Bot-Clientbibliothek](https://aka.ms/BotClientSample)
* [Sprachaktivierter WebChat-Client](https://aka.ms/BFWebChat)

Erfahren Sie mehr über [Spracherkennung und -umwandlung][speech] mit Microsoft Cognitive Services.

## <a name="web-search"></a>Websuche

Mithilfe der Bing-Suche-APIs können Sie Ihren Bots Funktionen für die intelligente Websuche hinzufügen. Mit nur wenigen Codezeilen können Sie auf Milliarden von Webseiten, Bildern, Videos, News und andere Ergebnistypen zugreifen. Sie können die APIs so konfigurieren, dass sie für eine höhere Relevanz Ergebnisse nach geografischem Standort, Markt oder Sprache zurückgeben. Sie können Ihre Suche mithilfe der unterstützten Suchparameter weiter anpassen, z.B. mit „Safesearch“, um nicht jugendfreie Inhalte herauszufiltern, und „Freshness“, um Ergebnisse entsprechend einem bestimmten Datum zurückzugeben.

In Cognitive Services stehen fünf Bing-Suche-APIs bereit.

- Die <a href="https://www.microsoft.com/cognitive-services/en-us/bing-web-search-api" target="_blank">Websuche-API</a> stellt Web-, Bild-, Video-, News- und verwandte Suchergebnisse mit einem einzigen API-Aufruf bereit.

- Die <a href="https://www.microsoft.com/cognitive-services/en-us/bing-image-search-api" target="_blank">Bildersuche-API</a> gibt Bilder als Ergebnisse mit erweiterten Metadaten (vorherrschende Farbe, Bildart usw.) zurück und unterstützt verschiedene Bildfilter zum Anpassen der Ergebnisse.

- Die <a href="https://www.microsoft.com/cognitive-services/en-us/bing-video-search-api" target="_blank">Videosuche-API</a> ruft Videos als Ergebnisse mit umfangreichen Metadaten (Videogröße, Qualität, Preis usw.) und einer Videovorschau ab und unterstützt verschiedene Videofilter zum Anpassen der Ergebnisse.

- Die <a href="https://www.microsoft.com/cognitive-services/en-us/bing-news-search-api" target="_blank">News-Suche-API</a> sucht weltweit nach Nachrichtenartikeln, die Ihrer Suchabfrage entsprechen oder im Internet aktuell beliebt sind.

- Die <a href="https://www.microsoft.com/cognitive-services/en-us/bing-autosuggest-api" target="_blank">Vorschlagssuche-API</a> bietet sofortige Vorschläge für die Abfragevervollständigung, damit die Suchabfrage schneller und mit weniger Eingabeaufwand vervollständigt werden kann. 

Erfahren Sie mehr über die [Websuche][search] mit Microsoft Cognitive Services.

## <a name="image-and-video-understanding"></a>Bild- und Videoanalyse

Die Bildanalyse-APIs bieten den Bots erweiterte Funktionen zur Bild- und Videoanalyse. Moderne Algorithmen ermöglichen es Ihnen, Bilder oder Videos zu verarbeiten und Informationen zurückzuerhalten, die Sie in Aktionen umwandeln können. Sie können diese APIs beispielsweise verwenden, um Objekte, Gesichter von Menschen, Alter, Geschlecht oder sogar Gefühle zu erkennen. 

Die Bildanalyse-APIs unterstützen eine Vielzahl von Funktionen zur Bildanalyse. Sie können nicht jugendfreie oder anstößige Inhalte erkennen, Akzentfarben einschätzen, Inhalte von Bildern kategorisieren, eine optische Zeichenerkennung ausführen und ein Bild in vollständigen englischen Sätzen beschreiben. Die Bildanalyse-APIs unterstützen auch verschiedene Funktionen zur Bild- und Videoverarbeitung, z.B. das intelligente Generieren von Miniaturansichten von Bildern oder Videos oder das Stabilisieren der Ausgabe eines Videos.

Cognitive Services bietet vier APIs, die Sie zum Verarbeiten von Bildern oder Videos verwenden können:

- Die <a href="https://www.microsoft.com/cognitive-services/en-us/computer-vision-api" target="_blank">Maschinelles Sehen-API</a> extrahiert umfassende Informationen zu Bildern (z.B. Objekte oder Personen), stellt fest, ob das Bild nicht jugendfreie oder anstößige Inhalte aufweist, und verarbeitet Text in Bildern (mithilfe von OCR).

- Die <a href="https://www.microsoft.com/cognitive-services/en-us/emotion-api" target="_blank">Emotionen-API</a> analysiert menschliche Gesichter und erkennt deren Emotionen in acht möglichen Kategorien menschlicher Emotionen.

- Die <a href="https://www.microsoft.com/cognitive-services/en-us/face-api" target="_blank">Gesichtserkennungs-API</a> erkennt menschliche Gesichter, vergleicht diese mit ähnlichen Gesichtern und kann sogar Personen anhand ihrer äußerlichen Ähnlichkeit in Gruppen organisieren.

- Die <a href="https://www.microsoft.com/cognitive-services/en-us/video-api" target="_blank">Video-API</a> analysiert und verarbeitet Videos zum Stabilisieren der Videoausgabe, erkennt Bewegungen, verfolgt Gesichter und kann eine Übersicht in Form von bewegten Miniaturansichten des Videos generieren.

Erfahren Sie mehr über die [Bild- und Videoanalyse][vision] mit Microsoft Cognitive Services.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

Eine umfassende Dokumentation zu jedem Produkt und die entsprechenden API-Referenzen finden Sie in der <a href="https://docs.microsoft.com/azure/cognitive-services" target="_blank">Dokumentation zu Cognitive Services</a>.

[language]: https://docs.microsoft.com/en-us/azure/cognitive-services/luis/home
[search]: https://docs.microsoft.com/en-us/azure/cognitive-services/bing-web-search/search-the-web
[vision]: https://docs.microsoft.com/en-us/azure/cognitive-services/computer-vision/home
[knowledge]: https://docs.microsoft.com/en-us/azure/cognitive-services/kes/overview
[speech]: https://docs.microsoft.com/en-us/azure/cognitive-services/speech/home
[location]: https://docs.microsoft.com/en-us/azure/cognitive-services/
