---
title: Erstellen eines Bots mit dem Bot Builder SDK für Java | Microsoft-Dokumentation
description: Erstellen Sie schnell einen Bot mithilfe des Bot Builder SDK für Java.
keywords: Bot Builder SDK, Erstellen eines Bots, Schnellstart, Java, erste Schritte
author: jonathanfingold
ms.author: jonathanfingold
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 08/30/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: bcfc1c76199d8bc729376bbbfe229b0781eb82ab
ms.sourcegitcommit: 3bf3dbb1a440b3d83e58499c6a2ac116fe04b2f6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/23/2018
ms.locfileid: "46707236"
---
# <a name="create-a-bot-with-the-bot-builder-sdk-for-java"></a>Erstellen eines Bots mit dem Bot Builder SDK für Java 
> [!NOTE] 
> Das Java SDK v4 befindet sich in der **Vorschauphase**. Weitere Informationen finden Sie im [GitHub-Repository für Java](https://github.com/Microsoft/botbuilder-java). Unsere Codebeispiele und Dokumente beziehen sich derzeit auf Java-Version 1.8.

## <a name="getting-started"></a>Erste Schritte

Version 4 des Java SDK besteht aus einer Reihe von [Bibliotheken](https://github.com/Microsoft/botbuilder-java/tree/master/libraries). Wie Sie sie lokal erstellen, erfahren Sie unter [Building the SDK (Erstellen des SDK)](https://github.com/Microsoft/botbuilder-java/wiki/building-the-sdk).

- Installieren des [Bot Framework-Emulator](https://github.com/Microsoft/BotFramework-Emulator/releases)

## <a name="create-echobot"></a>Erstellen von EchoBot

Fügen Sie Folgendes in der Datei „App.java“ hinzu:

```Java
package com.microsoft.bot.connector.sample;

import com.fasterxml.jackson.databind.DeserializationFeature;
import com.fasterxml.jackson.databind.ObjectMapper;
import com.microsoft.aad.adal4j.AuthenticationException;
import com.microsoft.bot.connector.customizations.CredentialProvider;
import com.microsoft.bot.connector.customizations.CredentialProviderImpl;
import com.microsoft.bot.connector.customizations.JwtTokenValidation;
import com.microsoft.bot.connector.customizations.MicrosoftAppCredentials;
import com.microsoft.bot.connector.implementation.ConnectorClientImpl;
import com.microsoft.bot.schema.models.Activity;
import com.microsoft.bot.schema.models.ActivityTypes;
import com.microsoft.bot.schema.models.ResourceResponse;
import com.sun.net.httpserver.HttpExchange;
import com.sun.net.httpserver.HttpHandler;
import com.sun.net.httpserver.HttpServer;

import java.io.IOException;
import java.io.InputStream;
import java.net.InetSocketAddress;
import java.net.URLDecoder;
import java.util.logging.Level;
import java.util.logging.Logger;

public class App {
    private static final Logger LOGGER = Logger.getLogger( App.class.getName() );
    private static String appId = "";       // <-- app id -->
    private static String appPassword = ""; // <-- app password -->

    public static void main( String[] args ) throws IOException {
        CredentialProvider credentialProvider = new CredentialProviderImpl(appId, appPassword);
        HttpServer server = HttpServer.create(new InetSocketAddress(3978), 0);
        server.createContext("/api/messages", new MessageHandle(credentialProvider));
        server.setExecutor(null);
        server.start();
    }

    static class MessageHandle implements HttpHandler {
        private ObjectMapper objectMapper;
        private CredentialProvider credentialProvider;
        private MicrosoftAppCredentials credentials;

        MessageHandle(CredentialProvider credentialProvider) {
            this.objectMapper = new ObjectMapper()
                    .configure(DeserializationFeature.FAIL_ON_UNKNOWN_PROPERTIES, false)
                    .findAndRegisterModules();
            this.credentialProvider = credentialProvider;
            this.credentials = new MicrosoftAppCredentials(appId, appPassword);
        }

        public void handle(HttpExchange httpExchange) throws IOException {
            if (httpExchange.getRequestMethod().equalsIgnoreCase("POST")) {
                Activity activity = getActivity(httpExchange);
                String authHeader = httpExchange.getRequestHeaders().getFirst("Authorization");
                try {
                    JwtTokenValidation.assertValidActivity(activity, authHeader, credentialProvider);

                    // send ack to user activity
                    httpExchange.sendResponseHeaders(202, 0);
                    httpExchange.getResponseBody().close();

                    if (activity.type().equals(ActivityTypes.MESSAGE)) {
                        // reply activity with the same text
                        ConnectorClientImpl connector = new ConnectorClientImpl(activity.serviceUrl(), this.credentials);
                        ResourceResponse response = connector.conversations().sendToConversation(activity.conversation().id(),
                                new Activity()
                                        .withType(ActivityTypes.MESSAGE)
                                        .withText("Echo: " + activity.text())
                                        .withRecipient(activity.from())
                                        .withFrom(activity.recipient())
                        );
                    }
                } catch (AuthenticationException ex) {
                    httpExchange.sendResponseHeaders(401, 0);
                    httpExchange.getResponseBody().close();
                    LOGGER.log(Level.WARNING, "Auth failed!", ex);
                } catch (Exception ex) {
                    LOGGER.log(Level.WARNING, "Execution failed", ex);
                }
            }
        }

        private String getRequestBody(HttpExchange httpExchange) throws IOException {
            StringBuilder buffer = new StringBuilder();
            InputStream stream = httpExchange.getRequestBody();
            int rByte;
            while ((rByte = stream.read()) != -1) {
                buffer.append((char)rByte);
            }
            stream.close();
            if (buffer.length() > 0) {
                return URLDecoder.decode(buffer.toString(), "UTF-8");
            }
            return "";
        }

        private Activity getActivity(HttpExchange httpExchange) {
            try {
                String body = getRequestBody(httpExchange);
                LOGGER.log(Level.INFO, body);
                return objectMapper.readValue(body, Activity.class);
            } catch (Exception ex) {
                LOGGER.log(Level.WARNING, "Failed to get activity", ex);
                return null;
            }

        }
    }
}
```

Wenn Sie Maven verwenden, können Sie die Datei „pom.xml“ aus dem Ordner „samples“ in dieses Repository kopieren. Führen Sie die ausführbare Datei aus. Zu diesem Zeitpunkt wird Ihr Bot lokal ausgeführt.

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
