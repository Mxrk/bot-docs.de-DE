---
title: Tutorial zum Erstellen und Bereitstellen eines Basisbots | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie einen Basisbot erstellen und anschließend in Azure bereitstellen.
keywords: Echobot, bereitstellen, Azure, Tutorial
author: Ivorb
ms.author: v-ivorb
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 1/9/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: dbde6eba946e27aaa6b883f1e9205adc63cb22f8
ms.sourcegitcommit: bdb981c0b11ee99d128e30ae0462705b2dae8572
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/17/2019
ms.locfileid: "54360950"
---
# <a name="tutorial-create-and-deploy-a-basic-bot"></a>Tutorial: Erstellen und Bereitstellen eines Basisbots

[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

In diesem Tutorial werden die Schritte zum Erstellen eines Basisbots mit dem Bot Framework SDK sowie zum Bereitstellen dieses Bots in Azure erläutert. Falls Sie bereits einen Basisbot erstellt haben und ihn lokal ausführen, fahren Sie mit dem Abschnitt [Bereitstellen Ihres Bots](#deploy-your-bot) fort.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen eines einfachen Echobots
> * Lokales Ausführen und Interagieren
> * Veröffentlichen Ihres Bots

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

[!INCLUDE [dotnet quickstart](~/includes/quickstart-dotnet.md)]

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

[!INCLUDE [javascript quickstart](~/includes/quickstart-javascript.md)]

---

## <a name="deploy-your-bot"></a>Bereitstellen Ihres Bots

Stellen Sie Ihren Bot nun in Azure bereit.

### <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [prerequisite snippet](~/includes/deploy/snippet-prerequisite.md)]

### <a name="login-to-azure-cli-and-set-your-subscription"></a>Anmelden bei der Azure CLI und Festlegen des Abonnements

Sie haben bereits lokal einen Bot erstellt und getestet und möchten ihn nun in Azure bereitstellen.

[!INCLUDE [az login snippet](~/includes/deploy/snippet-az-login.md)]

### <a name="create-a-web-app-bot"></a>Erstellen eines Web-App-Bots

Wenn Sie nicht bereits über eine Ressourcengruppe für die Veröffentlichung Ihres Bots verfügen, müssen Sie eine erstellen:

[!INCLUDE [az create group snippet](~/includes/deploy/snippet-az-create-group.md)]

[!INCLUDE [az create web app snippet](~/includes/deploy/snippet-create-web-app.md)]

Je nach E-Mail-Konto, das Sie für die Anmeldung bei Azure verwenden, gelten bestimmte Anweisungen. Lesen Sie diese Anweisungen, bevor Sie fortfahren.

#### <a name="msa-email-account"></a>MSA-E-Mail-Konto

Bei Verwendung eines [MSA](https://en.wikipedia.org/wiki/Microsoft_account)-E-Mail-Kontos müssen Sie im Anwendungsregistrierungsportal die App-ID und das App-Kennwort für die Verwendung mit dem Befehl `az bot create` erstellen.

[!INCLUDE [create bot msa snippet](~/includes/deploy/snippet-create-bot-msa.md)]

#### <a name="business-or-school-account"></a>Geschäfts-, Schul- oder Unikonto

[!INCLUDE [create bot snippet](~/includes/deploy/snippet-create-bot.md)]

### <a name="download-the-bot-from-azure"></a>Herunterladen des Bots aus Azure

Laden Sie als Nächstes den soeben erstellten Bot herunter. 
[!INCLUDE [download bot snippet](~/includes/deploy/snippet-download-bot.md)]

### <a name="decrypt-the-downloaded-bot-file-and-use-in-your-project"></a>Entschlüsseln der heruntergeladenen BOT-Datei und Verwenden dieser Datei in Ihrem Projekt

Die vertraulichen Informationen in der BOT-Datei sind verschlüsselt. Sie müssen sie also entschlüsseln, um die Verwendung zu erleichtern. 

Navigieren Sie zuerst zum Verzeichnis mit der heruntergeladenen BOT-Datei.

[!INCLUDE [decrypt bot snippet](~/includes/deploy/snippet-decrypt-bot.md)]

### <a name="test-your-bot-locally"></a>Lokales Testen Ihres Bots

Ihr Bot sollte nun genauso funktionieren wie mit der alten Datei vom Typ `.bot`. Vergewissern Sie sich, dass er mit der neuen Datei vom Typ `.bot` erwartungsgemäß funktioniert.

Im Emulator sollte nun ein *Produktionsendpunkt* angezeigt werden. Ist dies nicht der Fall, verwenden Sie wahrscheinlich noch die alte Datei vom Typ `.bot`.

### <a name="publish-your-bot-to-azure"></a>Veröffentlichen Ihres Bots in Azure

<!-- TODO: re-encrypt your .bot file? -->

[!INCLUDE [publish snippet](~/includes/deploy/snippet-publish.md)]

<!-- TODO: If we tell them to re-encrypt, this step is not necessary. -->

[!INCLUDE [clear encryption snippet](~/includes/deploy/snippet-clear-encryption.md)]

Sie sollten Ihren Bot nun in Webchat testen können.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

[!INCLUDE [additional resources snippet](~/includes/deploy/snippet-additional-resources.md)]

## <a name="next-steps"></a>Nächste Schritte
> [!div class="nextstepaction"]
> [Hinzufügen von Diensten zu Ihrem Bot](bot-builder-tutorial-add-qna.md)

