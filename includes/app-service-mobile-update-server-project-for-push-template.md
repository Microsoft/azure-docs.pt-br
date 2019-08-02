---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: c664b089f316255fabc4c8dc36b291d7d63e6280
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172423"
---
Nesta seção, você aprenderá a atualizar o código em seu projeto de back-end de Aplicativos Móveis para enviar uma notificação por push sempre que um novo item for adicionado. Esse processo é alimentado pelo recurso [modelo](../articles/notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) dos Hubs de Notificações do Microsoft Azure, que permite pushes de plataforma cruzada. Os vários clientes são registrados para notificações por push usando modelos, e um único envio por push universal pode chegar a todas as plataformas clientes.

Escolha um dos procedimentos abaixo que corresponda ao seu tipo de projeto de back-end&mdash;, um [back-end .NET](#dotnet) ou um [back-end Node.js](#nodejs).

### <a name="dotnet"></a>Projeto de back-end do .NET

1. No Visual Studio, clique com o botão direito do mouse no projeto do servidor. Em seguida, selecione **Gerenciar Pacotes do NuGet**. Pesquise `Microsoft.Azure.NotificationHubs` e, em seguida, selecione **Instalar**. Esse processo instala a biblioteca dos Hubs de Notificação para enviar notificações do back-end.
2. No projeto do servidor, abra **Controladores** > **TodoItemController.cs**. Em seguida, adicione as seguintes instruções usando:

    ```csharp
    using System.Collections.Generic;
    using Microsoft.Azure.NotificationHubs;
    using Microsoft.Azure.Mobile.Server.Config;
    ```

3. No método **PostTodoItem**, adicione o seguinte código após a chamada para **InsertAsync**:  

    ```csharp
    // Get the settings for the server project.
    HttpConfiguration config = this.Configuration;
    MobileAppSettingsDictionary settings =
        this.Configuration.GetMobileAppSettingsProvider().GetMobileAppSettings();

    // Get the Notification Hubs credentials for the mobile app.
    string notificationHubName = settings.NotificationHubName;
    string notificationHubConnection = settings
        .Connections[MobileAppSettingsKeys.NotificationHubConnectionString].ConnectionString;

    // Create a new Notification Hub client.
    NotificationHubClient hub = NotificationHubClient
    .CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

    // Send the message so that all template registrations that contain "messageParam"
    // receive the notifications. This includes APNS, GCM, WNS, and MPNS template registrations.
    Dictionary<string,string> templateParams = new Dictionary<string,string>();
    templateParams["messageParam"] = item.Text + " was added to the list.";

    try
    {
        // Send the push notification and log the results.
        var result = await hub.SendTemplateNotificationAsync(templateParams);

        // Write the success result to the logs.
        config.Services.GetTraceWriter().Info(result.State.ToString());
    }
    catch (System.Exception ex)
    {
        // Write the failure result to the logs.
        config.Services.GetTraceWriter()
            .Error(ex.Message, null, "Push.SendAsync Error");
    }
    ```

    Esse processo envia uma notificação de modelo que contém o item.Texto quando um novo item é inserido.

4. Republicar o projeto de servidor.

### <a name="nodejs"></a>Projeto de back-end do Node.js

1. Se você ainda não fez isso, [baixe o projeto de back-end de início rápido](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart) ou, caso contrário, use o [editor online no portal do Azure](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#online-editor).
2. Substitua o código existente em todoitem.js pelo código a seguir:

    ```javascript
    var azureMobileApps = require('azure-mobile-apps'),
    promises = require('azure-mobile-apps/src/utilities/promises'),
    logger = require('azure-mobile-apps/src/logger');

    var table = azureMobileApps.table();

    table.insert(function (context) {
    // For more information about the Notification Hubs JavaScript SDK,
    // see https://aka.ms/nodejshubs.
    logger.info('Running TodoItem.insert');

    // Define the template payload.
    var payload = '{"messageParam": "' + context.item.text + '" }';  

    // Execute the insert. The insert returns the results as a promise.
    // Do the push as a post-execute action within the promise flow.
    return context.execute()
        .then(function (results) {
            // Only do the push if configured.
            if (context.push) {
                // Send a template notification.
                context.push.send(null, payload, function (error) {
                    if (error) {
                        logger.error('Error while sending push notification: ', error);
                    } else {
                        logger.info('Push notification sent successfully!');
                    }
                });
            }
            // Don't forget to return the results from the context.execute().
            return results;
        })
        .catch(function (error) {
            logger.error('Error while running context.execute: ', error);
        });
    });

    module.exports = table;  
    ```

    Esse processo envia uma notificação de modelo que contém o item.text quando um novo item é inserido.

3. Ao editar o arquivo no computador local, publique o projeto do servidor.
