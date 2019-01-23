---
ms.openlocfilehash: 8eb125b2d0f0c9981cafb763ba809c7d042d8f77
ms.sourcegitcommit: b15cf37afc4f57d13ca6636d4227433809562f8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/11/2019
ms.locfileid: "54225985"
---
Der Botdienst bietet zwei verschiedene Hostingpläne für Bots. Das Konvertieren von Quellcode aus einem Plan in den anderen ist ein manueller Vorgang.   

## <a name="app-service-plan"></a>App Service-Plan

Ein Bot, der einen App Service-Plan verwendet, ist eine standardmäßige Azure-Web-App, die Sie so einrichten können, dass eine vordefinierte Kapazität mit vorhersagbaren Kosten und Skalierung zugewiesen wird. Ein Bot, der diesen Hostingplan verwendet, bietet folgende Möglichkeiten:

* Onlinebearbeitung des Bot-Quellcodes mithilfe eines erweiterten browserinternen Code-Editors
* Herunterladen, Debuggen und erneutes Veröffentlichen Ihres C#-Bots mithilfe von Visual Studio
* Problemloses Einrichten von Continuous Deployment für Visual Studio Online und GitHub
* Verwenden von Beispielcode, der für das Bot Framework SDK vorbereitet wurde

## <a name="consumption-plan"></a>Verbrauchsplan

Ein Bot, der einen Verbrauchsplan verwendet, ist ein serverloser Bot, der auf <a href="http://go.microsoft.com/fwlink/?linkID=747839" target="_blank">Azure Functions</a> ausgeführt wird und das Azure Functions-Preismodell für Bezahlen pro Ausführung verwendet. Ein Bot, der diesen Hostingplan verwendet, kann zur Handhabung hoher Datenverkehrsspitzen skaliert werden. Sie können den Bot-Quellcode mithilfe eines einfachen browserinternen Code-Editors online bearbeiten. Weitere Informationen zur Laufzeitumgebung eines Bots mit Verbrauchsplan finden Sie unter <a target='_blank' href='/azure/azure-functions/functions-scale'>Verbrauchs- und App Service-Pläne für Azure Functions</a>.
