---
title: Bot Framework – Spezifikation | Microsoft-Dokumentation
description: Bot Framework – Spezifikation
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 03/07/2018
ms.openlocfilehash: 0406d489f7d1e27131b4b01411e86850ca4a17b8
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39298620"
---
# <a name="bot-framework----activity"></a>Bot Framework – Aktivität

## <a name="abstract"></a>Zusammenfassung

Das Aktivitätsschema von Bot Framework ist eine Darstellung auf Anwendungsebene der Konversationsaktionen von Personen und automatisierter Software. Das Schema enthält Festlegungen für die Kommunikation von Text-, Multimedia- und anderen Aktionen ohne Inhalte wie soziale Interaktionen oder Eingabeindikatoren.

Dieses Schema wird im Bot Framework-Protokoll verwendet und von Microsoft-Chatsystemen und interoperablen Bots und Clients aus vielen Quellen implementiert.

## <a name="table-of-contents"></a>Inhaltsverzeichnis

1. [Einführung](#introduction)
2. [Grundlegende Struktur der Aktivitäten](#basic-activity-structure)
3. [message-Aktivität](#message-activity)
4. [contactRelationUpdate-Aktivität](#contact-relation-update-activity)
5. [conversationUpdate-Aktivität](#conversation-update-activity)
6. [endOfConversation-Aktivität](#end-of-conversation-activity)
7. [event-Aktivität](#event-activity)
8. [invoke-Aktivität](#invoke-activity)
9. [installationUpdate-Aktivität](#installation-update-activity)
10. [messageDelete-Aktivität](#message-delete-activity)
11. [messageUpdate-Aktivität](#message-update-activity)
12. [messageReaction-Aktivität](#message-reaction-activity)
13. [typing-Aktivität](#typing-activity)
14. [Komplexe Typen](#complex-types)
15. [Referenzen](#references)
16. [Anhang I – Änderungen](#appendix-i---changes)
17. [Anhang II – Nicht-IRI-Entitätstypen](#appendix-ii---non-iri-entity-types)
18. [Anhang III – Protokolle, die die invoke-Aktivität verwenden](#appendix-iii---protocols-using-the-invoke-activity)

## <a name="introduction"></a>Einführung

### <a name="overview"></a>Übersicht

Das Schema der Bot Framework-Aktivitäten Schema stellt das Konversationsverhalten von Menschen und automatisierter Software in Chat-, E-Mail- und anderen Programmen zur Textinteraktion dar. Jedes Aktivitätsobjekt enthält ein Feld „type“ und steht für eine einzelne Aktion: in den meisten Fällen für das Senden von Textinhalt, aber auch für das Anfügen von Multimediainhalten und von Verhalten, das nicht inhaltsbezogen ist, z.B. einer Schaltfläche „Gefällt mir“ oder einem Eingabeindikator.

Dieses Dokument enthält eine Beschreibung für jeden Aktivitätstyp und beschreibt die erforderlichen und optionalen Felder, die enthalten sein können. Außerdem werden die Rollen von Client und Server definiert, und Sie erfahren, welche Felder von welchem Teilnehmer verwaltet und welche ignoriert werden können.

Diese Spezifikation umfasst drei Rollen: Clients, die Aktivitäten für den Benutzer senden und empfangen, Bots, die Aktivitäten senden und empfangen und in der Regel automatisiert sind, und den Kanal, der Aktivitäten speichert und zwischen Clients und Bots weiterleitet.

Auch wenn in dieser Spezifikation Aktivitäten zwischen Rollen übertragen werden müssen, wird der genaue Ablauf der Übertragung hier nicht beschrieben.

Zur Verringerung des Umfangs werden visuelle interaktive Karten in dieser Spezifikation nicht definiert. Lesen Sie stattdessen die Spezifikation [Adaptive Karten](https://adaptivecards.io) [[11](#references)]. Diese Karte und andere nicht definierte Kartentypen können als Anlagen in Bot Framework-Aktivitäten eingefügt werden.

### <a name="requirements"></a>Requirements (Anforderungen)

Die Schlüsselwörter „MUSS“, „DARF NICHT“, „ERFORDERLICH“, „SOLL“, „SOLL NICHT“, „SOLLTE“, „SOLLTE NICHT“, „EMPFOHLEN“, „KANN“ und „OPTIONAL“ in diesem Dokument sind wie in [RFC 2119](https://tools.ietf.org/html/rfc2119) [[1](#references)] zu verstehen.

Eine Implementierung ist nicht konform, wenn sie Anforderungen der Stufe MUSS oder ERFORDERLICH für die Protokolle, die sie implementiert, nicht erfüllt. Eine Implementierung, die alle Anforderungen der Stufen MUSS oder ERFORDERLICH sowie der Stufe SOLL für seine Protokolle erfüllt, gilt als „bedingungslos konform“. Eine, die alle MUSS-Anforderungen, aber nicht alle SOLL-Anforderungen für ihre Protokolle erfüllt, gilt als „bedingt konform“.

### <a name="numbered-requirements"></a>Nummerierte Anforderungen

Zeilen, die mit Markern der Form `RXXXX` beginnen, sind spezifische Anforderungen, auf die anhand der Nummer in der Diskussion außerhalb dieses Dokument verwiesen werden soll. Sie haben keine höhere oder niedrigere Gewichtung als normative Anweisungen, die außerhalb von `RXXXX`-Zeilen stehen.

`R1000`: Bearbeiter dieser Spezifikation KÖNNEN neue `RXXXX`-Anforderungen hinzufügen. Sie SOLLTEN numerische `RXXXX`-Werte finden, die den Flow des Dokuments beibehalten.

`R1001`: Bearbeiter DÜRFEN vorhandene `RXXXX`-Anforderungen NICHT neu nummerieren.

`R1002`: Bearbeiter KÖNNEN `RXXXX`-Anforderungen löschen oder ändern. Bei einer Überarbeitung SOLLTEN Bearbeiter den vorhandenen `RXXXX`-Wert beibehalten, wenn das Thema der Anforderung zum größten Teil erhalten bleibt.

`R1003`: Bearbeiter SOLLTEN zurückgezogene `RXXXX`-Werte NICHT wiederverwenden. Eine Liste der gelöschten Werte KANN am Ende dieses Dokuments geführt werden.

### <a name="terminology"></a>Begriff

activity
> Eine Aktion, die durch einen Bot, einen Kanal oder ein Client ausgedrückt wird und mit dem Aktivitätsschema konform ist.

channel
> Software, die Aktivitäten sendet und empfängt und diese in oder aus Chat- oder Anwendungsverhalten umwandelt. Kanäle sind der autoritative Speicher für Aktivitätsdaten.

Bot
> Software, die Aktivitäten sendet und empfängt und automatisierte, halbautomatisierte oder vollständig manuelle Antworten generiert. Bots verfügen über Endpunkte, die in Kanälen registriert sind.

Client
> Software, die Aktivitäten sendet und empfängt – in der Regel im Auftrag von menschlichen Benutzern. Clients verfügen nicht über Endpunkte.

Absender
> Software, die eine Aktivität überträgt

Empfänger
> Software, die eine Aktivität annimmt

endpoint
> Ein programmgesteuert adressierbarer Speicherort, an dem Aktivitäten von einem Bot oder Kanal empfangen werden können kann.

address
> Eine ID oder Adresse, über die ein Benutzer oder Bot kontaktiert werden kann.

Feld
> Ein benannter Wert innerhalb einer Aktivität oder eines geschachtelten Objekts.

### <a name="overall-organization"></a>Allgemeine Organisation

Das Aktivitätsobjekt ist eine flache Liste von Name-Wert-Paaren, von denen einige primitive und andere komplexe (geschachtelte) Objekte sind. Das Aktivitätsobjekt wird meist im JSON-Format gebildet, es kann aber auch in speicherinternen Datenstrukturen in .NET oder JavaScript definiert werden.

Das Feld `type` der Aktivität legt die Bedeutung der Aktivität und der darin enthaltenen Felder fest. Abhängig von der Rolle des Teilnehmers (Client, Bot oder Kanal) sind die einzelnen Felder obligatorisch oder optional oder werden ignoriert. Das Feld `id` wird z.B. durch den Kanal verwaltet und ist in einigen Fällen obligatorisch. Es wird jedoch ignoriert, wenn es von einem Bot gesendet wird.

Felder, die die Identität der Aktivität sowie der Teilnehmer beschreiben, z.B. die Felder `type` und `from`, werden von allen Aktivitäten genutzt. In vielen Programmiersprachen ist es sinnvoll, diese Felder in einem Basistyp zu organisieren, von dem andere, präzisere Aktivitätstypen abgeleitet werden können.

Bei der Speicherung und Übertragung von Aktivitäten werden einige Felder möglicherweise im Transportmechanismus dupliziert. Wenn eine Aktivität z.B. per HTTP POST an eine URL gesendet wird, die die Konversations-ID enthält, leitet der Empfänger den Wert eventuell ab, ohne dass er im Text der Aktivität enthalten ist. In diesem Dokument werden lediglich die abstrakten Anforderungen für diese Felder beschrieben. Es obliegt den Steuerprotokollen, festzulegen, ob die Werte explizit deklariert werden müssen oder ob implizite oder abgeleitete Werte zulässig sind.

Wenn ein Bot oder Client eine Aktivität an einen Kanal sendet, ist dies in der Regel eine Anforderung, die Aktivität aufzuzeichnen. Wenn ein Kanal eine Aktivität an einen Bot oder Client sendet, ist dies in der Regel eine Benachrichtigung darüber, dass die Aktivität bereits aufgezeichnet wurde.

## <a name="basic-activity-structure"></a>Grundlegende Struktur der Aktivitäten

In diesem Abschnitt werden die Anforderungen an die grundlegende Struktur von Aktivitätsobjekten beschrieben.

Aktivitätsobjekte enthalten eine flache Liste von Name-Wert-Paaren, die als Felder bezeichnet werden. Felder können primitive Typen sein. Als allgemeines Austauschformat wird JSON verwendet, und auch wenn nicht alle Aktivitäten jedes Mal in JSON serialisiert werden, müssen sie stets in JSON serialisierbar sein. Dies erlaubt es Implementierungen, nur einen einfachen Satz von Konventionen für die Verarbeitung bekannter und unbekannter Aktivitätsfeldern zu nutzen.

`R2001`: Aktivitäten MÜSSEN in das JSON-Format serialisierbar sein. Dies gilt auch für die Einhaltung etwa von Einschränkungen zur Eindeutigkeit der Felder.

`R2002`: Empfänger KÖNNEN Feldnamen ohne ordnungsgemäße Groß-/Kleinschreibung zulassen, obwohl dies nicht erforderlich ist. Empfänger KÖNNEN Aktivitäten ablehnen, die keine Felder mit der richtigen Groß-/Kleinschreibung enthalten.

`R2003`: Empfänger KÖNNEN Aktivitäten ablehnen, die Feldwerte enthalten, deren Typen nicht mit den Werttypen übereinstimmen, die in dieser Spezifikation beschrieben werden.

`R2004`: Sofern nicht anders angegeben, SOLLTEN Absender NICHT leere Zeichenfolgenwerte für Felder mit Zeichenfolgen verwenden.

`R2005`: Sofern nicht anders angegeben, KÖNNEN die Absender zusätzliche Felder innerhalb der Aktivität oder geschachtelte komplexe Objekte einfügen. Empfänger MÜSSEN Felder akzeptieren, die sie nicht verstehen.

`R2006`: Empfänger SOLLTEN Ereignisse von Typen akzeptieren, die sie nicht verstehen.

### <a name="type"></a>Typ

Das Feld `type` legt die Bedeutung der jeweiligen Aktivität fest. Es handelt sich laut Konvention um kurze Zeichenfolgen (z.B. „`message`“). Absender können eigene Typen auf Anwendungsebene definieren. Sie sollten jedoch keine Werte auswählen, die in Zukunft zu Konflikten mit klar definierten Werten führen könnten. Wenn Absender URIs als Werte für das Feld „type“ verwenden, SOLLTEN sie NICHT URI-Leitervergleiche implementieren, um Äquivalenz herzustellen.

`R2010`: Aktivitäten MÜSSEN ein Feld `type` mit dem Werttyp Zeichenfolge enthalten.

`R2011`: Zwei `type`-Werte sind nur dann gleichwertig, wenn ihre Ordnungszahl identisch ist.

`R2012`: Ein Absender KANN `type`-Werte für eine Aktivität generieren, die in diesem Dokument nicht definiert werden.

`R2013`: Ein Kanal SOLLTE Aktivitäten ablehnen, deren Typ er nicht versteht.

`R2014`: Ein Bot oder Client SOLLTE Aktivitäten ignorieren, deren Typ er nicht versteht.

### <a name="channel-id"></a>Kanal-ID

Das Feld `channelId` legt den Kanal und den autoritativen Speicher für die Aktivität fest. Der Wert des Felds `channelId` ist ein Zeichenfolgentyp.

`R2020`: Aktivitäten MÜSSEN ein Feld `channelId` mit dem Werttyp Zeichenfolge enthalten.

`R2021`: Zwei `channelId`-Werte sind nur dann gleichwertig, wenn ihre Ordnungszahl identisch ist.

`R2022`: Ein Kanal KANN Aktivitäten ignorieren oder ablehnen, die er ohne einen erwarteten `channelId`-Wert empfängt.

### <a name="id"></a>ID

Das Feld `id` legt die Identität der Aktivität fest, nachdem diese im Kanal aufgezeichnet wurde. Laufende Aktivitäten, die noch nicht aufgezeichnet wurden, haben keine Identitäten. Nicht allen Aktivitäten werden Identitäten zugewiesen (einer [typing-Aktivität](#typing-activity) wird z.B. möglicherweise keine `id` zugewiesen). Der Wert des Felds `id` ist ein Zeichenfolgentyp.

`R2030`: Kanäle SOLLTEN ein Feld `id` enthalten, sofern es für diese Aktivität verfügbar ist.

`R2031`: Clients und Bots SOLLTEN das Feld `id` NICHT in Aktivitäten einfügen, die sie generieren.

Zur Vereinfachung der Implementierung sollte davon ausgegangen werden, dass die anderen Teilnehmer keine außerordentlichen Kenntnisse der Aktivitäts-IDs haben und nur den Ordnungszahlvergleich verwenden, um Äquivalenz herzustellen.

Beispielsweise kann ein Kanal hexadezimal codierte GUIDs für jede Aktivitäts-ID verwenden. Auch wenn GUIDs, die in Großbuchstaben codiert sind, logisch äquivalent zu GUIDs sind, die in Kleinbuchstaben codiert wurden, SOLLTEN Absender nach Möglichkeit NICHT diese alternativen Codierungen verwenden. Die normalisierte Version der einzelnen IDs wird vom autoritativen Speicher, also dem Kanal, festgelegt.

`R2032`: Beim Generieren von `id`-Werten SOLLTEN Absender Werte auswählen, deren Äquivalenz durch einen Ordnungszahlvergleich hergestellt werden kann. Jedoch KÖNNEN Absender und Empfänger logische Äquivalenz der beiden Werte zulassen, auch wenn ihre Ordnungszahl nicht äquivalent ist, sofern sie die speziellen Umstände kennen.

Das Feld `id` soll die Deduplizierung ermöglichen, es ist aber in den meisten Anwendungen nicht zulässig.

`R2033`: Empfänger KÖNNEN Aktivitäten nach ID deduplizieren, Absender SOLLTEN sich jedoch NICHT darauf verlassen, dass die Empfänger diese Deduplizierung ausführen.

### <a name="timestamp"></a>Zeitstempel

Das Feld `timestamp` zeichnet die genaue UTC-Zeit des Auftretens der Aktivität auf. Aufgrund der verteilten Struktur von Computersystemen ist besonders der Zeitpunkt wichtig, zu dem der Kanal (der autoritative Speicher) die Aktivität aufzeichnet. Die Uhrzeit, zu der ein Client oder Bot eine Aktivität initiiert, kann separat im Feld `localTimestamp` übermittelt werden. Der Wert des Felds `timestamp` ist ein nach [ISO 8601](https://www.iso.org/iso-8601-date-and-time-format.html) [[2](#references)] codierter datetime-Wert innerhalb einer Zeichenfolge.

`R2040`: Kanäle SOLLTEN ein Feld `timestamp` enthalten, sofern es für diese Aktivität verfügbar ist.

`R2041`: Clients und Bots SOLLTEN das Feld `timestamp` NICHT in Aktivitäten einfügen, die sie generieren.

`R2042`: Clients und Bots SOLLTEN `timestamp` NICHT verwenden, um Aktivitäten abzulehnen, da sie andernfalls als „außer Betrieb“ gelten könnten. Allerdings KÖNNEN sie `timestamp` verwenden, um die Aktivitäten in einer Benutzeroberfläche oder für die Downstreamverarbeitung zu sortieren.

`R2043`: Absender SOLLTEN den Wert von `timestamp`-Feldern immer als UTC-Zeit codieren, und sie SOLLTEN immer Z als explizites UTC-Kennzeichen im Wert verwenden.

### <a name="local-timestamp"></a>Lokaler Zeitstempel

Das Feld `localTimestamp` gibt Datum, Uhrzeit und Zeitzonenversatz des Zeitpunkts an, zu dem die Aktivität generiert wurde. Dies kann sich vom `timestamp` der Aufzeichnung der Aktivität in UTC unterscheiden. Der Wert des Felds `localTimestamp` ist ein nach ISO 8601 [[3](#references)] codierter datetime-Wert innerhalb einer Zeichenfolge.

`R2050`: Clients und Bots KÖNNEN das Feld `localTimestamp` in ihre Aktivitäten einfügen. Sie SOLLTEN den Zeitzonenversatz innerhalb des codierten Werts explizit angeben.

`R2051`: Kanäle SOLLTEN `localTimestamp` beibehalten, wenn sie Aktivitäten von einem Absender zu Empfängern weiterleiten.

### <a name="from"></a>From

Das Feld `from` beschreibt, welcher Client, Bot oder Kanal die Aktivität generiert hat. Der Wert des Felds `from` ist ein komplexes Objekt vom Typ [Kanalkonto](#channel-account).

Das Feld `from.id` gibt an, wer eine Aktivität generiert hat. In den meisten Fällen ist dies ein anderer Benutzer oder Bot innerhalb des Systems. In einigen Fällen gibt das Feld `from` den Kanal selbst an.

`R2060`: Kanäle MÜSSEN die Felder `from` und `from.id` beim Generieren einer Aktivität einfügen.

`R2061`: Bots und Clients SOLLTEN die Felder `from` und `from.id` beim Generieren einer Aktivität einfügen. Ein Kanal KANN eine Aktivität ablehnen, wenn die Felder `from` und `from.id` fehlen.

Das Feld `from.name` ist optional. Es enthält den Anzeigenamen für das Konto innerhalb des Kanals. Kanäle SOLLTEN diesen Wert einfügen, damit Clients und Bots ihre Benutzeroberflächen und Back-End-Systeme damit auffüllen können. Bots und Clients SOLLTEN diesen Wert NICHT an Kanäle senden, die einen zentralen Datensatz ihres Speichers führen, sie KÖNNEN diesen Wert aber an Kanäle senden, die den Wert für jede Aktivität auffüllen (z.B. E-Mail-Kanäle).

`R2062`: Kanäle SOLLTEN das Feld `from.name` einfügen, wenn das Feld `from` vorhanden und `from.name` verfügbar ist.

`R2063`: Bots und Clients SOLLTEN das Feld `from.name` NICHT einfügen, wenn es eine semantische Bedeutung innerhalb des Kanals hat.

### <a name="recipient"></a>Recipient

Das Feld `recipient` beschreibt, welcher Client oder Bot diese Aktivität empfängt. Dieses Feld ist nur sinnvoll, wenn eine Aktivität an genau einen Empfänger übertragen wird. Es ist nicht aussagekräftig, wenn ein Broadcast an mehrere Empfänger erfolgt (wenn z.B. eine Aktivität an einen Kanal gesendet wird). Dieses Feld dient dazu, dass sich der Empfänger selbst identifizieren kann. Dies ist hilfreich, wenn ein Client oder Bot innerhalb des Kanals über mehrere Identitäten verfügt. Der Wert des Felds `recipient` ist ein komplexes Objekt vom Typ [Kanalkonto](#channel-account).

`R2070`: Kanäle MÜSSEN die Felder `recipient` und `recipient.id` einfügen, wenn sie eine Aktivität an genau einen Empfänger senden.

`R2071`: Bots und Clients SOLLTEN das Feld `recipient` beim Generieren einer Aktivität NICHT einfügen.

Das Feld `recipient.name` ist optional. Es enthält den Anzeigenamen für das Konto innerhalb des Kanals. Kanäle SOLLTEN diesen Wert einfügen, damit Clients und Bots ihre Benutzeroberflächen und Back-End-Systeme damit auffüllen können.

`R2072`: Kanäle SOLLTEN das Feld `recipient.name` einfügen, wenn das Feld `recipient` vorhanden und `recipient.name` verfügbar ist.

### <a name="conversation"></a>Unterhaltung

Das Feld `conversation` beschreibt die Konversation, zu der die Aktivität gehört. Der Wert des Felds `conversation` ist ein komplexes Objekt vom Typ [Konversationskonto](#conversation-account).

`R2080`: Kanäle, Bots und Clients MÜSSEN die Felder `conversation` und `conversation.id` beim Generieren einer Aktivität einfügen.

Das Feld `conversation.name` ist optional und stellt den Anzeigenamen für die Konversation dar, sofern er vorhanden und verfügbar ist.

`R2081`: Kanäle SOLLTEN die Felder `conversation.name` und `conversation.isGroup` einfügen, sofern sie verfügbar sind.

`R2082`: Bots und Clients SOLLTEN das Feld `conversation.name` NICHT einfügen, wenn es eine semantische Bedeutung innerhalb des Kanals hat.

`R2083`: Bots und Clients SOLLTEN das Feld `conversation.isGroup` NICHT in Aktivitäten einfügen, die sie generieren.

### <a name="reply-to-id"></a>Antworten-an-ID

Das Feld `replyToId` gibt die vorherige Aktivität an, auf die die aktuelle Aktivität eine Antwort ist. In diesem Feld können Konversationsthreads und geschachtelte Kommentare zwischen Teilnehmern ausgetauscht werden. `replyToId` ist nur innerhalb der aktuellen Konversation gültig. (Informationen zu Verweisen auf andere Konversationen finden Sie unter [relatesTo](#relates-to).) Der Wert des Felds `replyToId` ist eine Zeichenfolge.

`R2090`: Absender SOLLTEN `replyToId` in eine Aktivität einfügen, wenn diese eine Antwort auf eine andere Aktivität ist.

`R2091`: Ein Kanal KANN eine Aktivität ablehnen, wenn deren `replyToId` nicht auf eine gültige Aktivität in der Konversation verweist.

`R2092`: Bots und Clients KÖNNEN `replyToId` auslassen, wenn sie wissen, dass der Kanal dieses Feld nicht nutzt, auch wenn die gesendete Aktivität eine Antwort auf eine andere Aktivität ist.

### <a name="entities"></a>Entitäten

Das Feld `entities` enthält eine flache Liste von Metadatenobjekten zu dieser Aktivität. Im Gegensatz zu Anlagen (siehe Feld [attachments](#attachments)) gelten Entitäten nicht unbedingt als Inhaltselemente für die Benutzerinteraktion und sollten ignoriert werden, wenn sie nicht verstanden werden. Absender können Entitäten einfügen, wenn sie denken, dass diese für einen Empfänger nützlich sein könnten. Dies gilt auch dann, wenn sie nicht wissen, ob der Empfänger sie akzeptieren kann. Der Wert jedes Elements der Liste `entities` ist ein komplexes Objekt vom Typ [Entität](#entity).

`R2100`: Absender SOLLTEN das Feld `entities` auslassen, wenn es keine Elemente enthält.

`R2101`: Absender KÖNNEN mehrere Entitäten desselben Typs senden, sofern die Entitäten unterschiedliche Bedeutung haben.

`R2102`: Absender DÜRFEN NICHT zwei oder mehr Entitäten mit identischen Typen und Inhalten einfügen.

`R2103`: Absender und Empfänger SOLLTEN sich NICHT auf eine bestimmte Reihenfolge bei den in einer Aktivität enthaltenen Entitäten verlassen.

### <a name="channel-data"></a>Kanaldaten

Daten zur Erweiterbarkeit im Aktivitätsschema werden grundsätzlich im Feld `channelData` organisiert. Dies vereinfacht die Verwendung von SDKs, die das Protokoll implementieren. Das Format des `channelData`-Objekts wird vom Kanal definiert, der die Aktivität sendet oder empfängt.

`R2200`: Kanäle SOLLTEN NICHT `channelData`-Formate definieren, die primitive JSON-Typen (z.B. Zeichenfolgen, ganze Zahlen) sind. Sie SOLLTEN stattdessen `channelData` als komplexen Typ definieren oder undefiniert lassen.

`R2201`: Wenn das `channelData`-Format für den aktuellen Kanal nicht definiert ist, SOLLTEN Empfänger den Inhalt von `channelData` ignorieren.

### <a name="service-url"></a>Dienst-URL

Aktivitäten werden häufig asynchron über separate Transportverbindungen für das Senden und Empfangen von Datenverkehr übermittelt. Das Feld `serviceUrl` wird von Kanälen dazu verwendet, die URL anzugeben, an die Antworten auf die aktuelle Aktivität gesendet werden können. Der Wert des Felds `serviceUrl` ist ein Zeichenfolgentyp.

`R2300`: Kanäle MÜSSEN das Feld `serviceUrl` in alle Aktivitäten einfügen, die sie an Bots senden.

`R2301`: Kanäle SOLLTEN das Feld `serviceUrl` NICHT in Aktivitäten an Clients einfügen, die angeben, den Kanalendpunkt bereits zu kennen.

`R2302`: Bots und Clients SOLLTEN das Feld `serviceUrl` NICHT in Aktivitäten auffüllen, die sie generieren.

`R2302`: Kanäle MÜSSEN den Wert von `serviceUrl` in Aktivitäten ignorieren, die von Bots und Clients gesendet werden.

`R2304`: Kanäle SOLLTEN stabile Werte für das Feld `serviceUrl` verwenden, da Bots diese für längere Zeiträume beibehalten können.

## <a name="message-activity"></a>message-Aktivität

Nachrichtenaktivitäten (message) stellen Inhalte dar, die auf der Benutzeroberfläche einer Konversation angezeigt werden sollen. Nachrichtenaktivitäten können Text, Sprache, interaktive Karten und binäre oder unbekannte Anlagen enthalten. Kanäle erfordern in der Regel maximal ein solches Element, damit die Nachrichtenaktivität als wohlgeformt gilt.

Nachrichtenaktivitäten werden durch den `type`-Wert `message` gekennzeichnet.

### <a name="text"></a>Text

Das Feld `text` enthält Textinhalt, entweder im Markdown- oder XML-Format oder als einfacher Text. Das Format wird durch das Feld [`textFormat`](#text-format) festgelegt. Wenn keine oder eine unpräzise Angabe erfolgt, wird Nur-Text verwendet. Der Wert des Felds `text` ist ein Zeichenfolgentyp.

`R3000`: Das Feld `text` KANN eine leere Zeichenfolge enthalten, um anzugeben, dass Text ohne Inhalt gesendet wird.

`R3001`: Kanäle SOLLTEN Text im `markdown`-Format so handhaben, dass der Kanal diesen einfach verarbeiten kann.

### <a name="text-format"></a>Textformat

Das Feld `textFormat` gibt an, ob das Feld [`text`](#text) als [Markdown](https://daringfireball.net/projects/markdown/) [[4](#references)], Nur-Text oder XML interpretiert werden soll. Der Wert des Felds `textFormat` ist vom Typ Zeichenfolge mit den definierten Werten `markdown`, `plain` und `xml`. Standardwert: `plain`. Dieses Feld ist nicht für die Erweiterung mit beliebigen Werten vorgesehen.

Das Feld `textFormat` steuert zusätzliche Felder in Anlagen usw. Diese Beziehung wird in diesen Feldern an anderer Stelle in diesem Dokument beschrieben.

`R3010`: Wenn ein Absender das Feld `textFormat` einfügt, SOLLTE er nur definierte Werte senden.

`R3011`: Absender SOLLTEN `textFormat` auslassen, wenn der Wert `plain` lautet.

`R3012`: Empfänger SOLLTEN nicht definierte Werte als `plain` interpretieren.

`R3013`: Bots und Clients SOLLTEN den Wert `xml` NICHT senden, es sei denn, sie wissen im Voraus, dass der Kanal diesen und die Merkmale des unterstützten XML-Dialekts unterstützt.

`R3014`: Kanäle SOLLTEN `markdown`- oder `xml`-Inhalte NICHT an Bots senden.

`R3015`: Kanäle SOLLTEN mindestens die `textformat`-Werte `plain` und `markdown` akzeptieren.

`R3016`: Kanäle KÖNNEN den `textformat`-Wert `xml` ablehnen.

### <a name="locale"></a>Gebietsschema

Das Feld `locale` gibt den Sprachcode für das Feld [`text`](#text) an. Der Wert des Felds `locale` ist ein [ISO 639](https://www.iso.org/iso-639-language-codes.html)-Code [[5](#references)] innerhalb einer Zeichenfolge.

`R3020`: Empfänger SOLLTEN fehlende und unbekannte Werte des Felds `locale` als unbekannt behandeln.

`R3021`: Empfänger SOLLTEN Aktivitäten mit einem unbekannten Gebietsschema NICHT ablehnen.

### <a name="speak"></a>Speak

Das Feld `speak` gibt an, wie die Aktivität über ein System zur Sprachsynthese gesprochen werden soll. Das Feld wird nur verwendet, um die Sprachwiedergabe anzupassen, wenn der Standardwert als unzureichend eingestuft wird. Es ersetzt die Sprachsynthese für alle Inhalte innerhalb der Aktivität, einschließlich Text, Anlagen und Zusammenfassungen. Der Wert des Felds `speak` ist innerhalb einer Zeichenfolge [SSML](https://www.w3.org/TR/speech-synthesis/)-codiert [[6](#references)]. Entpackter Text ist zulässig und wird automatisch in reines SSML umgewandelt.

`R3030`: Das Feld `speak` KANN eine leere Zeichenfolge enthalten, um anzugeben, dass keine Sprachausgabe generiert werden soll.

`R3031`: Empfänger, die keine Sprachausgabe generieren können, SOLLTEN das Feld `speak` ignorieren.

`R3032`: Wenn kein Stamm-SSML-Element gefunden wird, MÜSSEN Empfänger den enthaltenen Text als internen Inhalt eines SSML-`<speak>`-Tag annehmen, dem ein gültiger [XML-Prolog](https://www.w3.org/TR/xml/#sec-prolog-dtd) [[7](#references)] vorangestellt ist, und andernfalls in ein gültiges SSML-Dokument umgewandelt werden muss.

`R3033`: Empfänger SOLLTEN NICHT XML-DTD oder Schemaauflösungen verwenden, um Remoteressourcen von außerhalb des kommunizierten XML-Fragments einzufügen.

`R3034`: Kanäle SOLLTEN das Feld `speak` NICHT an Bots senden.

### <a name="input-hint"></a>Eingabehinweis

Das Feld `inputHint` gibt an, ob der Generator der Aktivität eine Antwort erwartet. Dieses Feld wird vorwiegend in Kanälen mit modalen Benutzeroberflächen verwendet. Es wird in der Regel nicht in Kanälen mit fortlaufenden Chatfeeds verwendet. Der Wert des Felds `inputHint` ist vom Typ Zeichenfolge mit den definierten Werten `accepting`, `expecting` und `ignoring`. Standardwert: `accepting`.

`R3040`: Wenn ein Absender das Feld `inputHint` einfügt, SOLLTE er nur definierte Werte senden.

`R3041`: Beim Senden einer Aktivität an einen Kanal, in dem `inputHint` verwendet wird, SOLLTEN Bots das Feld einfügen, selbst wenn der Wert `accepting` lautet.

`R3042`: Empfänger SOLLTEN nicht definierte Werte als `accepting` interpretieren.

### <a name="attachments"></a>Attachments

Das Feld `attachments` enthält eine flache Liste von Objekten, die als Teil dieser Aktivität angezeigt werden sollen. Der Wert jedes Elements der Liste `attachments` ist ein komplexes Objekt vom Typ [Anlage](#attachment).

`R3050`: Absender SOLLTEN das Feld `attachments` auslassen, wenn es keine Elemente enthält.

`R3051`: Absender KÖNNEN mehrere Entitäten desselben Typs senden.

`R3052`: Empfänger KÖNNEN Anlagen mit unbekanntem Typ als Dokumente zum Herunterladen behandeln.

`R3053`: Empfänger SOLLTEN die Reihenfolge der Anlagen bei der Verarbeitung von Inhalten beibehalten, sofern nicht Renderingeinschränkungen, wie z.B. die Gruppierung von Dokumenten nach Bildern, Änderungen erzwingen.

### <a name="attachment-layout"></a>Anlagelayout

Das Feld `attachmentLayout` weist Renderern von Benutzeroberflächen an, wie der Inhalt im Feld [`attachments`](#attachments) dargestellt werden soll. Der Wert des Felds `attachmentLayout` ist vom Typ Zeichenfolge mit den definierten Werten `list` und `carousel`. Standardwert: `list`.

`R3060`: Wenn ein Absender das Feld `attachmentLayout` einfügt, SOLLTE er nur definierte Werte senden.

`R3061`: Empfänger SOLLTEN nicht definierte Werte als `list` interpretieren.

### <a name="summary"></a>Zusammenfassung

Das Feld `summary` enthält den Text, der [`attachments`](#attachments) in Kanälen ersetzt, die diese nicht unterstützen. Der Wert des Felds `summary` ist ein Zeichenfolgentyp.

`R3070`: Empfänger SOLLTEN erwägen, das Feld `summary` logisch an das Feld `text` anzupassen.

`R3071`: Kanäle SOLLTEN das Feld `summary` NICHT an Bots senden.

`R3072`: Kanäle, die alle Anlagen innerhalb einer Aktivität verarbeite können, SOLLTEN das Feld `summary` ignorieren.

### <a name="suggested-actions"></a>Vorgeschlagene Aktionen

Das Feld `suggestedActions` enthält eine Vielzahl interaktiver Aktionen, die dem Benutzer angezeigt werden können. Die Unterstützung für `suggestedActions` und deren Definition hängt stark vom Kanal ab. Der Wert des Felds `suggestedActions` ist ein komplexes Objekt vom Typ [vorgeschlagene Aktionen](#suggested-actions-2).

### <a name="value"></a>Wert

Das Feld `value` enthält eine programmgesteuerte Payload, die spezifisch für die gesendete Aktivität ist. Die Bedeutung und das Format werden in anderen Abschnitten dieses Dokuments definiert, in denen auch die Verwendung beschrieben wird.

`R3080`: Absender SOLLTEN KEINE `value`-Felder mit primitiven Typen (z.B. Zeichenfolge, ganze Zahl) einfügen. `value`-Felder SOLLTEN komplexe Typen sein oder ausgelassen werden.

### <a name="expiration"></a>Ablauf

Das Feld `expiration` enthält eine Zeit, zu der die Aktivität als „abgelaufen“ angesehen und nicht mehr dem Empfänger angezeigt werden soll. Der Wert des Felds `expiration` ist ein nach [ISO 8601](https://www.iso.org/iso-8601-date-and-time-format.html) [[2](#references)] codierter datetime-Wert innerhalb einer Zeichenfolge.

`R3090`: Absender SOLLTEN den Wert von `expiration`-Feldern immer als UTC-Zeit codieren, und sie SOLLTEN immer Z als explizites UTC-Kennzeichen im Wert verwenden.

### <a name="importance"></a>Wichtigkeit

Das Feld `importance` enthält einen aufgelisteten Satz von Werten, die dem Empfänger die relative Wichtigkeit der Aktivität signalisieren.  Es ist Aufgabe des Empfängers, diese Hinweise zur Wichtigkeit für die Benutzer zuzuordnen. Der Wert des Felds `importance` ist vom Typ Zeichenfolge mit den definierten Werten `low`, `normal` und `high`. Standardwert: `normal`.

`R3100`: Wenn ein Absender das Feld `importance` einfügt, SOLLTE er nur definierte Werte senden.

`R3101`: Empfänger SOLLTEN nicht definierte Werte als `normal` interpretieren.

### <a name="deliverymode"></a>Zustellungsmodus

Das Feld `deliveryMode` enthält einen aufgelisteten Satz von Werten, die dem Empfänger alternative Zustellungspfade für die Aktivität signalisieren.  Der Wert des Felds `DeliveryMode` ist vom Typ Zeichenfolge mit den definierten Werten `normal` und `notification`. Standardwert: `normal`.

`R3110`: Wenn ein Absender das Feld `deliveryMode` einfügt, SOLLTE er nur definierte Werte senden.

`R3111`: Empfänger SOLLTEN nicht definierte Werte als `normal` interpretieren.

## <a name="contact-relation-update-activity"></a>contactRelationUpdate-Aktivität

Aktivitäten zu Änderungen an der Kontaktbeziehung (contactRelationUpdate) signalisieren eine Änderung in der Beziehung zwischen dem Empfänger und einem Benutzer innerhalb des Kanals. contactRelationUpdate-Aktivitäten enthalten in der Regel keine benutzergenerierten Inhalte. Die durch eine contactRelationUpdate-Aktivität beschriebene Beziehungsänderung erfolgt zwischen dem Benutzer im Feld `from` (häufig, aber nicht immer der Benutzer, der die Änderung initiiert) und dem Benutzer oder Bot im Feld `recipient`.

Aktivitäten zu Änderungen an der Kontaktbeziehung werden durch den `type`-Wert `contactRelationUpdate` gekennzeichnet.

### <a name="action"></a>Aktion

Das Feld `action` beschreibt die Bedeutung der contactRelationUpdate-Aktivität. Der Wert des Felds `action` ist eine Zeichenfolge. Nur die Werte `add` und `remove` sind definiert. Sie kennzeichnen eine Beziehung zwischen den Benutzern/Bots in den Feldern `from` und `recipient`.

## <a name="conversation-update-activity"></a>conversationUpdate-Aktivität

Aktivitäten zu Änderungen an der Konversation (conversationUpdate) beschreiben eine Änderung bei den Teilnehmern, der Beschreibung oder dem Vorhandensein bzw. eine andere Änderung an einer Konversation. conversationUpdate-Aktivitäten enthalten in der Regel keine benutzergenerierten Inhalte. Die geänderte Konversation wird im Feld `conversation` beschrieben.

Aktivitäten zu Änderungen an der Konversation werden durch den `type`-Wert `conversationUpdate` gekennzeichnet.

`R4100`: Absender KÖNNEN 0 (null) oder mehr der Felder `membersAdded`, `membersRemoved`, `topicName` und `historyDisclosed` in eine conversationUpdate-Aktivität einfügen.

`R4101`: Jedes `channelAccount` (angegeben durch das Feld `id`) SOLLTE höchstens einmal in den Feldern `membersAdded` und `membersRemoved` enthalten sein. Eine ID SOLLTE in beiden Feldern NICHT enthalten sein. Eine ID SOLLTE NICHT in einem der Felder dupliziert werden.

`R4102`: Kanäle SOLLTEN die conversationUpdate-Aktivitäten NICHT verwenden, um Änderungen an den Feldern eines Kanals (z.B. `name`) anzugeben, wenn das Kanalkonto nicht hinzugefügt oder aus der Konversation entfernt wurde.

`R4103`: Kanäle SOLLTEN die Felder `topicName` oder `historyDisclosed` NICHT senden, wenn die Aktivität keine Änderung des Werts für eines der Felder signalisiert.

### <a name="members-added"></a>Hinzugefügte Mitglieder

Das Feld `membersAdded` enthält eine Liste der Kanalteilnehmer (Bots oder Benutzer), die der Konversation hinzugefügt wurden. Der Wert des Felds `membersAdded` ist ein Array vom Typ [`channelAccount`](#channel-account).

### <a name="members-removed"></a>Entfernte Mitglieder

Das Feld `membersRemoved` enthält eine Liste der Kanalteilnehmer (Bots oder Benutzer), die aus der Konversation entfernt wurden. Der Wert des Felds `membersRemoved` ist ein Array vom Typ [`channelAccount`](#channel-account).

### <a name="topic-name"></a>Themenname

Das Feld `topicName` enthält das Thema oder die Beschreibung des Texts für die Konversation. Der Wert des Felds `topicName` ist ein Zeichenfolgentyp.

### <a name="history-disclosed"></a>Offengelegter Verlauf

Das Feld `historyDisclosed` ist veraltet.

`R4110`: Absender SOLLTEN das Feld `historyDisclosed` NICHT einfügen.

## <a name="end-of-conversation-activity"></a>endOfConversation-Aktivität

Aktivitäten zum Ende einer Konversation (endOfConversation) signalisieren das Ende einer Konversation aus Sicht des Empfängers. Die Ursache kann sein, dass die Konversation vollständig beendet wurde oder dass der Empfänger auf eine Weise aus der Konversation entfernt wurde, die nicht von einer Beendigung unterschieden werden kann. Die beendete Konversation wird im Feld `conversation` beschrieben.

Aktivitäten zum Ende einer Konversation werden durch den `type`-Wert `endOfConversation` gekennzeichnet.

Die Felder `code` und `text` sind optional.

### <a name="code"></a>Code

Das Feld `code` enthält einen programmgesteuerten Wert, der beschreibt, warum und wie die Konversation beendet wurde. Der Wert des Felds `code` ist vom Typ Zeichenfolge, und seine Bedeutung wird durch den Kanal definiert, der die Aktivität sendet.

### <a name="text"></a>Text

Das Feld `text` enthält den optionalen Textinhalt, der an einen Benutzer übermittelt werden soll. Der Wert des Felds `text` ist vom Typ Zeichenfolge, und das Format ist Nur-Text.

## <a name="event-activity"></a>event-Aktivität

Ereignisaktivitäten (event) kommunizieren programmgesteuerte Informationen von einem Client oder Kanal an einen Bot. Die Bedeutung einer Ereignisaktivität wird durch das Feld `name` definiert, das innerhalb des Bereichs eines Kanals gilt. Ereignisaktivitäten sind sowohl für interaktive Informationen (z.B. Klicks auf Schaltflächen) als auch für nicht interaktive Informationen (z.B. eine Benachrichtigung für einen Client zur automatischen Aktualisierung eines eingebetteten Sprachmodells) vorgesehen.

Ereignisaktivitäten stellen das asynchrone Gegenstück zu [Aufrufaktivitäten](#invoke-activity) (invoke) dar. Im Gegensatz zu „invoke“ ist „event“ dafür vorgesehen, durch Clientanwendungserweiterungen erweitert zu werden.

Ereignisaktivitäten werden durch den `type`-Wert `event` und bestimmte Werte im Feld `name` gekennzeichnet.

`R5000`: Kanäle KÖNNEN anwendungsdefinierte Ereignismeldungen zwischen Clients und Bots zulassen, wenn die Clients eine Anwendungsanpassung erlauben.

### <a name="name"></a>NAME

Das Feld `name` steuert die Bedeutung des Ereignisses und das Schema des Felds `value`. Der Wert des Felds `name` ist ein Zeichenfolgentyp.

`R5001`: Ereignisaktivitäten MÜSSEN ein Feld `name` enthalten.

`R5002`: Empfänger MÜSSEN Ereignisaktivitäten mit `name`-Feldern, die sie nicht verstehen, ignorieren.

### <a name="value"></a>Wert

Das Feld `value` enthält Parameter spezifisch für dieses Ereignis, das durch den Ereignisnamen definiert wird. Der Wert des Felds `value` ist ein komplexer Typ.

`R5100`: Das Feld `value` KANN fehlen oder leer sein, wenn es durch den Namen des Ereignisses definiert wird.

`R5101`: Erweiterungen für die Ereignisaktivität SOLLTEN NICHT erfordern, dass Empfänger andere Informationen als die Felder `type` und `name` der Aktivität verwenden, um das Schema des Felds `value` zu verstehen.

### <a name="relates-to"></a>Bezieht sich auf

Das Feld `relatesTo` verweist auf eine andere Konversation und optional auf eine bestimmte Aktivität in dieser Aktivität. Der Wert des Felds `relatesTo` ist ein komplexes Objekt vom Typ Konversationsverweis.

`R5200`: `relatesTo` SOLLTE NICHT auf eine Aktivität in der Konversation, die durch das Feld `conversation` angegeben wird, verweisen.

## <a name="invoke-activity"></a>invoke-Aktivität

Aufrufaktivitäten (invoke) übermitteln programmgesteuerte Informationen von einem Client oder Kanal an einen Bot. Sie verfügen über eine entsprechende Rückgabepayload für die Verwendung innerhalb des Kanals. Die Bedeutung einer Aufrufaktivität wird durch das Feld `name` definiert, das innerhalb des Bereichs eines Kanals gilt. 

Aufrufaktivitäten stellen das synchrone Gegenstück zu [Ereignisaktivitäten](#event-activity) dar. Ereignisaktivitäten sind jedoch erweiterbar. Aufrufaktivitäten unterscheiden sich nur dadurch, dass sie Antwortpayloads an den Kanal zurückgeben können. Da der Kanal entscheiden muss, wo und wie die Antwortpayloads verarbeitet werden, ist „invoke“ nur in solchen Fällen hilfreich, in denen im Kanal eine explizite Unterstützung für die einzelnen invoke-Namen hinzugefügt wurde. Daher sind Aufrufaktivitäten nicht als allgemeiner Mechanismus zur Anwendungserweiterung ausgelegt.

Aufrufaktivitäten werden durch den `type`-Wert `invoke` und bestimmte Werte im Feld `name` gekennzeichnet.

Die Liste der definierten Aufrufaktivitäten finden Sie im [Anhang III](#appendix-iii---protocols-using-the-invoke-activity).

`R5301`: Kanäle SOLLTEN KEINE anwendungsdefinierten Aufrufnachrichten zwischen Clients und Bots zulassen.

### <a name="name"></a>NAME

Das Feld `name` steuert die Bedeutung des Aufrufs und das Schema des Felds `value`. Der Wert des Felds `name` ist ein Zeichenfolgentyp.

`R5401`: Aufrufaktivitäten MÜSSEN ein Feld `name` enthalten.

`R5402`: Empfänger MÜSSEN Ereignisaktivitäten mit `name`-Feldern, die sie nicht verstehen, ignorieren.

### <a name="value"></a>Wert

Das Feld `value` enthält Parameter spezifisch für dieses Ereignis, das durch den Ereignisnamen definiert wird. Der Wert des Felds `value` ist ein komplexer Typ.

`R5500`: Das Feld `value` KANN fehlen oder leer sein, wenn es durch den Namen des Ereignisses definiert wird.

`R5501`: Erweiterungen für die Ereignisaktivität SOLLTEN NICHT erfordern, dass Empfänger andere Informationen als die Felder `type` und `name` der Aktivität verwenden, um das Schema des Felds `value` zu verstehen.

### <a name="relates-to"></a>Bezieht sich auf

Das Feld `relatesTo` verweist auf eine andere Konversation und optional auf eine bestimmte Aktivität in dieser Aktivität. Der Wert des Felds `relatesTo` ist ein komplexes Objekt vom Typ [Konversationsverweis](#conversation-reference).

`R5600`: `relatesTo` SOLLTE NICHT auf eine Aktivität in der Konversation, die durch das Feld `conversation` angegeben wird, verweisen.

## <a name="installation-update-activity"></a>installationUpdate-Aktivität

Aktivitäten zur Aktualisierung der Installation (installationUpdate) stellen die Installation oder Deinstallation eines Bots in einer Organisationseinheit (z.B. einem Kundenmandanten oder einem „Team“) eines Kanals dar. Im Allgemeinen stellen sie nicht das Hinzufügen oder Entfernen eines Kanals dar.

Aktivitäten zu Änderungen an der Installation werden durch den `type`-Wert `installationUpdate` gekennzeichnet.

`R5700`: Kanäle KÖNNEN Installationsaktivitäten senden, wenn ein Bot einem Mandanten, Team oder einer anderen Organisationseinheit im Kanal hinzugefügt oder daraus entfernt wird.

`R5701`: Kanäle SOLLTEN NICHT Installationsaktivitäten senden, wenn der Bot in einen Kanal installiert oder aus einem Kanal entfernt wird.

### <a name="action"></a>Aktion

Das Feld `action` beschreibt die Bedeutung der installationUpdate-Aktivität. Der Wert des Felds `action` ist eine Zeichenfolge. Nur die Werte `add` und `remove` sind definiert.

## <a name="message-delete-activity"></a>messageDelete-Aktivität

Aktivitäten zum Löschen einer Nachricht (messageDelete) stellen das Löschen einer vorhandenen Nachrichtenaktivität innerhalb einer Konversation dar. Auf die gelöschte Aktivität wird durch die Felder `id` und `conversation` innerhalb der Aktivität verwiesen.

Aktivitäten zur Löschung einer Nachricht werden durch den `type`-Wert `messageDelete` gekennzeichnet.

`R5800`: Kanäle KÖNNEN Aktivitäten zum Löschen einer Nachricht für alle Löschvorgänge innerhalb einer Konversation, für einen Teil der Löschungen innerhalb einer Konversation (z.B. nur für Löschvorgänge von bestimmten Benutzern) oder für keine Aktivitäten in der Konversation senden.

`R5801`: Kanäle SOLLTEN NICHT Aktivitäten zum Löschen einer Nachricht für Konversationen oder Aktivitäten senden, die der Bot nicht überwacht.

`R5802`: Wenn ein Bot eine Löschung auslöst, SOLLTE der Kanal KEINE Aktivität zum Löschen einer Nachricht an den Bot zurücksenden.

`R5803`: Kanäle SOLLTEN KEINE Aktivitäten zum Löschen einer Nachricht senden, wenn der Typ der Aktivitäten nicht `message` ist.

## <a name="message-update-activity"></a>messageUpdate-Aktivität

Aktivitäten zum Aktualisieren einer Nachricht (messageUpdate) stellen das Aktualisieren einer vorhandenen Nachrichtenaktivität innerhalb einer Konversation dar. Auf die aktualisierte Aktivität wird über die Felder `id` und `conversation` innerhalb der Aktivität verwiesen. Die messageUpdate-Aktivität enthält alle Felder in der geänderten Nachrichtenaktivität.

Aktivitäten zur Aktualisierung einer Nachricht werden durch den `type`-Wert `messageUpdate` gekennzeichnet.

`R5900`: Kanäle KÖNNEN Aktivitäten zum Aktualisieren einer Nachricht für alle Aktualisierungen innerhalb einer Konversation, für einen Teil der Aktualisierungen innerhalb einer Konversation (z.B. nur für Aktualisierungen von bestimmten Benutzern) oder für keine Aktivitäten in der Konversation senden.

`R5901`: Wenn ein Bot eine Aktualisierung auslöst, SOLLTE der Kanal KEINE Aktivität zum Aktualisieren einer Nachricht an den Bot zurücksenden.

`R5902`: Kanäle SOLLTEN KEINE Aktivitäten zum Aktualisieren einer Nachricht senden, wenn der Typ der Aktivitäten nicht `message` ist.

## <a name="message-reaction-activity"></a>messageReaction-Aktivität

Aktivitäten zur Antwort auf eine Nachricht (messageReaction) stellen eine soziale Interaktion mit einer vorhandenen Nachrichtenaktivität innerhalb einer Konversation dar. Auf die ursprüngliche Aktivität wird durch die Felder `id` und `conversation` innerhalb der Aktivität verwiesen. Das Feld `from` stellt die Quelle der Antwort dar (d.h. den Benutzer, der auf die Nachricht antwortet).

Aktivitäten zur Antwort auf eine Nachricht werden durch den `type`-Wert `messageReaction` gekennzeichnet.

### <a name="reactions-added"></a>Hinzugefügte Reaktionen

Das Feld `reactionsAdded` enthält eine Liste der Antworten, die dieser Aktivität hinzugefügt wurden. Der Wert des Felds `reactionsAdded` ist ein Array vom Typ [`messageReaction`](#message-reaction).

### <a name="reactions-removed"></a>Entfernte Antworten

Das Feld `reactionsRemoved` enthält eine Liste der Antworten, die aus dieser Aktivität entfernt wurden. Der Wert des Felds `reactionsRemoved` ist ein Array vom Typ [`messageReaction`](#message-reaction).

## <a name="typing-activity"></a>typing-Aktivität

Eingabeaktivitäten (typing) stellen eine fortlaufende Eingabe durch einen Benutzer oder Bot dar. Diese Aktivität wird häufig gesendet, wenn ein Benutzer etwas über die Tastatur eingibt, aber auch Bots können damit angeben, dass sie „nachdenken“. Darüber hinaus könnte damit z.B. angegeben werden, dass Audiodaten von den Benutzern erfasst werden.

Eingabeaktivitäten sollten auf Benutzeroberflächen drei Sekunden lang beibehalten werden.

Eingabeaktivitäten werden durch den `type`-Wert `typing` gekennzeichnet.

`R6000`: Wenn möglich, SOLLTEN Clients Eingabeindikatoren nach dem Empfang einer Eingabeaktivität für drei Sekunden anzeigen.

`R6001`: Sofern durch den Kanal keine andere Vorgabe erfolgt, SOLLTEN Absender Eingabeaktivitäten NICHT öfter als einmal alle drei Sekunden senden. (Absender KÖNNEN Eingabeaktivitäten alle zwei Sekunden senden, um Pausen bei der Anzeige zu verhindern.)

`R6002`: Wenn ein Kanal einer Eingabeaktivität eine [`id`](#id) zuweist, KANN er Bots und Clients erlauben, die Eingabeaktivität vor ihrem Ablauf zu löschen.

`R6003`: Wenn möglich, SOLLTEN Kanäle Eingabeaktivitäten an Bots senden.

## <a name="complex-types"></a>Komplexe Typen

In diesem Abschnitt werden die komplexen Typen, die innerhalb des oben beschriebenen Aktivitätsschemas verwendet werden, beschrieben.

### <a name="attachment"></a>Anhang

Anlagen sind Inhalte, die in eine [Nachrichtenaktivität](#message-activity) eingefügt werden: Karten, binäre Dokumente und andere interaktive Inhalte. Sie sind dafür vorgesehen, zusammen mit Textinhalten angezeigt zu werden. Inhalte können über URL-Daten-URIs im Feld `contentUrl` oder intern im Feld `content` gesendet werden.

`R7100`: Absender SOLLTEN NICHT die Felder `content` und `contentUrl` zusammen in eine Anlage einfügen.

#### <a name="content-type"></a>Content-Typ

Das Feld `contentType` beschreibt den [MIME-Medientyp](https://www.iana.org/assignments/media-types/media-types.xhtml) [[8](#references)] des Inhalts der Anlage. Der Wert des Felds `contentType` ist ein Zeichenfolgentyp.

#### <a name="content"></a>Inhalt

Wenn vorhanden, enthält das Feld `content` eine strukturierte JSON-Objektanlage. Der Wert des Felds `content` ist ein komplexer Typ, der durch das Feld `contentType` definiert wird.

`R7110`: Absender SOLLTEN NICHT primitive JSON-Typen in das Feld `content` einer Anlage einfügen.

#### <a name="content-url"></a>Inhalts-URL

Wenn vorhanden, enthält das Feld `contentUrl` eine URL zum Inhalt der Anlage. Daten-URIs gemäß [RFC 2397](https://tools.ietf.org/html/rfc2397) [[9](#references)] werden in der Regel von Kanälen unterstützt. Der Wert des Felds `contentUrl` ist ein Zeichenfolgentyp.

`R7120`: Empfänger SOLLTEN HTTPS-URLs akzeptieren.

`R7121`: Empfänger KÖNNEN HTTP-URLs akzeptieren.

`R7122`: Kanäle SOLLTEN Daten-URIs akzeptieren.

`R7123`: Kanäle SOLLTEN KEINE Daten-URIs an Clients oder Bots senden.

#### <a name="name"></a>NAME

Das Feld `name` enthält einen optionalen Namen oder Dateinamen für die Anlage. Der Wert des Felds `name` ist ein Zeichenfolgentyp.

#### <a name="thumbnail-url"></a>URL der Miniaturansicht

Einige Clients können benutzerdefinierte Miniaturansichten für nicht interaktive Anlagen oder Platzhalter für interaktive Anlagen anzeigen. Das Feld `thumbnailUrl` gibt die Quelle für diese Miniaturansicht an. Daten-URIs gemäß [RFC 2397](https://tools.ietf.org/html/rfc2397) [[9](#references)] sind in der Regel ebenfalls zulässig.

`R7140`: Empfänger SOLLTEN HTTPS-URLs akzeptieren.

`R7141`: Empfänger KÖNNEN HTTP-URLs akzeptieren.

`R7142`: Kanäle SOLLTEN Daten-URIs akzeptieren.

`R7143`: Kanäle SOLLTEN `thumbnailUrl`-Felder NICHT an Bots senden.

### <a name="card-action"></a>Kartenaktion

Eine Kartenaktion stellt eine Schaltfläche dar, auf die geklickt werden kann oder die interaktiv ist und die in Karten oder als [empfohlene Aktionen](#suggested-actions) verwendet werden kann. Sie wird verwendet, um Eingaben von Benutzern anzufordern. Trotz des Namens sind Kartenaktionen nicht ausschließlich auf Karten beschränkt.

Kartenaktionen sind nur sinnvoll, wenn sie an Kanäle gesendet werden.

Kanäle entscheiden, wie die einzelnen Aktion auf ihrer Benutzeroberfläche dargestellt werden. In den meisten Fällen kann auf die Karten geklickt werden. In anderen Fällen können sie durch Spracheingabe ausgewählt werden. Wenn der Kanal keine Möglichkeit zur interaktiven Aktivierung verfügbar macht (z.B. bei der Interaktion per SMS), unterstützt der Kanal möglicherweise keinerlei Aktivierungen. Die Entscheidung zum Rendern von Aktionen wird durch normative Anforderungen an anderer Stelle in diesem Dokument gesteuert (z.B. über das Kartenformat oder in der Definition [empfohlener Aktionen](#suggested-actions)).

#### <a name="type"></a>Typ

Das Feld `type` beschreibt die Bedeutung der Schaltfläche und des Verhaltens beim Aktivieren der Schaltfläche. Die Werte im Feld `type` sind laut Konvention kurze Zeichenfolgen (z.B. „`openUrl`“). In den nachfolgenden Abschnitten finden Sie die Anforderungen für die einzelnen Aktivitätstypen.

* Eine Aktion vom Typ `messageBack` stellt eine Textantwort dar, die über das Chatsystem gesendet wird.
* Eine Aktion vom Typ `imBack` stellt eine Textantwort dar, die dem Chatfeed hinzugefügt wird.
* Eine Aktion vom Typ `postBack` stellt eine Textantwort dar, die dem Chatfeed nicht hinzugefügt wird.
* Eine Aktion vom Typ `openUrl` stellt einen Hyperlink dar, der vom Client verarbeitet werden soll.
* Eine Aktion vom Typ `downloadFile` stellt einen Hyperlink dar, der heruntergeladen werden soll.
* Eine Aktion vom Typ `showImage` stellt ein Bild dar, das angezeigt werden kann.
* Eine Aktion vom Typ `signin` stellt einen Hyperlink dar, der vom Anmeldesystem des Clients verarbeitet werden soll.
* Eine Aktion vom Typ `playAudio` stellt Audiomedien dar, die wiedergegeben werden können.
* Eine Aktion vom Typ `playVideo` stellt Videomedien dar, die wiedergegeben werden können.
* Eine Aktion vom Typ `call` stellt eine Telefonnummer dar, die angerufen werden kann.
* Eine Aktion vom Typ `payment` stellt eine Zahlungsanforderung dar.

#### <a name="title"></a>Titel

Das Feld `title` enthält Text, der auf der Schaltfläche angezeigt werden soll. Der Wert des Felds `title` ist vom Typ Zeichenfolge und enthält kein Markup.

Dieses Feld gilt für Aktionen aller Typen.

`R7210`: Kanäle SOLLTEN Markup innerhalb des Felds `title` (z.B. Markdown) NICHT verarbeiten.

#### <a name="image"></a>Image

Das Feld `image` enthält eine URL zu einem Bild, das auf der Schaltfläche angezeigt werden soll. Daten-URIs gemäß [RFC 2397](https://tools.ietf.org/html/rfc2397) [[9](#references)] werden in der Regel von Kanälen unterstützt. Der Wert des Felds `image` ist ein Zeichenfolgentyp.

Dieses Feld gilt für Aktionen aller Typen.

`R7220`: Kanäle SOLLTEN HTTPS-URLs akzeptieren.

`R7221`: Kanäle KÖNNEN HTTP-URLs akzeptieren.

`R7222`: Kanäle SOLLTEN Daten-URIs akzeptieren.

#### <a name="text"></a>Text

Das Feld `text` enthält Textinhalt, der an einen Bot gesendet und in den Chatfeed eingefügt werden soll, wenn auf die Schaltfläche geklickt wird. Den Inhalt des Felds `text` kann je nach Schaltflächentyp angezeigt werden. Das Feld `text` kann Markup enthalten, das durch das Feld [`textFormat`](#text-format) im Stamm der Aktivität gesteuert wird. Der Wert des Felds `text` ist ein Zeichenfolgentyp.

Dieses Feld wird nur bei Aktionen bestimmter Typen verwendet. Details zu den einzelnen Aktionstypen finden Sie weiter unten in diesem Dokument.

`R7230`: Das Feld `text` KANN eine leere Zeichenfolge enthalten, um anzugeben, dass Text ohne Inhalt gesendet wird.

`R7231`: Kanäle SOLLTEN den Inhalt des Felds `text` in Übereinstimmung mit dem Feld [`textFormat`](#text-format) im Stamm der Aktivität verarbeiten.

#### <a name="display-text"></a>Anzeigetext

Das Feld `displayText` enthält Textinhalt, der in den Chatfeed eingefügt werden soll, wenn auf die Schaltfläche geklickt wird. Den Inhalt des Felds `displayText` SOLLTE immer angezeigt werden, wenn dies innerhalb des Kanals technisch möglich ist. Das Feld `displayText` kann Markup enthalten, das durch das Feld [`textFormat`](#text-format) im Stamm der Aktivität gesteuert wird. Der Wert des Felds `displayText` ist ein Zeichenfolgentyp.

Dieses Feld wird nur bei Aktionen bestimmter Typen verwendet. Details zu den einzelnen Aktionstypen finden Sie weiter unten in diesem Dokument.

`R7240`: Das Feld `displayText` KANN eine leere Zeichenfolge enthalten, um anzugeben, dass Text ohne Inhalt gesendet wird.

`R7241`: Kanäle SOLLTEN den Inhalt des Felds `displayText` in Übereinstimmung mit dem Feld [`textFormat`](#text-format) im Stamm der Aktivität verarbeiten.

#### <a name="value"></a>Wert

Das Feld `value` enthält programmgesteuerten Inhalt, der an einen Bot gesendet werden soll, wenn auf die Schaltfläche geklickt wird. Die Inhalte des Felds `value` können von einem beliebigen primitiven oder komplexen Typ sein. Bei einigen Aktivitätstypen gelten jedoch Einschränkungen für dieses Feld.

Dieses Feld wird nur bei Aktionen bestimmter Typen verwendet. Details zu den einzelnen Aktionstypen finden Sie weiter unten in diesem Dokument.

#### <a name="message-back"></a>Rücknachricht

Eine `messageBack`-Aktion stellt eine Textantwort dar, die über das Chatsystem gesendet wird. Für die Rücknachricht werden die folgenden Felder verwendet:
* `type` („`messageBack`“)
* `title`
* `image`
* `text`
* `displayText`
* `value` (beliebiger Typ)

`R7350`: Absender SOLLTEN KEINE `value`-Felder mit primitiven Typen (z.B. Zeichenfolge, ganze Zahl) einfügen. `value`-Felder SOLLTEN komplexe Typen sein oder ausgelassen werden.

`R7351`: Kanäle KÖNNEN `value`-Felder ablehnen oder löschen, die keinen komplexen Typ aufweisen.

`R7352`: Bei einer Aktivierung MÜSSEN Kanäle eine Aktivität vom Typ `message` an alle relevanten Empfänger senden.

`R7353`: Wenn der Kanal das Speichern und Übertragen von Text unterstützt, MUSS der Inhalt des Felds `text` der Aktion beibehalten und im Feld `text` der generierten Nachrichtenaktivität übertragen werden.

`R7352`: Wenn der Kanal das Speichern und Übertragen ergänzender programmgesteuerter Werte unterstützt, MUSS der Inhalt des Felds `value` beibehalten und im Feld `value` der generierten Nachrichtenaktivität übertragen werden.

`R7353`: Wenn der Channel das Beibehalten eines anderen Werts als des an die Bots gesendeten Werts im Chatfeed unterstützt, MUSS das Feld `displayText` in den Chatverlauf eingefügt werden.

`R7354`: Wenn der Kanal `R7353` nicht unterstützt, aber das Aufzeichnen von Text im Chatfeed unterstützt, MUSS er das Feld `text` in den Chatverlauf einfügen.

#### <a name="im-back"></a>IM-Antwort

Eine `imBack`-Aktion stellt eine Textantwort dar, die dem Chatfeed hinzugefügt wird. Für die Rücknachricht werden die folgenden Felder verwendet:
* `type` („`imBack`“)
* `title`
* `image`
* `value` (vom Typ Zeichenfolge)

`R7360`: Bei einer Aktivierung MÜSSEN Kanäle eine Aktivität vom Typ `message` an alle relevanten Empfänger senden.

`R7361`: Wenn der Kanal das Speichern und Übertragen von Text unterstützt, MUSS der Inhalt des Felds `title` beibehalten und im Feld `text` der generierten Nachrichtenaktivität übertragen werden.

`R7362`: Wenn das Feld `title` bei einer Aktion fehlt und das Feld `value` vom Typ Zeichenfolge ist, KANN der Kanal den Inhalt des Felds `value` im Feld `text` der generierten Nachrichtenaktivität übertragen.

`R7363`: Wenn der Kanal das Aufzeichnen von Text im Chatfeed unterstützt, MUSS er den Inhalt des Felds `title` in den Chatverlauf einfügen.

#### <a name="post-back"></a>Postback

Eine `postBack`-Aktion stellt eine Textantwort dar, die dem Chatfeed nicht hinzugefügt wird. Für das Postback werden die folgenden Felder verwendet:
* `type` („`postBack`“)
* `title`
* `image`
* `value` (vom Typ Zeichenfolge)

`R7370`: Bei einer Aktivierung MÜSSEN Kanäle eine Aktivität vom Typ `message` an alle relevanten Empfänger senden.

`R7371`: Kanäle SOLLTEN NICHT Text in den Chatverlauf einfügen, wenn eine Postbackaktion aktiviert wird.

`R7372`: Kanäle MÜSSEN `value`-Felder ablehnen oder löschen, die keinen Zeichenfolgentyp aufweisen.

`R7373`: Wenn der Kanal das Speichern und Übertragen von Text unterstützt, MUSS der Inhalt des Felds `value` beibehalten und im Feld `text` der generierten Nachrichtenaktivität übertragen werden.

`R7374`: Wenn der Kanal das Übertragen an den Bot ohne Einfügen in den Chatfeed nicht unterstützen kann, SOLLTE er das Feld `title` als Anzeigetext verwenden.

#### <a name="open-url-actions"></a>openURL-Aktionen

Eine `openUrl`-Aktion stellt einen Hyperlink dar, der vom Client verarbeitet werden soll. Für openURL-Aktionen werden die folgenden Felder verwendet:
* `type` („`openUrl`“)
* `title`
* `image`
* `value` (vom Typ Zeichenfolge)

`R7380`: Absender MÜSSEN eine URL in das Feld `value` einer `openUrl`-Aktion einfügen.

`R7381`: Empfänger KÖNNEN `openUrl`-Aktionen ablehnen, deren Feld `value` fehlt oder keine Zeichenfolge ist.

`R7382`: Empfänger SOLLTEN `openUrl`-Aktionen, deren Feld `value` einen Daten-URI gemäß [RFC 2397](https://tools.ietf.org/html/rfc2397) [[9](#references)] enthält, ablehnen oder löschen.

`R7383`: Empfänger SOLLTEN NICHT `openUrl`-Aktionen ablehnen, deren `value`-URI auf andere Weise unerwartete URI-Schemas oder -Werte aufweist.

`R7384`: Clients mit Kenntnissen zu bestimmten URI-Schemas (z.B. HTTP) KÖNNEN `openUrl`-Aktionen mit einem eingebetteten Renderer (z.B. einem Browsersteuerelement) verarbeiten.

`R7385`: Sofern verfügbar, SOLLTEN Clients die Verarbeitung von `openUrl`-Aktionen, die nicht nach `R7354` verarbeitet werden, an den URI-Handler auf Betriebssystem- oder Shellebene delegieren.

#### <a name="download-file-actions"></a>downloadFile-Aktionen

Eine `downloadFile`-Aktion stellt einen Hyperlink dar, der heruntergeladen werden soll. Für downloadFile-Aktionen werden die folgenden Felder verwendet:
* `type` („`downloadFile`“)
* `title`
* `image`
* `value` (vom Typ Zeichenfolge)

`R7390`: Absender MÜSSEN eine URL in das Feld `value` einer `downloadFile`-Aktion einfügen.

`R7391`: Empfänger KÖNNEN `downloadFile`-Aktionen ablehnen, deren Feld `value` fehlt oder keine Zeichenfolge ist.

`R7392`: Empfänger SOLLTEN `downloadFile`-Aktionen, deren Feld `value` einen Daten-URI gemäß [RFC 2397](https://tools.ietf.org/html/rfc2397) [[9](#references)] enthält, ablehnen oder löschen.

#### <a name="show-image-file-actions"></a>showImage-Aktionen

Eine `showImage`-Aktion stellt ein Bild dar, das angezeigt werden kann. Für showImage-Aktionen werden die folgenden Felder verwendet:
* `type` („`showImage`“)
* `title`
* `image`
* `value` (vom Typ Zeichenfolge)

`R7400`: Absender MÜSSEN eine URL in das Feld `value` einer `showImage`-Aktion einfügen.

`R7401`: Empfänger KÖNNEN `showImage`-Aktionen ablehnen, deren Feld `value` fehlt oder keine Zeichenfolge ist.

`R7402`: Empfänger KÖNNEN `showImage`-Aktionen ablehnen, deren gesamtes Feld `value` ein Daten-URI gemäß [RFC 2397](https://tools.ietf.org/html/rfc2397) [[9](#references)] ist.

#### <a name="signin"></a>Signin

Eine `signin`-Aktion stellt einen Hyperlink dar, der vom Anmeldesystem des Clients verarbeitet werden soll. Für signin-Aktionen werden die folgenden Felder verwendet:
* `type` („`signin`“)
* `title`
* `image`
* `value` (vom Typ Zeichenfolge)

`R7410`: Absender MÜSSEN eine URL in das Feld `value` einer `signin`-Aktion einfügen.

`R7411`: Empfänger KÖNNEN `signin`-Aktionen ablehnen, deren Feld `value` fehlt oder keine Zeichenfolge ist.

`R7412`: Empfänger MÜSSEN `signin`-Aktionen, deren Feld `value` einen Daten-URI gemäß [RFC 2397](https://tools.ietf.org/html/rfc2397) [[9](#references)] enthält, ablehnen oder löschen.

#### <a name="play-audio"></a>playAudio

Eine `playAudio`-Aktion stellt Audiomedien dar, die wiedergegeben werden können. Für playAudio-Aktionen werden die folgenden Felder verwendet:
* `type` („`playAudio`“)
* `title`
* `image`
* `value` (vom Typ Zeichenfolge)

`R7420`: Bei Aktivierung KÖNNEN Kanäle Medienereignisse senden.

`R7421`: Kanäle MÜSSEN `value`-Felder ablehnen oder löschen, die keinen Zeichenfolgentyp aufweisen.

`R7422`: Absender SOLLTEN NICHT Daten-URIs gemäß [RFC 2397](https://tools.ietf.org/html/rfc2397) [[9](#references)] senden, ohne vorher zu wissen, ob der Kanal diese unterstützt.

#### <a name="play-video"></a>playVideo

Eine `playAudio`-Aktion stellt Videomedien dar, die wiedergegeben werden können. Für playVideo-Aktionen werden die folgenden Felder verwendet:
* `type` („`playVideo`“)
* `title`
* `image`
* `value` (vom Typ Zeichenfolge)

`R7430`: Bei Aktivierung KÖNNEN Kanäle Medienereignisse senden.

`R7431`: Kanäle MÜSSEN `value`-Felder ablehnen oder löschen, die keinen Zeichenfolgentyp aufweisen.

`R7432`: Absender SOLLTEN NICHT Daten-URIs gemäß [RFC 2397](https://tools.ietf.org/html/rfc2397) [[9](#references)] senden, ohne vorher zu wissen, ob der Kanal diese unterstützt.

#### <a name="call"></a>Aufruf

Eine `call`-Aktion stellt eine Telefonnummer dar, die angerufen werden kann. Für call-Aktionen werden die folgenden Felder verwendet:
* `type` („`call`“)
* `title`
* `image`
* `value` (vom Typ Zeichenfolge)

`R7440`: Absender MÜSSEN eine URL vom Schema `tel` in das Feld `value` einer `signin`-Aktion einfügen.

`R7441`: Empfänger MÜSSEN `signin`-Aktionen ablehnen, deren Feld `value` fehlt oder kein Zeichenfolgen-URI mit dem Schema `tel` ist.

#### <a name="payment"></a>payment

Eine `payment`-Aktion stellt eine Zahlungsanforderung dar. Für payment-Aktionen werden die folgenden Felder verwendet:
* `type` („`payment`“)
* `title`
* `image`
* `value` (vom komplexen Typ [Zahlungsanforderung](#payment-request))

`R7450`: Absender MÜSSEN ein komplexes Objekt vom Typ [Zahlungsanforderung](#payment-request) in das Feld `value` einer `payment`-Aktion einfügen.

`R7451`: Kanäle MÜSSEN `payment`-Aktionen ablehnen, deren Feld `value` fehlt oder kein komplexes Objekt vom Typ [Zahlungsanforderung](#payment-request) ist.

### <a name="channel-account"></a>Kanalkonto

Kanalkonten stellen Identitäten innerhalb eines Kanals dar. Das Kanalkonto umfasst eine ID, die zum Identifizieren und Kontaktieren des Kontos in diesem Kanal verwendet werden kann. Manchmal befinden sich diese IDs in einem einzelnen Namespace (z.B. Skype-IDs). In einigen Fällen werden sie über mehrere Server (z.B. E-Mail-Adressen) in einem Verbund zusammengefasst. Neben der ID enthalten Kanalkonten die Anzeigenamen und die AAD-Objekt-IDs (Azure Active Directory).

#### <a name="channel-account-id"></a>Kanalkonto-ID

Das Feld `id` ist der Bezeichner und die Adresse innerhalb des Kanals. Der Wert des Felds `id` ist eine Zeichenfolge. Ein Beispiel für eine `id` innerhalb eines Kanals, der E-Mail-Adressen verwendet, ist „name@example.com“.

`R7510`: Kanäle SOLLTEN die gleichen Werte und Konventionen für Konto-IDs verwenden, unabhängig von ihrer Position innerhalb des Schemas (`from.id`, `recipient.id`, `membersAdded` usw.). Dadurch können Bots und Clients Ordnungszahlvergleiche durchführen, um beispielsweise zu erfahren, wenn sie im Feld `membersAdded` einer `conversationUpdate`-Aktivität beschrieben werden.

#### <a name="channel-account-name"></a>Kanalkontoname

Das Feld `name` ist ein optionaler Anzeigename innerhalb des Kanals. Der Wert des Felds `name` ist eine Zeichenfolge. Ein Beispiel für einen `name` innerhalb eines Kanals ist „John Doe“.

#### <a name="channel-account-aad-object-id"></a>AAD-Objekt-ID eines Kanalkontos

Das Feld `aadObjectId` ist eine optionale ID, die der Objekt-ID des Kontos in Azure Active Directory (AAD) entspricht. Der Wert des Felds `aadObjectId` ist eine Zeichenfolge.

### <a name="conversation-account"></a>Konversationskonto

Konversationskonten stellen die Identität der Konversationen innerhalb eines Kanals dar. In Kanälen, die nur eine einzelne Konversation zwischen zwei Konten unterstützen (z.B. SMS), ist das Konversationskonto persistent und verfügt nicht über einen vordefinierten Start oder ein vordefiniertes Ende. In Kanälen, die mehrere parallele Konversationen unterstützen (z.B. E-Mail), hat jede Konversation wahrscheinlich eine eigene ID.

#### <a name="conversation-account-id"></a>Konversationskonto-ID

Das Feld `id` ist der Bezeichner innerhalb des Kanals. Das Format dieser ID wird vom Kanal definiert und im Protokoll als nicht transparente Zeichenfolge verwendet.

Kanäle SOLLTEN `id`-Werte auswählen, die für alle Teilnehmer innerhalb einer Konversation stabil sind. (Ein negatives Beispiel für das Feld `id` bei einer 1:1-Konversation stellt z.B. die Verwendung der ID anderer Teilnehmer als `id`-Wert dar. Dies würde aus Sicht der einzelnen Teilnehmer zu einer jeweils anderen `id` führen. Eine bessere Wahl ist die Sortierung der IDs beider Teilnehmer mit anschließender Verkettung, sodass der Wert für beide Parteien gleich ist.)

#### <a name="conversation-account-name"></a>Konversationskontoname

Das Feld `name` ist ein optionaler Anzeigename für die Konversation innerhalb des Kanals. Der Wert des Felds `name` ist eine Zeichenfolge.

#### <a name="conversation-account-aad-object-id"></a>AAD-Objekt-ID eines Konversationskontos

Das Feld `aadObjectId` ist eine optionale ID, die der Objekt-ID der Konversation in Azure Active Directory (AAD) entspricht. Der Wert des Felds `aadObjectId` ist eine Zeichenfolge.

#### <a name="conversation-account-is-group"></a>Konversationskonten für Gruppen

Das Feld `isGroup` gibt an, ob die Konversation zu dem Zeitpunkt, an dem die Aktivität generiert wurde, mehr als zwei Teilnehmer enthielt. Der Wert des Felds `isGroup` ist boolesch. Wenn er nicht angegeben wird, lautet der Standardwert `false`. Dieses Feld steuert in der Regel das Verhalten für Teilnehmer bei der Erwähnung im Kanal. Es SOLLTE nur dann auf `true` festgelegt werden, wenn mehr als zwei Teilnehmer die Möglichkeit haben, Aktivitäten in der Konversation zu senden und zu empfangen.

### <a name="entity"></a>Entität

Entitäten enthalten Metadaten zu einer Aktivität oder Konversation. Die Bedeutung und Form jeder Entität wird durch das Feld `type` definiert. Zusätzliche typspezifische Felder werden als Peers für das Feld `type` verwendet.

`R7600`: Empfänger MÜSSEN Entitäten ignorieren, deren Typen sie nicht verstehen.

`R7601`: Empfänger SOLLTEN Entitäten ignorieren, deren Typ sie zwar verstehen, die sie aber aufgrund von z.B. Syntaxfehlern nicht verarbeiten können.

Parteien, die einen vorhandenen Entitätstyp in das Format einer Aktivitätsentität übertragen möchten, sollten zum Lösen von Konflikten mit dem Namen des Felds `type` sowie Inkompatibilitäten mit den Serialisierungsanforderungen unter `R2001` als Teil der Bindung zwischen IRI und Entitätsschema auflösen.

#### <a name="type"></a>Typ

Das Feld `type` ist erforderlich und definiert die Bedeutung und die Form der Entität. `type` ist für [IRIs](https://tools.ietf.org/html/rfc3987) [[3](#references)] vorgesehen, allerdings enthält [Anhang I](#appendix-ii---non-iri-entity-types) auch eine kleine Anzahl von Nicht-IRI-Entitätstypen.

`R7610`: Absender SOLLTEN Nicht-IRI-Typennamen nur für die in [Anhang II](#appendix-ii---non-iri-entity-types) beschriebenen Typen verwenden.

`R7611`: Absender KÖNNEN IRI-Typen für die in [Anhang II](#appendix-ii---non-iri-entity-types) beschriebenen Typen senden, wenn sie wissen, dass der Empfänger sie versteht.

`R7612`: Absender SOLLTEN IRIs für Entitätstypen verwenden oder festlegen, die nicht in [Anhang II](#appendix-ii---non-iri-entity-types) definiert sind.

### <a name="suggested-actions"></a>Vorgeschlagene Aktionen

Vorgeschlagene Aktionen können im Nachrichteninhalt gesendet werden, um interaktive Aktionselemente in einer Clientbenutzeroberfläche zu erstellen.

`R7700`: Clients, die keine Benutzeroberfläche unterstützen, die vorgeschlagene Aktionen rendern kann, SOLLTEN das Feld `suggestedActions` ignorieren.

`R7701`: Absender SOLLTEN das Feld `suggestedActions` auslassen, wenn das Feld `actions` leer ist.

#### <a name="to"></a>To

Das Feld `to` enthält die IDs der Kanalkonten, denen die vorgeschlagenen Aktionen angezeigt werden sollen. Dieses Feld kann verwendet werden, um Aktionen auf eine Teilmenge der Teilnehmer in der Konversation zu filtern.

`R7710`: Wenn das Feld `to` fehlt oder ist leer, SOLLTE der Client die vorgeschlagenen Aktionen für alle Teilnehmer der Konversation anzeigen.

`R7711`: Wenn das Feld `to` ungültige IDs enthält, SOLLTEN diese Werte ignoriert werden.

#### <a name="actions"></a>Actions

Das Feld `actions` enthält eine flache Liste von Aktionen, die angezeigt werden sollen. Der Wert jedes Elements der Liste `actions` ist ein komplexes Objekt vom Typ `cardAction`.

### <a name="message-reaction"></a>Nachrichtenantwort

Nachrichtenantworten stellen eine soziale Interaktion dar („Gefällt mir“, „+ 1“ usw.). Nachrichtenantworten enthalten derzeit nur ein einzelnes Feld: `type`.

#### <a name="type"></a>Typ

Das Feld `type` beschreibt den Typ der sozialen Interaktion. Der Wert des Felds `type` ist vom Typ Zeichenfolge, und seine Bedeutung wird durch den Kanal definiert, in dem die Interaktion erfolgt. Zwei häufige Werte sind `like` und `+1`, die nur gemäß der Konvention einheitlich sind – und nicht aufgrund einer Regel.

## <a name="references"></a>Referenzen

1. [RFC 2119](https://tools.ietf.org/html/rfc2119)
2. [ISO 8601](https://www.iso.org/iso-8601-date-and-time-format.html)
3. [RFC 3987](https://tools.ietf.org/html/rfc3987)
4. [Markdown](https://daringfireball.net/projects/markdown/)
5. [ISO 639](https://www.iso.org/iso-639-language-codes.html)
6. [SSML](https://www.w3.org/TR/speech-synthesis/)
7. [XML](https://www.w3.org/TR/xml/)
8. [MIME-Medientypen](https://www.iana.org/assignments/media-types/media-types.xhtml)
9. [RFC 2397](https://tools.ietf.org/html/rfc2397)
10. [ISO 3166-1](https://www.iso.org/iso-3166-country-codes.html)
11. [Adaptive Cards](https://adaptivecards.io)

# <a name="appendix-i---changes"></a>Anhang I – Änderungen

## <a name="2018-02-07"></a>2018-02-07

* Erster Entwurf

# <a name="appendix-ii---non-iri-entity-types"></a>Anhang II – Nicht-IRI-Entitätstypen

[Entitäten](#entity) in Aktivitäten dienen der Übermittlung zusätzlicher Metadaten zu der Aktivität, z.B. des Standorts eines Benutzers oder der Version der verwendeten Messaging-App. Als Aktivitätstypen sind IRIs vorgesehen, eine kleine Zahl von Nicht-IRI-Namen wird aber häufiger verwendet. Dieser Anhang enthält eine vollständige Liste der unterstützten Nicht-IRI-Entitätstypen.

| Typ           | URI-äquivalent                          | BESCHREIBUNG               |
| -------------- | --------------------------------------- | ------------------------- |
| GeoCoordinates | https://schema.org/GeoCoordinates       | Schema.org-Geokoordinaten |
| Mention        | https://botframework.com/schema/mention | @-mention                 |
| Ort          | https://schema.org/Place                | Schema.org-Ort          |
| Thing          | https://schema.org/Thing                | Schema.org-Objekt          |
| clientInfo     | N/V                                     | Skype-Clientinformationen         |

### <a name="clientinfo"></a>clientInfo

Die clientInfo-Entität enthält erweiterte Informationen über die Clientsoftware, die zum Senden einer Benutzernachricht verwendet wurde. Sie enthält drei Eigenschaften, die alle optional sind.

`R9201`: Bots SOLLTEN die `clientInfo`-Entität NICHT senden.

`R9202`: Absender SOLLTEN die `clientInfo`-Entität nur einfügen, wenn auch mindestens ein Feld ausgefüllt wird.

#### <a name="locale-deprecated"></a>locale (Gebietsschema, veraltet)

Das Feld `locale` enthält das Gebietsschema des Benutzers. Dieses Feld dupliziert das Feld [`locale`](#locale) im Stamm der Aktivität. Der Wert des Felds `locale` ist ein [ISO 639](https://www.iso.org/iso-639-language-codes.html)-Code [[5](#references)] innerhalb einer Zeichenfolge.

Das Feld `locale` in `clientInfo` ist veraltet.

`R9211`: Empfänger SOLLTEN das Feld `locale` NICHT innerhalb des `clientInfo`-Objekts verwenden.

`R9212`: Absender KÖNNEN das Feld `locale` in `clientInfo` aus Kompatibilitätsgründen ausfüllen. Wenn keine Kompatibilität mit älteren Empfänger erforderlich ist, SOLLTEN Absender die `locale`-Eigenschaft NICHT senden.

#### <a name="country"></a>Country

Das Feld `country` enthält den für den Benutzer ermittelten Standort. Dieser Wert kann sich von den [`locale`](#locale)-Daten unterscheiden, da `country` ermittelt wird, während `locale` in der Regel eine vom Benutzer oder der Anwendung vorgenommene Einstellung ist. Der Wert des Felds `country` ist ein Ländercode nach [ISO 3166-1](https://www.iso.org/iso-3166-country-codes.html) [[10](#references)] mit 2 oder 3 Buchstaben.

`R9220`: Kanäle SOLLTEN NICHT zulassen, dass Clients beliebige Werte für das Feld `country` angeben. Kanäle SOLLTEN ein Verfahren wie GPS, Standort-API oder IP-Adresserkennung verwenden, um das Land, in dem eine Anforderung generiert wurde, zu bestimmen.

#### <a name="platform"></a>Plattform

Das Feld `platform` beschreibt die Plattform des Messagingclients, die zum Generieren der Aktivität verwendet wurde. Der Wert des Felds `platform` ist eine Zeichenfolge und die Liste der möglichen Werte mit ihrer Bedeutung und wird durch den Kanal definiert, der es sendet.

Beachten Sie, dass `platform` bei Kanälen mit einem dauerhaften Chatfeed in der Regel nur bei der Entscheidung hilfreich ist, welche Inhalte eingeschlossen werden sollen, nicht aber über das Format des Inhalts. Wenn z.B. ein Benutzer auf einem mobilen Gerät um Produktsupport bittet, könnte ein Bot spezifische Hilfe zu diesem mobilen Gerät generieren. Allerdings kann der Benutzer dann den Chatfeed erneut auf dem PC öffnen, sodass er ihn auf dem Bildschirm lesen kann, während er am mobilen Gerät Änderungen vornimmt. In diesem Fall dient das Feld `platform` dazu, den Inhalt zu informieren, aber der Inhalt sollte auch auf anderen Geräten anzeigbar sein.

`R9230`: Bots SOLLTEN das Feld `platform` NICHT verwenden, um die Antwortdaten zu formatieren, es sei denn, sie haben spezifische Kenntnisse darüber, dass der gesendete Inhalt immer nur auf dem betreffenden Gerät angezeigt wird.

# <a name="appendix-iii---protocols-using-the-invoke-activity"></a>Anhang III – Protokolle, die die invoke-Aktivität verwenden

Die [invoke-Aktivität](#invoke-activity) dient nur zur Verwendung innerhalb der von Bot Framework-Kanälen unterstützten Protokolle (d.h., es ist kein generischer Erweiterungsmechanismus). Dieser Anhang enthält eine Liste aller Bot Framework-Protokolle, die diese Aktivität verwenden.

## <a name="payments"></a>Payments

Das payments-Protokoll von Bot Framework verwendet invoke zum Berechnen von Versand- und Steuerbeträgen und zur Übermittlung einer Bestätigung zum Abschluss der Zahlungen.

Das payments-Protokoll definiert drei Vorgänge (im Feld `name` einer invoke-Aktivität):
* `payment/shippingAddressChange`
* `payment/shppingOptionsChange`
* `payment/paymentResponse`

Weitere Einzelheiten finden Sie auf der Seite [Anfordern von Zahlungen](https://docs.microsoft.com/en-us/bot-framework/dotnet/bot-builder-dotnet-request-payment).

## <a name="teams-compose-extension"></a>Teams-Erstellungserweiterung

Der Microsoft Teams-Kanal verwendet invoke für [Erstellungserweiterungen](https://docs.microsoft.com/en-us/microsoftteams/platform/concepts/messaging-extensions). Diese Verwendung von invoke ist spezifisch für Microsoft Teams.



