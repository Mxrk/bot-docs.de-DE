---
title: Benutzerdefinierter Assistent – Übersicht | Microsoft-Dokumentation
description: Es wird beschrieben, wie Sie Ihren eigenen benutzerdefinierten Assistenten erstellen.
author: darrenj
ms.author: darrenj
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 13/12/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: ddb1a7f8f189705de6b40bf2802a7bc2d1b49135
ms.sourcegitcommit: 3bf3dbb1a440b3d83e58499c6a2ac116fe04b2f6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/23/2018
ms.locfileid: "46708611"
---
## <a name="custom-assistant-overview"></a>Benutzerdefinierter Assistent – Übersicht

## <a name="overview"></a>Übersicht

Wir haben festgestellt, dass unsere Kunden und Partner Bedarf an einem Konversationsassistenten haben, der auf ihre Marke zugeschnitten, auf ihre Kunden ausgerichtet und für diverse Konversationscanvas und -geräte verfügbar ist. Mit dem benutzerdefinierten persönlichen Assistenten auf Open-Source-Basis wird der Open Source-Ansatz von Microsoft für das Bot Framework SDK fortgeführt. Er ermöglicht eine vollständige Kontrolle über die Endbenutzererfahrung, die auf einer Reihe von grundlegenden Funktionen basiert. Darüber hinaus können der Erfahrung Informationen zu Endbenutzern sowie zu Geräten und Ökosystemen hinzugefügt werden, um eine vollständig integrierte und intelligente Benutzeroberfläche bereitzustellen.

Wir sind davon überzeugt, dass unsere Kunden ihre Kundenbeziehungen und -erkenntnisse besitzen und über Erweiterungsmöglichkeiten dafür verfügen sollten. Deswegen können unsere Kunden und Partner die Benutzererfahrung mit allen benutzerdefinierten Assistenten vollständig steuern. Name, Stil und Typ können individuell an die Organisation angepasst werden. Mit der Lösung für den benutzerdefinierten Assistenten ist die Erstellung eines eigenen Assistenten sehr einfach, und Sie können in wenigen Minuten loslegen. 

Der Funktionsumfang des benutzerdefinierten persönlichen Assistenten ist breit gefächert und bietet Endbenutzern eine große Auswahl an Funktionen. Um die Produktivität von Entwicklern zu steigern und ein aktives Ökosystem aus wiederverwendbaren Konversationserfahrungen zu schaffen, stellen wir Entwicklern Beispiele für Konversationsfähigkeiten zur Verfügung, die wiederverwendet werden können. Diese Fähigkeiten können in die Konversationsanwendung integriert werden, um bestimmte Konversationserfahrungen zu verbessern, z.B. das Suchen nach einem Point of Interest, das Interagieren mit Kalendern, Aufgaben, E-Mails usw. Die Fähigkeiten sind vollständig anpassbar und umfassen Sprachmodelle für mehrere Sprachen, Dialoge und Code.

Derzeit wird eine erste Vorschauversion ausgeführt. Wir arbeiten eng mit langjährigen Kunden und Partnern in einem Open-Source-Repository zusammen, um die ersten Benutzeroberflächen in den kommenden Monaten zum Leben zu erwecken und für ein breiteres Publikum verfügbar zu machen. 

![Benutzerdefinierter Assistent – Diagramm](media/enterprise-template/CustomAssistantDiagram.jpg)

## <a name="complete-control-of-the-user-experience"></a>Vollständige Kontrolle über die Benutzeroberfläche

Alle Aspekte der Oberfläche für Endbenutzer befinden sich in Ihrem Besitz und werden von Ihnen gesteuert. Dies umfasst Branding, Name, Stimme, Persönlichkeit, Antworten und Avatar. Der Quellcode für den benutzerdefinierten Assistenten und die unterstützenden Fähigkeiten werden vollständig bereitgestellt, sodass sie ihn je nach Bedarf anpassen können.

## <a name="complete-ownership-and-control-of-data"></a>Vollständiger Besitz und Steuerung der Daten

Ihr benutzerdefinierter Assistent wird unter Ihrem Azure-Abonnement bereitgestellt. Daher sind alle vom Assistenten generierten Daten (gestellte Fragen, Benutzerverhalten usw.) vollständig in Ihrem Azure-Abonnement enthalten. Weitere genauere Informationen finden Sie im Artikel zu [Cognitive Services für die vertrauenswürdige Azure-Cloud](https://www.microsoft.com/en-us/trustcenter/cloudservices/cognitiveservices) und im [Azure-Abschnitt im Trust Center](https://www.microsoft.com/en-us/TrustCenter/CloudServices/Azure).

## <a name="your-assistant-anywhere"></a>Ihr Assistent – Überall verfügbar

Da für den benutzerdefinierten Assistenten die Microsoft Conversational AI-Plattform genutzt wird, kann er über beliebige Bot-Framework-[Kanäle](https://docs.microsoft.com/en-us/azure/bot-service/bot-service-manage-channels?view=azure-bot-service-4.0) bereitgestellt werden, z.B. WebChat, FaceBook Messenger, Skype usw. Außerdem können über den [Direct Line](https://docs.microsoft.com/en-us/azure/bot-service/rest-api/bot-framework-rest-direct-line-3-0-concepts?view=azure-bot-service-4.0)-Kanal Benutzeroberflächen in Desktop-Apps und mobile Apps eingebettet werden, z.B. Fahrzeuge, Lautsprecher, Wecker usw.

## <a name="built-on-enterprise-grade-technology"></a>Basiert auf Technologie für große Unternehmen

Da die Lösung für den benutzerdefinierten Assistenten auf Azure Bot Service, dem Cognitive Service „Language Understanding“, vereinheitlichter Sprache und einem weiten Bereich von unterstützenden Azure-Komponenten aufbaut, profitieren Sie von der [globalen Azure-Infrastruktur](https://azure.microsoft.com/en-gb/global-infrastructure/).

Außerdem wird über den Cognitive Service „LUIS“ Language Understanding-Unterstützung bereitgestellt, um eine größere Gruppe von Sprachen zu unterstützen, die [hier](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/luis-supported-languages) aufgeführt sind. Über den [Cognitive Service „Translator“](https://azure.microsoft.com/en-us/services/cognitive-services/translator-text-api/) werden zusätzliche Funktionen für die maschinelle Übersetzung bereitgestellt, um die Reichweite Ihres benutzerdefinierten Assistenten noch weiter zu erhöhen.

## <a name="integrated-and-context-aware"></a>Integriert und kontextbezogen

Ihr benutzerdefinierter Assistent kann in Ihr Gerät und Ihr Ökosystem integriert werden, um eine wirklich integrierte und intelligente Umgebung zu schaffen. Aufgrund dieser Kontextbezogenheit können intelligentere Benutzeroberflächen entwickelt werden, und die Personalisierung kann besser weiterentwickelt werden, als dies sonst der Fall wäre.

## <a name="3rd-party-assistant-integration"></a>Integration von Drittanbieterassistenten

Mit dem benutzerdefinierten Assistenten können Sie Ihre eigene einzigartige Umgebung bereitstellen, aber für bestimmte Arten von Fragen können Sie auch den von Endbenutzern gewählten digitalen Assistenten einbinden.

## <a name="flexible-integration"></a>Flexible Integration

Die Architektur unseres benutzerdefinierten Assistenten ist flexibel und kann in vorhandene Investitionen integriert werden, die Sie in Bezug auf gerätebasierte Funktionen zur Verarbeitung von Sprache bzw. natürlicher Sprache ggf. getätigt haben. Selbstverständlich ist auch die Integration in Ihre vorhandenen Back-End-Systeme und APIs möglich.

## <a name="adaptive-cards"></a>Adaptive Karten

[Adaptive Karten](https://adaptivecards.io/) ermöglichen für Ihren benutzerdefinierten Assistenten neben Antworten auf Textbasis auch das Zurückgeben von Elementen der Benutzeroberfläche (z.B. Karten, Bilder, Schaltflächen). Wenn die Geräte- oder Konversationscanvas über einen Bildschirm verfügt, können diese adaptiven Karten auf einem großen Bereich von Geräten und Plattformen gerendert werden und unterstützende Oberflächenelemente bereitstellen, sofern dies erforderlich ist. Beispiele für adaptive Karten finden Sie [hier](https://adaptivecards.io/samples/), und Informationen zu Renderingoptionen finden Sie [hier](https://docs.microsoft.com/en-us/adaptive-cards/rendering-cards/getting-started).


## <a name="skills"></a>Fähigkeiten

Zusätzlich zum Basisassistenten gibt es einen weiten Bereich mit allgemeinen Funktionen, die von jedem Entwickler selbst erstellt werden können. Die Produktivität ist ein gutes Beispiel, bei dem jede Organisation Sprachmodelle (LUIS), Dialoge (Code), Integration (Code) und Sprachgenerierung (Antworten) erstellen muss, um häufige Szenarien wie Point of Interest, E-Mail, Kalender oder Aufgaben zu ermöglichen.

Dies wird dann weiter verkompliziert, weil mehrere Sprachen unterstützt werden müssen, und führt für Organisationen, die ihren eigenen Assistenten erstellen, zu einem hohen Arbeitsaufwand.

Unsere Lösung für den benutzerdefinierten Assistenten enthält eine neue Funktion für Fähigkeiten, die per Konfiguration in einen benutzerdefinierten Assistenten eingebunden werden können. 

Alle Aspekte der einzelnen Fähigkeiten (Sprachmodell, Dialoge, Integrationscode und Sprachgenerierung) können von den Entwicklern vollständig angepasst werden, da auf GitHub zusammen mit dem benutzerdefinierten Assistenten der gesamte Quellcode bereitgestellt wird.

## <a name="example-scenarios"></a>Beispielszenarien

Der benutzerdefinierte Assistent ist für viele verschiedene Branchenszenarien geeignet. Hier sind zu Referenzzwecken Beispielszenarien angegeben:

- Automobilindustrie: Persönlicher Assistent mit Sprachsteuerung, der in das Fahrzeug integriert ist und es Endbenutzern ermöglicht, herkömmliche Vorgänge (z.B. Navigation, Radio) in Fahrzeugen durchzuführen. Darüber hinaus ist er aber auch für Produktivitätsszenarien geeignet, z.B. Verschieben von Besprechungen bei Verspätung, Hinzufügen von Einträgen zur Aufgabenliste und Gewährleisten der Proaktivität (das Fahrzeug schlägt basierend auf Ereignissen, z.B. Starten des Motors, Fahrt nach Hause oder Aktivierung des Tempomats, zu erfüllende Aufgaben vor). Adaptive Karten werden in der Head-Up-Einheit gerendert, und die Sprachintegration erfolgt über Interaktionen per Push-To-Talk oder Aktivierungswort.

- Hotel- und Gaststättengewerbe: Der persönliche Assistent mit Sprachsteuerung ist in ein Gerät in einem Hotelzimmer integriert und ermöglicht viele verschiedene Szenarien für das Hotelgewerbe (z.B. Verlängerung des Aufenthalts, Anfragen wegen spätem Checkout, Zimmerservice), einschließlich Concierge-Diensten und der Möglichkeit zur Suche nach Restaurants und Sehenswürdigkeiten. Eine optionale Verknüpfung mit Ihren Produktivitätskonten ermöglicht eine noch stärkere Personalisierung, z.B. vorgeschlagene Weckanrufe, Unwetterwarnungen und Erlernen von Mustern während des Aufenthalts. Dies ist eine Weiterentwicklung der derzeitigen Personalisierung über den Fernseher, die in Hotelzimmern bereits üblich ist.

- Großunternehmen: Mit Branding versehene Mitarbeiterassistenten mit Sprach- und Textsteuerung, die in Unternehmensgeräte und vorhandene Konversationscanvases integriert sind (z.B. Teams, WebChat, Slack) und Mitarbeitern das Verwalten ihrer Kalender, Ermitteln von verfügbaren Besprechungsräumen, Finden von Personen mit bestimmten Fähigkeiten oder Durchführen von personalbezogenen Vorgängen ermöglichen. 

## <a name="getting-started"></a>Erste Schritte

Derzeit wird eine erste Vorschauversion ausgeführt. Wir arbeiten eng mit langjährigen Kunden und Partnern in einem Open-Source-Repository zusammen, um die ersten Benutzeroberflächen in den kommenden Monaten zum Leben zu erwecken und für ein breiteres Publikum verfügbar zu machen. Wenn Sie Ihr Interesse bekunden und einsteigen möchten, können Sie [dieses Formular](https://aka.ms/customassistantpreviewform) ausfüllen. Wir nehmen dann Kontakt mit Ihnen auf.

