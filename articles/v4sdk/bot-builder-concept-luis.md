---
title: Language Understanding | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Ihren Bots mit Microsoft Cognitive Services künstliche Intelligenz hinzufügen können, um sie benutzerfreundlicher und ansprechender zu gestalten.
keywords: LUIS, Absicht, Erkennung, Dispatch-Tool, QnA, QnA Maker
author: ivorb
ms.author: v-ivorb
manager: rstand
ms.topic: article
ms.prod: bot-framework
ms.date: 09/19/2018
ms.reviewer: ''
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: af79bb40e3d24557fd898fa0a0ca2ef7b0286af4
ms.sourcegitcommit: 3bf3dbb1a440b3d83e58499c6a2ac116fe04b2f6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/23/2018
ms.locfileid: "46707546"
---
# <a name="language-understanding"></a>Language Understanding

[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

Bots können eine Vielzahl von Unterhaltungsstilen verwenden, von strukturiert und geführt bis zur freien Form und offenem Ende. Ein Bot muss basierend auf dem, was der Benutzer gesagt hat, entscheiden, was er als nächstes in seinem Gesprächsfluss sagen soll, und in einer Unterhaltung mit offenem Ende gibt es eine große Auswahl an Benutzerantworten.

| Geführt | Öffnen |
|------|------|
| Ich bin der Reisebot. Wählen Sie eine der folgenden Optionen aus: Flüge suchen, Hotels suchen, Mietwagen suchen. | Ich kann Sie beim Buchen von Reisen unterstützen. Was möchten Sie? |
| Benötigen Sie noch etwas anderes? Klicken Sie auf „Ja“ oder „Nein“. | Benötigen Sie noch etwas anderes? |

Die Interaktion zwischen Benutzern und Bots erfolgt häufig in freier Form, und Bots müssen Sprache auf natürliche Weise und kontextabhängig verstehen. Dieser Artikel erläutert, wie Sie mithilfe von Language Understanding (LUIS) feststellen können, was Benutzer wollen, Konzepte und Entitäten in Sätzen identifizieren und schließlich Ihren Bots erlauben, mit der entsprechenden Aktion zu reagieren.

## <a name="recognize-intent"></a>Erkennen der Absicht

[LUIS](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/home) hilft Ihnen bei der Bestimmung der **Absicht** des Benutzers (was er tun möchte) aus dem, was er sagt, damit Ihr Bot angemessen reagieren kann. LUIS ist besonders hilfreich, wenn das, was ein Benutzer zu Ihrem Bot sagt, nicht einer vorhersehbaren Struktur oder einem bestimmten Muster folgt. Wenn ein Bot eine Unterhaltungsbenutzeroberfläche aufweist, in die der Benutzer spricht oder eine Antwort eingibt, kann es endlose Variationen von *Äußerungen* geben, die die gesprochene oder Texteingabe des Benutzers sind.

Betrachten Sie zum Beispiel die zahlreichen Möglichkeiten, wie ein Benutzer eines Reisebots einen Flug buchen kann.

![Verschiedene unterschiedliche formulierte Äußerungen für eine Flugbuchung](media/cognitive-services-add-bot-language/cognitive-services-luis-utterances.png)

Diese Äußerungen können unterschiedliche Strukturen aufweisen und verschiedene Synonyme für „Flug“ enthalten, an die Sie nicht gedacht haben. In Ihrem Bot kann es eine Herausforderung sein, die Programmlogik zu schreiben, die alle Äußerungen berücksichtigt und sie dennoch von anderen Absichten unterscheidet, die dieselben Wörter enthalten. Darüber hinaus muss Ihr Bot *Entitäten* extrahieren, die weitere wichtige Wörter (z.B. Orte und Zeiten) sind. LUIS vereinfacht diesen Vorgang durch Identifizieren von Absichten und Entitäten im Kontext.

Wenn Sie Ihren Bot für die Eingabe in natürlicher Sprache entwerfen, bestimmen Sie, welche Absichten und Entitäten Ihr Bot erkennen muss, und überlegen sich, wie diese mit Aktionen verbunden werden, die Ihr Bot ausführt. In [luis.ai](https://www.luis.ai) definieren Sie benutzerdefinierte Absichten und Entitäten und geben ihr Verhalten an, indem Sie Beispiele für jede Absicht bereitstellen und die Entitäten damit kennzeichnen.

Ihr Bot verwendet die Absicht, die von LUIS erkannt wurde, um das Unterhaltungsthema zu bestimmen oder einen Gesprächsfluss zu beginnen. Wenn ein Benutzer z.B. sagt „Ich möchte einen Flug buchten“, erkennt Ihr Bot die BookFlight-Absicht und ruft den Gesprächsfluss zum Starten einer Suche nach Flügen auf. LUIS erkennt Entitäten wie den Zielort und das Abreisedatum, sowohl in der ursprünglichen Äußerung, die die Absicht auslöst, als auch später im Gesprächsfluss. Sobald der Bot über alle Informationen verfügt, die er benötigt, kann er der Absicht des Benutzers nachkommen.

![Eine Unterhaltung mit einem Bot wird durch die BookFlight-Absicht ausgelöst.](media/cognitive-services-add-bot-language/cognitive-services-luis-conversation-high-level.png)

### <a name="recognize-intent-in-common-scenarios"></a>Erkennen der Absicht in gängigen Szenarien

Um Entwicklungszeit einzusparen, bietet LUIS vorab trainierte Sprachmodelle, die gängige Äußerungen für gängige Kategorien von Bots erkennen. 

**Vordefinierte Domänen** sind vorab trainierte, gebrauchsfertige Sammlungen von Absichten und Entitäten, die für gängige Szenarien wie Termine, Erinnerungen, Verwaltung, Fitness, Unterhaltung, Kommunikation, Reservierungen und vieles mehr gut zusammen funktionieren. Die vordefinierte Domäne **Utilities** (Hilfsprogramme) unterstützt Ihren Bot bei allgemeinen Aufgaben wie Stornieren, Bestätigungen, Hilfe, Wiederholen und Beenden. Sehen Sie sich die [vordefinierten Domänen](https://docs.microsoft.com/en-us/azure/cognitive-services/LUIS/luis-how-to-use-prebuilt-domains) von LUIS an.

**Vordefinierte Entitäten** helfen Ihrem Bot dabei, gängige Arten von Informationen wie Datum, Uhrzeit, Zahlen, Temperatur, Währung, Geografie und Alter zu erkennen. Unter [Verwenden vordefinierter Entitäten](https://docs.microsoft.com/en-us/azure/cognitive-services/LUIS/pre-builtentities) finden Sie Hintergrundinformationen zu den Typen, die von LUIS erkannt werden.

## <a name="how-your-bot-gets-messages-from-luis"></a>Abrufen von Nachrichten aus LUIS durch den Bot

Nachdem Sie LUIS eingerichtet und eine Verbindung damit hergestellt haben, kann Ihr Bot die Nachricht an Ihre LUIS-App senden, die eine JSON-Antwort mit den Absichten und Entitäten zurückgibt. Anschließend können Sie den [Durchlaufkontext](bot-builder-concept-activity-processing.md#turn-context) im _Durchlaufhandler_ Ihres Bots verwenden, um den Konversationsfluss basierend auf der Absicht in der LUIS-Antwort zu steuern. 

![Übergeben von Absichten und Entitäten an Ihren Bot](./media/cognitive-services-add-bot-language/cognitive-services-luis-message-flow-bot-code.png)

Informationen zum Einstieg in die Nutzung einer LUIS-App mit Ihrem Bot finden Sie unter [Verwenden von LUIS für Language Understanding][luis-v4-how-to].

## <a name="best-practices-for-language-understanding"></a>Bewährte Methoden für Language Understanding

Ziehen Sie beim Entwerfen eines Sprachmodells für Ihren Bot die folgenden Methoden in Betracht.

### <a name="consider-the-number-of-intents"></a>Berücksichtigen der Anzahl der Absichten

LUIS-Apps erkennen die Absicht durch die Zuordnung einer Äußerung zu einer von mehreren Kategorien. Das natürliche Ergebnis ist, dass die Bestimmung der richtigen Kategorie aus einer großen Anzahl von Absichten die Fähigkeit einer LUIS-App verringern kann, zwischen ihnen zu unterscheiden.

Eine Möglichkeit zur Verringerung der Anzahl der Absichten besteht in der Verwendung eines hierarchischen Entwurfs. Betrachten wir den Fall eines persönlichen Assistentenbots, der drei Absichten in Bezug auf das Wetter, drei Absichten in Bezug auf die Automatisierung des Hauses und drei weitere Hilfsprogrammabsichten hat, nämlich Hilfe, Stornieren und Begrüßen. Wenn Sie alle Absichten in der gleichen LUIS-App verwenden, sind es bereits 9, und wenn Sie dem Bot Features hinzufügen, können es am Ende Dutzende sein. Stattdessen können Sie eine LUIS-Dispatcher-App verwenden, um festzustellen, ob sich die Anforderung des Benutzers auf das Wetter, die Automatisierung des Hauses oder das Hilfsprogramm bezieht, und dann die LUIS-Anwendung für die Kategorie aufrufen, die der Dispatcher bestimmt. In diesem Fall beginnt jede der LUIS-Apps mit nur 3 Absichten.

### <a name="use-a-none-intent"></a>Verwenden einer None-Absicht

Es ist häufig der Fall, dass Benutzer Ihres Bots etwas unerwartetes oder nicht relevantes sagen, das sich nicht auf den aktuellen Gesprächsfluss bezieht. Die _None_-Absicht wird bereitgestellt, um diese Nachrichten zu verarbeiten.

Wenn Sie keine Absicht für die Behandlung der Fallback-, Standard- oder „Nichts von alledem“-Fälle trainieren, kann Ihre LUIS-App nur Nachrichten in die für sie definierten Absichten klassifizieren. Nehmen wir beispielsweise an an, Sie verwenden eine LUIS-App mit zwei Absichten: `HomeAutomation.TurnOn` und `HomeAutomation.TurnOff`. Wenn dies die einzigen Absichten sind und die Eingabe ganz anders lautet (z.B. „Termin am Freitag vereinbaren“), hat Ihre LUIS-Anwendung keine andere Wahl, als diese Nachricht entweder als HomeAutomation.TurnOn oder HomeAutomation.TurnOff zu klassifizieren. Wenn Ihre LUIS-App über eine `None`-Absicht mit einigen Beispielen verfügt, können Sie Fallbacklogik in Ihrem Bot zum Behandeln von unerwarteten Äußerungen bereitstellen.

Die `None`-Absicht ist hilfreich, wenn Sie die Erkennungsergebnisse verbessern möchten. In diesem Szenario zur Gebäudeautomatisierung kann „Termin am Freitag vereinbaren“ die `HomeAutomation.TurnOn`-Absicht mit niedriger Zuverlässigkeit erzeugen, sodass sie von Ihrem Bot abgelehnt werden sollte. Sie können Ihrem Modell solche Ausdrücke unter der `None`-Absicht hinzufügen, damit sie korrekt in `None` aufgelöst werden.

### <a name="review-the-utterances-that-luis-app-receives"></a>Überprüfen der Äußerungen, die die LUIS-App empfängt

LUIS-Apps bieten ein Feature zur Verbesserung der App-Leistung, indem sie Nachrichten überprüfen, die Benutzer an sie gesendet haben. Eine exemplarische Schritt-für-Schritt-Vorgehensweise finden Sie unter [Überprüfen von Endpunktäußerungen](https://docs.microsoft.com/azure/cognitive-services/LUIS/label-suggested-utterances).


## <a name="integrate-multiple-luis-apps-and-qna-services-with-the-dispatch-tool"></a>Integrieren mehrerer LUIS-Apps and QnA-Dienste mit dem Dispatch-Tool

Wenn Sie einen Mehrzweckbot erstellen, der mehrere Konversationsthemen versteht, können Sie damit beginnen, Dienste für jede Funktion separat zu entwickeln und diese dann zu integrieren. Diese Dienste können LUIS-Apps (Language Understanding) und QnAMaker-Dienste umfassen. Hier sind einige Beispielszenarien, in denen ein Bot mehrere LUIS-Apps, mehrere QnAMaker-Dienste oder eine Kombination aus beiden kombinieren kann:

* Ein persönlicher Assistentenbot ermöglicht dem Benutzer, eine Vielzahl von Befehlen aufzurufen. Jede Kategorie von Befehlen bildet eine „Fertigkeit“, die separat entwickelt werden kann, und für jede Fertigkeit ist eine LUIS-App vorhanden.
* Ein Bot durchsucht viele Wissensdatenbanken, um Antworten auf häufig gestellte Fragen (FAQs) zu finden.
* Ein Bot für ein Unternehmen verfügt über LUIS-Apps zum Erstellen von Kundenkonten und Aufgeben von Bestellungen sowie außerdem über einen QnAMaker-Dienst für die häufig gestellten Fragen.  

### <a name="the-dispatch-tool"></a>Das Dispatch-Tool

Das Dispatch-Tool hilft Ihnen, mehrere LUIS-Apps und QnAMaker-Dienste in Ihren Bot zu integrieren, indem es eine *Dispatch-App* erstellt, also eine neue LUIS-App, die Nachrichten an die entsprechenden LUIS- und QnAMaker-Dienste weiterleitet. Eine Schritt-für-Schritt-Anleitung, in der mehrere LUIS-Apps und QnAMaker-Dienste in einem Bot kombiniert werden, finden Sie im [Dispatch-Tutorial](./bot-builder-tutorial-dispatch.md).

## <a name="use-luis-to-improve-speech-recognition"></a>Verwenden von LUIS zur Verbesserung der Spracherkennung

Für einen Bot, mit dem Benutzer sprechen, kann die Integration in LUIS Ihrem Bot helfen, Wörter zu identifizieren, die bei der Umwandlung von Sprache in Text ggf. missverstanden werden können.  In einem Schachszenario könnte ein Benutzer z.B. „Move knight to A7“ (Springer auf A7) sagen. Ohne Kontext für die Absicht des Benutzers wird die Äußerung ggf. als „Move night 287“ erkannt. Indem Sie Entitäten erstellen, die Schachfiguren und Koordinaten darstellen und sie in Äußerungen bezeichnen, stellen Sie einen Kontext für die Spracherkennung zur Verfügung, um sie zu identifizieren. Sie können das [Spracherkennungspriming][speechrecognitionpriming] mit Bot Framework-Kanälen aktivieren, die in die Bing-Spracheingabe integriert sind, z.B. Web Chat, Bot Framework Emulator und Cortana.  

## <a name="additional-resources"></a>Zusätzliche Ressourcen
Weitere Informationen finden Sie in der Dokumentation zu [Cognitive Services](https://docs.microsoft.com/en-us/azure/cognitive-services/).
