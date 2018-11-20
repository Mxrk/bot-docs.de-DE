---
redirect_url: /bot-framework/bot-builder-send-welcome-message
ms.openlocfilehash: ef281fd1b6539484c06f68caffbd4e87ec8acc2b
ms.sourcegitcommit: cb0b70d7cf1081b08eaf1fddb69f7db3b95b1b09
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2018
ms.locfileid: "51332904"
---
<a name="--"></a><!--
---
Titel: Begrüßung des Benutzers | Microsoft-Dokumentation Beschreibung: Erfahren Sie, wie Sie Ihren Bot so entwerfen, dass eine einladende Benutzerumgebung geschaffen wird.
Schlüsselwörter: Übersicht, Entwurf, Benutzeroberfläche, Begrüßung, personalisierte Benutzeroberfläche author: dashel ms.author: dashel manager: kamrani ms.topic: article ms.service: bot-service ms.subservice: sdk ms.date: 8/30/2018 monikerRange: 'azure-bot-service-4.0'
---

# <a name="welcoming-the-user"></a>Willkommensumgebung für Benutzer

Das Hauptziel bei der Erstellung eines Bots besteht darin, mit dem Benutzer eine aussagekräftige Konversation zu führen. Dieses Ziel erreichen Sie am besten, indem Sie Folgendes sicherstellen: Gleich nach dem ersten Anmelden müssen Benutzer den Hauptzweck und die Funktionen des Bots erkennen können und sich darüber im Klaren sein, aus welchem Grund der Bot erstellt wurde.

## <a name="show-your-purpose"></a>Verdeutlichen des Zwecks

Stellen Sie sich vor, ein Benutzer stellt eine Verbindung mit Ihrem Bot „Chicago Tourist Information“ her, weil er hofft, Informationen zu Hotels in Chicago zu erhalten. Der Benutzer weiß aber nicht, dass Ihr Bot eigentlich für Fans von Deep-Dish-Pizzas gedacht ist und nur Tipps zu Restaurants liefert. Nachdem deutlich geworden ist, dass die Fragen des Benutzers nicht richtig beantwortet wurden, verlässt der Benutzer Ihren Bot sofort und ist der Meinung, dass Ihre Website eine schlechte Onlineerfahrung war. Eine schlechte Benutzererfahrung dieser Art kann vermieden werden, indem Sie für Ihre Benutzer bei der Begrüßung genügend Informationen bereitstellen, damit diese den Hauptzweck und die Funktionen Ihres Bots verstehen. 

![Begrüßungsnachricht](./media/welcome_message.png)

Wenn Ihr Bot für einen Benutzer nicht die gewünschten Informationen liefert, kann er nach dem Lesen Ihrer Begrüßungsnachricht dann schnell weitersuchen, bevor es zu einer frustrierenden Interaktion kommt.
Es sollte immer eine Begrüßungsnachricht generiert werden, wenn Ihre Benutzer zum ersten Mal mit Ihrem Bot interagieren. Hierzu können Sie die **Aktivitätstypen** Ihres Bots überwachen und auf neue Verbindungen warten. Für jede neue Verbindung können je nach Kanal bis zu zwei Aktivitäten zum Aktualisieren von Konversationen generiert werden.

- Eine Aktivität, wenn der Bot des Benutzers mit der Konversation verbunden wird.
- Eine weitere Aktivität, wenn der Benutzer der Konversation beitritt.

Es ist verlockend, jeweils einfach eine Begrüßungsnachricht zu generieren, wenn eine neue Konversationsaktualisierung erkannt wird. Dies kann aber zu unerwarteten Ergebnissen führen, wenn auf Ihren Bot über viele verschiedene Kanäle zugegriffen wird.

## <a name="design-for-different-channels"></a>Entwurf für unterschiedliche Kanäle

Sie haben zwar die vollständige Kontrolle über die Informationen, die von Ihrem Bot geliefert werden, aber ggf. nicht darüber, wie die Darstellung dieser Informationen von unterschiedlichen Kanälen implementiert wird. Für einige Kanäle wird eine Konversationsaktualisierung erstellt, wenn ein Benutzer zum ersten Mal eine Verbindung mit dem Kanal herstellt, und erst dann eine separate Konversationsaktualisierung, wenn vom Benutzer eine erste Eingabenachricht empfangen wird. Andere Kanäle generieren beide Aktivitäten, wenn der Benutzer zum ersten Mal eine Verbindung mit dem Kanal herstellt. Wenn Sie einfach auf ein Ereignis zur Konversationsaktualisierung warten und eine Begrüßungsnachricht in einem Kanal mit zwei Aktivitäten zur Konversationsaktualisierung anzeigen, erhält der Benutzer unter Umständen Folgendes:

![Doppelte Begrüßungsnachricht](./media/double_welcome_message.png)

Diese doppelte Nachricht kann vermieden werden, indem nur für das zweite Ereignis zur Konversationsaktualisierung eine erste Begrüßungsnachricht generiert wird. Das zweite Ereignis kann erkannt werden, wenn die beiden folgenden Bedingungen erfüllt sind:
- Ein Ereignis zur Konversationsaktualisierung ist eingetreten.
- Der Konversation wurde ein neues Mitglied (Benutzer) hinzugefügt.

Es ist auch wichtig zu berücksichtigen, wann die Eingabe Ihres Benutzers ggf. nützliche Informationen enthält. Auch dies kann je nach Kanal variieren. Wenn ein Kanal beide Aktivitäten für die Konversationsaktualisierung bei der ersten Verbindungsherstellung des Bots generiert, kann die erste Eingabe des Benutzers erfolgreich als Antwort auf die Eingabeaufforderung mit der Begrüßungsnachricht ausgewertet werden. Ein Beispiel hierfür ist die unten angegebene Konversation.

![Antwort auf Einzeleingabe](./media/single_input_response.png)

Wenn ein Kanal aber auf eine erste Benutzereingabe wartet, bevor ein zweites Ereignis für eine Konversationsaktualisierung generiert wird, wird mit dem gleichen oben verwendeten Code stattdessen die folgende Benutzeroberfläche erstellt:

![Einzelne Eingabe – Falsche Antwort](./media/single_input_wrong_response.png)

Um sicherzustellen, dass Ihre Benutzer auf allen möglichen Kanälen über eine gute Erfahrung verfügen, sollten Sie davon ausgehen, dass die erste Konversationseingabe eines Benutzers keine nützlichen Informationen enthält. Dies ist die bewährte Methode. Sehen Sie die erste Eingabe stattdessen als „Wegwerfdaten“ an, und fordern Sie Ihren Benutzer nach deren Erhalt auf, Informationen anzugeben, die zum Fortführen der Konversation erforderlich sind. Mit diesem Verfahren wird für eine einheitliche Benutzererfahrung gesorgt – unabhängig vom Kanal, der für den ersten Zugriff auf Ihren Bot verwendet wird.

![Verwerfen der ersten Eingabe](./media/no_first_input_response.png)

## <a name="personalize-the-user-experience"></a>Personalisieren der Benutzererfahrung

Nichts fühlt sich für einen Benutzer weniger einladend und unpersönlicher an, als ständig nach Informationen gefragt zu werden, die er bereits angegeben hat. Wenn Ihr Bot Informationen für Benutzer aufbewahrt, die den Bot bereits genutzt haben, ist es ratsam, zuerst diese Informationen zu überprüfen. Falls verfügbar, sollten Sie den Benutzer mit dem Namen begrüßen, den Sie beim vorherigen Besuch gespeichert haben. 

![Begrüßungsnachricht bei erneutem Besuch](./media/welcome_back.png)

In diesem Fall überspringt Ihre Konversationslogik das Abfragen eines Namens und fährt mit der nächsten Konversationsaktivität fort.

Darüber hinaus kann Ihr Bot eine Erfahrung für einen Benutzer auch personalisieren, indem eine passende Antwort auf unerwartete Benutzereingaben gegeben wird, z.B. das Anfordern der Beendigung einer Konversation.

![Reagieren auf das Anfordern der Beendigung](./media/respond_to_exit.png)

Indem Sie Ihre Interaktionen zeitlich passend und an die Konversation angepasst gestalten, erzielen Sie für Ihre Benutzer eine einladendere und befriedigendere Interaktion mit Ihrem Bot.

## <a name="next-steps"></a>Nächste Schritte
> [!div class="nextstepaction"]
> [Senden einer Begrüßungsnachricht an Benutzer](bot-builder-send-welcome-message.md)

-->
