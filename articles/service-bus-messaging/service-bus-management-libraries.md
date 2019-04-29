---
title: Bibliotecas de gerenciamento do Barramento de Serviço do Azure | Microsoft Docs
description: Gerencie namespaces e entidades de mensagens do Barramento de Serviço no .NET.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 09/05/2018
ms.author: aschhab
ms.openlocfilehash: d6c2cd813e96b40fc9f95785a1fd28e324a0437b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61315871"
---
# <a name="service-bus-management-libraries"></a>Bibliotecas de gerenciamento do Barramento de Serviço

As bibliotecas de gerenciamento do Barramento de Serviço do Azure podem provisionar dinamicamente namespaces e entidades do Barramento de Serviço. Isso permite implantações e cenários de mensagens complexos e possibilita determinar de forma programática quais entidades provisionar. Essas bibliotecas estão atualmente disponíveis para .NET.

## <a name="supported-functionality"></a>Funcionalidade com suporte

* Criação, atualização, exclusão de namespace
* Criação da fila, atualização, exclusão
* Criação de tópico, atualização, exclusão
* Criação da assinatura, atualização, exclusão

## <a name="prerequisites"></a>Pré-requisitos

Para começar a usar as bibliotecas de gerenciamento do Barramento de Serviço, você deve se autenticar com o serviço Azure Active Directory (Azure AD). O Microsoft Azure Active Directory exige que você autentique como uma entidade de serviço que forneça acesso aos recursos do Azure. Para saber mais sobre como criar uma entidade de serviço, veja um dos seguintes artigos:  

* [Usar o portal do Azure para criar um aplicativo e entidade de serviço do Active Directory que pode acessar recursos](/azure/azure-resource-manager/resource-group-create-service-principal-portal)
* [Usar o Azure PowerShell para criar uma entidade de serviço a fim de acessar recursos](/azure/azure-resource-manager/resource-group-authenticate-service-principal)
* [Usar a CLI do Azure para criar uma entidade de serviço a fim de acessar recursos](/azure/azure-resource-manager/resource-group-authenticate-service-principal-cli)

Estes tutoriais fornecem uma `AppId` (ID do Cliente), `TenantId` e `ClientSecret` (chave de autenticação), todas usadas para autenticação pelas bibliotecas de gerenciamento. Você deve ter as permissões **Proprietário** para o grupo de recursos no qual você deseja executar.

## <a name="programming-pattern"></a>Padrão de programação

O padrão para manipular qualquer recurso do Barramento de Serviço segue um protocolo comum:

1. Obtenha um token do Azure AD usando a biblioteca **Microsoft.IdentityModel.Clients.ActiveDirectory**:
   ```csharp
   var context = new AuthenticationContext($"https://login.microsoftonline.com/{tenantId}");

   var result = await context.AcquireTokenAsync("https://management.core.windows.net/", new ClientCredential(clientId, clientSecret));
   ```
2. Crie o objeto `ServiceBusManagementClient`:

   ```csharp
   var creds = new TokenCredentials(token);
   var sbClient = new ServiceBusManagementClient(creds)
   {
       SubscriptionId = SettingsCache["SubscriptionId"]
   };
   ```
3. Defina os parâmetros `CreateOrUpdate` com os seus valores especificados:

   ```csharp
   var queueParams = new QueueCreateOrUpdateParameters()
   {
       Location = SettingsCache["DataCenterLocation"],
       EnablePartitioning = true
   };
   ```
4. Execute a chamada:

   ```csharp
   await sbClient.Queues.CreateOrUpdateAsync(resourceGroupName, namespaceName, QueueName, queueParams);
   ```

## <a name="next-steps"></a>Próximas etapas

* [Amostra de gerenciamento do .NET](https://github.com/Azure-Samples/service-bus-dotnet-management/)
* [Referência de API de Microsoft.Azure.Management.ServiceBus](/dotnet/api/Microsoft.Azure.Management.ServiceBus)
