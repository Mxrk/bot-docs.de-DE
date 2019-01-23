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
ms.date: 01/07/2019
ms.openlocfilehash: 3ebc13cf9e2d111d716d081c36f125d28a441811
ms.sourcegitcommit: bdb981c0b11ee99d128e30ae0462705b2dae8572
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/17/2019
ms.locfileid: "54360740"
---
# <a name="deploy-your-bot-using-azure-cli"></a>Bereitstellen Ihres Bots mit der Azure CLI

[!INCLUDE [pre-release-label](./includes/pre-release-label.md)]

Nachdem Sie Ihren Bot erstellt und lokal getestet haben, können Sie ihn in Azure bereitstellen, um ihn von überall aus zugänglich zu machen. Wenn Sie Ihren Bot in Azure bereitstellen, werden Ihnen die Kosten für die von Ihnen genutzten Dienste in Rechnung gestellt. Im Artikel zur [Abrechnungs- und Kostenverwaltung](https://docs.microsoft.com/en-us/azure/billing/) finden Sie Informationen zur Azure-Abrechnung, zum Überwachen der Nutzung und Kosten sowie zum Verwalten Ihres Kontos und Ihrer Abonnements.

In diesem Artikel erfahren Sie, wie Sie C#- und JavaScript-Bots per Befehlszeile (`az` und `msbot`) in Azure bereitstellen. Bevor Sie die Schritte ausführen, sollten Sie diesen Artikel lesen, um sich mit allen Details der Bereitstellung eines Bots vertraut zu machen.

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [prerequisite snippet](~/includes/deploy/snippet-prerequisite.md)]


## <a name="deploy-javascript-and-c-bots-using-az-cli"></a>Bereitstellen von JavaScript- und C#-Bots mit der Azure CLI

Sie haben bereits lokal einen Bot erstellt und getestet und möchten ihn nun in Azure bereitstellen. Bei den folgenden Schritten wird davon ausgegangen, dass Sie die erforderlichen Azure-Ressourcen erstellt haben.

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

Die vertraulichen Informationen in der BOT-Datei sind verschlüsselt.

[!INCLUDE [decrypt bot snippet](~/includes/deploy/snippet-decrypt-bot.md)]

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

[!INCLUDE [publish snippet](~/includes/deploy/snippet-publish.md)]

<!-- TODO: If we tell them to re-encrypt, this step is not necessary. -->

[!INCLUDE [clear encryption snippet](~/includes/deploy/snippet-clear-encryption.md)]

## <a name="additional-resources"></a>Zusätzliche Ressourcen

[!INCLUDE [additional resources snippet](~/includes/deploy/snippet-additional-resources.md)]

## <a name="next-steps"></a>Nächste Schritte
> [!div class="nextstepaction"]
> [Einrichten von Continuous Deployment](bot-service-build-continuous-deployment.md)
