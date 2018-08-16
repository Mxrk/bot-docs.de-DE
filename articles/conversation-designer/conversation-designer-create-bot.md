---
title: Erstellen eines Conversation Designer-Bots | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie einen neuen Bot mithilfe von Conversation Designer erstellen.
author: v-ducvo
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
ROBOTS: NoIndex, NoFollow
ms.openlocfilehash: 1b33d08e56bf8ae473b28cf1cf3a26d8138ce861
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39301500"
---
# <a name="create-a-new-conversation-designer-bot"></a>Erstellen eines neuen Conversation Designer-Bots
> [!IMPORTANT]
> Conversation Designer ist noch nicht für alle Kunden verfügbar. Weitere Informationen zur Verfügbarkeit von Conversation Designer folgen später in diesem Jahr.

Dieses Tutorial enthält ausführliche Anleitungen zur Erstellung eines neuen Conversation Designer-Bots. 

## <a name="prerequisites"></a>Voraussetzungen

- Für Conversation Designer wird ein Azure-Abonnement benötigt. <a href="https://azure.microsoft.com/en-us/" target="_blank">Hier</a> können Sie loslegen.
- Melden Sie sich (falls noch nicht geschehen) mindestens einmal beim [LUIS-Portal](https://luis.ai) an, nachdem Sie dort ein Konto erstellt haben.
- JavaScript-Programmiererfahrung Benutzerdefinierte Skriptfunktionen werden in JavaScript geschrieben.
- Microsoft Edge oder Google Chrome

## <a name="create-a-conversation-designer-bot"></a>Erstellen eines Conversation Designer-Bots

So erstellen Sie einen Conversation Designer-Bot:
1. Rufen Sie https://dev.botframework.com/ auf, und melden Sie sich an. Verwenden Sie die E-Mail-Adresse, die Sie der Microsoft Corporation zur Verfügung gestellt haben, um die private Vorschauversion nutzen zu können.
2. Klicken Sie im oberen rechten Navigationsbereich auf **Create a bot** (Bot erstellen). 
3. Klicken Sie auf **Create** (Erstellen), um die Aktion *Create a bot with the Conversation Designer* (Bot mit Conversation Designer erstellen) auszuführen.
4. Für die ersten Schritte können Sie einen der [vielen Beispielbots](conversation-designer-sample-bots.md) auswählen. Klicken Sie auf **Weiter**. Wenn Sie sich nicht sicher sind, welchen **Beispielbot** Sie auswählen sollen, können Sie sich einfach für den entscheiden, der dem zu erstellenden Bot am ähnlichsten ist. Später können Sie einen anderen **Beispielbot** auswählen.
5. Füllen Sie alle Felder aus, und klicken Sie auf **Create bot** (Bot erstellen). Die Bereitstellung des Bots dauert ca. zwei Minuten. 

## <a name="bot-provisioning"></a>Bereitstellung des Bots

Die folgenden Azure-Features werden automatisch bereitgestellt, wenn Sie einen Conversation Designer-Bot erstellen: 

1. Azure-Ressourcengruppe mit dem angegebenen Botnamen
2. Azure App Service
3. Azure App Service-Plan 
4. Azure-Speicherkonto
5. Application Insights 
6. Cognitive Services-Abonnement für [LUIS.ai](https://luis.ai). Eine LUIS-App wird zusammen mit dem **Bothandle** (und einer zufällig generierten Zeichenfolge) als App-Name erstellt.
7. Microsoft-Konto – einzelne App [Weitere Informationen](https://apps.dev.microsoft.com/#/appList)

## <a name="welcome-message"></a>Begrüßungsnachricht

Nach der Bereitstellung des Bots öffnet Conversation Designer die Botseite **Build**. Eine Begrüßungsnachricht wird angezeigt. Diese enthält Informationen, die Ihnen die ersten Schritte erleichtern sollen. Sie können sich diese Informationen genauer ansehen oder die Nachricht schließen und mit der Arbeit an Ihrem Bot beginnen. Die Begrüßungsnachricht lässt sich erneut aufrufen, indem Sie im oberen linken Navigationsbereich auf die Auslassungspunkte (**...**) klicken und die Option **Welcome...** (Willkommen) auswählen.

## <a name="next-step"></a>Nächster Schritt
> [!div class="nextstepaction"]
> [Speichern eines Bots](conversation-designer-save-bot.md)

## <a name="additional-resources"></a>Zusätzliche Ressourcen
* Weitere Informationen zu [Aufgaben](conversation-designer-tasks.md)
* Weitere Informationen zum [Bot Builder SDK für Node](../nodejs/index.md) 
