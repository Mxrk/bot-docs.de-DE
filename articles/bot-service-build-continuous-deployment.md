---
title: Konfigurieren von Continuous Deployment für Bot Service | Microsoft-Dokumentation
description: Informationen zur Einrichtung von Continuous Deployment aus der Quellcodeverwaltung für Bot Service
keywords: Continuous Deployment, veröffentlichen, bereitstellen, Azure-Portal
author: v-ducvo
ms.author: v-ducvo
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 03/08/2018
ms.openlocfilehash: 596d264c4df72959c71ab353e5038175fc2bed31
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39301869"
---
# <a name="set-up-continuous-deployment"></a>Einrichten der fortlaufenden Bereitstellung

Continuous Deployment ermöglicht es Ihnen, Ihren Bot lokal zu entwickeln. Continuous Deployment ist nützlich, wenn Ihr Bot in eine Quellcodeverwaltung wie **GitHub** oder **Visual Studio Team Services** eingecheckt ist. Wenn Sie Änderungen in Ihrem Quellrepository einfügen, werden diese automatisch in Azure bereitgestellt.

> [!NOTE]
> Nachdem Continuous Deployment eingerichtet ist, ist der [Onlinecode-Editor](bot-service-build-online-code-editor.md) *schreibgeschützt*.

In diesem Artikel erfahren Sie, wie Continuous Deployment für **GitHub** und **Visual Studio Team Services** eingerichtet wird.

## <a name="continuous-deployment-using-github"></a>Continuous Deployment unter Verwendung von GitHub

So richten Sie Continuous Deployment unter Verwendung von GitHub ein:

1. [Forken](https://help.github.com/articles/fork-a-repo/) Sie das GitHub-Repository, das den Code enthält, den sie in Azure bereitstellen möchten.
2. Klicken Sie im Azure-Portal auf das **Build**-Blatt Ihres Bots und anschließend auf **Konfigurieren von Continuous Deployment**. 
3. Klicken Sie auf **Setup**.
   
   ![Continuous Deployment einrichten](~/media/azure-bot-build/continuous-deployment-setup.png)

4. Klicken Sie auf **Quelle auswählen**, und wählen Sie **GitHub** aus.

   ![GitHub auswählen](~/media/azure-bot-build/continuous-deployment-setup-github.png)

5. Klicken Sie auf **Autorisierung**, anschließend auf die Schaltfläche **Autorisieren**, und folgen Sie den Anweisungen, um Azure den Zugriff auf Ihr GitHub-Konto zu erlauben.

Die Einrichtung von Continuous Deployment mit GitHub ist abgeschlossen. Wenn Sie einen Commit ausführen, werden Ihre Änderungen automatisch in Azure bereitgestellt.

## <a name="continuous-deployment-using-visual-studio"></a>Continuous Deployment mit Visual Studio

1. Klicken Sie auf dem Blatt **Build** Ihres Bots auf **Konfigurieren von Continuous Deployment**. 
2. Klicken Sie auf **Setup**.
   
   ![Continuous Deployment einrichten](~/media/azure-bot-build/continuous-deployment-setup.png)

3. Klicken Sie auf **Quelle auswählen**, und wählen Sie **Visual Studio Team Services** aus.

   ![Visual Studio Team Services auswählen](~/media/azure-bot-build/continuous-deployment-setup-vs.png)

4. Klicken Sie auf **Konto auswählen**, und wählen Sie ein Konto aus.

> [!NOTE]
> Wenn Ihr Konto nicht angezeigt wird, stellen Sie sicher, dass es mit Ihrem Azure-Abonnement verknüpft ist.
> Weitere Informationen finden Sie unter [Linking your VSTS account to your Azure subscription (Verknüpfen Ihres VSTS-Kontos mit Ihrem Azure-Konto)](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App#linking-your-vsts-account-to-your-azure-subscription).

5. Klicken Sie auf **Projekt auswählen**, und wählen Sie ein Projekt aus.

> [!NOTE]
> Nur Git-Projekte von VSTS werden unterstützt.

6. Klicken Sie auf **Branch auswählen**, und wählen Sie einen Branch aus.
7. Klicken Sie auf **OK**, um den Setupvorgang abzuschließen.

   ![Visual Studio-Konfiguration](~/media/azure-bot-build/continuous-deployment-setup-vs-configuration.png)

Das Setup von Continuous Deployment mit Visual Studio Team Services ist abgeschlossen. Wenn Sie einen Commit ausführen, werden Ihre Änderungen automatisch in Azure bereitgestellt.

## <a name="disable-continuous-deployment"></a>Deaktivieren der fortlaufenden Bereitstellung

Während Ihr Bot für Continuous Deployment konfiguriert wird, können Sie den Onlinecode-Editor nicht verwenden, um Änderungen an Ihrem Bot vorzunehmen. Wenn Sie den Onlinecode-Editor verwenden möchten, können Sie Continuous Deployment vorübergehend deaktivieren.

Führen Sie folgende Schritte aus, um Continuous Deployment zu deaktivieren:

1. Klicken Sie auf dem Blatt **Build** Ihres Bots auf **Konfigurieren von Continuous Deployment**. 
2. Klicken Sie auf **Trennen**, um Continuous Deployment zu deaktivieren. Um Continuous Deployment erneut zu aktivieren, wiederholen Sie die entsprechenden Schritte aus den zuvor genannten Abschnitten.

## <a name="next-steps"></a>Nächste Schritte
Da Ihr Bot nun für Continuous Deployment eingerichtet ist, können Sie Ihren Code online mit Webchat testen.

> [!div class="nextstepaction"]
> [Testen in Webchat](bot-service-manage-test-webchat.md)
