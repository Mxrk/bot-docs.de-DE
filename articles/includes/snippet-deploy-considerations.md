## <a name="application-settings-and-messaging-endpoint"></a>Anwendungseinstellungen und Messaging-Endpunkt

### <a name="verify-application-settings"></a>Überprüfen von Anwendungseinstellungen

Damit Ihr Bot in der Cloud ordnungsgemäß funktioniert, müssen seine Anwendungseinstellungen korrekt sein. Wenn Sie über eine **appID** und ein **password** (Kennwort) verfügen, aktualisieren Sie im Rahmen des Bereitstellungsvorgangs die Werte `Microsoft AppId` und `Microsoft App Password` in den Konfigurationseinstellungen Ihrer Anwendung. Unter [MicrosoftAppID und MicrosoftAppPassword](~/bot-service-manage-overview.md#microsoftappid-and-microsoftapppassword) wird beschrieben, wie Sie die Werte für **AppID** und **AppPassword** für Ihren Bot finden.

> [!TIP]
> [!INCLUDE [Application configuration settings](~/includes/snippet-tip-bot-config-settings.md)]

Wenn Sie Ihren Bot noch nicht beim Bot-Framework registriert haben (und daher noch nicht über **appID** und **password** verfügen), können Sie Ihren Bot mit temporären Platzhalterwerten für diese Einstellungen bereitstellen.
Aktualisieren Sie dann später, nachdem Sie [Ihren Bot registriert](~/bot-service-quickstart-registration.md) haben, die Einstellungen Ihrer bereitgestellten Anwendung mit den Werten für **appID** und **password**, die während der Registrierung für Ihren Bot generiert wurden.

### <a id="messagingEndpoint"></a> Überprüfen des Messaging-Endpunkts

Ihr bereitgestellter Bot muss über einen **Messaging-Endpunkt** verfügen, der Nachrichten vom Bot Framework-Connectordienst empfangen kann.

> [!NOTE]
> Wenn Sie Ihren Bot in Azure bereitstellen, wird automatisch SSL für Ihre Anwendung konfiguriert und dadurch der für Bot Framework erforderliche **Messaging-Endpunkt** aktiviert.
> Wenn die Bereitstellung über einen anderen Clouddienst erfolgt, stellen Sie sicher, dass Ihre Anwendung für SSL konfiguriert ist, damit der Bot über einen **Messaging-Endpunkt** verfügt.
