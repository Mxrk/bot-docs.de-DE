---
title: Erstellen von datengesteuerten Oberflächen mit Azure Search | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie mit Azure Search datengesteuerte Oberflächen erstellen und Benutzer mit dem Bot Builder SDK für .NET und Azure Search bei der Navigation in großen Mengen von Inhalten in einem Bot unterstützen.
author: matthewshim-ms
ms.author: v-shimma
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 8205f40053b2b3d0e62d9b9ce622f59432e059a4
ms.sourcegitcommit: b78fe3d8dd604c4f7233740658a229e85b8535dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2018
ms.locfileid: "49999007"
---
# <a name="create-data-driven-experiences-with-azure-search"></a>Erstellen von datengesteuerten Oberflächen mit Azure Search 

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

> [!div class="op_single_selector"]
> - [.NET](../dotnet/bot-builder-dotnet-search-azure.md)
> - [Node.js](../nodejs/bot-builder-nodejs-search-azure.md)

Sie können einem Bot [Azure Search](https://azure.microsoft.com/en-us/services/search/) hinzufügen, um Benutzer dabei zu unterstützen, in großen Mengen von Inhalten zu navigieren und eine datengesteuerte Suchoberfläche zu erstellen.

Azure Search ist ein Azure-Dienst, der Stichwortsuche, integrierte Linguistik, benutzerdefinierte Bewertung, Facettennavigation und vieles mehr bietet. Azure Search kann auch Inhalte aus verschiedenen Quellen (einschließlich Azure SQL-Datenbank, DocumentDB, Blob Storage und Table Storage) indizieren. Der Dienst unterstützt die „Pushindizierung“ für andere Datenquellen und kann PDFs, Office-Dokumente und andere Formate mit unstrukturierten Daten öffnen. Nach der Erfassung wird der Inhalt in einem Azure Search-Index gespeichert, den der Bot dann abfragen kann.


## <a name="prerequisites"></a>Voraussetzungen

Installieren Sie das NuGet-Paket [Microsoft.Azure.Search](https://www.nuget.org/packages/Microsoft.Azure.Search/4.0.0-preview) in Ihrem Botprojekt. 

In der Lösung für Ihren Bot sind die folgenden drei C#-Projekte erforderlich. Diese Projekte stellen zusätzliche Funktionen für Bots und Azure Search bereit. Forken Sie die Projekte von [GitHub](https://github.com/Microsoft/botBuilder-Samples/tree/master/CSharp/demo-Search), oder laden Sie den Quellcode direkt herunter.

* [Search.Azure](https://github.com/Microsoft/botBuilder-Samples/tree/master/CSharp/demo-Search/Search.Azure) definiert den Aufruf des Azure-Diensts. 
* [Search.Contracts](https://github.com/Microsoft/botBuilder-Samples/tree/master/CSharp/demo-Search/Search.Contracts) definiert generische Schnittstellen und Datenmodelle, um Daten zu verarbeiten.
* [Search.Dialogs](https://github.com/Microsoft/botBuilder-Samples/tree/master/CSharp/demo-Search/Search.Dialogs) enthält verschiedene generische Bot Builder-Dialoge, die zum Abfragen von Azure Search verwendet werden.

## <a name="configure-azure-search-settings"></a>Konfigurieren der Azure Search-Einstellungen 

Konfigurieren Sie die Azure Search-Einstellungen in der Datei **Web.config** des Projekts, und verwenden Sie dabei in den Wertfeldern Ihre eigenen Azure Search-Anmeldeinformationen. Der Konstruktor in der `AzureSearchClient`-Klasse verwendet diese Einstellungen, um den Bot zu registrieren und an den Azure-Dienst zu binden.

```xml
<appSettings>
    <add key="SearchDialogsServiceName" value="Azure-Search-Service-Name" /> <!-- replace value field with Azure Service Name --> 
    <add key="SearchDialogsServiceKey" value="Azure-Search-Service-Primary-Key" /> <!-- replace value field with Azure Service Key --> 
    <add key="SearchDialogsIndexName" value="Azure-Search-Service-Index" /> <!-- replace value field with your Azure Search Index --> 
</appSettings>
```

## <a name="create-a-search-dialog"></a>Erstellen eines Suchdialogs

Erstellen Sie in Ihrem Botprojekt eine neue `AzureSearchDialog`-Klasse, um den Azure-Dienst in Ihrem Bot aufzurufen. Diese neue Klasse muss die `SearchDialog`-Klasse aus dem **Search.Dialogs**-Projekt erben, das den Großteil der Schwerstarbeit bewältigt. Die Außerkraftsetzung von `GetTopRefiners()` ermöglicht den Benutzern das Eingrenzen/Filtern ihrer Suchergebnisse, ohne die Suche wieder von Anfang an starten zu müssen, wobei der Zustand des Suchobjekts beibehalten wird. Sie können Ihre eigenen benutzerdefinierten Einschränkungen im `TopRefiners`-Array hinzufügen, damit Ihre Benutzer die Möglichkeit haben, ihre Suchergebnisse zu filtern oder einzugrenzen. 

```cs
[Serializable]
public class AzureSearchDialog : SearchDialog
{
    private static readonly string[] TopRefiners = { "refiner1", "refiner2", "refiner3" }; // define your own custom refiners 

    public AzureSearchDialog(ISearchClient searchClient) : base(searchClient, multipleSelection: true)
    {
    }

    protected override string[] GetTopRefiners()
    {
        return TopRefiners;
    }
}
```

## <a name="define-the-response-data-model"></a>Definieren des Datenmodells für die Antwort

Die **SearchHit.cs**-Klasse im `Search.Contracts`-Projekt definiert die relevanten Daten, die aus der Azure Search-Antwort analysiert werden. Die einzigen obligatorischen Einbindungen für Ihren Bot sind die `PropertyBag` IDictionary-Deklaration und die Erstellung im Konstruktor. Alle anderen Eigenschaften in dieser Klasse können Sie entsprechend den Anforderungen Ihres Bots definieren. 

```cs
[Serializable]
public class SearchHit
{
    public SearchHit()
    {
        this.PropertyBag = new Dictionary<string, object>();
    }

    public IDictionary<string, object> PropertyBag { get; set; }

    // customize the fields below as needed 
    public string Key { get; set; }

    public string Title { get; set; }

    public string PictureUrl { get; set; }

    public string Description { get; set; }
}
```

## <a name="after-azure-search-responds"></a>Nach der Antwort von Azure Search 

Bei einer erfolgreichen Abfrage an den Azure-Dienst muss das Suchergebnis analysiert werden, um die relevanten Daten für den Bot abzurufen, die dem Benutzer angezeigt werden. Um dies zu ermöglichen, müssen Sie eine `SearchResultMapper`-Klasse erstellen. Das im Konstruktor erstellte `GenericSearchResult`-Objekt definiert eine Liste und ein Wörterbuch zum Speichern von Ergebnissen und Facets, nachdem jedes Ergebnis entsprechend den Datenmodellen Ihres Bots analysiert wurde. 

Synchronisieren Sie die Eigenschaften in der `ToSearchHit`-Methode gemäß dem Datenmodell in **SearchHit.cs**. Die `ToSearchHit`-Methode wird ausgeführt und generiert ein neues `SearchHit` für jedes in der Antwort gefundene Ergebnis.  

```cs
public class SearchResultMapper : IMapper<DocumentSearchResult, GenericSearchResult>
{
    public GenericSearchResult Map(DocumentSearchResult documentSearchResult)
    {
        var searchResult = new GenericSearchResult();

        searchResult.Results = documentSearchResult.Results.Select(r => ToSearchHit(r)).ToList();
        searchResult.Facets = documentSearchResult.Facets?.ToDictionary(kv => kv.Key, kv => kv.Value.Select(f => ToFacet(f)));

        return searchResult;
    }

    private static GenericFacet ToFacet(FacetResult facetResult)
    {
        return new GenericFacet
        {
            Value = facetResult.Value,
            Count = facetResult.Count.Value
        };
    }

    private static SearchHit ToSearchHit(SearchResult hit)
    {
        return new SearchHit
        {
            // custom properties defined in SearchHit.cs 
            Key = (string)hit.Document["id"],
            Title = (string)hit.Document["title"],
            PictureUrl = (string)hit.Document["thumbnail"],
            Description = (string)hit.Document["description"]
        };
    }
}
```
Nachdem die Ergebnisse analysiert und gespeichert wurden, müssen dem Benutzer die Informationen weiterhin angezeigt werden. Die `SearchHitStyler`-Klasse muss verwaltet werden, um Ihr Datenmodell aus der `SearchHit`-Klasse aufzunehmen. Beispielsweise werden die Eigenschaften `Title`, `PictureUrl` und `Description` aus der **SearchHit.cs**-Klasse im Beispielcode verwendet, um neue Kartenanlagen zu erstellen. Der folgende Code erstellt eine neue Kartenanlage für jedes `SearchHit`-Objekt in der Ergebnisliste `GenericSearchResult`, die dem Benutzer angezeigt wird.   

```cs
[Serializable]
public class SearchHitStyler : PromptStyler
{
    public override void Apply<T>(ref IMessageActivity message, string prompt, IReadOnlyList<T> options, IReadOnlyList<string> descriptions = null)
    {
        var hits = options as IList<SearchHit>;
        if (hits != null)
        {
            var cards = hits.Select(h => new ThumbnailCard
            {
                Title = h.Title,
                Images = new[] { new CardImage(h.PictureUrl) },
                Buttons = new[] { new CardAction(ActionTypes.ImBack, "Pick this one", value: h.Key) },
                Text = h.Description
            });

            message.AttachmentLayout = AttachmentLayoutTypes.Carousel;
            message.Attachments = cards.Select(c => c.ToAttachment()).ToList();
            message.Text = prompt;
        }
        else
        {
            base.Apply<T>(ref message, prompt, options, descriptions);
        }
    }
}
```
Die Suchergebnisse werden dem Benutzer angezeigt, und Sie haben Ihrem Bot Azure Search erfolgreich hinzugefügt.

## <a name="samples"></a>Beispiele

Zwei vollständige Beispiele, die veranschaulichen, wie Azure Search mit Bots mithilfe des Bot Builder SDK für .NET unterstützt wird, finden Sie in GitHub im [RealEstateBot-Beispiel](https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/demo-Search/RealEstateBot) oder [JobListingBot-Beispiel](https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/demo-Search/JobListingBot). 

## <a name="additional-resources"></a>Zusätzliche Ressourcen
* [Azure Search][search]
* [Übersicht über Dialoge](bot-builder-dotnet-dialogs.md)
* [Azure Search-Botbeispiele](https://github.com/Microsoft/botBuilder-Samples/tree/master/CSharp/demo-Search)

[search]: /azure/search/search-what-is-azure-search
