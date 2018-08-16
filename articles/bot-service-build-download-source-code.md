---
title: Herunterladen und erneutes Bereitstellen des Quellcodes für einen Botdienst | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie einen Botdienst herunterladen und veröffentlichen.
keywords: Herunterladen von Quellcode, erneutes Bereitstellen, Bereitstellen, ZIP-Datei, Veröffentlichen
author: v-ducvo
ms.author: v-ducvo
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 03/08/2018
ms.openlocfilehash: 6d76388712ffeff94c56ba89b4bf4f4831caf45c
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39303320"
---
# <a name="download-and-redeploy-bot-service-source-code"></a>Herunterladen und erneutes Bereitstellen des Quellcodes für einen Botdienst

Mit Bot Service können Sie das gesamte Quellprojekt für Ihren Bot herunterladen. Dadurch können Sie mit einer IDE Ihrer Wahl lokal an Ihren Bot arbeiten. Nachdem Sie alle Änderungen vorgenommen haben, können Sie diese zurück in Azure veröffentlichen. 

In diesem Thema wird gezeigt, wie Sie den Quellcode Ihres Bots herunterladen und die Änderungen zurück in Azure veröffentlichen. 

## <a name="download-bot-source-code"></a>Herunterladen von Bot-Quellcode

Zum lokalen Entwickeln Ihres Bots führen Sie die folgenden Schritte aus:

1. Öffnen Sie im Azure-Portal das Blatt für den Bot.
2. Klicken Sie im Abschnitt **BOTVERWALTUNG** auf **Erstellen**.
3. Klicken Sie auf **ZIP-Datei herunterladen**. 

   ![Herunterladen des Quellcodes](~/media/azure-bot-build/download-zip-file.png)

4. Extrahieren Sie die ZIP-Datei in ein lokales Verzeichnis.
5. Navigieren Sie zum extrahierten Ordner, und öffnen Sie die Quelldateien in Ihrer bevorzugten IDE.
6. Nehmen Sie Änderungen an Ihren Quellen vor. Bearbeiten Sie entweder vorhandene Quelldateien, oder fügen Sie neue Quelldateien zu Ihrem Projekt hinzu.

Wenn Sie bereit sind, können Sie die Quellen zurück in Azure veröffentlichen.

## <a name="publish-node-bot-source-code-to-azure"></a>Veröffentlichen von Bot-Quellcode in Node.js in Azure

Zum Installieren dieser Pakete navigieren Sie an einer Eingabeaufforderung zu Ihrem Projektverzeichnis, und führen Sie die folgenden NPM-Befehle aus.

**Hinweis:** Diese Pakete müssen nur einmal hinzugefügt werden.

```console
npm install --save fs
npm install --save path
npm install --save request
npm install --save zip-folder
```

Jetzt können Sie Ihr Projekt in Microsoft Azure veröffentlichen. Zum Veröffentlichen Ihres Projekts in Microsoft Azure führen Sie den folgenden NPM-Befehl an der Eingabeaufforderung ein:

```console
npm run azure-publish
```

> [!NOTE]
> Wenn nach dem Ausführen dieses NPM-Befehls ein Fehler auftritt, müssen Sie möglicherweise `"scripts": {"azure-publish": "node publish.js"}` zu Ihrer Datei `package.json` hinzufügen und den Befehl erneut ausführen.

## <a name="publish-c-bot-source-code-to-azure"></a>Veröffentlichen von Bot-Quellcode in C# in Azure

Das Veröffentlichen von C#-Code in Azure mithilfe von Visual Studio erfolgt in zwei Schritten: Zuerst müssen Sie Veröffentlichungseinstellungen konfigurieren. Dann veröffentlichen Sie Ihre Änderungen.

Führen Sie die folgenden Schritte aus, um die Veröffentlichung über Visual Studio zu konfigurieren:

1. Klicken Sie in Visual Studio auf **Projektmappen-Explorer**.
2. Klicken Sie mit der rechten Maustaste auf den Projektnamen, und wählen Sie **Veröffentlichen...** aus. Das Fenster **Veröffentlichen** wird geöffnet.
3. Klicken Sie auf **Neues Profil erstellen**, auf **Profil importieren** und dann auf **OK**.
4. Navigieren Sie zu Ihrem Projektordner, und navigieren Sie dann zum Ordner **PostDeployScripts**. Wählen Sie die Datei aus, die auf **.PublishSettings** endet. Klicken Sie auf **Öffnen**.

Ihr Projekt ist jetzt zum Veröffentlichen von Änderungen in Azure konfiguriert.

Sobald Ihr Projekt konfiguriert ist, können Sie Ihren Bot-Quellcode zurück in Azure veröffentlichen. Dazu gehen Sie wie folgt vor:

1. Klicken Sie in Visual Studio auf **Projektmappen-Explorer**.
2. Klicken Sie mit der rechten Maustaste auf den Projektnamen, und wählen Sie **Veröffentlichen...** aus.
3. Klicken Sie auf die Schaltfläche **Veröffentlichen**, um die Änderungen in Azure zu veröffentlichen.

## <a name="next-steps"></a>Nächste Schritte
Da Sie nun wissen, wie Sie Ihren Bot lokal erstellen, können Sie die kontinuierliche Bereitstellung für Ihren Bot einrichten.

> [!div class="nextstepaction"]
> [Festlegen der kontinuierlichen Bereitstellung](bot-service-build-continuous-deployment.md)
