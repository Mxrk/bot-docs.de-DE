---
title: Veröffentlichen eines Bot Service-Bots aus der Quellcodeverwaltung oder Visual Studio | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie einen Bot Service-Bot einmalig aus Visual Studio oder kontinuierlich aus der Quellcodeverwaltung veröffentlichen.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: abs
ms.date: 12/13/2017
ms.openlocfilehash: 38b26ed5a50409de64518562faabf532f45c857e
ms.sourcegitcommit: b78fe3d8dd604c4f7233740658a229e85b8535dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2018
ms.locfileid: "49999147"
---
# <a name="publish-a-bot-to-bot-service"></a>Veröffentlichen eines Bots in Bot Service

Nachdem Sie den Bot-Quellcode in C# aktualisiert haben, können Sie ihn mithilfe von Visual Studio in einem ausgeführten Bot in Bot Service veröffentlichen. Sie können auch den Bot-Quellcode in C# oder Node.js, der in einer integrierten Entwicklungsumgebung (IDE) geschrieben wurde, automatisch bei jedem Einchecken einer Quelldatei in einen Quellcodeverwaltungsdienst veröffentlichen.


## <a name="publish-a-bot-on-app-service-plan-from-the-online-code-editor"></a>Veröffentlichen eines Bots im App Service-Plan aus dem Onlinecode-Editor

Wenn Sie Continuous Deployment nicht konfiguriert haben, können Sie die Quelldateien im Onlinecode-Editor ändern. Führen Sie die folgenden Schritte aus, um die geänderte Quelle bereitzustellen.

4. Klicken Sie auf das Symbol „Konsole öffnen“.  
    ![Konsolensymbol](~/media/azure-bot-service-console-icon.png)
2. Geben Sie im Konsolenfenster **build.cmd** ein, und drücken Sie die EINGABETASTE.


## <a name="publish-c-bot-on-app-service-plan-from-visual-studio"></a>Veröffentlichen eines C#-Bots im App Service-Plan aus Visual Studio 

Führen Sie die folgenden Schritte aus, um die Veröffentlichung aus Visual Studio mithilfe der Datei `.PublishSettings` einzurichten:

1. Klicken Sie im Azure-Portal auf Ihren Botdienst, klicken Sie auf die Registerkarte **ERSTELLEN**, und klicken Sie dann auf **ZIP-Datei herunterladen**.
3. Extrahieren Sie den Inhalt der heruntergeladenen ZIP-Datei in einen lokalen Ordner.
4. Suchen Sie im Explorer nach der Visual Studio-Projektmappendatei (SLN) für Ihren Bot, und doppelklicken Sie darauf.
4. Klicken Sie in Visual Studio auf **Ansicht** und dann auf **Projektmappen-Explorer**.
5. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt, und klicken Sie auf **Veröffentlichen...** Das Fenster „Veröffentlichen“ wird geöffnet. 
6. Klicken Sie im Fenster „Veröffentlichen“ auf **Neues Profil erstellen**, auf **Profil importieren** und dann auf **OK**.
7. Navigieren Sie zu Ihrem Projektordner und dann zum Ordner **PostDeployScripts**. Wählen Sie die Datei aus, die auf `.PublishSettings` endet, und klicken Sie auf **Öffnen**.

Sie haben jetzt die Veröffentlichung für dieses Projekt konfiguriert. Um den lokalen Quellcode in Bot Service zu veröffentlichen, klicken Sie mit der rechten Maustaste auf das Projekt, klicken Sie auf **Veröffentlichen...**, und klicken Sie dann auf die Schaltfläche **Veröffentlichen**. 

## <a name="set-up-continuous-deployment"></a>Einrichten der fortlaufenden Bereitstellung

Standardmäßig ermöglicht Bot Service Ihnen die Entwicklung Ihres Bots direkt im Browser mit dem Azure-Editor, ohne dass ein lokaler Editor oder eine lokale Quellcodeverwaltung erforderlich ist. Der Azure-Editor ermöglicht jedoch keine Verwaltung von Dateien innerhalb Ihrer Anwendung (z.B. das Hinzufügen, Umbenennen oder Löschen von Dateien). Wenn Sie die Möglichkeit zur Verwaltung von Dateien in Ihrer Anwendung haben möchten, können Sie Continuous Deployment einrichten und die integrierte Entwicklungsumgebung (IDE) sowie das Quellcodeverwaltungssystem Ihrer Wahl (z.B. Visual Studio Team, GitHub, Bitbucket) verwenden. Bei konfiguriertem Continuous Deployment werden alle Codeänderungen, für die Sie ein Commit zur Quellcodeverwaltung durchführen, automatisch in Azure bereitgestellt. Nachdem Sie Continuous Deployment konfiguriert haben, können Sie [Ihren Bot lokal debuggen](bot-service-debug-bot.md).

> [!NOTE]
> Wenn Sie Continuous Deployment für Ihren Bot aktivieren, müssen Sie Codeänderungen in Ihren Quellcodeverwaltungsdienst einchecken. Wenn Sie Ihren Code im Azure-Editor erneut bearbeiten möchten, müssen Sie [Continuous Deployment deaktivieren](#disable-continuous-deployment).

Mithilfe der folgenden Schritte können Sie Continuous Deployment für Ihre Bot-App aktivieren.

## <a name="set-up-continuous-deployment-for-a-bot-on-an-app-service-plan"></a>Einrichten von Continuous Deployment für einen Bot in einem App Service-Plan

Dieser Abschnitt beschreibt, wie Sie Continuous Deployment für einen Bot aktivieren, den Sie mithilfe von Bot Service erstellt haben und der einen App Service-Hostingplan aufweist.

1. Suchen Sie im Azure-Portal nach Ihrem Azure-Bot, klicken Sie auf die Registerkarte **ERSTELLEN**, und suchen Sie den Abschnitt **Continuous Deployment aus Quellcodeverwaltung**.
2. Bei Visual Studio Online oder GitHub geben Sie ein Zugriffstoken an, das für Sie auf diesen Websites ausgegeben wird. Ihre Quelle wird aus Azure in Ihr Quellrepository abgerufen.
3. Bei anderen Quellcodeverwaltungssystemen wählen Sie **andere** aus, und führen Sie die daraufhin angezeigten Schritte aus. 
3. Klicken Sie auf **Aktivieren**.  

### <a name="create-an-empty-repository-and-download-bot-source-code"></a>Erstellen eines leeren Repositorys und Herunterladen des Bot-Quellcodes

Führen Sie die folgenden Schritte aus, wenn Sie einen *anderen* Quellcodeverwaltungsdienst als Visual Studio Online oder GitHub verwenden möchten. Visual Studio Online und GitHub rufen den Quellcode für Ihren Bot aus Azure ab, sodass Benutzer dieser beiden Dienste die folgenden Schritte überspringen können.

3. Suchen Sie für einen Bot in einem App Service-Plan die Seite für den Bot in Azure, klicken Sie auf die Registerkarte **ERSTELLEN**, suchen Sie den Abschnitt **Quellcode herunterladen**, und klicken Sie auf **ZIP-Datei herunterladen**.
1. Erstellen Sie ein leeres Repository in einem der Quellcodeverwaltungssysteme, die Azure unterstützt.

    ![Quellcodeverwaltungssystem](~/media/continuous-integration-sourcecontrolsystem.png)

3. Extrahieren Sie den Inhalt der heruntergeladenen ZIP-Datei in den lokalen Ordner, in dem Ihre Bereitstellungsquelle synchronisiert werden soll.
4. Klicken Sie auf **Konfigurieren**, und führen Sie die daraufhin angezeigten Schritte aus. 

## <a name="set-up-continuous-deployment-for-a-bot-on-a-consumption-plan"></a>Einrichten von Continuous Deployment für einen Bot in einem Verbrauchsplan 

Wählen Sie die Bereitstellungsquelle für Ihren Bot aus, und stellen Sie eine Verbindung mit Ihrem Repository her. 

1. Klicken Sie im Azure-Portal in Ihrem Azure-Bot auf die Registerkarte **EINSTELLUNGEN**, und klicken Sie dann auf **Konfigurieren**, um den Abschnitt **Continuous Deployment** zu erweitern.  
2. Führen Sie die Schritte aus, und klicken Sie auf das Kontrollkästchen, um zu bestätigen, dass Sie bereit sind. 
3. Klicken Sie auf **Konfigurieren**, wählen Sie die Bereitstellungsquelle aus, die dem Quellcodeverwaltungssystem entspricht, in dem Sie zuvor das leere Repository erstellt haben, und führen Sie die Schritte zum Herstellen einer Verbindung aus.   


## <a name="disable-continuous-deployment"></a>Deaktivieren der fortlaufenden Bereitstellung 

Wenn Sie Continuous Deployment deaktivieren, ist der Quellcodeverwaltungsdienst weiter funktionsfähig, aber von Ihnen eingecheckte Änderungen werden nicht automatisch in Azure veröffentlicht. Zum Deaktivieren von Continuous Deployment führen Sie die folgenden Schritte aus:

1. Wenn Ihr Bot über einen App Service-Hostingplan verfügt, suchen Sie im Azure-Portal nach Ihrem Azure-Bot, klicken Sie auf die Registerkarte **ERSTELLEN**, und suchen Sie den Abschnitt **Continuous Deployment aus Quellcodeverwaltung**. 
2. Wenn Ihr Bot über einen Verbrauchsplan verfügt, klicken Sie auf die Registerkarte **Einstellungen**, erweitern Sie den Abschnitt **Continuous Deployment**, und klicken Sie auf **Konfigurieren**.
3. Wählen Sie im Bereich **Bereitstellungen** den Quellcodeverwaltungsdienst aus, in dem Continuous Deployment aktiviert ist, und klicken Sie auf **Trennen**.  


## <a name="additional-resources"></a>Zusätzliche Ressourcen

Informationen zum lokalen Debuggen Ihres Bots nach dem Konfigurieren von Continuous Deployment finden Sie unter [Debuggen eines Bot Service-Bots](bot-service-debug-bot.md).

In diesem Artikel sind die Funktionen für Continuous Deployment von Bot Service hervorgehoben. Informationen zu Continuous Deployment im Zusammenhang mit Azure App Services finden Sie unter <a href="https://azure.microsoft.com/en-us/documentation/articles/app-service-continuous-deployment/" target="_blank">Continuous Deployment in Azure App Service</a>.
