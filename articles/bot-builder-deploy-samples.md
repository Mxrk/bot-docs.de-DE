---
title: Bereitstellen von Bots aus dem Repository „botbuilder-samples“ | Microsoft-Dokumentation
description: Stellen Sie Ihren Bot in der Azure-Cloud bereit.
keywords: Bot bereitstellen, in Azure bereitstellen, Bot veröffentlichen, Bot mit dem az-Tool bereitstellen, Bot mit Visual Studio bereitstellen, MSBot veröffentlichen, MSBot klonen
author: ivorb
ms.author: v-ivorb
manager: kamrani
ms.topic: get-started-article
ms.service: bot-service
ms.subservice: abs
ms.date: 01/15/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 78e960357d6c4dc1c9751a9921a2338f552738b0
ms.sourcegitcommit: b94361234816e6b95459f142add936732fc40344
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/15/2019
ms.locfileid: "54317614"
---
# <a name="deploy-bots-from-botbuilder-samples-repo"></a>Bereitstellen von Bots aus dem Repository „botbuilder-samples“

[!INCLUDE [pre-release-label](./includes/pre-release-label.md)]

In diesem Artikel erfahren Sie, wie Sie C#- und JavaScript-Beispiele aus dem Repository [botbuilder-samples](https://github.com/Microsoft/BotBuilder-Samples) in Azure bereitstellen.

Die Schritte zum Bereitstellen von Beispielbots _unterscheiden_ sich von den Schritten zum [Bereitstellen eines Bots, für den bei der Erstellung bereits alle Ressourcen in Azure bereitgestellt wurden](https://docs.microsoft.com/en-us/azure/bot-service/bot-builder-deploy-az-cli?view=azure-bot-service-4.0&tabs=csharp).

> [!IMPORTANT]
> Beim Bereitstellen eines Bots aus dem Repository [botbuilder-samples](https://github.com/Microsoft/BotBuilder-Samples) in Azure werden Azure-Dienste bereitgestellt, und für die Nutzung der Dienste fallen Kosten an.
> Im Artikel zur [Abrechnungs- und Kostenverwaltung](https://docs.microsoft.com/en-us/azure/billing/) finden Sie Informationen zur Azure-Abrechnung, zum Überwachen der Nutzung und Kosten sowie zum Verwalten Ihres Kontos und Ihrer Abonnements.

Bevor Sie die Schritte ausführen, sollten Sie sich diesen Artikel einmal vollständig durchlesen, um sich mit allen Details der Bereitstellung eines Bots vertraut zu machen.

## <a name="prerequisites"></a>Voraussetzungen

- Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.
- Installieren Sie das [.NET Core SDK](https://dotnet.microsoft.com/download) (Version 2.2 oder höher). Verwenden Sie `dotnet --version`, um Ihre aktuelle Version zu ermitteln.
- Installieren Sie die aktuelle Version des [Azure CLI-Tools](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest). Verwenden Sie `az --version`, um Ihre aktuelle Version zu ermitteln.
- Installieren Sie die aktuelle Version des [MSBot](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/MSBot)-Tools.
  - Sie benötigen die [LUIS CLI](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUIS#installation), wenn der Klonvorgang LUIS- oder Dispatch-Ressourcen umfasst.
  - Sie benötigen die [QnA Maker CLI](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/QnAMaker#as-a-cli), wenn der Klonvorgang QnA Maker-Ressourcen umfasst.
- Installieren Sie [Bot Framework Emulator](https://aka.ms/Emulator-wiki-getting-started).
- Installieren und konfigurieren Sie [ngrok](https://github.com/Microsoft/BotFramework-Emulator/wiki/Tunneling-%28ngrok%29).
- Machen Sie sich mit [BOT-Dateien](v4sdk/bot-file-basics.md) vertraut.

Bei Verwendung von msbot 4.3.2 und höheren Versionen ist mindestens Version 2.0.54 der Azure CLI erforderlich. Wenn Sie die botservice-Erweiterung installiert haben, entfernen Sie sie mit dem folgenden Befehl:

```cmd
az extension remove --name botservice
```

### <a name="c"></a>C\#

 `msbot clone services` lädt nur die DLL-Datei und einige andere Dateien in Azure hoch, nicht aber Ihre Codedateien. Das Beispiel muss kompiliert werden, bevor Sie diesen Befehl ausführen.

### <a name="service-names"></a>Dienstnamen

Neben der Bereitstellung Ihres Bots stellt der Befehl `msbot clone services` auch alle zugehörigen Dienste für Ihr ausgewähltes Abonnement bereit.

Kombinationen aus Name und Dienst, die ggf. bereits im Abonnement vorhanden sind, führen zu einem Fehler. In diesem Fall müssen Sie die unvollständige Bereitstellung löschen, bevor Sie erneut beginnen. Das gilt auch für LUIS-Anwendungen, QnA Maker-Wissensdatenbanken und Dispatch-Modelle.

## <a name="deploy-javascript-and-c-bots-using-az-cli"></a>Bereitstellen von JavaScript- und C#-Bots mit der Azure CLI

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

### <a name="clone-the-sample"></a>Klonen des Beispiels

**Azure-Abonnementkonto:** Je nach E-Mail-Konto, das Sie für die Anmeldung bei Azure verwenden, gelten bestimmte Anweisungen. Lesen Sie diese Anweisungen, bevor Sie fortfahren.

**Erstellen von Diensten:** Der Befehl `msbot clone services` erstellt Azure-Dienste für den Bot.

1. Er listet die Dienste auf, die erstellt werden, und fordert Sie auf, den Vorgang zu bestätigen. Wenn Sie den Vorgang ablehnen, wird der Befehl ohne Erstellung der Dienste abgebrochen.
1. Sie müssen sich über Azure authentifizieren, damit der Vorgang fortgesetzt wird.

**LUIS-Dienste:** Wenn Ihr Bot LUIS oder Dispatch verwendet, müssen Sie Ihren LUIS-Erstellungsschlüssel in den Befehl `msbot clone services` einschließen.

#### <a name="msa-email-account"></a>MSA-E-Mail-Konto

Wenn Sie ein [MSA](https://en.wikipedia.org/wiki/Microsoft_account)-E-Mail-Konto verwenden, müssen Sie die „appId“ und das „appSecret“ mit dem Befehl `msbot clone services` erstellen.

- Navigieren Sie zum [Anwendungsregistrierungsportal](https://apps.dev.microsoft.com/). Klicken Sie auf **App hinzufügen**, um Ihre Anwendung zu registrieren, erstellen Sie eine **Anwendungs-ID**, und klicken Sie auf **Neues Kennwort generieren**.
- Speichern Sie die Anwendungs-ID und das soeben generierte neue Kennwort, damit Sie diese Daten mit dem Befehl `msbot clone services` verwenden können.
- Verwenden Sie zum Bereitstellen den Befehl, der für Ihren Bot gilt.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

`msbot clone services --folder deploymentScripts/msbotClone --location "<geographic-location>" --proj-file "<your.csproj>" --name "<bot-name>" --appId "xxxxxxxx" --appSecret "xxxxxxx" --verbose --luisAuthoringKey <luis-authoring-key>`

[!INCLUDE [deployment note](./includes/deployment-note-cli.md)]

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)

`msbot clone services --folder deploymentScripts/msbotClone --location "<geographic-location>"   --code-dir . --name "<bot-name>" --appId "xxxxxxxx" --appSecret "xxxxxxx" --verbose --luisAuthoringKey <luis-authoring-key>`

[!INCLUDE [deployment note](./includes/deployment-note-cli.md)]

---

#### <a name="business-or-school-account"></a>Geschäfts-, Schul- oder Unikonto

Wenn Sie ein von Ihrem Unternehmen oder Ihrer Schule bzw. Universität bereitgestelltes E-Mail-Konto für die Anmeldung bei Azure verwenden, müssen Sie die Anwendungs-ID und das Kennwort nicht erstellen. Verwenden Sie zum Bereitstellen den Befehl, der für Ihren Bot gilt.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

`msbot clone services --folder deploymentScripts/msbotClone --location "<geographic-location>" --verbose --proj-file "<your-project-file>" --name "<bot-name>" --luisAuthoringKey <luis-authoring-key>`

[!INCLUDE [deployment note](./includes/deployment-note-cli.md)]

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)

`msbot clone services --folder deploymentScripts/msbotClone --location "<geographic-location>" --verbose --code-dir . --name "<bot-name>" --luisAuthoringKey <luis-authoring-key>`

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
az bot publish --name "<your-azure-bot-name>" --proj-name "<your-proj-name>" --resource-group "<azure-resource-group>" --code-dir "<folder>" --verbose --version v4
```

| Argumente        | BESCHREIBUNG |
|----------------  |-------------|
| `name`      | Der Name, den Sie bei der ersten Bereitstellung des Bots in Azure verwendet haben.|
| `proj-name` | Bei C# ist dies der Name der Startprojektdatei (ohne „.csproj“) für die Veröffentlichung. Beispiel: `EnterpriseBot`. Verwenden Sie für Node.js den Haupteinstiegspunkt für den Bot. Beispiel: `index.js`. |
| `resource-group` | Die Azure-Ressourcengruppe, die im Befehl `msbot clone services` verwendet wurde.|
| `code-dir`  | Verweist auf den lokalen Botordner.|

## <a name="additional-resources"></a>Zusätzliche Ressourcen

Wenn Sie einen Bot bereitstellen, werden im Azure-Portal in der Regel folgende Ressourcen erstellt:

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
