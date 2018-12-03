---
title: Implementieren von benutzerdefiniertem Speicher für Ihren Bot | Microsoft-Dokumentation
description: Erstellen von benutzerdefiniertem Speicher im Bot Builder SDK v4.0
keywords: benutzerdefiniert, Speicher, Zustand, Dialog
author: johnataylor
ms.author: johtaylo
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 10/31/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: b005b9f024c5813ba22cd8663c196a8c3a5bb716
ms.sourcegitcommit: 15f7fa40b7e0a05507cdc66adf75bcfc9533e781
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2018
ms.locfileid: "50919009"
---
# <a name="implement-custom-storage-for-your-bot"></a>Implementieren von benutzerdefiniertem Speicher für Ihren Bot

Die Interaktionen eines Bots lassen sich in drei Bereiche unterteilen: erstens den Austausch von Aktivitäten mit dem Azure Bot Service, zweitens das Laden und Speichern des Dialogzustands mit einem Speicher und schließlich alle anderen Back-End-Dienste, mit denen der Bot zum Ausführen seiner Aufgabe arbeiten muss.

![Diagramm zur horizontalen Skalierung](../media/scale-out/scale-out-interaction.png)

In diesem Artikel wird die Semantik im Zusammenhang mit den Interaktionen des Bots mit dem Azure Bot Service und dem Speicher behandelt.

Bot Builder Framework enthält eine Standardimplementierung, die die Anforderungen vieler Anwendungen erfüllt und zu deren Verwendung nur einige Zeilen Initialisierungscode zum Zusammenfügen der einzelnen Komponenten erforderlich sind. Viele der Beispiele veranschaulichen diese Implementierung.

In diesem Artikel soll jedoch erläutert werden, was Sie tun können, wenn die Semantik der Standardimplementierung in Ihrer Anwendung nicht wie gewünscht funktioniert. Der springende Punkt ist, dass dies ein Framework ist und keine vordefinierte Anwendung mit einem festen Verhalten. Anders ausgedrückt: Die Implementierung vieler der Mechanismen im Framework ist lediglich die Standardimplementierung und nicht die einzige Implementierung.

Insbesondere gibt das Framework nicht die Beziehung zwischen dem Austausch von Aktivitäten mit dem Azure Bot Service und dem Laden und Speichern eines Botzustands vor. Es stellt lediglich eine Standardimplementierung bereit. Um diesen Punkt zu verdeutlichen, entwickeln wir eine alternative Implementierung mit einer anderen Semantik. Die alternative Lösung ist ebenso gut in das Framework integriert und möglicherweise sogar besser geeignet für die entwickelte Anwendung. Dies hängt einzig und allein vom Szenario ab.

## <a name="behavior-of-the-default-botframeworkadapter-and-storage-providers"></a>Verhalten der standardmäßigen BotFrameworkAdapter- und Speicheranbieter

Zuerst sehen wir uns die im folgenden Sequenzdiagramm dargestellte Standardimplementierung an, die in den Frameworkpaketen enthalten ist:

![Diagramm zur horizontalen Skalierung](../media/scale-out/scale-out-default.png)

Beim Empfang einer Aktivität lädt der Bot den Zustand, der dieser Konversation entspricht. Anschließend führt er die Dialoglogik mit diesem Zustand und der soeben empfangenen Aktivität aus. Während der Ausführung des Dialogs werden eine oder mehrere ausgehende Aktivitäten erstellt und sofort gesendet. Wenn die Verarbeitung des Dialogs abgeschlossen ist, speichert der Bot den aktualisierten Zustand, wobei der alte Zustand mit dem neuen überschrieben wird.

Bei diesem Verhalten können einige Probleme auftreten, die es zu erwägen gilt.

Erstens: Wenn beim Speichervorgang aus irgendeinem Grund ein Fehler auftritt, ist der Zustand nicht mehr mit der Anzeige im Kanal synchron, da für den Benutzer, der die Antworten gesehen hat, der Eindruck entsteht, dass sich der Zustand verändert hat, obwohl dies nicht der Fall ist. Dies ist generell schlechter als das erfolgreiche Speichern des Zustands und ein erfolgreiches Antwortmessaging. Dieses Problem kann Auswirkungen auf den Entwurf der Konversation haben: Der Dialog kann beispielsweise einen zusätzlichen, andernfalls redundanten Austausch von Bestätigungen mit dem Benutzer enthalten. 

Zweitens: Wenn die Implementierung horizontal hochskaliert auf mehreren Knoten bereitgestellt wird, kann der Zustand versehentlich überschrieben werden. Dies kann besonders verwirrend sein, da der Dialog wahrscheinlich Aktivitäten mit Bestätigungsnachrichten an den Kanal gesendet hat. Nehmen Sie das Beispiel eines Pizzabestellungsbots: Wenn der Benutzer bei der Frage nach dem Belag Pilze und dann ohne Verzögerung Käse hinzufügt, können in einem horizontal hochskalierten Szenario mit mehreren aktiven Instanzen nachfolgende Aktivitäten gleichzeitig an verschiedene Computer gesendet werden, auf denen der Bot ausgeführt wird. In diesem Fall kommt es zu einer so genannten „Racebedingung“, bei der ein Computer den von einem anderen Computer geschriebenen Zustand überschreiben kann. Da die Antworten in diesem Szenario bereits gesendet wurden, hat der Benutzer jedoch die Bestätigung erhalten, dass sowohl Pilze als auch Käse hinzugefügt wurden. Die gelieferte Pizza ist jedoch leider nur mit Pilzen oder nur mit Käse belegt, aber nicht mit beidem.

## <a name="optimistic-locking"></a>Optimistische Sperre

Die Lösung besteht darin, eine Sperre für den Status zu verwenden. Der Sperrentyp, den wir hier verwenden, wird als optimistische Sperre bezeichnet, da alle Prozesse so ausgeführt werden, als wären sie die jeweils einzigen aktiven Prozesse. Nach Abschluss der Verarbeitung wird dann eine Erkennung von Parallelitätsverletzungen ausgeführt. Dies mag kompliziert klingen, ist aber mithilfe von Cloudspeichertechnologien und den richtigen Erweiterungspunkten in Bot Framework sehr einfach umzusetzen.

Wir verwenden einen HTTP-Standardmechanismus, der auf dem Entitätstagheader (ETag) basiert. Das Verständnis dieses Mechanismus ist entscheidend, um den folgenden Code zu verstehen. Das unten stehende Diagramm veranschaulicht die Sequenz.

![Diagramm zur horizontalen Skalierung](../media/scale-out/scale-out-precondition-failed.png)

Das Diagramm zeigt eine Situation, in der zwei Clients eine Ressource aktualisieren. Wenn ein Client eine GET-Anforderung ausgibt und eine Ressource vom Server zurückgegeben wird, enthält sie einen ETag-Header. Der ETag-Header ist ein nicht transparenter Wert, der den Zustand der Ressource darstellt. Wenn eine Ressource geändert wird, wird das ETag aktualisiert. Nachdem der Client den Zustand aktualisiert hat, sendet er die Ressource mit einer POST-Anforderung an den Server zurück, wobei er den zuvor empfangenen ETag-Wert in einem If-Match-Vorbedingungsheader an die Anforderung anfügt. Stimmt dieses ETag nicht mit dem zuletzt vom Server (bei allen Antworten an beliebige Clients) zurückgegebenen Wert überein, schlägt die Überprüfung der Vorbedingung mit einem Vorbedingungsfehler vom Typ 412 fehl. Für den Client, der die POST-Anforderung sendet, ist dieser Fehler ein Indikator dafür, dass die Ressource aktualisiert wurde. Das typische Verhalten für einen Client beim Auftreten dieses Fehlers ist das erneute Abrufen der Ressource mit GET, Anwenden der gewünschten Aktualisierung und Zurücksenden der Ressource mit POST. Diese zweite POST-Anforderung ist erfolgreich, sofern kein anderer Client die Ressource aktualisiert hat. Sollte dies der Fall sein, muss der Client den Vorgang einfach wiederholen.

Dieser Prozess wird als „optimistisch“ bezeichnet, weil der Client, der eine Ressource abgerufen hat, seine Verarbeitung fortsetzt. Die Ressource selbst wird nicht „gesperrt“ in dem Sinn, dass andere Clients ohne Einschränkung auf sie zugreifen können. Konflikte zwischen Clients bezüglich des Zustands der Ressource sollten erst nach Abschluss der Verarbeitung ermittelt werden. Im Allgemeinen ist diese Strategie in einem verteilten System besser geeignet als der entgegengesetzte „pessimistische“ Ansatz.

Der oben beschriebene optimistische Sperrmechanismus setzt voraus, dass die Programmlogik sicher wiederholt werden kann. Entscheidend ist hierbei selbstverständlich, was mit externen Dienstaufrufen passiert. Die ideale Lösung wären idempotente Dienste. In der Informatik ist ein idempotenter Vorgang ein Vorgang, der keine weiteren Auswirkungen hat, wenn er mehrmals mit den gleichen Eingabeparametern aufgerufen wird. Dies trifft auf HTTP-REST-Dienste zu, die GET, PUT und DELETE implementieren. Der Grund hierfür liegt auf der Hand: Da die Verarbeitung möglicherweise wiederholt wird, ist es von Vorteil, wenn alle erforderlichen Aufrufe, die im Rahmen dieser Wiederholung erneut ausgeführt werden, keine zusätzlichen Auswirkungen haben. In dieser Erörterung gehen wir vom Idealfall aus und setzen voraus, dass die auf der rechten Seite des Systemdiagramms am Anfang des Artikels dargestellten Back-End-Dienste alle idempotente HTTP-REST-Dienste sind. Im verbleibenden Teil dieses Artikels konzentrieren wir uns daher ausschließlich auf den Austausch von Aktivitäten.

## <a name="buffering-outbound-activities"></a>Puffern von ausgehenden Aktivitäten

Das Senden einer Aktivität ist kein idempotenter Vorgang, und zudem ist nicht klar, ob dies im End-to-End-Szenario sinnvoll wäre. Im Grunde genommen enthält die Aktivität oftmals nur eine Nachricht, die an eine Ansicht angefügt oder vielleicht von einem Sprachsynthese-Agent gesprochen wird.

Beim Senden der Aktivitäten soll in erster Linie vermieden werden, dass diese mehrmals gesendet werden. Das Problem hierbei ist, dass der optimistische Sperrmechanismus die mehrmalige erneute Ausführung der Logik erfordert. Die Lösung ist einfach: Die ausgehenden Aktivitäten des Dialogs müssen gepuffert werden, bis sicher ist, dass die Logik nicht erneut ausgeführt wird. Die Aktivitäten müssen folglich bis zum erfolgreichen Abschluss eines Speichervorgangs gepuffert werden. Der gewünschte Ablauf sieht in etwa wie folgt aus:

![Diagramm zur horizontalen Skalierung](../media/scale-out/scale-out-buffer.png)

Sofern eine Wiederholungsschleife für die Ausführung des Dialogs erstellt werden kann, erhalten wir das folgende Verhalten, wenn beim Speichervorgang ein Vorbedingungsfehler auftritt:

![Diagramm zur horizontalen Skalierung](../media/scale-out/scale-out-save.png)

Wird dieser Mechanismus im obigen Beispiel angewendet, sollte niemals eine falsch positive Bestätigung für einen zu einer Bestellung hinzugefügten Pizzabelag erfolgen. Auch wenn wir die Bereitstellung auf mehrere Computer horizontal hochskalieren, erzielen wir mit dem optimistischen Sperrschema eine effektive Serialisierung der Zustandsaktualisierungen. Beim Pizzabestellungsbot kann die Bestätigung für das Hinzufügen eines Belags jetzt so geschrieben werden, dass der vollständige Zustand korrekt wiedergegeben wird. Wenn der Benutzer beispielsweise „Käse“ und dann, bevor der Bot antworten konnte, sofort „Pilze“ eingibt, können die beiden Antworten nun „Pizza mit Käse“ und anschließend „Pizza mit Käse und Pilzen“ lauten.

Im Sequenzdiagramm können Sie sehen, dass die Antworten nach einem erfolgreichen Speichervorgang verloren gehen können. Sie können jedoch an jeder beliebigen Stelle in der End-to-End-Kommunikation verloren gehen. Der springende Punkt ist, dass dieses Problem nicht durch die Infrastruktur für die Zustandsverwaltung behoben werden kann. Das Problem erfordert ein übergeordnetes Protokoll, möglicherweise unter Einbeziehung des Benutzers des Kanals. Wenn für den Benutzer der Eindruck entsteht, dass der Bot nicht geantwortet hat, ist beispielsweise zu erwarten, dass der Benutzer den Vorgang wiederholt oder sich ähnlich verhält. Gelegentliche vorübergehende Ausfälle können in einem Szenario zu erwarten sein. Deutlich unrealistischer ist jedoch die Annahme, dass ein Benutzer falsch positive Bestätigungen oder andere unbeabsichtigte Nachrichten herausfiltern kann. 

In der neuen benutzerdefinierten Speicherlösung setzen wir all dies mit drei Schritten bzw. Teilen um, die in der Standardimplementierung im Framework nicht enthalten sind. Erstens: Wir verwenden ETags, um Konflikte zu erkennen. Zweitens: Wir wiederholen die Verarbeitung, wenn der ETag-Fehler erkannt wird. Drittens: Wir puffern alle ausgehenden Aktivitäten, bis ein erfolgreicher Speichervorgang stattgefunden hat. Im weiteren Verlauf dieses Artikels wird die Implementierung dieser drei Teile beschrieben.

## <a name="implementing-etag-support"></a>Implementieren der ETag-Unterstützung

Zur Unterstützung von Komponententests definieren wir zunächst eine Schnittstelle für den neuen Speicher mit ETag-Unterstützung. Die Schnittstelle ermöglicht es, zwei Versionen zu schreiben: eine Version für die Komponententests, die im Arbeitsspeicher ausgeführt werden, ohne auf das Netzwerk zugreifen zu müssen, und eine weitere Version für die Produktion. Mithilfe der Schnittstelle können die Mechanismen zur Abhängigkeitsinjektion in ASP.NET ganz einfach genutzt werden.

Die Schnittstelle besteht aus Load- und Save-Methoden. Beide Methoden akzeptieren den Schlüssel, der für den Zustand verwendet wird. Die Load-Methode gibt die Daten und das zugehörige ETag zurück. Die Save-Methode akzeptiert diese Daten und das ETag. Außerdem gibt die Save-Methode einen booleschen Wert zurück. Dieser boolesche Wert gibt an, ob das ETag abgeglichen wurde und der Speichervorgang erfolgreich war. Er soll nicht als allgemeiner Fehlerindikator dienen, sondern als ein spezifischer Indikator für Vorbedingungsfehler. Wir modellieren den Wert nicht als Ausnahme, sondern als einen Rückgabecode, weil wir die zugehörige Ablaufsteuerungslogik in Form einer Wiederholungsschleife schreiben.

Da diese Speicherkomponente auf niedrigster Ebene austauschbar sein soll, stellen wir sicher, dass keine Serialisierungsanforderungen für sie gelten. Wir wollen jedoch festlegen, dass der Inhalt in JSON gespeichert wird, damit der Inhaltstyp von einer Speicherimplementierung festgelegt werden kann. Die einfachste und naheliegendste Methode hierzu ist in .NET die Verwendung von Argumenttypen. Insbesondere typisieren wir das Inhaltsargument als JObject. In JavaScript oder TypeScript ist dies nur ein reguläres natives Objekt.  

Die resultierende Schnittstelle sieht wie folgt aus:

```csharp
public interface IStore
{
  Task<(JObject content, string eTag)> LoadAsync(string key);
  Task<bool> SaveAsync(string key, JObject content, string eTag);
}
```
Die Implementierung dieser Lösung für Azure Blob Storage ist unkompliziert.
```csharp
public class BlobStore : IStore
{
  private CloudBlobContainer _container;

  public BlobStore(string myAccountName, string myAccountKey, string containerName)
  {
    var storageCredentials = new StorageCredentials(myAccountName, myAccountKey);
    var cloudStorageAccount = new CloudStorageAccount(storageCredentials, useHttps: true);
    var client = cloudStorageAccount.CreateCloudBlobClient();
    _container = client.GetContainerReference(containerName);
  }

  public async Task<(JObject content, string eTag)> LoadAsync(string key)
  {
    var blob = _container.GetBlockBlobReference(key);
    try
    {
      var content = await blob.DownloadTextAsync();
      var obj = JObject.Parse(content);
      var eTag = blob.Properties.ETag;
      return (obj, eTag);
    }
    catch (StorageException e)
      when (e.RequestInformation.HttpStatusCode ==
        (int)HttpStatusCode.NotFound)
    {
      return (new JObject(), null);
    }
  }

  public async Task<bool> SaveAsync(string key, JObject obj, string eTag)
  {
    var blob = _container.GetBlockBlobReference(key);
    blob.Properties.ContentType = "application/json";
    var content = obj.ToString();
    if (eTag != null)
    {
      try
      {
        await blob.UploadTextAsync(content,
          new AccessCondition { IfMatchETag = eTag },
          new BlobRequestOptions(),
          new OperationContext());
      }
      catch (StorageException e)
        when (e.RequestInformation.HttpStatusCode ==
          (int)HttpStatusCode.PreconditionFailed)
      {
        return false;
      }
    }
    else
    {
      await blob.UploadTextAsync(content);
    }
    return true;
  }
}
```

Wie Sie sehen, übernimmt Azure Blob Storage die eigentliche Arbeit. Beachten Sie den Catch-Block für bestimmte Ausnahmen und wie dies umgesetzt wird, um die Erwartungen des aufrufenden Codes zu erfüllen. Beim Laden soll eine Ausnahme vom Typ „Nicht gefunden“ NULL zurückgeben, und die Ausnahme für den Vorbedingungsfehler beim Speichern soll einen booleschen Wert zurückgeben.

Der vollständige Quellcode ist in einem entsprechenden [Beispiel](https://aka.ms/scale-out) verfügbar, das eine Arbeitsspeicherimplementierung enthält.

## <a name="implementing-the-retry-loop"></a>Implementieren der Wiederholungsschleife
Die grundlegende Form der Schleife leitet sich direkt von dem Verhalten ab, das in den Sequenzdiagrammen dargestellt ist.

Beim Empfang einer Aktivität erstellen wir einen Schlüssel für den entsprechenden Zustand für diese Konversation. Wir ändern die Beziehung zwischen der Aktivität und dem Konversationszustand nicht und erstellen den Schlüssel daher auf die gleiche Weise wie bei der Standardzustandsimplementierung.

Nachdem wir den geeigneten Schlüssel erstellt haben, versuchen wir, den entsprechenden Zustand zu laden. Anschließend führen wir die Dialoge des Bots aus und versuchen, die Informationen zu speichern. Wenn der Speichervorgang erfolgreich ist, senden wir die aus der Ausführung des Dialogs resultierenden ausgehenden Aktivitäten, womit der Vorgang abgeschlossen ist. Tritt beim Speichern ein Fehler auf, gehen wir zurück und wiederholen den gesamten Prozess vor dem Laden. Durch die Wiederholung des Ladevorgangs erhalten wir ein neues ETag, sodass der nächste Speichervorgang hoffentlich erfolgreich ist.

Die resultierende OnTurn-Implementierung sieht wie folgt aus:
```csharp
public async Task OnTurnAsync(ITurnContext turnContext,
  CancellationToken cancellationToken = default(CancellationToken))
{
  // Create the storage key for this conversation.
  string key = $"{turnContext.Activity.ChannelId}/conversations/{turnContext.Activity.Conversation?.Id}";

  // The execution sits in a loop because there might be a retry if the save operation fails.
  while (true)
  {
    // Load any existing state associated with this key
    var (oldState, etag) = await _store.LoadAsync(key);

    // Run the dialog system with the old state and inbound activity,
    // resulting in a new state and outbound activities.
    var (activities, newState) = await DialogHost.RunAsync(_rootDialog, turnContext.Activity, oldState);

    // Save the updated state associated with this key.
    bool success = await _store.SaveAsync(key, newState, etag);

    // Following a successful save, send any outbound Activities, otherwise retry everything.
    if (success)
    {
      if (activities.Any())
      {
        // This is an actual send on the TurnContext we were given and so will actual do a send this time.
        await turnContext.SendActivitiesAsync(activities);
      }
      break;
    }
  }
}
```
Beachten Sie, dass die Dialogausführung als Funktionsaufruf modelliert wurde. Eine komplexere Implementierung würde vielleicht eine Schnittstelle umfassen und diese Abhängigkeit als injizierbar festlegen. In diesem Fall betont die Platzierung des Dialogs hinter einer statischen Funktion jedoch die Funktionalität unseres Ansatzes. Indem wir die Implementierung so organisieren, dass die wichtigsten Teile funktionsfähig sind, schaffen wir uns eine gute Ausgangsposition für ihre erfolgreiche Verwendung in Netzwerken.


## <a name="implementing-outbound-activity-buffering"></a>Implementieren der Pufferung von ausgehenden Aktivitäten 

Die nächste Anforderung besteht darin, ausgehende Aktivitäten bis zur erfolgreichen Speicherung zu puffern. Dies erfordert eine benutzerdefinierte BotAdapter-Implementierung. In diesem Code implementieren wir die abstrakte SendActivity-Funktion, um die Aktivität einer Liste hinzuzufügen, anstatt sie zu senden. Der von uns gehostete Dialog verfügt nicht über mehr Informationen.
UpdateActivity- und DeleteActivity-Vorgänge werden in diesem speziellen Szenario nicht unterstützt und lösen daher nur eine Meldung vom Typ „Nicht implementiert“ von diesen Methoden aus. Der Rückgabewert des SendActivity-Vorgangs ist für uns ebenfalls nicht relevant. Dieser Wert wird von einigen Kanälen in Szenarien genutzt, in denen Aktualisierungen an Aktivitäten gesendet werden müssen (beispielsweise um Schaltflächen auf Karten zu deaktivieren, die im Kanal angezeigt werden). Dieser Nachrichtenaustausch kann insbesondere dann kompliziert sein, wenn der Zustand erforderlich ist (dies wird in diesem Artikel nicht beschrieben). Die vollständige Implementierung des benutzerdefinierten BotAdapter sieht wie folgt aus:

```csharp
public class DialogHostAdapter : BotAdapter
{
  private List<Activity> _response = new List<Activity>();

  public IEnumerable<Activity> Activities => _response;

  public override Task<ResourceResponse[]> SendActivitiesAsync(ITurnContext turnContext,
    Activity[] activities, CancellationToken cancellationToken)
  {
    foreach (var activity in activities)
    {
      _response.Add(activity);
    }
    return Task.FromResult(new ResourceResponse[0]);
  }

  public override Task DeleteActivityAsync(ITurnContext turnContext,
    ConversationReference reference, CancellationToken cancellationToken)
  {
    throw new NotImplementedException();
  }

  public override Task<ResourceResponse> UpdateActivityAsync(ITurnContext turnContext,
    Activity activity, CancellationToken cancellationToken)
  {
    throw new NotImplementedException();
  }
}
```
Integration. Jetzt müssen Sie diese verschiedenen neuen Teile nur noch in den vorhandenen Komponenten des Frameworks zusammenfügen. Die primäre Wiederholungsschleife befindet sich in der IBot OnTurn-Funktion. Sie enthält unsere benutzerdefinierte IStore-Implementierung, die wir zu Testzwecken als abhängigkeitsinjizierbar festgelegt haben. Wir haben den gesamten Dialoghostingcode in eine Klasse namens „DialogHost“ eingefügt, die eine einzelne öffentliche statische Funktion verfügbar macht. Diese Funktion ist so definiert, dass sie die eingehende Aktivität und den alten Zustand akzeptiert und dann die resultierenden Aktivitäten und den neuen Zustand zurückgibt.

Zunächst erstellen wir in dieser Funktion den benutzerdefinierten BotAdapter, der weiter oben in diesem Artikel vorgestellt wurde. Anschließend führen wir den Dialog einfach wie gewohnt aus, indem wir eine DialogSet- und DialogContext-Klasse erstellen und den üblichen „Weiter“- oder „Starten“-Fluss ausführen. Der einzige Teil, mit dem wir uns nicht befasst haben, ist die Notwendigkeit eines benutzerdefinierten Accessors. Dieser Accessor ist ein sehr einfacher Shim, der das Übergeben des Dialogzustands an das Dialogsystem ermöglicht. Der Accessor nutzt bei der Verwendung mit dem Dialogsystem Verweissemantik, sodass nur das Handle übergeben werden muss. Um dies zu verdeutlichen, haben wir die verwendete Klassenvorlage auf Verweissemantik beschränkt.

Bei der Erstellung der Ebenen gehen wir vorsichtig vor. Wir fügen die JsonSerialization inline in den Hostingcode ein, da sie nicht in der austauschbaren Speicherebene enthalten sein soll, wenn verschiedene Implementierungen die Serialisierung auf unterschiedliche Weise vornehmen können.

Hier sehen Sie den Treibercode:
```csharp
public class DialogHost
{
  private static readonly JsonSerializer StateJsonSerializer = new JsonSerializer()
    { TypeNameHandling = TypeNameHandling.All };

  public static async Task<Tuple<Activity[], JObject>> RunAsync(Dialog rootDialog,
    Activity activity, JObject oldState)
  {
    // A custom adapter and corresponding TurnContext that buffers any messages sent.
    var adapter = new DialogHostAdapter();
    var turnContext = new TurnContext(adapter, activity);

    // Run the dialog using this TurnContext with the existing state.
    JObject newState = await RunTurnAsync(rootDialog, turnContext, oldState);

    // The result is a set of activities to send and a replacement state.
    return Tuple.Create(adapter.Activities.ToArray(), newState);
  }

  private static async Task<JObject> RunTurnAsync(Dialog rootDialog,
    TurnContext turnContext, JObject state)
  {
    if (turnContext.Activity.Type == ActivityTypes.Message)
    {
      // If we have some state, deserialize it. (This mimics the shape produced by BotState.cs.)
      var dialogState = state?[nameof(DialogState)]?.ToObject<DialogState>(StateJsonSerializer);

      // A custom accessor is used to pass a handle on the state to the dialog system.
      var accessor = new RefAccessor<DialogState>(dialogState);

      // The following is regular dialog driver code.
      var dialogs = new DialogSet(accessor);
      dialogs.Add(rootDialog);

      var dialogContext = await dialogs.CreateContextAsync(turnContext);
      var results = await dialogContext.ContinueDialogAsync();

      if (results.Status == DialogTurnStatus.Empty)
      {
        await dialogContext.BeginDialogAsync("root");
      }

      // Serialize the result, and put its value back into a new JObject.
      return new JObject
      {
        { nameof(DialogState), JObject.FromObject(accessor.Value, StateJsonSerializer) }
      };
    }

    return state;
  }
}
```
Für den benutzerdefinierten Accessor müssen wir nur „Set“ implementieren, weil auf den Zustand verwiesen wird:
```csharp
public class RefAccessor<T> : IStatePropertyAccessor<T> where T : class
{
  public RefAccessor(T value)
  {
    Value = value;
  }

  public T Value { get; private set; }

  public string Name => nameof(T);

  public Task<T> GetAsync(ITurnContext turnContext, Func<T> defaultValueFactory = null,
    CancellationToken cancellationToken = default(CancellationToken))
  {
    if (Value == null)
    {
      if (defaultValueFactory == null)
      {
        throw new KeyNotFoundException();
      }
      else
      {
        Value = defaultValueFactory();
      }
    }
    return Task.FromResult(Value);
  }

  public Task DeleteAsync(ITurnContext turnContext,
    CancellationToken cancellationToken = default(CancellationToken))
  {
    throw new NotImplementedException();
  }

  public Task SetAsync(ITurnContext turnContext, T value,
    CancellationToken cancellationToken = default(CancellationToken))
  {
    throw new NotImplementedException();
  }
}
```

## <a name="additional-resources"></a>Zusätzliche Ressourcen
Der in diesem Artikel verwendete [C#](http://aka.ms/scale-out)-Beispielcode ist auf GitHub verfügbar.

