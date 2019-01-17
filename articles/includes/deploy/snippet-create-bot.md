---
ms.openlocfilehash: f95c8a37b1207a26dab0a714b86412a9dba2dcb4
ms.sourcegitcommit: bdb981c0b11ee99d128e30ae0462705b2dae8572
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/17/2019
ms.locfileid: "54360873"
---
```cmd
az bot create --kind webapp --name <bot-resource-name> --location <geographic-location> --version v4 --lang <language> --verbose --resource-group <resource-group-name>
```

| Option | BESCHREIBUNG |
|:---|:---|
| --name | Ein eindeutiger Name, der zum Bereitstellen des Bots in Azure verwendet wird. Der Name kann mit dem Namen Ihres lokalen Bots identisch sein. Der Name darf KEINE Leerzeichen oder Unterstriche enthalten. |
| --location | Geografischer Standort, der zum Erstellen der Bot Service-Ressourcen verwendet wird. Beispiele: `eastus`, `westus`, `westus2` und so weiter |
| --lang | Die Sprache für die Boterstellung: `Csharp`, oder `Node` (Standardwert: `Csharp`) |
| --resource-group | Der Name der Ressourcengruppe, in der der Bot erstellt werden soll. Sie können die Standardgruppe mit `az configure --defaults group=<name>` konfigurieren. |