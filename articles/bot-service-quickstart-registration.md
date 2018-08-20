---
title: Erstellen einer Botkanalregistrierung mit Bot Service | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie einen vorhandenen Bot bei Bot Service registrieren.
author: v-ducvo
ms.author: v-ducvo
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 6a32bc5712937c615962e4f6edfc7ea691d3ec39
ms.sourcegitcommit: 67445b42796d90661afc643c6bb6533e9a662cbc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/06/2018
ms.locfileid: "39574976"
---
# <a name="register-a-bot-with-bot-service"></a>Registrieren eines Bots bei Bot Service

[!INCLUDE [pre-release-label](includes/pre-release-label-v3.md)]

Wenn Sie über einen Bot verfügen, der bereits an anderer Stelle gehostet wird, und Sie den Bot über den Bot Service mit anderen Kanälen verbinden möchten, müssen Sie Ihren Bot bei Bot Service registrieren. In diesem Thema erfahren Sie, wie Sie Ihren Bot bei Bot Service registrieren, indem Sie einen Botdienst zur **Botkanalregistrierung** erstellen.

> [!IMPORTANT] 
> Sie müssen Ihren Bot nur registrieren, wenn er nicht in Azure gehostet wird. Beim [Erstellen eines Bots](bot-service-quickstart.md) über das Azure-Portal wird dieser bei Bot Service registriert.

## <a name="log-in-to-azure"></a>Anmelden an Azure
Melden Sie sich beim [Azure-Portal](http://portal.azure.com)an.

> [!TIP]
> Wenn Sie noch kein Abonnement besitzen, können Sie sich für ein <a href="https://azure.microsoft.com/en-us/free/" target="_blank">kostenloses Konto</a> registrieren.

## <a name="create-a-bot-channels-registration"></a>Erstellen einer Botkanalregistrierung
Sie benötigen einen Botdienst zur **Botkanalregistrierung**, um die Bot Service-Funktion nutzen zu können. Mit einem Registrierungsbot können Sie Ihren Bot mit Kanälen verbinden.

Gehen Sie wie folgt vor, um eine **Botkanalregistrierung** zu erstellen:

1. Klicken Sie in der linken oberen Ecke des Azure-Portals auf die Schaltfläche **Neu**, und wählen Sie dann **KI + Cognitive Services > Bot Channels Registration** (Botkanalregistrierung) aus. 

2. Ein neues Blatt mit Informationen über die **Botkanalregistrierung** wird geöffnet. Klicken Sie auf die Schaltfläche **Erstellen**, um den Erstellungsprozess zu starten. 

3. Geben Sie auf dem Blatt **Bot Service** die angeforderten Informationen über Ihren Bot wie in der Tabelle unter der Abbildung angegeben an.  <br/>
   ![Blatt zum Erstellen eines Registrierungsbots](~/media/azure-bot-quickstarts/registration-create-bot-service-blade.png)


   |                    Einstellung                     |         Empfohlener Wert         |                                                                                                  BESCHREIBUNG                                                                                                  |
   |------------------------------------------------|---------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
   |           <strong>Botname</strong>            |     Der Anzeigename des Bots     |                                                  Der Anzeigename für den Bot wird in Kanälen und Verzeichnissen angezeigt. Dieser Name kann jederzeit geändert werden.                                                  |
   |         <strong>Abonnement</strong>          |        Ihr Abonnement        |                                                                                Wählen Sie das Azure-Abonnement aus, das Sie verwenden möchten.                                                                                 |
   |        <strong>Ressourcengruppe</strong>         |         myResourceGroup         |                                 Sie können eine neue [Ressourcengruppe](/azure/azure-resource-manager/resource-group-overview#resource-groups) erstellen oder eine vorhandene Ressourcengruppe auswählen.                                  |
   |                    Standort                    |             USA (Westen)             |                                                        Wählen Sie einen Standort in der Nähe des Standorts, an dem Ihr Bot bereitgestellt wird, oder in der Nähe anderer Dienste aus, auf die Ihr Bot zugreift.                                                         |
   |         <strong>Tarif</strong>          |               F0                |             Wählen Sie einen Tarif. Sie können den Tarif jederzeit ändern. Weitere Informationen finden Sie unter [Bot Service – Preise](https://azure.microsoft.com/en-us/pricing/details/bot-service/).              |
   |      <strong>Messaging-Endpunkt</strong>       |               URL               |                                                                               Geben Sie die URL für den Messaging-Endpunkt Ihres Bots ein.                                                                                |
   |     <strong>Application Insights</strong>      |               Andererseits                | Entscheiden Sie, ob Sie [Application Insights](bot-service-manage-analytics.md) <strong>aktivieren</strong> oder <strong>deaktivieren</strong> möchten. Wenn Sie <strong>Ein</strong> auswählen, müssen Sie auch einen regionalen Standort angeben. |
   | <strong>Microsoft-App-ID und Kennwort</strong> | Automatisch erstellte App-ID mit Kennwort |              Verwenden Sie diese Option, wenn Sie manuell eine Microsoft-App-ID und ein Kennwort eingeben müssen. Andernfalls werden beim Erstellen des Bots eine neue Microsoft-App-ID und ein Kennwort für Sie erstellt.               |


4. Klicken Sie auf **Erstellen**, um den Dienst zu erstellen und den Messaging-Endpunkt Ihres Bots zu registrieren.

Vergewissern Sie sich, ob die Registrierung erstellt wurde, indem Sie das Kontrollkästchen **Benachrichtigungen** aktivieren. Die Benachrichtigungen ändern sich von **Die Bereitstellung wird ausgeführt** in **Bereitstellung erfolgreich**. Klicken Sie auf die Schaltfläche **Zu Ressource wechseln**, um das Ressourcenblatt des Bots zu öffnen. 

## <a name="bot-channels-registration-password"></a>Kennwort für die Botkanalregistrierung

Dem Botdienst **Botkanalregistrierung** ist kein App-Dienst zugeordnet. Daher weist dieser Botdienst nur eine *MicrosoftAppID* auf. Sie müssen das Kennwort selbst manuell generieren und speichern. Dieses Kennwort benötigen Sie, wenn Sie Ihren Bot mit dem [Emulator](bot-service-debug-emulator.md) testen möchten.

Gehen Sie wie folgt vor, um ein MicrosoftAppPassword zu generieren:

1. Klicken Sie auf dem Blatt **Einstellungen** auf **Verwalten**. Dieser Link wird je nach **Microsoft-App-ID** angezeigt. Über diesen Link können Sie ein Fenster öffnen, über das Sie ein neues Kennwort generieren können. <br/>
  ![Link „Verwalten“ auf dem Blatt „Einstellungen“](~/media/azure-bot-quickstarts/registration-settings-manage-link.png)

2. Klicken Sie auf **Neues Kennwort generieren**. Dadurch wird ein neues Kennwort für Ihren Bot generiert. Kopieren Sie dieses Kennwort, und speichern Sie es in einer Datei. Dies ist die einzige Gelegenheit, bei der dieses Kennwort angezeigt wird. Wenn Sie das Kennwort zu einem späteren Zeitpunkt benötigen und nicht vollständig gespeichert haben, müssen Sie den Vorgang zum Erstellen eines neuen Kennworts wiederholen. <br/>
  ![Generieren eines Microsoft-App-Kennworts](~/media/azure-bot-quickstarts/registration-generate-app-password.png)

## <a name="update-the-bot"></a>Aktualisieren des Bots

Wenn Sie das Bot Builder SDK für Node.js verwenden, legen Sie die folgenden Umgebungsvariablen fest:

* MICROSOFT_APP_ID
* MICROSOFT_APP_PASSWORD

Wenn Sie das Bot Builder SDK für .NET verwenden, legen Sie die folgenden Schlüsselwerte in der Datei „web.config“ fest:

* MicrosoftAppId
* MicrosoftAppPassword

## <a name="test-the-bot"></a>Testen des Bots

Nachdem Ihr Botdienst erstellt wurde, [testen Sie ihn im Webchat](bot-service-manage-test-webchat.md). Wenn Sie eine Nachricht eingeben, sollte Ihr Bot antworten.

## <a name="next-steps"></a>Nächste Schritte

In diesem Thema haben Sie erfahren, wie Sie Ihren gehosteten Bot beim Bot Service registrieren. Im nächste Schritt erfahren Sie, wie Sie Ihren Botdienst verwalten.

> [!div class="nextstepaction"]
> [Verwalten eines Bots](bot-service-manage-overview.md)

