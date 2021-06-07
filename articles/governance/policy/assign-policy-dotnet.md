---
title: 'Início Rápido: Nova atribuição de política com o .NET Core'
description: Neste início rápido, você usará o .NET Core para criar uma atribuição do Azure Policy para identificar recursos que não estão em conformidade.
ms.date: 03/31/2021
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: b89a0db419b1e7213e9742e4a39d073445d05189
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106091779"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-with-net-core"></a>Início Rápido: Criar uma atribuição de política para identificar recursos que não estão em conformidade com o .NET Core

A primeira etapa para compreender a conformidade no Azure é identificar o status de seus recursos. Neste guia de início rápido, você cria uma atribuição de política para identificar máquinas virtuais que não estão usando discos gerenciados. Ao concluir, você identificará máquinas virtuais que _não estão em conformidade_.

A biblioteca do .NET Core é usada para gerenciar recursos do Azure. Este guia explica como usar a biblioteca do .NET Core para o Azure Policy criar uma atribuição de política.

## <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.
- Uma entidade de serviço do Azure, incluindo _clientId_ e _clientSecret_. Se você não tiver uma entidade de serviço para uso com o Azure Policy ou quiser criar uma, confira [Bibliotecas de gerenciamento do Azure para autenticação do .NET](/dotnet/azure/sdk/authentication#mgmt-auth).
  Ignore a etapa para instalar os pacotes do .NET Core, pois faremos isso nas próximas etapas.

## <a name="create-the-azure-policy-project"></a>Criar o projeto do Azure Policy

Para habilitar o .NET Core para gerenciar o Azure Policy, crie um aplicativo de console e instale os pacotes necessários.

1. Verifique se o .NET Core mais recente está instalado (no mínimo a versão **3.1.8**). Se ainda não estiver instalado, baixe-o em [dotnet.microsoft.com](https://dotnet.microsoft.com/download/dotnet-core).

1. Inicialize um novo aplicativo de console .NET Core chamado "policyAssignment":

   ```dotnetcli
   dotnet new console --name "policyAssignment"
   ```

1. Altere os diretórios para a nova pasta do projeto e instale os pacotes necessários para o Azure Policy:

   ```dotnetcli
   # Add the Azure Policy package for .NET Core
   dotnet add package Microsoft.Azure.Management.ResourceManager --version 3.10.0-preview

   # Add the Azure app auth package for .NET Core
   dotnet add package Microsoft.Azure.Services.AppAuthentication --version 1.5.0
   ```

1. Substitua o padrão `program.cs` pelo seguinte código e salve o arquivo atualizado:

   ```csharp
   using System;
   using System.Collections.Generic;
   using System.Threading.Tasks;
   using Microsoft.IdentityModel.Clients.ActiveDirectory;
   using Microsoft.Rest;
   using Microsoft.Azure.Management.ResourceManager;
   using Microsoft.Azure.Management.ResourceManager.Models;
   
   namespace policyAssignment
   {
       class Program
       {
           static async Task Main(string[] args)
           {
               string strTenant = args[0];
               string strClientId = args[1];
               string strClientSecret = args[2];
               string strSubscriptionId = args[3];
               string strName = args[4];
               string strDisplayName = args[5];
               string strPolicyDefID = args[6];
               string strDescription = args[7];
               string strScope = args[8];
   
               var authContext = new AuthenticationContext($"https://login.microsoftonline.com/{strTenant}");
               var authResult = await authContext.AcquireTokenAsync(
                   "https://management.core.windows.net",
                   new ClientCredential(strClientId, strClientSecret));

               using (var client = new PolicyClient(new TokenCredentials(authResult.AccessToken)))
               {
                   var policyAssignment = new PolicyAssignment
                   {
                       DisplayName = strDisplayName,
                       PolicyDefinitionId = strPolicyDefID,
                       Description = strDescription
                   };
                   var response = await client.PolicyAssignments.CreateAsync(strScope, strName, policyAssignment);
               }
           }
       }
   }
   ```

1. Crie e publique o aplicativo de console `policyAssignment`:

   ```dotnetcli
   dotnet build
   dotnet publish -o {run-folder}
   ```

## <a name="create-a-policy-assignment"></a>Criar uma atribuição de política

Neste guia de início rápido, você cria uma atribuição de política e atribui a definição **Auditar VMs que não usam discos gerenciados** (`06a78e20-9358-41c9-923c-fb736d382a4d`). Esta definição de política identifica recursos que não estão em conformidade com as condições configuradas na definição de política.

1. Altere os diretórios para o `{run-folder}` que você definiu com o comando `dotnet publish` anterior.

1. Insira o seguinte comando no terminal:

   ```bash
   policyAssignment.exe `
      "{tenantId}" `
      "{clientId}" `
      "{clientSecret}" `
      "{subscriptionId}" `
      "audit-vm-manageddisks" `
      "Audit VMs without managed disks Assignment" `
      "/providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d" `
      "Shows all virtual machines not using managed disks" `
      "{scope}"
   ```

Os comandos anteriores usam as seguintes informações:

- `{tenantId}` – Substitua pela ID de locatário
- `{clientId}` – substituir pela ID do cliente da entidade de serviço
- `{clientSecret}` – substituir pelo segredo do cliente da entidade de serviço
- `{subscriptionId}`: substitua por sua ID da assinatura
- **name** – o nome exclusivo para o objeto de atribuição de política. O exemplo acima usa _audit-vm-manageddisks_.
- **displayName** – o nome de exibição da atribuição de política. Nesse caso, você está usando _Auditar VMs sem atribuição de discos gerenciados_.
- **policyDefID** – o caminho da definição da política, com base no que você está usando para criar a atribuição. Nesse caso, é a ID da definição de política _Auditar VMs que não usam discos gerenciados_.
- **description** – uma explicação mais detalhada do que a política faz ou de por que ela está atribuída a esse escopo.
- **scope** – um escopo determina em quais recursos ou agrupamento de recursos a atribuição de política é imposta. Ele pode variar de um grupo de gerenciamento a um recurso individual. Substitua `{scope}` por um dos seguintes padrões:
  - Grupo de gerenciamento: `/providers/Microsoft.Management/managementGroups/{managementGroup}`
  - Assinatura: `/subscriptions/{subscriptionId}`
  - Grupo de recursos: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}`
  - Recurso: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/[{parentResourcePath}/]`

Agora você está pronto para identificar recursos fora de conformidade para entender o estado de conformidade do ambiente.

## <a name="identify-non-compliant-resources"></a>Identificar recursos fora de conformidade

Agora que sua atribuição de política foi criada, você pode identificar os recursos que não estão em conformidade.

1. Inicialize um novo aplicativo de console .NET Core chamado "policyCompliance":

   ```dotnetcli
   dotnet new console --name "policyCompliance"
   ```

1. Altere os diretórios para a nova pasta do projeto e instale os pacotes necessários para o Azure Policy:

   ```dotnetcli
   # Add the Azure Policy package for .NET Core
   dotnet add package Microsoft.Azure.Management.PolicyInsights --version 3.1.0

   # Add the Azure app auth package for .NET Core
   dotnet add package Microsoft.Azure.Services.AppAuthentication --version 1.5.0
   ```

1. Substitua o padrão `program.cs` pelo seguinte código e salve o arquivo atualizado:

   ```csharp
   using System;
   using System.Collections.Generic;
   using System.Threading.Tasks;
   using Microsoft.IdentityModel.Clients.ActiveDirectory;
   using Microsoft.Rest;
   using Microsoft.Azure.Management.PolicyInsights;
   using Microsoft.Azure.Management.PolicyInsights.Models;
   
   namespace policyAssignment
   {
       class Program
       {
           static async Task Main(string[] args)
           {
               string strTenant = args[0];
               string strClientId = args[1];
               string strClientSecret = args[2];
               string strSubscriptionId = args[3];
               string strName = args[4];
   
               var authContext = new AuthenticationContext($"https://login.microsoftonline.com/{strTenant}");
               var authResult = await authContext.AcquireTokenAsync(
                   "https://management.core.windows.net",
                   new ClientCredential(strClientId, strClientSecret));
   
               using (var client = new PolicyInsightsClient(new TokenCredentials(authResult.AccessToken)))
               {
                   var policyQueryOptions = new QueryOptions
                   {
                       Filter = $"IsCompliant eq false and PolicyAssignmentId eq '{strName}'",
                       Apply = "groupby(ResourceId)"
                   };
   
                   var response = await client.PolicyStates.ListQueryResultsForSubscriptionAsync(
                       "latest", strSubscriptionId, policyQueryOptions);
                   Console.WriteLine(response.Odatacount);
               }
           }
       }
   }
   ```

1. Crie e publique o aplicativo de console `policyAssignment`:

   ```dotnetcli
   dotnet build
   dotnet publish -o {run-folder}
   ```

1. Altere os diretórios para o `{run-folder}` que você definiu com o comando `dotnet publish` anterior.

1. Insira o seguinte comando no terminal:

   ```bash
   policyCompliance.exe `
      "{tenantId}" `
      "{clientId}" `
      "{clientSecret}" `
      "{subscriptionId}" `
      "audit-vm-manageddisks"
   ```

Os comandos anteriores usam as seguintes informações:

- `{tenantId}` – Substitua pela ID de locatário
- `{clientId}` – substituir pela ID do cliente da entidade de serviço
- `{clientSecret}` – substituir pelo segredo do cliente da entidade de serviço
- `{subscriptionId}`: substitua por sua ID da assinatura
- **name** – o nome exclusivo para o objeto de atribuição de política. O exemplo acima usa _audit-vm-manageddisks_.

Os resultados no `response` correspondem ao que você vê na guia **Conformidade do recurso** de uma atribuição de política na exibição do portal do Azure.

## <a name="clean-up-resources"></a>Limpar os recursos

- Exclua a atribuição de política _Auditar VMs sem a atribuição de discos gerenciados_ por meio do portal. A definição de política é um recurso interno, portanto, não há nenhuma definição a ser removida.

- Se desejar remover os aplicativos de console .NET Core e os pacotes instalados, exclua as pastas de projeto `policyAssignment` e `policyCompliance`.

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você atribuiu uma definição de política para identificar recursos sem conformidade em seu ambiente do Azure.

Para saber mais sobre definições de atribuição de políticas para validar que novos recursos estejam em conformidade, continue com o tutorial para:

> [!div class="nextstepaction"]
> [Criando e gerenciando políticas](./tutorials/create-and-manage.md)
