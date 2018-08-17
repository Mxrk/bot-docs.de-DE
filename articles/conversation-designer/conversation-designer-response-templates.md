---
title: Antwortvorlage | Microsoft-Dokumentation
description: Erfahren Sie, wie die Antwortvorlage für Conversation Designer-Bots eingerichtet wird.
author: vkannan
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
ROBOTS: NoIndex, NoFollow
ms.openlocfilehash: b09b7fa5f5672c121711deb2edcdc9718a79983f
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39304184"
---
# <a name="response-template-for-conversation-designer-bots"></a>Antwortvorlage für Conversation Designer-Bots
> [!IMPORTANT]
> Conversation Designer ist noch nicht für alle Kunden verfügbar. Weitere Informationen zur Verfügbarkeit von Conversation Designer folgen später in diesem Jahr.

Dank Sprachgenerierung kann Ihr Bot mit Benutzern in einer vielfältigen und natürlichen Weise unter Verwendung variabler Antwortnachrichten kommunizieren. Diese Nachrichten werden über Antwortvorlagen in Conversation Designer verwaltet.

Antwortvorlagen ermöglichen die Wiederverwendung von Antworten und sind bei der Aufrechterhaltung eines konsistenten Tons und einer konsistenten Sprache in Botantworten hilfreich. 

## <a name="create-response-templates"></a>Erstellen von Antwortvorlagen

In Conversation Designer können Sie Antwortvorlagen erstellen, die in allen Situationen wiederverwendet werden können, in denen Sie eine Antwort an einen Benutzer senden müssen. 

Einfache Antwortvorlagen definieren eine einfache Sammlung einzelner möglicher Sprach- und Anzeigeäußerungen. Sie können diese Vorlagen dann in den Antworten, in Abfragezuständen oder auf adaptiven Karten Ihres Bots verwenden, um vollständig aufgelöste Zeichenfolgen zu rekonstruieren.

Gehen Sie wie folgt vor, um eine einfache Antwortvorlage hinzuzufügen:
1. Klicken Sie im linken Bereich auf **Hinzufügen**. Ein Kontextmenü wird angezeigt.
2. Klicken Sie auf **Einfache Antwort**. Im Hauptinhaltsbereich wird ein Editorfenster angezeigt.
3. Geben Sie im Feld **Simple response name** (Name der einfachen Antwort) einen Namen für diese einfache Antwort ein.
4. Geben Sie im Feld **Bot's response to user** (Antwort des Bots an den Benutzer) die Antwort ein, eine Formulierung nach der anderen.
5. Klicken Sie in der oberen rechten Ecke auf **Speichern**, wenn Sie das Einrichten der einfachen Antwort abgeschlossen haben. 

Beispielsweise können Sie eine Vorlage mit Bestätigungsformeln (die Sie „AckPhrase“ nennen) erstellen, die folgende Elemente enthält:

- OK
- Ja, natürlich
- Klar doch
- Ich helfe Ihnen gerne weiter.
- Darf ich helfen?

Bei Verwendung dieser Vorlage löst die Unterhaltungsruntime zufällig zu einer dieser Formulierungen auf, so dass die Antworten des Bots recht natürlich wirken, statt dumpf und monoton.

## <a name="conditional-response-templates"></a>Bedingte Antwortvorlagen

In bedingten Antwortvorlagen sind mehrere Antworten mit Bedingungen angegeben. Die von Ihnen geschriebene Rückruffunktion hilft dem Sprachgenerierungsmodul zu entscheiden, welche Antwortzeichenfolge auf der Grundlage der von Ihnen angegebenen Bedingung verwendet werden soll. 

Beispielsweise sollte eine Tageszeitvorlage je nach Tageszeit zu „Guten Morgen“ oder „Guten Abend“ aufgelöst werden. 

Gehen Sie wie folgt vor, um eine bedingte Antwortvorlage hinzuzufügen:
1. Klicken Sie im linken Bereich auf **Hinzufügen**. Ein Kontextmenü wird angezeigt.
2. Klicken Sie auf **Conditional response** (Bedingte Antwort). Im Hauptinhaltsbereich wird ein Editorfenster angezeigt.
3. Geben Sie im Feld **Conditional response name** (Name der bedingten Antwort) einen Namen für diese Vorlage ein.
4. Geben Sie im Feld **Conditional response** (Bedingte Antwort) Ihre Formulierungen eine nach der anderen ein. Geben Sie beispielsweise für „timeOfDayTemplate“ „Guten Morgen“ und „Guten Abend“ als zwei mögliche Antworten in die Vorlage ein.
5. Geben Sie für die Antwort „Guten Morgen“ den **Condition name** (Name der Bedingung) als „Morgen“ an. Geben Sie analog dazu für die Antwort „Guten Abend“ den **Condition name** (Name der Bedingung) als „Abend“ an. Das benutzerdefinierte Skript, das Sie erstellen, gibt eine dieser Bedingungen zurück.
6. Geben Sie im Feld **Code to execute on run** (Bei der Ausführung auszuführender Code) den Namen einer Rückruffunktion ein (z.B. `fnResolveTimeOfDayTemplate`). Klicken Sie dann auf **Code anzeigen**, um den *Scripts**-Editor zu laden. Von dort ausgehend können Sie die Implementierung für diese Rückruffunktion definieren.
7. Klicken Sie in der oberen rechten Ecke auf **Speichern**, um Ihre Änderungen zu speichern und diese Vorlage zu erstellen.

Beispielcode für die Rückruffunktion *fnResolveTimeOfDayTemplate*. Diese Funktion gibt die Zeichenfolge zurück, die mit dem in der Antwort angegebenen **Condition name** „Morgen“ oder „Abend“ übereinstimmt.

```javascript
module.exports.fnTimeOfDayTemplate = function(context) {
    var currentTime = new Date().getHours();
    if(currentTime >= 12) {
        return "evening!";
    } else {
        return "morning!";
    }
}
```

## <a name="send-a-response-to-user"></a>Senden einer Antwort an einen Benutzer

Um mithilfe von Antwortvorlagen eine Antwort an einen Benutzer zu senden, schließen Sie die Namen der Vorlagen in eckige Klammern ein. Um beispielsweise die einfache Antwortvorlage „AckPhrase“ in einem Feedbackzustand zu verwenden, geben Sie die Formulierung **Bot's response to user** (Antwort des Bots an den Benutzer) als `[AckPhrase], I will get that done for you right away` zurück.

Wenn Sie in Ihren Antworten eckige Klammern verwenden möchten, verwenden Sie "\" als Escapezeichen, um die Unterhaltungsruntime anzuweisen, die Auflösung für eckige Klammern zu überspringen.

Wenn Sie Entitäten definiert haben, können Sie diese ebenfalls in Ihrer Antwort an Benutzer verwenden. Sie können bei der Sprachgenerierung auf Entitäten verweisen, indem Sie den Namen der Entität in geschweifte Klammern einschließen. Wenn Ihr Bot beispielsweise eine `location`-Entität aufweist, können Sie in Ihren Antworten wie folgt auf sie verweisen: `[AckPhrase], I can help you find a table at {location}.`

## <a name="nesting-response-templates"></a>Verschachteln von Antwortvorlagen

Antwortvorlagen können „verschachtelt“ werden – eine Antwortvorlage kann Verweise auf eine andere Antwortvorlage enthalten. Beispielsweise können Sie die einfache Antwortvorlage `AckPhrase` und die bedingte Antwortvorlage `timeOfDayTemplate` verwenden, um eine neue Antwortvorlage `timeBasedGreeting` zu erstellen, die beide Vorlagen verwendet, um die endgültige Antwort aufzulösen. 

> [!TIP]
> Das Verschachteln von Vorlagen stellt zwar ein leistungsstarkes Feature dar, achten Sie aber darauf, durch das Verschachteln von Vorlagen keine Endlosschleife zu verursachen. Vermeiden Sie also Situationen, in denen `AckPhrase` `timeOfDayTemplate` und `timeOfDayTemplate` umgekehrt wieder `AckPhrase` aufruft.

Erstellen Sie beispielsweise einen neuen **Simple response name** (Name der einfachen Antwort) `timeBasedGreeting`, und geben Sie den folgenden Text als eine mögliche **Bot's response to user** (Antwort des Bots an den Benutzer) für diese Vorlage an: `[timeOfDayTemplate] [AckPhrase], ... `

## <a name="define-user-utterance-as-help-tips"></a>Definieren von Benutzeräußerungen als Hilfetipps

Wenn Sie **Äußerungen** von Benutzern definieren, können Sie auch eine Äußerung als **Hilfetipp** festlegen. Um eine Äußerung als einen **Hilfetipp** festzulegen, klicken Sie auf `...` rechts neben der Äußerung, und wählen Sie **Use as help tip** (Als Hilfetipp verwenden) aus. 

Der Hilfetipp wird in der integrierten Sprachgenerierungsvorlage `[builtin-tasktips]` überall da verwendet, wo Sie ein Feld **Bot's response to user** (Antwort des Bots an den Benutzer) definieren. Beispielsweise können Sie eine Antwort mit diesem Wortlaut verfassen: `Sorry I did not understand that. Here are some things you can try - [builtin.tasktips]`

Wenn für eine Aufgabe kein **Hilfetipp** definiert ist, wird `[builtin-tasktips]` zu einer leeren Zeichenfolge aufgelöst. Wenn für eine Aufgabe mehrere **Hilfetipps** definiert sind, wählt `[builtin-tasktips]` bei jedem Geben einer Antwort zufällig einen aus.

## <a name="next-step"></a>Nächster Schritt
> [!div class="nextstepaction"]
> [Adaptive Karten](conversation-designer-adaptive-cards.md)
