---
ms.openlocfilehash: fed6222fb9cf2d7793776c5575dfbcda49b54224
ms.sourcegitcommit: bdb981c0b11ee99d128e30ae0462705b2dae8572
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/17/2019
ms.locfileid: "54360887"
---
1. Navigieren Sie zum [**Anwendungsregistrierungsportal**](https://apps.dev.microsoft.com/).
1. Klicken Sie auf **App hinzufügen**, um Ihre Anwendung zu registrieren, erstellen Sie eine **Anwendungs-ID**, und klicken Sie auf **Neues Kennwort generieren**. Falls Sie bereits über eine Anwendung und ein Kennwort verfügen, sich aber nicht mehr an das Kennwort erinnern, müssen Sie im Geheimnisabschnitt ein neues Kennwort generieren.
1. Speichern Sie die Anwendungs-ID und das soeben generierte neue Kennwort, damit Sie diese Daten mit dem Befehl `az bot create` verwenden können.  

```cmd
az bot create --kind webapp --name <bot-resource-name> --location <geographic-location> --version v4 --lang <language> --verbose --resource-group <resource-group-name> --appid "<application-id>" --password "<application-password>" --verbose
```

| Option | BESCHREIBUNG |
|:---|:---|
| --name | Ein eindeutiger Name, der zum Bereitstellen des Bots in Azure verwendet wird. Der Name kann mit dem Namen Ihres lokalen Bots identisch sein. Der Name darf KEINE Leerzeichen oder Unterstriche enthalten. |
| --location | Geografischer Standort, der zum Erstellen der Bot Service-Ressourcen verwendet wird. Beispiele: `eastus`, `westus`, `westus2` und so weiter |
| --lang | Die Sprache für die Boterstellung: `Csharp`, oder `Node` (Standardwert: `Csharp`) |
| --resource-group | Der Name der Ressourcengruppe, in der der Bot erstellt werden soll. Sie können die Standardgruppe mit `az configure --defaults group=<name>` konfigurieren. |
| --appid | Die Microsoft-Konto-ID (MSA-ID) für den Bot. |
| --password | Das Kennwort des Microsoft-Kontos (MSA) für den Bot. |
