---
title: Bereitstellen Ihres Bots in Azure | Microsoft Docs
description: Stellen Sie Ihren Bot in der Azure-Cloud bereit.
keywords: Bereitstellen des Bots, Azure-Bereitstellung, Botkanalregistrierung, Veröffentlichen aus Visual Studio
author: ivorb
ms.author: v-ivorb
manager: kamrani
ms.topic: get-started-article
ms.prod: bot-framework
ms.date: 05/14/2018
ms.openlocfilehash: 70a3b7f093bb80dd16c854c65331c141fbba3725
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39303824"
---
# <a name="deploy-your-bot-to-azure"></a>Bereitstellen Ihres Bots in Azure

Nachdem Sie Ihren Bot erstellt und lokal überprüft haben, können Sie ihn in Azure pushen, um ihn von überall aus zugänglich zu machen. Zu diesem Zweck stellen Sie den Bot zunächst in einem App Service in Azure bereit und konfigurieren ihn dann mit dem Azure Bot Service über das Element Botkanalregistrierung.

## <a name="publish-from-visual-studio"></a>Veröffentlichen mit Visual Studio

Verwenden Sie Visual Studio, um Ihre Ressourcen in Azure zu erstellen und Ihren Code zu veröffentlichen.

Klicken Sie im Fenster des Projektmappen-Explorers mit der rechten Maustaste auf den Knoten Ihres Projekts, und wählen Sie „Veröffentlichen“ aus.

![Veröffentlichungseinstellung](media/azure-bot-quickstarts/getting-started-publish-setting.png)

2. Stellen Sie im Dialogfeld „Veröffentlichungsziel“ sicher, dass **App Service** auf der linken Seite und **Neu erstellen** auf der rechten Seite ausgewählt ist.

3. Klicken Sie auf die Schaltfläche „Veröffentlichen“.

4. Stellen Sie in der oberen rechten Ecke des Dialogfelds sicher, dass das Dialogfeld die richtige Benutzer-ID für Ihr Azure-Abonnement anzeigt.

![Veröffentlichen von „main“](media/azure-bot-quickstarts/getting-started-publish-main.png)

5. Geben Sie den App-Namen, das Abonnement, die Ressourcengruppe und die Hostingplaninformationen ein.

6. Wenn Sie fertig sind, klicken Sie auf „Erstellen“. Das Abschließen dieses Vorgangs kann einige Minuten dauern.

7. Nach Abschluss des Vorgangs wird ein Webbrowser geöffnet, der die öffentliche URL Ihres Bots anzeigt.

8. Erstellen Sie eine Kopie dieser URL (sie ähnelt https://<yourbotname>.azurewebsites.net/).

> [!NOTE] 
> Sie müssen die HTTPS-Version der URL verwenden, wenn Sie Ihren Bot registrieren. Azure bietet SSL-Unterstützung mit Azure App Service.

## <a name="create-your-bot-channels-registration"></a>Erstellen Ihrer Botkanalregistrierung
Wenn Sie Ihren Bot in Azure bereitgestellt haben, müssen Sie ihn beim Azure Bot Service registrieren.

1. Greifen Sie auf das Azure-Portal unter https://portal.azure.com zu.

2. Melden Sie sich mit der gleichen Identität an, die Sie zuvor aus Visual Studio verwendet haben, um Ihren Bot zu veröffentlichen.

3. Klicken Sie auf „Ressource erstellen“.

4. Geben Sie im Feld „Marketplace durchsuchen“ die Angabe „Botkanalregistrierung“ ein, und drücken Sie die EINGABETASTE.

5. Wählen Sie in der zurückgegebenen Liste „Botkanalregistrierung“ aus:

![Veröffentlichen](media/azure-bot-quickstarts/getting-started-bot-registration.png)

6. Klicken Sie auf dem Blatt, das geöffnet wird, auf „Erstellen“.

7. Geben Sie einen Namen für Ihren Bot an.

8. Wählen Sie das gleiche Abonnement aus, in dem Sie den Code Ihres Bots bereitgestellt haben.

9. Wählen Sie Ihre vorhandene Ressourcengruppe aus, die den Speicherort festlegt.

10. Sie können den F0-Tarif für Entwicklung und Tests auswählen.

11. Geben Sie die URL Ihres Bots ein. Stellen Sie sicher, dass Sie mit HTTPS beginnen und /api/messages hinzufügen. Beispiel: https://yourbotname.azurewebsites.net/api/messages

12. Sie können Application Insights vorerst deaktivieren.

13. Klicken Sie auf die Microsoft App-ID und das Kennwort.

14. Klicken Sie auf dem neuen Blatt auf „Neu erstellen“.

15. Klicken Sie auf dem neuen Blatt, das auf der rechten Seite geöffnet wird, auf „App-ID im App-Registrierungsportal erstellen“. Eine neue Browserregisterkarte wird geöffnet.

![Bot-MSA](media/azure-bot-quickstarts/getting-started-msa.png)

16. Erstellen Sie auf der neuen Registerkarte eine Kopie der App-ID, und speichern sie diese. 

17. Klicken Sie auf die Schaltfläche „Zum Fortfahren App-Kennwort generieren“.

18. Ein Browserdialogfeld wird geöffnet und stellt das Kennwort Ihrer App bereit. Dies ist der einzige Zeitpunkt, zu dem es angezeigt wird. Kopieren und speichern Sie dieses Kennwort an einem beliebigen Speicherort, auf den Sie später zugreifen können.

19. Klicken Sie auf „OK“, nachdem Sie das Kennwort gespeichert haben.

20. Schließen Sie die Browserregisterkarte, und kehren Sie zur Registerkarte „Azure-Portal“ zurück.

21. Fügen Sie Ihre App-ID und das Kennwort in die entsprechenden Felder ein, und klicken Sie auf „OK“.

22. Klicken Sie jetzt auf „Erstellen“, um Ihre Kanalregistrierung einzurichten. Dieser Vorgang kann in wenigen Sekunden erfolgen oder einige Minuten in Anspruch nehmen.

## <a name="update-your-bots-application-settings"></a>Aktualisieren der Anwendungseinstellungen Ihres Bots
Damit sich Ihr Bot beim Azure Bot Service authentifizieren kann, müssen Sie zwei Einstellungen zu den Anwendungseinstellungen Ihres Bots in Azure App Service hinzufügen. 

1. Klicken Sie auf „App Services“. Geben Sie den Namen Ihres Bots in das Textfeld „Abonnements“ ein. Klicken Sie dann in der Liste auf den Namen Ihres Bots.

![App Service](media/azure-bot-quickstarts/getting-started-app-service.png)

2. Suchen Sie in der Liste der Optionen auf der linken Seite in den Optionen Ihres Bots im Abschnitt „Einstellungen“ nach „Anwendungseinstellungen“, und klicken Sie darauf.

![Bot-ID](media/azure-bot-quickstarts/getting-started-app-settings-1.png)

3. Scrollen Sie bis zum Abschnitt „Anwendungseinstellungen“.

![Bot-MSA](media/azure-bot-quickstarts/getting-started-app-settings-2.png)

4. Klicken Sie auf „Neue Einstellung hinzufügen“.

5. Geben Sie **MicrosoftAppId** als Namen und Ihre App-ID als Wert ein.

6. Klicken Sie auf „Neue Einstellung hinzufügen“.

7. Geben Sie **MicrosoftAppPassword** als Namen und Ihr Kennwort als Wert ein.

8. Klicken Sie oben auf die Schaltfläche „Speichern“.

## <a name="test-your-bot-in-production"></a>Testen Ihres Bots in der Produktion
An diesem Punkt können Sie Ihren Bot aus Azure unter Verwendung des integrierten Webchatclients testen.

1. Navigieren Sie im Portal zurück zu Ihrer Ressourcengruppe.

2. Öffnen Sie die Botregistrierung.

3. Wählen Sie unter „Botverwaltung“ die Option „Testen“ in Webchat aus.

![Testen in Webchat](media/azure-bot-quickstarts/getting-started-test-webchat.png)

4. Geben Sie eine Nachricht wie `Hi` ein, und drücken Sie die EINGABETASTE. Der Bot gibt `Turn 1: You sent Hi` zurück.
