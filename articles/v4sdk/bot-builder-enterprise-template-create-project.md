---
title: 'Bot für Unternehmen: Erstellen eines neuen Projekts | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie auf der Grundlage der Vorlage für den Bot für Unternehmen einen neuen Bot erstellen.
author: darrenj
ms.author: darrenj
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 09/18/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: bb2f8abccc75fcc1c63589bc41289443cf1fc211
ms.sourcegitcommit: 6c719b51c9e4e84f5642100a33fe346b21360e8a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/28/2018
ms.locfileid: "52452002"
---
# <a name="enterprise-bot-template---creating-a-new-project"></a>Vorlage für den Bot für Unternehmen: Erstellen eines neuen Projekts

> [!NOTE]
> Dieses Thema gilt für die SDK-Version v4. 

Die Vorlage für den Bot für Unternehmen vereint alle Best Practices und unterstützenden Komponenten, die wir im Rahmen der Erstellung von Konversationsumgebungen ermittelt haben. Sie steht auf folgenden Bot Builder SDK-Plattformen zur Verfügung:

- .NET
- Node.js (in Kürze verfügbar)

## <a name="net"></a>.NET

Die Vorlage für den Bot für Unternehmen ist für .NET verfügbar und auf die SDK-Version **V4** ausgerichtet. Sie ist als [VSIX-Paket](https://docs.microsoft.com/en-us/visualstudio/extensibility/anatomy-of-a-vsix-package) erhältlich. Klicken Sie zum Herunterladen auf den folgenden Link:

- [Bot Builder SDK V4: Vorlage für den Bot für Unternehmen](https://aka.ms/GetEnterpriseBotTemplate)

#### <a name="prerequisites"></a>Voraussetzungen

- [Visual Studio 2017 oder höher](https://www.visualstudio.com/downloads/)
- [Azure-Konto](https://azure.microsoft.com/en-us/free/)
- [Azure PowerShell](https://docs.microsoft.com/en-us/powershell/azure/overview?view=azurermps-6.8.1)

### <a name="install-the-template"></a>Installieren der Vorlage

Öffnen Sie im gespeicherten Verzeichnis einfach das VSIX-Paket. Die Vorlage für den Bot für Unternehmen wird daraufhin in Visual Studio installiert und steht beim nächsten Öffnen zur Verfügung.

Wenn Sie ein neues Botprojekt unter Verwendung der Vorlage erstellen möchten, klicken Sie in Visual Studio auf **Datei** > **Neu** > **Projekt**, und klicken Sie anschließend in Visual C# auf **Bot Framework** > „Enterprise Bot Template“ (Vorlage für den Bot für Unternehmen). Dadurch wird lokal ein neues Botprojekt erstellt, das Sie nach Bedarf bearbeiten können. 

![„Datei“ > „Neu“ > „Projekt“ > Vorlage](media/enterprise-template/EnterpriseBot-NewProject.png)

## <a name="deploy-your-bot"></a>Bereitstellen Ihres Bots

Im Anschluss an die Projekterstellung müssen Sie die unterstützende Azure-Infrastruktur erstellen und Konfigurations-/Bereitstellungsschritte ausführen, damit der Bot direkt verwendet werden kann. Fahren Sie mit [Bereitstellen des Bots](bot-builder-enterprise-template-deployment.md) fort.

> Sie müssen diesen Schritt ausführen, da die Abhängigkeiten Ihres Bots (Azure Bot Service, Application Insights, LUIS usw.) andernfalls nicht verfügbar sind.

## <a name="customize-your-bot"></a>Anpassen Ihres Bots

Nachdem Sie sich vergewissert haben, dass die Erstellung des vorgefertigten Bots erfolgreich war, können Sie ihn für Ihr Szenario und Ihre Anforderungen anpassen. Fahren Sie mit [Anpassen des Bots](bot-builder-enterprise-template-customize.md) fort.
