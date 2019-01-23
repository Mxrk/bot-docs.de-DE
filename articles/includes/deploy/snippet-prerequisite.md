---
ms.openlocfilehash: 266cdc2bbeeb140e4b601c1bf0fb3fa8eb085dda
ms.sourcegitcommit: bdb981c0b11ee99d128e30ae0462705b2dae8572
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/17/2019
ms.locfileid: "54360888"
---
- Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.
- Installieren Sie die aktuelle Version des [Azure CLI-Tools](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest).
- Installieren Sie die aktuelle Version des [MSBot](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/MSBot)-Tools.
- Installieren Sie die neueste veröffentlichte Version von [Bot Framework Emulator](https://aka.ms/Emulator-wiki-getting-started).
- Installieren und konfigurieren Sie [ngrok](https://github.com/Microsoft/BotFramework-Emulator/wiki/Tunneling-%28ngrok%29).
- Kenntnisse der [BOT](~/v4sdk/bot-file-basics.md)-Datei.

Bei Verwendung von msbot 4.3.2 und höheren Versionen ist mindestens Version 2.0.54 der Azure CLI erforderlich. Wenn Sie die botservice-Erweiterung installiert haben, entfernen Sie sie mit dem folgenden Befehl:

```cmd
az extension remove --name botservice
```