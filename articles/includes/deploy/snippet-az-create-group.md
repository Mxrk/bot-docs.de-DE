---
ms.openlocfilehash: 53db401b00e53b964027f08c32ca7a38a4915f60
ms.sourcegitcommit: bdb981c0b11ee99d128e30ae0462705b2dae8572
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/17/2019
ms.locfileid: "54360881"
---
```cmd
az group create --name <resource-group-name> --location <geographic-location> --verbose
```

| Option | BESCHREIBUNG |
|:---|:---|
| --name | Ein eindeutiger Name für die Ressourcengruppe. Der Name darf KEINE Leerzeichen oder Unterstriche enthalten. |
| --location | Geografischer Standort, der zum Erstellen der Ressourcengruppe verwendet wird. Beispiele: `eastus`, `westus`, `westus2` und so weiter Verwenden Sie `az account list-locations` zum Abrufen einer Standortliste. |