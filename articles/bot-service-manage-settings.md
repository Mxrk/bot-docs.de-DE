---
title: Konfigurieren von Boteinstellungen | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie unterschiedliche Botoptionen mithilfe des Azure-Portals konfigurieren.
keywords: Konfigurieren von Boteinstellungen, Anzeigename, Symbol, Application Insights, Blatt „Einstellungen“
author: v-royhar
ms.author: v-royhar
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
ms.openlocfilehash: 531e37f2186de2e315f11362dcefcc30a2ab6879
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39301861"
---
# <a name="configure-bot-settings"></a>Konfigurieren von Boteinstellungen

Boteinstellungen wie der Anzeigename, das Symbol und Application Insights können auf dem Blatt **Einstellungen** angezeigt und geändert werden.

![Blatt für Boteinstellungen](~/media/bot-service-portal-configure-settings/bot-settings-blade.png)

Die folgende Liste enthält die Felder, die auf dem Blatt **Einstellungen** verfügbar sind:

| Feld | BESCHREIBUNG |
| :---  | :---        |
| Symbol | Ein individuelles Symbol, das Ihren Bot in Kanälen, Skype, Cortana und anderen Diensten kennzeichnet. Dieses Symbol muss im PNG-Format vorliegen und darf nicht größer als 30 KB sein. Dieser Wert kann jederzeit geändert werden. |
| Anzeigename | Der Name Ihres Bots in Kanälen und Verzeichnissen. Dieser Wert kann jederzeit geändert werden. Maximal 35 Zeichen. |
| Bothandle | Eindeutiger Bezeichner für den Bot. Dieser Wert kann nach dem Erstellen des Bots mit dem Botdienst nicht geändert werden. |
| Messaging-Endpunkt | Der Endpunkt für die Kommunikation mit Ihrem Bot. |
| Microsoft-App-ID | Eindeutiger Bezeichner für den Bot. Dieser Wert kann nicht geändert werden. Sie können ein neues Kennwort generieren, indem Sie auf den Link **Verwalten** klicken. |
| Application Insights-Instrumentierungsschlüssel | Eindeutiger Schlüssel für Bot-Telemetriedaten. Kopieren Sie Ihren Azure Application Insights-Schlüssel in dieses Feld, wenn Sie Telemetriedaten für diesen Bot empfangen möchten. Dieser Wert ist optional. Für Bots, die im Azure-Portal erstellt werden, wird dieser Schlüssel automatisch generiert. Weitere Informationen zu diesem Feld finden Sie unter [Application Insights-Schlüssel](~/bot-service-resources-app-insights-keys.md). |
| Application Insights-API-Schlüssel | Eindeutiger Schlüssel für die Botanalyse. Kopieren Sie Ihren Azure Application Insights-API-Schlüssel in dieses Feld, wenn Sie Analysen zu Ihrem Bot auf dem Dashboard anzeigen möchten. Dieser Wert ist optional. Weitere Informationen zu diesem Feld finden Sie unter [Application Insights-Schlüssel](~/bot-service-resources-app-insights-keys.md). |
| Application Insights-Anwendungs-ID | Eindeutiger Schlüssel für die Botanalyse. Kopieren Sie Ihren Azure Application Insights-Anwendungsschlüssel in dieses Feld, wenn Sie Analysen zu Ihrem Bot auf dem Dashboard anzeigen möchten. Dieser Wert ist optional. Für Bots, die im Azure-Portal erstellt werden, wird dieser Schlüssel automatisch generiert. Weitere Informationen zu diesem Feld finden Sie unter [Application Insights-Schlüssel](~/bot-service-resources-app-insights-keys.md). |

> [!NOTE]
> Klicken Sie nach dem Ändern der Boteinstellungen oben auf dem Blatt auf **Speichern**, um die neuen Boteinstellungen zu speichern.

## <a name="next-steps"></a>Nächste Schritte
In diesem Artikel haben Sie erfahren, wie Sie Einstellungen für Ihren Botdienst konfigurieren. Im nächsten Artikel wird beschrieben, wie Sie die Sprachvorbereitung konfigurieren.
> [!div class="nextstepaction"]
> [Sprachvorbereitung](bot-service-manage-speech-priming.md)