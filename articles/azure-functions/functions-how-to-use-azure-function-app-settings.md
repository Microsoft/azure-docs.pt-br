---
title: Definir configurações do aplicativo de funções no Azure Functions
description: Saiba como definir as configurações do aplicativo de funções no Azure Functions.
ms.assetid: 81eb04f8-9a27-45bb-bf24-9ab6c30d205c
ms.topic: conceptual
ms.date: 04/13/2020
ms.custom: cc996988-fb4f-47, devx-track-azurecli
ms.openlocfilehash: 5080d16a7b14506b24e07e2ee4ba862c645f83a8
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98875442"
---
# <a name="manage-your-function-app"></a>Gerenciar seu aplicativo de funções 

No Azure Functions, um aplicativo de funções fornece o contexto de execução para suas funções individuais. Os comportamentos do aplicativo de funções se aplicam a todas as funções hospedadas por um aplicativo função de determinada. Todas as funções em um aplicativo de funções devem ser do mesmo [idioma](supported-languages.md). 

As funções individuais em um aplicativo de funções são implantadas juntas e dimensionadas juntas. Todas as funções no mesmo aplicativo de funções compartilham recursos, por instância, à medida que o aplicativo de funções é dimensionado. 

Cadeias de conexão, variáveis de ambiente e outras configurações de aplicativo são definidas separadamente para cada aplicativo de funções. Todos os dados que devem ser compartilhados entre os aplicativos de funções devem ser armazenados externamente em um repositório persistente.

## <a name="get-started-in-the-azure-portal"></a>Introdução ao portal do Azure

1. Acesse o [portal do Azure] e entre usando sua conta do Azure. Na barra de pesquisa na parte superior do portal, insira o nome do seu aplicativo de funções e selecione-o na lista. 

2. Em **configurações** no painel esquerdo, selecione **configuração**.

    :::image type="content" source="./media/functions-how-to-use-azure-function-app-settings/azure-function-app-main.png" alt-text="Visão geral do aplicativo de funções no portal do Azure":::

Você pode navegar até tudo o que precisa para gerenciar seu aplicativo de funções na página Visão geral, em particular as **[configurações do aplicativo](#settings)** e os **[recursos da plataforma](#platform-features)**.

## <a name="work-with-application-settings"></a><a name="settings"></a>Trabalhar com as configurações do aplicativo

As configurações do aplicativo podem ser gerenciadas no [portal do Azure](functions-how-to-use-azure-function-app-settings.md?tabs=portal#settings) e usando o [CLI do Azure](functions-how-to-use-azure-function-app-settings.md?tabs=azurecli#settings) e [Azure PowerShell](functions-how-to-use-azure-function-app-settings.md?tabs=powershell#settings). Você também pode gerenciar as configurações do aplicativo de [Visual Studio Code](functions-develop-vs-code.md#application-settings-in-azure) e do [Visual Studio](functions-develop-vs.md#function-app-settings). 

Essas configurações são armazenadas criptografadas. Para saber mais, consulte [segurança de configurações de aplicativo](security-concepts.md#application-settings).

# <a name="portal"></a>[Portal](#tab/portal)

Para localizar as configurações do aplicativo, consulte Introdução ao [portal do Azure](#get-started-in-the-azure-portal). 

A guia **configurações do aplicativo** mantém as configurações que são usadas pelo seu aplicativo de funções. Você deve selecionar **Mostrar valores** para ver os valores no Portal. Para adicionar uma configuração no portal, selecione **nova configuração de aplicativo** e adicione o novo par chave-valor.

![Configurações do aplicativo de funções no portal do Azure.](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-settings-tab.png)

# <a name="azure-cli"></a>[CLI do Azure](#tab/azurecli)

O [`az functionapp config appsettings list`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-list) comando retorna as configurações de aplicativo existentes, como no exemplo a seguir:

```azurecli-interactive
az functionapp config appsettings list --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME>
```

O [`az functionapp config appsettings set`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) comando adiciona ou atualiza uma configuração de aplicativo. O exemplo a seguir cria uma configuração com uma chave chamada `CUSTOM_FUNCTION_APP_SETTING` e um valor de `12345` :


```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--settings CUSTOM_FUNCTION_APP_SETTING=12345
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

O [`Get-AzFunctionAppSetting`](/powershell/module/az.functions/get-azfunctionappsetting) cmdlet retorna as configurações de aplicativo existentes, como no exemplo a seguir: 

```azurepowershell-interactive
Get-AzFunctionAppSetting -Name <FUNCTION_APP_NAME> -ResourceGroupName <RESOURCE_GROUP_NAME>
```

O [`Update-AzFunctionAppSetting`](/powershell/module/az.functions/update-azfunctionappsetting) comando adiciona ou atualiza uma configuração de aplicativo. O exemplo a seguir cria uma configuração com uma chave chamada `CUSTOM_FUNCTION_APP_SETTING` e um valor de `12345` :

```azurepowershell-interactive
Update-AzFunctionAppSetting -Name <FUNCTION_APP_NAME> -ResourceGroupName <RESOURCE_GROUP_NAME> -AppSetting @{"CUSTOM_FUNCTION_APP_SETTING" = "12345"}
```

---

### <a name="use-application-settings"></a>Usar configurações do aplicativo

[!INCLUDE [functions-environment-variables](../../includes/functions-environment-variables.md)]

Ao desenvolver um aplicativo de funções localmente, você deve manter cópias locais desses valores na local.settings.jsno arquivo de projeto. Para obter mais informações, consulte [Local Settings File](functions-run-local.md#local-settings-file).

## <a name="hosting-plan-type"></a>Tipo de plano de hospedagem

Ao criar um aplicativo de funções, você também cria um plano de hospedagem no qual o aplicativo é executado. Um plano pode ter um ou mais aplicativos de funções. A funcionalidade, o dimensionamento e o preço de suas funções dependem do tipo de plano. Para saber mais, confira [Azure Functions opções de hospedagem](functions-scale.md).

Você pode determinar o tipo de plano que está sendo usado pelo seu aplicativo de funções do portal do Azure ou usando as APIs CLI do Azure ou Azure PowerShell. 

Os valores a seguir indicam o tipo de plano:

| Tipo de plano | Portal | CLI do Azure/PowerShell |
| --- | --- | --- |
| [Consumo](consumption-plan.md) | **Consumo** | `Dynamic` |
| [Premium](functions-premium-plan.md) | **ElasticPremium** | `ElasticPremium` |
| [Dedicado (serviço de aplicativo)](dedicated-plan.md) | Vários | Vários |

# <a name="portal"></a>[Portal](#tab/portal)

Para determinar o tipo de plano usado pelo seu aplicativo de funções, consulte **plano do serviço de aplicativo** na guia **visão geral** do aplicativo de funções no [portal do Azure](https://portal.azure.com). Para ver o tipo de preço, selecione o nome do **plano do serviço de aplicativo** e, em seguida, selecione **Propriedades** no painel esquerdo.

![Exibir o plano de dimensionamento no portal](./media/functions-scale/function-app-overview-portal.png)

# <a name="azure-cli"></a>[CLI do Azure](#tab/azurecli)

Execute o seguinte comando CLI do Azure para obter o tipo de plano de hospedagem:

```azurecli-interactive
functionApp=<FUNCTION_APP_NAME>
resourceGroup=FunctionMonitoringExamples
appServicePlanId=$(az functionapp show --name $functionApp --resource-group $resourceGroup --query appServicePlanId --output tsv)
az appservice plan list --query "[?id=='$appServicePlanId'].sku.tier" --output tsv

```  

No exemplo anterior, substitua `<RESOURCE_GROUP>` e `<FUNCTION_APP_NAME>` pelos nomes do grupo de recursos e do aplicativo de funções, respectivos. 

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Execute o seguinte comando Azure PowerShell para obter o tipo de plano de hospedagem:

```azurepowershell-interactive
$FunctionApp = '<FUNCTION_APP_NAME>'
$ResourceGroup = '<RESOURCE_GROUP>'

$PlanID = (Get-AzFunctionApp -ResourceGroupName $ResourceGroup -Name $FunctionApp).AppServicePlan
(Get-AzFunctionAppPlan -Name $PlanID -ResourceGroupName $ResourceGroup).SkuTier
```
No exemplo anterior, substitua `<RESOURCE_GROUP>` e `<FUNCTION_APP_NAME>` pelos nomes do grupo de recursos e do aplicativo de funções, respectivos. 

---

## <a name="plan-migration"></a>Planejar a migração

Você pode usar CLI do Azure comandos para migrar um aplicativo de funções entre um plano de consumo e um plano Premium no Windows. Os comandos específicos dependem da direção da migração. Atualmente, não há suporte para a migração direta para um plano dedicado (serviço de aplicativo).

Não há suporte para essa migração no Linux.

### <a name="consumption-to-premium"></a>Consumo para Premium

Use o procedimento a seguir para migrar de um plano de consumo para um plano Premium no Windows:

1. Execute o comando a seguir para criar um novo plano do serviço de aplicativo (Premium elástico) na mesma região e no mesmo grupo de recursos que o aplicativo de funções existente.  

    ```azurecli-interactive
    az functionapp plan create --name <NEW_PREMIUM_PLAN_NAME> --resource-group <MY_RESOURCE_GROUP> --location <REGION> --sku EP1
    ```

1. Execute o comando a seguir para migrar o aplicativo de funções existente para o novo plano Premium

    ```azurecli-interactive
    az functionapp update --name <MY_APP_NAME> --resource-group <MY_RESOURCE_GROUP> --plan <NEW_PREMIUM_PLAN>
    ```

1. Se você não precisar mais do plano do aplicativo de funções de consumo anterior, exclua o plano do aplicativo de funções original depois de confirmar que você migrou com êxito para o novo. Execute o comando a seguir para obter uma lista de todos os planos de consumo em seu grupo de recursos.

    ```azurecli-interactive
    az functionapp plan list --resource-group <MY_RESOURCE_GROUP> --query "[?sku.family=='Y'].{PlanName:name,Sites:numberOfSites}" -o table
    ```

    Você pode excluir com segurança o plano com zero sites, que é aquele do qual você migrou.

1. Execute o comando a seguir para excluir o plano de consumo do qual você migrou.

    ```azurecli-interactive
    az functionapp plan delete --name <CONSUMPTION_PLAN_NAME> --resource-group <MY_RESOURCE_GROUP>
    ```

### <a name="premium-to-consumption"></a>Premium para consumo

Use o procedimento a seguir para migrar de um plano Premium para um plano de consumo no Windows:

1. Execute o comando a seguir para criar um novo aplicativo de funções (consumo) na mesma região e no mesmo grupo de recursos que o aplicativo de funções existente. Esse comando também cria um novo plano de consumo no qual o aplicativo de funções é executado.

    ```azurecli-interactive
    az functionapp create --resource-group <MY_RESOURCE_GROUP> --name <NEW_CONSUMPTION_APP_NAME> --consumption-plan-location <REGION> --runtime dotnet --functions-version 3 --storage-account <STORAGE_NAME>
    ```

1. Execute o comando a seguir para migrar o aplicativo de funções existente para o novo plano de consumo.

    ```azurecli-interactive
    az functionapp update --name <MY_APP_NAME> --resource-group <MY_RESOURCE_GROUP> --plan <NEW_CONSUMPTION_PLAN>
    ```

1. Exclua o aplicativo de funções criado na etapa 1, pois você só precisa do plano que foi criado para executar o aplicativo de funções existente.

    ```azurecli-interactive
    az functionapp delete --name <NEW_CONSUMPTION_APP_NAME> --resource-group <MY_RESOURCE_GROUP>
    ```

1. Se você não precisar mais do plano do aplicativo de funções Premium anterior, exclua seu plano do aplicativo de funções original depois de confirmar que você migrou com êxito para o novo. Observe que, se o plano não for excluído, você ainda será cobrado pelo plano Premium. Execute o comando a seguir para obter uma lista de todos os planos Premium em seu grupo de recursos.

    ```azurecli-interactive
    az functionapp plan list --resource-group <MY_RESOURCE_GROUP> --query "[?sku.family=='EP'].{PlanName:name,Sites:numberOfSites}" -o table
    ```

1. Execute o comando a seguir para excluir o plano Premium do qual você migrou.

    ```azurecli-interactive
    az functionapp plan delete --name <PREMIUM_PLAN> --resource-group <MY_RESOURCE_GROUP>
    ```

## <a name="platform-features"></a>Recursos da plataforma

Os aplicativos de funções são executados no e são mantidos pelo, a plataforma de serviço Azure App. Dessa forma, seus aplicativos de funções têm acesso à maioria dos recursos da principal plataforma de hospedagem na Web do Azure. O painel esquerdo é onde você acessa os vários recursos da plataforma do serviço de aplicativo que você pode usar em seus aplicativos de funções. 

> [!NOTE]
> Nem todos os recursos do Serviço de Aplicativo estão disponíveis quando um aplicativo de funções é executado no plano de hospedagem de Consumo.

O restante deste artigo se concentra nos seguintes recursos do serviço de aplicativo no portal do Azure que são úteis para as funções do:

+ [Editor do Serviço de Aplicativo](#editor)
+ [Console](#console)
+ [Ferramentas avançadas (Kudu)](#kudu)
+ [Opções de implantação](#deployment)
+ [CORS](#cors)
+ [Autenticação](#auth)

Para saber mais sobre como trabalhar com configurações da Serviço de Aplicativo, confira [Definir configurações do Serviço de Aplicativo do Azure](../app-service/configure-common.md).

### <a name="app-service-editor"></a><a name="editor"></a>Editor do Serviço de Aplicativo

![O Editor de Serviço de Aplicativo](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-appservice-editor.png)

O Editor do Serviço de Aplicativo é um editor avançado dentro do portal que você pode usar para modificar arquivos de configuração JSON e arquivos de código. Escolher essa opção inicia uma nova guia do navegador com um editor básico. Isso permite que você se integre ao repositório do Git, execute e depure código, além de modificar as configurações do aplicativos de funções. Esse editor fornece um ambiente de desenvolvimento aprimorado para suas funções comparadas com o editor de funções interno.  

Recomendamos que você considere o desenvolvimento de suas funções no computador local. Quando você desenvolve localmente e publica no Azure, seus arquivos de projeto são somente leitura no Portal. Para saber mais, confira [código e teste Azure Functions localmente](functions-develop-local.md).

### <a name="console"></a><a name="console"></a>Console

![Console do aplicativo de funções](./media/functions-how-to-use-azure-function-app-settings/configure-function-console.png)

O console no portal é uma ferramenta ideal de desenvolvimento quando você preferir interagir com seu aplicativo de funções na linha de comando. Os comandos comuns incluem criação e navegação de diretório e arquivo, bem como execução scripts e arquivos em lote. 

Ao desenvolver localmente, é recomendável usar o [Azure Functions Core Tools](functions-run-local.md) e o [CLI do Azure].

### <a name="advanced-tools-kudu"></a><a name="kudu"></a>Ferramentas avançadas (Kudu)

![Configurar Kudu](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-kudu.png)

As ferramentas avançadas para o Serviço de Aplicativo (também conhecidas como Kudu) fornecem acesso a recursos administrativos avançados de seu aplicativo de funções. No Kudu, você pode gerenciar informações do sistema, configurações do aplicativo, variáveis de ambiente, extensões do site, cabeçalhos HTTP e variáveis de servidor. Você também pode iniciar o **Kudu** navegando até o ponto de extremidade SCM de seu aplicativo de funções, como`https://<myfunctionapp>.scm.azurewebsites.net/` 


### <a name="deployment-center"></a><a name="deployment"></a>Centro de Implantação

Quando você usa uma solução de controle do código-fonte para desenvolver e manter seu código de funções, a central de implantação permite criar e implantar a partir do controle do código-fonte. Seu projeto é compilado e implantado no Azure quando você faz atualizações. Para obter mais informações, consulte [tecnologias de implantação no Azure Functions](functions-deployment-technologies.md).

### <a name="cross-origin-resource-sharing"></a><a name="cors"></a>Compartilhamento de recursos entre origens

Para evitar a execução de código mal-intencionado no cliente, os navegadores modernos bloqueiam solicitações de aplicativos Web para recursos em execução em um domínio separado. O [CORS (compartilhamento de recursos entre origens)](https://developer.mozilla.org/docs/Web/HTTP/CORS) permite `Access-Control-Allow-Origin` que um cabeçalho declare quais origens têm permissão para chamar pontos de extremidade em seu aplicativo de funções.

#### <a name="portal"></a>Portal

Quando você configura a lista de **origens permitidas** para seu aplicativo de funções, o `Access-Control-Allow-Origin` cabeçalho é automaticamente adicionado a todas as respostas de pontos de extremidade http em seu aplicativo de funções. 

![Configurar lista CORS do aplicativo de funções](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-cors.png)

Quando o curinga ( `*` ) é usado, todos os outros domínios são ignorados. 

Use o [`az functionapp cors add`](/cli/azure/functionapp/cors#az-functionapp-cors-add) comando para adicionar um domínio à lista de origens permitidas. O exemplo a seguir adiciona o domínio contoso.com:

```azurecli-interactive
az functionapp cors add --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--allowed-origins https://contoso.com
```

Use o [`az functionapp cors show`](/cli/azure/functionapp/cors#az-functionapp-cors-show) comando para listar as origens permitidas atualmente.

### <a name="authentication"></a><a name="auth"></a>Autenticação

![Configurar a autenticação para um aplicativo de funções](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-authentication.png)

Quando as funções usam um gatilho HTTP, você pode exigir que as chamadas sejam autenticadas primeiro. O serviço de aplicativo dá suporte à autenticação Azure Active Directory e a entrar com provedores sociais, como Facebook, Microsoft e Twitter. Para obter detalhes sobre como configurar provedores de autenticação específicos, consulte [Visão geral de autenticação do serviço de aplicativo do Azure](../app-service/overview-authentication-authorization.md). 


## <a name="next-steps"></a>Próximas etapas

+ [Definir configurações do Serviço de Aplicativo do Azure](../app-service/configure-common.md)
+ [Implantação contínua para Azure Functions](functions-continuous-deployment.md)

[CLI do Azure]: /cli/azure/
[Azure portal]: https://portal.azure.com
