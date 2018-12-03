---
title: Migrieren eines C#-Bots aus einem Verbrauchsplan zu einem App Service-Plan | Microsoft-Dokumentation
description: Migrieren Sie einen Bot Service-C#-Bot aus einem verbrauchsbasierten Hostingplan zu einem App Service-Hostingplan.
author: v-ducvo
ms.author: v-ducvo
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: abs
ms.date: 12/13/2017
ms.openlocfilehash: aafbfb2a38e2d5370cb2db5721dd7bc130497d74
ms.sourcegitcommit: b78fe3d8dd604c4f7233740658a229e85b8535dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2018
ms.locfileid: "49999217"
---
# <a name="change-the-hosting-plan-for-your-bot-service"></a>Ändern des Hostingplans für den Botdienst

In diesem Thema wird erläutert, wie Sie einen C#-Skriptbot mit einem Verbrauchsplan in einen C#-Bot mit einem App Service-Plan migrieren können. 

## <a name="advantages-of-a-bot-on-an-app-service-plan"></a>Vorteile eines Bots unter einem App Service-Plan

Bots unter einem App Service-Plan werden als Azure-Web-Apps ausgeführt. Web-App-Bots haben Möglichkeiten, über die Bots unter einem Verbrauchsplan nicht verfügen:

- Ein Web-App-Bot kann benutzerdefinierte route-Definitionen hinzufügen.
- Ein Web-App-Bot kann einen WebSocket-Server aktivieren. 
- Für einen Bot, der einen verbrauchsbasierten Hostingplan nutzt, gelten dieselben Einschränkungen wie für den gesamten Code, der unter Azure Functions ausgeführt wird. Weitere Informationen finden Sie unter <a target='_blank' href='/azure/azure-functions/functions-scale'>Verbrauchs- und App Service-Pläne für Azure Functions</a>.

## <a name="download-your-existing-bot-source"></a>Herunterladen des Quellcodes des vorhandenen Bots

Gehen Sie wie folgt vor, um den Quellcode des vorhandenen Bots herunterzuladen:

1. Klicken Sie im Azure-Bot auf die Registerkarte **Einstellungen**, und erweitern Sie den Abschnitt **Continuous Deployment**.  
2. Klicken Sie auf die blaue Schaltfläche, um die ZIP-Datei herunterzuladen, die den Quellcode für Ihren Bot enthält.  
    ![Herunterladen der ZIP-Datei für den Bot](~/media/continuous-deployment-consumption-download.png)
3. Extrahieren Sie den Inhalt der heruntergeladenen ZIP-Datei in einen lokalen Ordner. 


## <a name="create-a-bot-template"></a>Erstellen einer Bot-Vorlage

Bot Service verwendet für Bots unter dem Verbrauchsplan dieselben Vorlagen wie für Bots unter dem App Service-Plan. Erstellen Sie zum Migrieren eines Verbrauchsplan-Bots auf der Grundlage derselben Vorlage einen neuen App Service-Plan-Bot in Bot Service. Der zugrunde liegende Code kann zwischen den beiden Hostingtypen trotz gleicher Vorlage unterschiedlich sein. Die neue Web-App weist jedoch eine ähnliche Struktur und ähnliche Konfigurationsfunktionen wie der vorhandene Bot auf.

## <a name="download-the-new-bot-source"></a>Herunterladen des Quellcodes des neuen Bots

Gehen Sie wie folgt vor, um den Quellcode des neuen Bots herunterzuladen:

1. Klicken Sie in Ihrem Azure-Bot auf die Registerkarte **BUILD**, suchen Sie den Abschnitt **Quellcode herunterladen**, und klicken Sie auf **ZIP-Datei herunterladen**. 
2. Extrahieren Sie den Inhalt der heruntergeladenen ZIP-Datei in den lokalen Ordner.

## <a name="add-source-files-to-new-solution"></a>Hinzufügen von Quelldateien zu einer neuen Projektmappe

Einige CSX-Dateien werden in der neuen Projektmappe möglicherweise als CS-Dateien kompiliert und ausgeführt. Erstellen Sie für jede CSX-Datei in Ihrer Projektmappe eine CS-Datei, außer für `run.csx`. Die Logik `run.csx` migrieren Sie manuell. Möglicherweise müssen Sie Ihren CS-Dateien eine Klassendeklaration und optional eine Namespacedeklaration hinzufügen.

## <a name="migrate-runcsx-logic-into-your-project"></a>Migrieren der „run.csx“-Logik in Ihr Projekt

C#-Skriptprojekte enthalten eine `Run`-Methode, in der unterschiedliche `ActivityTypes`-Werte behandelt werden. Importieren Sie die Aktivität, die die Logik behandelt, in `MessageController.cs` in die `MessageController.Post`-Methode.

## <a name="remove-compiler-keywords"></a>Entfernen von Compilerschlüsselwörtern

C#-Skriptdateien können ein Verweismodul enthalten, das das `#r`-Schlüsselwort verwendet. Entfernen Sie diese Zeilen, und fügen Sie sie Ihrem Visual Studio-Projekt als Verweise hinzu. Entfernen Sie darüber hinaus auch `#load`-Schlüsselwörter, die andere Quellcodedateien in eine Dateikompilierung einfügen. Fügen Sie stattdessen dem Projekt alle `.csx`-Dateien als `.cs`-Quellcode hinzu.

## <a name="add-references-from-projectjson"></a>Hinzufügen von Verweisen aus „project.json“

Wenn Ihr Verbrauchsplan-Bot NuGet-Verweise in der `project.json`-Datei hinzufügt, fügen Sie diese Verweise Ihrer neuen Visual Studio-Projektmappe hinzu, indem Sie im Bereich „Projektmappen-Explorer“ mit der rechten Maustaste auf das Projekt klicken und anschließend auf **Verweis hinzufügen** klicken.

### <a name="add-references-that-were-implicit"></a>Hinzufügen von impliziten Verweisen

Ein Bot Service-Bot in einem Verbrauchsplan fügt diese Verweise in allen CSX-Dateien implizit hinzu. Für Quellcode, der in CS-Quelldateien migriert wird, müssen für folgende Klassen möglicherweise explizite Verweise hinzugefügt werden:

- `TraceWriter` im NuGet-Pakte `Microsoft.Azure.WebJobs`, das die `Microsoft.Azure.WebJobs.Host`-Namespacetypen bereitstellt. 
- Zeitgebertrigger im NuGet-Paket `Microsoft.Azure.WebJobs.Extensions`
- `Newtonsoft.Json`, `Microsoft.ServiceBus` und andere automatisch referenzierte Assemblys
- `System.Threading.Tasks` und andere automatisch importierte Namespaces

Weitere Anleitungen finden Sie im Abschnitt *Converting to class files (Konvertieren in Klassendateien)* unter <a target='_blank' href='https://blogs.msdn.microsoft.com/appserviceteam/2017/03/16/publishing-a-net-class-library-as-a-function-app/'>Publishing a .NET class library as a Function App (Veröffentlichen einer .NET-Klassenbibliothek als Funktionen-App)</a>.

## <a name="debug-your-new-bot"></a>Debuggen des neuen Bots

Ein Bot unter einem App Service-Plan lässt sich wesentlich einfacher lokal debuggen als ein Bot unter einem Verbrauchsplan. Sie können den [Emulator](bot-service-debug-emulator.md) verwenden, um den migrierten Code lokal zu debuggen.

## <a name="publish-from-visual-studio-or-set-up-continuous-deployment"></a>Veröffentlichen über Visual Studio oder Einrichten von Continuous Deployment

Veröffentlichen Sie den migrierten Quellcode in Bot Service, indem Sie entweder die zugehörige `.PublishSettings`-Datei importieren und auf **Veröffentlichen** klicken oder indem Sie [Continuous Deployment einrichten](bot-service-debug-bot.md).
