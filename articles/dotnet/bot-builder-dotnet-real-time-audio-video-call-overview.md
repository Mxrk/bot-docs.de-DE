---
title: Erstellen eines Echtzeit-Medienbots für Skype | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie mit dem Bot Builder SDK für .NET und dem Bot Builder-RealTimeMediaCalling SDK für .NET einen Bot erstellen, der Audio-/Videoanrufe in Echtzeit durchführt.
author: MalarGit
ms.author: malarch
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 12/13/17
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 6ceeca9adc9cad9e60a73c1c7c91bea43b97fdd9
ms.sourcegitcommit: b78fe3d8dd604c4f7233740658a229e85b8535dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2018
ms.locfileid: "49997919"
---
# <a name="build-a-real-time-media-bot-for-skype"></a>Erstellen eines Echtzeit-Medienbots für Skype

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

Die Echtzeit-Medienplattform für Bots ist eine erweiterte Funktion mit der Bots Sprach- und Videoinhalte Frame für Frame senden und empfangen können. Der Bot hat Rohdatenzugriff auf die Echtzeitmodalitäten der Sprach-, Video- und Bildschirmfreigabe. Dieser Artikel bietet einen Überblick über das Erstellen eines Bots für Audio-/Videoanrufe und das Zugreifen auf die Echtzeitmodalitäten.

In diesem Artikel wird der Bot in einem Azure-Clouddienst ausgeführt, entweder als Webrolle oder als Workerrolle, die das ASP.NET-Web-API-Framework selbst hostet.

> [!IMPORTANT]
> Dieser Artikel ist vorläufig. Im Ordner „Samples“ (Beispiele) im GitHub-Repository <a href="https://github.com/Microsoft/BotBuilder-RealTimeMediaCalling">BotBuilder-RealTimeMediaCalling</a> finden Sie den vollständigen Code von beispielhaften Echtzeit-Medienbots.

## <a name="configure-the-service-hosting-the-real-time-media-bot"></a>Konfigurieren des Diensthosts des Echtzeit-Medienbots

Die Nutzung der Echtzeit-Medienplattform erfordert die folgenden Dienstkonfigurationen:

* Der Bot muss den vollqualifizierten Domänennamen (FQDN) seines Dienstes kennen. Dieser wird nicht von der Azure-RoleEnvironment-API bereitgestellt. Stattdessen muss der FQDN in der Clouddienstkonfiguration des Bots gespeichert und bei Dienststart gelesen werden.

* Azure Bot Service benötigt ein von einer anerkannten Zertifizierungsstelle ausgestelltes Zertifikat. Der Zertifikatfingerabdruck muss in der Clouddienstkonfiguration des Bots gespeichert und bei Dienststart gelesen werden.

* Ein öffentlicher <a href="/azure/cloud-services/cloud-services-enable-communication-role-instances#instance-input-endpoint">Instanzeingabeendpunkt</a> muss bereitgestellt werden. Dadurch wird jeder Instanz eines virtuellen Computers (VM) im Dienst des Bots ein eindeutiger öffentlicher Port zugewiesen. Dieser Port wird von der Echtzeit-Medienplattform für die Kommunikation mit der Cloud für Skype-Anrufe verwendet.
  ```xml
  <InstanceInputEndpoint name="InstanceMediaControlEndpoint" protocol="tcp" localPort="20100">
    <AllocatePublicPortFrom>
    <FixedPortRange max="20200" min="20101" />
    </AllocatePublicPortFrom>
  </InstanceInputEndpoint>
  ```

  Es ist auch sinnvoll, einen weiteren Instanzeingabeendpunkt für Anrufrückrufe und Benachrichtigungen zu erstellen. Das Verwenden eines Instanzeingabeendpunkts gewährleistet, dass die Rückrufe und Benachrichtigungen an dieselbe VM-Instanz in der Dienstbereitstellung übermittelt werden, die die Echtzeit-Mediensitzung für den Anruf bereitstellt.
  ```xml
  <InstanceInputEndpoint name="InstanceCallControlEndpoint" protocol="tcp" localPort="10100">
    <AllocatePublicPortFrom>
    <FixedPortRange max="10200" min="10101" />
    </AllocatePublicPortFrom>
  </InstanceInputEndpoint>
  ```

* Jede VM-Instanz muss eine öffentliche IP-Adresse auf Instanzebene haben. Beim Start muss der Bot die öffentliche IP-Adresse auf Instanzebene ermitteln, die jeder Dienstinstanz zugewiesen ist. Unter <a href="/azure/virtual-network/virtual-networks-instance-level-public-ip">Öffentliche IP-Adresse auf Instanzebene</a> finden Sie weitere Informationen zum Abrufen und Konfigurieren ebendieser IP-Adresse.
  ```xml
  <NetworkConfiguration>
  <AddressAssignments>
    <InstanceAddress roleName="WorkerRole">
    <PublicIPs>
        <PublicIP name="InstancePublicIP" domainNameLabel="InstancePublicIP" />
    </PublicIPs>
    </InstanceAddress>
  </AddressAssignments>
  </NetworkConfiguration>
  ```

* Beim Starten der Dienstinstanz muss das Skript `MediaPlatformStartupScript.bat` (Teil des Nuget-Pakets) als Startaufgabe mit erhöhten Rechten ausgeführt werden. Die Ausführung des Skripts muss abgeschlossen sein, bevor die Initialisierungsmethode der Plattform aufgerufen wird. 

```xml
<Startup>
<Task commandLine="MediaPlatformStartupScript.bat" executionContext="elevated" taskType="simple" />      
</Startup> 
```

## <a name="initialize-the-media-platform-on-service-startup"></a>Initialisieren der Medienplattform bei Dienststart

Beim Starten der Dienstinstanz muss die Echtzeit-Medienplattform initialisiert werden. Dieser Vorgang muss nur einmal erfolgen, bevor der Bot Skype-Audio-/Videoanrufe auf dieser Instanz annehmen kann. Die Initialisierung der Medienplattform erfordert das Festlegen verschiedener Konfigurationseinstellungen, einschließlich des vollqualifizierten Domänennamens des Dienstes, der öffentlichen IP-Adresse auf Instanzebene, des Portwerts des Instanzeingabeendpunkts und der **Microsoft-App-ID** des Bots.

> [!NOTE]
> Unter [MicrosoftAppID und MicrosoftAppPassword](~/bot-service-manage-overview.md#microsoftappid-and-microsoftapppassword) finden Sie die Werte für **AppID** und **AppPassword** für Ihren Bot.

```cs
var mediaPlatformSettings = new MediaPlatformSettings()
{
    MediaPlatformInstanceSettings = new MediaPlatformInstanceSettings()
    {
        CertificateThumbprint = certificateThumbprint,
        InstanceInternalPort = instanceMediaControlEndpointInternalPort,
        InstancePublicIPAddress = instancePublicIPAddress,
        InstancePublicPort = instanceMediaControlEndpointPublicPort,
        ServiceFqdn = serviceFqdn
    },

    ApplicationId = MicrosoftAppId
};

MediaPlatform.Initialize(mediaPlatformSettings);            
```

## <a name="register-to-receive-incoming-call-requests"></a>Registrieren für den Empfang eingehender Anrufanforderungen

Definieren Sie eine `CallController`-Klasse. Dies ermöglicht Bot Service, sich für eingehende Skype-Anrufe zu registrieren und sicherzustellen, dass Rückruf- und Benachrichtigungsanforderungen an das entsprechende `RealTimeMediaCall`-Objekt weitergeleitet werden.

```cs
[BotAuthentication]
[RoutePrefix("api/calling")]
public class CallController : ApiController
{
    static CallController()
    {
        RealTimeMediaCalling.RegisterRealTimeMediaCallingBot(
            c => { return new RealTimeMediaCall(c); },
            new RealTimeMediaCallingBotServiceSettings()
        );
    }

    [Route("call")]
    public async Task<HttpResponseMessage> OnIncomingCallAsync()
    {
        // forwards the incoming call to the associated RealTimeMediaCall object
        return await RealTimeMediaCalling.SendAsync(this.Request, RealTimeMediaCallRequestType.IncomingCall);
    }

    [Route("callback")]
    public async Task<HttpResponseMessage> OnCallbackAsync()
    {
        // forwards the incoming callback to the associated RealTimeMediaCall object
        return await RealTimeMediaCalling.SendAsync(this.Request, RealTimeMediaCallRequestType.CallingEvent);
    }

    [Route("notification")]
    public async Task<HttpResponseMessage> OnNotificationAsync()
    {
        // forwards the incoming notification to the associated RealTimeMediaCall object
        return await RealTimeMediaCalling.SendAsync(this.Request, RealTimeMediaCallRequestType.NotificationEvent);
    }
}
```

`RealTimeMediaCallingBotServiceSettings` implementiert `IRealTimeMediaCallServiceSettings` und stellt Webhooklinks für Rückruf- und Benachrichtigungsereignisse bereit.

## <a name="register-for-incoming-events-for-the-call"></a>Registrieren für eingehende Ereignisse für Anrufe

Erstellen Sie eine `RealTimeMediaCall`-Klasse, die `IRealTimeMediaCall` implementiert. Für jeden Anruf, den der Bot empfängt, wird von Bot Framework eine `RealTimeMediaCall`-Instanz erstellt. Das an den Konstruktor übermittelte Objekt `IRealTimeMediaCallService` erlaubt dem Bot, sich für Ereignisse zu registrieren, um mit dem Echtzeit-Medienanruf verbundene Ereignisse zu verarbeiten.

```cs
internal class RealTimeMediaCall : IRealTimeMediaCall
{
     public RealTimeMediaCall(IRealTimeMediaCallService callService)
     {
         if (callService == null)
             throw new ArgumentNullException(nameof(callService));

         CallService = callService;
         CorrelationId = callService.CorrelationId;
         CallId = CorrelationId + ":" + Guid.NewGuid().ToString();

         // Register for the call events
         CallService.OnIncomingCallReceived += OnIncomingCallReceived;
         CallService.OnAnswerAppHostedMediaCompleted += OnAnswerAppHostedMediaCompleted;
         CallService.OnCallStateChangeNotification += OnCallStateChangeNotification;
         CallService.OnRosterUpdateNotification += OnRosterUpdateNotification;
         CallService.OnCallCleanup += OnCallCleanup;
     }
}
```

## <a name="create-audio-and-video-sockets"></a>Erstellen von AudioSocket- und VideoSocket-Objekten
Bevor der Bot einen eingehenden Skype-Audio-/Videoanruf annehmen kann, muss er `AudioSocket`- und `VideoSocket`-Objekte erstellen, um das Senden und Empfangen von Echtzeitmedien zu unterstützen. Falls der Bot kein Video unterstützen will, sollte er nur ein AudioSocket-Objekt erstellen.

Der Bot muss im Voraus entscheiden, welche Modalitäten er unterstützen möchte, und die entsprechenden AudioSocket- und VideoSocket-Objekte erstellen. Der Bot kann nicht ändern, welche Modalitäten er für den Anruf unterstützt, nachdem der eingehende Anruf angenommen wurde.

Für jedes AudioSocket- und VideoSocket-Objekt gibt der Bot an, ob der Mediensocket sowohl das Senden als auch das Empfangen von Medien bzw. nur das Senden oder nur das Empfangen von Medien unterstützt. Beispielsweise kann der Bot Audiodateien senden und empfangen („Sendrecv“), Videos hingegen nur senden („Sendonly“). Der Bot muss auch angeben, welche Medienformate für jeden Mediensocket unterstützt werden. Für ein AudioSocket-Objekt ist das aktuell unterstützte Format „Pcm16K“: (signierte) 16-Bit-PCM-Codierung, 16 KHz Samplingrate. Für ein VideoSocket-Objekt werden die Medienformate für das Senden und Empfangen separat angegeben. Für den Videoempfang wird nur das „NV12“-Format unterstützt, wohingegen verschiedene Formate für das Senden unterstützt werden.

```cs
_audioSocket = new AudioSocket(new AudioSocketSettings
{
    StreamDirections = StreamDirection.Sendrecv,
    SupportedAudioFormat = AudioFormat.Pcm16K,
    CallId = correlationId
});

_videoSocket = new VideoSocket(new VideoSocketSettings
{
    StreamDirections = StreamDirection.Sendrecv,
    ReceiveColorFormat = VideoColorFormat.NV12,
    SupportedSendVideoFormats = new VideoFormat[]
    {
        VideoFormat.Yuy2_1280x720_30Fps,
        VideoFormat.Yuy2_720x1280_30Fps,
    },
    CallId = correlationId
});

_audioSocket.AudioMediaReceived += OnAudioMediaReceived;
_audioSocket.AudioSendStatusChanged += OnAudioSendStatusChanged;
_audioSocket.DominantSpeakerChanged += OnDominantSpeakerChanged;
_videoSocket.VideoMediaReceived += OnVideoMediaReceived;
_videoSocket.VideoSendStatusChanged += OnVideoSendStatusChanged;
```                             

## <a name="create-a-mediaconfiguration"></a>Erstellen eines MediaConfiguration-Objekts
Nachdem die Mediensockets erstellt wurden, muss der Bot ein `MediaConfiguration`-Objekt erstellen, um Mediensockets einem eingehenden Skype-Audio-/Videoanruf zuzuordnen.

```cs
var mediaConfiguration = MediaPlatform.CreateMediaConfiguration(_audioSocket, _videoSocket);
```

##  <a name="answer-an-incoming-audiovideo-call"></a>Annehmen eingehender Audio-/Videoanrufe
Das Ereignis `OnIncomingCallReceived` wird aufgerufen, damit der Bot einen eingehenden Skype-Audio-/Videoanruf annehmen kann. Dazu erstellt der Bot ein `AnswerAppHostedMedia`-Objekt mit dem `MediaConfiguration`-Objekt. Der Bot registriert sich für Benachrichtigungen in Zusammenhang mit diesem Skype-Anruf.

```cs
private Task OnIncomingCallReceived(RealTimeMediaIncomingCallEvent incomingCallEvent)
{
    // ... create Audio/VideoSocket objects and MediaConfiguration ...

    incomingCallEvent.RealTimeMediaWorkflow.Actions = new ActionBase[]
    {
        new AnswerAppHostedMedia
        {
            MediaConfiguration = mediaConfiguration,
            OperationId = Guid.NewGuid().ToString()
        }
    };

    // subscribe for roster and call state changes
    incomingCallEvent.RealTimeMediaWorkflow.NotificationSubscriptions = new NotificationType[]
    {
        NotificationType.CallStateChange,
        NotificationType.RosterUpdate
    };
}
```

## <a name="outcome-of-the-call"></a>Ergebnis des Anrufs
`OnAnswerAppHostedMediaCompleted` wird ausgelöst, wenn die Aktion `AnswerAppHostedMedia` abgeschlossen ist. Die Eigenschaft `Outcome` in `AnswerAppHostedMediaOutcomeEvent` zeigt an, ob der Vorgang erfolgreich war. Wenn die Anrufverbindung nicht hergestellt werden kann, muss der Bot die AudioSocket- und VideoSocket-Objekte löschen, die er für den Anruf erstellt hat.

## <a name="receive-audio-media"></a>Empfangen von Audiomedien
Wenn `AudioSocket` so erstellt wurde, dass Audio empfangen werden kann, wird das Ereignis `AudioMediaReceived` jedes Mal aufgerufen, wenn ein Audioframe empfangen wird. Der Bot sollte dieses Ereignis etwa 50 Mal pro Sekunde verarbeiten, unabhängig davon, welcher Peer Audioinhalte empfängt (da Komfortrauschpuffer lokal generiert werden, wenn kein Audio vom Peer empfangen wird). Jedes Audioinhaltspaket wird in einem `AudioMediaBuffer`-Objekt übermittelt. Dieses Objekt enthält einen Zeiger auf einen nativen vom Heap zugewiesenen Speicherpuffer mit dem dekodierten Audioinhalt. 

```cs
void OnAudioMediaReceived(
            object sender,
            AudioMediaReceivedEventArgs args)
{
   var buffer = args.Buffer;

   // native heap-allocated memory containing decoded content
   IntPtr rawData = buffer.Data;            
}
```

Die Reaktion des Ereignishandlers muss schnell erfolgen. Es wird empfohlen, dass die Anwendung `AudioMediaBuffer` in eine Warteschlange einreiht, damit die Verarbeitung asynchron erfolgt. `OnAudioMediaReceived`-Ereignisse werden von der Echtzeit-Medienplattform serialisiert, d.h. das nächste Ereignis wird erst ausgelöst, wenn das aktuelle zurückgegeben wurde. Sobald ein `AudioMediaBuffer` verbraucht wurde, muss die Anwendung die Dispose-Methode des Puffers aufrufen, damit der zugrunde liegende nicht verwaltete Speicher von der Medienplattform zurückgenommen werden kann. 

```cs
   // release/dispose buffer when done 
   buffer.Dispose();
```

> [!IMPORTANT]
> Der Bot darf die Dispose-Methode des Puffers erst dann aufrufen, wenn der Zugriff auf den Puffer abgeschlossen ist.

## <a name="receive-video-media"></a>Empfangen von Videomedien
Das Empfangen von Videos ähnelt dem Empfangen von Audiomedien, nur dass die Anzahl von Puffern pro Sekunde vom Bildfrequenzwert abhängt. `VideoMediaBuffer` verfügt über die Eigenschaften `VideoFormat` und `OriginalVideoFormat`. `OriginalVideoFormat` stellt das ursprüngliche Format des Puffers dar, als er erstellt wurde. Die Eigenschaft ist nur verfügbar, wenn Videopuffer über den `IVideoSocket.VideoMediaReceived`-Ereignishandler empfangen werden. Wenn die Größe des Puffers vor der Übertragung geändert wurde, hat die Eigenschaft `OriginalVideoFormat` die ursprüngliche Breite und Höhe, wohingegen `VideoFormat` die Größe nach der Größenänderung hat. Wenn die Breiten- und Höheneigenschaften von `OriginalVideoFormat` von der `VideoFormat`-Eigenschaft abweichen, muss der Consumer des im `VideoMediaReceived`-Ereignis ausgelösten `VideoMediaBuffer` die Puffergröße an die Größe von `OriginalVideoFormat` anpassen. Derzeit wird für das Empfangen nur das „NV12“-Format unterstützt.

## <a name="send-audio-media"></a>Senden von Audiomedien
Wenn `AudioSocket` für das Senden von Medien konfiguriert ist, muss sich der Bot für den `AudioSendStatusChanged`-Ereignishandler auf dem `AudioSocket`-Objekt registrieren, um Benachrichtigungen zu Sendestatusänderungen zu erhalten. Der Bot sollte erst dann mit dem Senden von Audio beginnen, wenn der Sendestatus „Aktiv“ entspricht.

```cs
void AudioSocket_OnSendStatusChanged(
             object sender,
             AudioSendStatusChangedEventArgs args)
{
    switch (args.MediaSendStatus)
    {
    case MediaSendStatus.Active:
        // notify bot to begin sending audio 
        break;
     
    case MediaSendStatus.Inactive:
        // notify bot to stop sending audio
        break;
    }
}
```

Um Audiomedien zu senden, wird davon ausgegangen, dass der PCM-Audioinhalt in einem nativen vom Heap zugewiesenen Puffer enthalten ist. Der Bot muss aus der abstrakten `AudioMediaBuffer`-Klasse abgeleitet werden. Medien werden asynchron über die Methode `Send` des AudioSocket-Objekts gesendet, und die Plattform ruft `Dispose` auf `AudioMediaBuffer` auf, wenn der Sendevorgang abgeschlossen ist. Der Bot darf die nicht verwalteten Ressourcen nicht freigeben (oder an eine Poolzuweisung zurückgeben), wenn `Send` zurückgegeben wird. Er muss warten, bis `Dispose` aufgerufen wird.

## <a name="send-video-media"></a>Senden von Videomedien
Das Senden von Videomedien ist vergleichbar mit dem Senden von Audiomedien. Der Bot sollte sich für das `VideoSendStatusChanged`-Ereignis registrieren und warten bis `MediaSendStatus` `Active` ist. Der Bot darf die nicht verwalteten Pufferressourcen nicht freigeben oder zurücknehmen, bis die `Dispose`-Methode aufgerufen wird. Es werden die Farbformate RGB24, NV12 und Yuy2 unterstützt.

Während mehrere `VideoFormat`-Eigenschaften für das Senden von Videos unterstützt werden, wird die derzeit bevorzugte `VideoFormat`-Eigenschaft über das `VideoSendStatusChanged`-Ereignis an den Bot übermittelt. Die bevorzugte `VideoFormat`-Eigenschaft für das Senden von Videos kann sich ändern – aufgrund der Netzwerkbandbreitenbedingungen, oder weil der Peerclient die Videofenstergröße ändert.

```cs
void VideoSocket_OnSendStatusChanged(
            object sender,
            VideoSendStatusChangedEventArgs args)
{
    VideoFormat preferredVideoFormat;

    switch (args.MediaSendStatus)
    {
    case MediaSendStatus.Active:
        // notify bot to begin sending audio 
        // bot is recommended to use this format for sourcing video content.
        preferredVideoFormat = args.PreferredVideoSourceFormat;
        break;
     
    case MediaSendStatus.Inactive:
        // notify bot to stop sending audio
        break;
    }
}
```

Für `VideoMediaBuffer` gibt es jeweils eine VideoFormat-Eigenschaft, die das Format des Videoinhalts für diesen speziellen Puffer angibt. Obwohl die `VideoFormat`-Eigenschaft nicht mit der `PreferredVideoSourceFormat`-Eigenschaft übereinstimmen muss, die im `VideoSendStatusChanged`-Ereignis angegeben ist, wird dringend empfohlen, die angegebene `PreferredVideoSourceFormat`-Eigenschaft zu verwenden, um verschwenderische CPU-Zyklen zu vermeiden, die für Größenänderung an Videobildern nötig sind.

## <a name="call-notifications"></a>Anrufbenachrichtigungen
### <a name="call-state-changes"></a>Änderungen des Anrufstatus
Der Bot kann Benachrichtigungen zu Anrufstatusänderungen erhalten, indem er `NotificationType.CallStateChange` in `NotificationSubscriptions` von `RealTimeMediaIncomingCallEvent.RealTimeMediaWorkflow` abonniert.

```cs
private Task OnCallStateChangeNotification(CallStateChangeNotification callStateChangeNotification)
{
    if (callStateChangeNotification.CurrentState == CallState.Terminated)
    {   
        // stop sending media and dispose the media sockets
        _audioSocket.Dispose();
        _videoSocket.Dispose();
    }

    return Task.CompletedTask;
}
 ```

### <a name="roster-update"></a>Listenupdates
Wenn der Bot einer Konferenz hinzugefügt wird, kann er auf die Konferenzliste zugreifen, indem er `NotificationType.RosterUpdate` in `NotificationSubscriptions` von `RealTimeMediaIncomingCallEvent.RealTimeMediaWorkflow` abonniert. `RosterUpdateNotification` beinhaltet alle Informationen zu den Konferenzteilnehmern. Der Bot kann auf eine Benachrichtigung von einem Teilnehmer warten, der ein Video sendet, und dann das Video des Teilnehmers auf einem seiner `VideoSocket`-Objekte abonnieren (`Subscribe`).

```cs
private async Task OnRosterUpdateNotification(RosterUpdateNotification rosterUpdateNotification)
{
    // Find a video source in the conference to subscribe
    foreach (RosterParticipant participant in rosterUpdateNotification.Participants)
    {
        if (participant.MediaType == ModalityType.Video
            && (participant.MediaStreamDirection == "sendonly" || participant.MediaStreamDirection == "sendrecv")
            )
        {
            var videoSubscription = new VideoSubscription
            {
                ParticipantIdentity = participant.Identity,
                OperationId = Guid.NewGuid().ToString(),
                SocketId = _videoSocket.SocketId,
                VideoModality = ModalityType.Video,
                VideoResolution = ResolutionFormat.Hd720p
            };

            await CallService.Subscribe(videoSubscription).ConfigureAwait(false);
            break;
        }
    }  
}
```

## <a name="end-the-call"></a>Beenden eines Anrufs

### <a name="handle-call-termination-from-the-caller"></a>Beenden des Anrufs vom Anrufer
Wenn der Benutzer die Verbindung zum Bot in einer Peer-zu-Peer-Unterhaltung trennt, oder der Bot aus der Konferenz entfernt wird, erhält der Bot eine Benachrichtigung zur Anrufstatusänderung. Dabei entspricht „CallState“ „Beendet“. Der Bot muss alle erstellten Mediensockets löschen.

### <a name="terminate-the-call-from-the-bot"></a>Beenden des Anrufs vom Bot
Der Bot kann einen Anruf beenden, indem er `EndCall` in `IRealTimeMediaCallService` aufruft.

### <a name="handle-call-clean-up-by-the-bot-framework"></a>Verarbeiten der Anrufbereinigung mit Bot Framework
Unter Fehlerbedingungen (z.B. wenn `AnswerAppHostedMediaOutcomeEvent` nicht innerhalb einer angemessenen Zeit empfangen wird) kann Bot Framework den Anruf beenden. Der Bot muss sich für das `OnCallCleanup`-Ereignis registrieren und die Mediensockets löschen.

