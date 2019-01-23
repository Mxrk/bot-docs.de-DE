---
title: Medienanrufe in Echtzeit mit Skype | Microsoft-Dokumentation
description: Informationen zu wichtigen Konzepten zum Erstellen eines Bots mithilfe des Bot Framework SDK für .NET, der in Echtzeit Audio- und Videoanrufe mit Skype durchführen kann
author: ssulzer
ms.author: ssulzer
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 893458a484c0e26545c23016ccbf3049adc61960
ms.sourcegitcommit: b15cf37afc4f57d13ca6636d4227433809562f8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/11/2019
ms.locfileid: "54225255"
---
# <a name="real-time-media-calling-with-skype"></a>Medienanrufe in Echtzeit mit Skype

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

Mit der Real-Time Media Platform for Bots bekommt die Art, in der Bots mit Benutzern interagieren können, durch Aktivierung von Modalitäten zur Sprach-, Video- und Bildschirmübertragung in Echtzeit eine neue Dimension. Dies bietet die Möglichkeit, faszinierende, interaktive Unterhaltungs-, Bildungs- und Assistenzbots zu erstellen. Benutzer kommunizieren über Skype mit Echtzeit-Medienbots.

Dies ist eine erweiterte Funktion, die dem Bot ermöglicht, Sprach- und Videoinhalt *Frame für Frame* zu senden und zu empfangen. Der Bot hat Rohdatenzugriff auf die Modalitäten zur Sprach-, Video- und Bildschirmübertragung in Echtzeit. Wenn der Benutzer spricht, empfängt der Bot beispielsweise 50 Audioframes pro Sekunde, wobei jeder Frame 20 Millisekunden (ms) Audioinhalt enthält. Der Bot kann während des Empfangs der Audioframes in Echtzeit eine Spracherkennung ausführen und muss nicht auf eine Aufzeichnung warten, nachdem der Benutzer gesprochen hat. Außerdem kann der Bot auch hochauflösende Videos mit 30 Frames pro Sekunde zusammen mit Audioinhalt an den Benutzer senden.

Die Real-Time Media Platform for Bots nimmt gemeinsam mit Skype Calling Cloud die Anrufkonfiguration und Einrichtung einer Mediensitzung vor, sodass der Bot eine Sprach- und (optional) eine Videounterhaltung mit einem Skype-Anrufer durchführen kann. Die Plattform bietet eine einfache „Socket“-ähnliche API, über die der Bot Medien senden und empfangen kann, und übernimmt die Codierung und Decodierung von Medien in Echtzeit mithilfe von Codecs wie z.B. SILK für Audio und H.264 für Video. Ein Echtzeit-Medienbot kann auch an Skype-Gruppen-Videoanrufen mit mehreren Teilnehmern teilnehmen.

In diesem Artikel werden wichtige Konzepte im Zusammenhang mit dem Erstellen eines Bots vorgestellt, der in Echtzeit Audio- und Videoanrufe mit Skype durchführen kann, und er enthält Links zu relevanten Entwicklerressourcen.

## <a name="media-session"></a>Mediensitzung
Wenn ein Echtzeit-Medienbot einen eingehenden Skype-Anruf annimmt, entscheidet er, ob sowohl Audio- als auch Videomodalität oder nur die Audiomodalität unterstützt werden sollen. Für jede unterstützte Modalität kann der Bot entweder Medien sowohl senden als auch empfangen, nur empfangen oder nur senden. Beispielsweise möchte ein Bot vielleicht Audioinhalt sowohl senden als auch empfangen, Videoinhalt jedoch nur senden (weil er das Video des Skype-Anrufers nicht empfangen möchte). Der Satz der zwischen dem Skype-Anrufer und dem Bot eingerichteten Audio- und Videomodalitäten wird als **Mediensitzung** bezeichnet.

Zwei Videomodalitäten werden unterstützt: „Hauptvideo“ und „Bildschirmübertragung“. Mit „Hauptvideo“ wird das Video, das der Bot generiert oder wiedergibt, an den Anrufer übertragen, und das Video des Anrufers (in der Regel von der Webkamera des Benutzers) an den Bot. Die Modalität „Bildschirmübertragung“ ermöglicht dem Anrufer, seinen Bildschirm (als Video) für den Bot freizugeben. Der Bot kann kein Bildschirmübertragungsvideo an den Benutzer senden.

Bei Teilnahme an einem Skype-**Gruppen-Videoanruf** mit mehreren Teilnehmern kann der Echtzeit-Medienbot den gleichzeitigen Empfang mehrerer Hauptvideostreams unterstützen. So kann der Bot mehr „sehen“ als ein Teilnehmer des Gruppen-Videoanrufs.

## <a name="frames-and-frame-rate"></a>Frames und Bildfrequenz
Ein Echtzeit-Medienbot interagiert direkt mit den Audio- und Videomodalitäten eines Skype-Anrufs. Dies bedeutet, dass der Bot Medien als Sequenz von **Frames** sendet und/oder empfängt, wobei jeder Frame eine Inhaltseinheit darstellt. Eine Sekunde Audio kann als eine Sequenz von 50 Frames übertragen werden, wobei jeder Frame 20 Millisekunden (ms) (1/50 Sekunde) Inhalt hat. Eine Sekunde Video kann in eine Sequenz von 30 Standbildern aufgeteilt werden, die jeweils für nur 33ms (1/30 Sekunde) angezeigt werden – anschließend wird der nächste Videoframe angezeigt. Die Anzahl der pro Sekunde übertragenen bzw. gerenderten Frames wird als **Bildfrequenz** bezeichnet. „30Bps“ gibt 30 Frames pro Sekunde an.

## <a name="audio-format"></a>Audioformat
Jede Audiosekunde wird in 16.000 **Samples** dargestellt, wobei jedes Sample 16 Datenbits speichert. Ein Audioframe von 20ms enthält 320 Samples (640 Datenbytes).

## <a name="video-format"></a>Videoformat
Verschiedene Videoformate werden unterstützt. Zwei wichtige Eigenschaften eines Videoformats sind **Framegröße** und **Farbformat**. Die Framegrößen 640x360 („360p“), 1280x720 („720p“) und 1920x1080 („1080p“) werden unterstützt. Die Farbformate NV12 (12 Bits pro Pixel) und RGB24 (24 Bits pro Pixel) werden unterstützt.

Ein „720p“-Videoframe enthält 921.600 Pixel (1.280 mal 720). Im Farbformat RGB24 wird jedes Pixel mit 3 Bytes (24-Bit) dargestellt, jeweils ein Byte für die rote, grüne und blaue Farbkomponente. Daher benötigt ein einzelner 720p-RGB24-Videoframe 2.764.800 Datenbytes (921.600 Pixel mal 3 Bytes). Bei einer Bildfrequenz von 30fps müssen beim Senden von 720p-RGB24-Videoframes ca. 80 MB/s Inhalt verarbeitet werden (die im Wesentlichen vor der Netzwerkübertragung vom H.264-Videocodec komprimiert werden).

## <a name="active-and-dominant-speakers"></a>Aktive und dominante Sprecher
Bei Teilnahme an einem Skype-Gruppen-Videoanruf mit mehreren Teilnehmern kann der Bot den derzeit sprechenden Teilnehmer identifizieren. **Aktive Sprecher** erkennt in jedem empfangenen Audioframe, welche Teilnehmer zu hören sind. **Dominante Sprecher** erkennt, welche Teilnehmer derzeit in der Gruppenunterhaltung am aktivsten sind (d.h. „dominant“), obwohl ihre Stimmen nicht unbedingt in jedem Audioframe hörbar sind. Der Satz dominanter Sprecher kann sich ändern, wenn verschiedene Beteiligte abwechselnd sprechen.

## <a name="video-subscription"></a>Videoabonnement
In einem Anruf mit einem einzelnen Skype-Anrufer empfängt der Bot automatisch das Video des Anrufers (wenn der Hauptvideoempfang für den Bot aktiviert ist). In einem Skype-Gruppen-Videoanruf mit mehreren Teilnehmern muss der Bot der Echtzeit-Medienplattform mitteilen, welche Teilnehmer er sehen möchte. Ein Videoabonnement ist eine Anforderung des Bots, das Video eines Teilnehmers zu empfangen. Während die Teilnehmer eines Gruppen-Videoanrufs ihre Unterhaltung führen, kann ein Bot seine gewünschten Videoabonnements basierend auf Updates des Satzes dominanter Sprecher ändern.

## <a name="developer-resources"></a>Entwicklerressourcen 

### <a name="sdks"></a>SDKs

Um einen Echtzeit-Medienbot zu entwickeln, müssen Sie diese NuGet-Pakete in Ihrem Visual Studio-Projekt installieren:

- [Bot Framework SDK für .NET](bot-builder-dotnet-overview.md)
- [Bot Builder Real-Time Media Calling für .NET](https://www.nuget.org/packages?q=Bot.Builder.RealTimeMediaCalling)
- [Microsoft.Skype.Bots.Media-Bibliothek für .NET](https://www.nuget.org/packages?q=Microsoft.Skype.Bots.Media)

### <a name="code-samples"></a>Codebeispiele

Die Beispiele im [BotBuilder-RealTimeMediaCalling](https://github.com/Microsoft/BotBuilder-RealTimeMediaCalling)-GitHub-Repository zeigen Ihnen, wie Sie Echtzeit-Medienbots für Skype erstellen.
