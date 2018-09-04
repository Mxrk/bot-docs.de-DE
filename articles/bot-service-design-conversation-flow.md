---
title: Entwerfen und Steuern des Konversationsflusses | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie den Konversationsfluss in Ihrem Bot entwerfen und steuern, um eine gute Benutzererfahrung bereitzustellen.
keywords: Entwerfen, Steuern, Konversationsfluss, Unterbrechungen behandeln, Übersicht
author: v-ducvo
ms.author: v-ducvo
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 4/8/2018
ms.openlocfilehash: 6e661d030f49cb8004f122de72de7514e804cb9c
ms.sourcegitcommit: 2dc75701b169d822c9499e393439161bc87639d2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/24/2018
ms.locfileid: "42905539"
---
::: moniker range="azure-bot-service-3.0"

# <a name="design-and-control-conversation-flow"></a>Entwerfen und Steuern des Konversationsflusses

[!INCLUDE [pre-release-label](./includes/pre-release-label-v3.md)]

In herkömmlichen Anwendungen besteht die Benutzeroberfläche (UI) aus einer Reihe von Bildschirmen. 
Eine einzelne App oder Website kann je nach Bedarf einen oder mehrere Bildschirme zum Austausch von Informationen mit dem Benutzer nutzen. 
Die meisten Anwendungen beginnen mit einem Hauptbildschirm, auf den die Benutzer zuerst gelangen und der die Navigation ermöglicht. Dies führt zu anderen Bildschirmen für verschiedene Funktionen, wie das Erstellen einer neuen Bestellung, das Durchsuchen der Produkte oder die Suche nach Hilfe.

Wie Apps und Websites verfügen auch Bots über eine Benutzeroberfläche. Diese besteht aber anstelle von Bildschirmen aus **Dialogen**. Dialoge helfen dabei, Ihre Position innerhalb einer Konversation beizubehalten, die Benutzer bei Bedarf zu Eingaben aufzufordern und eine Eingabevalidierung auszuführen. Sie sind hilfreich für die Verwaltung von Konversationen mit mehreren Turns und einfache „formularbasierte“ Auflistungen von Informationen zum Ausführen von Aktivitäten wie das Buchen eines Flugs.

Dialoge ermöglichen Botentwicklern die logische Trennung der verschiedenen Bereiche der Botfunktionalität und die Steuerung des Konversationsflusses. Sie können beispielsweise einen Dialog mit der Logik entwerfen, mit der die Benutzer Produkte suchen können, und einen separaten Dialog mit der Logik, die Benutzer beim Erstellen einer neuen Bestellung unterstützt. 

Dialoge können über grafische Benutzeroberflächen verfügen, dies ist aber nicht unbedingt erforderlich. Sie können Schaltflächen, Text und andere Elemente enthalten, oder sie können vollständig sprachbasiert sein. Dialoge enthalten auch Aktionen, um Aufgaben wie das Aufrufen anderer Dialoge oder die Verarbeitung von Benutzereingaben auszuführen.

## <a name="using-dialogs-to-manage-conversation-flow"></a>Verwenden von Dialogen zum Verwalten des Konversationsflusses

[!INCLUDE [Dialog flow example](./includes/snippet-dotnet-manage-conversation-flow-intro.md)]

Eine ausführliche exemplarische Vorgehensweise bei der Verwaltung des Konversationsflusses mithilfe von Dialogen und dem Bot Builder SDK finden Sie unter:

- [Verwalten des Konversationsflusses mit Dialogen (.NET)](./dotnet/bot-builder-dotnet-manage-conversation-flow.md)
- [Verwalten des Konversationsflusses mit Dialogen (Node.js)](./nodejs/bot-builder-nodejs-manage-conversation-flow.md)

## <a name="dialog-stack"></a>Dialogstapel

Wenn ein Dialog einen anderen aufruft, fügt Bot Builder den neuen Dialog am Anfang des Dialogstapels hinzu. 
Der Dialog ganz oben im Stapel steuert die Konversation. 
Jede neue vom Benutzer gesendete Nachricht wird durch diesen Dialog verarbeitet, bis er geschlossen oder auf einen anderen Dialog umgeleitet wird. 
Wenn ein Dialog geschlossen wird, wird er aus dem Stapel entfernt, und der vorherige Dialog im Stapel übernimmt die Steuerung der Konversation. 

> [!IMPORTANT]
> Es ist sehr wichtig, die Konzepte hinter dem Erweitern und Verkleinern des Dialogstapels durch Bot Builder und dem Aufrufen der Dialoge untereinander sowie deren Schließen usw. zu verstehen, um den Konversationsfluss eines Bots effektiv entwerfen zu können. 

## <a name="dialogs-stacks-and-humans"></a>Dialoge, Stapel und Personen

Es ist verlockend, davon auszugehen, dass Benutzer durch Dialoge navigieren, einen Dialogstapel erstellen und irgendwann in umgekehrter Reihenfolge zurück navigieren und dabei den Dialogstapel einzeln und auf praktische und geordnete Weise wieder auflösen. 
Beispielsweise könnte ein Benutzer beim Stammdialog beginnen, von dort einen neuen Bestelldialog und danach den Dialog für die Produktsuche aufrufen. 
Anschließend sucht sich der Benutzer ein Produkt aus, bestätigt dieses, beendet den Dialog für die Produktsuche, schließt die Bestellung ab, beendet den Dialog für die neue Bestellung und gelangt schließlich wieder zum Stammdialog. 

Auch wenn es fantastisch wäre, wenn Benutzer immer ein solch linearen, logischen Pfad nutzen würden, kommt es nur sehr selten vor. 
Menschen kommunizieren nicht in „Stapeln“. Sie neigen dazu, sich häufig umzuentscheiden. 
Betrachten Sie das folgende Beispiel: 

![Bot](./media/bot-service-design-conversation-flow/stack-issue.png)

Obwohl Ihr Bot einen logisch konstruierten Stapel von Dialogen erstellt hat, entscheidet der Benutzer, etwas ganz anderes zu tun oder eine Frage zu stellen, die mit dem aktuellen Thema nichts zu tun hat. 
Im Beispiel stellt der Benutzer eine Frage, anstatt die Ja-Nein-Antwort zu geben, die der Dialog erwartet. 
Wie soll Ihr Dialog reagieren?

- Er könnte darauf bestehen, dass der Benutzer zunächst die Frage beantwortet. 
- Er könnte alle zuvor vom Benutzer getroffenen Entscheidungen verwerfen, den kompletten Dialogstapel zurücksetzen und ganz neu beginnen, indem er versucht, die Frage des Benutzers zu beantworten. 
- Er könnte versuchen, die Frage des Benutzers zu beantworten und dann zu dieser Ja-Nein-Frage zurückkehren und versuchen, erneut von dort fortzufahren. 

Es gibt keine *richtige* Antwort auf diese Frage. Die beste Lösung hängt von der jeweiligen Situation ab und davon, was Benutzer als angemessene Reaktion des Bots erwarten würden. Allerdings wird die Verwaltung von **Dialogen** schwieriger, wenn die Komplexität Ihrer Konversation zunimmt. In Situationen mit komplexen Verzweigungen kann es einfacher sein, einen eigenen Steuerlogikablauf zu erstellen, um die Konversation des Benutzers nachzuverfolgen.

## <a name="next-steps"></a>Nächste Schritte

Das Verwalten der Benutzernavigation innerhalb der Dialoge und der Entwurf eines Konversationsflusses, der es Benutzern ermöglicht, ihre Ziele (auch auf nicht lineare Weise) zu erreichen, ist eine der wesentlichen Herausforderungen beim Botentwurf. 
Im [nächsten Artikel](./bot-service-design-navigation.md) werden einige häufiger auftretende Probleme bei schlechten Navigationsentwürfen sowie Strategien zur Vermeidung dieser Fallen erläutert. 

::: moniker-end

::: moniker range="azure-bot-service-4.0"
# <a name="design-and-control-conversation-flow"></a>Entwerfen und Steuern des Konversationsflusses

In herkömmlichen Anwendungen besteht die Benutzeroberfläche (UI) aus einer Reihe von Bildschirmen. Eine einzelne App oder Website kann je nach Bedarf einen oder mehrere Bildschirme zum Austausch von Informationen mit dem Benutzer nutzen. 
Die meisten Anwendungen beginnen mit einem Hauptbildschirm, auf den die Benutzer zuerst gelangen und der die Navigation ermöglicht. Diese führt zu anderen Bildschirmen für verschiedene Funktionen, wie das Erstellen einer neuen Bestellung, das Durchsuchen der Produkte oder die Suche nach Hilfe.

Wie Apps und Websites verfügen auch Bots über eine Benutzeroberfläche. Diese besteht aber anstelle von Bildschirmen aus **Nachrichten**. Nachrichten können Schaltflächen, Text und andere Elemente enthalten, oder sie können vollständig sprachbasiert sein. 

Während traditionelle Anwendungen oder Websites mehrere Informationen gleichzeitig auf einem Bildschirm anfordern können, sammelt ein Bot die gleiche Menge an Informationen über mehrere Nachrichten. Auf diese Weise ist der Vorgang des Sammelns von Informationen vom Benutzer eine aktive Interaktion, bei der der Benutzer eine aktive Konversation mit dem Bot hat. 

Ein gut entworfener Bot sorgt für einen Konversationsfluss, der sich natürlich anfühlt. Der Bot sollte in der Lage sein, die Kernkonversation flüssig durchzuführen und Unterbrechungen oder Themenwechsel während der Konversation problemlos zu verarbeiten. 

## <a name="procedural-conversation-flow"></a>Prozeduraler Konversationsfluss

Die Konversation mit einem Bot konzentriert sich in der Regel auf die Aufgabe, die ein Bot erreichen soll. Dieser Ablauf wird als prozedural bezeichnet. Der Bot stellt dem Benutzer eine Reihe von Fragen, um alle Informationen zu sammeln, die er zur Verarbeitung der Aufgabe benötigt.

Bei einem prozeduralen Konversationsfluss definieren Sie die Reihenfolge der Fragen, und der Bot stellt die Fragen in der Reihenfolge, die Sie festgelegt haben. Sie können die Fragen in logischen *Modulen* organisieren, um den Code zu zentralisieren und den Fokus auf dem Führen der Konversation zu halten. Sie können beispielsweise eine Nachricht entwerfen, die die Logik enthält, mit der die Benutzer Produkte suchen können, und eine separate Nachricht mit der Logik, die Benutzer beim Erstellen einer neuen Bestellung unterstützt. 

Sie können den Ablauf dieser Module auf beliebige Weise strukturieren – von einer komplett freien Form bis hin zum sequenziellen Abarbeiten. Das Bot Builder SDK bietet mehrere Bibliotheken, mit denen Sie einen für Ihren Bot geeigneten Konversationsfluss erstellen können. Mithilfe der `prompts`-Bibliothek können Sie z.B. Benutzer um Eingaben bitten. Die `waterfall`-Bibliothek ermöglicht das Definieren einer Folge von Frage-Antwort-Paaren, die `dialog control`-Bibliothek erlaubt Ihnen das Modularisierten der Logik Ihres Konversationsflusses usw. Alle diese Bibliotheken sind über ein `dialogs`-Objekt miteinander verknüpft. Werfen wir einen genaueren Blick auf die Implementierung von Modulen als `dialogs` zum Entwerfen und Verwalten von Konversationsflüssen, um zu erfahren, worin der Fluss dem traditionellen Anwendungsfluss ähnelt.

![Bot](./media/designing-bots/core/dialogs-screens.png)

In einer herkömmlichen Anwendung beginnt alles mit dem **Hauptbildschirm**.
Der **Hauptbildschirm** ruft den **Bildschirm für neue Bestellungen** auf.
Der **Bildschirm für neue Bestellungen** bleibt aktiv, bis er geschlossen wird oder andere Bildschirme aufruft, z.B. den **Bildschirm für die Produktsuche**. 
Wenn der **Bildschirm für neue Bestellungen** geschlossen wird, kehrt der Benutzer zurück zum **Hauptbildschirm**.

Bei einem Bot beginnt alles mit dem **Stammdialog**. 
Der **Stammdialog** ruft den **Dialog für neue Bestellungen** auf. 
An diesem Punkt übernimmt der **Dialog für neue Bestellungen** die Steuerung der Konversation, bis er geschlossen wird oder andere Dialoge aufruft, z.B. den **Dialog für die Produktsuche**. 
Wenn der **Dialog für neue Bestellungen** geschlossen wird, übernimmt der **Stammdialog** wieder die Steuerung der Konversation.

Sehen Sie sich das konzeptuelle Thema zum [Konversationsfluss](v4sdk/bot-builder-conversations.md) an, das weitere Informationen zu den Konversationstypen bietet.

## <a name="handle-interruptions"></a>Behandeln von Unterbrechungen

Es ist verlockend, davon auszugehen, dass Benutzer prozedurale Aufgaben nacheinander auf praktische und geordnete Weise ausführen. 
Beispielsweise könnte ein Benutzer in einem prozeduralen Fluss mit `dialogs` am Stammdialog beginnen, von dort einen neuen Bestelldialog und danach den Dialog für die Produktsuche aufrufen. Anschließend sucht sich der Benutzer ein Produkt aus, bestätigt dieses, beendet den Dialog für die Produktsuche, schließt die Bestellung ab, beendet den Dialog für die neue Bestellung und gelangt schließlich wieder zum Stammdialog. 

Auch wenn es fantastisch wäre, wenn Benutzer immer ein solch linearen, logischen Pfad nutzen würden, kommt es nur sehr selten vor. 
Menschen kommunizieren nicht in sequenziellen `dialogs`. Sie neigen dazu, sich häufig umzuentscheiden. 
Betrachten Sie das folgende Beispiel: 

![Bot](./media/bot-service-design-conversation-flow/stack-issue.png)

Obwohl Ihr Bot prozedural ausgelegt ist, entscheidet der Benutzer, etwas ganz anderes zu tun oder eine Frage zu stellen, die mit dem aktuellen Thema nichts zu tun hat. 
Im Beispiel oben stellt der Benutzer eine Frage anstatt die Ja-Nein-Antwort zu geben, die der Bot erwartet. 
Wie soll Ihr Bot reagieren?

- Er könnte darauf bestehen, dass der Benutzer zunächst die Frage beantwortet. 
- Er könnte alle zuvor vom Benutzer getroffenen Entscheidungen verwerfen, den kompletten Dialogstapel zurücksetzen und ganz neu beginnen, indem er versucht, die Frage des Benutzers zu beantworten. 
- Er könnte versuchen, die Frage des Benutzers zu beantworten und dann zu dieser Ja-Nein-Frage zurückkehren und versuchen, erneut von dort fortzufahren. 

Es gibt keine *richtige* Antwort auf diese Frage. Die beste Lösung hängt von der jeweiligen Situation ab und davon, was Benutzer als angemessene Reaktion des Bots erwarten würden. Weitere Informationen finden Sie unter [Behandeln von Benutzerunterbrechungen](v4sdk/bot-builder-howto-handle-user-interrupt.md).

## <a name="next-steps"></a>Nächste Schritte

Das Verwalten der Benutzernavigation innerhalb der Dialoge und der Entwurf eines Konversationsflusses, der es Benutzern ermöglicht, ihre Ziele (auch auf nicht lineare Weise) zu erreichen, ist eine der wesentlichen Herausforderungen beim Botentwurf. 
Im [nächsten Artikel](~/bot-service-design-navigation.md) werden einige häufiger auftretende Probleme bei schlechten Navigationsentwürfen sowie Strategien zur Vermeidung dieser Fallen erläutert. 

::: moniker-end
