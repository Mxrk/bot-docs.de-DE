---
title: Bereitstellen der Vorlage für den Bot für Unternehmen | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie alle unterstützenden Azure-Ressourcen für Ihren Bot für Unternehmen bereitstellen.
author: darrenj
ms.author: darrenj
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 09/18/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: a4ac88872e11cd32a9de96d52dcf9e917bb3488f
ms.sourcegitcommit: 87b5b0ca9b0d5e028ece9f7cc4948c5507062c2b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2018
ms.locfileid: "47029798"
---
# <a name="enterprise-bot-template---deploying-your-bot"></a>Vorlage für den Bot für Unternehmen: Bereitstellen des Bots

> [!NOTE]
> Dieses Thema gilt für die SDK-Version v4. 

## <a name="prerequisites"></a>Voraussetzungen

- Vergewissern Sie sich, dass der [Knotenpaket-Manager](https://nodejs.org/en/) installiert ist.

- Installieren Sie die Azure Bot Service-Befehlszeilentools (CLI). Dieser Schritt ist auch erforderlich, wenn Sie die Tools bereits verwendet haben. So ist sichergestellt, dass Sie über die neuesten Versionen verfügen.

```shell
npm install -g ludown luis-apis qnamaker botdispatch msbot luisgen chatdown
```

- Installieren Sie die Azure-Befehlszeilentools (CLI). Eine entsprechende Anleitung finden Sie [hier](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli-windows?view=azure-cli-latest).

- Installieren Sie die AZ-Erweiterung für Bot Service.
```shell
az extension add -n botservice
```

## <a name="configuration"></a>Konfiguration

- Rufen Sie Ihren LUIS-Erstellungsschlüssel ab.
   - Informationen zum korrekten LUIS-Portal für die Region, in der Sie die Bereitstellung durchführen möchten, finden Sie auf [dieser Dokumentationsseite](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/luis-reference-regions). 
   - Klicken Sie nach der Anmeldung in der rechten oberen Ecke auf Ihren Namen.
   - Klicken Sie auf „Einstellungen“, und notieren Sie sich den Erstellungsschlüssel für den nächsten Schritt.

## <a name="deployment"></a>Bereitstellung

>Falls Sie über mehrere Azure-Abonnements verfügen und sicherstellen möchten, dass bei der Bereitstellung das richtige Abonnement ausgewählt wird, führen Sie die folgenden Befehle aus, bevor Sie fortfahren.

 Melden Sie sich über den Browser bei Ihrem Azure-Konto an.
```shell
az login
az account list
az account set --subscription "YOUR_SUBSCRIPTION_NAME"
```

Für auf der Unternehmensvorlage basierende Bots werden folgende Abhängigkeiten benötigt, damit alles ordnungsgemäß funktioniert:
- Azure-Web-App
- Azure-Speicherkonto (Transkripte)
- Azure Application Insights (Telemetriedaten)
- Azure Cosmos DB (Zustand)
- Azure Cognitive Services – Sprachverständnis
- Azure Cognitive Services – QnAMaker (einschließlich Azure Search und Azure-Web-App)
- Azure Cognitive Services – Content Moderator (optionaler manueller Schritt)

Das Bereitstellungsrezept Ihres neuen Botprojekts ermöglicht es, mit dem Befehl `msbot clone services` die Bereitstellung aller oben genannten Dienste in Ihrem Azure-Abonnement zu automatisieren und sicherzustellen, dass die BOT-Datei in Ihrem Projekt mit allen Diensten, die Schlüssel enthalten, aktualisiert wird, um den reibungslosen Betrieb Ihres Bots zu gewährleisten.

> Überprüfen Sie nach der Bereitstellung die Tarife für die erstellten Dienste, und passen Sie sie an Ihr Szenario an.

Die Datei „README.md“ in Ihrem erstellten Projekt enthält eine exemplarische Befehlszeile vom Typ „msbot clone services“, die mit dem Namen Ihres erstellten Bots aktualisiert wurde. (Eine generische Version finden Sie weiter unten.) Aktualisieren Sie den Erstellungsschlüssel mit dem Schlüssel aus dem vorherigen Schritt, und wählen Sie den Standort des zu verwendenden Azure-Datencenters aus (beispielsweise „westus“ oder „westeurope“).

> Achten Sie darauf, dass der LUIS-Erstellungsschlüssel, den Sie im vorherigen Schritt abgerufen haben, für die weiter unten angegebene Region gültig ist.

```shell
msbot clone services --name "YOUR_BOT_NAME" --luisAuthoringKey "YOUR_AUTHORING_KEY" --folder "DeploymentScripts\msbotClone" --location "westus"
```

Das msbot-Tool zeigt eine Zusammenfassung des Bereitstellungsplans einschließlich Standort und SKU an. Prüfen Sie diese, bevor Sie fortfahren.

![Bereitstellungsbestätigung](./media/enterprise-template/EnterpriseBot-ConfirmDeployment.png)

>Notieren Sie sich nach Abschluss der Bereitstellung **unbedingt** das angegebene Geheimnis der BOT-Datei. Es wird in späteren Schritten benötigt.

- Aktualisieren Sie die Datei `appsettings.json` mit dem Namen und dem Geheimnis der neu erstellten BOT-Datei.
- Führen Sie den folgenden Befehl aus, rufen Sie den Instrumentierungsschlüssel (InstrumentationKey) für Ihre Application Insights-Instanz ab, und aktualisieren Sie „InstrumentationKey“ in der Datei `appsettings.json`.

`msbot list --bot YOURBOTFILE.bot --secret YOUR_BOT_SECRET`

        {
          "botFilePath": ".\\YOURBOTFILE.bot",
          "botFileSecret": "YOUR_BOT_SECRET",
          "ApplicationInsights": {
            "InstrumentationKey": "YOUR_INSTRUMENTATION_KEY"
          }
        }

## <a name="testing"></a>Testen

Führen Sie Ihr fertiges Botprojekt innerhalb Ihrer Entwicklungsumgebung aus, und öffnen Sie den Bot Framework-Emulator. Klicken Sie innerhalb des Emulators im Menü „Datei“ auf „Bot öffnen“, und navigieren Sie zu der BOT-Datei in Ihrem Verzeichnis.

Geben Sie anschließend ```hi``` ein, um zu überprüfen, ob alles funktioniert.

## <a name="deploy-to-azure"></a>Bereitstellen in Azure

Tests können vollständig lokal ausgeführt werden. Wenn Sie bereit sind, Ihren Bot für weitere Tests in Azure bereitzustellen, können Sie den folgenden Befehl verwenden, um den Quellcode zu veröffentlichen. Dieser Befehl kann jederzeit ausgeführt werden, um Quellcodeaktualisierungen zu pushen.

```shell
az bot publish -g YOUR_BOT_NAME -n YOUR_BOT_NAME --proj-file YOUR_BOT_NAME.csproj --sdk-version v4
```

## <a name="enabling-more-scenarios"></a>Ermöglichen weiterer Szenarien

Ihr Botprojekt bietet zusätzliche Funktionen, die Sie mittels folgender Schritte aktivieren können.

### <a name="authentication"></a>Authentifizierung

Wenn Sie die Authentifizierungsfunktion aktivieren möchten, führen Sie die folgenden Schritte aus, nachdem Sie in den Einstellungen Ihres Bots im Azure-Portal einen Authentifizierungsverbindungsnamen konfiguriert haben. Weitere Informationen finden Sie in der [Dokumentation](https://docs.microsoft.com/en-us/azure/bot-service/bot-builder-tutorial-authentication?view=azure-bot-service-3.0).

Registrieren Sie `SignInDialog` im MainDialog-Konstruktor:
    
`AddDialog(new SignInDialog(_services.AuthConnectionName));`

Fügen Sie Ihrem Code am gewünschten Ort Folgendes hinzu, um einen einfachen Anmeldungsablauf zu testen:
    
`var signInResult = await dc.BeginAsync(SignInDialog.Name);`

### <a name="content-moderation"></a>Inhaltsmoderation

Die Inhaltsmoderation kann zur Erkennung personenbezogener Informationen (personally identifiable information, PII) und nicht jugendfreier Inhalte in an den Bot gesendeten Nachrichten verwendet werden. Erstellen Sie zum Aktivieren dieser Funktion einen neuen Content Moderator-Dienst im Azure-Portal. Erfassen Sie Ihren Abonnementschlüssel und Ihre Region, um Ihre BOT-Datei zu konfigurieren. 

> Dieser Schritt wird später noch automatisiert.

Fügen Sie in „startup“ am Ende der Methode „service.AddBot<>()“ den folgenden Code hinzu, um die Inhaltsmoderation für jede Runde zu aktivieren. Auf das Ergebnis der Inhaltsmoderation kann über den Botzustand zugegriffen werden. 
    
```
    // Content Moderation Middleware (analyzes incoming messages for inappropriate content including PII, profanity, etc.)
    var moderatorService = botConfig.Services.Where(s => s.Name == ContentModeratorMiddleware.ServiceName).FirstOrDefault();
    if (moderatorService != null)
    {
        var moderator = moderatorService as GenericService;
        var moderatorKey = moderator.Configuration["subscriptionKey"];
        var moderatorRegion = moderator.Configuration["region"];
        var moderatorMiddleware = new ContentModeratorMiddleware(moderatorKey, moderatorRegion);
        options.Middleware.Add(moderatorMiddleware);
    }
```
Rufen Sie innerhalb Ihres Dialogstapels Folgendes auf, um auf das Middlewareergebnis zuzugreifen:
```     
    var cm = dc.Context.TurnState.Get<Microsoft.CognitiveServices.ContentModerator.Models.Screen>(ContentModeratorMiddleware.TextModeratorResultKey);
```

## <a name="customize-your-bot"></a>Anpassen Ihres Bots

Nachdem Sie sich vergewissert haben, dass die Erstellung des vorgefertigten Bots erfolgreich war, können Sie ihn für Ihr Szenario und Ihre Anforderungen anpassen. Fahren Sie mit [Anpassen des Bots](bot-builder-enterprise-template-customize.md) fort.
