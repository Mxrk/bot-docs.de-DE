---
title: Bereitstellen Ihres Bots mit der Azure CLI | Microsoft-Dokumentation
description: Stellen Sie Ihren Bot in der Azure-Cloud bereit.
keywords: Bot bereitstellen, in Azure bereitstellen, Bot veröffentlichen, Bot mit dem az-Tool bereitstellen, Bot mit Visual Studio bereitstellen, MSBot veröffentlichen, MSBot klonen
author: ivorb
ms.author: v-ivorb
manager: kamrani
ms.topic: get-started-article
ms.service: bot-service
ms.subservice: abs
ms.date: 12/11/2018
ms.openlocfilehash: a7cb9cb1e3df14f2f46bc5a4c3a633f5212b5dfd
ms.sourcegitcommit: 0b421ff71617f03faf55ea175fb91d1f9e348523
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/12/2018
ms.locfileid: "53286616"
---
# <a name="deploy-your-bot-using-azure-cli"></a>Bereitstellen Ihres Bots mit der Azure CLI

[!INCLUDE [pre-release-label](./includes/pre-release-label.md)]

Nachdem Sie Ihren Bot erstellt und lokal getestet haben, können Sie ihn in Azure bereitstellen, um ihn von überall aus zugänglich zu machen. Wenn Sie Ihren Bot in Azure bereitstellen, werden Ihnen die Kosten für die von Ihnen genutzten Dienste in Rechnung gestellt. Im Artikel zur [Abrechnungs- und Kostenverwaltung](https://docs.microsoft.com/en-us/azure/billing/) finden Sie Informationen zur Azure-Abrechnung, zum Überwachen der Nutzung und Kosten sowie zum Verwalten Ihres Kontos und Ihrer Abonnements.

In diesem Artikel erfahren Sie, wie Sie C#- und JavaScript-Bots mit dem `msbot`-Tool in Azure bereitstellen. Bevor Sie die Schritte ausführen, sollten Sie diesen Artikel lesen, um sich mit allen Details der Bereitstellung eines Bots vertraut zu machen.


## <a name="prerequisites"></a>Voraussetzungen
- Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.
- Installieren Sie das [.NET Core SDK](https://dotnet.microsoft.com/download)(Version 2.2 oder höher). 
- Installieren Sie die aktuelle Version des [Azure CLI-Tools](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest).
- Installieren Sie die aktuelle `botservice`-Erweiterung für das `az`-Tool. 
  - Entfernen Sie zunächst mithilfe des Befehls `az extension remove -n botservice` die alte Version. Installieren Sie anschließend mit dem Befehl `az extension add -n botservice` die aktuelle Version.
- Installieren Sie die aktuelle Version des [MSBot](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/MSBot)-Tools.
  - Sie benötigen die [LUIS CLI](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUIS#installation), wenn der Klonvorgang LUIS- oder Dispatch-Ressourcen umfasst.
  - Sie benötigen die [QnA Maker CLI](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/QnAMaker#as-a-cli), wenn der Klonvorgang QnA Maker-Ressourcen umfasst.
- Installieren Sie [Bot Framework Emulator](https://aka.ms/Emulator-wiki-getting-started).
- Installieren und konfigurieren Sie [ngrok](https://github.com/Microsoft/BotFramework-Emulator/wiki/Tunneling-%28ngrok%29).
- Kenntnisse der [BOT](v4sdk/bot-file-basics.md)-Datei.

## <a name="deploy-javascript-and-c-bots-using-az-cli"></a>Bereitstellen von JavaScript- und C#-Bots mit der Azure CLI
Sie haben bereits einen Bot erstellt und möchten ihn nun in Azure bereitstellen. Die folgenden Schritte setzen voraus, dass Sie die erforderlichen Azure-Ressourcen erstellt und die Dienstverweise in der BOT-Datei mit dem Befehl `msbot connect` oder mit Bot Framework Emulator aktualisiert haben. Wenn die BOT-Datei nicht aktuell ist, wird der Bereitstellungsprozess zwar möglicherweise ohne Fehler oder Warnungen abgeschlossen, der bereitgestellte Bot funktioniert aber nicht.

Öffnen Sie eine Eingabeaufforderung, um sich beim Azure-Portal anzumelden.

```cmd
az login
```
Ein Browserfenster wird geöffnet, in dem Sie sich anmelden können. 

### <a name="set-the-subscription"></a>Festlegen des Abonnements 
Führen Sie zum Festlegen des Abonnements den folgenden Befehl aus:

```cmd
az account set --subscription "<azure-subscription>"
``` 

Falls Sie nicht sicher, welches Abonnement Sie für die Bereitstellung des Bots verwenden müssen, können Sie die Liste der `subscriptions` für Ihr Konto mit dem Befehl `az account list` anzeigen.

Navigieren Sie zum Botordner. 
```cmd 
cd <local-bot-folder>
```

### <a name="azure-subscription-account"></a>Azure-Abonnementkonto
Je nach E-Mail-Konto, das Sie für die Anmeldung bei Azure verwenden, gelten bestimmte Anweisungen. Lesen Sie diese Anweisungen, bevor Sie fortfahren.

**MSA-E-Mail-Konto**

Wenn Sie ein [MSA](https://en.wikipedia.org/wiki/Microsoft_account)-E-Mail-Konto verwenden, müssen Sie die „appId“ und das „appSecret“ mit dem Befehl `msbot clone services` erstellen. 

- Navigieren Sie zum [Anwendungsregistrierungsportal](https://apps.dev.microsoft.com/). Klicken Sie auf **App hinzufügen**, um Ihre Anwendung zu registrieren, erstellen Sie eine **Anwendungs-ID**, und klicken Sie auf **Neues Kennwort generieren**. 
- Speichern Sie die Anwendungs-ID und das soeben generierte neue Kennwort, damit Sie diese Daten mit dem Befehl `msbot clone services` verwenden können. 
- Verwenden Sie zum Bereitstellen den Befehl, der für Ihren Bot gilt.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

`msbot clone services --folder deploymentScripts/msbotClone --location "<geographic-location>" --proj-file "<your.csproj>" --name "<bot-name>" --appid "xxxxxxxx" --password "xxxxxxx" --verbose`

[!INCLUDE [deployment note](./includes/deployment-note-cli.md)]

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)

`msbot clone services --folder deploymentScripts/msbotClone --location "<geographic-location>"   --code-dir . --name "<bot-name>" --appid "xxxxxxxx" --password "xxxxxxx" --verbose`


[!INCLUDE [deployment note](./includes/deployment-note-cli.md)]

---

**Geschäfts-, Schul- oder Unikonto**

Wenn Sie ein von Ihrem Unternehmen oder Ihrer Schule bzw. Universität bereitgestelltes E-Mail-Konto für die Anmeldung bei Azure verwenden, müssen Sie die Anwendungs-ID und das Kennwort nicht erstellen. Verwenden Sie zum Bereitstellen den Befehl, der für Ihren Bot gilt.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

`msbot clone services --folder deploymentScripts/msbotClone --location "<geographic-location>" --verbose --proj-file "<your-project-file>" --name "<bot-name>"`

[!INCLUDE [deployment note](./includes/deployment-note-cli.md)]

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)

`msbot clone services --folder deploymentScripts/msbotClone --location "<geographic-location>" --verbose --code-dir . --name "<bot-name>"`


[!INCLUDE [deployment note](./includes/deployment-note-cli.md)]

---

#### <a name="save-the-secret-used-to-decrypt-bot-file"></a>Speichern des zum Entschlüsseln der BOT-Datei verwendeten Geheimnisses
Beachten Sie, dass durch den Bereitstellungsprozess eine _neue BOT-Datei erstellt und mit einem Geheimnis verschlüsselt wird_. Während der Bot bereitgestellt wird, wird die folgende Meldung in der Befehlszeile angezeigt, um Sie zum Speichern des Geheimnisses der BOT-Datei aufzufordern. 

`The secret used to decrypt myAzBot.bot is:`
`hT6U1SIPQeXlebNgmhHYxcdseXWBZlmIc815PpK0WWA=`

`NOTE: This secret is not recoverable and you should save it in a safe place according to best security practices.
      Copy this secret and use it to open the <file.bot> the first time.`
      
Speichern Sie das Geheimnis der BOT-Datei zur späteren Verwendung. Die neue verschlüsselte BOT-Datei wird im Azure-Portal mit dem „botFileSecret“ verwendet. Falls Sie den Namen der BOT-Datei oder das Geheimnis später ändern müssen, wechseln Sie im Portal zum Abschnitt **App Service-Einstellungen -> Anwendungseinstellungen**. Beachten Sie, dass der Name der BOT-Datei in der Datei „appsettings.json“ oder der ENV-Datei mit der erstellten aktuellen Bot-Datei aktualisiert wird.  

### <a name="test-your-bot"></a>Testen Ihres Bots
Testen Sie Ihre App im Emulator mit einem Produktionsendpunkt. Wenn Sie die App lokal testen möchten, müssen Sie sicherstellen, dass Ihr Bot auf dem lokalen Computer ausgeführt wird. 

### <a name="to-update-your-bot-code-in-azure"></a>So aktualisieren Sie Ihren Botcode in Azure
Verwenden Sie NICHT den Befehl `msbot clone services`, um den Botcode in Azure zu aktualisieren. Sie müssen wie unten dargestellt den Befehl `az bot publish` verwenden:

```cmd
az bot publish --name "<your-azure-bot-name>" --proj-file "<your-proj-file>" --resource-group "<azure-resource-group>" --code-dir "<folder>" --verbose --version v4
```

| Argumente        | BESCHREIBUNG |
|----------------  |-------------|
| `name`      | Der Name, den Sie bei der ersten Bereitstellung des Bots in Azure verwendet haben.|
| `proj-file` | Für einen C#-Bot ist dies die CSPROJ-Datei. Für einen JS-/TS-Bot ist dies der Name der Startprojektdatei (z. B. „index.js“ oder „index.ts“) Ihres lokalen Bots.|
| `resource-group` | Die Azure-Ressourcengruppe, die im Befehl `msbot clone services` verwendet wurde.|
| `code-dir`  | Verweist auf den lokalen Botordner.|



## <a name="additional-resources"></a>Zusätzliche Ressourcen

Wenn Sie einen Bot bereitstellen, werden in der Regel die folgenden Ressourcen im Azure-Portal erstellt:

| Ressourcen      | BESCHREIBUNG |
|----------------|-------------|
| Web-App-Bot | Ein Azure Bot Service-Bot, der in einer Azure App Service-Instanz bereitgestellt wird.|
| [App Service](https://docs.microsoft.com/en-us/azure/app-service/)| Ermöglicht Ihnen das Erstellen und Hosten von Webanwendungen.|
| [App Service-Plan](https://docs.microsoft.com/en-us/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview)| Definiert eine Gruppe von Computeressourcen für eine auszuführende Web-App.|
| [Application Insights](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-overview)| Bietet Tools zum Erfassen und Analysieren von Telemetriedaten.|
| [Speicherkonto](https://docs.microsoft.com/en-us/azure/storage/common/storage-introduction)| Bietet hoch verfügbaren, sicheren, stabilen, skalierbaren und redundanten Cloudspeicher.|

Die Dokumentation zu `az bot`-Befehlen finden Sie in der [Referenz](https://docs.microsoft.com/en-us/cli/azure/bot?view=azure-cli-latest).

Falls Sie nicht mit Azure-Ressourcengruppen vertraut sind, lesen Sie die Erläuterung der grundlegenden [Begriffe](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-overview#terminology).

## <a name="next-steps"></a>Nächste Schritte
> [!div class="nextstepaction"]
> [Einrichten von Continuous Deployment](bot-service-build-continuous-deployment.md)
