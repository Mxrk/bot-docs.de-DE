---
title: Erstellen von datengesteuerten Oberflächen mit Azure Search | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie mit Azure Search datengesteuerte Oberflächen erstellen und Benutzer mit dem Bot Builder SDK für Node.js und Azure Search bei der Navigation in großen Mengen von Inhalten in einem Bot unterstützen.
author: matthewshim-ms
ms.author: v-shimma
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: e9f07cdd4616a2649dca31f096eca3377cd46b7d
ms.sourcegitcommit: 2dc75701b169d822c9499e393439161bc87639d2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/24/2018
ms.locfileid: "42904234"
---
# <a name="create-data-driven-experiences-with-azure-search"></a>Erstellen von datengesteuerten Oberflächen mit Azure Search 

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

> [!div class="op_single_selector"]
> - [.NET](../dotnet/bot-builder-dotnet-search-azure.md)
> - [Node.js](../nodejs/bot-builder-nodejs-search-azure.md)

Sie können Ihrem Bot [Azure Search][search] hinzufügen, um den Benutzer dabei zu unterstützen, in großen Mengen von Inhalten zu navigieren und eine datengesteuerte Suchoberfläche für Benutzer Ihres Bots zu erstellen.

Azure Search ist ein Azure-Dienst, der Schlüsselwortsuche, integrierte Linguistik, benutzerdefinierte Bewertung, Facettennavigation und vieles mehr bietet. Azure Search kann auch Inhalte aus verschiedenen Quellen (einschließlich Azure SQL-Datenbank, DocumentDB, Blob Storage und Table Storage) indizieren. Der Dienst unterstützt die „Pushindizierung“ für andere Datenquellen und kann PDFs, Office-Dokumente und andere Formate mit unstrukturierten Daten öffnen. Nach der Erfassung wird der Inhalt in einem Azure Search-Index gespeichert, den der Bot dann abfragen kann.

## <a name="install-dependencies"></a>Installieren von Abhängigkeiten

Navigieren Sie über eine Eingabeaufforderung zum Projektverzeichnis Ihres Bots, und installieren Sie mit Node.js Package Manager (npm) die folgenden Module:

* [bluebird](https://www.npmjs.com/package/bluebird)
* [lodash](https://www.npmjs.com/package/lodash)
* [Anforderung](https://www.npmjs.com/package/request)

## <a name="prerequisites"></a>Voraussetzungen

Im Folgenden werden die **erforderlichen** Komponenten aufgeführt: 
- Sie verfügen über ein Azure-Abonnement und einen Azure Search-Primärschlüssel. Diese finden Sie im Azure-Portal.
- Sie haben die [SearchDialogLibrary](https://github.com/Microsoft/botBuilder-Samples/tree/master/Node/demo-Search/SearchDialogLibrary)-Bibliothek in das Projektverzeichnis Ihres Bots kopiert. Diese Bibliothek enthält allgemeine Dialoge, die der Benutzer durchsuchen kann, die jedoch nach Bedarf entsprechend Ihres Bots angepasst werden können. 

- Sie haben die [SearchProviders](https://github.com/Microsoft/botBuilder-Samples/tree/master/Node/demo-Search/SearchProviders)-Bibliothek in das Projektverzeichnis Ihres Bots kopiert. Diese Bibliothek enthält alle Komponenten, die zum Erstellen einer Anforderung und zum Senden an Azure Search erforderlich sind.

## <a name="connect-to-the-azure-service"></a>Herstellen der Verbindung mit dem Azure-Dienst 

Erstellen Sie in der Hauptprogrammdatei Ihres Bots (z.B. „app.js“) die Verweispfade zu den beiden Bibliotheken, die Sie zuvor installiert haben. 

```javascript
var SearchLibrary = require('./SearchDialogLibrary');
var AzureSearch = require('./SearchProviders/azure-search');
```

Fügen Sie Ihrem Bot den folgenden Beispielcode hinzu: Übergeben Sie im `AzureSearch`-Objekt Ihre eigenen Azure Search-Einstellungen in der `.create`-Methode. Ihr Bot wird zur Laufzeit an den Azure Search-Dienst gebunden und wartet auf eine abgeschlossene Benutzerabfrage in Form eines `Promise`.  

```javascript
// Azure Search
var azureSearchClient = AzureSearch.create('Your-Azure-Search-Service-Name', 'Your-Azure-Search-Primary-Key', 'Your-Azure-Search-Service-Index');
var ResultsMapper = SearchLibrary.defaultResultsMapper(ToSearchHit);
```

 Der `azureSearchClient`-Verweis erstellt das Azure Search-Modell, in dem die Autorisierungseinstellungen für den Azure-Dienst über die `.env`-Einstellungen des Bots übergeben werden. 
 `ResultsMapper` analysiert das Azure-Antwortobjekt und ordnet die Daten zu, während wir diese in der `ToSearchHit`-Methode definieren. Eine beispielhafte Implementierung dieser Methode finden Sie unter [Nach einer Antwort von Azure Search](#after-azure-search-responds).

## <a name="register-the-search-library"></a>Registrieren der Suchbibliothek
Sie können Ihre Suchdialoge direkt im `SearchLibrary`-Modul selbst anpassen. Die `SearchLibrary` führt den Großteil der aufwendigen Arbeit durch, einschließlich des Aufrufs an Azure Search. 

Fügen Sie den folgenden Code in die Hauptprogrammdatei Ihres Bots ein, um die Suchbibliothek für Dialoge bei Ihrem Bot zu registrieren. 

```javascript
bot.library(SearchLibrary.create({
    multipleSelection: true,
    search: function (query) { return azureSearchClient.search(query).then(ResultsMapper); },
    refiners: ['refiner1', 'refiner2', 'refiner3'], // customize your own refiners 
    refineFormatter: function (refiners) {
        return _.zipObject(
            refiners.map(function (r) { return 'By ' + _.capitalize(r); }),
            refiners);
    }
}));
```
Die `SearchLibrary` speichert nicht nur alle Dialoge im Zusammenhang mit der Suche, sondern verwendet die Benutzerabfrage, die an Azure Search gesendet werden soll. Sie müssen Ihre eigenen Einschränkungen im `refiners`-Array definieren, um Entitäten anzugeben, mit denen Ihr Benutzer seine Suchergebnisse eingrenzen oder filtern kann.  

## <a name="create-a-search-dialog"></a>Erstellen eines Suchdialogs

Sie können Ihre Dialoge beliebig strukturieren. Die einzige Voraussetzung zum Einrichten eines Azure Search-Dialogs besteht darin, die `.begin`-Methode über das `SearchLibrary`-Objekt aufzurufen und dabei das `session`-Objekt zu übergeben, das vom Bot Builder SDK generiert wurde. 

```javascript
function (session) {
        // Trigger Azure Search dialogs 
        SearchLibrary.begin(session);
    },
    function (session, args) {
        // Process selected search results
        session.send(
            'Search Completed!',
            args.selection.map(  ); // format your response 
    }
```
Weitere Informationen zu Dialogen finden Sie unter [Verwalten von Konversationen mithilfe von Dialogen](bot-builder-nodejs-dialog-manage-conversation.md).

## <a name="after-azure-search-responds"></a>Nach der Antwort von Azure Search 

Nachdem eine Azure Search-Suche erfolgreich aufgelöst wurde, müssen Sie die gewünschten Daten aus dem Antwortobjekt speichern und auf sinnvolle Weise für den Benutzer darstellen.

> [!TIP]
> Ziehen Sie die Einbeziehung des [util-Moduls][NodeUtil] in Erwägung. Dies vereinfacht das Formatieren und Zuordnen der Antwort über Azure Search.

Erstellen Sie in der Hauptprogrammdatei Ihres Bots eine `ToSearchHit`-Methode. Diese Methode gibt ein Objekt zurück, das die relevanten Daten aus der Azure-Antwort formatiert. Der folgende Code zeigt, wie Sie Ihre eigenen Parameter in der `ToSearchHit`-Methode definieren können. 
 
 ```javascript
 function ToSearchHit(azureResponse) {
     return {
         // define your own parameters 
         key: azureResponse.id,
         title: azureResponse.title,
         description: azureResponse.description,
         imageUrl: azureResponse.thumbnail
     };
 }
```
Nachdem dies geschehen ist, müssen Sie lediglich die Daten für den Benutzer anzeigen. 

 In der Datei **index.js** des Projekts **SearchDialogLibrary** analysiert die `searchHitAsCard`-Methode jede Antwort von Azure Search und erstellt ein neues Kartenobjekt, das dem Benutzer angezeigt werden soll. Die Felder, die Sie in der `ToSearchHit`-Methode über die Hauptprogrammdatei Ihres Bots definiert haben, muss mit den Eigenschaften in der `searchHitAsCard`-Methode synchronisiert werden. 

Das folgende Beispiel zeigt, wie und an welcher Stelle Ihre definierten Parameter aus der `ToSearchHit`-Methode zum Erstellen einer Benutzeroberfläche zum Anfügen von Karten verwendet werden, die für den Benutzer gerendert werden sollen. 

```javascript
function searchHitAsCard(showSave, searchHit) {
    var buttons = showSave
        ? [new builder.CardAction().type('imBack').title('Save').value(searchHit.key)]
        : [];

    var card = new builder.HeroCard()
        .title(searchHit.title) 
        .buttons(buttons);

    if (searchHit.description) {
        card.subtitle(searchHit.description);
    }

    if (searchHit.imageUrl) {
        card.images([new builder.CardImage().url(searchHit.imageUrl)]);
    }

    return card;
}
```

## <a name="sample-code"></a>Beispielcode

Zwei vollständige Beispiele, die veranschaulichen, wie Azure Search mit Bots mithilfe des Bot Builder SDK für Node.js unterstützt wird, finden Sie in GitHub im [RealEstateBot-Beispiel](https://github.com/Microsoft/BotBuilder-Samples/tree/master/Node/demo-Search/RealEstateBot) oder [JobListingBot-Beispiel](https://github.com/Microsoft/BotBuilder-Samples/tree/master/Node/demo-Search/JobListingBot). 

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Azure Search][search]
* [Knoten „Util“][NodeUtil]
* [Dialoge](bot-builder-nodejs-dialog-manage-conversation.md)

[NodeUtil]: https://nodejs.org/api/util.html
[search]: /azure/search/search-what-is-azure-search
