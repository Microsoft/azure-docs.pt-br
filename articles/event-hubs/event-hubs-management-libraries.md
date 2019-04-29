---
title: Bibliotecas de gerenciamento - Hubs de Eventos do Azure | Microsoft Docs
description: Este artigo fornece informações sobre a biblioteca que você pode usar para gerenciar namespaces de Hubs de Eventos do Azure e entidades do .NET.
services: event-hubs
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: dotnet
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 431fe04461f422274697d1e91c4b56e914ce2d4e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60746651"
---
# <a name="event-hubs-management-libraries"></a>Bibliotecas de gerenciamento dos Hubs de Eventos

Você pode usar as bibliotecas de gerenciamento de Hubs de eventos podem provisionar dinamicamente entidades e namespaces de Hubs de Eventos do Azure. Essa natureza dinâmica permite implantações e cenários de mensagens complexos, para que seja possível determinar de forma programática quais entidades serão provisionadas. Essas bibliotecas estão atualmente disponíveis para .NET.

## <a name="supported-functionality"></a>Funcionalidade com suporte

* Criação, atualização, exclusão de namespace
* Criação de Hubs de eventos, atualização, exclusão
* Criação de grupo de consumidores, atualização, exclusão

## <a name="prerequisites"></a>Pré-requisitos

Para começar a usar as bibliotecas de gerenciamento de Hubs de eventos, você deverá se autenticar com o Azure Active Directory (AAD). AAD exige que você autentique como uma entidade de serviço, que fornece acesso aos recursos do Azure. Para saber mais sobre como criar uma entidade de serviço, veja um dos seguintes artigos:  

* [Usar o portal do Azure para criar um aplicativo e entidade de serviço do Active Directory que pode acessar recursos](../active-directory/develop/howto-create-service-principal-portal.md)
* [Usar o Azure PowerShell para criar uma entidade de serviço a fim de acessar recursos](../active-directory/develop/howto-authenticate-service-principal-powershell.md)
* [Usar a CLI do Azure para criar uma entidade de serviço a fim de acessar recursos](../azure-resource-manager/resource-group-authenticate-service-principal-cli.md)

Estes tutoriais fornecem uma `AppId` (ID do Cliente), `TenantId` e `ClientSecret` (chave de autenticação), todas usadas para autenticação pelas bibliotecas de gerenciamento. Você deve ter permissões de **Proprietário** para o grupo de recursos no qual você deseja executar.

## <a name="programming-pattern"></a>Padrão de programação

O padrão para manipular qualquer recurso de Hubs de eventos a seguir, um protocolo comum:

1. Obtenha um token do AAD usando a biblioteca `Microsoft.IdentityModel.Clients.ActiveDirectory`.
    ```csharp
    var context = new AuthenticationContext($"https://login.microsoftonline.com/{tenantId}");

    var result = await context.AcquireTokenAsync(
        "https://management.core.windows.net/",
        new ClientCredential(clientId, clientSecret)
    );
    ```

1. Crie o objeto `EventHubManagementClient`.
    ```csharp
    var creds = new TokenCredentials(token);
    var ehClient = new EventHubManagementClient(creds)
    {
        SubscriptionId = SettingsCache["SubscriptionId"]
    };
    ```

1. Defina os parâmetros `CreateOrUpdate` com os valores especificados.
    ```csharp
    var ehParams = new EventHubCreateOrUpdateParameters()
    {
        Location = SettingsCache["DataCenterLocation"]
    };
    ```

1. Execute a chamada.
    ```csharp
    await ehClient.EventHubs.CreateOrUpdateAsync(resourceGroupName, namespaceName, EventHubName, ehParams);
    ```

## <a name="next-steps"></a>Próximas etapas
* [Exemplo do Gerenciamento do .NET](https://github.com/Azure-Samples/event-hubs-dotnet-management/)
* [Referência de Microsoft.Azure.Management.EventHub](/dotnet/api/Microsoft.Azure.Management.EventHub) 
