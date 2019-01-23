---
title: Funktionsweise von Bot Service | Microsoft-Dokumentation
description: Erfahren Sie mehr über die Features und Funktionen von Bot Service.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 85ef0fde39980bab1b891518e338fddbd56b275a
ms.sourcegitcommit: b15cf37afc4f57d13ca6636d4227433809562f8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/11/2019
ms.locfileid: "54224455"
---
# <a name="how-bot-service-works"></a>Funktionsweise von Bot Service

Bot Service stellt die Kernkomponenten zum Erstellen von Bots bereit, einschließlich Bot Framework SDK für die Entwicklung von Bots und Bot Framework für die Verbindung von Bots mit Kanälen. Bot Service bietet fünf Vorlagen für das Erstellen von Bots mit Unterstützung für .NET und Node.js bereit.

> [!IMPORTANT]
> Sie benötigen ein Microsoft Azure-Abonnement, um Bot Service verwenden zu können. Wenn Sie noch kein Abonnement besitzen, können Sie sich für ein <a href="https://azure.microsoft.com/en-us/free/" target="_blank">kostenloses Konto</a> registrieren.

## <a name="hosting-plans"></a>Hostingpläne
Bot Service stellt zwei Hostingpläne für Ihre Bots bereit. Sie können einen Hostingplan auswählen, der Ihren Anforderungen entspricht.

### <a name="app-service-plan"></a>App Service-Plan

Ein Bot, der einen App Service-Plan verwendet, ist eine standardmäßige Azure-Web-App, die Sie so einrichten können, dass eine vordefinierte Kapazität mit vorhersagbaren Kosten und Skalierung zugewiesen wird. Ein Bot, der diesen Hostingplan verwendet, bietet folgende Möglichkeiten:

* Onlinebearbeitung des Bot-Quellcodes mithilfe eines erweiterten browserinternen Code-Editors
* Herunterladen, Debuggen und erneutes Veröffentlichen Ihres C#-Bots mithilfe von Visual Studio
* Problemloses Einrichten von Continuous Deployment für Visual Studio Online und GitHub
* Verwenden von Beispielcode, der für das Bot Framework SDK vorbereitet wurde

### <a name="consumption-plan"></a>Verbrauchsplan
Ein Bot, der einen Verbrauchsplan verwendet, ist ein serverloser Bot, der auf <a href="http://go.microsoft.com/fwlink/?linkID=747839" target="_blank">Azure Functions</a> ausgeführt wird und das Azure Functions-Preismodell für Bezahlen pro Ausführung verwendet. Ein Bot, der diesen Hostingplan verwendet, kann zur Handhabung hoher Datenverkehrsspitzen skaliert werden. Sie können den Bot-Quellcode mithilfe eines einfachen browserinternen Code-Editors online bearbeiten. Weitere Informationen zur Laufzeitumgebung eines Bots mit Verbrauchsplan finden Sie unter <a target='_blank' href='/azure/azure-functions/functions-scale'>Verbrauchs- und App Service-Pläne für Azure Functions</a>.

## <a name="templates"></a>Vorlagen

Bot Service ermöglichen Ihnen das schnelle und problemlose Erstellen eines Bots in C# oder Node.js mithilfe einer von fünf Vorlagen.

[!INCLUDE [Bot Service templates](~/includes/snippet-abs-templates.md)]

[Erfahren Sie mehr](bot-service-concept-templates.md) über die verschiedenen Vorlagen und wie diese Sie beim Erstellen von Bots unterstützen können.

## <a name="develop-and-deploy"></a>Entwickeln und Bereitstellen

Standardmäßig ermöglicht Bot Service Ihnen die Entwicklung Ihres Bots direkt im Browser mit dem Onlinecode-Editor, ohne dass eine Toolkette erforderlich ist. 

Sie können Ihren Bot lokal mit dem Bot Framework SDK und einer IDE (z.B. Visual Studio 2017) entwickeln und debuggen. Sie können Ihren Bot mithilfe von Visual Studio 2017 oder der Azure-Befehlszeilenschnittstelle direkt in Azure veröffentlichen. Sie können auch [Continuous Deployment](bot-service-continuous-deployment.md) mit dem Quellcodeverwaltungssystem Ihrer Wahl (z.B. VSTS oder GitHub) einrichten. Bei konfiguriertem Continuous Deployment können Sie das Entwickeln und Debuggen in einer IDE auf dem lokalen Computer vornehmen, und alle Codeänderungen, für die Sie ein Commit zur Quellcodeverwaltung durchführen, werden automatisch in Azure bereitgestellt.  

> [!TIP]
> Stellen Sie nach der Aktivierung von Continuous Deployment sicher, dass Ihr Code nur über Continuous Deployment und nicht über andere Mechanismen geändert wird, um Konflikte zu vermeiden.

## <a name="manage-your-bot"></a>Verwalten Ihres Bots 

Während des Prozesses zum Erstellen eines Bots mit Bot Service geben Sie einen Namen für Ihren Bot an, richten Sie dessen Hostingplan ein, wählen Sie einen Tarif aus, und konfigurieren Sie einige andere Einstellungen. Nachdem Sie Ihren Bot erstellt haben, können Sie dessen Einstellungen ändern, ihn zum Ausführen in einem oder mehreren Kanälen konfigurieren und ihn mit Web Chat testen. 

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erstellen eines Bots mit Bot Service](bot-service-quickstart.md)