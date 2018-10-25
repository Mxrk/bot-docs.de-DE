---
title: Leitfaden für IDs in Bot Framework | Microsoft-Dokumentation
description: In diesem Leitfaden werden die Merkmale von ID-Feldern beschrieben, die im Bot Framework v3-Protokoll enthalten sind.
keywords: ID, Bots, Protokoll
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 12/13/2017
ms.openlocfilehash: 3c4f8549f40740961feea24f73aa2e4b9b7bc82f
ms.sourcegitcommit: b78fe3d8dd604c4f7233740658a229e85b8535dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2018
ms.locfileid: "49998887"
---
# <a name="id-fields-in-the-bot-framework"></a>ID-Felder in Bot Framework

In diesem Leitfaden werden die Merkmale von ID-Feldern in Bot Framework beschrieben.

## <a name="channel-id"></a>Kanal-ID

Jeder Bot Framework-Kanal ist durch eine eindeutige ID gekennzeichnet.

Beispiel: `"channelId": "skype"`

Kanal-IDs dienen als Namespaces für andere IDs. Laufzeitaufrufe im Bot Framework-Protokoll müssen im Kontext eines Kanals erfolgen. Der Kanal gibt der Konversation und den Konto-IDs, die bei der Kommunikation verwendet werden, eine Bedeutung.

Gemäß der Konvention bestehen alle Kanal-IDs aus Kleinbuchstaben. Kanäle garantieren, dass die von ihnen ausgegebenen Kanal-IDs eine konsistente Schreibweise aufweisen, sodass Bots Ordinalvergleiche zum Herstellen von Äquivalenz verwenden können.

### <a name="rules-for-channel-ids"></a>Regeln für Kanal-IDs

- Bei Kanal-IDs ist die Groß-/Kleinschreibung zu beachten.

## <a name="bot-handle"></a>Bot-Handle

Jeder Bot, der bei Bot Framework registriert wurde, verfügt über einen Bot-Handle.

Beispiel: `FooBot`

Ein Bot-Handle stellt die Registrierung eines Bots bei Bot Framework (online) dar. Diese Registrierung ist einem HTTP-Webhook-Endpunkt und Registrierungen mit Kanälen zugeordnet.

Das Bot Framework-Entwicklerportal gewährleistet die Eindeutigkeit von Bot-Handles. Das Portal führt eine Eindeutigkeitsprüfung ohne Beachtung der Groß-/Kleinschreibung durch (d.h., dass Bot-Handles mit unterschiedlicher Groß-/Kleinschreibung als ein einzelner Handle behandelt werden), obwohl dies ein Merkmal des Entwicklerportals ist, und nicht unbedingt der Bot-Handle selbst.

### <a name="rules-for-bot-handles"></a>Regeln für Bot-Handles

* Bot-Handles sind innerhalb von Bot Framework eindeutig (ohne Beachtung der Groß-/Kleinschreibung).

## <a name="app-id"></a>App-ID

Jeder Bot, der bei Bot Framework registriert wurde, verfügt über eine App-ID.

> [!NOTE]
> Zuvor wurden Apps gewöhnlich als „MSA-Apps“ oder „MSA/AAD-Apps“ bezeichnet. Jetzt werden Apps gemeinhin einfach als „Apps“ bezeichnet, doch kann bei einigen Protokollelementen weiterhin die Bezeichnung „MSA-Apps“ verwendet werden.

Beispiel: `"msaAppId": "353826a6-4557-45f8-8d88-6aa0526b8f77"`

Eine App stellt eine Registrierung beim App-Portal des Identitätsteams dar und dient als der Dienst-zu-Dienst-Identitätsmechanismus innerhalb des Bot Framework-Laufzeitprotokolls. Apps können andere nicht botspezifische Zuordnungen aufweisen, z.B. Websites und mobile Anwendungen/Desktop-Anwendungen.

Jeder registrierte Bot weist genau eine App auf. Obwohl es einem Botbesitzer nicht möglich ist, die Zuordnung einer App zum eigenen Bot unabhängig zu ändern, kann dies in wenigen Ausnahmefällen vom Bot Framework-Team durchgeführt werden.

Bots und Kanäle können App-IDs zur eindeutigen Kennzeichnung eines registrierten Bots verwenden.

Es ist gewährleistet, dass es sich bei App-IDs um GUIDs handelt. App-IDs sollten ohne Beachtung der Groß-/Kleinschreibung verglichen werden.

### <a name="rules-for-app-ids"></a>Regeln für App-IDs

* App-IDs sind innerhalb der Microsoft App-Plattform eindeutig (GUID-Vergleich).
* Jeder Bot verfügt über genau eine entsprechende App.
* Eine Änderung der App, die einem Bot zugeordnet ist, muss durch das Bot Framework-Team erfolgen.

## <a name="channel-account"></a>Kanalkonto

Jeder Bot und Benutzer verfügt über ein Konto in jedem Kanal. Das Konto enthält einen Bezeichner (`id`) und andere informative, nicht strukturelle Daten des Bots, wie einen optionalen Namen.

Beispiel: `"from": { "id": "john.doe@contoso.com", "name": "John Doe" }`

Dieses Konto beschreibt die Adresse innerhalb des Kanals, über den Nachrichten gesendet und empfangen werden können. In einigen Fällen befinden sich diese Registrierungen in einem einzelnen Dienst (z.B. Skype, Facebook). In anderen Fällen erstrecken sich Registrierungen über viele Systeme (E-Mail-Adressen, Telefonnummern). Bei anonymeren Kanälen (z.B. Web Chat) kann die Registrierung temporär sein.

Kanalkonten sind in Kanälen geschachtelt. Ein Facebook-Konto ist beispielsweise einfach eine Nummer. Diese Nummer kann in anderen Kanälen eine andere Bedeutung haben und hat außerhalb aller Kanäle keine Bedeutung.

Die Beziehung zwischen Kanalkonten und Benutzern (tatsächlichen Personen) hängt von Konventionen ab, die den einzelnen Kanälen zugeordnet sind. Beispielsweise bezieht sich eine SMS-Nummer normalerweise über einen bestimmten Zeitraum auf eine Person, und danach kann die Nummer an eine andere Person übertragen werden. Im Gegensatz dazu bezieht sich ein Facebook-Konto normalerweise dauerhaft auf eine Person, obwohl es nicht ungewöhnlich ist, dass sich zwei Personen ein Facebook-Konto teilen.

Bei den meisten Kanälen kann man sich ein Kanalkonto als eine Art Postfach vorstellen, an das Nachrichten übermittelt werden können. Normalerweise können bei den meisten Kanälen einem einzelnen Postfach mehrere Adressen zugeordnet werden. So können beispielsweise „jdoe@contoso.com“und „john.doe@service.contoso.com“ in den gleichen Posteingang aufgelöst werden. Einige Kanäle gehen noch einen Schritt weiter und ändern die Adresse des Kontos basierend auf dem jeweils darauf zugreifenden Bot. Beispielsweise ändern Skype und Facebook die Benutzer-IDs, sodass jeder Bot über eine andere Adresse zum Senden und Empfangen von Nachrichten verfügt.

Es ist zwar in einigen Fällen möglich, Äquivalenz zwischen Adressen herzustellen, doch müssen zum Herstellen von Äquivalenz zwischen Postfächern und Äquivalenz zwischen Personen die Konventionen innerhalb des Kanals bekannt sein, und dies ist in vielen Fällen nicht möglich.

Einem Bot wird die Adresse seines Kanalkontos über das Feld `recipient` bei Aktivitäten mitgeteilt, die an den Bot gesendet werden.

### <a name="rules-for-channel-accounts"></a>Regeln für Kanalkonten

* Kanalkonten haben nur innerhalb ihres zugeordneten Kanals eine Bedeutung.
* Mehrere IDs können in das gleiche Konto aufgelöst werden.
* Ein Ordinalvergleich kann verwendet werden, um zwei IDs als identisch festzulegen.
* Es gibt im Allgemeinen keinen Vergleich, mit dem festgestellt werden kann, ob zwei unterschiedliche IDs in das gleiche Konto, den gleichen Bot oder die gleiche Person aufgelöst werden.
* Die Stabilität von Zuordnungen zwischen IDs, Konten, Postfächern und Personen hängt vom Kanal ab.

## <a name="conversation-id"></a>Konversations-ID

Nachrichten werden im Kontext einer Konversation gesendet und empfangen, die durch eine ID gekennzeichnet ist.

Beispiel: `"conversation": { "id": "1234" }`

Eine Konversation umfasst den Austausch von Nachrichten und andere Aktivitäten. Jede Konversation weist null oder mehr Aktivitäten auf, und jede Aktivität tritt in genau einer Konversation auf. Konversationen können unbefristet sein oder eindeutige Start- und Endzeitpunkte aufweisen. Der Prozess zum Erstellen, Ändern oder Beenden einer Konversation tritt innerhalb des Kanals auf (d.h., dass eine Konversation vorhanden ist, wenn dies vom Kanal erkannt wird), und die Merkmale dieser Prozesse werden durch den Kanal festgelegt.

Die Aktivitäten innerhalb einer Konversation werden von Benutzern und Bots gesendet. Die Definition, welche Benutzer an einer Konversation „teilnehmen“, ist je nach Kanal verschieden, und kann theoretisch vorhandene Benutzer, Benutzer, die jemals eine Nachricht empfangen haben, und Benutzer, die eine Nachricht gesendet haben, umfassen.

Verschiedene Kanäle (z.B. SMS, Skype und möglicherweise andere) haben die Eigenart, dass die Konversations-ID, die einer 1:1-Konversation zugewiesen ist, die Remotechannel-Konto-ID ist. Diese Eigenart hat zwei Nebeneffekte:
1. Die Konversations-ID basiert darauf, von wem sie angezeigt wird. Wenn sich Teilnehmer A und B unterhalten, wird Teilnehmer A die Konversations-ID „B“ und Teilnehmer B die Konversations-ID „A“ angezeigt.
2. Wenn der Bot über mehrere Kanalkonten in diesem Kanal verfügt (z.B., wenn der Bot zwei SMS-Nummern hat), reicht die Konversations-ID nicht aus, um die Konversation aus Sichtweise des Bots eindeutig zu identifizieren.

Daher stellt eine Konversations-ID nicht unbedingt eine eindeutige Identifizierung einer einzelnen Konversation innerhalb eines Kanals dar, auch nicht bei einem einzelnen Bot.

### <a name="rules-for-conversation-ids"></a>Regeln für Konversations-IDs

* Konversationen haben nur innerhalb ihres zugeordneten Kanals eine Bedeutung.
* Mehrere IDs können in die gleiche Konversation aufgelöst werden.
* Ordinalgleichheit bewirkt nicht unbedingt, dass es sich bei zwei Konversations-IDs um die gleiche Konversation handelt, obwohl dies in den meisten Fällen so ist.

## <a name="activity-id"></a>Aktivitäts-ID

Aktivitäten werden innerhalb des Bot Framework-Protokolls gesendet und empfangen, und diese sind manchmal identifizierbar.

Beispiel: `"id": "5678"`

Aktivitäts-IDs sind optional und werden von Kanälen verwendet, um dem Bot eine Möglichkeit zu bieten, in nachfolgenden API-Aufrufen (sofern verfügbar) auf die ID zu verweisen:
* Antworten auf eine bestimmte Aktivität
* Abfragen der Liste der Teilnehmer auf Aktivitätsebene

Da keine weiteren Anwendungsfälle festgelegt wurden, liegen keine weiteren Regeln für die Handhabung von Aktivitäts-IDs vor.
