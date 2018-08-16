---
title: Speichern des Status und Zugreifen auf Daten | Microsoft-Dokumentation
description: Hier erfahren Sie mehr über Status-Manager, Unterhaltungsstatus und Benutzerstatus innerhalb des Bot Builder-SDK.
keywords: LUIS, Unterhaltungsstatus, Benutzerstatus, Speicher, Statusverwaltung
author: DeniseMak
ms.author: v-demak
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 02/15/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 56814ab12a85d18e52b0d5ec83fd81682f3b9f60
ms.sourcegitcommit: f95702d27abbd242c902eeb218d55a72df56ce56
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/19/2018
ms.locfileid: "39304624"
---
# <a name="save-state-and-access-data"></a>Speichern des Status und Zugreifen auf Daten
[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

Wesentlich für gute Botentwicklung ist, den Unterhaltungskontext nachzuverfolgen, damit sich der Bot beispielsweise an Antworten auf bereits gestellte Fragen erinnert.
Je nachdem, wofür Ihr Bot verwendet wird, müssen Sie möglicherweise auch den Status verfolgen oder Informationen über die Lebensdauer einer Unterhaltung hinaus speichern.
Der *Status* eines Bots ist eine Information, die er sich merkt, um auf eingehende Nachrichten angemessen zu reagieren. Das Bot Builder SDK stellt Klassen zum Speichern und Abrufen von Statusdaten als Objekt bereit, das einem Benutzer oder einer Unterhaltung zugeordnet ist.

* **Unterhaltungseigenschaften** helfen Ihrem Bot, die aktuelle Unterhaltung mit dem Benutzer nachzuverfolgen. Wenn Ihr Bot eine Reihe von Schritten ausführen oder zwischen Unterhaltungsthemen wechseln muss, können Sie mit den Unterhaltungseigenschaften diese Schritte verwalten oder das aktuelle Thema nachverfolgen. Da die Unterhaltungseigenschaften den Status der aktuellen Unterhaltung wiedergeben, löschen Sie sie in der Regel am Ende einer Sitzung, wenn der Bot eine Aktivität zum _Beenden der Unterhaltung_ empfängt.
* **Benutzereigenschaften** können für viele Zwecke verwendet werden, z.B. um festzustellen, an welcher Stelle eine frühere Unterhaltung unterbrochen wurde, oder um einen wiederkehrenden Benutzer mit seinem Namen zu begrüßen. Wenn Sie die Einstellungen eines Benutzers speichern, können Sie diese Informationen zum Anpassen der Unterhaltung beim nächsten Chat verwenden. Sie können beispielsweise den Benutzer auf einen Nachrichtenartikel zu einem Thema, das ihn interessiert, aufmerksam machen oder ihn benachrichtigen, wenn ein Termin verfügbar wird. Sie sollten die Daten löschen, wenn der Bot eine Aktivität zum _Löschen der Benutzerdaten_ empfängt.

Sie können [Speicher](bot-builder-howto-v4-storage.md) verwenden, um aus einem beständigen Speicher zu lesen und in den Speicher zu schreiben. So kann Ihr Bot beispielsweise gemeinsame Ressourcen aktualisieren, Antworten oder Abstimmungen aufzeichnen oder historische Wetterdaten lesen. Genauso wie eine App den Speicher für ihre Zwecke nutzt, kann Ihr Bot eine Unterhaltung mit Ihrem Benutzer verwenden.

<!-- 
*Conversation state* pertains to the current conversation that the user is having with your bot. When the conversation ends, your bot deletes this data.

You can also store *user state* that persists after a conversation ends. For example, if you store a user's preferences, you can use that information to customize the conversation the next time you chat. For example, you might alert the user to a news article about a topic that interests her, or alert a user when an appointment becomes available. 
-->

<!-- You should generally avoid saving state using a global variable or function closures.
Doing so will create issues when you want to scale out your bot. Instead, use the conversation state and user state middleware that the BotBuilder SDK provides --> 


## <a name="types-of-underlying-storage"></a>Typen zugrunde liegenden Speichers

Das SDK stellt eine Botstatus-Manager-Middleware bereit, um die Unterhaltung und den Benutzerstatus zu erhalten. Auf den Status kann über den Kontext des Bot zugegriffen werden. Dieser Status-Manager kann Azure Table Storage, Datei- und Arbeitsspeicher als zugrunde liegenden Datenspeicher verwenden. Sie können für Ihren Bot auch Ihre eigenen Speicherkomponenten erstellen.

Mit Azure Table Storage erstellte Bots können statusfrei sein und über mehrere Serverknoten hinweg skaliert werden.

> [!NOTE] 
> Datei-und Arbeitsspeicher lassen sich nicht über Knoten hinweg skalieren.

## <a name="writing-directly-to-storage"></a>Direktes Schreiben in den Speicher

Sie können das Bot Builder SDK auch verwenden, um Daten direkt aus dem Speicher zu lesen und in den Speicher zu schreiben, ohne Middleware oder den Botkontext zu verwenden. Dies ist ggf. für Daten geeignet, die Ihr Bot verwendet, die aus einer Quelle außerhalb des Unterhaltungsflusses Ihres Bots stammen.

Angenommen, Ihr Bot erlaubt dem Benutzer, nach dem Wetterbericht zu fragen, und Ihr Bot ruft den Wetterbericht für ein bestimmtes Datum ab, indem er ihn aus einer externen Datenbank liest. Der Inhalt der Wetterdatenbank ist nicht von Benutzerinformationen oder dem Unterhaltungskontext abhängig, sodass Sie ihn direkt aus dem Speicher lesen können, anstatt den Status-Manager zu verwenden.  Weitere Informationen finden Sie unter [Direktes Schreiben in den Speicher](bot-builder-howto-v4-storage.md).

## <a name="next-steps"></a>Nächste Schritte

Als Nächstes geht es um das Verarbeiten von Aktivitäten und die entsprechenden Reaktionen.

> [!div class="nextstepaction"]
> [Aktivitätsverarbeitung](bot-builder-concept-activity-processing.md)

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Speichern des Status](bot-builder-howto-v4-state.md)
- [Direktes Schreiben in den Speicher](bot-builder-howto-v4-storage.md)
