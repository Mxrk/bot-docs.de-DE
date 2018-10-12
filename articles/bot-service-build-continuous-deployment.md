---
title: Konfigurieren von Continuous Deployment für Bot Service | Microsoft-Dokumentation
description: Informationen zur Einrichtung von Continuous Deployment aus der Quellcodeverwaltung für Bot Service
keywords: Continuous Deployment, veröffentlichen, bereitstellen, Azure-Portal
author: ivorb
ms.author: v-ivorb
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 09/18/2018
ms.openlocfilehash: 45c89c911106d5b6a1e250f6e6ab3d472c90ab92
ms.sourcegitcommit: 3bf3dbb1a440b3d83e58499c6a2ac116fe04b2f6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/23/2018
ms.locfileid: "46707986"
---
# <a name="set-up-continuous-deployment"></a>Einrichten der fortlaufenden Bereitstellung
Wenn Ihr Code in **GitHub** oder **Azure DevOps (früher Visual Studio Team Services)** eingecheckt ist, können Sie Continuous Deployment nutzen, um Codeänderungen aus Ihrem Quellrepository automatisch in Azure bereitzustellen. In diesem Thema richten wir Continuous Deployment für **GitHub** und **Azure DevOps** ein.

> [!NOTE]
> Nachdem Continuous Deployment eingerichtet wurde, ist der Onlinecode-Editor im Azure-Portal *SCHREIBGESCHÜTZT*.

## <a name="continuous-deployment-using-github"></a>Continuous Deployment unter Verwendung von GitHub

So richten Sie Continuous Deployment unter Verwendung von GitHub ein:

1. Verwenden Sie das GitHub-Repository mit dem Quellcode, den Sie in Azure bereitstellen möchten. Sie können entweder ein vorhandenes Repository [forken](https://help.github.com/articles/fork-a-repo/) oder ein eigenes erstellen und den zugehörigen Quellcode in Ihr GitHub-Repository hochladen.
2. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zum Blatt **Build** Ihres Bots und klicken Sie auf **Continuous Deployment konfigurieren**. 
3. Klicken Sie auf **Setup**.
   
   ![Continuous Deployment einrichten](~/media/azure-bot-build/continuous-deployment-setup.png)

4. Klicken Sie auf **Quelle auswählen**, und wählen Sie die Option **GitHub**.

   ![GitHub auswählen](~/media/azure-bot-build/continuous-deployment-setup-github.png)

5. Klicken Sie auf **Autorisierung**, anschließend auf die Schaltfläche **Autorisieren**, und folgen Sie den Anweisungen, um Azure den Zugriff auf Ihr GitHub-Konto zu erlauben.

6. Klicken Sie auf **Projekt auswählen**, und wählen Sie ein Projekt aus.

7. Klicken Sie auf **Branch auswählen**, und wählen Sie einen Branch aus.

8. Klicken Sie auf **OK**, um den Setupvorgang abzuschließen.

Die Einrichtung von Continuous Deployment mit GitHub ist jetzt abgeschlossen. Bei jedem Commit, den Sie für das Quellcoderepository ausführen, werden Ihre Änderungen automatisch in Azure Bot Service bereitgestellt.

## <a name="continuous-deployment-using-azure-devops"></a>Continuous Deployment mit Azure DevOps

1. Klicken Sie im [Azure-Portal](https://portal.azure.com) auf dem Blatt **Build** Ihres Bots auf **Continuous Deployment konfigurieren**. 
2. Klicken Sie auf **Setup**.
   
   ![Continuous Deployment einrichten](~/media/azure-bot-build/continuous-deployment-setup.png)

3. Klicken Sie auf **Quelle auswählen**, und wählen Sie die Option **Visual Studio Team Services**. Beachten Sie, dass Visual Studio Team Services jetzt Azure DevOps Services heißt.

   ![Visual Studio Team Services auswählen](~/media/azure-bot-build/continuous-deployment-setup-vs.png)

4. Klicken Sie auf **Konto auswählen**, und wählen Sie ein Konto aus.

> [!NOTE]
> Wenn Ihr Konto nicht angezeigt wird, stellen Sie sicher, dass es mit Ihrem Azure-Abonnement verknüpft ist. Navigieren Sie zum Azure-Portal, und öffnen Sie **Azure DevOps Services-Organisationen (früher Team Services)**, um Ihr Konto mit Ihrem Azure-Abonnement zu verknüpfen. Die Liste mit Ihren Organisationen unter Azure DevOps wird angezeigt. Klicken Sie auf die Organisation mit dem Quellcode des Bots, den Sie bereitstellen möchten. Die Schaltfläche **Verbindung mit AAD herstellen** wird angezeigt. Wenn die von Ihnen gewählte Organisation nicht mit Ihrem Azure-Abonnement verknüpft ist, ist diese Schaltfläche aktiv. Klicken Sie also auf diese Schaltfläche, um die Verbindung einzurichten. Es kann einige Zeit dauern, bis die Verbindungseinrichtung wirksam ist.

5. Klicken Sie auf **Projekt auswählen**, und wählen Sie ein Projekt aus.

> [!NOTE]
> Nur Git-Projekte von VSTS werden unterstützt.

6. Klicken Sie auf **Branch auswählen**, und wählen Sie einen Branch aus.
7. Klicken Sie auf **OK**, um den Setupvorgang abzuschließen.

   ![Visual Studio-Konfiguration](~/media/azure-bot-build/continuous-deployment-setup-vs-configuration.png)

Die Einrichtung von Continuous Deployment mit Azure DevOps ist jetzt abgeschlossen. Wenn Sie einen Commit ausführen, werden Ihre Änderungen automatisch in Azure bereitgestellt.

## <a name="disable-continuous-deployment"></a>Deaktivieren der fortlaufenden Bereitstellung

Während Ihr Bot für Continuous Deployment konfiguriert wird, können Sie den Onlinecode-Editor nicht verwenden, um Änderungen an Ihrem Bot vorzunehmen. Wenn Sie den Onlinecode-Editor verwenden möchten, können Sie Continuous Deployment vorübergehend deaktivieren.

Führen Sie folgende Schritte aus, um Continuous Deployment zu deaktivieren:

1. Klicken Sie auf dem Blatt **Build** Ihres Bots auf **Konfigurieren von Continuous Deployment**. 
2. Klicken Sie auf **Trennen**, um Continuous Deployment zu deaktivieren. Um Continuous Deployment erneut zu aktivieren, wiederholen Sie die entsprechenden Schritte aus den zuvor genannten Abschnitten.

## <a name="additional-information"></a>Zusätzliche Informationen
- [Azure DevOps](https://docs.microsoft.com/en-us/azure/devops/?view=vsts)
