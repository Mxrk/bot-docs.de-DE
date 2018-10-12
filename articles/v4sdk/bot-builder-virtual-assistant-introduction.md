---
title: Virtueller Assistent – Übersicht | Microsoft-Dokumentation
description: Es wird beschrieben, wie Sie Ihren eigenen virtuellen Assistenten erstellen.
author: darrenj
ms.author: darrenj
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 13/12/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 075c090571eb7a45efc57b915a4bc912fc9c55df
ms.sourcegitcommit: 87b5b0ca9b0d5e028ece9f7cc4948c5507062c2b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2018
ms.locfileid: "47029856"
---
# <a name="virtual-assistant-solution-overview"></a>Lösung für virtuelle Assistenten – Übersicht

## <a name="overview"></a>Übersicht
Wir haben festgestellt, dass unsere Kunden und Partner Bedarf an einem Konversationsassistenten haben, der auf ihre Marke zugeschnitten, auf ihre Kunden ausgerichtet und für diverse Konversationscanvas und -geräte verfügbar ist. Mit der Lösung für den benutzerdefinierten persönlichen Assistenten auf Open-Source-Basis wird der Open Source-Ansatz von Microsoft für das Bot Framework SDK fortgeführt. Er ermöglicht eine vollständige Kontrolle über die Endbenutzererfahrung, die auf einer Reihe von grundlegenden Funktionen basiert. Darüber hinaus können der Erfahrung Informationen zu Endbenutzern sowie zu Geräten und Ökosystemen hinzugefügt werden, um eine vollständig integrierte und intelligente Benutzeroberfläche bereitzustellen.

Wir sind davon überzeugt, dass unsere Kunden ihre Kundenbeziehungen und -erkenntnisse besitzen und über Erweiterungsmöglichkeiten dafür verfügen sollten. Deswegen können unsere Kunden und Partner die Benutzeroberfläche für alle virtuellen Assistenten vollständig steuern, indem sie den Open-Source-Code auf GitHub verwenden. Name, Stil und Typ können individuell an die Organisation angepasst werden. Mit unserer Lösung für virtuelle Assistenten wird die Erstellung Ihres eigenen Assistenten vereinfacht. Sie können in wenigen Minuten starten und die Lösung dann erweitern, indem Sie unsere End-to-End-Tools für die Entwicklung nutzen.

Der Funktionsumfang des virtuellen Assistenten ist breit gefächert und bietet Endbenutzern eine große Auswahl an Funktionen. Um die Produktivität von Entwicklern zu steigern und ein aktives Ökosystem aus wiederverwendbaren Konversationserfahrungen zu schaffen, stellen wir Entwicklern Beispiele für Konversationsfähigkeiten zur Verfügung, die wiederverwendet werden können. Diese Fähigkeiten können in eine Konversationsanwendung integriert werden, um bestimmte Konversationserfahrungen zu verbessern, z.B. das Suchen nach einem Point of Interest, das Interagieren mit Kalendern, Aufgaben, E-Mails usw. Die Fähigkeiten sind vollständig anpassbar und umfassen Sprachmodelle für mehrere Sprachen, Dialoge und Code.

Derzeit wird eine erste Vorschauversion ausgeführt. Wir arbeiten eng mit langjährigen Kunden und Partnern in einem Open-Source-Repository zusammen, um die ersten Benutzeroberflächen in den kommenden Monaten zum Leben zu erwecken und für ein breiteres Publikum verfügbar zu machen.

![Virtueller Assistent – Diagramm](./media/enterprise-template/customassistantdiagram.jpg)

## <a name="whats-in-the-box"></a>Lieferumfang 

Der virtuelle Assistent baut auf den Funktionen der [Unternehmensvorlage](./bot-builder-enterprise-template-overview.md) auf. Hierüber werden grundlegende Funktionen für Konversationsoberflächen bereitgestellt, z.B. einfache Konversationsabsichten in mehreren Sprachen, Dispatchvorgänge, Fragen und Antworten und konversationsbezogene Erkenntnisse. Die folgenden Funktionen für Assistenten werden derzeit bereitgestellt, und weitere Funktionen sind in Planung. Wir arbeiten eng mit Kunden und Partnern zusammen, um Informationen zur Roadmap zu liefern.

Feature | BESCHREIBUNG |
------------ | -------------
Onboarding | Ein Beispiel für einen Onboarding-Ablauf, mit dem Ihr Assistent den Benutzer begrüßen und erste Informationen sammeln kann.
Ereignisarchitektur | Mit Ereignissen im Kontext des virtuellen Assistenten kann die Clientanwendung, die den Assistenten hostet (in einem Webbrowser oder auf einem Gerät, z.B. Fahrzeug oder Lautsprecher), Informationen zu Benutzer- oder Geräteereignissen austauschen, während außerdem Ereignisse zum Durchführen von Gerätevorgängen empfangen werden.
Verknüpfte Konten | In einem Szenario mit Sprachsteuerung ist es für einen Benutzer nicht praktisch, seinen Benutzernamen und das Kennwort einzugeben, um Systeme über Sprachbefehle zu unterstützen. Aus diesem Grund kann sich der Benutzer über eine separate Begleitoberfläche anmelden und für einen virtuellen Assistenten die Berechtigung zum Abrufen von Token zur späteren Verwendung erteilen.
Aktivierung von Fähigkeiten | Es gibt heutzutage viele allgemeine Funktionen, die von Entwicklern selbst erstellt werden müssen. Unsere Lösung für virtuelle Assistenten enthält eine neue Funktion für Fähigkeiten, mit der neue Funktionen per Konfiguration in einen virtuellen Assistenten eingefügt werden können. Außerdem ist ein Authentifizierungsmechanismus vorhanden, mit dem für Fähigkeiten Token für nachgeschaltete Aktivitäten angefordert werden können.
Fähigkeit „Point of Interest“ | Bei der in der Vorschauphase befindlichen Fähigkeit „Point of Interest“ (PoI) wird ein umfassendes Sprachmodell zum Finden von Points of Interest und zum Anfordern einer Wegbeschreibung bereitgestellt. Die Fähigkeit ermöglicht derzeit die Integration in Azure Maps.
Fähigkeit „Kalender“ | Bei der in der Vorschauphase befindlichen Fähigkeit „Kalender“ wird ein umfassendes Sprachmodell für allgemeine kalenderbezogene Aktivitäten bereitgestellt. Die Fähigkeit ist derzeit in Microsoft Graph (Office 365/Outlook.com) integriert, und die Unterstützung für Google-APIs folgt in Kürze.
Fähigkeit „E-Mail“ | Bei der in der Vorschauphase befindlichen Fähigkeit „E-Mail“ wird ein umfassendes Sprachmodell für allgemeine E-Mail-bezogene Aktivitäten bereitgestellt. Die Fähigkeit ist derzeit in Microsoft Graph (Office 365/Outlook.com) integriert, und die Unterstützung für Google-APIs folgt in Kürze.
Fähigkeit „Aufgaben“ | Bei der in der Vorschauphase befindlichen Fähigkeit „Aufgaben“ wird ein umfassendes Sprachmodell für allgemeine aufgabenbezogene Aktivitäten bereitgestellt. Die Fähigkeit ist derzeit in OneNote integriert, und die Unterstützung für Microsoft Graph (outlookTask) folgt in Kürze.
Geräteintegration | Unsere Azure Bot Service SDKs (DirectLine) ermöglichen zusammen mit SDKs für adaptive Karten und die Sprachsteuerung eine einfache plattformübergreifende Integration in Geräte. Zusätzliche Beispiele für die Geräteintegration und Plattformen mit Edge sind geplant.
Testumgebungen | Zusätzlich zum Bot Framework Emulator wird eine WebChat-basierte Testumgebung bereitgestellt, mit der komplexere Authentifizierungsszenarien getestet werden können. In einer einfachen konsolenbasierten Testumgebung wird der Ansatz zum Austauschen von Nachrichten veranschaulicht, um die Einfachheit der Geräteintegration zu verdeutlichen.
Automatisierte Bereitstellung | Alle Azure-Ressourcen, die für Ihren Assistenten erforderlich sind, werden automatisch bereitgestellt: Botregistrierung, Azure App Service, LUIS, QnAMaker, Content Moderator, Cosmos DB, Azure Storage und Application Insights. Darüber hinaus werden LUIS-Modelle für alle Fähigkeiten, QnAMaker und Dispatchmodelle erstellt, trainiert und veröffentlicht, um das sofortige Testen zu ermöglichen.
Sprachmodell für Automobilindustrie | Ein Sprachmodell für die Automobilindustrie, mit dem Kernbereiche wie Telefonie, Navigation und Steuerung von Fahrzeugfunktionen abgedeckt werden, ist in Kürze verfügbar.

## <a name="example-scenarios"></a>Beispielszenarien

Der virtuelle Assistent ist für viele verschiedene Branchenszenarien geeignet. Hier sind zu Referenzzwecken einige Beispielszenarien angegeben.

- Automobilindustrie: Persönlicher Assistent mit Sprachsteuerung, der in das Fahrzeug integriert ist und es Endbenutzern ermöglicht, herkömmliche Vorgänge (z.B. Navigation, Radio) in Fahrzeugen durchzuführen. Darüber hinaus ist er aber auch für Produktivitätsszenarien geeignet, z.B. Verschieben von Besprechungen bei Verspätung, Hinzufügen von Einträgen zur Aufgabenliste und Gewährleisten der Proaktivität (das Fahrzeug schlägt basierend auf Ereignissen, z.B. Starten des Motors, Fahrt nach Hause oder Aktivierung des Tempomats, zu erfüllende Aufgaben vor). Adaptive Karten werden in der Head-Up-Einheit gerendert, und die Sprachintegration erfolgt über Interaktionen per Push-To-Talk oder Aktivierungswort.

- Hotel- und Gaststättengewerbe: Der persönliche Assistent mit Sprachsteuerung ist in ein Gerät in einem Hotelzimmer integriert und ermöglicht viele verschiedene Szenarien für das Hotelgewerbe (z.B. Verlängerung des Aufenthalts, Anfragen wegen spätem Checkout, Zimmerservice), einschließlich Concierge-Diensten und der Möglichkeit zur Suche nach Restaurants und Sehenswürdigkeiten. Eine optionale Verknüpfung mit Ihren Produktivitätskonten ermöglicht eine noch stärkere Personalisierung, z.B. vorgeschlagene Weckanrufe, Unwetterwarnungen und Erlernen von Mustern während des Aufenthalts. Dies ist eine Weiterentwicklung der derzeitigen Personalisierung über den Fernseher, die in Hotelzimmern bereits üblich ist.

- Großunternehmen: Mit Branding versehene Mitarbeiterassistenten mit Sprach- und Textsteuerung, die in Unternehmensgeräte und vorhandene Konversationscanvases integriert sind (z.B. Teams, WebChat, Slack) und Mitarbeitern das Verwalten ihrer Kalender, Ermitteln von verfügbaren Besprechungsräumen, Finden von Personen mit bestimmten Fähigkeiten oder Durchführen von personalbezogenen Vorgängen ermöglichen.

## <a name="virtual-assistant-principles"></a>Prinzipien des virtuellen Assistenten

### <a name="your-data-your-brand-and-your-experience"></a>Ihre Daten, Ihre Marke und Ihre Erfahrung
Alle Aspekte der Oberfläche für Endbenutzer befinden sich in Ihrem Besitz und werden von Ihnen gesteuert. Dies umfasst Branding, Name, Stimme, Persönlichkeit, Antworten und Avatar. Der Quellcode für den virtuellen Assistenten und die unterstützenden Fähigkeiten werden vollständig bereitgestellt, sodass sie ihn je nach Bedarf anpassen können.

Ihr virtueller Assistent wird unter Ihrem Azure-Abonnement bereitgestellt. Daher sind alle vom Assistenten generierten Daten (gestellte Fragen, Benutzerverhalten usw.) vollständig in Ihrem Azure-Abonnement enthalten. Weitere genauere Informationen finden Sie im Artikel zu [Cognitive Services für die vertrauenswürdige Azure-Cloud](https://www.microsoft.com/en-us/trustcenter/cloudservices/cognitiveservices) und im [Azure-Abschnitt im Trust Center](https://www.microsoft.com/en-us/TrustCenter/CloudServices/Azure).

### <a name="write-it-once-embed-it-anywhere"></a>Einmal schreiben, überall einbetten
Da für den virtuellen Assistenten die Microsoft Conversational AI-Plattform genutzt wird, kann er über beliebige Bot-Framework-[Kanäle](https://docs.microsoft.com/en-us/azure/bot-service/bot-service-manage-channels?view=azure-bot-service-4.0) bereitgestellt werden, z.B. WebChat, FaceBook Messenger, Skype usw. 

Außerdem können über den [Direct Line](https://docs.microsoft.com/en-us/azure/bot-service/rest-api/bot-framework-rest-direct-line-3-0-concepts?view=azure-bot-service-4.0)-Kanal Benutzeroberflächen in Desktop-Apps und mobile Apps eingebettet werden, z.B. Fahrzeuge, Lautsprecher, Wecker usw.

### <a name="enterprise-grade-solutions"></a>Für große Unternehmen geeignete Lösungen
Da die Lösung für den virtuellen Assistenten auf Azure Bot Service, dem Cognitive Service „Language Understanding“, vereinheitlichter Sprache und einem weiten Bereich mit unterstützenden Azure-Komponenten aufbaut, profitieren Sie von der [globalen Azure-Infrastruktur](https://azure.microsoft.com/en-gb/global-infrastructure/) (z.B. Zertifizierungen ISO 27018, HIPPA, PCI-DSS, SOC 1, 2 und 3).

Außerdem wird über den Cognitive Service „LUIS“ Language Understanding-Unterstützung bereitgestellt, um eine größere Gruppe von Sprachen zu unterstützen, die [hier](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/luis-supported-languages) aufgeführt sind. Über den [Cognitive Service „Translator“](https://azure.microsoft.com/en-us/services/cognitive-services/translator-text-api/) werden zusätzliche Funktionen für die maschinelle Übersetzung bereitgestellt, um die Reichweite Ihres virtuellen Assistenten noch mehr zu erhöhen.

### <a name="integrated-and-context-aware"></a>Integriert und kontextbezogen
Ihr virtueller Assistent kann in Ihr Gerät und Ihr Ökosystem integriert werden, um eine wirklich integrierte und intelligente Umgebung zu schaffen. Aufgrund dieser Kontextbezogenheit können intelligentere Benutzeroberflächen entwickelt werden, und die Personalisierung kann besser weiterentwickelt werden, als dies sonst der Fall wäre.

### <a name="3rd-party-assistant-integration"></a>Integration von Drittanbieterassistenten
Mit dem virtuellen Assistenten können Sie Ihre eigene einzigartige Umgebung bereitstellen, aber für bestimmte Arten von Fragen auch den von Endbenutzern gewählten digitalen Assistenten einbinden.

### <a name="flexible-integration"></a>Flexible Integration
Die Architektur unseres virtuellen Assistenten ist flexibel und kann in vorhandene Investitionen integriert werden, die Sie in Bezug auf gerätebasierte Funktionen zur Verarbeitung von Sprache bzw. natürlicher Sprache ggf. getätigt haben. Selbstverständlich ist auch die Integration in Ihre vorhandenen Back-End-Systeme und APIs möglich.

### <a name="adaptive-cards"></a>Adaptive Karten
[Adaptive Karten](https://adaptivecards.io/) ermöglichen für Ihren virtuellen Assistenten neben Antworten auf Textbasis auch das Zurückgeben von Elementen der Benutzeroberfläche (z.B. Karten, Bilder, Schaltflächen). Wenn die Geräte- oder Konversationscanvas über einen Bildschirm verfügt, können diese adaptiven Karten auf einem großen Bereich von Geräten und Plattformen gerendert werden und unterstützende Oberflächenelemente bereitstellen, sofern dies erforderlich ist. Beispiele für adaptive Karten finden Sie [hier](https://adaptivecards.io/samples/), und Informationen zu Renderingoptionen finden Sie [hier](https://docs.microsoft.com/en-us/adaptive-cards/rendering-cards/getting-started).

### <a name="skills"></a>Fähigkeiten
Zusätzlich zum Basisassistenten gibt es einen weiten Bereich mit allgemeinen Funktionen, die von jedem Entwickler selbst erstellt werden können. Die Produktivität ist ein gutes Beispiel, bei dem jede Organisation Sprachmodelle (LUIS), Dialoge (Code), Integration (Code) und Sprachgenerierung (Antworten) erstellen muss, um beliebte Benutzeroberflächen für die Bereiche Kalender, Aufgaben oder E-Mail zu ermöglichen.

Dies wird dann weiter verkompliziert, weil mehrere Sprachen unterstützt werden müssen, und führt für Organisationen, die ihren eigenen Assistenten erstellen, zu einem hohen Arbeitsaufwand.

Unsere Lösung für den virtuellen Assistenten enthält eine neue Funktion für Fähigkeiten, die per Konfiguration in einen benutzerdefinierten Assistenten eingebunden werden können. 

Alle Aspekte der einzelnen Fähigkeiten (Sprachmodell, Dialoge, Integrationscode und Sprachgenerierung) können von den Entwicklern vollständig angepasst werden, da auf GitHub zusammen mit dem virtuellen Assistenten der gesamte Quellcode bereitgestellt wird.

## <a name="getting-started"></a>Erste Schritte

Derzeit wird eine erste Vorschauversion ausgeführt. Wir arbeiten eng mit langjährigen Kunden und Partnern in einem Open-Source-Repository zusammen, um die ersten Benutzeroberflächen in den kommenden Monaten zum Leben zu erwecken und für ein breiteres Publikum verfügbar zu machen. Wenn Sie Ihr Interesse bekunden und einsteigen möchten, können Sie [dieses Formular](https://aka.ms/customassistantpreviewform) ausfüllen. Wir nehmen dann Kontakt mit Ihnen auf.

