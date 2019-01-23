---
ms.openlocfilehash: 88732d2d5490962d7a899d936767e7dd148e94c5
ms.sourcegitcommit: bdb981c0b11ee99d128e30ae0462705b2dae8572
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/17/2019
ms.locfileid: "54360857"
---
Veröffentlichen Sie Ihren lokalen Bot in Azure. Dieser Schritt kann eine Weile dauern.

```cmd
az bot publish --name <bot-resource-name> --proj-name "<project-file-name>" --resource-group <resource-group-name> --code-dir <directory-path> --verbose --version v4
```

| Option | BESCHREIBUNG |
|:---|:---|
| --name | Der Ressourcenname des Bots in Azure. |
| --proj-name | Bei C# ist dies der Name der Startprojektdatei (ohne „.csproj“) für die Veröffentlichung. Beispiel: `EnterpriseBot`. Verwenden Sie für Node.js den Haupteinstiegspunkt für den Bot. Beispiel: `index.js`. |
| --resource-group | Name der Ressourcengruppe |
| --code-dir | Das Quellverzeichnis zum Hochladen von Botcode. |

Nach erfolgreichem Abschluss dieses Vorgangs ist Ihr Bot in Azure bereitgestellt.