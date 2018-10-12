---
title: Ausführliche Übersicht über die Vorlage für den Bot für Unternehmen | Microsoft-Dokumentation
description: Hier finden Sie Informationen zu den Designentscheidungen im Zusammenhang mit der Vorlage für den Bot für Unternehmen.
author: darrenj
ms.author: darrenj
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 09/18/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 6f295794ca7d3cc17688337e70df2a52cdb665ed
ms.sourcegitcommit: 87b5b0ca9b0d5e028ece9f7cc4948c5507062c2b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2018
ms.locfileid: "47029849"
---
# <a name="enterprise-template---detailed-overview"></a>Unternehmensvorlage: ausführliche Übersicht

> [!NOTE]
> Dieses Thema gilt für die SDK-Version v4. 

Die Vorlage für den Bot für Unternehmen beinhaltet eine Reihe von Best Practices, die wir im Rahmen der Erstellung von Konversationsumgebungen ermittelt haben, und automatisiert die Integration von Komponenten, die nach unserer Erfahrung für Azure Bot Service-Entwickler besonders hilfreich sind. Dieser Abschnitt enthält einige Hintergrundinformationen zu zentralen Entscheidungen, um die Gründe für die Funktionsweise der Vorlage zu erläutern.

## <a name="introduction-card"></a>Einführungskarte

Ein zentrales Problem vieler Konversationsumgebungen ist, dass Endbenutzer zunächst nicht so recht wissen, was sie tun sollen. Das führt dann zu allgemeinen Fragen, die der Bot möglicherweise nicht optimal beantworten kann. Der erste Eindruck ist jedoch sehr wichtig! Eine Einführungskarte ermöglicht es, den Endbenutzer mit den Funktionen des Bots vertraut zu machen und erste Fragen vorzuschlagen, um dem Benutzer den Einstieg zu erleichtern. Außerdem können Sie hier auch sehr gut die Persönlichkeit Ihres Bots vorstellen.

Ihnen steht standardmäßig eine einfache Einführungskarte zur Verfügung, die Sie nach Bedarf anpassen können.

## <a name="basic-language-understanding-luis-intents"></a>Grundlegendes Sprachverständnis (LUIS) zur Erkennung von Absichten

Jeder Bot muss über ein grundlegendes Sprachverständnis für Konversationen verfügen. Kein Bot sollte beispielsweise Probleme mit einer Begrüßung haben. Normalerweise müssen Entwickler diese Grundabsichten erstellen und erste Trainingsdaten bereitstellen. Die Vorlage für den Bot für Unternehmen stellt exemplarische LU-Dateien bereit, um Ihnen den Einstieg zu erleichtern. Dadurch müssen Sie diese nicht für jedes Projekt erstellen, und Ihnen stehen bereits standardmäßig grundlegende Funktionen zur Verfügung.

Die LU-Dateien umfassen folgenden Absichten für Englisch, Französisch, Italienisch, Deutsch und Spanisch:

> Begrüßung, Hilfe, Abbruch, Neustart, Eskalierung, Bestätigungen (ja/nein/mehr), Weiter, Verabschiedung

Das Format [LU](https://github.com/Microsoft/botbuilder-tools/blob/master/packages/Ludown/docs/lu-file-format.md) ist mit MarkDown vergleichbar und ermöglicht eine problemlose Anpassung und Quellcodeverwaltung. Die LU-Dateien werden anschließend mithilfe des Tools [LuDown](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Ludown) in LUIS-Modelle konvertiert, die dann über das Portal oder mithilfe des entsprechenden [LUIS](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUIS)-Befehlszeilentools (CLI) in Ihrem LUIS-Abonnement veröffentlicht werden können.

## <a name="content-moderator"></a>Content Moderator

Content Moderator ermöglicht die Erkennung potenzieller Obszönitäten und unterstützt Sie bei der Suche nach personenbezogenen Informationen (Personally Identifiable Information, PII). Durch die Integration dieses Features kann ein Bot entsprechend reagieren, wenn ein Benutzer Obszönitäten verwendet oder personenbezogene Informationen preisgibt. So kann sich der Bot etwa entschuldigen und das Gespräch an einen menschlichen Gesprächspartner übergeben oder die Speicherung von Telemetriedaten unterbinden, wenn personenbezogene Informationen erkannt wurden.

Texte und Oberflächen werden mithilfe einer bereitgestellten Middlewarekomponente über ein Element vom Typ ```TextModeratorResult``` des TurnState-Objekts überprüft.

## <a name="telemetry"></a>Telemetrie

Die Bereitstellung von Erkenntnissen zur Benutzerinteraktion Ihres Bots hat sich als äußerst nützlich erwiesen. Anhand dieser Erkenntnisse können Sie nachvollziehen, auf welchen Ebenen die Benutzerinteraktion stattfindet, welche Features des Bots verwendet werden (Absichten) sowie welche gestellten Fragen der Bot nicht beantworten kann. Dies gibt Aufschluss über mögliche Wissenslücken des Bots, die etwa mithilfe neuer QnAMaker-Artikel geschlossen werden können.

Die Integration von Application Insights liefert standardmäßig hilfreiche betriebsbezogene oder technische Erkenntnisse, kann aber auch zur Erfassung spezifischer botbezogener Ereignisse verwendet werden (gesendete und empfangene Nachrichten in Kombination mit LUIS und QnAMaker-Vorgängen).

Telemetriedaten auf Botebene sind untrennbar mit technischen und betriebsbezogenen Telemetriedaten verbunden und ermöglichen es Ihnen, zu ermitteln, wie eine bestimmte Benutzerfrage beantwortet wurde (und umgekehrt).

Mithilfe einer Kombination aus einer Middlewarekomponente und einer Wrapperklasse um die SDK-Klassen „QnAMaker“ und „LuisRecognizer“ können Sie elegant einen konsistenten Satz von Ereignissen erfassen. Diese konsistenten Ereignisse können dann von den Application Insights-Tools sowie von Tools wie Power BI genutzt werden.

Bei jedem Projekt, das mit der Vorlage für den Bot für Unternehmen erstellt wurde, steht ein exemplarisches Power BI-Dashboard zur Verfügung. Weitere Informationen finden Sie im Abschnitt [Power BI](bot-builder-enterprise-template-powerbi.md).

## <a name="dispatcher"></a>Dispatcher

Die Nutzung von Sprachverständnis (LUIS) und QnAMaker hat sich bereits in den ersten Konversationsumgebungen bewährt. LUIS wurde mit Aufgaben trainiert, die Ihr Bot für einen Endbenutzer erledigen kann, während QnAMaker eher mit allgemeinen Informationen trainiert wurde.

Alle eingehenden Äußerungen (Fragen) wurden zur Analyse an LUIS weitergeleitet. Wurde die Absicht einer bestimmten Äußerung nicht erkannt, wurde sie mit der Absicht „None“ markiert. Anschließend wurde mithilfe von QnAMaker versucht, eine Antwort für den Endbenutzer zu finden.

Dieses Muster funktioniert zwar gut, es gab jedoch zwei zentrale Problemszenarien:

- Leichte Überschneidungen von Äußerungen im LUIS-Modell und in QnAMaker führten ggf. zu Fällen mit merkwürdigem Verhalten, in denen LUIS versucht, eine Frage zu verarbeiten, obwohl diese eigentlich an QnAMaker weitergeleitet werden sollte.
- In Fällen mit mehreren LUIS-Modellen muss ein Bot die einzelnen Modelle aufrufen und einen Vergleich der Absichtsauswertung durchführen, um zu ermitteln, wohin eine bestimmte Äußerung gesendet werden soll. Aufgrund einer fehlenden Baseline war der modellübergreifende Wertevergleich nicht effektiv und beeinträchtige die Benutzerfreundlichkeit.

[Dispatcher](https://docs.microsoft.com/en-us/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&tabs=csaddref%2Ccsbotconfig) löst diese Probleme auf elegante Weise: Das Tool extrahiert Äußerungen aus den einzelnen konfigurierten LUIS-Modellen und Fragen von QnAMaker und erstellt ein LUIS-Modell mit zentraler Verteilung.

So kann ein Bot schnell ermitteln, welches LUIS-Modell bzw. welche Komponente für die Verarbeitung einer bestimmten Äußerung geeignet ist. Außerdem wird dadurch sichergestellt, dass QnAMaker-Daten auf der obersten Ebene der Absichtsverarbeitung berücksichtigt werden (und nicht wie zuvor nur die Absicht „None“).

Darüber hinaus ermöglicht dieses Dispatch-Tool eine Auswertung, die vor der Bereitstellung auf Unklarheiten und Überschneidungen zwischen LUIS-Modellen und QnAMaker-Wissensdatenbanken aufmerksam macht.

Dispatcher ist eine zentrale Komponente jedes Projekts, das unter Verwendung der Vorlage für den Bot für Unternehmen erstellt wird. Das Dispatchmodell wird innerhalb der Klasse `MainDialog` verwendet, um zu ermitteln, ob es sich bei dem Ziel um ein LUIS-Modell oder um QnA handelt. Im Fall von LUIS wird das sekundäre LUIS-Modell aufgerufen, das dann wie gewohnt die Absicht und Entitäten zurückgibt.

## <a name="qnamaker"></a>QnAMaker

Mit [QnAMaker](https://www.qnamaker.ai/) können Benutzer ohne Entwicklungserfahrung allgemeine Informationen in Form von Frage-und-Antwort-Paaren zusammenstellen. Diese Informationen können aus FAQ-Datenquellen und Produkthandbüchern sowie interaktiv innerhalb des QnaMaker-Portals importiert werden.

Einen Beispielsatz von QnA-Einträgen im Dateiformat [LU](https://github.com/Microsoft/botbuilder-tools/blob/master/packages/Ludown/docs/lu-file-format.md) finden Sie im QnA-Ordner „CogSvcModels“. Anschließend wird mithilfe des Bereitstellungsskripts unter Verwendung von [LuDown](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Ludown) eine QnAMaker-JSON-Datei erstellt, die vom CLI-Tool [QnAMaker](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/QnAMaker) (Befehlszeile) verwendet wird, um Elemente in der QnAMaker-Wissensdatenbank zu veröffentlichen.
