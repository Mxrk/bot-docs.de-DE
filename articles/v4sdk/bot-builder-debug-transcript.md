---
title: Debuggen Ihres Bots mit Transkriptdateien | Microsoft-Dokumentation
description: Es wird beschrieben, wie Sie eine Transkriptdatei zum Debuggen Ihres Bots verwenden.
keywords: Debuggen, häufig gestellte Fragen, Transkriptdatei, Emulator
author: DanDev33
ms.author: v-dashel
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservices: sdk
ms.date: 10/18/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: a7a8b7088e65d015f22438ee2050f97823b72e49
ms.sourcegitcommit: 49a76dd34d4c93c683cce6c2b8b156ce3f53280e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2018
ms.locfileid: "50134782"
---
# <a name="debug-your-bot-using-transcript-files"></a>Debuggen Ihres Bots mit Transkriptdateien
[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

Einer der Schlüssel für das erfolgreiche Testen und Debuggen eines Bots ist, dass Sie die Bedingungen aufzeichnen und untersuchen können, die beim Ausführen Ihres Bots herrschen. In diesem Artikel wird die Erstellung und Verwendung einer Bot-Transkriptdatei beschrieben, um einen ausführlichen Satz mit Benutzerinteraktionen und Bot-Antworten für das Testen und Debuggen bereitzustellen.

## <a name="the-bot-transcript-file"></a>Bot-Transkriptdatei
Eine Bot-Transkriptdatei ist eine spezielle JSON-Datei, mit der die Interaktionen zwischen einem Benutzer und Ihrem Bot beibehalten (gespeichert) werden. Mit einer Transkriptdatei wird nicht nur der Inhalt einer Nachricht beibehalten, sondern auch die Interaktionsdetails, z.B. Benutzer-ID, Kanal-ID, Kanaltyp, Kanalfunktionen, Zeitpunkt der Interaktion usw. Diese Informationen können dann verwendet werden, um beim Testen bzw. Debuggen Ihres Bots Probleme zu ermitteln und zu beheben. 

## <a name="creatingstoring-a-bot-transcript-file"></a>Erstellen/Speichern einer Bot-Transkriptdatei
In diesem Artikel wird veranschaulicht, wie Sie Bot-Transkriptdateien mit dem [Bot Framework Emulator](https://github.com/Microsoft/BotFramework-Emulator) von Microsoft erstellen. Transkriptdateien können auch programmgesteuert erstellt werden. Weitere Informationen zu diesem Ansatz finden Sie [hier](./bot-builder-howto-v4-storage.md#blob-transcript-storage). In diesem Artikel verwenden wir den Bot Framework-Beispielcode für [EchoBot with Counter](https://aka.ms/EchoBot-With-Counter) (EchoBot mit Zähler), der geändert wurde, um den Namen und die Telefonnummer eines Benutzers anzufordern. Zum Erstellen einer Transkriptdatei kann aber beliebiger Code genutzt werden, auf den mit dem Bot Framework Emulator von Microsoft zugegriffen werden kann.

Stellen Sie für diesen Prozess zunächst sicher, dass der zu testende Botcode in Ihrer Entwicklungsumgebung ausgeführt wird. Starten Sie den Bot Framework Emulator, wählen Sie die Schaltfläche _Open Bot_ (Bot öffnen), und verbinden Sie den Emulator mit der Datei für die _Botkonfiguration_ des Codes. Dies ist in der Abbildung unten dargestellt.

![Verbinden des Emulators mit Ihrem Code](./media/emulator_open_bot_configuration.png)

Nachdem Sie den Emulator mit Ihrem ausgeführten Code verbunden haben, können Sie den Code testen, indem Sie simulierte Benutzerinteraktionen an den Bot senden. Für dieses Beispiel haben wir den Namen und die Telefonnummer des Benutzers übergeben. Nachdem Sie alle Benutzerinteraktionen eingegeben haben, die Sie beibehalten möchten, können Sie den Bot Framework Emulator verwenden, um eine Transkriptdatei mit dieser Konversation zu erstellen und zu speichern. 

Wählen Sie auf der Registerkarte _Livechat_ (unten dargestellt) die Schaltfläche _Save transcript_ (Transkript speichern). 

![Auswählen von „Save transcript“ (Transkript speichern)](./media/emulator_transcript_save.png)

Wählen Sie einen Speicherort und einen Namen für Ihre Transkriptdatei und dann die Schaltfläche „Speichern“ aus.

![Transkript – Speichern unter – Ursula](./media/emulator_transcript_saveas_ursula.png)

Alle Benutzerinteraktionen und Bot-Antworten, die Sie zum Testen Ihres Codes mit dem Emulator eingegeben haben, wurden jetzt in einer Transkriptdatei gespeichert. Sie können diese Datei später erneut laden, um das Debuggen von Interaktionen zwischen einem Benutzer und Ihrem Bot zu debuggen.

## <a name="retrieving-a-bot-transcript-file"></a>Abrufen einer Bot-Transkriptdatei
Wählen Sie zum Abrufen einer Bot-Transkriptdatei mit dem Bot Framework Emulator oben links im Emulator im Abschnitt _RESOURCES_ (RESSOURCEN) das Listensteuerelement _TRANSCRIPTS_ (TRANSKRIPTS) wie unten dargestellt aus. Wählen Sie als Nächstes die Transkriptdatei aus, die Sie abrufen möchten. In diesem Beispiel rufen wir die Transkriptdatei mit dem Namen „Ursula_User_transcript“ ab. Beim Auswählen einer Transkriptdatei wird die gesamte gespeicherte Konversation automatisch in eine neue Registerkarte mit dem Namen _Transkript_ geladen.

![Abrufen des gespeicherten Transkripts](./media/emulator_transcript_retrieve.png)

## <a name="debug-using-transcript-file"></a>Debuggen mit der Transkriptdatei
Nachdem die Transkriptdatei geladen wurde, können Sie nun Interaktionen debuggen, die Sie zwischen einem Benutzer und Ihrem Bot erfasst haben. Klicken Sie hierzu einfach auf ein aufgezeichnetes Ereignis oder eine Aktivität, das bzw. die unten rechts im Emulator im Abschnitt _LOG_ (PROTOKOLL) angezeigt wird. Im Beispiel unten haben wir erste Interaktion des Benutzers ausgewählt, also das Senden der Nachricht „Hello“. Hierbei werden alle Informationen in Ihrer Transkriptdatei, die diese spezifische Interaktion betreffen, im JSON-Format im Fenster _INSPECTOR_ (INSPEKTOR) des Emulators angezeigt. Wenn wir uns einige dieser Werte von unten nach oben ansehen, erhalten wir folgende Informationen:
* Der Interaktionstyp lautet _message_.
* Der Zeitpunkt, zu dem die Nachricht gesendet wurde.
* Der gesendete Text enthält das Wort „Hello“.
* Die Nachricht wurde an unseren Bot gesendet.
* Benutzer-ID und Informationen
* Kanal-ID, Funktionen und Informationen

![Debuggen per Transkript](./media/emulator_transcript_debug.png)

Dank dieser ausführlichen Informationen können Sie die Interaktionen zwischen der Eingabe des Benutzers und der Antwort Ihres Bots Schritt für Schritt nachvollziehen. Dies ist für das Debuggen in Situationen hilfreich, in denen Ihr Bot entweder nicht auf die erwartete Weise oder gar nicht antwortet. Wenn Sie über beide Werte sowie eine Aufzeichnung der Schritte bis zur fehlerhaften Interaktion verfügen, können Sie den Code schrittweise durchlaufen, die Stelle mit der unerwarteten Antwort finden und die Probleme beheben.

Die Nutzung von Transkriptdateien zusammen mit dem Bot Framework Emulator ist nur einer der vielen Tool-Ansätze, die Sie verwenden können, um den Code und die Benutzerinteraktionen Ihres Bots zu testen und zu debuggen. Weitere Möglichkeiten zum Testen und Debuggen Ihres Bots finden Sie unten in den zusätzlichen Ressourcen.

## <a name="additional-resources"></a>Zusätzliche Ressourcen
Weitere Informationen zum Testen und Debuggen finden Sie unter:
* [Richtlinien für das Testen und Debuggen](./bot-builder-testing-debugging.md)
* [Debuggen mit dem Bot Framework Emulator](../bot-service-debug-emulator.md)
* [Debuggen in Visual Studio](https://docs.microsoft.com/en-us/visualstudio/debugger/index)


