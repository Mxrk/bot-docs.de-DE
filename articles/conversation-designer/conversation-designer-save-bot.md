---
title: Speichern eines Conversation Designer-Bots | Microsoft-Dokumentation
description: Erfahren Sie, wie das Sprachverständnismodell und die Hauptspracherkennung in einem Conversation Designer-Bot gespeichert und trainiert werden.
author: vkannan
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
ROBOTS: NoIndex, NoFollow
ms.openlocfilehash: 3a911158c379f879c0be604fb5e8ba30ab22ee44
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39304120"
---
# <a name="saving-your-conversation-designer-bot"></a>Speichern Ihres Conversation Designer-Bots
> [!IMPORTANT]
> Conversation Designer ist noch nicht für alle Kunden verfügbar. Weitere Informationen zur Verfügbarkeit von Conversation Designer folgen später in diesem Jahr.

Beim Arbeiten in Conversation Designer wird Ihre gesamte Arbeit im Browserspeicher zwischengespeichert. Um Ihre Änderungen zu committen, klicken Sie auf die Schaltfläche **Speichern**, die sich in der oberen linken Ecke des linken Navigationsmenüs befindet. Um den Verlust von Arbeit zu vermeiden, empfiehlt es sich, Ihre Arbeit häufig zu speichern. Abgesehen vom Klicken auf die Schaltfläche **Speichern** können Sie Ihre Arbeit auch mithilfe der Tastenkombination **STRG+S** speichern.

## <a name="trains-luis-and-primes-speech-recognition"></a>Trainiert LUIS und macht die Spracherkennung betriebsbereit

Beim Klicken auf die Schaltfläche **Speichern** werden Änderungen am Bot gespeichert und außerdem einige weitere Aufgaben ausgeführt. Im Gegensatz zur Tastenkombination weist das Klicken auf die Schaltfläche **Speichern** Conversation Designer außerdem an, die folgenden Aufgaben auszuführen:

- Trainieren aller neuen LUIS-Absichten und -Entitäten für den Bot und lokales Veröffentlichen des LUIS-Modells in Ihrem Botdienst (sofern erforderlich). Diese Absichten können in Conversation Designer oder in der dem Bot zugeordneten [LUIS](https://luis.ai)-App hinzugefügt werden.
- Aktualisieren der Unterhaltungsruntime für die Verwendung des neuen LUIS-Modells.
- Betriebsbereit machen der Spracherkennung durch Vorbereiten und Senden Ihrer Beispieläußerungen an Microsoft Cognitive Services, wodurch die Genauigkeit der Spracherkennung für diesen Bot enorm verbessert wird.

## <a name="next-step"></a>Nächster Schritt
> [!div class="nextstepaction"]
> [Testen eines Bots](conversation-designer-debug-bot.md)
