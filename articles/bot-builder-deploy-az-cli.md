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
ms.date: 12/14/2018
ms.openlocfilehash: 19960940a40fa291534bc1f88290bc6a7da109e0
ms.sourcegitcommit: 8c10aa7372754596a3aa7303a3a893dd4939f7e9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/20/2018
ms.locfileid: "53654335"
---
# <a name="deploy-your-bot-using-azure-cli"></a>Bereitstellen Ihres Bots mit der Azure CLI

[!INCLUDE [pre-release-label](./includes/pre-release-label.md)]

Nachdem Sie Ihren Bot erstellt und lokal getestet haben, können Sie ihn in Azure bereitstellen, um ihn von überall aus zugänglich zu machen. Wenn Sie Ihren Bot in Azure bereitstellen, werden Ihnen die Kosten für die von Ihnen genutzten Dienste in Rechnung gestellt. Im Artikel zur [Abrechnungs- und Kostenverwaltung](https://docs.microsoft.com/en-us/azure/billing/) finden Sie Informationen zur Azure-Abrechnung, zum Überwachen der Nutzung und Kosten sowie zum Verwalten Ihres Kontos und Ihrer Abonnements.

In diesem Artikel erfahren Sie, wie Sie C#- und JavaScript-Bots per Befehlszeile (`az` und `msbot`) in Azure bereitstellen. Bevor Sie die Schritte ausführen, sollten Sie diesen Artikel lesen, um sich mit allen Details der Bereitstellung eines Bots vertraut zu machen.


## <a name="prerequisites"></a>Voraussetzungen
- Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.
- Installieren Sie die aktuelle Version des [Azure CLI-Tools](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest).
- Installieren Sie die aktuelle `botservice`-Erweiterung für das `az`-Tool.
  - Entfernen Sie zunächst mithilfe des Befehls `az extension remove -n botservice` die alte Version. Installieren Sie anschließend mit dem Befehl `az extension add -n botservice` die aktuelle Version.
- Installieren Sie die aktuelle Version des [MSBot](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/MSBot)-Tools.
- Installieren Sie die neueste veröffentlichte Version von [Bot Framework Emulator](https://aka.ms/Emulator-wiki-getting-started).
- Installieren und konfigurieren Sie [ngrok](https://github.com/Microsoft/BotFramework-Emulator/wiki/Tunneling-%28ngrok%29).
- Kenntnisse der [BOT](v4sdk/bot-file-basics.md)-Datei.

## <a name="deploy-javascript-and-c-bots-using-az-cli"></a>Bereitstellen von JavaScript- und C#-Bots mit der Azure CLI

Sie haben bereits lokal einen Bot erstellt und getestet und möchten ihn nun in Azure bereitstellen. Bei den folgenden Schritten wird davon ausgegangen, dass Sie die erforderlichen Azure-Ressourcen erstellt haben.

Öffnen Sie eine Eingabeaufforderung, um sich beim Azure-Portal anzumelden.

```cmd
az login
```

Ein Browserfenster wird geöffnet, in dem Sie sich anmelden können.

### <a name="set-the-subscription"></a>Festlegen des Abonnements

Legen Sie das zu verwendende Standardabonnement fest.

```cmd
az account set --subscription "<azure-subscription>"
```

Falls Sie nicht sicher, welches Abonnement Sie für die Bereitstellung des Bots verwenden müssen, können Sie die Liste der `subscriptions` für Ihr Konto mit dem Befehl `az account list` anzeigen.

Navigieren Sie zum Botordner.

```cmd
cd <local-bot-folder>
```

### <a name="create-a-web-app-bot"></a>Erstellen eines Web-App-Bots

Erstellen Sie die Botressource für die Veröffentlichung Ihres Bots.

Je nach E-Mail-Konto, das Sie für die Anmeldung bei Azure verwenden, gelten bestimmte Anweisungen. Lesen Sie diese Anweisungen, bevor Sie fortfahren.

#### <a name="msa-email-account"></a>MSA-E-Mail-Konto

Bei Verwendung eines [MSA](https://en.wikipedia.org/wiki/Microsoft_account)-E-Mail-Kontos müssen Sie im Anwendungsregistrierungsportal die App-ID und das App-Kennwort für die Verwendung mit dem Befehl `az bot create` erstellen.

1. Navigieren Sie zum [**Anwendungsregistrierungsportal**](https://apps.dev.microsoft.com/).
1. Klicken Sie auf **App hinzufügen**, um Ihre Anwendung zu registrieren, erstellen Sie eine **Anwendungs-ID**, und klicken Sie auf **Neues Kennwort generieren**. Falls Sie bereits über eine Anwendung und ein Kennwort verfügen, sich aber nicht mehr an das Kennwort erinnern, müssen Sie im Geheimnisabschnitt ein neues Kennwort generieren.
1. Speichern Sie die Anwendungs-ID und das soeben generierte neue Kennwort, damit Sie diese Daten mit dem Befehl `az bot create` verwenden können.  

```cmd
az bot create --kind webapp --name <bot-resource-name> --location <geographic-location> --version v4 --lang <language> --verbose --resource-group <resource-group-name> --appid "<application-id>" --password "<application-password>" --verbose
```

| Option | Beschreibung |
|:---|:---|
| --name | Ein eindeutiger Name, der zum Bereitstellen des Bots in Azure verwendet wird. Der Name kann mit dem Namen Ihres lokalen Bots identisch sein. Der Name darf KEINE Leerzeichen oder Unterstriche enthalten. |
| --location | Geografischer Standort, der zum Erstellen der Bot Service-Ressourcen verwendet wird. Beispiele: `eastus`, `westus`, `westus2` und so weiter |
| --lang | Die Sprache für die Boterstellung: `Csharp`, oder `Node` (Standardwert: `Csharp`) |
| --resource-group | Der Name der Ressourcengruppe, in der der Bot erstellt werden soll. Sie können die Standardgruppe mit `az configure --defaults group=<name>` konfigurieren. |
| --appid | Die Microsoft-Konto-ID (MSA-ID) für den Bot. |
| --password | Das Kennwort des Microsoft-Kontos (MSA) für den Bot. |

#### <a name="business-or-school-account"></a>Geschäfts-, Schul- oder Unikonto

```cmd
az bot create --kind webapp --name <bot-resource-name> --location <geographic-location> --version v4 --lang <language> --verbose --resource-group <resource-group-name>
```

| Option | Beschreibung |
|:---|:---|
| --name | Ein eindeutiger Name, der zum Bereitstellen des Bots in Azure verwendet wird. Der Name kann mit dem Namen Ihres lokalen Bots identisch sein. Der Name darf KEINE Leerzeichen oder Unterstriche enthalten. |
| --location | Geografischer Standort, der zum Erstellen der Bot Service-Ressourcen verwendet wird. Beispiele: `eastus`, `westus`, `westus2` und so weiter |
| --lang | Die Sprache für die Boterstellung: `Csharp`, oder `Node` (Standardwert: `Csharp`) |
| --resource-group | Der Name der Ressourcengruppe, in der der Bot erstellt werden soll. Sie können die Standardgruppe mit `az configure --defaults group=<name>` konfigurieren. |

### <a name="download-the-bot-from-azure"></a>Herunterladen des Bots aus Azure

Laden Sie als Nächstes den soeben erstellten Bot herunter. Der folgende Befehl erstellt ein Unterverzeichnis unter dem Speicherpfad (save-path). Der angegebene Pfad muss allerdings bereits vorhanden sein.

```cmd
az bot download --name <bot-resource-name> --resource-group <resource-group-name> --save-path "<path>"
```

| Option | Beschreibung |
|:---|:---|
| --name | Der Name des Bots in Azure. |
| --resource-group | Der Name der Ressourcengruppe, in der sich der Bot befindet. |
| --save-path | Ein vorhandenes Zielverzeichnis für den Download des Botcodes. |

### <a name="decrypt-the-downloaded-bot-file"></a>Entschlüsseln der heruntergeladenen BOT-Datei

Die vertraulichen Informationen in der BOT-Datei sind verschlüsselt.

Rufen Sie den Verschlüsselungsschlüssel ab.

1. Melden Sie sich beim [Azure-Portal](http://portal.azure.com/) an.
1. Öffnen Sie die Web-App-Bot-Ressource für Ihren Bot.
1. Öffnen Sie die **Anwendungseinstellungen** des Bots.
1. Scrollen Sie im Fenster **Anwendungseinstellungen** nach unten zu den **Anwendungseinstellungen**.
1. Suchen Sie nach **botFileSecret**, und kopieren Sie den Wert.

Entschlüsseln Sie die BOT-Datei.

```cmd
msbot secret --bot <name-of-bot-file> --secret "<bot-file-secret>" --clear
```

| Option | Beschreibung |
|:---|:---|
| --bot | Der relative Pfad zur heruntergeladenen BOT-Datei. |
| --secret | Der Verschlüsselungsschlüssel. |

### <a name="use-the-downloaded-bot-file-in-your-project"></a>Verwenden der heruntergeladenen BOT-Datei in Ihrem Projekt

Kopieren Sie die entschlüsselte BOT-Datei in das Verzeichnis, das Ihr lokales Botprojekt enthält.

Aktualisieren Sie Ihren Bot für die Verwendung der neuen BOT-Datei.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Aktualisieren Sie in **appsettings.json** die Eigenschaft **botFilePath**, sodass sie auf die neue BOT-Datei verweist.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Aktualisieren Sie in **.env** die Eigenschaft **botFilePath**, sodass sie auf die neue BOT-Datei verweist.

---

### <a name="update-the-bot-file"></a>Aktualisieren der Bot-Datei

Falls Ihr Bot LUIS-, QnA Maker- oder Dispatch-Dienste verwendet, müssen diesen ebenfalls Verweise auf Ihre BOT-Datei hinzugefügt werden. Andernfalls können Sie diesen Schritt überspringen.

1. Öffnen Sie Ihren Bot unter Verwendung der neuen BOT-Datei in Bot Framework Emulator. Der Bot muss nicht lokal ausgeführt werden.
1. Erweitern Sie im Bereich **BOT EXPLORER** (BOT-EXPLORER) den Abschnitt **DIENSTE**.
1. Um Verweise zu LUIS-Apps hinzuzufügen, klicken Sie rechts neben **DIENSTE** auf das Pluszeichen (+).
   1. Wählen Sie **Add Language Understanding (LUIS)** (Sprachverständnis (LUIS) hinzufügen) aus.
   1. Melden Sie sich ggf. nach entsprechender Aufforderung bei Ihrem Azure-Konto an.
   1. Es wird eine Liste mit LUIS-Anwendungen angezeigt, auf die Sie Zugriff haben. Wählen Sie die Anwendungen für Ihren Bot aus.
1. Um Verweise zu einer QnA Maker-Wissensdatenbank hinzuzufügen, klicken Sie rechts neben **DIENSTE** auf das Pluszeichen (+).
   1. Wählen Sie **Add QnA Maker** (QnA Maker hinzufügen) aus.
   1. Melden Sie sich ggf. nach entsprechender Aufforderung bei Ihrem Azure-Konto an.
   1. Es wird eine Liste mit den Wissensdatenbanken angezeigt, auf die Sie Zugriff haben. Wählen Sie die Anwendungen für Ihren Bot aus.
1. Um Verweise zu Dispatch-Modellen hinzuzufügen, klicken Sie rechts neben **DIENSTE** auf das Pluszeichen (+).
   1. Wählen Sie **Add Dispatch** (Dispatch hinzufügen) aus.
   1. Melden Sie sich ggf. nach entsprechender Aufforderung bei Ihrem Azure-Konto an.
   1. Es wird eine Liste mit den Dispatch-Modellen angezeigt, auf die Sie Zugriff haben. Wählen Sie die Anwendungen für Ihren Bot aus.

### <a name="test-your-bot-locally"></a>Lokales Testen Ihres Bots

Ihr Bot sollte nun genauso funktioniert wie mit der alten BOT-Datei. Vergewissern Sie sich, dass er mit der neuen BOT-Datei erwartungsgemäß funktioniert.

### <a name="publish-your-bot-to-azure"></a>Veröffentlichen Ihres Bots in Azure

<!-- TODO: re-encrypt your .bot file? -->

Veröffentlichen Sie Ihren lokalen Bot in Azure. Dieser Schritt kann eine Weile dauern.

```cmd
az bot publish --name <bot-resource-name> --proj-file "<project-file-name>" --resource-group <resource-group-name> --code-dir <directory-path> --verbose --version v4
```

<!-- Question: What should --proj-file be for a Node project? -->

| Option | Beschreibung |
|:---|:---|
| --name | Der Ressourcenname des Bots in Azure. |
| --proj-file | Der Name der Startprojektdatei (ohne „.csproj“) für die Veröffentlichung. Beispiel:  EnterpriseBot |
| --resource-group | Name der Ressourcengruppe |
| --code-dir | Das Quellverzeichnis zum Hochladen von Botcode. |

Nach erfolgreichem Abschluss dieses Vorgangs ist Ihr Bot in Azure bereitgestellt.

<!-- TODO: If we tell them to re-encrypt, this step is not necessary. -->

Löschen Sie die Einstellung für den Verschlüsselungsschlüssel.

1. Melden Sie sich beim [Azure-Portal](http://portal.azure.com/) an.
1. Öffnen Sie die Web-App-Bot-Ressource für Ihren Bot.
1. Öffnen Sie die **Anwendungseinstellungen** des Bots.
1. Scrollen Sie im Fenster **Anwendungseinstellungen** nach unten zu den **Anwendungseinstellungen**.
1. Suchen Sie nach **botFileSecret**, und löschen Sie es.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

Wenn Sie einen Bot bereitstellen, werden im Azure-Portal in der Regel folgende Ressourcen erstellt:

| Ressourcen      | Beschreibung |
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
