---
title: 'Início Rápido: Criar um grupo de gerenciamento com o .NET Core'
description: Neste guia de início rápido, você usará o .NET Core para criar um grupo de gerenciamento para organizar seus recursos em uma hierarquia de recursos.
ms.date: 02/05/2021
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: a74cea9b142785c093b8ed235fc40049746f11a0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99592544"
---
# <a name="quickstart-create-a-management-group-with-net-core"></a>Início Rápido: Criar um grupo de gerenciamento com o .NET Core

Grupos de gerenciamento são contêineres que o ajudarão a gerenciar o acesso, a política e a conformidade entre várias assinaturas. Criar esses contêineres para criar uma hierarquia eficaz e eficiente que pode ser usada com o [Azure Policy](../policy/overview.md) e os [Controles de Acesso Baseados em Função do Azure](../../role-based-access-control/overview.md). Para obter mais informações sobre grupos de gerenciamento, consulte [Organizar seus recursos com grupos de gerenciamento do Azure](overview.md).

O primeiro grupo de gerenciamento criado no diretório pode levar até 15 minutos para ser concluído. Há processos que são executados pela primeira vez para configurar o serviço de grupos de gerenciamento no Azure para seu diretório. Você recebe uma notificação quando o processo é concluído. Para obter mais informações, confira [Configuração inicial dos grupos de gerenciamento](./overview.md#initial-setup-of-management-groups).

## <a name="prerequisites"></a>Pré-requisitos

- Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

- Uma entidade de serviço do Azure, incluindo _clientId_ e _clientSecret_. Se você não tiver uma entidade de serviço para uso com o Azure Policy ou quiser criar uma, confira [Bibliotecas de gerenciamento do Azure para autenticação do .NET](/dotnet/azure/sdk/authentication#mgmt-auth).
  Ignore a etapa para instalar os pacotes do .NET Core, pois faremos isso nas próximas etapas.

- Qualquer usuário do Azure AD no locatário poderá criar um grupo de gerenciamento sem ter recebido a permissão de gravação do grupo de gerenciamento se a [proteção de hierarquia](./how-to/protect-resource-hierarchy.md#setting---require-authorization) não estiver habilitada. Esse novo grupo de gerenciamento passa a ser um filho do grupo de gerenciamento raiz ou o [grupo de gerenciamento padrão](./how-to/protect-resource-hierarchy.md#setting---default-management-group), e o criador recebe uma atribuição de função "Proprietário". O serviço de grupo de gerenciamento permite essa capacidade para que as atribuições de função não sejam necessárias no nível raiz. Nenhum usuário tem acesso ao grupo de gerenciamento raiz quando ele é criado. Para evitar problemas de localização dos administradores globais do Azure AD para começar a usar os grupos de gerenciamento, permitimos a criação dos grupos de gerenciamento iniciais no nível raiz.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="application-setup"></a>Instalação do aplicativo

Para permitir que o .NET Core administre grupos de gerenciamento, crie um aplicativo de console e instale os pacotes necessários.

1. Verifique se o .NET Core mais recente está instalado (no mínimo a versão **3.1.8**). Se ainda não estiver instalado, baixe-o em [dotnet.microsoft.com](https://dotnet.microsoft.com/download/dotnet-core).

1. Inicialize um novo aplicativo de console .NET Core chamado "mgCreate":

   ```dotnetcli
   dotnet new console --name "mgCreate"
   ```

1. Altere os diretórios para a nova pasta do projeto e instale os pacotes necessários para o Azure Policy:

   ```dotnetcli
   # Add the Azure Policy package for .NET Core
   dotnet add package Microsoft.Azure.Management.ManagementGroups --version 1.1.1-preview

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
   using Microsoft.Azure.Management.ManagementGroups;
   using Microsoft.Azure.Management.ManagementGroups.Models;
   
   namespace mgCreate
   {
       class Program
       {
           static async Task Main(string[] args)
           {
               string strTenant = args[0];
               string strClientId = args[1];
               string strClientSecret = args[2];
               string strGroupId = args[3];
               string strDisplayName = args[4];
   
               var authContext = new AuthenticationContext($"https://login.microsoftonline.com/{strTenant}");
               var authResult = await authContext.AcquireTokenAsync(
                      "https://management.core.windows.net",
                      new ClientCredential(strClientId, strClientSecret));
   
               using (var client = new ManagementGroupsAPIClient(new TokenCredentials(authResult.AccessToken)))
               {
                   var mgRequest = new CreateManagementGroupRequest
                   {
                       DisplayName = strDisplayName
                   };
                   var response = await client.ManagementGroups.CreateOrUpdateAsync(strGroupId, mgRequest);
               }
           }
       }
   }
   ```

1. Crie e publique o aplicativo de console `mgCreate`:

   ```dotnetcli
   dotnet build
   dotnet publish -o {run-folder}
   ```

## <a name="create-the-management-group"></a>Criar o grupo de gerenciamento

Neste guia de início rápido, você criará um grupo de gerenciamento no grupo de gerenciamento raiz.

1. Altere os diretórios para o `{run-folder}` que você definiu com o comando `dotnet publish` anterior.

1. Insira o seguinte comando no terminal:

   ```bash
   mgCreate.exe `
      "{tenantId}" `
      "{clientId}" `
      "{clientSecret}" `
      "{groupID}" `
      "{displayName}"
   ```

Os comandos anteriores usam as seguintes informações:

- `{tenantId}` – Substitua pela ID de locatário
- `{clientId}` – substituir pela ID do cliente da entidade de serviço
- `{clientSecret}` – substituir pelo segredo do cliente da entidade de serviço
- `{groupID}` – fazer a substituição pela ID do novo grupo de gerenciamento
- `{displayName}` – fazer a substituição pelo nome amigável do seu novo grupo de gerenciamento

O resultado é um novo grupo de gerenciamento no grupo de gerenciamento raiz.

## <a name="clean-up-resources"></a>Limpar os recursos

- Exclua o novo grupo de gerenciamento pelo portal.

- Se desejar remover os aplicativos de console .NET Core e os pacotes instalados, exclua as pastas de projeto `mgCreate`.

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você criou um grupo de gerenciamento para organizar sua hierarquia de recursos. O grupo de gerenciamento pode conter assinaturas ou outros grupos de gerenciamento.

Para saber mais sobre grupos de gerenciamento e como gerenciar sua hierarquia de recursos, prossiga para:

> [!div class="nextstepaction"]
> [Gerenciar seus recursos com grupos de gerenciamento](./manage.md)