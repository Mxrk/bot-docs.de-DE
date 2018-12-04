---
title: Verwalten des Zustands | Microsoft-Dokumentation
description: Es wird beschrieben, wie Zustände im Bot Builder SDK funktionieren.
keywords: Zustand, Botzustand, Konversationszustand, Benutzerzustand
author: ivorb
ms.author: v-ivorb
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 11/15/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 940dba389205ff339b80f741b8a8aec87ff54f1d
ms.sourcegitcommit: bcde20bd4ab830d749cb835c2edb35659324d926
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/27/2018
ms.locfileid: "52338563"
---
# <a name="managing-state"></a>Verwalten des Zustands

Der Zustand in einem Bot basiert auf den gleichen Paradigmen wie bei modernen Webanwendungen, und über das Bot Framework SDK werden einige Abstraktionen bereitgestellt, um die Verwaltung des Zustands zu vereinfachen.

Wie bei Web-Apps auch, ist ein Bot grundsätzlich zustandslos. Jedes einzelne Segment der Konversation kann auch von einer anderen Instanz Ihres Bots verarbeitet werden. Bei einigen Bots wird diese einfache Einrichtung bevorzugt. Der Bot kann entweder ohne zusätzliche Informationen betrieben werden, oder es wird sichergestellt, dass die erforderlichen Informationen in der eingehenden Nachricht enthalten sind. Bei anderen Bots wird der Zustand benötigt (z.B. die Stelle der Konversation oder zuvor empfangene Daten zum Benutzer), damit der Bot eine nützliche Konversation durchführen kann.

**Warum benötige ich den Zustand?**

Wenn der Zustand verwaltet wird, kann Ihr Bot aussagekräftigere Konversationen durchführen, indem bestimmte Punkte zu einem Benutzer oder einer Konversation gespeichert werden. Wenn mit einem Benutzer beispielsweise schon einmal eine Konversation erfolgt ist, können Sie die bereits erhaltenen Informationen zum Benutzer speichern, damit Sie nicht noch einmal danach fragen müssen. Bei der Zustandsspeicherung werden Daten auch länger als nur für den aktuellen Durchlauf (Turn) gespeichert, damit Informationen auch im Verlauf einer Konversation mit mehreren Durchläufen erhalten bleiben.

Es gibt verschiedene Ebenen für die Nutzung des Zustands. Da dies für Bots relevant ist, werden sie hier beschrieben: Speicherebene, Zustandsverwaltung und Zustandseigenschaftenaccessoren.

## <a name="storage-layer"></a>Speicherebene

Auf dem Back-End, auf dem die Zustandsinformationen gespeichert werden, befindet sich die *Speicherebene*. Wir können uns dies wie unseren physischen Speicher vorstellen, z.B. „In-Memory“, Azure oder Drittanbieterserver.

Das Bot Framework SDK enthält einige Implementierungen für die Speicherebene:

- Bei **Arbeitsspeicher** wird zu Testzwecken In-Memory-Speicher implementiert. In-Memory-Datenspeicher ist nur für lokale Tests bestimmt, da er flüchtig und temporär ist. Die Daten werden jedes Mal gelöscht, wenn der Bot neu gestartet wird.
- Bei **Azure Blob Storage** wird eine Verbindung mit einer Azure Blob Storage-Objektdatenbank hergestellt.
- Bei **Azure Cosmos DB-Speicher** wird eine Verbindung mit einer Cosmos DB-NoSQL-Datenbank hergestellt.

Eine Anleitung zum Herstellen einer Verbindung für andere Speicheroptionen finden Sie unter [Direktes Schreiben in den Speicher](bot-builder-howto-v4-storage.md).

## <a name="state-management"></a>Zustandsverwaltung

Mit der *Zustandsverwaltung* wird das Lesen und Schreiben des Zustands Ihres Bots auf die zugrunde liegende Speicherebene automatisiert. Der Zustand wird in Form von *Zustandseigenschaften* gespeichert. Hierbei handelt es sich im Wesentlichen um Schlüssel-Wert-Paare, die von Ihrem Bot über das Zustandsverwaltungsobjekt gelesen und geschrieben werden können, ohne sich um die zugrunde liegenden Implementierungen kümmern zu müssen. Mit diesen Zustandseigenschaften wird definiert, wie diese Informationen gespeichert werden. Wenn Sie beispielsweise eine Eigenschaft abrufen, die Sie als spezifische Klasse oder Objekt definiert haben, wissen Sie, wie diese Daten strukturiert werden.

Diese Zustandseigenschaften werden in bereichsbezogenen „Buckets“ zusammengefasst, wobei es sich lediglich um Sammlungen für die Organisation dieser Eigenschaften handelt. Das SDK enthält drei dieser Buckets:

- Benutzerzustand
- Konversationszustand
- Zustand der privaten Konversation

Alle diese Buckets sind Unterklassen der *Bot State*-Klasse, von der Ableitungen erstellt werden können, um andere Arten von Buckets mit anderen Bereichen zu definieren.

Diese vordefinierten Buckets beziehen sich jeweils auf eine bestimmte Sichtbarkeit:

- Der Benutzerzustand ist für jeden Durchlauf verfügbar, den der Bot unabhängig von der Konversation mit dem Benutzer über den Kanal durchführt.
- Der Konversationszustand ist für jeden Durchlauf einer bestimmten Konversation unabhängig vom Benutzer (also den Gruppenkonversationen) verfügbar.
- Der Zustand der privaten Konversation bezieht sich auf die jeweilige Konversation und den jeweiligen Benutzer.

> [!TIP]
> Sowohl der Benutzerzustand als auch der Konversationszustand bezieht sich jeweils auf den Kanal.
> Wenn eine Person unterschiedliche Kanäle nutzt, um auf Ihren Bot zuzugreifen, wird sie jeweils als ein anderer Benutzer angezeigt (einer für jeden Kanal), der jeweils einen eigenen Benutzerzustand aufweist.

Die Schlüssel, die für diese einzelnen vordefinierten Buckets verwendet werden, gelten spezifisch für den Benutzer oder die Konversation (oder beides). Beim Festlegen des Werts Ihrer Zustandseigenschaft wird der Schlüssel für Sie intern definiert und umfasst die im Durchlaufkontext enthaltenen Informationen, um sicherzustellen, dass die einzelnen Benutzer bzw. Konversationen im richtigen Bucket und unter der richtigen Eigenschaft angeordnet werden. Die Schlüssel werden wie folgt definiert:

- Vom Benutzerzustand wird ein Schlüssel erstellt, indem die *Kanal-ID* und die *Von-ID* (Channel ID/From ID) verwendet werden. Beispiel: _{Activity.ChannelId}/users/{Activity.From.Id}#YourPropertyName_.
- Mit dem Konversationszustand wird ein Schlüssel erstellt, indem die *Kanal-ID* und die *Konversations-ID* (Channel ID/Conversation ID) verwendet werden. Beispiel: _{Activity.ChannelId}/conversations/{Activity.Conversation.Id}#YourPropertyName_.
- Mit dem Zustand der privaten Konversation wird ein Schlüssel erstellt, indem die *Kanal-ID*, die *Von-ID* und die *Konversations-ID* verwendet werden. Beispiel: _{Activity.ChannelId}/conversations/{Activity.Conversation.Id}/users/{Activity.From.Id}#YourPropertyName_.

### <a name="when-to-use-each-type-of-state"></a>Verwenden der einzelnen Zustandsarten

Der Konversationszustand ist gut für die Nachverfolgung des Konversationskontexts, z.B.:

- Ob der Bot dem Benutzer eine Frage gestellt hat und welche Frage dies war
- Was das aktuelle Thema der Konversation ist oder das vorherige Thema war

Der Benutzerzustand ist gut für Nachverfolgungsinformationen zum Benutzer, z.B.:

- Nicht kritische Benutzerinformationen, z.B. Name und Präferenzen, eine Alarmeinstellung oder eine Alarmpräferenz
- Informationen zur letzten Konversation des Benutzers mit dem Bot
  - Ein Bot für die Produktunterstützung kann beispielsweise verfolgen, nach welchen Produkten der Benutzer gefragt hat.

Der private Konversationszustand ist gut für Kanäle, die Gruppenkonversationen unterstützen, bei denen Sie aber sowohl benutzer- als auch konversationsspezifische Informationen nachverfolgen möchten. Beispielsweise bei einem Clicker-Bot für ein Klassenzimmer:

- Der Bot kann Antworten der Kursteilnehmer auf eine bestimmte Frage aggregieren und anzeigen.
- Der Bot kann die Leistung jedes Kursteilnehmers aggregieren und am Ende der Stunde für jeden Kursteilnehmer jeweils „privat“ bereitstellen.

Weitere Informationen zur Verwendung dieser vordefinierten Buckets finden Sie im [Gewusst-wie-Artikel zum Zustand](bot-builder-howto-v4-state.md).

### <a name="connecting-to-multiple-databases"></a>Herstellen einer Verbindung mit mehreren Datenbanken

Wenn Ihr Bot eine Verbindung mit mehreren Datenbanken herstellen muss, können Sie eine Speicherebene für jede Datenbank erstellen.
Erstellen Sie für jede Speicherebene die Objekte für die Zustandsverwaltung, die Sie zum Unterstützen Ihrer Zustandseigenschaften benötigen.

## <a name="state-property-accessors"></a>Zustandseigenschaftenaccessoren

*Zustandseigenschaftenaccessoren* werden verwendet, um das eigentliche Lesen bzw. Schreiben für eine Ihrer Zustandseigenschaften durchzuführen und *get*-, *set*- und *delete*-Methoden für den Zugriff auf Ihre Zustandseigenschaften aus einem Durchlauf bereitzustellen. Für die Erstellung eines Accessors müssen Sie den Eigenschaftennamen angeben (normalerweise beim Initialisieren Ihres Bots). Anschließend können Sie diesen Accessor verwenden, um diese Eigenschaft für den Zustand Ihres Bots abzurufen und zu ändern.

Mit den Accessoren kann das SDK den Zustand aus dem zugrunde liegenden Speicher abrufen und den *Zustandscache* für Sie aktualisieren. Der Zustandscache ist ein lokaler Cache, der von Ihrem Bot verwaltet und in dem das Zustandsobjekt für Sie gespeichert wird. Auf diese Weise werden Lese- und Schreibvorgänge ermöglicht, ohne dass auf den zugrunde liegenden Speicher zugegriffen werden muss. Wenn er nicht bereits im Cache vorhanden ist, wird durch das Aufrufen der *get*-Methode des Accessors der Zustand abgerufen und im Cache abgelegt. Nach dem Abruf kann die Zustandseigenschaft genau wie jede andere lokale Variable geändert werden.

Mit der *delete*-Methode des Accessors wird die Eigenschaft aus dem Cache entfernt und aus dem zugrunde liegenden Speicher gelöscht.

> [!IMPORTANT]
> Für den ersten Aufruf der *get*-Methode eines Accessors müssen Sie eine Factorymethode für die Erstellung des Objekts bereitstellen, falls es in Ihrem Zustand noch nicht enthalten ist. Wenn keine Factorymethode angegeben ist, tritt eine Ausnahme auf. Ausführliche Informationen zur Nutzung einer Factorymethode finden Sie im [Gewusst wie-Artikel zum Zustand](bot-builder-howto-v4-state.md).

Damit die Änderungen beibehalten werden, die Sie an der vom Accessor erhaltenen Zustandseigenschaft vornehmen, muss die Eigenschaft im Zustandscache aktualisiert werden. Hierfür können Sie die *set*-Methode des Accessors aufrufen. Mit dieser Methode wird der Wert Ihrer Eigenschaft im Cache festgelegt, damit er verfügbar ist, wenn zu einem späteren Zeitpunkt des Durchlaufs das Lesen oder Aktualisieren erforderlich ist. Sie müssen dann [den Zustand speichern](#saving-state), um diese Daten dauerhaft im zugrunde liegenden Speicher abzulegen (damit sie auch nach dem aktuellen Durchlauf verfügbar sind).

### <a name="how-the-state-property-accessor-methods-work"></a>Funktionsweise der Zustandseigenschaftenaccessor-Methoden

Die Accessormethoden stellen den Hauptansatz für die Interaktion Ihres Bots mit dem Zustand dar. Hier sind die einzelnen Funktionsweisen und zugrunde liegenden Ebenen beschrieben:

- Die *get*-Methode des Accessors:
  - Der Accessor fordert die Eigenschaft vom Zustandscache an.
  - Wenn die Eigenschaft im Cache enthalten ist, wird sie zurückgegeben. Andernfalls wird sie aus dem Zustandsverwaltungsobjekt abgerufen.
    Falls sie im Zustand noch nicht verfügbar ist, können Sie die Factorymethode im *get*-Aufruf des Accessors verwenden. -Die *set*-Methode des Accessors:
  - Aktualisiert den Zustandscache mit dem neuen Eigenschaftswert.
- Die *save changes*-Methode des Objekts für die Zustandsverwaltung:
  - Überprüfen Sie die Änderungen an der Eigenschaft im Zustandscache.
  - Schreiben Sie diese Eigenschaft in den Speicher.

## <a name="saving-state"></a>Speichern des Zustands

Wenn Sie die set-Methode des Accessors aufrufen, um den aktualisierten Zustand aufzuzeichnen, wurde diese Zustandseigenschaft noch nicht in Ihrem permanenten Speicher abgelegt, sondern nur im Zustandscache Ihres Bots. Um die im Zustandscache befindlichen Änderungen im permanenten Speicher abzulegen, müssen Sie die *save changes*-Methode des Zustandsverwaltungsobjekts aufrufen. Sie ist unter der oben erwähnten Implementierung der Botzustandsklasse verfügbar (z.B. Benutzerzustand oder Konversationszustand).

Durch das Aufrufen der „save changes“-Methode für ein Zustandsverwaltungsobjekt (also die oben erwähnten Buckets) werden alle Eigenschaften, die Sie für den jeweiligen Bucket bisher eingerichtet haben, im Zustandscache gespeichert. Dies gilt aber nicht für die anderen Buckets, über die Sie unter dem Zustand Ihres Bots ggf. verfügen.

> [!TIP]
> Anhand des Botzustands wird das Verhalten „letzter Schreibvorgang gewinnt“ implementiert, bei dem der letzte Schreibvorgang den vorherigen Schreibzustand außer Kraft setzt. Dieses Verhalten ist ggf. für viele Anwendungen geeignet, aber es sind einige Aspekte zu beachten. Dies gilt vor allem für Szenarien mit horizontaler Skalierung, bei denen bestimmte Parallelitäts- oder Latenzebenen zu berücksichtigen sind.

Falls Sie über benutzerdefinierte Middleware verfügen, über die der Zustand nach Abschluss Ihres Turn-Handlers aktualisiert wird, helfen Ihnen ggf. die Informationen unter [Verarbeiten des Zustands in Middleware](bot-builder-concept-middleware.md#handling-state-in-middleware) weiter.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Dialogzustand](bot-builder-concept-dialog.md#dialog-state)
- [Direktes Schreiben in den Speicher](bot-builder-howto-v4-storage.md)
- [Speichern von Benutzer- und Konversationsdaten](bot-builder-howto-v4-state.md)