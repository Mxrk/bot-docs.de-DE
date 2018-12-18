---
title: Konfigurieren von Continuous Deployment für Bot Service | Microsoft-Dokumentation
description: Informationen zur Einrichtung von Continuous Deployment aus der Quellcodeverwaltung für Bot Service
keywords: Continuous Deployment, veröffentlichen, bereitstellen, Azure-Portal
author: ivorb
ms.author: v-ivorb
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 12/06/2018
ms.openlocfilehash: ffbc3ef83c8fe1cd6f04697a3fff9e229df9956f
ms.sourcegitcommit: 080b9633925ffe381f2c3cf11c8f8ca4b37e2046
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2018
ms.locfileid: "53068713"
---
# <a name="set-up-continuous-deployment"></a>Einrichten der fortlaufenden Bereitstellung
Wenn Ihr Code in **GitHub** oder **Azure DevOps (früher Visual Studio Team Services)** eingecheckt ist, können Sie Continuous Deployment nutzen, um Codeänderungen aus Ihrem Quellrepository automatisch in Azure bereitzustellen. In diesem Thema richten wir Continuous Deployment für **GitHub** und **Azure DevOps** ein.

> [!NOTE]
> Das in diesem Artikel beschriebene Szenario setzt voraus, dass Sie Ihren Bot in Azure bereitgestellt haben und jetzt Continous Deployment für den Bot aktivieren möchten. Beachten Sie, dass der Onlinecode-Editor im Azure-Portal nach dem Einrichten von Continuous Deployment schreibgeschützt ist.

## <a name="continuous-deployment-using-github"></a>Continuous Deployment unter Verwendung von GitHub

Gehen Sie wie folgt vor, um Continuous Deployment mithilfe des GitHub-Repositorys einzurichten, das den in Azure bereitzustellenden Quellcode enthält:

1. Wechseln Sie im [Azure-Portal](https://portal.azure.com) zum Blatt **Alle App Service-Einstellungen** Ihres Bots, und klicken Sie auf **Bereitstellungsoptionen (klassisch)**. 

1. Klicken Sie auf **Quelle auswählen**, und wählen Sie die Option **GitHub**.

   ![GitHub auswählen](~/media/azure-bot-build/continuous-deployment-setup-github.png)

1. Klicken Sie auf **Autorisierung**, anschließend auf die Schaltfläche **Autorisieren**, und folgen Sie den Anweisungen, um Azure den Zugriff auf Ihr GitHub-Konto zu erlauben.

1. Klicken Sie auf **Projekt auswählen**, und wählen Sie ein Projekt aus.

1. Klicken Sie auf **Branch auswählen**, und wählen Sie einen Branch aus.

1. Klicken Sie auf **OK**, um den Setupvorgang abzuschließen.

Die Einrichtung von Continuous Deployment mit GitHub ist jetzt abgeschlossen. Bei jedem Commit, den Sie für das Quellcoderepository ausführen, werden Ihre Änderungen automatisch in Azure Bot Service bereitgestellt.

## <a name="continuous-deployment-using-azure-devops"></a>Continuous Deployment mit Azure DevOps

1. Wechseln Sie im [Azure-Portal](https://portal.azure.com) zum Blatt **Alle App Service-Einstellungen** Ihres Bots, und klicken Sie auf **Bereitstellungsoptionen (klassisch)**. 
2. Klicken Sie auf **Quelle auswählen**, und wählen Sie die Option **Visual Studio Team Services**. Beachten Sie, dass Visual Studio Team Services jetzt Azure DevOps Services heißt.

   ![Visual Studio Team Services auswählen](~/media/azure-bot-build/continuous-deployment-setup-vs.png)

3. Klicken Sie auf **Konto auswählen**, und wählen Sie ein Konto aus.

> [!NOTE]
> Falls Ihr Konto nicht aufgeführt ist, müssen Sie [Ihr Konto mit Ihrem Azure-Abonnement verknüpfen](https://docs.microsoft.com/en-us/azure/devops/organizations/accounts/connect-organization-to-azure-ad?view=vsts&tabs=new-nav). Beachten Sie, dass nur Git-Projekte von VSTS unterstützt werden.

4. Klicken Sie auf **Projekt auswählen**, und wählen Sie ein Projekt aus.
5. Klicken Sie auf **Branch auswählen**, und wählen Sie einen Branch aus.
6. Klicken Sie auf **OK**, um den Setupvorgang abzuschließen.

   ![Visual Studio-Konfiguration](~/media/azure-bot-build/continuous-deployment-setup-vs-configuration.png)

Die Einrichtung von Continuous Deployment mit Azure DevOps ist jetzt abgeschlossen. Wenn Sie einen Commit ausführen, werden Ihre Änderungen automatisch in Azure bereitgestellt.

## <a name="disable-continuous-deployment"></a>Deaktivieren der fortlaufenden Bereitstellung

Während Ihr Bot für Continuous Deployment konfiguriert wird, können Sie den Onlinecode-Editor nicht verwenden, um Änderungen an Ihrem Bot vorzunehmen. Wenn Sie den Onlinecode-Editor verwenden möchten, können Sie Continuous Deployment vorübergehend deaktivieren.

Führen Sie folgende Schritte aus, um Continuous Deployment zu deaktivieren:
1. Wechseln Sie im [Azure-Portal](https://portal.azure.com) zum Blatt **Alle App Service-Einstellungen** Ihres Bots, und klicken Sie auf **Bereitstellungsoptionen (klassisch)**. 
2. Klicken Sie auf **Trennen**, um Continuous Deployment zu deaktivieren. Um Continuous Deployment erneut zu aktivieren, wiederholen Sie die entsprechenden Schritte aus den zuvor genannten Abschnitten.

## <a name="additional-information"></a>Zusätzliche Informationen
- Visual Studio Team Services heißt jetzt [Azure DevOps Services](https://docs.microsoft.com/en-us/azure/devops/?view=vsts).
