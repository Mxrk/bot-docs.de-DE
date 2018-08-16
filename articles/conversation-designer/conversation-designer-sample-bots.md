---
title: Erstellen eines Conversation Designer-Bots aus einem Beispielbot| Microsoft-Dokumentation
description: Starten Sie einen neuen Bot mit einem der vorgestellten Beispielbots.
author: v-ducvo
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
ROBOTS: NoIndex, NoFollow
ms.openlocfilehash: 0ebf0d1b90b03789d8a77710631c83f0914099c5
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39302525"
---
# <a name="create-a-conversation-designer-bot-from-a-sample-bots"></a>Erstellen eines Conversation Designer-Bots aus einem Beispielbot

Conversation Designer ist ein leistungsfähiges Framework, das einen visuellen Botgenerator, ein deklaratives Modell zum Definieren von Bots und eine Runtime für das einfache Erstellen von Bots bietet. Eine Möglichkeit, die Boterstellung zu vereinfachen, besteht darin, den Bot von einem vorhandenen Bot aus zu starten.

Sie können Conversation Designer-Bots aus einem von vielen Beispielbots oder aus einem zuvor exportierten Conversation Designer-Bot erstellen. Jeder Beispielbot ist ein voll funktionsfähiger Bot. Das ist ein guter Ausgangspunkt, um Ihren eigenen Bot zu erstellen oder einen Beispielbot an Ihre Szenarien anzupassen.

## <a name="the-sample-bots"></a>Beispielbots

Conversation Designer enthält eine Reihe von **Beispielbots**. Sie können Ihren eigenen Bot auf Grundlage dieser **Beispielbots** erstellen. Die meisten dieser **Beispielbots** decken ein kleines Szenario eines voll funktionsfähigen Bots ab. Wenn Sie alle Funktionen dieser Beispielbots zusammenführen möchten, erstellen Sie den umfangreichen **Contoso Cafe-Bot**. 

Die folgende Tabelle listet die Bottypen auf, die Sie erstellen können, und beschreibt diese sowie die abgedeckten Szenarien. 

| Botname | Szenarien | Eingeführte Funktionen |
| ---- | ---- | ---- |
| **Basisbot** | Ein Bot mit einigen grundlegenden Fähigkeiten. Dieser Bot zeigt beispielsweise eine Willkommensnachricht an, wenn ein neuer Benutzer einer Konversation hinzugefügt wird, antwortet auf Willkommensnachrichten und führt ein Fallbackverhalten aus, wenn der Benutzer etwas fragt, das der Bot nicht versteht. | - [Aufgaben](conversation-designer-tasks.md) <br/>– Aufgabentrigger und -aktionen <br/>- [Language Understanding-Trigger](conversation-designer-luis.md)<br/>- [Skripttrigger](conversation-designer-code-recognizer.md)<br/>- [Antwortaktion](conversation-designer-reply.md) |
| **Echobot** | Ein **Basisbot**, der die Nachricht eines Benutzers zurücksendet.  Was auch immer Sie an den Bot senden, folgende Nachricht wird zurückkommen: „Hier ist, was Sie gesagt haben – ...Nachricht...“. | - [Skripttrigger](conversation-designer-code-recognizer.md)<br/>– Interpretieren des Kontextobjekts im Code<br/>– Erstellen von Entitäten im Code<br>– Verwenden von Entitäten in der Antwort des Bots |
| **QnA-Bot** | Ein Bot, der mit [QnAMaker.ai](http://qnamaker.ai) einen einzigen Frage-Antwort-Durchlauf ausführen kann. Damit dieser Beispielbot funktioniert, müssen Sie ein [QnA Maker](http://qnamaker.ai)-Konto eingerichtet und den Bot mit einigen Fragen und Antworten trainiert haben. Öffnen Sie dann diesen **QnA-Bot**, rufen Sie die Seite **Skripts** auf, und ersetzen Sie diese beiden Platzhalter durch die QnA Maker-Angaben: `<INSERT YOUR KB ID>` und `<INSERT YOUR SUBSCRIPTION KEY>`. [Speichern](conversation-designer-save-bot.md) Sie den Bot, wenn Sie diese beiden Informationen eingegeben haben. Danach können Sie Ihren Bot [testen](conversation-designer-debug-bot.md). | - [Skripttrigger](conversation-designer-code-recognizer.md)<br/>– Erstellen von HTTP-Anforderungen aus dem Code<br/>– Verbinden mit benutzerdefiniertem NLP/LU-Dienst (QnAMaker.ai im Beispiel) |
| **Konversationsbot** | Ein Bot, der eine einfache Unterhaltung führen kann und sich den Kontext merkt, z.B. den Namen des Benutzers. | - [Dialogaktion](conversation-designer-dialogues.md)<br/>– Grundlegende [Dialogstatus](conversation-designer-dialogues.md#dialogue-states) und -eigenschaften<br/>– Einführung in [Aufforderungsdialoge](conversation-designer-dialogues.md#prompt-state) (Definieren des Verhaltens für Eingabeaufforderungen und erneute Eingabeaufforderungen)<br/>- [Erstellen von Absichten](conversation-designer-luis.md#create-a-new-intent) mit bezeichneten Entitäten in Aufforderungsdialogen<br/>– Verwenden von Entitäten, die von Language Understanding in der Botantwort generiert werden<br/>– Verwenden von [Karten](conversation-designer-adaptive-cards.md) zur Verbesserung der Benutzerfreundlichkeit<br/>– Binden von Botentitäten an [Eingabeformulare](conversation-designer-adaptive-cards.md#input-form)<br/>– Erstellen und Verwenden der Botressource [Antwortvorlage](conversation-designer-response-templates.md) |
| **Speicherbot** | Ein Bot, der Benutzereinstellungen erfragen kann und diese Informationen später in einer Unterhaltung abruft. Angenommen, Sie sagen „Kommunikationseinstellungen festlegen“. Dann fragt dieser Bot, ob die Kommunikation via „Anruf“ oder „Text“ erfolgen soll. Sie können diesen Bot auch nach Informationen zum Standort von Contoso Cafe fragen, indem Sie z.B. „Standort des Cafés suchen“ oder „Wegbeschreibung nach Contoso Cafe in Redmond, Washington, abrufen“ sagen. Sobald der Bot die Informationen gefunden hat, wird er Sie ihnen entweder via Anruf oder in einer Textnachricht zukommen lassen. <br/><br/>Wenn eine Kommunikationseinstellung bereits festgelegt ist, verwendet der Bot diese Einstellung. Andernfalls wird er den Benutzer zum Festlegen auffordern. | - [Dialogaktion](conversation-designer-dialogues.md)<br/>– Speichern von Daten im Botspeicher mit [`context.global`](conversation-designer-context-object.md#context-properties)<br/>– Abrufen von Kontextinformationen aus dem Speicher für Dialoge |
| **Tischreservierungsbot** | Ein Bot, der mit einem Benutzer eine Unterhaltung mit mehreren Durchläufen führen kann, um ihm bei der Reservierung eines Tisches in Contoso Cafe zu helfen. <br/><br/>Um einen Tisch zu reservieren, benötigt dieser Bot drei Informationen von Ihnen: (1) *Gästeanzahl*, (2) *Datum/Uhrzeit* und (3) *Ort*. <br/><br/>Sie können alle drei Informationen in einer Nachricht liefern, indem Sie sagen: „Ich möchte einen Tisch für 4 Personen am Dienstag um 14 Uhr in Redmond reservieren.“ Wenn Sie eine der drei Informationen weglassen, wird der Bot Sie dazu auffordern, diese mitzuteilen. | - [Dialogaktion](conversation-designer-dialogues.md)<br/>– Komplexe Dialogaktion mit Verweisen auf andere Dialoge<br/>– Zusammenstellen von Dialogen<br/>– Einrichten des Botdialogs mithilfe der vollständigen LUIS-Funktionalität, z.B. um Slots vorauszufüllen und Korrekturen vorzunehmen<br/>– Verwenden von [Karten](conversation-designer-adaptive-cards.md) zur Verbesserung der Benutzerfreundlichkeit<br/>– Binden von Botentitäten an [Eingabeformulare](conversation-designer-adaptive-cards.md#input-form)<br/>– Erstellen und Verwenden der Botressource [Bedingte Antwortvorlage](conversation-designer-response-templates.md#conditional-response-templates) |
| **Suchoptimierungsbot** | Ein Bot, mit dem Sie Ihre Suche anhand von Kontextinformationen aus früheren Unterhaltungen verfeinern können. Sie können beispielsweise fragen „Ist das Geschäft in Seattle dieses Wochenende geöffnet?“. Dem können Sie „Und wie sieht es mit dem Geschäft in Renton aus?“, hinzufügen. Denn der Bot wird sich daran erinnern, dass Sie sich in der zweiten Frage auf „dieses Wochenende“ beziehen. Sie können z.B. auch fragen „Welche Geschäfte haben diesen Freitag um 8 Uhr geöffnet?“, gefolgt von „und um 10 Uhr?“ usw. | - [Dialogaktion](conversation-designer-dialogues.md)<br/>– Speichern von Daten im Botspeicher mit [`context.global`](conversation-designer-context-object.md#context-properties)<br/>– Verwenden des Botspeichers, um mit Benutzern kontextbezogene Unterhaltungen fortzuführen<br/>– Abrufen von Kontextinformationen aus dem Speicher für Dialoge |
| **Sandwichbestellungsbot** | Ein Bot, der verschiedene Möglichkeiten demonstriert, einen Benutzer bei einer Sandwichbestellung zur Eingabe aufzufordern. Dieser Bot hilft Ihnen beispielsweise dabei, ein Sandwich zu bestellen. <br/><br/>Um ein Sandwich zu bestellen, benötigt der Bot vier Informationen: (1) *Sandwichgröße*, (2) *Brottyp*, (3) *Proteinoption* und (4) *Sandwichbeläge*. <br/><br/>Sie können, ähnlich wie beim **Tischreservierungsbot** alle vier erforderlichen Informationen in einer Nachricht übermitteln oder eine Information nach der anderen angeben, indem Sie sagen „Ich möchte ein Sandwich bestellen“ oder „Kann ich eine großes Sandwich haben?“. Daraufhin wird der Bot die restlichen Informationen erfragen, um Ihre Bestellung zu verarbeiten. | - [Dialogaktion](conversation-designer-dialogues.md)<br/>– Einrichten von [Eingabeaufforderungen](conversation-designer-response-templates.md), um Informationen auf unterschiedliche Weise zu sammeln |
| **Contoso Cafe-Bot** | Ein voll funktionsfähiger Bot, der für Contoso Cafe erstellt wurde. Dieser Bot verfügt über alle Funktionen, die die anderen Beispielbots auch haben. Er erzeugt eine Willkommensnachricht mit einer Rich Card, mit der Sie interagieren können. Er unterstützt Willkommensnachrichten sowie Hilfe- und Fallbackmeldungen. Außerdem können Sie mit diesem Bot einen Tisch reservieren, den Standort eines Cafés finden oder ein Sandwich bestellen. | – Alle Funktionen von Conversation Designer<br/>– Speichern von Daten im Botspeicher mit [`context.global`](conversation-designer-context-object.md#context-properties)<br/>– Verwenden von [Karten](conversation-designer-adaptive-cards.md), um bestimmte Botaufgaben auszulösen, die auf Benutzereingabe basieren (siehe Karte mit Schaltflächen in Willkommensnachrichten)<br/>– Erstellen eines komplexen Bots, der diverse [Aufgaben](conversation-designer-tasks.md) ausführen kann |

## <a name="explore-sample-bots"></a>Erkunden von Beispielbots

Wenn der aktuelle Beispielbot, den Sie verwenden, nicht ganz Ihren Vorstellungen entspricht, können Sie jederzeit zu einem anderen Beispielbot wechseln. Sie finden eine Liste mit allen Beispielbots auf der Seite **Erkunden von Beispielbots**.

> [!WARNING] 
> Wenn Sie einen Beispielbot importieren, wird der aktuelle Bot durch diesen ersetzt. Wenn Sie nicht möchten, dass die am aktuellen Bot vorgenommenen Anpassungen verloren gehen, [exportieren](conversation-designer-export-import-bot.md#export-a-conversation-designer-bot) Sie diese in eine ZIP-Datei.

Gehen Sie wie folgt vor, um zu einem anderen Beispielbot zu wechseln:
1. Klicken Sie im aktuellen Bot auf **Beispielbots erkunden**. Diese Option befindet sich unten im linken Navigationsbereich. 
2. Wählen Sie aus einer **Beispielbots**-Liste einen Bot aus, und klicken Sie auf **Importieren**.
3. Wenn Sie Ihre Arbeit am aktuellen Bot speichern möchten, wählen Sie die Option **Sichern und importieren** aus. Diese Option speichert den aktuellen Bot auf Ihrem lokalen Computer und importiert dann den neuen **Beispielbot**. Wählen Sie andernfalls **Ohne Sicherung importieren** aus.

Ihr Bot wird nun durch einen neuen Beispielbot ersetzt, den Sie ausgewählt haben. 

## <a name="next-step"></a>Nächster Schritt
> [!div class="nextstepaction"]
> [Speichern eines Bots](conversation-designer-save-bot.md)
