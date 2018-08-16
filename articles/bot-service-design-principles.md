---
title: Prinzipien des Bot-Entwurfs | Microsoft-Dokumentation
description: Hier erfahren Sie, was einen guten Konversationsbot ausmacht und wie Sie Bots so planen und entwerfen, dass sie Ihren Anforderungen entsprechen und Ihre Benutzer erfreuen.
keywords: bewährte Methoden, Bot-Entwurf
author: matvelloso
ms.author: mateusv
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
ms.openlocfilehash: d42df09fe364e04d85704c83b3489d7659cfc98f
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39301277"
---
# <a name="principles-of-bot-design"></a>Prinzipien des Bot-Entwurfs

Mit Bot Framework können Entwickler überzeugende Bot-Oberflächen für verschiedene geschäftliche Probleme erstellen. Wenn Sie die in diesem Abschnitt beschriebenen Konzepte kennen, können Sie einen Bot entwerfen, der sich an bewährten Methoden orientiert und bisherige Erfahrungen in diesem relativ neuen Bereich nutzt. 

## <a name="designing-a-bot"></a>Entwerfen eines Bots

Wenn Sie einen Bot erstellen, werden Sie wohl erwarten, dass er von Benutzern verwendet wird. Sie werden wohl auch hoffen, dass Benutzer den Bot anderen Möglichkeiten wie Apps, Websites, Telefonaten und anderen Mitteln zur Befriedigung ihrer Bedürfnisse vorziehen werden. Das heißt, Ihr Bot kämpft mit anderen Möglichkeiten wie Apps und Websites um die Zeit der Benutzer. Wie können Sie also Ihre Chancen erhöhen, dass Sie die Benutzer von Ihrem Bot überzeugen? Letztlich geht es nur darum, beim Entwerfen des Bots die richtigen Faktoren zu priorisieren.

## <a name="factors-that-do-not-guarantee-a-bots-success"></a>Faktoren, die den Erfolg eines Bots nicht gewährleisten

Achten Sie beim Entwerfen des Bots darauf, dass keiner der folgenden Faktoren den Erfolg eines Bots zwangsläufig gewährleistet: 

- **„Intelligenz“ des Bots**: Es ist eher unwahrscheinlich, dass ein intelligenterer Bot Benutzer zufriedener macht oder für die bessere Akzeptanz einer Plattform sorgt. In der Praxis verfügen Bots meist kaum über innovative Funktionen für maschinelles Lernen oder natürliche Sprache. Möglicherweise ist ein Bot mit diesen Funktionen ausgestattet, wenn diese benötigt werden, um die Probleme zu lösen, für die der Bot entwickelt wurde. Es besteht jedoch nicht zwangsläufig ein Zusammenhang zwischen der Intelligenz eines Bots und der Benutzerakzeptanz des Bots.

- **Grad der Unterstützung für natürliche Sprache**: Möglicherweise ist Ihr Bot hervorragend für Unterhaltungen geeignet. Er hat einen enormen Wortschatz und kann sogar Witze machen. Wenn er jedoch die Probleme der Benutzer nicht löst, tragen diese Funktionen kaum zum Erfolg des Bots bei. Einige Bots haben sogar überhaupt keine Konversationsfunktionen. Und meist ist das vollkommen in Ordnung.

- **Sprache**: Benutzer finden einen Bot mit Sprachunterstützung nicht unbedingt attraktiver. Häufig frustriert die zwangsweise Nutzung von Sprache. Daher sollten Sie beim Entwurf Ihres Bots immer überlegen, ob Sprache das geeignete Mittel für das jeweilige Problem darstellt. Wird der Bot in einer lauten Umgebung verwendet? Liefert Sprache die Informationen, die der Benutzer benötigt? 

## <a name="factors-that-do-influence-a-bots-success"></a>Faktoren, die den Erfolg eines Bots beeinflussen

Die meisten erfolgreichen Apps oder Websites haben mindestens eine Gemeinsamkeit: ein optimales Maß an Benutzerfreundlichkeit. Bots sind in dieser Hinsicht nicht anders. Daher sollte die Benutzerfreundlichkeit oberste Priorität beim Entwurf eines Bots haben. Einige wichtige Überlegungen:

- Löst der Bot das Benutzerproblem mühelos mit nur wenigen Schritten?

- Löst der Bot das Benutzerproblem besser/leichter/schneller als alle alternativen Plattformen?

- Kann der Bot auf den Geräten und Plattformen ausgeführt werden, die der Benutzer gerne verwendet?

- Wird der Bot gefunden? Wissen die Benutzer intuitiv, wie sie mit dem Bot umgehen müssen?

Keine dieser Fragen steht in direktem Zusammenhang mit Faktoren wie der Intelligenz eines Bots, der Anzahl der Funktionen für natürliche Sprache, der Verwendung von maschinellem Lernen oder der für die Entwicklung des Bots verwendeten Programmiersprache. Benutzer interessieren sich kaum für diese Fragen, wenn der Bot ihr Problem löst und benutzerfreundlich daherkommt. Bei einem benutzerfreundlichen Bot muss der Benutzer nicht allzu viel Text eingeben, nicht allzu viel sprechen, sich nicht mehrmals wiederholen oder Dinge erläutern, die der Bot automatisch wissen sollte.

> [!TIP]
> Die Benutzerfreundlichkeit sollte unabhängig von der Art der Anwendung (Bot, Website oder App) immer oberste Priorität haben.

Ob Sie einen Bot entwerfen oder eine App oder Website, macht keinen Unterschied. Die Erfahrungen aus der Entwicklung von benutzerfreundlichen Benutzeroberflächen für Apps und Websites gelten auch für die Entwicklung von Bots. 

Wenn Sie also an der einen oder anderen Stelle unsicher sind, welches das richtige Konzept für den Entwurf Ihres Bots ist, treten Sie einen Schritt zurück, und überlegen Sie, wie Sie das Problem bei einer App oder Website lösen würden. Vermutlich können Sie dasselbe Konzept beim Entwurf Ihres Bots anwenden. 

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun die grundlegenden Prinzipen des Bot-Entwurfs kennen, erfahren Sie im restlichen Abschnitt mehr über den Entwurf von Benutzeroberfläche und allgemeinen Mustern.