---
title: Aufgabenübersicht | Microsoft-Dokumentation
description: Informationen zu Aufgaben im Conversation Designer-Bot
author: vkannan
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
ROBOTS: NoIndex, NoFollow
ms.openlocfilehash: 685a0296f1bfa5452c28f4ec4d2e4e439f09baca
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39303125"
---
# <a name="tasks-in-conversation-designer-bots"></a>Aufgaben im Conversation Designer-Bot
> [!IMPORTANT]
> Conversation Designer ist noch nicht für alle Kunden verfügbar. Weitere Informationen zur Verfügbarkeit von Conversation Designer folgen später in diesem Jahr.

Bots in Conversation Designer sind aus einer Gruppe verwandter Aufgaben zusammengesetzt. Eine **Aufgabe** ist eine Aktion, die Ihr Bot als Reaktion auf eine bestimmte Benutzeranforderung oder -aktivität ausführt. Beispielsweise kann ein Restaurant-Bot diese Aufgaben beinhalten: „Standorte suchen“, „Tisch reservieren“, „Essen bestellen“ und „Reservierungen verwalten“. Jede Aufgabe entspricht einem anderen Handlungsziel des Benutzers. 

## <a name="triggers-and-actions"></a>Trigger und Aktionen
Eine Aufgabe führt eine Aktion aus, wenn eine Auslöserbedingung erfüllt ist. Alle Aufgaben folgen diesem Modell: **WENN Auslöser**, **FÜHRE AUS Aktion**.

Ein **Auslöser** kann einem von zwei Typen angehören:
1. **Benutzer tritt einer Unterhaltung bei oder verlässt sie** (durch Aktivität eingeleitet): Eine von „Benutzer tritt einer Unterhaltung bei oder verlässt sie“ ausgelöste Aufgabe führt eine Aktion aus, wenn ein Benutzer eine Unterhaltung mit dem Bot beginnt oder beendet. Dieser Auslöser ist für das Senden einer Einführungsnachricht an den Benutzer nützlich. 
2. **Der Benutzer sagt dem Bot etwas oder gibt etwas in ihn ein** (durch Nachricht eingeleitet): Eine Aufgabe, die durch „Benutzer sagt dem Bot etwas oder gibt etwas in ihn ein“ ausgelöst wird, führt eine Aktion als Reaktion auf die Nachricht des Benutzers aus. Die Nachricht des Benutzers wird von einer **Erkennung** interpretiert.

Wenn eine Auslösebedingung erfüllt ist, führt eine Aufgabe eine der folgenden Aktionen aus:
- **Antworten**: Eine Antwort kann eine beliebige Kombination aus angezeigtem Text, gesprochenem Text oder eine Rich Card sein. Mit dieser Aktion sendet der Bot eine Antwort an den Benutzer und schließt die Aufgabe ab. Eine Antwort ist eine Unterhaltung in einem Durchgang, das heißt, die Aufgabe erwartet keine Folgenachrichten vom Benutzer.
- **Dialog starten**: Ein Dialog ist eine Unterhaltung zwischen dem Bot und dem Benutzer in mehreren Durchgängen. Dialoge sind insbesondere dann nützlich, wenn der Bot an einer hin und her springenden Unterhaltung mit dem Benutzer teilnehmen muss, um die zum Abschluss einer Aufgabe erforderlichen Informationen zu sammeln.
- **Ausführen einer Skriptfunktion**: Ihr Bot kann benutzerdefinierte Skripts ausführen, die Sie schreiben, um einen Back-End-Dienst zum Durchführen einer Aufgabe aufzurufen.

## <a name="tips-for-modeling-tasks"></a>Tipps für die Modellierung von Aufgaben

1. Jeder Bot sollte so ausgelegt sein, dass er eine Reihe verschiedener Aufgaben für Ihren Kunden ausführt. Sie sollten die Liste dieser Funktionen durchdenken und eine Aufgabe für jede von ihnen erstellen.
2. Denken Sie beim Einrichten von Auslösern daran, wie Sie die Absicht des Benutzers, eine Aufgabe auszuführen, ermitteln können. Wie *erfährt* der Bot, was der Kunde tun möchte?
3. Wenn Sie Sprachverständnis als Erkennung verwenden, achten Sie darauf, ausreichend Beispiele für die verschiedenen Arten einzuschließen, in denen der Benutzer seine Absicht ausdrücken kann, eine bestimmte Aufgabe auszuführen. „Buche einen Tisch“ sollte die gleiche Aktion auslösen wie „mache eine Reservierung“ oder „reserviere einen Tisch“.
4. Erwägen Sie, Ihrer Sprachverständnisabsicht Beispiele hinzuzufügen, die grammatisch korrekt sind.
5. Wenn Sie Ihren Bot als Cortana-Funktion veröffentlichen möchten, erwägen Sie die Hinzufügung von Musterformulierungen, die bei Cortana-Auslösern gut funktionieren. „Bitten Sie Ihren Bot (Name), etwas zu tun“. 
6. Erstellen Sie für die Codeerkennung Regex-Muster, um die Benutzerabsicht zu bestimmen. Die Codeerkennung kann darüber hinaus Entitäten zurückgeben, die Sie in Ihrer Aufgabe verwenden können.
7. Wenn **antworten** die *Aktion* der Aufgabe ist, kann sie optional eine adaptive Karte für die Wiedergabe auf unterstützten Kanälen beinhalten.

## <a name="next-step"></a>Nächster Schritt
> [!div class="nextstepaction"]
> [LUIS-Erkennung](conversation-designer-luis.md)
