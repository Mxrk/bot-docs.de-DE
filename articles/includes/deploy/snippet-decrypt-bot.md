---
ms.openlocfilehash: eac6abae509d92ea4714bc01221f180ea575950f
ms.sourcegitcommit: bdb981c0b11ee99d128e30ae0462705b2dae8572
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/17/2019
ms.locfileid: "54360864"
---
Rufen Sie den Verschlüsselungsschlüssel ab.

1. Melden Sie sich beim [Azure-Portal](http://portal.azure.com/) an.
1. Öffnen Sie die Web-App-Bot-Ressource für Ihren Bot.
1. Öffnen Sie die **Anwendungseinstellungen** des Bots.
1. Scrollen Sie im Fenster **Anwendungseinstellungen** nach unten zu den **Anwendungseinstellungen**.
1. Suchen Sie nach **botFileSecret**, und kopieren Sie den Wert.

Entschlüsseln Sie die BOT-Datei.

```cmd
msbot secret --bot <name-of-bot-file> --secret "<bot-file-secret>" --clear
```

| Option | BESCHREIBUNG |
|:---|:---|
| --bot | Der relative Pfad zur heruntergeladenen BOT-Datei. |
| --secret | Der Verschlüsselungsschlüssel. |

Kopieren Sie die entschlüsselte Datei vom Typ `.bot` in das Verzeichnis mit Ihrem lokalen Botprojekt, aktualisieren Sie Ihren Bot zur Verwendung dieser neuen Datei vom Typ `.bot`, und entfernen Sie die alte Datei vom Typ `.bot`.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Aktualisieren Sie in **appsettings.json** die Eigenschaft **botFilePath**, sodass sie auf die neue, dem lokalen Verzeichnis hinzugefügte Datei vom Typ `.bot` verweist.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Aktualisieren Sie in **.env** die Eigenschaft **botFilePath**, sodass sie auf die neue, dem lokalen Verzeichnis hinzugefügte Datei vom Typ `.bot` verweist.

---

Löschen Sie nach der Aktualisierung des Bots das temporäre Verzeichnis des heruntergeladenen Bots.