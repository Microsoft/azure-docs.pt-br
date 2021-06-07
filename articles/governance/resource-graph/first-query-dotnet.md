---
title: 'Início Rápido: Sua primeira consulta do .NET Core'
description: Neste início rápido, você seguirá as etapas para habilitar os pacotes NuGet do Resource Graph para .NET Core e executará sua primeira consulta.
ms.date: 01/27/2021
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: 0135dfd499af48b3c60314679f4c9b635a5ce15a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98917564"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-net-core"></a>Início Rápido: Execute sua primeira consulta ao Resource Graph usando o .NET Core

A primeira etapa para usar o Azure Resource Graph é verificar se os pacotes necessários para .NET Core estão instalados. Este início rápido orienta você no processo de adição dos pacotes à instalação do .NET Core.

No fim desse processo, você terá adicionado os pacotes à sua instalação do .NET Core e executará a primeira consulta ao Resource Graph.

## <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.
- Uma entidade de serviço do Azure, incluindo _clientId_ e _clientSecret_. Se você não tiver uma entidade de serviço para uso com o Resource Graph ou quiser criar uma, confira [Bibliotecas de gerenciamento do Azure para autenticação do .NET](/dotnet/azure/sdk/authentication#mgmt-auth).
  Ignore a etapa para instalar os pacotes do .NET Core, pois faremos isso nas próximas etapas.

## <a name="create-the-resource-graph-project"></a>Criar o projeto do Resource Graph

Para habilitar o .NET Core para consultar o Azure Resource Graph, crie um aplicativo de console e instale os pacotes necessários.

1. Verifique se o .NET Core mais recente está instalado (pelo menos **3.1.5**). Se ainda não estiver instalado, baixe-o em [dotnet.microsoft.com](https://dotnet.microsoft.com/download/dotnet-core).

1. Inicialize um novo aplicativo de console .NET Core chamado "argQuery":

   ```dotnetcli
   dotnet new console --name "argQuery"
   ```

1. Altere os diretórios para a nova pasta do projeto e instale os pacotes necessários para o Azure Resource Graph:

   ```dotnetcli
   # Add the Resource Graph package for .NET Core
   dotnet add package Microsoft.Azure.Management.ResourceGraph --version 2.0.0

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
   using Microsoft.Azure.Management.ResourceGraph;
   using Microsoft.Azure.Management.ResourceGraph.Models;

   namespace argQuery
   {
       class Program
       {
           static async Task Main(string[] args)
           {
               string strTenant = args[0];
               string strClientId = args[1];
               string strClientSecret = args[2];
               string strSubscriptionId = args[3];
               string strQuery = args[4];

               AuthenticationContext authContext = new AuthenticationContext("https://login.microsoftonline.com/" + strTenant);
               AuthenticationResult authResult = await authContext.AcquireTokenAsync("https://management.core.windows.net", new ClientCredential(strClientId, strClientSecret));
               ServiceClientCredentials serviceClientCreds = new TokenCredentials(authResult.AccessToken);

               ResourceGraphClient argClient = new ResourceGraphClient(serviceClientCreds);
               QueryRequest request = new QueryRequest();
               request.Subscriptions = new List<string>(){ strSubscriptionId };
               request.Query = strQuery;

               QueryResponse response = argClient.Resources(request);
               Console.WriteLine("Records: " + response.Count);
               Console.WriteLine("Data:\n" + response.Data);
           }
       }
   }
   ```

1. Crie e publique o aplicativo de console `argQuery`:

   ```dotnetcli
   dotnet build
   dotnet publish -o {run-folder}
   ```

## <a name="run-your-first-resource-graph-query"></a>Executar a primeira consulta ao Resource Graph

Com o aplicativo de console .NET Core criado e publicado, é hora de experimentar uma consulta do Resource Graph simples. A consulta retorna os cinco primeiros recursos do Azure com o **Nome** e o **Tipo de Recurso** de cada um.

Em cada chamada à `argQuery`, há variáveis usadas que precisam ser substituídas com seus valores:

- `{tenantId}` – Substitua pela ID de locatário
- `{clientId}` – substituir pela ID do cliente da entidade de serviço
- `{clientSecret}` – substituir pelo segredo do cliente da entidade de serviço
- `{subscriptionId}`: substitua por sua ID da assinatura

1. Altere os diretórios para o `{run-folder}` que você definiu com o comando `dotnet publish` anterior.

1. Execute sua primeira consulta do Azure Resource Graph usando o aplicativo .NET Core compilado:

   ```bash
   argQuery "{tenantId}" "{clientId}" "{clientSecret}" "{subscriptionId}" "Resources | project name, type | limit 5"
   ```

   > [!NOTE]
   > Devido a essa consulta de exemplo não fornecer um modificador de classificação, tal como `order by`, executar essa consulta várias vezes provavelmente produzirá um conjunto diferente de recursos por solicitação.

1. Altere o primeiro parâmetro para `argQuery.exe` e altere a consulta para `order by` a propriedade **Name**:

   ```bash
   argQuery "{tenantId}" "{clientId}" "{clientSecret}" "{subscriptionId}" "Resources | project name, type | limit 5 | order by name asc"
   ```

   > [!NOTE]
   > Assim como ocorre com a primeira consulta, executar esta consulta várias vezes provavelmente produzirá um conjunto diferente de recursos por solicitação. A ordem dos comandos de consulta é importante. Neste exemplo, o `order by` vem após o `limit`. Essa ordem de comando limita primeiro os resultados da consulta e os ordena.

1. Altere o parâmetro final para `argQuery.exe` e altere a consulta para, primeiro, `order by` a propriedade **Name** e depois `limit` conforme os primeiros cinco resultados:

   ```bash
   argQuery "{tenantId}" "{clientId}" "{clientSecret}" "{subscriptionId}" "Resources | project name, type | order by name asc | limit 5"
   ```

Quando a consulta final for executada várias vezes, supondo que nada em seu ambiente esteja sendo alterado, os resultados retornados serão consistentes e ordenados segundo a propriedade **Name**, mas ainda limitados aos cinco primeiros resultados.

## <a name="clean-up-resources"></a>Limpar os recursos

Se você quiser remover o aplicativo de console do .NET Core e os pacotes instalados, poderá fazer isso excluindo a pasta de projeto `argQuery`.

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você criou um aplicativo de console do .NET Core com os pacotes do Resource Graph necessários e executou sua primeira consulta. Para saber mais sobre a linguagem do Resource Graph, prossiga para a página de detalhes da linguagem de consulta.

> [!div class="nextstepaction"]
> [Obter mais informações sobre a linguagem de consulta](./concepts/query-language.md)