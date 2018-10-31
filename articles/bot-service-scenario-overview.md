---
title: Übersicht über Bot Service-Szenarien | Microsoft-Dokumentation
description: Erkunden Sie wichtige Szenarien für leistungsfähige und erfolgreiche Bots, die mit Bot Service erstellt werden.
author: BrianRandell
ms.author: v-brra
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: e195f83eefd5f162b74f8891f3b174efc8934700
ms.sourcegitcommit: b78fe3d8dd604c4f7233740658a229e85b8535dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2018
ms.locfileid: "49997966"
---
# <a name="bot-scenarios"></a>Botszenarien

[!INCLUDE [pre-release-label](includes/pre-release-label-v3.md)]

In diesem Thema werden wichtige Szenarien für leistungsfähige und erfolgreiche Bots beschrieben, die mit Bot Service erstellt werden.

Sie können den Quellcode für die Beispielbots aller Szenarien unter den [Beispielen für häufige Bot Framework-Szenarios](https://aka.ms/bot/scenarios) herunterladen oder klonen.

## <a name="commerce-bot-scenario"></a>Szenario für einen gewerblichen Bot
Das Szenario für einen [gewerblichen Bot](bot-service-scenario-commerce.md) beschreibt einen Bot, der die herkömmlichen Interaktionen per E-Mail und Telefon ersetzt, die normalerweise zwischen Gästen und dem Portierdienst eines Hotels stattfinden. Der Bot nutzt Cognitive Services, um Kundenanfragen per Text und Sprache mit Kontext, der durch die Integration von Back-End-Diensten erfasst wird, besser zu verarbeiten.

Im Szenario für einen gewerblichen Bot kann ein Kunde Portierdienste bei einem Hotel anfordern. Die Authentifizierung erfolgt über einen Azure Active Directory v2-Authentifizierungsendpunkt. Der Bot kann Reservierungen des Kunden nachschlagen und verschiedene Dienstoptionen bereitstellen. Beispielsweise kann der Kunde ein Sonnensegel am Pool buchen. Der Bot verwendet LUIS (Language Understanding Intelligent Services), um die Anforderung zu analysieren, und dann führt der Bot den Benutzer durch den Prozess zum Buchen eines Sonnensegels für eine bestehende Reservierung.

## <a name="cortana-skill-bot-scenario"></a>Szenario für einen Bot für Cortana-Funktionen
Beim Szenario für einen [Bot für Cortana-Funktionen](bot-service-scenario-cortana-skill.md) wird Cortana verwendet. Mithilfe Ihrer Stimme und dem benutzerdefinierten Bot für Cortana-Funktionen können Sie Cortana darum bitten, mit einer Organisation zu sprechen, also z.B. einer Werkstatt für professionelle Autopflege, damit Sie leichter einen Termin vereinbaren können, und zwar ausgehend von Ihrem Standort beim Telefonat. Der Bot kann Ihnen eine Liste mit Dienstleistungen und verfügbaren Terminen anzeigen sowie die Dauer des Termins bestimmen.

## <a name="enterprise-productivity-bot-scenario"></a>Szenario für einen Bot für die Unternehmensproduktivität
Das Szenario für einen [Bot für die Unternehmensproduktivität](bot-service-scenario-enterprise-productivity.md) zeigt Ihnen, wie Sie zur Steigerung der Produktivität einen Bot in Ihren Office 365-Kalender und andere Dienste integrieren.

Der Bot wird in Office 365 integriert, damit eine Besprechungsanfrage für eine andere Person schneller und einfacher erstellt werden kann. Bei diesem Prozess kann auch auf zusätzliche Dienste wie Dynamics CRM zugegriffen werden. Dieses Beispiel enthält den Code, der zur Integration in Office 365 mit Authentifizierung über Azure Active Directory erforderlich ist. Es bietet Pseudoeinstiegspunkte für externe Dienste als Übung für den Leser.

## <a name="information-bot-scenario"></a>Szenario für einen Informationsbot
Dieser [Informationsbot](bot-service-scenario-informational.md) kann Fragen beantworten, die mithilfe von QnA Maker von Cognitive Services in einer Wissenssammlung oder in häufig gestellten Fragen definiert wurden. Offenere Fragen kann der Bot mithilfe von Azure Search beantworten.

Häufig sind Informationen in strukturierten Datenspeichern wie SQL Server verborgen, die durch eine Suche leicht zugänglich gemacht werden können. Stellen Sie sich vor, Sie schlagen den Bestellstatus eines Kunden mithilfe einfacher Konversationsbefehle nach. Bei Verwendung von QnA Maker von Cognitive Services werden dem Benutzer mehrere gültige Suchoptionen angezeigt, z.B. Nachschlagen eines Kunden, Überprüfen der letzten Bestellung eines Kunden usw. Durch das definierte QnA-Format kann der Benutzer ganz einfach Fragen stellen, die von Azure Search unterstützt werden, und dieser Dienst kann Daten nachschlagen, die in einer SQL-Datenbank gespeichert sind.

## <a name="iot-bot-scenario"></a>Szenario für einen IoT-Bot
Dieser [Internet der Dinge (IoT)-Bot](bot-service-scenario-internet-things.md) erleichtert Ihnen das Steuern von Geräten in Ihrem Zuhause – z.B. Philips Hue-Lichtsysteme – mit Befehlen in einem interaktiven Chat.

Mithilfe dieses einfachen Bots können Sie Ihre Philips Hue-Lichtsysteme in Kombination mit dem kostenlosen IFTTT-Dienst (If This Then That) steuern. Als IoT-Gerät kann Philips Hue lokal über die bereitgestellte API gesteuert werden. Diese API steht jedoch nicht für den allgemeinen Zugriff von außerhalb des lokalen Netzwerks bereit. IFTTT ist aber ein „[Freund von Hue](http://www2.meethue.com/en-us/friends-of-hue/ifttt/)“ und hat daher eine Reihe von Steuerungsbefehlen verfügbar gemacht, die Sie ausgeben können, wie z.B. das Ein- und Ausschalten der Beleuchtung sowie das Ändern der Farbe oder Stärke des Lichts.

## <a name="next-steps"></a>Nächste Schritte
Nachdem Sie nun eine Übersicht über die Szenarien erhalten haben, sehen Sie sich die einzelnen Szenarien genauer an.

> [!div class="nextstepaction"]
> [Gewerblicher Bot](bot-service-scenario-commerce.md)
