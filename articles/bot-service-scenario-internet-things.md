---
title: Bot-Szenario für das Internet der Dinge | Microsoft-Dokumentation
description: Erkunden Sie das Bot-Szenario für das Internet der Dinge mit Bot Framework.
author: BrianRandell
ms.author: v-brra
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 3583c4382a9262b959b31d5a9a1b7b1b97fc8a04
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39301829"
---
# <a name="internet-of-things-iot-bot-scenario"></a>Bot-Szenario für das Internet der Dinge (IoT)
Dieser Internet der Dinge (IoT)-Bot erleichtert Ihnen das Steuern von Geräten in Ihrem Zuhause – z.B. Philips Hue-Lichtsysteme – mit Sprachbefehlen oder Befehlen in einem interaktiven Chat.

Menschen sprechen gerne mit ihren Dingen. Seit die erste Fernbedienung auf den Markt kam, haben Menschen es genossen, ihre Umgebung zu beeinflussen, ohne sich dabei zu bewegen. Dieser IoT-Bot ermöglicht Benutzern das Verwalten von Philips Hue mit einfachen Chat- oder Sprachbefehlen. Außerdem können bei Verwendung des Chats visuelle Auswahlmöglichkeiten anhand von Farben bereitgestellt werden.

![Das Diagramm zum Internet der Dinge-Bot](~/media/scenarios/bot-service-scenario-iot-bot.png)

Hier ist logische Ablauf eines IoT-Bots:

1. Der Benutzer meldet sich bei Skype an und greift auf den IoT-Bot zu.
2. Der Benutzer bittet den Bot per Spracheingabe, über das IoT-Gerät die Lichter einzuschalten.
3. Die Anforderung wird an einen Drittanbieterdienst übermittelt, der über Zugriff auf das IoT-Gerätenetzwerk verfügt.
4. Die Ergebnisse des Befehls werden dem Benutzer zurückgegeben.
5. Application Insights erfasst die Telemetrie der Runtime, um die Entwicklung der Bot-Leistung und -Nutzung zu unterstützen.

## <a name="sample-bot"></a>Beispiel-Bot
Der IoT-Bot ermöglicht Ihnen den schnellen Einsatz von Chatbefehlen aus Kanälen wie Skype oder Slack zum Steuern Ihres Hue-Systems. Zum Vereinfachen des Remotezugriffs rufen Sie für Hue vordefinierte IFTTT-Applets auf.

Sie können den Quellcode für diesen Beispiel-Bot unter [Samples for Common Bot Framework Scenarios (Beispiele für häufige Bot Framework-Szenarios)](https://aka.ms/bot/scenarios) herunterladen oder klonen.

## <a name="components-youll-use"></a>Komponenten, die Sie verwenden werden
Der Internet der Dinge (IoT)-Bot verwendet die folgenden Komponenten:
-   Philips Hue
-   If This Then That (IFTTT)
-   Application Insights

### <a name="philips-hue"></a>Philips Hue
Mit Philips Hue verbundene Glühbirnen und die Bridge ermöglichen Ihnen die vollständige Kontrolle über Ihre Beleuchtung. Wie auch immer Sie Ihre Beleuchtung nutzen möchten – Hue macht es möglich. Hue verfügt über eine API, die Sie in Ihrem lokalen Netzwerk verwenden können. Wenn Sie allerdings von überall aus auf Ihre über Hue gesteuerten Geräte und Lampen zugreifen möchten, nutzen Sie eine benutzerfreundliche Bot-Schnittstelle. Also greifen Sie über IFTTT auf Hue zu.

### <a name="ifttt"></a>IFTTT
IFTTT ist ein kostenloser webbasierter Dienst, mit dem Benutzer Verkettungen von einfachen bedingten Anweisungen namens Applets erstellen. Sie können ein Applet über Ihren Bot auslösen, damit es in Ihrem Auftrag etwas ausführt. Es gibt eine Reihe von vordefinierten Hue-Applets, z.B. zum Ein- und Ausschalten von Licht, Szenenwechsel und vieles mehr.

### <a name="application-insights"></a>Application Insights
Mit Application Insights können Sie über Application Performance Management (APM) und Sofortanalysen nützliche Erkenntnisse gewinnen. Sie erhalten ohne großen Konfigurationsaufwand eine umfassende Leistungsüberwachung, leistungsstarke Warnungen und einfach zu bedienende Dashboards, damit die Verfügbarkeit und Leistung Ihres Bots auch Ihren Erwartungen entspricht. Sie können schnell ermitteln, ob ein Problem besteht, und anschließend eine Ursachenanalyse durchführen, um das Problem zu finden und zu beheben.
