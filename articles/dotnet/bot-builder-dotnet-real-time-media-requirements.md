---
title: Anforderungen und Aspekte für Echtzeit-Medienbots | Microsoft-Dokumentation
description: Verstehen Sie wichtige Anforderungen und Aspekte in Bezug auf das Erstellen von Echtzeit-Medienbots für Skype mithilfe des Bot Builder SDK für .NET.
author: ssulzer
ms.author: ssulzer
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/17
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 091c90da9b14c0abe70d08f45f528a3cce818cef
ms.sourcegitcommit: 2dc75701b169d822c9499e393439161bc87639d2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/24/2018
ms.locfileid: "42904034"
---
# <a name="requirements-and-considerations-for-real-time-media-bots"></a>Anforderungen und Aspekte für Echtzeit-Medienbots

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

Nicht alle für die Entwicklung von Messaging- und IVR-Anrufbots geltenden Hinweise gelten gleichermaßen für die Erstellung von Echtzeit-Medienbots. In diesem Artikel werden einige der wichtigen Anforderungen und Aspekte für das Entwickeln von Echtzeit-Medienbots beschrieben. 

> [!NOTE]
> Da es sich bei der Real-Time Media Platform for Bots um eine Preview-Technologie handelt, können die Inhalte in diesem Artikel geändert werden.

## <a name="real-time-media-bot-development-requires-cnet-and-windows-server"></a>Für die Entwicklung von Echtzeit-Medienbots sind C#/.NET und Windows Server erforderlich

- Der Echtzeit-Medienbot benötigt die (über <a href="https://www.nuget.org/" target="_blank">NuGet</a> verfügbare) .NET-Bibliothek `Microsoft.Skype.Bots.Media` für den Zugriff auf die Audio- und Videomedien, und der Bot muss auf einem Windows Server-Computer (oder unter dem Windows Server-Gastbetriebssystem in Azure) bereitgestellt werden. Aus diesem Grund muss der Bot mit C# und dem standardmäßigen .NET Framework entwickelt und in Azure bereitgestellt werden. C++- und Node.js-APIs können nicht zum Zugreifen auf Echtzeitmedien verwendet werden.

- Der Echtzeit-Medienbot muss auf einer aktuellen Version der .NET-Bibliothek `Microsoft.Skype.Bots.Media` ausgeführt werden. Der Bot sollte entweder die neueste verfügbare Version des <a href="https://www.nuget.org/" target="_blank">NuGet</a>-Pakets oder eine Version verwenden, die nicht älter als drei Monate ist. Ältere Versionen der Bibliothek sind veraltet und werden nach einigen Monaten nicht mehr funktionieren.

## <a name="real-time-media-calls-stay-on-the-machine-where-they-were-created"></a>Echtzeit-Medienanrufe verbleiben auf dem Computer, auf dem sie erstellt wurden

- Echtzeit-Medienbots sind sehr zustandsbehaftet. Der Echtzeit-Medienanruf wird an die Instanz des virtuellen Computers (Virtual Machine, VM) angeheftet, die den eingehenden Anruf angenommen hat: Sprach- und Videomedien vom Skype-Anrufer fließen zu dieser VM-Instanz, und Medien, die der Bot an den Anrufer zurücksendet, müssen auch von diesem VM stammen.

- Wenn Echtzeit-Medienanrufe stattfinden, wenn der virtuelle Computer beendet wird, werden diese Anrufe abrupt beendet. Wenn der Bot vom ausstehenden Herunterfahren des virtuellen Computers Kenntnis hat, kann er versuchen, die Anrufe „kontrolliert“ zu beenden.

## <a name="real-time-media-bots-must-be-directly-accessible-on-the-internet"></a>Echtzeit-Medienbots müssen direkt über das Internet zugänglich sein

- Auf jede VM-Instanz, die einen Echtzeit-Medienbot hostet, muss direkt über das Internet zugegriffen werden können. Für in Azure gehostete Echtzeit-Medienbots muss jede VM-Instanz über eine öffentliche IP-Adresse auf Instanzebene (Instance-Level Public IP Address, ILPIP) verfügen. Informationen zum Abrufen und Konfigurieren einer ILPIP finden Sie unter <a href="/azure/virtual-network/virtual-networks-instance-level-public-ip" target="_blank">Übersicht über die öffentliche IP-Adresse (klassisch) auf Instanzebene</a>. Ein Azure-Abonnement kann standardmäßig bis zu 5 ILPIP-Adressen erhalten. Wenden Sie sich an den Azure-Support, um das Kontingent Ihres Abonnements zu erhöhen.

- Aufgrund der erforderlichen öffentlichen IP-Adresse müssen Echtzeit-Medienbots entweder auf einem virtuellen „IaaS“ Azure-Computer oder in einem „klassischen“ Azure-Clouddienst gehostet werden. Andere Laufzeitumgebungen, z. B. Bot Service oder Service Fabric mit VM Scale Sets werden nicht unterstützt, da diese ILPIP nicht unterstützen.

- Echtzeit-Medienbots werden auch nicht vom [Bot Framework-Emulator](../bot-service-debug-emulator.md) unterstützt.

## <a name="scalability-and-performance-considerations"></a>Skalierbarkeits- und Leistungsaspekte

- Echtzeit-Medienbots benötigen mehr Compute- und Netzwerkkapazität (Bandbreite) als Messagingbots und können wesentlich höhere Betriebskosten verursachen. Entwickler von Echtzeit-Medienbots müssen die Skalierbarkeit des Bots sorgfältig messen und sicherstellen, dass der Bot nicht mehr gleichzeitige Anrufe annimmt, als er verwalten kann. Ein videofähiger Bot kann maximal zwei gleichzeitige Echtzeit-Medienanrufe pro CPU-Kern bewältigen.

- Für die aktuelle Vorschauversion der Real-Time Media Platform for Bots gelten bestimmte Skalierbarkeitsgrenzen, die dem Botentwickler bekannt sein müssen (diese werden in zukünftigen Versionen möglicherweise verbessert): 
  1. Für eine einzelne VM-Instanz dürfen nicht mehr als 10 Videosockets gleichzeitig erstellt werden.
  2. Die Real-Time Media Platform kann aktuell keinen auf dem virtuellen Computer verfügbaren Grafikprozessor (Graphics Processing Unit, GPU) nutzen, um die H.264-Videocodierung/-decodierung auszulagern. Stattdessen erfolgen die Videocodierung und die Videodecodierung in der Software auf der CPU. Wenn ein Grafikprozessor verfügbar ist, kann der Bot diesen für das eigene Grafik-Rendering nutzen (z. B. wenn der Bot ein 3D-Grafikmodul verwendet).

- Die den Echtzeit-Medienbot hostende VM-Instanz muss über mindestens 2 CPU-Kerne verfügen. Für Azure wird ein virtueller Computer der Dv2-Serie empfohlen. Ausführliche Informationen zu Azure-VM-Typen finden Sie in der <a href="/azure/virtual-machines/windows/sizes-general" target="_blank">Azure-Dokumentation</a>. 
