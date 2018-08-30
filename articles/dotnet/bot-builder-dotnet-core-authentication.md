---
title: Authentifizieren von Aktivitäten mit .NET Core | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie Botaktivitäten mit .NET Core authentifizieren können.
author: v-ducvo
ms.author: v-ducvo
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/17
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 431367cf4afe702fd83feff60b0ee4e260d50f17
ms.sourcegitcommit: 2dc75701b169d822c9499e393439161bc87639d2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/24/2018
ms.locfileid: "42905762"
---
# <a name="authenticating-activities-using-net-core"></a>Authentifizieren von Aktivitäten mit .NET Core

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

Wenn Sie Ihren Bot mit [.NET Core](/dotnet/core/index) entwickeln, können Sie den [Bot Framework Connector](bot-builder-dotnet-connector.md), um [Aktivitätsmeldungen](https://docs.botframework.com/en-us/csharp/builder/sdkreference/dc/d2f/class_microsoft_1_1_bot_1_1_connector_1_1_activity.html) von Ihrem Bot zu senden und zu empfangen. Damit Sie den Connector-Dienst nutzen können, müssen Sie ein geeignetes Authentifizierungsmodell für die Framework-Version einrichten, die Sie als Ziel verwenden.

Bot Framework Connector.AspNetCore unterstützt die folgenden Versionen von ASP.NET:
* Verwenden Sie für .NET Core v1.1/AspNetCore1.x **Microsoft.Bot.Connector.AspNetCore 1.x**
* Verwenden Sie für .NET Core v2.0/AspNetCore2.x **Microsoft.Bot.Connector.AspNetCore 2.x**

In diesem Artikel erfahren Sie, wie Sie für das Framework, das von Ihrem Bot als Ziel verwendet wird, ein Authentifizierungsmodell einrichten.

## <a name="prerequisites"></a>Voraussetzungen

* [Visual Studio 2017](https://www.visualstudio.com/downloads/)
* [.NET Core](https://www.microsoft.com/net/download/windows). Installieren Sie die .NET Core-Version, die Sie als Ziel verwenden (z.B. .NET Core v1.1 oder .NET Core v 2.0).
* [Registrieren eines Bots](~/bot-service-quickstart-registration.md). Registrieren Sie Ihren Bot, um eine AppID und ein Kennwort für die Authentifizierung abzurufen.

## <a name="create-a-net-core-project"></a>Erstellen eines .NET Core-Projekts

Gehen Sie wie folgt vor, um ein .NET Core-Projekt zu erstellen:

1. Öffnen Sie Visual Studio 2017, und klicken Sie auf **Datei > Neu > Projekt...**.
2. Erweitern Sie den Knoten **Visual C#**, und klicken Sie auf **.NET Core**.
3. Wählen Sie den Projekttyp **ASP.NET Core-Webanwendung** aus, und geben Sie die Projektinformationen ein (z.B. Name, Speicherort und Projektmappenname).
4. Klicken Sie auf **OK**.
5. Stellen Sie sicher, dass das Projekt *.NET Core* und die gewünschte Version von *ASP.NET Core* als Ziel verwendet. In der folgenden Abbildung ist beispielsweise dargestellt, dass das Projekt **.NET Core** und **ASP.NET Core 2.0** als Ziel verwendet:

![Erstellen eines ASP.NET Core v2.0-Projekts](~/media/dotnet-core-authentication/create-asp-net-core-2x-project.png)
 
  > [!NOTE]
  > Wenn Sie **ASP.NET Core 2.0** als Ziel verwenden, müssen Sie sicherstellen, dass auf Ihrem Computer mindestens Version 2.x oder höher installiert ist.

6. Wählen Sie den Projekttyp **Web-API** aus.
7. Klicken Sie auf **OK**, um das Projekt zu erstellen.

## <a name="download-the-nuget-package"></a>Herunterladen des NuGet-Pakets

Damit Sie den **Bot Framework Connector** verwenden können, müssen Sie das für Ihre .NET Core-Version geeignete NuGet-Paket installieren. Gehen Sie wie folgt vor, um das NuGet-Paket zu installieren:

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf den Projektnamen und dann auf **NuGet-Pakete verwalten...**.
2. Klicken Sie auf **Durchsuchen**, und suchen Sie nach **Connector.ASPNetCore**. 
3. Wählen Sie die Version aus, die Sie als Ziel verwenden möchten. In der folgenden Abbildung wurde Version **2.0.0.3** ausgewählt. Wenn Sie Version 1.1.3.2 installieren möchten, klicken Sie auf die Dropdownliste **Version**, und wählen Sie stattdessen Version **1.1.3.2** aus.
![NuGet-Paket für Version 2.0.0.3 von ASP Net Core](~/media/dotnet-core-authentication/nuget-package-net-core-version.png)
4. Klicken Sie auf **Installieren**.

## <a name="update-the-appsettingsjson"></a>Aktualisieren der Datei „appsettings.json“

Der Bot Framework Connector benötigt zum Authentifizieren des Bots Ihre AppID und Ihr Kennwort. Diese Werte können Sie in der Datei **appsettings.json** Ihres Web-App-Projekts festlegen.

> [!NOTE]
> Unter [MicrosoftAppID und MicrosoftAppPassword](~/bot-service-manage-overview.md#microsoftappid-and-microsoftapppassword) wird beschrieben, wie Sie die Werte für **AppID** und **AppPassword** für Ihren Bot finden.

### <a name="appsettingsjson-for-net-core-v11"></a>„appsettings.json“ für .NET Core v1.1:

```json
{
  "Logging": {
    "IncludeScopes": false,
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "MicrosoftAppId": "<MicrosoftAppId>",
  "MicrosoftAppPassword": "<MicrosoftAppPassword>"
}
```

### <a name="appsettingsjson-for-net-core-v20"></a>„appsettings.json“ für .NET Core v2.0:

```json
{
  "Logging": {
    "IncludeScopes": false,
    "Debug": {
      "LogLevel": {
        "Default": "Warning"
      }
    },
    "Console": {
      "LogLevel": {
        "Default": "Warning"
      }
    }
  },
  "MicrosoftAppId": "<MicrosoftAppId>",
  "MicrosoftAppPassword": "<MicrosoftAppPassword>"
}
```

## <a name="update-the-startupcs-class"></a>Aktualisieren der „startup.cs“-Klasse

Aktualisieren Sie die **startup.cs**-Klasse. Aktualisieren Sie das Codefragment für die jeweilige Projektversion, damit die Authentifizierung durchgeführt werden kann.

### <a name="startupcs-for-net-core-v11"></a>„startup.cs“ für .NET Core v1.1

```cs
public class Startup
    {
        public Startup(IHostingEnvironment env)
        {
            var builder = new ConfigurationBuilder()
                .SetBasePath(env.ContentRootPath)
                .AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
                .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true)
                .AddEnvironmentVariables();
            Configuration = builder.Build();
        }

        public IConfigurationRoot Configuration { get; }

        // This method gets called by the runtime. Use this method to add services to the container.
        public void ConfigureServices(IServiceCollection services)
        {
            services.AddSingleton(_ => Configuration);

            services.AddSingleton(typeof(ICredentialProvider), new StaticCredentialProvider(
                Configuration.GetSection(MicrosoftAppCredentials.MicrosoftAppIdKey)?.Value,
                Configuration.GetSection(MicrosoftAppCredentials.MicrosoftAppPasswordKey)?.Value));

            // Add framework services.
            services.AddMvc(options =>
            {
                options.Filters.Add(typeof(TrustServiceUrlAttribute));
            });
        }

        // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
        public void Configure(IApplicationBuilder app, IServiceProvider serviceProvider, IHostingEnvironment env, ILoggerFactory loggerFactory)
        {
            loggerFactory.AddConsole(Configuration.GetSection("Logging"));
            loggerFactory.AddDebug();

            app.UseStaticFiles();

            ICredentialProvider credentialProvider = serviceProvider.GetService<ICredentialProvider>();

            app.UseBotAuthentication(credentialProvider);

            app.UseMvc();
        }
    }
```

### <a name="startupcs-for-net-core-v20"></a>„startup.cs“ für .NET Core v2.0:

```cs
public class Startup
    {
        public Startup(IHostingEnvironment env)
        {
            var builder = new ConfigurationBuilder()
                .SetBasePath(env.ContentRootPath)
                .AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
                .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true)
                .AddEnvironmentVariables();
            Configuration = builder.Build();
        }

        public IConfiguration Configuration { get; }
        // This method gets called by the runtime. Use this method to add services to the container.
        public void ConfigureServices(IServiceCollection services)
        {
            services.AddSingleton(_ => Configuration);

            var credentialProvider = new StaticCredentialProvider(Configuration.GetSection(MicrosoftAppCredentials.MicrosoftAppIdKey)?.Value,
                Configuration.GetSection(MicrosoftAppCredentials.MicrosoftAppPasswordKey)?.Value);

            services.AddAuthentication(
                    options =>
                    {
                        options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
                        options.DefaultChallengeScheme = JwtBearerDefaults.AuthenticationScheme;
                    }
                )
                .AddBotAuthentication(credentialProvider);

            services.AddSingleton(typeof(ICredentialProvider), credentialProvider);

            services.AddMvc(options =>
            {
                options.Filters.Add(typeof(TrustServiceUrlAttribute));
            });
        }


        // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
        public void Configure(IApplicationBuilder app, IHostingEnvironment env)
        {
            app.UseStaticFiles();
            app.UseAuthentication();
            app.UseMvc();
        }
    }
```

## <a name="create-the-messagescontrollercs-class"></a>Erstellen der „MessagesController.cs“-Klasse

Fügen Sie über den **Projektmappen-Explorer** eine neue leere Klasse mit dem Namen **MessagesController.cs** hinzu. Aktualisieren Sie in der **MessagesController.cs**-Klasse die **Post**-Methode mit dem folgenden Code. Dadurch kann Ihr Bot Meldungen für den Benutzer senden und empfangen.

```cs
private IConfiguration configuration;

public MessagesController(IConfiguration configuration)
{
    this.configuration = configuration;
}


[Authorize(Roles = "Bot")]
[HttpPost]
public async Task<OkResult> Post([FromBody] Activity activity)
{
    if (activity.Type == ActivityTypes.Message)
    {
        //MicrosoftAppCredentials.TrustServiceUrl(activity.ServiceUrl);
        var appCredentials = new MicrosoftAppCredentials(configuration);
        var connector = new ConnectorClient(new Uri(activity.ServiceUrl), appCredentials);

        // return our reply to the user
        var reply = activity.CreateReply("HelloWorld");
        await connector.Conversations.ReplyToActivityAsync(reply);
    }
    else
    {
        //HandleSystemMessage(activity);
    }
    return Ok();
}
```
