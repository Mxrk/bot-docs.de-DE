---
ms.openlocfilehash: 867e65b25878f810e3247eb3cace95f4d31e11db
ms.sourcegitcommit: bdb981c0b11ee99d128e30ae0462705b2dae8572
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/17/2019
ms.locfileid: "54360872"
---
Verwenden Sie ein temporäres Verzeichnis außerhalb des aktuellen Projektverzeichnisses. 

Der folgende Befehl erstellt ein Unterverzeichnis unter dem Speicherpfad (save-path). Der angegebene Pfad muss allerdings bereits vorhanden sein.

```cmd
az bot download --name <bot-resource-name> --resource-group <resource-group-name> --save-path "<path>"
```

| Option | BESCHREIBUNG |
|:---|:---|
| --name | Der Name des Bots in Azure. |
| --resource-group | Der Name der Ressourcengruppe, in der sich der Bot befindet. |
| --save-path | Ein vorhandenes Zielverzeichnis für den Download des Botcodes. |