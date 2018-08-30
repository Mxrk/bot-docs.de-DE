---
title: Bereitstellen eines Echtzeit-Medien-Bots für Skype in Azure | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie mithilfe der in Visual Studio integrierten Veröffentlichungsfunktion einen Echtzeit-Audio-Video-Bot für Skype in Azure bereitstellen.
author: MalarGit
ms.author: malarch
manager: ssulzer
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/17
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: add83b0534ff950e9e7dd5c97a970d251b9c8fea
ms.sourcegitcommit: 2dc75701b169d822c9499e393439161bc87639d2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/24/2018
ms.locfileid: "42904116"
---
# <a name="deploy-a-real-time-media-bot-from-visual-studio-to-azure"></a>Bereitstellen eines Echtzeit-Medien-Bots in Azure über Visual Studio

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

Echtzeit-Medien-Bots können entweder auf einem virtuellen IaaS-Azure-Computer oder in einem klassischen Azure-Clouddienst gehostet werden. In diesem Artikel wird gezeigt, wie Sie über Visual Studio und die integrierte Veröffentlichungsfunktion einen Bot bereitstellen, der in einer Workerrolle eines Azure-Clouddiensts gehostet wird.

## <a name="prerequisites"></a>Voraussetzungen

Sie benötigen ein Microsoft Azure-Abonnement, um einen Bot in Azure bereitstellen zu können. Wenn Sie noch kein Abonnement besitzen, können Sie sich für ein <a href="https://azure.microsoft.com/en-us/free/" target="_blank">kostenloses Konto</a> registrieren. Darüber hinaus ist für das in diesem Artikel beschriebene Verfahren Visual Studio erforderlich. Sie können <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2017 Community</a> kostenlos herunterladen, falls Sie Visual Studio noch nicht erworben haben.

### <a name="certificate-from-a-valid-certificate-authority"></a>Zertifikat von einer gültigen Zertifizierungsstelle
Der Bot muss mit einem gültigen Zertifikat von einer vertrauenswürdigen Zertifizierungsstelle (ZS) konfiguriert werden. Der Antragstellername oder der letzte Eintrag des alternativen Antragstellernamens des Zertifikats muss der Name des Clouddiensts sein. Platzhalterzertifikate werden derzeit nicht unterstützt. Wenn mit einem CNAME-Eintrag auf den Clouddienst verwiesen wird, muss CNAME der Antragstellername oder der letzte Eintrag des alternativen Antragstellernamens des Zertifikats sein.

## <a name="configure-application-settings"></a>Konfigurieren von Anwendungseinstellungen
Damit Ihr Bot in der Cloud ordnungsgemäß funktioniert, müssen seine Anwendungseinstellungen korrekt sein. Genauer gesagt: Legen Sie die folgenden Schlüsselwerte in der Datei „app.config“ der Workerrolle fest:
> <ul><li>MicrosoftAppId</li><li>MicrosoftAppPassword</li></ul>

> [!NOTE]
> Wie Sie **AppID** und **AppPassword** für Ihren Bot finden, ist unter [MicrosoftAppID und MicrosoftAppPassword](~/bot-service-manage-overview.md#microsoftappid-and-microsoftapppassword) beschrieben.

## <a name="create-worker-role-in-the-azure-portal"></a>Erstellen einer Workerrolle im Azure-Portal
### <a name="step-1-create-cloud-serviceclassic"></a>Schritt 1: Erstellen eines Clouddiensts (klassisch)
Melden Sie sich beim <a href="https://portal.azure.com">Azure-Portal</a> an. Klicken Sie auf der linken Seite des Bildschirms auf **+** und dann auf **Clouddienste (klassisch)**. Geben Sie die erforderlichen Informationen ins Formular ein, und klicken Sie auf **Erstellen**.

![Erstellen des Clouddiensts](../media/real-time-media-bot-portal-service-creation.png)

> [!NOTE]
> Der DNS-Name des Bots sollte in der URL für die Bot-Registrierung angegeben werden.

### <a name="step-2-upload-the-certificate-for-the-bot"></a>Schritt 2: Hochladen des Zertifikats für den Bot
Laden Sie nach dem Erstellen des Bots das Zertifikat für ihn hoch.

![Hochladen des Zertifikats](../media/real-time-media-bot-portal-certificates.png)

## <a name="modify-service-configuration-with-worker-role-details"></a>Ändern der Dienstkonfiguration mit den Details der Workerrolle
Der vollqualifizierte Domänenname (Fully-Qualified Domain Name, FQDN) des Bots ist nicht über die Azure-APIs für die Rollenumgebung verfügbar. Aus diesem Grund muss für den Bot der FQDN angegeben werden. Darüber hinaus muss das Zertifikat für HTTPS bereitgestellt werden. Diese Angaben können in der Dienstkonfigurationsdatei (.cscfg) der Workerrolle konfiguriert werden.

> [!TIP]
> Gehen Sie beim Bereitstellen eines Beispiels über das Git-Repository „BotBuilder-RealTimeMediaCalling“ wie folgt vor:
> - Ersetzen Sie „$DnsName$“ entweder durch den Clouddienstnamen oder durch den CNAME-Eintrag, sofern einer in der Dienstkonfiguration verwendet wird:
>   ```xml
>      <Setting name="ServiceDnsName" value="$DnsName$" />
>   ```
> 
> - Ersetzen Sie in den folgenden Zeilen der Konfiguration „$CertThumbprint$“ durch den Fingerabdruck des in den Bot hochgeladenen Zertifikats:
>   ```xml
>      <Setting name="DefaultCertificate" value="$CertThumbprint$" />
>      <Certificate name="Default" thumbprint="$CertThumbprint$" thumbprintAlgorithm="sha1" />
>   ```

## <a name="publish-the-bot-from-visual-studio"></a>Veröffentlichen des Bots über Visual Studio
### <a name="step-1-launch-the-microsoft-azure-publishing-wizard-in-visual-studio"></a>Schritt 1: Starten des Microsoft Azure-Veröffentlichungs-Assistenten in Visual Studio

Öffnen Sie Ihr Projekt in Visual Studio. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Clouddienstprojekt, und wählen Sie **Veröffentlichen** aus. Dadurch wird der Microsoft Azure-Veröffentlichungs-Assistent gestartet. Melden Sie sich mit Ihren Anmeldeinformationen beim entsprechenden Abonnement an.

![Klicken mit der rechten Maustaste auf das Projekt und Auswählen von „Veröffentlichen“, um den Microsoft Azure-Veröffentlichungs-Assistenten zu starten](../media/real-time-media-bot-publish-signin.png)

### <a name="step-2-publish-the-bot"></a>Schritt 2: Veröffentlichen des Bots

Klicken Sie auf **Weiter**. Dadurch wird die Registerkarte **Einstellungen** geöffnet. Geben Sie den Clouddienst, die Umgebung, die Buildkonfiguration und die Dienstkonfiguration für das Bereitstellen des Bots an.

![Klicken auf „Weiter“ und Wechseln zur Registerkarte „Einstellungen“](../media/real-time-media-bot-publish-settings.png)

Optional können Sie auf **Erweiterte Einstellungen** klicken und das Speicherkonto für die Bereitstellungsprotokolle (zum Debuggen von Problemen) angeben.

![Klicken auf die Registerkarte „Erweiterte Einstellungen“](../media/real-time-media-bot-publish-advanced-settings.png)

Überprüfen Sie die Konfiguration auf der Registerkarte **Zusammenfassung**, und klicken Sie auf **Veröffentlichen**, um den Bot in Microsoft Azure bereitzustellen.
