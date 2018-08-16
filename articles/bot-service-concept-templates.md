---
title: Bot Service-Vorlagen | Microsoft-Dokumentation
description: Hier erhalten Sie Informationen über die verschiedenen Vorlagen, die Sie beim Erstellen eines Bots mit Bot Service verwenden können.
author: robstand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: ab1f0b938703f404417e48520467dc75b9f0717d
ms.sourcegitcommit: 67445b42796d90661afc643c6bb6533e9a662cbc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/06/2018
ms.locfileid: "39574836"
---
# <a name="bot-service-templates"></a>Bot Service-Vorlagen

[!INCLUDE [pre-release-label](includes/pre-release-label-v3.md)]

Bot Service umfasst fünf Vorlagen, um Ihnen den Einstieg in das Erstellen von Bots zu erleichtern. Mit diesen Vorlagen wird ein voll funktionsfähiger und sofort einsatzbereiter Bot für den schnellen Einstieg bereitgestellt. Wenn Sie [einen Bot erstellen](bot-service-quickstart.md), wählen Sie eine Vorlage und die SDK-Sprache für Ihren Bot aus.

Jede Vorlage dient als ein Ausgangspunkt, der auf einer Kernfunktion für einen Bot basiert. 

## <a name="basic-bot"></a>Basisbot
Wenn Sie einen Bot erstellen möchten, der Dialoge zum Antworten auf Benutzereingaben verwendet, wählen Sie die Vorlage „Basis“ aus. Mit der Vorlage **Basis** wird ein Bot erstellt, der den anfänglich erforderlichen Mindestsatz von Dateien und Code enthält. Der Bot gibt dem Benutzer den jeweils eingegebenen Text zurück. Sie können diese Vorlage als Einstieg in das Erstellen eines Konversationsflusses in Ihrem Bot verwenden.

## <a name="form-bot"></a>Formularbot
Wenn Sie einen Bot erstellen möchten, der Eingaben von einem Benutzer über eine geführte Konversation sammelt, wählen Sei die Vorlage **Formular** aus. Mit einem Formularbot soll ein bestimmter Satz von Informationen vom Benutzer gesammelt werden. Beispielsweise muss ein Bot, der die Bestellung eines Sandwiches von einem Benutzer aufnehmen soll, Informationen wie die Brotsorte, Auswahl des Belags, Größe des Sandwiches usw. sammeln.

Bots, die mit der Vorlage „Formular“ in der Sprache C# erstellt werden, verwenden [FormFlow](dotnet/bot-builder-dotnet-formflow.md) zum Verwalten von Formularen. Bots, die mit der Vorlage „Formular“ in der Sprache Node.js erstellt werden, verwenden [Wasserfälle](nodejs/bot-builder-nodejs-dialog-waterfall.md) zum Verwalten von Formularen.

## <a name="language-understanding-bot"></a>Language Understanding-Bot
Wenn Sie einen Bot erstellen möchten, der Modelle für natürliche Sprache zum Verstehen der Benutzerabsicht verwendet, wählen Sie die Vorlage **Language Understanding** aus. Diese Vorlage nutzt <a href="https://www.luis.ai" target="_blank">Language Understanding (LUIS)</a> zum Verstehen natürlicher Sprache.

Wenn ein Benutzer z.B. die Nachricht „Suche Neuigkeiten zu Unternehmen für virtuelle Realität“ sendet, kann der Bot mithilfe von LUIS die Bedeutung der Nachricht interpretieren. Mit LUIS kann schnell ein HTTP-Endpunkt bereitgestellt werden, der die Benutzereingabe in Hinsicht auf die enthaltene Absicht (Suche Neuigkeiten) und die wichtigsten vorhandenen Entitäten (Unternehmen für virtuelle Realität) interpretiert. LUIS ermöglicht es Ihnen, den Satz von Absichten und Entitäten anzugeben, die für Ihre Anwendung relevant sind, und führt Sie dann durch den Prozess zur Erstellung einer Language Understanding-Anwendung.

Beim Erstellen eines Bots mithilfe der Language Understanding-Vorlage erstellt Bot Service eine entsprechende LUIS-Anwendung, die leer ist (d.h., die immer `None` zurückgibt). Um Ihr LUIS-Anwendungsmodell so zu aktualisieren, dass es Benutzereingaben interpretieren kann, müssen Sie sich bei <a href="https://www.luis.ai" target="_blank">LUIS</a> anmelden und auf **Meine Anwendungen** klicken. Dann wählen Sie die Anwendung aus, die der Dienst für Sie erstellt hat, und erstellen Sie anschließend Absichten, geben Sie Entitäten an, und trainieren Sie die Anwendung.

## <a name="question-and-answer-bot"></a>Frage-und-Antwort-Bot
Wenn Sie einen Bot erstellen möchten, der teilweise strukturierte Daten wie Frage-und-Antwort-Paare in eindeutigen, hilfreichen Antworten zusammenführt, wählen Sie die Vorlage **Frage und Antwort** aus. Diese Vorlage nutzt den <a href="https://qnamaker.ai">QnA Maker</a>-Dienst zum Analysieren von Fragen und Bereitstellen von Antworten. 

Wenn Sie einen Bot mit der Vorlage „Frage und Antwort“ erstellen, müssen Sie sich bei <a href="https://qnamaker.ai">QnA Maker</a> anmelden und einen neuen QnA-Dienst erstellen. Dieser QnA-Dienst stellt Ihnen die Wissensdatenbank-ID und den Abonnementschlüssel bereit, mit denen Sie die Werte unter den [Anwendungseinstellungen](bot-service-manage-settings.md) für **QnAKnowldegebasedId** und **QnASubscriptionKey** aktualisieren können. Sobald diese Werte festgelegt sind, kann Ihr Bot alle Fragen beantworten, über die der QnA-Dienst in seiner Wissensdatenbank verfügt.

## <a name="proactive-bot"></a>Proaktiver Bot
Wenn Sie einen Bot erstellen möchten, der proaktive Nachrichten an den Benutzer senden kann, wählen Sie die Vorlage **Proaktiv** aus. In der Regel steht jede Nachricht, die ein Bot an den Benutzer sendet, in direktem Zusammenhang mit der vorherigen Eingabe des Benutzers. In einigen Fällen jedoch muss ein Bot dem Benutzer möglicherweise Informationen senden, die nicht in direktem Zusammenhang mit der letzten Nachricht des Benutzers stehen. Dieser Nachrichtentyp wird **proaktive Nachrichten** genannt. Proaktive Nachrichten können in einer Vielzahl von Szenarien nützlich sein. Wenn ein Bot beispielsweise einen Timer oder eine Erinnerung festlegt, muss er möglicherweise den Benutzer benachrichtigen, wenn dieser Zeitpunkt erreicht ist. Auch in dem Fall, dass ein Bot eine Benachrichtigung über ein externes Ereignis empfängt, muss er möglicherweise diese Informationen an den Benutzer weitergeben. 

Wenn Sie einen Bot mithilfe der Vorlage „Proaktiv“ erstellen, werden automatisch mehrere Azure-Ressourcen erstellt und zu Ihrer Ressourcengruppe hinzugefügt. Standardmäßig sind diese Azure-Ressourcen bereits für ein sehr einfaches Szenario proaktiver Nachrichten konfiguriert. 

| Ressource | BESCHREIBUNG |
|----|----|
| Azure Storage | Wird zum Erstellen der Warteschlange verwendet. |
| Azure-Funktionen-App | Eine Azure-Funktion vom Typ `queueTrigger`, die ausgelöst wird, sobald eine Nachricht in der Warteschlange vorhanden ist. Die Kommunikation mit Bot Service erfolgt über [Direct Line](https://docs.microsoft.com/bot-framework/rest-api/bot-framework-rest-direct-line-3-0-concepts). Diese Funktion verwendet Botbindung, um die Nachricht als Teil der Nutzlast des Auslösers zu senden. Bei unserer Beispielfunktion wird die Nachricht des Benutzers in unveränderter Form aus der Warteschlange weitergeleitet.
| Botdienst | Ihr Bot. Enthält die Logik, die die Nachricht vom Benutzer empfängt, fügt die Nachricht der Azure-Warteschlange hinzu, empfängt Auslöser von der Azure-Funktion und sendet die empfangene Nachricht über die Nutzlast des Auslösers zurück. |

Das folgende Diagramm zeigt die Funktionsweise ausgelöster Ereignisse bei Erstellung eines Bots mit der Vorlage „Proaktiv“.

![Übersicht über einen proaktiven Beispielbot](~/media/bot-proactive-diagram.png)

Der Vorgang beginnt, wenn der Benutzer eine Nachricht über Bot Framework-Server an Ihren Bot sendet (Schritt 1).

## <a name="next-steps"></a>Nächste Schritte
Da Sie die verschiedenen Vorlagen jetzt kennen, informieren Sie sich über die Verwendung von Cognitive Services in Bots.

> [!div class="nextstepaction"]
> [Cognitive Services für Bots](bot-service-concept-intelligence.md)
