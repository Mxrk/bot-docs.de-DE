---
title: Problembehandlung bei Bots | Microsoft-Dokumentation
description: Beheben Sie allgemeine Probleme bei der Bot-Entwicklung anhand häufig gestellter technischer Fragen.
author: DeniseMak
ms.author: v-demak
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 09/26/2018
ms.openlocfilehash: 34a23910c76a22fe39d1ce5457bb74dd285ca939
ms.sourcegitcommit: b15cf37afc4f57d13ca6636d4227433809562f8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/11/2019
ms.locfileid: "54225518"
---
# <a name="troubleshooting-general-problems"></a>Behandeln allgemeiner Probleme
Mithilfe dieser häufig gestellten Fragen können Sie allgemeine Probleme bei der Entwicklung oder dem Betrieb von Bots beheben.

## <a name="how-can-i-troubleshoot-issues-with-my-bot"></a>Wie kann ich Probleme mit meinem Bot beheben?

1. Debuggen Sie den Quellcode des Bots mit [Visual Studio Code](debug-bots-locally-vscode.md) oder [Visual Studio](https://docs.microsoft.com/en-us/visualstudio/debugger/navigating-through-code-with-the-debugger?view=vs-2017).
2. Testen Sie den Bot mit dem [Emulator](bot-service-debug-emulator.md), bevor Sie ihn in der Cloud bereitstellen.
3. Stellen Sie den Bot auf einer Cloudhostingplattform wie Azure bereit, und testen Sie dann die Verbindung mit dem Bot mithilfe des integrierten Webchat-Steuerelements auf dem Dashboard Ihres Bots im <a href="https://dev.botframework.com" target="_blank">Bot Framework-Portal</a>. Wenn für Ihren Bot nach der Bereitstellung in Azure Probleme auftreten, hilft Ihnen ggf. dieser Blogartikel weiter: [Understanding Azure troubleshooting and support](https://azure.microsoft.com/en-us/blog/understanding-azure-troubleshooting-and-support/) (Grundlegendes zu Azure-Problembehandlung und -Support).
4. Schließen Sie die [Authentifizierung][TroubleshootingAuth] als mögliches Problem aus.
5. Testen Sie den Bot auf Skype. Dadurch können Sie die End-to-End-Benutzererfahrung überprüfen.
6. Es empfiehlt sich, den Bot auf Kanälen zu testen, die zusätzliche Authentifizierungsanforderungen aufweisen, z.B. Direct Line oder Web Chat.

## <a name="how-can-i-troubleshoot-authentication-issues"></a>Wie kann ich Probleme bei der Authentifizierung beheben?

Ausführliche Informationen zur Behandlung von Authentifizierungsproblemen für Ihren Bot finden Sie unter [Problembehandlung bei der Bot Framework-Authentifizierung][TroubleshootingAuth].

## <a name="im-using-the-bot-framework-sdk-for-net-how-can-i-troubleshoot-issues-with-my-bot"></a>Ich verwende das Bot Framework SDK für .NET. Wie kann ich Probleme mit meinem Bot beheben?

**Suchen Sie nach Ausnahmen.**  
Wechseln Sie in Visual Studio 2017 zu **Debuggen** > **Windows** > **Ausnahmeeinstellungen**. Aktivieren Sie im Fenster **Ausnahmeeinstellungen** das Kontrollkästchen **Unterbrechen bei Auslösen von** neben **Common Language Runtime-Ausnahmen**. Möglicherweise wird auch eine Diagnoseausgabe im Fenster „Ausgabe“ angezeigt, wenn ausgelöste oder nicht behandelte Ausnahmen vorliegen.

**Sehen Sie sich die Aufrufliste an.**  
In Visual Studio können Sie auswählen, ob Sie [Nur eigenen Code](https://msdn.microsoft.com/en-us/library/dn457346.aspx) debuggen oder nicht. Das Untersuchen der vollständigen Aufrufliste kann zusätzliche Einblicke in Probleme geben.

**Stellen Sie sicher, dass alle Dialogmethoden mit einem Plan zur Handhabung der nächsten Nachricht enden.**  
Alle Dialogschritte müssen in den nächsten Schritt des Wasserfalls einfließen oder den aktuellen Dialog beenden, damit er aus dem Stapel herausfällt. Wenn ein Schritt nicht korrekt verarbeitet wird, verläuft die Konversation nicht wie gewünscht. Weitere Informationen zu Dialogen finden Sie im Artikel zu den [Dialogkonzepten](v4sdk/bot-builder-concept-dialog.md).

## <a name="why-doesnt-the-typing-activity-do-anything"></a>Warum hat die Eingabeaktivität keine Wirkung?
Bei einigen Kanälen werden vorübergehende Aktualisierungen der Eingabe im Client nicht unterstützt.

## <a name="what-is-the-difference-between-the-connector-library-and-builder-library-in-the-sdk"></a>Was ist der Unterschied zwischen der Connector-Bibliothek und der Builder-Bibliothek im SDK?

Mit der Connector-Bibliothek wird die REST-API verfügbar gemacht. Die Builder-Bibliothek fügt das Programmiermodell für den Konversationsdialog sowie andere Funktionen wie Eingabeaufforderungen, Wasserfälle, Ketten und geführtes Ausfüllen von Formularen hinzu. Die Builder-Bibliothek bietet außerdem Zugriff auf Cognitive Services wie LUIS.

## <a name="what-causes-an-error-with-http-status-code-429-too-many-requests"></a>Was verursacht einen Fehler mit dem HTTP-Statuscode 429 „Zu viele Anforderungen“?

Eine Fehlerantwort mit dem HTTP-Statuscode 429 gibt an, dass zu viele Anforderungen in einem bestimmten Zeitraum ausgestellt wurden. Der Antworttext sollte eine Erläuterung des Problems enthalten und kann auch das erforderliche Mindestintervall zwischen Anforderungen angeben. Eine mögliche Ursache für diesen Fehler ist [ngrok](https://ngrok.com/). Wenn Sie einen kostenlosen Plan („Free“) verwenden und die Beschränkungen von ngrok erreichen, wechseln Sie zur Seite mit Preisen und Einschränkungen auf der entsprechenden Website, um weitere [Optionen](https://ngrok.com/product#pricing) anzuzeigen. 

## <a name="why-arent-my-bot-messages-getting-received-by-the-user"></a>Warum erhält der Benutzer meine Botnachrichten nicht?

Die in der Antwort generierte Nachrichtenaktivität muss richtig adressiert sein, da sie sonst nicht am gewünschten Ziel ankommt. In der überwiegenden Zahl der Fälle müssen Sie dies nicht explizit einrichten. Das SDK übernimmt die Adressierung der Nachrichtenaktivität für Sie. 

Das richtige Adressieren einer Aktivität bedeutet, dass die entsprechenden Details zum *Konversationsverweis* sowie die Details zum Absender und Empfänger vorhanden sind. Meist wird die Nachrichtenaktivität als Antwort auf eine eingehende Nachrichtenaktivität gesendet. Die Details für die Adressierung können daher der eingehenden Aktivität entnommen werden. 

Wenn Sie Ablaufverfolgungen oder Überwachungsprotokolle untersuchen, können Sie überprüfen, ob Ihre Nachrichten richtig adressiert sind. Falls nicht, können Sie in Ihrem Bot einen Breakpoint festlegen und nachsehen, wo die IDs für Ihre Nachricht festgelegt werden.

## <a name="how-can-i-run-background-tasks-in-aspnet"></a>Wie kann ich Hintergrundaufgaben in ASP.NET ausführen? 

In einigen Fällen empfiehlt es sich, eine asynchrone Aufgabe zu initiieren, die einige Sekunden wartet und dann einen Code zum Löschen des Benutzerprofils oder Zurücksetzen des Konversations-/Dialogzustands ausführt. Weitere Informationen zur Vorgehensweise finden Sie unter [Ausführen von Hintergrundaufgaben in ASP.NET](https://www.hanselman.com/blog/HowToRunBackgroundTasksInASPNET.aspx). Ziehen Sie vor allem die Verwendung von [HostingEnvironment.QueueBackgroundWorkItem](https://msdn.microsoft.com/en-us/library/dn636893(v=vs.110).aspx) in Betracht. 


## <a name="how-do-user-messages-relate-to-https-method-calls"></a>Welcher Zusammenhang besteht zwischen Benutzernachrichten und HTTPS-Methodenaufrufen?

Wenn der Benutzer eine Nachricht über einen Kanal sendet, gibt der Bot Framework-Webdienst eine HTTPS-POST-Anforderung an den Webdienstendpunkt des Bots aus. Der Bot kann auf diesem Kanal null, eine oder viele Nachrichten an den Benutzer zurücksenden, indem er eine separate HTTPS-POST-Anforderung an Bot Framework für jede Nachricht ausstellt, die er sendet.

## <a name="my-bot-is-slow-to-respond-to-the-first-message-it-receives-how-can-i-make-it-faster"></a>Mein Bot reagiert nur langsam auf die erste Nachricht, die er empfängt. Wie kann ich ihn schneller machen?

Bots sind Webdienste, und einige Hostingplattformen, einschließlich Azure, setzen den Dienst automatisch in den Energiesparmodus, wenn über einen bestimmten Zeitraum kein Datenverkehr empfangen wird. Wenn das bei Ihrem Bot der Fall ist, muss er beim nächsten Empfangen einer Nachricht ganz neu starten, wodurch er wesentlich langsamer reagiert als wenn er bereits ausgeführt würde.

Auf einigen Hostingplattformen können Sie den Dienst so konfigurieren, dass er nicht in den Energiesparmodus gesetzt wird. In Azure navigieren Sie dazu im [Azure-Portal](https://portal.azure.com) zu dem Dienst Ihres Bots und wählen **Anwendungseinstellungen** und dann **Immer aktiviert** aus. Diese Option steht in den meisten, aber nicht allen Diensttarifen zur Verfügung.

## <a name="how-can-i-guarantee-message-delivery-order"></a>Wie kann ich die Reihenfolge der Nachrichtenübermittlung sicherstellen?

Bot Framework behält die Nachrichtenreihenfolge so weit wie möglich bei. Wenn Sie z.B. Nachricht A senden und auf den Abschluss dieses HTTP-Vorgangs warten, bevor Sie einen weiteren HTTP-Vorgang zum Senden von Nachricht B initiieren, wird dies von Bot Framework automatisch so verstanden, dass Nachricht A der Nachricht B vorangestellt werden soll. Allerdings kann die Reihenfolge der Nachrichtenübermittlung im Allgemeinen nicht garantiert werden, da letztendlich der Kanal für die Nachrichtenübermittlung verantwortlich ist und Nachrichten neu anordnen kann. Um das Risiko einer Übermittlung von Nachrichten in der falschen Reihenfolge zu verringern, können Sie eine zeitliche Verzögerung zwischen Nachrichten implementieren.

## <a name="how-can-i-intercept-all-messages-between-the-user-and-my-bot"></a>Wie kann ich alle Nachrichten zwischen dem Benutzer und meinem Bot abfangen?

Mithilfe des Bot Framework SDK für .NET können Sie Implementierungen der `IPostToBot`- und `IBotToUser`-Schnittstellen zum `Autofac`-Abhängigkeitsinjektionscontainer bereitstellen. Mit dem Bot Framework SDK für beliebige Sprachen können Sie Middleware nahezu für denselben Zweck verwenden. Das [BotBuilder-Azure](https://github.com/Microsoft/BotBuilder-Azure)-Repository enthält C#- und Node.js-Bibliotheken, die diese Daten in einer Azure-Tabelle protokollieren.

## <a name="why-are-parts-of-my-message-text-being-dropped"></a>Warum werden Teile meines Nachrichtentexts gelöscht?

Bot Framework und viele Kanäle interpretiert Text so, als wäre er mit [Markdown](https://en.wikipedia.org/wiki/Markdown) formatiert. Überprüfen Sie, ob Ihr Text Zeichen enthält, die als Markdown-Syntax interpretiert werden könnten.

## <a name="how-can-i-support-multiple-bots-at-the-same-bot-service-endpoint"></a>Wie kann ich mehrere Bots am gleichen Bot-Dienstendpunkt unterstützen? 

Dieses [Beispiel](https://github.com/Microsoft/BotBuilder/issues/2258#issuecomment-280506334) zeigt, wie Sie `Conversation.Container` mit den richtigen `MicrosoftAppCredentials` konfigurieren und einen einfachen `MultiCredentialProvider` zum Authentifizieren mehrerer App-IDs und Kennwörter verwenden.

## <a name="identifiers"></a>Bezeichner

## <a name="how-do-identifiers-work-in-the-bot-framework"></a>Wie funktionieren Bezeichner in Bot Framework?

Ausführliche Informationen zu Bezeichnern in Bot Framework finden Sie in der [Anleitung zu Bezeichnern für Bot Framework][BotFrameworkIDGuide].

## <a name="how-can-i-get-access-to-the-user-id"></a>Wie erhalte ich Zugriff auf die Benutzer-ID?

SMS- und E-Mail-Nachrichten stellen die Benutzer-ID in Rohform in der Eigenschaft `from.Id` bereit. In Skype-Nachrichten enthält die `from.Id`-Eigenschaft eine eindeutige ID für den Benutzer, die sich von der Skype-ID des Benutzers unterscheidet. Wenn Sie eine Verbindung mit einem vorhandenen Konto herstellen müssen, können Sie eine Anmeldekarte verwenden und Ihren eigenen OAuth-Flow implementieren, um die Benutzer-ID mit der Benutzer-ID Ihres eigenen Diensts zu verbinden.

## <a name="why-are-my-facebook-user-names-not-showing-anymore"></a>Warum werden meine Facebook-Benutzernamen nicht mehr angezeigt?

Haben Sie Ihr Facebook-Kennwort geändert? Dadurch wird das Zugriffstoken ungültig, und Sie müssen die Konfigurationseinstellungen Ihres Bots für den Facebook Messenger-Kanal im <a href="https://dev.botframework.com" target="_blank">Bot Framework-Portal</a> aktualisieren.

## <a name="why-is-my-kik-bot-replying-im-sorry-i-cant-talk-right-now"></a>Warum gibt mein Kik-Bot als Antwort zurück, dass er jetzt nicht sprechen kann?

Für Bots in der Entwicklungsphase sind auf Kik 50 Abonnenten zulässig. Nachdem 50 eindeutige Benutzer mit Ihrem Bot interagiert haben, erhält jeder neue Benutzer, der mit Ihrem Bot zu chatten versucht, die Meldung, dass dieser jetzt nicht sprechen kann. Weitere Informationen finden Sie in der [Kik-Dokumentation](https://botsupport.kik.com/hc/en-us/articles/225764648-How-can-I-share-my-bot-with-Kik-users-while-in-development-).

## <a name="how-can-i-use-authenticated-services-from-my-bot"></a>Wie kann ich authentifizierte Dienste von meinem Bot aus verwenden?

Informationen zur Azure Active Directory-Authentifizierung finden Sie in den Artikeln zum Hinzufügen der Authentifizierung ([V3](https://docs.microsoft.com/en-us/azure/bot-service/bot-builder-tutorial-authentication?view=azure-bot-service-3.0&tabs=csharp) | [V4](https://docs.microsoft.com/en-us/azure/bot-service/bot-builder-tutorial-authentication?view=azure-bot-service-4.0&tabs=csharp)). 

> [!NOTE] 
> Wenn Sie Authentifizierungs- und Sicherheitsfunktionen zu Ihrem Bot hinzufügen, sollten Sie sicherstellen, dass die in Ihrem Code implementierten Muster den für Ihre Anwendung geeigneten Sicherheitsstandards entsprechen.

## <a name="how-can-i-limit-access-to-my-bot-to-a-pre-determined-list-of-users"></a>Wie kann ich den Zugriff auf meinen Bot auf eine vordefinierte Liste von Benutzern beschränken?

Einige Kanäle (z.B. SMS und E-Mail) stellen Adressen ohne Bereichseinschränkung bereit. In diesen Fällen enthalten Nachrichten vom Benutzer die Benutzer-ID in Rohform in der `from.Id`-Eigenschaft.

Andere Kanäle (z.B. Skype, Facebook und Slack) stellen entweder bereichs- oder mandantenbezogene Adressen auf eine Weise bereit, durch die verhindert wird, dass ein Bot die ID eines Benutzers vorab vorhersagen kann. In diesen Fällen müssen Sie den Benutzer über einen Anmeldelink oder einen gemeinsamen geheimen Schlüssel authentifizieren, um zu bestimmen, ob er zur Verwendung des Bots berechtigt ist.

## <a name="why-does-my-direct-line-11-conversation-start-over-after-every-message"></a>Warum beginnt meine Direct Line 1.1-Konversation nach jeder Nachricht neu?

Wenn Ihre Direct Line-Konversation nach jeder Nachricht neu zu beginnen scheint, liegt das wahrscheinlich daran, dass in Nachrichten, die von Ihrem Direct Line-Client an den Bot gesendet werden, die `from`-Eigenschaft nicht vorhanden oder `null` ist. Wenn ein Direct Line-Client eine Nachricht sendet, bei der die `from`-Eigenschaft entweder fehlt oder `null` ist, weist der Direct Line-Dienst automatisch eine ID zu, sodass jede vom Client gesendete Nachricht von einem neuen und anderen Benutzer zu stammen scheint.

Um dieses Problem zu beheben, legen Sie die `from`-Eigenschaft in jeder vom Direct Line-Client gesendeten Nachricht auf einen stabilen Wert fest, der den Benutzer, der die Nachricht sendet, eindeutig darstellt. Wenn sich beispielsweise ein Benutzer bereits bei einer Webseite oder App angemeldet hat, können Sie diese vorhandene Benutzer-ID als Wert für die `from`-Eigenschaft in Nachrichten verwenden, die der Benutzer sendet. Alternativ können Sie auch eine zufällige Benutzer-ID beim Laden der Seite oder der Anwendung generieren, diese ID in einem Cookie oder Gerätestatus speichern und diese ID als Wert für die `from`-Eigenschaft in Nachrichten verwenden, die der Benutzer sendet.

## <a name="what-causes-the-direct-line-30-service-to-respond-with-http-status-code-502-bad-gateway"></a>Warum antwortet der Direct Line 3.0-Dienst mit dem HTTP-Statuscode 502 „Ungültiges Gateway“?
Direct Line 3.0 gibt den HTTP-Statuscode 502 zurück, wenn der Dienst versucht, Kontakt mit Ihrem Bot aufzunehmen, die Anforderung aber nicht erfolgreich abgeschlossen wird. Dieser Fehler weist darauf hin, dass entweder der Bot einen Fehler zurückgeben hat oder bei der Anforderung eine Zeitüberschreitung aufgetreten ist. Zum Anzeigen weiterer Informationen zu Fehlern, die von Ihrem Bot generiert werden, wechseln Sie zum Dashboard des Bots im <a href="https://dev.botframework.com" target="_blank">Bot Framework-Portal</a>, und klicken Sie auf den Link „Probleme“ für den betreffenden Kanal. Wenn Sie Application Insights für Ihren Bot konfiguriert haben, können Sie auch dort ausführliche Informationen zu Fehlern finden. 

::: moniker range="azure-bot-service-3.0"

## <a name="what-is-the-idialogstackforward-method-in-the-bot-framework-sdk-for-net"></a>Was ist die IDialogStack.Forward-Methode im Bot Framework SDK für .NET?

Der Hauptzweck von `IDialogStack.Forward` besteht darin, einen vorhandenen untergeordneten Dialog wiederzuverwenden, der häufig „reaktiv“ ist, wobei der untergeordnete Dialog (in `IDialog.StartAsync`) auf ein Objekt `T` mit einem `ResumeAfter`-Handler wartet. Insbesondere wenn Sie einen untergeordneten Dialog haben, der auf eine `IMessageActivity` `T` wartet, können Sie die eingehende `IMessageActivity` (bereits von einem übergeordneten Dialog empfangen) mithilfe der `IDialogStack.Forward`-Methode weiterleiten. Wenn Sie beispielsweise eine eingehende `IMessageActivity` an einen `LuisDialog` weiterleiten möchten, rufen Sie `IDialogStack.Forward` auf, um den `LuisDialog` per Pushvorgang auf den Dialogstapel zu übertragen, führen Sie den Code in `LuisDialog.StartAsync` aus, bis ein Wartevorgang für die nächste Nachricht geplant ist, und erfüllen Sie dann sofort diesen Wartevorgang mit der weitergeleiteten `IMessageActivity`.

`T` ist in der Regel eine `IMessageActivity`, da `IDialog.StartAsync` normalerweise zum Warten auf diese Art von Aktivität ausgelegt ist. Sie können `IDialogStack.Forward` für einen `LuisDialog` als Mechanismus zum Abfangen von Nachrichten vom Benutzer für eine Verarbeitung vor dem Weiterleiten der Nachricht an einen vorhandenen `LuisDialog` verwenden. Alternativ können Sie auch `DispatchDialog` mit `ContinueToNextGroup` für diesen Zweck verwenden.

Sie können das weitergeleitete Element im ersten `ResumeAfter`-Handler (z.B. `LuisDialog.MessageReceived`) finden, der von `StartAsync` geplant ist.

::: moniker-end

## <a name="what-is-the-difference-between-proactive-and-reactive"></a>Was ist der Unterschied zwischen „proaktiv“ und „reaktiv“?

Aus Botperspektive bedeutet „reaktiv“, dass der Benutzer die Konversation einleitet, indem er eine Nachricht an den Bot sendet, und der Bot reagiert, indem er auf diese Nachricht antwortet. Im Gegensatz dazu bedeutet „proaktiv“, dass der Bot die Konversation einleitet, indem er die erste Nachricht an den Benutzer sendet. Ein Bot kann beispielsweise eine proaktive Nachricht senden, um einen Benutzer zu benachrichtigen, wenn ein Timer abläuft oder ein Ereignis auftritt.

## <a name="how-can-i-send-proactive-messages-to-the-user"></a>Wie kann ich proaktive Nachrichten an den Benutzer senden?

Beispiele für das Senden proaktiver Nachrichten finden Sie unter den [C# V4-Beispielen](https://github.com/Microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore/16.proactive-messages) und [Node.js V4-Beispielen](https://github.com/Microsoft/BotBuilder-Samples/tree/master/samples/javascript_nodejs/16.proactive-messages) im Repository „BotBuilder-Samples“ auf GitHub.

## <a name="how-can-i-reference-non-serializable-services-from-my-c-dialogs"></a>Wie kann ich von meinen C#-Dialogen auf nicht serialisierbare Dienste verweisen?

Es gibt mehrere Möglichkeiten:

* Heben Sie die Abhängigkeit über `Autofac` und `FiberModule.Key_DoNotSerialize` auf. Dies ist die sauberste Lösung.
* Verwenden Sie die Attribute [NonSerialized](https://msdn.microsoft.com/en-us/library/system.nonserializedattribute(v=vs.110).aspx) und [OnDeserialized](https://msdn.microsoft.com/en-us/library/system.runtime.serialization.ondeserializedattribute(v=vs.110).aspx), um die Abhängigkeit bei der Deserialisierung wiederherzustellen. Dies ist die einfachste Lösung.
* Speichern Sie diese Abhängigkeit nicht, damit sie nicht serialisiert wird. Diese Lösung ist zwar technisch möglich, wird aber nicht empfohlen.
* Verwenden Sie Reflektion als Serialisierungsersatz. Diese Lösung ist in einigen Fällen möglicherweise nicht umsetzbar und bedeutet ein zu großes Risiko für die Serialisierung.

::: moniker range="azure-bot-service-3.0"

## <a name="where-is-conversation-state-stored"></a>Wo wird der Konversationszustand gespeichert?

Daten in den Eigenschaftensammlungen für Benutzer, Konversation und private Konversation werden mithilfe der `IBotState`-Schnittstelle des Connectors gespeichert. Der Bereich jeder Eigenschaftensammlung ist durch die ID des Bots festgelegt. Die Eigenschaftensammlung für den Benutzer wird durch die Benutzer-ID bestimmt, die Eigenschaftensammlung für die Konversation wird durch die Konversations-ID bestimmt, und die Eigenschaftensammlung für die private Konversation wird sowohl durch die Benutzer-ID als auch die Konversations-ID bestimmt. 

Wenn Sie das Bot Framework SDK für .NET oder das Bot Framework SDK für Node.js zum Erstellen Ihres Bots verwenden, werden der Dialogstapel und Dialogdaten automatisch als Einträge in der Eigenschaftensammlung für die private Konversation gespeichert. Die C#-Implementierung verwendet die binäre Serialisierung, und die Node.js-Implementierung verwendet die JSON-Serialisierung.

Die REST-Schnittstelle `IBotState` wird durch zwei Dienste implementiert.

* Der Bot Framework-Connector stellt einen Clouddienst bereit, der diese Schnittstelle implementiert und Daten in Azure speichert.  Diese Daten werden im Ruhezustand verschlüsselt und laufen nicht absichtlich ab.
* Der Bot Framework-Emulator stellt eine In-Memory-Implementierung dieser Schnittstelle für das Debuggen Ihres Bots bereit. Diese Daten laufen ab, wenn der Emulatorprozess beendet wird.

Wenn Sie diese Daten in Ihren Rechenzentren speichern möchten, können Sie eine benutzerdefinierte Implementierung des State-Diensts bereitstellen. Dies kann auf mindestens zwei Arten erfolgen:

* Verwenden Sie die REST-Ebene zur Bereitstellung eines benutzerdefinierten `IBotState`-Diensts.
* Verwenden Sie die Builder-Schnittstellen auf der Sprachebene (Node.js oder C#).

> [!IMPORTANT]
> Die Bot Framework State-Dienst-API wird nicht für Produktionsumgebungen empfohlen und kann in einem zukünftigen Release veraltet sein. Es wird empfohlen, dass Sie Ihren Bot-Code aktualisieren, um den In-Memory-Speicher für Testzwecke zu nutzen, oder eine der **Azure-Erweiterungen** für Produktions-Bots verwenden. Weitere Informationen finden Sie unter dem Thema **Verwalten von Zustandsdaten** für die [.NET](~/dotnet/bot-builder-dotnet-state.md)- bzw. [Node.js](~/nodejs/bot-builder-nodejs-state.md)-Implementierung.

::: moniker-end

## <a name="what-is-an-etag--how-does-it-relate-to-bot-data-bag-storage"></a>Was ist ein ETag?  In welcher Beziehung steht es zur Speicherung in Botdatenbehältern?

Ein [ETag](https://en.wikipedia.org/wiki/HTTP_ETag) ist ein Mechanismus zur [Steuerung für optimistische Parallelität](https://en.wikipedia.org/wiki/Optimistic_concurrency_control). Bei der Speicherung in Botdatenbehältern werden ETags verwendet, um Aktualisierungskonflikte bei Daten zu verhindern. Ein ETag-Fehler mit dem HTTP-Statuscode 412 „Fehler bei der Vorbedingung“ deutet darauf hin, dass für diesen Botdatenbehälter mehrere „Lesen-Ändern-Schreiben“-Sequenzen gleichzeitig ausgeführt wurden.

Der Dialogstapel und -zustand werden in Botdatenbehältern gespeichert. Möglicherweise wird die ETag-Fehlermeldung „Fehler bei der Vorbedingung“ "angezeigt, wenn Ihr Bot noch immer eine vorherige Nachricht verarbeitet, während er eine neue Nachricht für diese Konversation empfängt.

## <a name="what-causes-an-error-with-http-status-code-412-precondition-failed-or-http-status-code-409-conflict"></a>Was verursacht einen Fehler mit dem HTTP-Statuscode 412 „Fehler bei der Vorbedingung“ oder HTTP-Statuscode 409 „Konflikt“?

Der `IBotState`-Dienst des Connectors wird zum Speichern der Botdatenbehälter verwendet (d.h. die Botdatenbehälter für Benutzer, Konversation und private Konversation, wobei der Botdatenbehälter für private Konversation den Zustand „Ablaufsteuerung“ für den Dialogstapel umfasst). Die Steuerung der Parallelität im `IBotState`-Dienst wird durch optimistische Parallelität über ETags verwaltet. Wenn während einer „Lesen-Ändern-Schreiben“-Sequenz ein Aktualisierungskonflikt auftritt (aufgrund einer gleichzeitigen Aktualisierung eines einzelnen Botdatenbehälters), geschieht Folgendes:

* Wenn ETags beibehalten werden, wird ein Fehler mit dem HTTP-Statuscode 412 „Fehler bei der Vorbedingung“ vom `IBotState`-Dienst ausgelöst. Dies ist das Standardverhalten im Bot Framework SDK für .NET.
* Wenn ETags nicht beibehalten werden (ETag ist auf `\*` festgelegt), gilt die Richtlinie, dass der letzte Schreibzugriff Priorität hat, wodurch „Fehler bei der Vorbedingung“ verhindert wird, jedoch das Risiko eines Datenverlusts besteht. Dies ist das Standardverhalten im Bot Framework SDK für Node.js.

## <a name="how-can-i-fix-precondition-failed-412-or-conflict-409-errors"></a>Wie kann ich Fehler vom Typ „Fehler bei der Vorbedingung“ (412) oder „Konflikt“ (409) beheben?

Diese Fehler weisen darauf hin, dass der Bot mehrere Nachrichten für dieselbe Konversation gleichzeitig verarbeitet hat. Wenn der Bot mit Diensten verbunden ist, die genau geordnete Nachrichten erfordern, empfiehlt es sich, den Konversationszustand zu sperren, um sicherzustellen, dass Nachrichten nicht parallel verarbeitet werden. 

::: moniker range="azure-bot-service-3.0"

Das Bot Framework SDK für .NET bietet einen Mechanismus (Klasse `LocalMutualExclusion`, die `IScope` implementiert) zum pessimistischen Serialisieren der Handhabung einer einzelnen Konversation mit einem In-Memory-Semaphor. Sie könnten diese Implementierung auf die Verwendung eines Redis-Lease erweitern, dessen Bereich durch die Adresse der Konversation festgelegt ist.

Wenn der Bot nicht mit externen Diensten verbunden ist oder die parallele Verarbeitung von Nachrichten aus derselben Konversation akzeptabel ist, können Sie den folgenden Code hinzufügen, um alle Konflikte zu ignorieren, die in der Bot State-API auftreten. Dadurch kann der Konversationszustand durch die letzte Antwort festgelegt werden.

```cs
var builder = new ContainerBuilder();
builder
    .Register(c => new CachingBotDataStore(c.Resolve<ConnectorStore>(), CachingBotDataStoreConsistencyPolicy.LastWriteWins))
    .As<IBotDataStore<BotData>>()
    .AsSelf()
    .InstancePerLifetimeScope();
builder.Update(Conversation.Container);
```
::: moniker-end

## <a name="is-there-a-limit-on-the-amount-of-data-i-can-store-using-the-state-api"></a>Gibt es einen Grenzwert für die Datenmenge, die über die State-API gespeichert werden kann?

Ja. Jeder Zustandsspeicher (d.h. Botdatenbehälter für Benutzer, Konversation und private Konversation) kann bis zu 64 KB Daten enthalten. Weitere Informationen finden Sie unter [Verwalten von Zustandsdaten][StateAPI].

::: moniker range="azure-bot-service-3.0"

## <a name="how-do-i-version-the-bot-data-stored-through-the-state-api"></a>Wie erstelle ich Versionen für die über die State-API gespeicherten Botdaten?

> [!IMPORTANT]
> Die Bot Framework-Zustandsdienst-API wird nicht für Produktionsumgebungen oder v4-Bots empfohlen und kann in einem zukünftigen Release vollständig veraltet sein. Es wird empfohlen, dass Sie Ihren Bot-Code aktualisieren, um den In-Memory-Speicher für Testzwecke zu nutzen, oder eine der **Azure-Erweiterungen** für Produktions-Bots verwenden. Weitere Informationen finden Sie unter dem Thema [Verwalten von Zustandsdaten](v4sdk/bot-builder-howto-v4-state.md).

Mit dem State-Dienst können Sie den Fortschritt über die Dialoge in einer Konversation speichern, sodass ein Benutzer zu einem späteren Zeitpunkt zu einer Konversation mit einem Bot zurückkehren kann, ohne die Position zu verlieren. Zu diesem Zweck werden die Eigenschaftensammlungen für Botdaten, die über die State-API gespeichert werden, nicht automatisch gelöscht, wenn Sie den Code des Bots ändern. Sie müssen entscheiden, ob die Botdaten gelöscht werden sollen oder nicht, und zwar abhängig davon, ob der geänderte Code mit älteren Versionen der Daten kompatibel ist. 

* Wenn Sie den Dialogstapel und -zustand der Konversation während der Entwicklung Ihres Bots manuell zurücksetzen möchten, können Sie den Befehl ` /deleteprofile` zum Löschen von Zustandsdaten verwenden. Vergewissern Sie sich, dass Sie das führende Leerzeichen in diesen Befehl einfügen, um eine Interpretation durch den Kanal zu verhindern.
* Nachdem Ihr Bot in der Produktionsumgebung bereitgestellt wurde, können Sie Versionen Ihrer Botdaten erstellen, damit bei einer Änderung der Version die zugehörigen Zustandsdaten gelöscht werden. Beim Bot Framework SDK für Node.js kann dies mithilfe von Middleware erreicht werden, und beim Bot Framework SDK für .NET ist dies mithilfe einer `IPostToBot`-Implementierung möglich.

> [!NOTE]
> Wenn der Dialogstapel aufgrund von Änderungen des Serialisierungsformats oder zu weitreichender Änderungen des Codes nicht ordnungsgemäß deserialisiert werden kann, wird der Konversationszustand zurückgesetzt.

::: moniker-end

## <a name="what-are-the-possible-machine-readable-resolutions-of-the-luis-built-in-date-time-duration-and-set-entities"></a>Was sind mögliche maschinenlesbare Lösungen der in LUIS integrierten Entitäten für Datum, Uhrzeit, Dauer und Satz?

Eine Liste von Beispielen finden Sie im [Abschnitt zu integrierten Entitäten][LUISPreBuiltEntities] in der LUIS-Dokumentation.

## <a name="how-can-i-use-more-than-the-maximum-number-of-luis-intents"></a>Wie kann ich mehr als die maximale Anzahl von LUIS-Absichten verwenden?

Sie können überlegen, ob Sie Ihr Modell aufteilen und den LUIS-Dienst nacheinander oder parallel aufrufen.

## <a name="how-can-i-use-more-than-one-luis-model"></a>Wie kann ich mehr als ein LUIS-Modell verwenden?

Sowohl das Bot Framework SDK für Node.js als auch das Bot Framework SDK für .NET unterstützen das Aufrufen mehrerer LUIS-Modelle aus einem einzigen LUIS-Absichtsdialog. Beachten Sie dabei die folgenden Einschränkungen:

* Die Verwendung mehrerer LUIS-Modelle setzt voraus, dass die LUIS-Modelle nicht überlappende Sätze von Absichten aufweisen.
* Die Verwendung mehrerer LUIS-Modelle setzt voraus, dass die Bewertungen aus verschiedenen Modellen vergleichbar sind, um die „am besten übereinstimmende Absicht“ bei mehreren Modellen auszuwählen.
* Die Verwendung mehrerer LUIS-Modelle bedeutet, dass bei Übereinstimmung einer Absicht mit einem Modell, auch die Absichten vom Typ „None“ (Keine) der anderen Modelle abgeglichen werden. In diesem Fall können Sie die Auswahl der Absicht vom Typ „None“ verhindern. Das Bot Framework SDK für Node.js skaliert die Bewertung für „None“ automatisch zentral herunter, um dieses Problem zu vermeiden.

## <a name="where-can-i-get-more-help-on-luis"></a>Wo finde ich weitere Hilfe zu LUIS?

* [Einführung in Language Understanding (LUIS) – Microsoft Cognitive Services](https://www.youtube.com/watch?v=jWeLajon9M8) (Video)
* [Erweiterte Lernsitzung für Language Understanding (LUIS) ](https://www.youtube.com/watch?v=39L0Gv2EcSk) (Video)
* [LUIS-Dokumentation](/azure/cognitive-services/LUIS/Home)
* [Language Understanding-Forum](https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=LUIS) 


## <a name="what-are-some-community-authored-dialogs"></a>Gibt es von der Community erstellte Dialoge?

* [BotAuth](https://www.nuget.org/packages/BotAuth) – -Azure Active Directory-Authentifizierung
* [BestMatchDialog](http://www.garypretty.co.uk/2016/08/01/bestmatchdialog-for-microsoft-bot-framework-now-available-via-nuget/) – Auf regulären Ausdrücken basierende Ausgabe von Benutzertext an Dialogmethoden

## <a name="what-are-some-community-authored-templates"></a>Gibt es von der Community erstellte Vorlagen?

* [ES6 BotBuilder](https://github.com/brene/botbuilder-es6-template) – ES6 Bot Builder-Vorlage

## <a name="why-do-i-get-an-authorizationrequestdenied-exception-when-creating-a-bot"></a>Warum erhalte ich beim Erstellen eines Bots eine Ausnahme vom Typ „Authorization_RequestDenied“?

Die Berechtigungen zum Erstellen von Azure Bot Service-Bots werden über das Azure Active Directory (AAD)-Portal verwaltet. Wenn Berechtigungen im [AAD-Portal](http://aad.portal.azure.com) nicht ordnungsgemäß konfiguriert sind, erhalten Benutzer bei dem Versuch, einen Botdienst zu erstellen, die Ausnahme **Authorization_RequestDenied**.

Überprüfen Sie zunächst, ob Sie ein „Gast“ von Active Directory sind:

1. Melden Sie sich beim [Azure-Portal](http://portal.azure.com) an.
2. Klicken Sie auf **Alle Dienste**, und suchen Sie nach *aktiv*.
3. Wählen Sie **Azure Active Directory**.
4. Klicken Sie auf **Users**.
5. Suchen Sie den Benutzer in der Liste, und stellen Sie sicher, dass der **Benutzertyp** nicht **Gast** lautet.

![Azure Active Directory-Benutzertyp](~/media/azure-active-directory/user_type.png)

Nachdem Sie sich vergewissert haben, dass Sie kein **Gast** sind, muss der AAD-Administrator die folgenden Einstellungen konfigurieren, um sicherzustellen, dass Benutzer in einem Active Directory Botdienste erstellen können:

1. Melden Sie sich beim [AAD-Portal](http://aad.portal.azure.com) an. Wechseln Sie zu **Benutzer und Gruppen**, und wählen Sie **Benutzereinstellungen** aus.
2. Legen Sie im Abschnitt **App-Registrierung** die Option **Benutzer können Anwendungen registrieren** auf **Ja** fest. Dadurch können Benutzer in Ihrem Active Directory Botdienste erstellen.
3. Setzen Sie im Abschnitt **Externe Benutzer** die Option **Berechtigungen für Gastbenutzer sind eingeschränkt** auf **Nein**. Dadurch können Gastbenutzer in Ihrem Active Directory Botdienste erstellen.

![Azure Active Directory Admin Center](~/media/azure-active-directory/admin_center.png)

## <a name="why-cant-i-migrate-my-bot"></a>Warum kann ich meinen Bot nicht migrieren?

Wenn beim Migrieren Ihres Bots Probleme auftreten, kann der Grund dafür sein, dass der Bot einem anderen Verzeichnis als dem Standardverzeichnis angehört. Führen Sie die folgenden Schritte aus:

1. Fügen Sie im Zielverzeichnis einen neuen Benutzer hinzu (über die E-Mail-Adresse), der kein Mitglied des Standardverzeichnisses ist, und weisen Sie dem Benutzer die Rolle „Mitwirkender“ für die Abonnements zu, die das Ziel der Migration sind.

2. Fügen Sie im [Entwicklerportal](https://dev.botframework.com) die E-Mail-Adresse des Benutzers als Mitbesitzer des zu migrierenden Bots hinzu. Melden Sie sich anschließend ab.

3. Melden Sie sich beim [Entwicklerportal](https://dev.botframework.com) als der neue Benutzer an, und fahren Sie mit der Migration des Bots fort.

## <a name="where-can-i-get-more-help"></a>Wo finde ich weitere Hilfe?

* Nutzen Sie die Informationen in den bereits beantworteten Fragen auf [Stack Overflow](https://stackoverflow.com/questions/tagged/botframework), oder stellen Sie eigene Fragen unter Verwendung des `botframework`-Tags. Beachten Sie, dass es bei Stack Overflow Richtlinien gibt. So sind beispielsweise ein beschreibender Titel, eine umfassende und präzise Erläuterung des Problems sowie ausreichende Informationen zur Reproduktion Ihres Problems erforderlich. Featureanforderungen oder zu weit gefasste Fragen sind nicht angemessen. Neue Benutzer sollten sich die Informationen im [Stack Overflow-Hilfecenter](https://stackoverflow.com/help/how-to-ask) ansehen.
* Informationen zu bekannten Problemen mit dem Bot Framework SDK finden Sie in den [BotBuilder-Problemen](https://github.com/Microsoft/BotBuilder/issues) auf GitHub. Dort können Sie auch ein neues Problem melden.
* Nutzen Sie die Informationen in der Diskussion der BotBuilder-Community auf [Gitter](https://gitter.im/Microsoft/BotBuilder).




[LUISPreBuiltEntities]: /azure/cognitive-services/luis/pre-builtentities
[BotFrameworkIDGuide]: bot-service-resources-identifiers-guide.md
[StateAPI]: ~/rest-api/bot-framework-rest-state.md
[TroubleshootingAuth]: bot-service-troubleshoot-authentication-problems.md

