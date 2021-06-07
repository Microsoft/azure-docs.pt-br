---
title: 'Início Rápido: Sua primeira consulta à API REST'
description: Neste guia de início rápido, você seguirá as etapas para chamar o ponto de extremidade do Resource Graph para a API REST e executar sua primeira consulta.
ms.date: 01/27/2021
ms.topic: quickstart
ms.openlocfilehash: 670ceba95d937be278c44c34704cb844eead480d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98920043"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-rest-api"></a>Início Rápido: Execute sua primeira consulta ao Resource Graph usando a API REST

A primeira etapa para usar o Azure Resource Graph com a API REST é verificar se você tem uma ferramenta para chamar as APIs REST disponíveis. Este início rápido orienta você pelo processo de execução de uma consulta e recuperação dos resultados chamando o ponto de extremidade da API REST do Azure Resource Graph.

No final desse processo, você terá as ferramentas para chamar os pontos de extremidade da API REST e executar sua primeira consulta do Resource Graph.

## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="getting-started-with-rest-api"></a>Introdução à API REST

Se você não estiver familiarizado com a API REST, comece examinando a [Referência de API REST do Azure](/rest/api/azure/) para obter um entendimento geral sobre a API REST, especificamente o corpo da solicitação e o URI de solicitação. Este artigo usa esses conceitos para fornecer instruções para trabalhar com o Azure Resource Graph e pressupõe um conhecimento prático deles. Ferramentas como [ARMClient](https://github.com/projectkudu/ARMClient) e outras pessoas podem lidar com autorização automaticamente e são recomendadas para iniciantes.

Para obter as especificações do Azure Resource Graph, confira [API REST do Azure Resource Graph](/rest/api/azure-resourcegraph/).

### <a name="rest-api-and-powershell"></a>API REST e PowerShell

Se você ainda não tiver uma ferramenta para fazer chamadas à API REST, considere usar o PowerShell para essas instruções. O exemplo de código a seguir obtém um cabeçalho para autenticação com o Azure. Gere um cabeçalho de autenticação, às vezes chamado de um **token de portador**, e forneça o URI da API REST para se conectar com quaisquer parâmetros ou um **corpo da solicitação**:

```azurepowershell-interactive
# Log in first with Connect-AzAccount if not using Cloud Shell

$azContext = Get-AzContext
$azProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
$profileClient = New-Object -TypeName Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient -ArgumentList ($azProfile)
$token = $profileClient.AcquireAccessToken($azContext.Subscription.TenantId)
$authHeader = @{
    'Content-Type'='application/json'
    'Authorization'='Bearer ' + $token.AccessToken
}

# Invoke the REST API
$restUri = 'https://management.azure.com/subscriptions/{subscriptionId}?api-version=2020-01-01'
$response = Invoke-RestMethod -Uri $restUri -Method Get -Headers $authHeader
```

Substitua `{subscriptionId}` na variável `$restUri` para obter informações sobre a sua assinatura.
A variável `$response` armazena o resultado do cmdlet `Invoke-RestMethod`, que pode ser analisado com os cmdlets, como [ConvertFrom-Json](/powershell/module/microsoft.powershell.utility/convertfrom-json). Se o ponto de extremidade de serviço da API REST espera um **Corpo da Solicitação**, forneça uma variável formatada do JSON para o parâmetro `-Body` de `Invoke-RestMethod`.

## <a name="run-your-first-resource-graph-query"></a>Executar a primeira consulta ao Resource Graph

Com as ferramentas da API REST adicionadas ao ambiente de sua escolha, é hora de experimentar uma consulta simples ao Resource Graph. A consulta retorna os cinco primeiros recursos do Azure com o **Nome** e o **Tipo de Recurso** de cada um.

No corpo da solicitação de cada chamada à API REST, há uma variável usada que você precisa substituir pelo seu valor:

- `{subscriptionID}`: substitua por sua ID da assinatura

1. Execute sua primeira consulta do Azure Resource Graph usando a API REST e o ponto de extremidade `resources`:

   - URI da API REST

     ```http
     POST https://management.azure.com/providers/Microsoft.ResourceGraph/resources?api-version=2019-04-01
     ```

   - Corpo da solicitação

     ```json
     {
         "subscriptions": [
             "{subscriptionID}"
         ],
         "query": "Resources | project name, type | limit 5"
     }
     ```

   > [!NOTE]
   > Por essa consulta de exemplo não fornecer um modificador de classificação, como `order by`, executar essa consulta várias vezes provavelmente produzirá um conjunto diferente de recursos por solicitação.

1. Atualize a chamada ao ponto de extremidade `resouces` e altere a **consulta** para `order by` a propriedade **Name**:

   - URI da API REST

     ```http
     POST https://management.azure.com/providers/Microsoft.ResourceGraph/resources?api-version=2019-04-01
     ```

   - Corpo da solicitação

     ```json
     {
         "subscriptions": [
             "{subscriptionID}"
         ],
         "query": "Resources | project name, type | limit 5 | order by name asc"
     }
     ```

   > [!NOTE]
   > Assim como ocorre com a primeira consulta, executar esta consulta várias vezes provavelmente produzirá um conjunto diferente de recursos por solicitação. A ordem dos comandos de consulta é importante. Neste exemplo, o `order by` vem após o `limit`. Essa ordem de comando limita primeiro os resultados da consulta e os ordena.

1. Atualize a chamada ao ponto de extremidade `resources` e altere a **consulta** para primeiro `order by` a propriedade **Name** e então `limit` os cinco principais resultados:

   - URI da API REST

     ```http
     POST https://management.azure.com/providers/Microsoft.ResourceGraph/resources?api-version=2019-04-01
     ```

   - Corpo da solicitação

     ```json
     {
         "subscriptions": [
             "{subscriptionID}"
         ],
         "query": "Resources | project name, type | order by name asc | limit 5"
     }
     ```

Quando a consulta final for executada várias vezes, supondo que nada em seu ambiente esteja sendo alterado, os resultados retornados serão consistentes e ordenados segundo a propriedade **Name**, mas ainda limitados aos cinco primeiros resultados.

Para obter mais exemplos de chamadas à API REST para o Azure Resource Graph, confira os [Exemplos de REST do Azure Resource Graph](/rest/api/azureresourcegraph/resourcegraph(2019-04-01)/resources/resources#examples).

## <a name="clean-up-resources"></a>Limpar os recursos

A API REST não tem bibliotecas nem módulos para desinstalar. Se você tiver instalado uma ferramenta como _ARMClient_ ou _Postman_ para fazer as chamadas e não precisar mais dela, você poderá desinstalar a ferramenta agora.

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você chamou o ponto de extremidade da API REST do Resource Graph e executou sua primeira consulta. Para saber mais sobre a linguagem do Resource Graph, prossiga para a página de detalhes da linguagem de consulta.

> [!div class="nextstepaction"]
> [Obter mais informações sobre a linguagem de consulta](./concepts/query-language.md)
