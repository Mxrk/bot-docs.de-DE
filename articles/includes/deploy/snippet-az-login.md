---
ms.openlocfilehash: f8aad539a2d1e415833609f66cd5b398c88206f1
ms.sourcegitcommit: bdb981c0b11ee99d128e30ae0462705b2dae8572
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/17/2019
ms.locfileid: "54360851"
---
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