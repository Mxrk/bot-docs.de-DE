---
title: Testen einer Cortana-Funktion | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie einen Cortana-Bot durch Aufrufen einer Cortana-Funktion testen.
keywords: Bot Builder SDK, Bot registrieren, Cortana
author: v-ducvo
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 05/01/18
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: ceeb854ace1388b6e0435aacc3acf9027763ee73
ms.sourcegitcommit: 3bf3dbb1a440b3d83e58499c6a2ac116fe04b2f6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/23/2018
ms.locfileid: "46707966"
---
# <a name="test-a-cortana-skill"></a>Testen einer Cortana-Funktion

[!INCLUDE [pre-release-label](includes/pre-release-label-v3.md)]
 
Wenn Sie mithilfe des Bot Builder SDK eine Cortana-Funktion erstellt haben, können Sie diese testen, indem Sie sie über Cortana aufrufen. Die folgenden Anweisungen führen Sie durch die erforderlichen Schritte zum Testen Ihrer Cortana-Funktion.

## <a name="register-your-bot"></a>Registrieren Ihres Bots
Wenn Sie Ihren Bot mithilfe von Bot Service in Azure [erstellt haben](~/bot-service-quickstart.md), ist Ihr Bot bereits registriert, und Sie können diesen Schritt überspringen.

Wenn Sie Ihren Bot anderswo bereitgestellt haben oder lokal testen möchten, müssen Sie ihn [registrieren](bot-service-quickstart-registration.md), um ihn mit Cortana zu verbinden. Während des Registrierungsvorgangs müssen Sie den **Messaging-Endpunkt** Ihres Bots angeben. Wenn Sie Ihren Bot lokal testen möchten, müssen Sie eine Tunneling-Software wie [ngrok](http://ngrok.com) ausführen, um einen Endpunkt für Ihren lokalen Bot zu erhalten.

## <a name="get-messaging-endpoint-using-ngrok"></a>Abrufen eines Messaging-Endpunkts mithilfe von ngrok

Wenn Sie den Bot lokal ausführen, können Sie einen Endpunkt für Testzwecke durch Ausführen von Tunneling-Software wie [ngrok](https://ngrok.com) abrufen. Zum Abrufen eines Endpunkts mithilfe von ngrok geben Sie Folgendes in ein Konsolenfenster ein: 

```cmd
 ngrok.exe http 3979 -host-header="localhost:3979"
``` 

Dadurch wird ein ngrok-Weiterleitungslink konfiguriert und angezeigt, der Anforderungen an Ihren Bot weiterleitet, der an Port 3978 ausgeführt wird. Die URL des Weiterleitungslinks sollte in etwa wie folgt aussehen: `https://0d6c4024.ngrok.io`.  Fügen Sie `/api/messages` an den Link an, um eine Messaging-Endpunkt-URL in diesem Format zu bilden: `https://0d6c4024.ngrok.io/api/messages`. 

Geben Sie diese Endpunkt-URL in den Abschnitt **Konfiguration** des Blatts [Einstellungen](~/bot-service-manage-settings.md) Ihres Bots ein.

## <a name="enable-speech-recognition-priming"></a>Aktivieren der Spracherkennungsvorbereitung
Wenn Ihr Bot eine Language Understanding (LUIS)-App nutzt, achten Sie darauf, die LUIS-Anwendungs-ID Ihrem registrierten Bot-Dienst zuzuordnen. Dies hilft Ihrem Bot beim Erkennen von gesprochenen Äußerungen, die in Ihrem LUIS-Modell definiert sind. Weitere Informationen finden Sie unter [Sprachvorbereitung](~/bot-service-manage-speech-priming.md).

## <a name="add-the-cortana-channel"></a>Hinzufügen des Cortana-Kanals
Um Cortana als Kanal hinzuzufügen, führen Sie die Schritte in [Verbinden eines Bots mit Cortana](bot-service-channel-connect-cortana.md) aus.

## <a name="test-using-web-chat-control"></a>Testen mithilfe des Web Chat-Steuerelements

Zum Testen Ihres Bots mithilfe des integrierten Webchat-Steuerelement in Bot Service klicken Sie auf **Testen in Web Chat** und geben eine Nachricht ein, um sicherzustellen, dass Ihr Bot funktioniert.

## <a name="test-using-emulator"></a>Testen mithilfe des Emulators

Zum Testen Ihres Bots mit dem [Emulator](~/bot-service-debug-emulator.md) gehen Sie folgendermaßen vor:

1. Führen Sie den Bot aus.
2. Öffnen Sie den Emulator, und geben Sie die erforderlichen Informationen ein. Wie Sie **AppID** und **AppPassword** für Ihren Bot finden, ist unter [MicrosoftAppID und MicrosoftAppPassword](bot-service-manage-overview.md#microsoftappid-and-microsoftapppassword) beschrieben. 
3. Klicken Sie auf **Verbinden**, um den Emulator mit Ihrem Bot zu verbinden.
4. Geben Sie eine Nachricht ein, um sicherzustellen, dass Ihr Bot funktioniert.

## <a name="test-using-cortana"></a>Testen mithilfe von Cortana
Sie können Ihre Cortana-Funktion aufrufen, indem Sie eine Aufrufphrase an Cortana sprechen. 
1. Öffnen Sie Cortana.
2. Öffnen Sie das Notizbuch in Cortana, und klicken Sie auf **Über mich**, um anzuzeigen, welches Konto Sie für Cortana verwenden. Stellen Sie sicher, dass Sie mit dem Microsoft-Konto angemeldet sind, das Sie zum Registrieren Ihres Bots verwendet haben. 
   ![Anmelden beim Notizbuch von Cortana](~/media/cortana/cortana-notebook.png)
2. Klicken Sie auf die Schaltfläche „Mikrofon“ in der Cortana-App oder in das Suchfeld „Frag mich etwas“ in Windows, und sprechen Sie die [Aufrufphrase][InvocationNameGuidelines] Ihres Bots. Die Aufrufphrase enthält ein *Aufrufnamen*, der die aufzurufende Funktion eindeutig identifiziert. Beispiel: Wenn die Aufrufphrase einer Funktion „Northwind Foto“ ist, kann eine geeignete Aufrufphrase „Bitte Northwind Foto,...“ oder „Sage Northwind Foto, dass...“ enthalten.

   Sie geben den *Aufrufnamen* Ihres Bots an, wenn Sie ihn für Cortana konfigurieren.
   ![Eingeben des Aufrufnamens beim Konfigurieren des Cortana-Kanals](~/media/cortana/cortana-invocation-name-callout.png)

3. Wenn Cortana Ihre Aufrufphrase erkennt, wird Ihr Bot in der Canvas von Cortana gestartet. 

## <a name="troubleshoot"></a>Problembehandlung

Wenn Ihre Cortana-Funktion nicht startet, überprüfen Sie Folgendes:
* Stellen Sie sicher, dass Sie bei Cortana mit dem gleichen Microsoft-Konto angemeldet sind, das Sie zum Registrieren Ihres Bots im Bot Framework-Portal verwendet haben.
* Überprüfen Sie, ob der Bot funktioniert, indem Sie auf **Testen in Web Chat** klicken, um das Fenster **Chat** zu öffnen, und dort eine Nachricht eingeben.
* Überprüfen Sie, ob Ihr Aufrufname den [Richtlinien][InvocationNameGuidelines] entspricht. Wenn Ihr Aufrufname länger als drei Wörter ist, schwer auszusprechen ist, oder wie andere Wörter klingt, hat Cortana möglicherweise Probleme, ihn zu erkennen.
* Wenn Ihre Funktion ein LUIS-Modell verwendet, achten Sie darauf, [die Spracherkennungsvorbereitung zu aktivieren](~/bot-service-manage-speech-priming.md).

Im Artikel zum [Debuggen von Cortana-Funktionen][Cortana-TestBestPractice] finden Sie weitere Tipps zur Problembehandlung sowie Informationen dazu, wie das Debuggen Ihrer Funktion auf dem Cortana-Dashboard aktiviert wird. 


## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie Ihre Cortana-Funktion getestet und sichergestellt haben, dass sie erwartungsgemäß funktioniert, können Sie sie für eine Gruppe von Beta-Testern bereitstellen oder veröffentlichen. Weitere Informationen finden Sie unter [Veröffentlichen von Cortana-Funktionen][Cortana-Publish].

## <a name="additional-resources"></a>Zusätzliche Ressourcen
* [Cortana Skills Kit][CortanaGetStarted]

[CortanaGetStarted]: /cortana/getstarted

[BFPortal]: https://dev.botframework.com/
[CortanaDevCenter]: https://developer.microsoft.com/en-us/cortana

[CortanaSpecificEntities]: https://aka.ms/lgvcto
[CortanaAuth]: https://aka.ms/vsdqcj

[InvocationNameGuidelines]: https://aka.ms/cortana-invocation-guidelines 


[Cortana-Debug]: https://aka.ms/cortana-enable-debug
[Cortana-TestBestPractice]: https://aka.ms/cortana-test-best-practice
[Cortana-Publish]: /cortana/skills/publish-skill
