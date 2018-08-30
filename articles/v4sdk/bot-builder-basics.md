---
title: Grundlagen für Bot Builder | Microsoft-Dokumentation
description: Grundlegende Bot Builder SDK-Struktur.
keywords: durchlaufkontext, botstruktur, empfang von eingaben
author: ivorb
ms.author: v-ivorb
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 04/18/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 34564b411f911ae82197d5a34cb954a103abe70b
ms.sourcegitcommit: 2dc75701b169d822c9499e393439161bc87639d2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/24/2018
ms.locfileid: "42905414"
---
# <a name="basic-bot-structure"></a>Grundlegende Botstruktur

[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

Der Azure Bot Service und das Bot Builder SDK stellen Bibliotheken, Beispiele und Tools zur Verfügung, die Sie beim Erstellen und Debuggen von Bots unterstützen. Bevor wir jedoch zu sehr ins Detail gehen, ist es wichtig, die Grundstruktur eines Bots und die Funktionsweise zu verstehen. Diese Konzepte gelten unabhängig von der gewählten Programmiersprache. Im Laufe dieses Artikels finden Sie Links zu weiterführenden Inhalten. Außerdem enthält dieser Artikel einen ersten Überblick über die Funktionsweise eines Bots.

Schauen wir uns die grundlegende Struktur eines Bots an.

## <a name="creation-of-your-bot"></a>Erstellen Ihres Bots

Es gibt verschiedene Möglichkeiten, einen Bot zu erstellen, z.B. innerhalb des [Azure-Portals](~/bot-service-quickstart.md), in [Visual Studio](~/dotnet/bot-builder-dotnet-sdk-quickstart.md) oder durch Befehlszeilentools für [JavaScript](~/javascript/bot-builder-javascript-quickstart.md), [Java](~/java/bot-builder-java-quickstart.md) oder [Python](~/python/bot-builder-python-quickstart.md). Nach der Erstellung können Bots auf einem lokalen Computer, auf Azure oder einem anderen Clouddienst ausgeführt werden. Sie funktionieren alle sehr ähnlich, unabhängig davon, wo sie ausgeführt werden oder wie sie erstellt wurden.

## <a name="interaction-with-your-bot"></a>Interaktion mit Ihrem Bot

Ein Bot hat grundsätzlich keine sichtbare Benutzeroberfläche wie eine Website oder App, sondern ein Benutzer interagiert mit ihm über eine [Konversation](~/v4sdk/bot-concepts.md#activities-and-conversations). Je nach Anwendung, die zur Verbindung mit dem Bot verwendet wird (das nennen wir einen [Kanal](~/v4sdk/bot-concepts.md), aber wir werden an dieser Stelle nicht genauer darauf eingehen), werden bestimmte Informationen zwischen dem Benutzer und Ihrem Bot hin- und hergeschickt.

Jede Informationseinheit wird innerhalb unseres Bots als **Aktivität** bezeichnet, und eine Aktivität kann in verschiedenen Formen auftreten. Aktivitäten umfassen sowohl die Kommunikation vom Benutzer, die als **Nachricht** bezeichnet wird, als auch zusätzliche Informationen, die in verschiedenen anderen [Aktivitätstypen](~/bot-service-activities-entities.md) zusammengefasst sind. Diese zusätzlichen Informationen können z.B. der Beitritt eines neuen Teilnehmers zu bzw. der Austritt aus einer Konversation sein, das Beenden einer Konversation usw. Diese Typen der Kommunikation über die Verbindung des Benutzers werden vom zugrundeliegenden System gesendet, ohne dass der Benutzer etwas tun muss.

Der Bot empfängt die Kommunikation und fasst sie in ein Aktivitätsobjekt mit dem richtigen Typ ein, um sie an Ihren Botcode zu übergeben. Alle anderen Aktivitätstypen liefern nützliche Informationen, jedoch ist die interessanteste und häufigste Aktivität die **Nachricht** des Benutzers.

Jede Aktivität, die der Bot erhält, beginnt einen Durchlauf. Das werden wir in Kürze genauer beschreiben.

## <a name="receiving-user-input"></a>Empfangen von Benutzereingaben

Wenn wir eine Nachricht vom Benutzer erhalten, müssen wir verstehen, was er uns sendet. Der einfachste Weg ist, den eingehenden Nachrichtentext einfach einer Zeichenfolge zuzuordnen. Basierend auf der Zeichenfolge, können wir uns für eine Reaktion entscheiden, je nachdem, was Ihr Bot zu erreichen versucht. Dies kann das Reagieren auf den Benutzer, das Aktualisieren von Variablen oder Ressourcen, das Speichern im [Speicher](~/v4sdk/bot-builder-storage-concept.md) oder ähnliches sein.

Es gibt komplexere Möglichkeiten, Benutzereingaben zu erkennen, wie z.B. [LUIS](~/v4sdk/bot-builder-concept-luis.md) oder [QnA Maker](~/v4sdk/bot-builder-howto-qna.md), aber der Zeichenfolgenabgleich ist am einfachsten.

## <a name="defining-a-turn"></a>Definieren eines Durchlaufs

[!INCLUDE [Define a turn](~/includes/snippet-definition-turn.md)]

Die Aktivitätsverarbeitung wird durch den **Adapter** verwaltet, wie unter [Aktivitätsverarbeitung](~/v4sdk/bot-builder-concept-activity-processing.md) näher beschrieben. Wenn der Adapter eine Aktivität empfängt, erstellt er einen **Durchlaufkontext**, um Informationen über die Aktivität und Kontext für den aktuellen Durchlauf bereitzustellen, der verarbeitet wird. Dieser Durchlaufkontext existiert für die Dauer des Durchlaufs und wird dann gelöscht, wodurch das Ende des Durchlaufs gekennzeichnet wird.

Der [Durchlaufkontext](~/v4sdk/bot-builder-concept-activity-processing.md#turn-context) enthält verschiedene Informationen, und auf allen Ebenen des Bots wird das gleiche Objekt verwendet. Dies ist hilfreich, da dieses Durchlaufkontextobjekt verwendet wird, um Informationen zu speichern, die später im Durchlauf benötigt werden könnten.

> [!IMPORTANT]
> Alle **Durchläufe** sind unabhängig voneinander, werden einzeln ausgeführt und können Sie überschneiden. Der Bot kann mehrere Durchläufe, von verschiedenen Benutzern auf verschiedenen Kanälen gleichzeitig verarbeiten. Jeder Durchlauf hat seinen eigenen Durchlaufkontext, Sie sollten jedoch die Komplexität zu berücksichtigen, die in manchen Situationen entsteht.

## <a name="where-to-go-from-here"></a>Weiterführende Informationen

Dieser Artikel hat viele Details ausgeklammert, wie z.B. wie [Aktivitäten verarbeitet werden](~/v4sdk/bot-builder-concept-activity-processing.md), verschiedene [Konversationstypen](~/v4sdk/bot-builder-conversations.md), die Nachverfolgung des [Konversationszustands](~/v4sdk/bot-builder-storage-concept.md) für intelligentere Konversationen usw. Der Rest der Konzeptthemen baut auf den Grundlagen auf und deckt den Rest der Inhalte ab, die benötigt werden, um sowohl Bots als auch den Azure Bot Service zu verstehen. Sie können die nächsten Anweisungen befolgen, um Ihr Wissen auszubauen, oder Sie können zu den für Sie interessanten Themen springen. Probieren Sie den [Schnellstart](~/bot-service-quickstart.md) aus, um Ihren ersten Bot zu erstellen, oder beschäftigen Sie sich eingehender mit der [Entwicklung](~/v4sdk/bot-builder-howto-send-messages.md) von Bots.

## <a name="next-steps"></a>Nächste Schritte

Der Bot Connector Service ermöglicht es Ihrem Bot, mit Benutzern auf verschiedenen Plattformen zu kommunizieren.

> [!div class="nextstepaction"]
> [Kanäle und der Bot Connector Service](~/v4sdk/bot-concepts.md)
