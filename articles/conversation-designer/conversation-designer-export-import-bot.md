---
title: Importieren und Exportieren eines Conversation Designer-Bots | Microsoft-Dokumentation
description: Erfahren Sie, wie Conversation Designer-Bots im- und exportiert werden.
author: v-ducvo
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
ROBOTS: NoIndex, NoFollow
ms.openlocfilehash: ed055cb0f75d148e6a0dd3248366851901d9a675
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39304189"
---
# <a name="export-and-import-a-conversation-designer-bot"></a>Exportieren und Importieren eines Conversation Designer-Bots

Bots in Conversation Designer können als ZIP-Datei auf Ihren Computer exportiert werden. Auf diese Weise können Sie eine Sicherungskopie Ihres Bots erstellen. Sie können Ihren Bot zu einem späteren Zeitpunkt in einem früheren Zustand wiederherstellen, indem Sie eine der exportierten ZIP-Dateien verwenden. 

## <a name="export-a-conversation-designer-bot"></a>Exportieren eines Conversation Designer-Bots

Durch Exportieren können Sie den aktuellen Zustand Ihres Conversation Designer-Bots auf Ihrem lokalen Computer speichern. 

Gehen Sie wie folgt vor, um einen Conversation Designer-Bot zu exportieren:
1. Klicken Sie in der oberen rechten Ecke des Navigationsbereichs auf die Auslassungspunkte (**...**).
2. Klicken Sie auf **Exportieren**. Der Server sammelt die erforderlichen Informationen und präsentiert Ihnen anschließend die Option, die ZIP-Datei zu speichern.
3. Speichern Sie die exportierte ZIP-Datei auf dem lokalen Computer.

Ihr Bot ist jetzt in einer ZIP-Datei auf Ihrem Computer gespeichert. Sie können den Bot zu einem späteren Zeitpunkt in diesem Zustand wiederherstellen, indem Sie ihn wieder in den Bot [importieren](#import-a-conversation-designer-bot).

## <a name="import-a-conversation-designer-bot"></a>Importieren eines Conversation Designer-Bots

Durch Importieren können Sie einen früheren Zustand des Conversation Designer-Bots wiederherstellen. Beim Importieren wird der aktuelle Bot durch den importierten Bot ersetzt. Wenn Sie den aktuellen Inhalt des Bots nicht verlieren möchten, achten Sie darauf, den aktuellen Bot zu [exportieren](#export-a-conversation-designer-bot), bevor Sie einen Importvorgang ausführen.

Gehen Sie wie folgt vor, um einen Conversation Designer-Bot zu importieren:
1. Klicken Sie in der oberen rechten Ecke des Navigationsbereichs auf die Auslassungspunkte (**...**).
2. Klicken Sie auf **Importieren**. 
3. Wenn Sie Ihre Arbeit am aktuellen Bot speichern möchten, wählen Sie die Option **Backup and import** (Sichern und importieren) aus. Bei dieser Option wird Ihr aktueller Bot auf dem lokalen Computer gespeichert, anschließend werden Sie nach dem Speicherort der zu importierenden ZIP-Datei gefragt. Wählen Sie andernfalls **Import without backing up** (Ohne Sicherung importieren) aus.

Ihr Bot wird jetzt importiert.

> [!NOTE]
> Es können nur Bots importiert werden, die von Conversation Designer exportiert wurden.

## <a name="import-a-luis-app-into-a-conversation-designer-bot"></a>Importieren einer LUIS-App in einen Conversation Designer-Bot

Wenn Sie über eine LUIS-App verfügen und diese in Ihrem Conversation Designer-Bot verwenden möchten, können Sie Ihre LUIS-App in Ihren Conversation Designer-Bot importieren. Konzeptionell erfordert dieser Vorgang, dass Sie Ihre LUIS-App und den Conversation Designer-Bot exportieren und anschließend den Inhalt der **luis.model**-Datei Ihres Bots durch Ihre **luis.json**-Datei ersetzen. Importieren Sie anschließend Ihre Änderungen wieder in Ihren Conversation Designer-Bot. Im Wesentlichen ersetzen Sie dabei die Absichten in Ihrem Bot durch die Absichten in Ihrer LUIS-App. Daher ist es sinnvoll, diesen Importvorgang auszuführen, bevor Sie mit dem Anpassen der LUIS-Absichten Ihres Bots beginnen, andernfalls wird Ihre Arbeit beim Importvorgang überschrieben.

> [!NOTE]
> Wenn Sie Änderungen an einer [LUIS](https://luis.ai)-App vornehmen, die Ihrem Bot zugeordnet ist (jeder Conversation Designer-Bot weist eine entsprechende LUIS-App auf), müssen Sie diese Schritte nicht ausführen. Stattdessen müssen Sie lediglich zu Ihrem Conversation Designer-Bot navigieren und auf [**Speichern**](conversation-designer-save-bot.md) klicken.

Gehen Sie wie folgt vor, um Ihre LUIS-App in Ihren Conversation Designer-Bot zu importieren:

1. Öffnen Sie aus [LUIS.ai](https://luis.ai) Ihre LUIS-App, und klicken Sie dann auf **EINSTELLUNGEN**.
2. Wählen Sie die **Versionen** der App aus, die Sie verwenden möchten, und klicken Sie auf das Aktionssymbol **{ }**. Durch diese Aktion wird die **luis.json**-Datei auf den lokalen Computer heruntergeladen. 
3. Nun müssen Sie in Conversation Designer entweder [einen neuen Bot erstellen](conversation-designer-create-bot.md#create-a-conversation-designer-bot) oder einen vorhandenen Bot öffnen.
4. [Exportieren](#export-a-conversation-designer-bot) Sie Ihren Bot. Dadurch wird Ihr Bot als ZIP-Datei auf Ihren Computer exportiert.
5. Navigieren Sie zu Ihrer exportierten ZIP-Datei, und extrahieren Sie sie.
6. Öffnen Sie die **luis.model**-Datei in einem Text-Editor, und ersetzen Sie den Inhalt dieser Datei durch den Inhalt der **luis.json**-Datei. Speichern Sie die Datei .
7. Zippen Sie den Ordner Ihres Conversation Designer-Bots.
8. [Importieren](#import-a-conversation-designer-bot) Sie Ihren Bot wieder in Ihren Conversation Designer-Bot.

Jetzt kann Ihr Bot die neuen LUIS-Absichten verwenden, die Sie soeben importiert haben.

## <a name="next-step"></a>Nächster Schritt
> [!div class="nextstepaction"]
> [Aufgaben](conversation-designer-tasks.md)
