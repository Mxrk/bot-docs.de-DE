---
title: Konfigurieren der Sprachvorbereitung | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie die Sprachvorbereitung für Ihren Botdienst über das Azure-Portal konfigurieren.
keywords: Sprachvorbereitung, Spracherkennung, LUIS
author: v-royhar
ms.author: v-royhar
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
ms.openlocfilehash: ba2aec255cf160a72c11c3ddfda021baae304568
ms.sourcegitcommit: 3cb288cf2f09eaede317e1bc8d6255becf1aec61
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/27/2018
ms.locfileid: "47389639"
---
# <a name="configure-speech-priming"></a>Konfigurieren der Sprachvorbereitung

Die Sprachvorbereitung verbessert die Erkennung von gesprochenen Wörtern und Ausdrücken, die häufig in Ihrem Bot verwendet werden. Für sprachaktivierte Bots, die die Webchat- und Cortana-Kanäle verwenden, verwendet die Sprachvorbereitung Beispiele, die in LUIS-Apps (Language Understanding Intelligent Service) angegeben werden, zur Verbesserung der Genauigkeit der Spracherkennung für wichtige Wörter.

Ihr Bot ist möglicherweise bereits in eine LUIS-App integriert. Sie können aber auch eine LUIS-App erstellen, die Sie Ihrem Bot für Sprachvorbereitung zuordnen. Die LUIS-App enthält Beispiele für die von Benutzern zu erwartenden Aussagen für Ihren Bot. Wichtige Wörter, die der Bot unbedingt erkennen soll, sollten als Entitäten bezeichnet werden. Bei einem Schachbot würden Sie beispielsweise sicherstellen, dass bei der Benutzeranweisung „Move Knight“ nicht „Move Night“ verstanden wird. Die LUIS-App sollte Beispiele enthalten, in denen „Knight“ als Entität gekennzeichnet ist.

> [!NOTE]
> Um die Sprachvorbereitung mit dem Webchatkanal zu verwenden, müssen Sie den Bing-Spracheingabe-Dienst verwenden. Eine Erläuterung der Verwendung des Bing-Spracheingabe-Diensts finden Sie unter [Aktivieren der Spracheingabe im Webchatkanal](~/bot-service-channel-connect-webchat-speech.md).

> [!IMPORTANT]
> Die Sprachvorbereitung gilt nur für Bots, die für den Cortana-Kanal oder den Webchatkanal konfiguriert wurden.

> [!IMPORTANT]
> Die Vorbereitung wird in LUIS-Apps in Regionen außerhalb der USA nicht unterstützt (etwa in „eu.luis.ai“ und „au.luis.ai“).

## <a name="change-the-list-of-luis-apps-your-bot-uses"></a>Ändern der Liste der LUIS-Apps, die Ihr Bot verwendet

Um die Liste der LUIS-Apps, die von der Bing-Spracheingabe mit Ihrem Bot verwendet werden, zu ändern, führen Sie folgende Schritte aus:

1. Klicken Sie auf dem Blatt des Botdiensts auf **Speech priming** (Sprachvorbereitung). Es wird eine Liste der LUIS-Apps, die Ihnen zur Verfügung stehen, angezeigt.
2. Wählen Sie die LUIS-Apps aus, die von der Bing-Spracheingabe verwendet werden sollen.
 
    a. Zum Auswählen einer LUIS-App in der Liste zeigen Sie auf das LUIS-Modell, bis ein Kontrollkästchen angezeigt wird. Aktivieren Sie dann dieses Kontrollkästchen.
     
    b. Wenn Sie eine LUIS-App auswählen möchten, die nicht in der Liste enthalten ist, scrollen Sie nach unten und geben die GUID der LUIS-Anwendungs-ID in das Textfeld ein.
     
3. Klicken Sie auf **Speichern**, um die Liste der LUIS-Apps, die der Bing-Spracheingabe für Ihren Bot zugeordnet sind, zu speichern.

![Der Bereich der Sprachvorbereitung](~/media/bot-service-manage-speech-priming/speech-priming.png)

## <a name="additional-resources"></a>Weitere Ressourcen

- Weitere Informationen zum Aktivieren von Sprache im Webchat finden Sie unter [Aktivieren der Spracheingabe im Webchat](~/bot-service-channel-connect-webchat-speech.md).
- Weitere Informationen zur Sprachvorbereitung finden Sie unter [Speech Support in Bot Framework – Webchat to Directline, to Cortana](https://blog.botframework.com/2017/06/26/Speech-To-Text/) (Sprachunterstützung in Bot Framework – von Webchat über Direct Line bis Cortana).
- Weitere Informationen zu LUIS-Apps finden Sie unter [Language Understanding Intelligent Service](https://www.luis.ai).
