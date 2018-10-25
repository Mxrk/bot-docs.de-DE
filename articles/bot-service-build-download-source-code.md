---
title: Herunterladen und erneutes Bereitstellen des Bot-Quellcodes | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie einen Botdienst herunterladen und veröffentlichen.
keywords: Herunterladen von Quellcode, erneutes Bereitstellen, Bereitstellen, ZIP-Datei, Veröffentlichen
author: ivorb
ms.author: v-ivorb
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 09/26/2018
ms.openlocfilehash: ee7a7a9f1b4c06f8ad762f750099383e218d98f2
ms.sourcegitcommit: b8bd66fa955217cc00b6650f5d591b2b73c3254b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2018
ms.locfileid: "49326427"
---
# <a name="download-and-redeploy-bot-code"></a>Herunterladen und erneutes Bereitstellen des Botcodes
Mit Azure Bot Service können Sie das gesamte Quellprojekt für Ihren Bot herunterladen, damit Sie lokal in einer IDE Ihrer Wahl arbeiten können. Nachdem Sie die Aktualisierung des Codes abgeschlossen haben, können Sie Ihre Änderungen wieder im Azure-Portal veröffentlichen. Wir zeigen Ihnen, wie Sie Code mit dem Azure-Portal und der `az`-CLI herunterladen. Außerdem wird beschrieben, wie Sie Ihren aktualisierten Botcode mit Visual Studio und der `az`-Cli erneut bereitstellen. Sie können die Methode auswählen, die für Sie am besten geeignet ist.

## <a name="prerequisites"></a>Voraussetzungen
- [Azure-Befehlszeilenschnittstelle](https://docs.microsoft.com/en-us/cli/azure/?view=azure-cli-latest)
- Installieren einer „az botservice“-Erweiterung mit dem Befehl `az extension add -n botservice`

### <a name="download-code-using-the-azure-portal"></a>Herunterladen von Code mit dem Azure-Portal
Gehen Sie wie folgt vor, um Code über das [Azure-Portal](https://portal.azure.com) herunterzuladen:
1. Öffnen Sie das Blatt für den Bot.
1. Klicken Sie im Abschnitt **Botverwaltung** auf **Erstellen**.
1. Klicken Sie unter **Quellcode herunterladen** auf **ZIP-Datei herunterladen**.
1. Warten Sie, bis Azure Ihren URI für den Download vorbereitet hat, und klicken Sie in der Benachrichtigung dann auf **ZIP-Datei herunterladen**.
1. Speichern und extrahieren Sie die ZIP-Datei in ein lokales Verzeichnis.

Aktualisieren Sie bei Verwendung eines C#-Bots die Datei `appsettings.json`, damit sie BOT-Dateiinformationen enthält. Dies ist hier dargestellt:

```
{
  "botFilePath": "yourbasicBot.bot",
  "botFileSecret": "ukxxxxxxxxxxxs="
}
```
`botFilePath` verweist auf den Namen Ihres Bots. Ersetzen Sie einfach „yourbasicBot.bot“ durch den Namen Ihres eigenen Bots. Hilfe zum Beschaffen des `botFileSecret`-Schlüssels finden Sie im Artikel zur [Verschlüsselung der Botdatei](https://aka.ms/bot-file-encryption), in dem das Generieren eines Schlüssels für Ihren Bot beschrieben wird.


Fügen Sie bei Verwendung eines Node.js-Bots die Datei `.env` mit den folgenden Einträgen hinzu:
```
botFilePath=yourbasicBot.bot
botFileSecret=ukxxxxxxxxxxxxs=
```

Nehmen Sie als Nächstes Änderungen an Ihren Quellen vor, indem Sie entweder vorhandene Quelldateien bearbeiten oder Ihrem Projekt neue hinzufügen. Testen Sie Ihren Code mit dem Emulator. Befolgen Sie die unten angegebene Anleitung, wenn Sie den geänderten Code im Azure-Portal erneut bereitstellen möchten.

### <a name="publish-code-using-visual-studio"></a>Veröffentlichen von Code mit Visual Studio
1. Klicken Sie in Visual Studio mit der rechten Maustaste auf den Projektnamen, und wählen Sie **Veröffentlichen...**. Das Fenster **Veröffentlichen** wird geöffnet.

![Azure – Veröffentlichen](~/media/azure-bot-build/azure-csharp-publish.png)

2. Wählen Sie das Profil für Ihr Projekt aus.
3. Kopieren Sie das Kennwort, das in der Datei _publish.cmd_ angegeben ist, in Ihr Projekt.
4. Klicken Sie auf **Veröffentlichen**.
5. Geben Sie bei Aufforderung das Kennwort ein, das Sie in Schritt 3 kopiert haben.   

Nachdem Ihr Projekt konfiguriert wurde, werden Ihre Projektänderungen in Azure veröffentlicht. 

Als Nächstes wird beschrieben, wie Sie Code mit der `az`-CLI herunterladen und erneut bereitstellen.

### <a name="download-code-using-azure-cli"></a>Herunterladen von Code mit der Azure CLI

Melden Sie sich zuerst mit der az-CLI am Azure-Portal an.

```azcli
az login
```

Es wird eine Eingabeaufforderung mit einem eindeutigen temporären Authentifizierungscode angezeigt. Um sich anzumelden, verwenden Sie einen Webbrowser und die Microsoft-Seite für die [Geräteanmeldung](https://microsoft.com/devicelogin). Fügen Sie den durch die CLI bereitgestellten Code ein, um den Vorgang fortzusetzen.

Verwenden Sie den folgenden Befehl, um mit der `az`-CLI Code herunterzuladen:
```azcli
az bot download --name "my-bot-name" --resource-group "my-resource-group"`
```
Gehen Sie wie folgt vor, nachdem der Code heruntergeladen wurde:
- Aktualisieren Sie bei Verwendung eines C#-Bots die Datei „appsettings.json“, damit sie BOT-Dateiinformationen enthält. Dies ist hier dargestellt:

```
{
  "botFilePath": "yourbasicBot.bot",
  "botFileSecret": "ukxxxxxxxxxxxs="
}
```

- Fügen Sie bei Verwendung eines Node.js-Bots eine ENV-Datei mit den folgenden Einträgen hinzu:

```
botFilePath=yourbasicBot.bot
botFileSecret=ukxxxxxxxxxxxxs=
```

Nehmen Sie als Nächstes Änderungen an Ihren Quellen vor, indem Sie entweder vorhandene Quelldateien bearbeiten oder Ihrem Projekt neue hinzufügen. Testen Sie Ihren Code mit dem Emulator. Befolgen Sie die unten angegebene Anleitung, wenn Sie den geänderten Code im Azure-Portal erneut bereitstellen möchten.

### <a name="login-to-azure-cli-by-running-the-following-command"></a>Melden Sie sich an der Azure CLI an, indem Sie den folgenden Befehl ausführen.
Sie können diesen Schritt überspringen, wenn Sie bereits angemeldet sind.

```azcli
az login
```
Es wird eine Eingabeaufforderung mit einem eindeutigen temporären Authentifizierungscode angezeigt. Um sich anzumelden, verwenden Sie einen Webbrowser und die Microsoft-Seite für die [Geräteanmeldung](https://microsoft.com/devicelogin). Fügen Sie den durch die CLI bereitgestellten Code ein, um den Vorgang fortzusetzen.

### <a name="publish-code-using-azure-cli"></a>Veröffentlichen von Code mit der Azure CLI
Verwenden Sie den folgenden Befehl, um Code mit der `az`-CLI wieder in Azure zu veröffentlichen:
```azcli
az bot publish --name "my-bot-name" --resource-group "my-resource-group" --code-dir <path to directory> 
```

Sie können die Option `code-dir` verwenden, um anzugeben, welches Verzeichnis verwendet werden soll. Wenn dies nicht angegeben wird, nutzt der Befehl `az bot publish` das lokale Verzeichnis für die Veröffentlichung.

## <a name="next-steps"></a>Nächste Schritte
Nachdem Sie sich damit vertraut gemacht haben, wie Sie Änderungen wieder in Azure hochladen können, können Sie den Continuous Deployment-Prozess für Ihren Bot einrichten.

> [!div class="nextstepaction"]
> [Festlegen der kontinuierlichen Bereitstellung](bot-service-build-continuous-deployment.md)
