---
title: Erstellen eines Bots mit Bot Service | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie mit Bot Service – einer integrierten, dedizierten Bot-Entwicklungsumgebung – einen Bot erstellen.
keywords: Schnellstart, Bots erstellen, Bot Service, Web-App-Bot
author: v-ducvo
ms.author: v-ducvo
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 08/13/2018
ms.openlocfilehash: e62d57a39c87e9de32a1a492b2dc1386d6574fd3
ms.sourcegitcommit: bff936a6a3dd5b1bd3ddfeed8bd1023e52929f08
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/30/2018
ms.locfileid: "43312494"
---
::: moniker range="azure-bot-service-3.0"

# <a name="create-a-bot-with-bot-service"></a>Erstellen eines Bots mit Bot Service

[!INCLUDE [pre-release-label](includes/pre-release-label-v3.md)]

Bot Service stellt die Kernkomponenten zum Erstellen von Bots bereit, einschließlich Bot Builder SDK für die Entwicklung von Bots und Bot Framework für die Verbindung von Bots mit Kanälen. Bot Service bietet fünf Vorlagen für das Erstellen von Bots mit Unterstützung für .NET und Node.js bereit. In diesem Thema erfahren Sie, wie Sie Bot Service verwenden, um einen neuen Bot zu erstellen, der das Bot Builder SDK verwendet.

## <a name="log-in-to-azure"></a>Anmelden an Azure
Melden Sie sich beim [Azure-Portal](http://portal.azure.com)an.

> [!TIP]
> Wenn Sie noch kein Abonnement besitzen, können Sie sich für ein <a href="https://azure.microsoft.com/en-us/free/" target="_blank">kostenloses Konto</a> registrieren.

## <a name="create-a-new-bot-service"></a>Erstellen eines neuen Botdiensts

1. Klicken Sie links oben im Azure-Portal auf den Link **Neue Ressource erstellen**, und wählen Sie dann **KI + Machine Learning > Web-App-Bot** aus. 

2. Ein neues Blatt mit Informationen über den **Web-App-Bot** wird geöffnet.  

3. Geben Sie auf dem Blatt **Bot Service** die angeforderten Informationen über Ihren Bot wie in der Tabelle unter der Abbildung angegeben an.  <br/>
   ![Blatt zum Erstellen eines Web-App-Bots](~/media/azure-bot-quickstarts/sdk-create-bot-service-blade.png)

   | Einstellung | Empfohlener Wert | BESCHREIBUNG |
   | ---- | ---- | ---- |
   | **Botname** | Der Anzeigename des Bots | Der Anzeigename für den Bot wird in Kanälen und Verzeichnissen angezeigt. Dieser Name kann jederzeit geändert werden. |
   | **Abonnement** | Ihr Abonnement | Wählen Sie das Azure-Abonnement aus, das Sie verwenden möchten. |
   | **Ressourcengruppe** | myResourceGroup | Sie können eine neue [Ressourcengruppe](/azure/azure-resource-manager/resource-group-overview#resource-groups) erstellen oder eine vorhandene Ressourcengruppe auswählen. |
   | **Location** | Der Standardstandort | Wählen Sie den geografischen Standort für die Ressourcengruppe aus. Sie können in der Liste einen beliebigen Standort auswählen, es ist jedoch oftmals sinnvoll, einen Standort in der Nähe Ihrer Kunden auszuwählen. Der Standort kann nicht mehr geändert werden, nachdem der Bot erstellt wurde. |
   | **Preisstufe** | F0 | Wählen Sie einen Tarif. Sie können den Tarif jederzeit ändern. Weitere Informationen finden Sie unter [Bot Service – Preise](https://azure.microsoft.com/en-us/pricing/details/bot-service/). |
   | **App-Name** | Ein eindeutiger Name | Der eindeutige URL-Name des Bots. Wenn Sie Ihren Bot z.B. *myawesomebot* nennen, lautet die URL Ihres Bots `http://myawesomebot.azurewebsites.net`. Der Name darf ausschließlich alphanumerische Zeichen und den Unterstrich enthalten. Für dieses Feld gilt ein Limit von maximal 35 Zeichen. Der App-Name kann nicht mehr geändert werden, nachdem der Bot erstellt wurde. |
   | **Botvorlage** | Basic | Wählen Sie für diesen Schnellstart **C#** oder **Node.js** und dann die Vorlage **Basic** aus, und klicken Sie auf **Auswählen**. Die Basic-Vorlage erstellt einen Echobot. [Weitere Informationen](bot-service-concept-templates.md) zu den Vorlagen. |
   | **App Service-Plan/Standort** | Ihr App Service-Plan  | Wählen Sie einen Standort für den [App Service-Plan](https://azure.microsoft.com/en-us/pricing/details/app-service/plans/) aus. Sie können in der Liste einen beliebigen Standort auswählen, es ist jedoch oftmals sinnvoll, einen Standort in der Nähe Ihrer Kunden auszuwählen. (Nicht verfügbar für Functions-Bots.) |
   | **Azure Storage** | Ihr Azure Storage-Konto | Sie können ein neues Datenspeicherkonto erstellen oder ein vorhandenes auswählen. Standardmäßig verwendet der Bot [Table Storage](/azure/storage/common/storage-introduction#table-storage). |
   | **Application Insights** | Andererseits | Entscheiden Sie, ob Sie [Application Insights](/bot-framework/bot-service-manage-analytics) **aktivieren** oder **deaktivieren** möchten. Bei Auswahl von **Ein** müssen Sie auch einen regionalen Standort angeben. Sie können in der Liste einen beliebigen Standort auswählen, es ist jedoch oftmals sinnvoll, einen Standort in der Nähe Ihrer Kunden auszuwählen. |
   | **Microsoft-App-ID und Kennwort** | Automatisch erstellte App-ID mit Kennwort | Verwenden Sie diese Option, wenn Sie manuell eine Microsoft-App-ID und ein Kennwort eingeben müssen. Andernfalls werden beim Erstellen des Bots eine neue Microsoft-App-ID und ein Kennwort für Sie erstellt. |

   > [!NOTE]
   > 
   > Bei der Erstellung eines **Functions-Bots** wird das Feld **App Service-Plan/Standort** nicht angezeigt. Stattdessen wird das Feld *Hostingplan* angezeigt. Sie müssen in diesem Fall einen [Hostingplan](bot-service-overview-readme.md#hosting-plans) auswählen.

4. Klicken Sie auf **Erstellen**, um den Dienst zu erstellen und den Bot in der Cloud bereitzustellen. Dieser Vorgang kann mehrere Minuten dauern.

Vergewissern Sie sich, dass der Bot bereitgestellt wurde, indem Sie das Kontrollkästchen **Benachrichtigungen** aktivieren. Die Benachrichtigungen ändern sich von **Die Bereitstellung wird ausgeführt** in **Bereitstellung erfolgreich**. Klicken Sie auf die Schaltfläche **Zu Ressource wechseln**, um das Ressourcenblatt des Bots zu öffnen.

 > [!TIP] 
 > Aus Leistungsgründen wurden **Functions-Bots** mit Node.js-Vorlagen bereits mithilfe des Tools *Azure Functions Pack* gepackt. Das Tool *Azure Functions Pack* fasst alle Node.js-Module in einer *.js-Datei zusammen.
 > Weitere Informationen finden Sie unter [Azure Functions Pack](https://github.com/Azure/azure-functions-pack).
 
## <a name="test-the-bot"></a>Testen des Bots
Nachdem Ihr Bot erstellt wurde, testen Sie ihn im [Webchat](bot-service-manage-test-webchat.md). Geben Sie eine Nachricht ein, und Ihr Bot sollte antworten.

## <a name="next-steps"></a>Nächste Schritte

In diesem Thema haben Sie gelernt, wie Sie einen **einfachen** Web-App-Bot/Functions-Bot mithilfe von Bot Service erstellen. Außerdem haben Sie die Funktionalität Ihres Bots mit dem integrierten Webchat-Steuerelement von Azure überprüft. Nun erfahren Sie, wie Sie Ihren Bot verwalten und mit dem Quellcode arbeiten.

> [!div class="nextstepaction"]
> [Verwalten eines Bots](bot-service-manage-overview.md)

::: moniker-end

::: moniker range="azure-bot-service-4.0"

# <a name="create-a-bot-with-bot-service"></a>Erstellen eines Bots mit Bot Service
[!INCLUDE [pre-release-label](includes/pre-release-label.md)]

Azure Bot Service stellt die Kernkomponenten zum Erstellen von Bots bereit, einschließlich Bot Builder SDK für die Entwicklung von Bots und Bot Service für die Verbindung von Bots mit Kanälen. In diesem Thema können Sie eine .NET- oder Node.js-Vorlage zum Erstellen eines Bots mit der Vorschauversion des Bot Builder SDK v4 auswählen.

## <a name="log-in-to-azure"></a>Anmelden an Azure
Melden Sie sich beim [Azure-Portal](http://portal.azure.com)an.

> [!TIP]
> Wenn Sie noch kein Abonnement besitzen, können Sie sich für ein <a href="https://azure.microsoft.com/en-us/free/" target="_blank">kostenloses Konto</a> registrieren.

## <a name="create-a-new-bot-service"></a>Erstellen eines neuen Botdiensts

1. Klicken Sie links oben im Azure-Portal auf den Link **Neue Ressource erstellen**, und wählen Sie dann **KI + Machine Learning > Web-App-Bot** aus. 

2. Ein neues Blatt mit Informationen über den **Web-App-Bot** wird geöffnet.  

3. Geben Sie auf dem Blatt **Bot Service** die angeforderten Informationen über Ihren Bot wie in der Tabelle unter der Abbildung angegeben an.  <br/>
 ![Blatt zum Erstellen eines Web-App-Bots](~/media/azure-bot-quickstarts/sdk-create-bot-service-blade.png)

 | Einstellung | Empfohlener Wert | BESCHREIBUNG |
 | ---- | ---- | ---- |
 | **Botname** | Der Anzeigename des Bots | Der Anzeigename für den Bot wird in Kanälen und Verzeichnissen angezeigt. Dieser Name kann jederzeit geändert werden. |
 | **Abonnement** | Ihr Abonnement | Wählen Sie das Azure-Abonnement aus, das Sie verwenden möchten. |
 | **Ressourcengruppe** | myResourceGroup | Sie können eine neue [Ressourcengruppe](/azure/azure-resource-manager/resource-group-overview#resource-groups) erstellen oder eine vorhandene Ressourcengruppe auswählen. |
 | **Location** | Der Standardstandort | Wählen Sie den geografischen Standort für die Ressourcengruppe aus. Sie können in der Liste einen beliebigen Standort auswählen, es ist jedoch oftmals sinnvoll, einen Standort in der Nähe Ihrer Kunden auszuwählen. Der Standort kann nicht mehr geändert werden, nachdem der Bot erstellt wurde. |
 | **Preisstufe** | F0 | Wählen Sie einen Tarif. Sie können den Tarif jederzeit ändern. Weitere Informationen finden Sie unter [Bot Service – Preise](https://azure.microsoft.com/en-us/pricing/details/bot-service/). |
 | **App-Name** | Ein eindeutiger Name | Der eindeutige URL-Name des Bots. Wenn Sie Ihren Bot z.B. *myawesomebot* nennen, lautet die URL Ihres Bots `http://myawesomebot.azurewebsites.net`. Der Name darf ausschließlich alphanumerische Zeichen und den Unterstrich enthalten. Für dieses Feld gilt ein Limit von maximal 35 Zeichen. Der App-Name kann nicht mehr geändert werden, nachdem der Bot erstellt wurde. |
 | **Botvorlage** | Basic | Wählen Sie **SDK v4** aus. Wählen Sie für diesen Schnellstart entweder C# oder Node.js und die **Basic**-Vorlage aus, und klicken Sie dann auf **Auswählen**. Die Basic-Vorlage erstellt einen Echobot. 
 | **App Service-Plan/Standort** | Ihr App Service-Plan  | Wählen Sie einen Standort für den [App Service-Plan](https://azure.microsoft.com/en-us/pricing/details/app-service/plans/) aus. Sie können in der Liste einen beliebigen Standort auswählen, es ist jedoch oftmals sinnvoll, einen Standort in der Nähe Ihrer Kunden auszuwählen. |
 | **Azure Storage** | Ihr Azure Storage-Konto | Sie können ein neues Datenspeicherkonto erstellen oder ein vorhandenes auswählen. Standardmäßig verwendet der Bot [Table Storage](/azure/storage/common/storage-introduction#table-storage). |
 | **Application Insights** | Andererseits | Entscheiden Sie, ob Sie [Application Insights](/bot-framework/bot-service-manage-analytics) **aktivieren** oder **deaktivieren** möchten. Bei Auswahl von **Ein** müssen Sie auch einen regionalen Standort angeben. Sie können in der Liste einen beliebigen Standort auswählen, es ist jedoch oftmals sinnvoll, einen Standort in der Nähe Ihrer Kunden auszuwählen. |
 | **Microsoft-App-ID und Kennwort** | Automatisch erstellte App-ID mit Kennwort | Verwenden Sie diese Option, wenn Sie manuell eine Microsoft-App-ID und ein Kennwort eingeben müssen. Andernfalls werden beim Erstellen des Bots eine neue Microsoft-App-ID und ein Kennwort für Sie erstellt. |

4. Klicken Sie auf **Erstellen**, um den Dienst zu erstellen und den Bot in der Cloud bereitzustellen. Dieser Vorgang kann mehrere Minuten dauern.

Vergewissern Sie sich, dass der Bot bereitgestellt wurde, indem Sie das Kontrollkästchen **Benachrichtigungen** aktivieren. Die Benachrichtigungen ändern sich von **Die Bereitstellung wird ausgeführt** in **Bereitstellung erfolgreich**. Klicken Sie auf die Schaltfläche **Zu Ressource wechseln**, um das Ressourcenblatt des Bots zu öffnen.

## <a name="test-the-bot"></a>Testen des Bots
Nachdem Ihr Bot erstellt wurde, testen Sie ihn im [Webchat](bot-service-manage-test-webchat.md). Geben Sie eine Nachricht ein, und Ihr Bot sollte antworten.

## <a name="next-steps"></a>Nächste Schritte

In diesem Thema haben Sie gelernt, wie Sie einen **einfachen** Web-App-Bot mithilfe von Bot Service erstellen. Außerdem haben Sie die Funktionalität Ihres Bots mit dem integrierten Webchat-Steuerelement von Azure überprüft. Nun erfahren Sie, wie Sie Ihren Bot verwalten und mit dem Quellcode arbeiten.

> [!div class="nextstepaction"]
> [Verwalten eines Bots](bot-service-manage-overview.md)

::: moniker-end
