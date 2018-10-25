---
title: Dialogzustand | Microsoft-Dokumentation
description: Es wird beschrieben, wie Zustände im Bot Builder SDK funktionieren.
keywords: Zustand, Botzustand, Konversationszustand, Benutzerzustand
author: johnataylor
ms.author: johtaylo
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 9/21/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 4da715650eb1c3e7b284aaa47aa5ce4ac7280f4c
ms.sourcegitcommit: b78fe3d8dd604c4f7233740658a229e85b8535dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2018
ms.locfileid: "49998757"
---
# <a name="dialog-state"></a>Dialogzustand

Dialoge sind ein Ansatz zum Implementieren einer Konversationslogik mit mehreren Durchläufen („Turns“) und ein Beispiel für ein Feature im SDK, das auf einem persistenten Zustand beruht. 

Ein auf Dialogen basierender Bot enthält normalerweise eine DialogSet-Sammlung als Membervariable in der Botimplementierung. Das DialogSet-Element wird mit einem Handle für ein Objekt erstellt, das als „Accessor“ bezeichnet wird. 

Der Accessor ist das zentrale Konzept im SDK-Zustandsmodell. Ein Accessor implementiert die IStatePropertyAccessor-Schnittstelle und muss Implementierungen für die Get-, Set- und Delete-Methoden bereitstellen. Zum Erstellen eines Accessors stellen Sie einen Eigenschaftennamen bereit. 

Der Code, für den der Accessor verwendet wird, kann dessen Get-, Set- und Delete-Methoden aufrufen, da sicher ist, dass damit auf eine Eigenschaft dieses Namens verwiesen wird. Wenn für eine Komponente einer höheren Ebene die Beibehaltung eines Zustands erforderlich ist, sollte der Accessor genutzt werden. Auf diese Weise können in unterschiedlichen Szenarien jeweils die eigenen Implementierungen des Speicherzustands verwendet werden, während trotzdem der gleiche allgemeine Code genutzt wird. Für die Dialoge wird beispielsweise der Accessor verwendet, und dies ist hierfür die einzige Möglichkeit des Zugriffs auf den persistenten Zustand.

![Dialogzustand](media/bot-builder-dialog-state.png)

Wenn das OnTurn-Element des Bots aufgerufen wird, initialisiert der Bot das Dialogsubsystem, indem CreateContext für DialogSet aufgerufen wird. Da für die Erstellung eines DialogContext-Elements ein Zustand benötigt wird, nutzt das DialogSet-Element den Accessor, um den entsprechenden JSON-Code zum Dialogzustand abzurufen. Das SDK stellt eine Implementierung des Accessors als BotState-Klasse bereit. Anwendungen, die die Zustandsimplementierung nutzen möchten, können BotState als Unterklasse verwenden und so eine Implementierung des Accessors erben. Im SDK sind zwei Unterklassen von BotState enthalten:

- UserState
- ConversationState

Für UserState und ConversationState werden Schlüssel für den zugrunde liegenden Speicher verwendet. Der Schlüssel wird an den physischen Speicher übergeben. In logischer Hinsicht fungiert der Schlüssel als Namespace für die vom Accessor benannte Eigenschaft. Für die BotState-Implementierung wird intern die eingehende Aktivität für das TurnContext-Element verwendet, um den verwendeten Speicherschlüssel dynamisch zu generieren.

- Mit UserState wird ein Schlüssel erstellt, indem die Kanal-ID und die Von-ID (Channel ID/From ID) verwendet werden. Beispiel: _{Activity.ChannelId}/conversations/{Activity.From.Id}#DialogState_
- Mit ConversationState wird ein Schlüssel erstellt, indem die Kanal-ID und die Konversations-ID (Channel ID/Conversation ID)verwendet werden. Beispiel: _{Activity.ChannelId}/users/{Activity.Conversation.Id}#YourPropertyName_

Eine Anwendung muss einen Accessor bereitstellen. Die Bindung an den entsprechenden dynamisch erstellten Speicherschlüssel und Eigenschaftennamen erfolgt dann im Hintergrund. Die BotState-Implementierung des Accessors enthält einige Optimierungen: 

- Die erste Optimierung ist eine zwischengespeicherte und verzögerte Last. Eine Last für die eigentliche Speicherimplementierung wird verzögert, bis der Get-Aufruf für den Accessor erfolgt, und das Ergebnis dieser Last wird in einem Cache vorgehalten. Ein Verweis auf diesen Cache wird dem TurnContext-Element hinzugefügt, indem ein vom entsprechenden BotState bereitgestellter Schlüssel verwendet wird. Der Cache für UserState wird also in einem Feld mit dem Namen „UserState“ vorgehalten, und der Cache für ConversationState befindet sich im Feld „ConversationState“. An Aufrufe der verschiedenen Accessorobjekte wird das TurnContext übergeben, damit der entsprechende Cache abgerufen werden kann.

- Die zweite Optimierung besteht darin, dass die BotState-Klasse Logik enthält, mit der bestimmt werden kann, ob Änderungen am Zustand vorgenommen wurden. Nur wenn Änderungen vorgenommen wurden, wird für den zugrunde liegenden Speicher ein tatsächlicher Speichervorgang durchgeführt.

- Die dritte Optimierung innerhalb der BotState-Implementierung befindet sich in einer Klasse mit dem Namen BotStateSet. Die BotStateSet-Klasse ist eine Sammlung mit BotState-Objekten, die einen Aufruf ihrer SaveChanges-Methode parallel an alle Member der Sammlung delegiert.

Es sollte beachtet werden, dass der SaveChanges-Aufruf von BotState nicht Teil der IStatePropertyAccessor-Schnittstelle ist. Der Grund ist, dass SaveChanges eine bestimmte Optimierung der BotState-Implementierung und kein Kernaspekt des Modells ist. Beispielsweise verfügt der Code der Dialoge nicht über Informationen zu BotState, geschweige denn zu SaveChanges. Der Code von Dialogen ist nur mit dem Accessor gekoppelt. Hiermit soll erreicht werden, dass die SaveChanges-Methode außerhalb des Dialogsystems aufgerufen wird, nachdem die Ausführung abgeschlossen ist. Wie im Diagramm dargestellt, kann sie beispielsweise über die OnTurn-Methode des Bots aufgerufen werden.

Es ist wichtig zu beachten, dass für die BotState-Implementierung eine bestimmte Semantik gilt. Insbesondere wird das Verhalten „letzter Schreibvorgang gewinnt“ unterstützt, bei dem der letzte Schreibvorgang den vorherigen Schreibzustand außer Kraft setzt. Dies funktioniert für viele Anwendungen ggf. gut, aber es sind einige Aspekte zu beachten. Dies gilt vor allem für Szenarien mit horizontaler Skalierung, bei denen bestimmte Parallelitätsebenen zu berücksichtigen sind. Falls dies ein Problem darstellt, besteht die Lösung darin, einen eigenen Accessor zu implementieren und an den Dialog zu übergeben. Es gibt viele alternative Ansätze. Für eine Lösung kann beispielsweise die Entscheidung getroffen werden, die ETag-Bedingung zu nutzen, deren Einsatz für Cloudspeicherdienste wie Azure Storage beliebt ist. In diesem Fall würden mit der Lösung wahrscheinlich andere wichtige Teile implementiert werden. Unter Umständen werden ausgehende Aktivitäten beispielsweise gepuffert und erst nach einem erfolgreichen Speichervorgang gesendet. Es ist wichtig, hierbei Folgendes zu beachten: Dieses Verhalten ist nicht das Verhalten der BotState-Implementierung, sondern etwas, das von einer Anwendung bereitgestellt und auf Accessorebene hinzugefügt wird.

Die BotState-Implementierung selbst verfügt über ein austauschbares Speichermodell. Hierfür wird ein einfaches Laden/Speichern-Muster verwendet, und das SDK stellt zwei alternative Implementierungen für die Produktion bereit. Eine für Azure Storage und die andere für Cosmos DB, sowie außerdem eine In-Memory-Implementierung zu Testzwecken. Hierbei sollte aber unbedingt beachtet werden, dass die Semantik von „letzter Schreibvorgang gewinnt“ von der BotState-Implementierung vorgegeben wird.

## <a name="handling-state-in-middleware"></a>Verarbeiten des Zustands in Middleware
Im obigen Diagramm ist ein Aufruf von SaveAllChanges dargestellt, der am Ende des OnTurn-Vorgangs des Bots durchgeführt wird. Hier ist das gleiche Diagramm mit dem Schwerpunkt auf dem Aufruf angegeben.

![Zustand der Middleware – Probleme](media/bot-builder-dialog-state-problem.png)

Das Problem bei diesem Ansatz ist, dass alle Zustandsaktualisierungen, die über die benutzerdefinierte Middleware nach der Rückgabe der OnTurn-Methode des Bots durchgeführt werden, nicht im beständigen Speicher gespeichert werden. Die Lösung besteht darin, den Aufruf von SaveAllChanges auf den Zeitpunkt nach dem Abschluss der benutzerdefinierten Middleware zu verschieben, indem am Ende des Middlewarestapels „AutoSaveChangesMiddleware“ hinzugefügt wird. Die Ausführung ist unten dargestellt.

![Zustand der Middleware – Lösung](media/bot-builder-dialog-state-solution.png)

## <a name="additional-resources"></a>Zusätzliche Ressourcen
Ausführlichere Informationen finden Sie im Bot Builder SDK auf GitHub [[C#](https://github.com/Microsoft/BotBuilder-dotnet) | [JavaScript](https://github.com/Microsoft/BotBuilder-js)].
