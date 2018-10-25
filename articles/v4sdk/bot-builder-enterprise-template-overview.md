---
title: Übersicht über die Vorlage für den Bot für Unternehmen | Microsoft-Dokumentation
description: Hier finden Sie Informationen zu den Funktionen der Vorlage für den Bot für Unternehmen.
author: darrenj
ms.author: darrenj
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 09/18/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: f81bba9077f4935a29f47eddf078932731cdec20
ms.sourcegitcommit: b78fe3d8dd604c4f7233740658a229e85b8535dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2018
ms.locfileid: "49997287"
---
# <a name="enterprise-bot-template"></a>Vorlage für den Bot für Unternehmen 

> [!NOTE]
> Dieses Thema gilt für die SDK-Version v4. 

Für die Erstellung einer hochwertigen Konversationsumgebung werden gewisse Grundfunktionen benötigt. Aus diesem Grund haben wir die Vorlage für den Bot für Unternehmen erstellt, um Sie bei der Erstellung überzeugender Konversationsumgebungen zu unterstützen. Diese Vorlage vereint alle Best Practices und unterstützenden Komponenten, die wir im Rahmen der Erstellung von Konversationsumgebungen ermittelt haben. 

Mit ihr lassen sich neue Botprojekte deutlich einfacher erstellen. Die Vorlage bietet folgende vorgefertigte Funktionen und nutzt dabei sowohl [Bot Builder SDK v4](https://github.com/Microsoft/botbuilder) als auch [Bot Builder-Tools](https://github.com/Microsoft/botbuilder-tools).

Feature | BESCHREIBUNG |
------------ | -------------
Einführungsnachricht | Einführungsnachricht mit einer adaptiven Karte für den Konversationsbeginn. Die Nachricht informiert über die Funktionen des Bots und bietet Schaltflächen für erste Fragen. Entwickler können nach Bedarf Anpassungen vornehmen.
Automatisierte Eingabeindikatoren  | Senden Sie visuelle Eingabeindikatoren während Konversationen, und wiederholen Sie dies bei Vorgängen mit langer Ausführungsdauer.
Auf BOT-Datei basierende Konfiguration | Alle Konfigurationsinformationen für Ihren Bot (LUIS, Dispatcher-Endpunkte, Application Insights und Ähnliches) befinden sich in der BOT-Datei und werden beim Start Ihres Bots herangezogen.
Grundlegende Konversationsabsichten  | Grundabsichten (Begrüßung, Verabschiedung, Hilfe, Abbruch usw.) in englischer, französischer, italienischer, deutscher und spanischer Sprache. Diese werden in Sprachverständnisdateien (LU-Dateien) bereitgestellt und lassen sich problemlos anpassen.
Grundlegende Konversationsantworten  | Antworten auf grundlegende Konversationsabsichten, abstrahiert in separaten View-Klassen. Diese werden später in die neuen Sprachgenerationsdateien (LG-Dateien) verschoben.
Erkennung ungeeigneter Inhalte oder personenbezogener Informationen (personally identifiable information, PII)  |Ermöglicht die Erkennung ungeeigneter Daten oder personenbezogener Informationen in eingehenden Konversationen durch die Verwendung von [Content Moderator](https://azure.microsoft.com/en-us/services/cognitive-services/content-moderator/) in einer Middlewarekomponente.
Transkripte  | Transkripts sämtlicher Konversationen, die in Azure Storage gespeichert sind.
Verteiler | Ein integriertes [Dispatchmodell](https://docs.microsoft.com/en-us/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&tabs=csaddref%2Ccsbotconfig) zur Erkennung, ob eine bestimmte Äußerung von LUIS und dem Code verarbeitet oder an QnAMaker übergeben werden soll.
QnAMaker-Integration  | Integration von [QnAMaker](https://www.qnamaker.ai) zur Beantwortung allgemeiner Fragen auf der Grundlage einer Wissensdatenbank, die auf bereits vorhandene Datenquellen (beispielsweise PDF-Handbücher) zurückgreifen kann.
Konversationserkenntnisse  | Integration von [Application Insights](https://azure.microsoft.com/en-gb/services/application-insights/) zur Erfassung von Telemetriedaten für alle Konversationen und ein exemplarisches Power BI-Dashboard, um Ihnen den Einstieg in die Gewinnung von Erkenntnissen über Ihre Konversationsumgebungen zu erleichtern.

Darüber hinaus werden alle für den Bot erforderlichen Azure-Ressourcen automatisch bereitgestellt: Botregistrierung, Azure App Service, LUIS, QnAMaker, Content Moderator, Cosmos DB, Azure Storage und Application Insights. Außerdem werden grundlegende Modelle für LUIS, QnAMaker und Dispatcher erstellt, trainiert und veröffentlicht, um das sofortige Testen grundlegender Absichten und der Weiterleitung zu ermöglichen.

Nach Erstellung der Vorlage und Ausführung der Bereitstellungsschritte können Sie F5 drücken, um einen umfassenden Test durchzuführen. Dadurch erhalten Sie nicht nur eine solide Grundlage für die Erstellung Ihrer Konversationsumgebung, Sie sparen sich im Gegensatz zu früher auch mehrere Tage Arbeit und erreichen eine höhere Konversationsqualität.

Fahren Sie zum Einstieg mit dem [Erstellen des Projekts](bot-builder-enterprise-template-create-project.md) fort. Weitere Informationen zu den Best Practices und Erkenntnissen, die den oben genannten Features zugrunde liegen, finden Sie im [Thema zu den Vorlagendetails](bot-builder-enterprise-template-overview-detail.md). 

Den vollständigen Quellcode der Vorlage für den Bot für Unternehmen finden Sie auf [GitHub](https://github.com/Microsoft/AI/tree/master/templates/Enterprise-Template).
