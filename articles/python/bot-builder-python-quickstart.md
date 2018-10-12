---
title: Erstellen eines Bots mit dem Bot Builder SDK für Python | Microsoft-Dokumentation
description: Erstellen Sie schnell einen Bot mithilfe des Bot Builder SDK für Python.
keywords: Bot Builder SDK, Bot erstellen, Schnellstart, Python, erste Schritte
author: jonathanfingold
ms.author: jonathanfingold
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 08/30/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 6b63fe2780c51e57ee16c5e3dba5a83f46566157
ms.sourcegitcommit: 3bf3dbb1a440b3d83e58499c6a2ac116fe04b2f6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/23/2018
ms.locfileid: "46707278"
---
# <a name="create-a-bot-with-the-bot-builder-sdk-for-python"></a>Erstellen eines Bots mit dem Bot Builder SDK für Python

>[!NOTE] 
> Das Python SDK befindet sich in der **Vorschauphase**. Weitere Informationen finden Sie im [GitHub-Repository](https://github.com/Microsoft/botbuilder-python) für Python. 

Dieser Schnellstart führt Sie durch die Schritte zum Erstellen eines Bots und anschließenden Testen mit dem Bot Framework-Emulator. 

## <a name="pre-requisite"></a>Voraussetzung
- [Python 3.6.4](https://www.python.org/downloads/) 
- [Bot Framework-Emulator](https://github.com/Microsoft/BotFramework-Emulator/releases)

## <a name="create-a-bot"></a>Erstellen eines Bots
Importieren Sie die folgenden Standardmodule in die Datei „main.py“:

```python
import http.server
import json
import asyncio
```

Importieren Sie außerdem die folgenden SDK-Module:
```python
from botbuilder.schema import (Activity, ActivityTypes)
from botframework.connector import ConnectorClient
from botframework.connector.auth import (MicrosoftAppCredentials,
                                         JwtTokenValidation, SimpleCredentialProvider)
```
Fügen Sie als Nächstes den folgenden Code zum Erstellen des Bots mithilfe von ConnectorClient hinzu:
```python
APP_ID = ''
APP_PASSWORD = ''


class BotRequestHandler(http.server.BaseHTTPRequestHandler):

    @staticmethod
    def __create_reply_activity(request_activity, text):
        return Activity(
            type=ActivityTypes.message,
            channel_id=request_activity.channel_id,
            conversation=request_activity.conversation,
            recipient=request_activity.from_property,
            from_property=request_activity.recipient,
            text=text,
            service_url=request_activity.service_url)

    def __handle_conversation_update_activity(self, activity):
        self.send_response(202)
        self.end_headers()
        if activity.members_added[0].id != activity.recipient.id:
            credentials = MicrosoftAppCredentials(APP_ID, APP_PASSWORD)
            reply = BotRequestHandler.__create_reply_activity(activity, 'Hello and welcome to the echo bot!')
            connector = ConnectorClient(credentials, base_url=reply.service_url)
            connector.conversations.send_to_conversation(reply.conversation.id, reply)

    def __handle_message_activity(self, activity):
        self.send_response(200)
        self.end_headers()
        credentials = MicrosoftAppCredentials(APP_ID, APP_PASSWORD)
        connector = ConnectorClient(credentials, base_url=activity.service_url)
        reply = BotRequestHandler.__create_reply_activity(activity, 'You said: %s' % activity.text)
        connector.conversations.send_to_conversation(reply.conversation.id, reply)

    def __handle_authentication(self, activity):
        credential_provider = SimpleCredentialProvider(APP_ID, APP_PASSWORD)
        loop = asyncio.new_event_loop()
        try:
            loop.run_until_complete(JwtTokenValidation.authenticate_request(
                activity, self.headers.get("Authorization"), credential_provider))
            return True
        except Exception as ex:
            self.send_response(401, ex)
            self.end_headers()
            return False
        finally:
            loop.close()

    def __unhandled_activity(self):
        self.send_response(404)
        self.end_headers()

    def do_POST(self):
        body = self.rfile.read(int(self.headers['Content-Length']))
        data = json.loads(str(body, 'utf-8'))
        activity = Activity.deserialize(data)

        if not self.__handle_authentication(activity):
            return

        if activity.type == ActivityTypes.conversation_update.value:
            self.__handle_conversation_update_activity(activity)
        elif activity.type == ActivityTypes.message.value:
            self.__handle_message_activity(activity)
        else:
            self.__unhandled_activity()


try:
    SERVER = http.server.HTTPServer(('localhost', 9000), BotRequestHandler)
    print('Started http server on localhost:9000')
    SERVER.serve_forever()
except KeyboardInterrupt:
    print('^C received, shutting down server')
    SERVER.socket.close()
```


Speichern Sie „main.py“. Geben Sie zum Ausführen des Beispiels unter Windows den folgenden Befehl im Befehlszeilenfenster ein:
```
python main.py
```
In Ihrem lokalen Terminal sollte die Meldung „Started http server on localhost:9000“ (HTTP-Server auf localhost:9000 gestartet) angezeigt werden.

## <a name="start-the-emulator-and-connect-your-bot"></a>Starten des Emulators und Herstellen einer Verbindung mit Ihrem Bot

Starten Sie im nächste Schritt den Emulator, und stellen Sie dann im Emulator eine Verbindung mit Ihrem Bot her:

1. Klicken Sie im Emulator auf der Registerkarte „Willkommen“ auf den Link **Bot öffnen**. 
2. Wählen Sie in dem Verzeichnis, in dem Sie das Projekt erstellt haben, die BOT-Datei aus.

## <a name="interact-with-your-bot"></a>Interagieren mit Ihrem Bot

Senden Sie eine Nachricht an den Bot, und der Bot antwortet mit einer Nachricht.
![Ausgeführter Emulator](../media/emulator-v4/emulator-running.png)


## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Botkonzepte](../v4sdk/bot-builder-basics.md)
