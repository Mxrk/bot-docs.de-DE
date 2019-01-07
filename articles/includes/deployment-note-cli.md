Wenn Sie Dienste wie LUIS (Language Understanding Intelligent Service) verwenden, müssen Sie auch `luisAuthoringKey` übergeben. Wenn Sie eine vorhandene Ressourcengruppe in Azure verwenden möchten, verwenden Sie das `groupName`-Argument mit dem obigen Befehl.

Es wird dringend empfohlen, die Option `verbose` zu verwenden, um die Behandlung von Problemen zu erleichtern, die während der Bereitstellung des Bots auftreten können. Weitere Optionen, die mit dem Befehl `msbot clone services` verwendet werden, werden im Folgenden beschrieben:

| Argumente    | Beschreibung |
|--------------|-------------|
| `folder`     | Speicherort der Datei `bot.recipe`. Standardmäßig wird die Rezeptdatei unter `DeploymentsScript/MSBotClone` erstellt. Diese Datei DARF NICHT GEÄNDERT WERDEN.|
| `location`   | Geografischer Standort, der zum Erstellen der Bot Service-Ressourcen verwendet wird. Beispiele sind „eastus“, „westus“, „westus2“ usw.|
| `proj-file`  | Für einen C#-Bot ist dies die CSPROJ-Datei. Für einen JS-Bot ist dies der Name der Startprojektdatei (z. B. „index.js“) Ihres lokalen Bots.|
| `name`       | Ein eindeutiger Name, der zum Bereitstellen des Bots in Azure verwendet wird. Der Name kann mit dem Namen Ihres lokalen Bots identisch sein. Der Name darf KEINE Leerzeichen oder Unterstriche enthalten.|
| `luisAuthoringKey` | Ihr Erstellungsschlüssel für die entsprechende LUIS-Erstellungsregion für die LUIS-Ressourcen. |

Bevor Azure-Ressourcen erstellt werden können, werden Sie zum Durchführen der Authentifizierung aufgefordert. Folgen Sie den Anweisungen, die auf dem Bildschirm angezeigt werden, um diesen Schritt durchzuführen.

Beachten Sie, dass der oben genannte Schritt _einige Sekunden oder auch Minuten_ in Anspruch nimmt und die Namen der in Azure erstellten Ressourcen geändert werden. Weitere Informationen zur Änderung von Namen finden Sie in der Beschreibung für das [Problem 796](https://github.com/Microsoft/botbuilder-tools/issues/796) im GitHub-Repository.
