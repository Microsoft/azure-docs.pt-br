---
title: Criar um novo recurso do Azure Application Insights | Microsoft Docs
description: Configure manualmente o monitoramento do Application Insights para um novo aplicativo em tempo real.
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: e69611d650f61630c8b36f7387b79cffbb61f227
ms.sourcegitcommit: 1a98b3f91663484920a747d75500f6d70a6cb2ba
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99062906"
---
# <a name="create-an-application-insights-resource"></a>Criar um recurso do Application Insights

O Azure Application Insights exibe dados sobre o seu aplicativo em um *recurso* do Microsoft Azure. Por isso, criar um recurso faz parte da [configuração do Application Insights para monitorar um novo aplicativo][start]. Depois de criar o recurso, você pode obter a chave de instrumentação dele e usá-la para configurar o SDK do Application Insights. A chave de instrumentação vincula sua telemetria ao recurso.

## <a name="sign-in-to-microsoft-azure"></a>Entrar no Microsoft Azure

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="create-an-application-insights-resource"></a>Criar um recurso do Application Insights

Entre no [portal do Azure](https://portal.azure.com) e crie um recurso do Application Insights:

![Clique no sinal de "+" no canto superior esquerdo. Selecionar Ferramentas para Desenvolvedores seguido por Application Insights](./media/create-new-resource/new-app-insights.png)

   | Configurações        |  Valor           | Descrição  |
   | ------------- |:-------------|:-----|
   | **Nome**      | `Unique value` | Nome que identifica o aplicativo que você está monitorando. |
   | **Grupo de recursos**     | `myResourceGroup`      | Nome para o grupo de recursos novo ou existente no qual hospedar dados do Application Insights. |
   | **Região** | `East US` | Escolha uma localização perto de você ou de onde seu aplicativo está hospedado. |
   | **Modo de Recurso** | `Classic` ou `Workspace-based` | Os recursos baseados em workspace estão atualmente em versão prévia pública e permitem que você envie sua telemetria do Application Insights para um workspace comum do Log Analytics. Para obter mais informações, confira o [artigo sobre recursos baseados em workspace](create-workspace-resource.md).

> [!NOTE]
> Embora você possa usar o mesmo nome de recurso em grupos de recursos diferentes, pode ser benéfico usar um nome globalmente exclusivo. Isso pode ser útil se você planeja [executar consultas entre recursos diferentes](../log-query/cross-workspace-query.md#identifying-an-application), pois simplifica a sintaxe necessária.

Insira os valores apropriados nos campos obrigatórios e selecione **Examinar + criar**.

> [!div class="mx-imgBorder"]
> ![Insira os valores nos campos obrigatórios e, em seguida, selecione "examinar + criar".](./media/create-new-resource/review-create.png)

Quando seu aplicativo tiver sido criado, um novo painel será aberto. Esse painel mostra os dados de uso e de desempenho do seu aplicativo monitorado. 

## <a name="copy-the-instrumentation-key"></a>Copiar a chave de instrumentação

A chave de instrumentação identifica o recurso com o qual você deseja associar os dados de telemetria. Você precisará copiar a chave de instrumentação e adicioná-la ao código do aplicativo.

> [!IMPORTANT]
> Novas regiões do Azure **exigem** o uso de cadeias de conexão em vez de chaves de instrumentação. A [cadeia de conexão](./sdk-connection-string.md?tabs=net) identifica o recurso ao qual você deseja associar os dados de telemetria. Ele também permite que você modifique os pontos de extremidade que o recurso usará como um destino para a telemetria. Você precisará copiar a cadeia de conexão e adicioná-la ao código do aplicativo ou a uma variável de ambiente.

## <a name="install-the-sdk-in-your-app"></a>Instalar o SDK em seu aplicativo

Instale o SDK do Application Insights SDK no aplicativo. Esta etapa depende muito do tipo do seu aplicativo.

Use a chave de instrumentação para configurar o [SDK instalado no seu aplicativo][start].

O SDK inclui módulos padrão que enviam telemetria sem precisar escrever nenhum código adicional. Para rastrear ações do usuário ou diagnosticar problemas mais detalhadamente, [use a API][api] para enviar sua própria telemetria.

## <a name="creating-a-resource-automatically"></a>Criando um recurso automaticamente

### <a name="powershell"></a>PowerShell

Criar um novo recurso do Application Insights

```powershell
New-AzApplicationInsights [-ResourceGroupName] <String> [-Name] <String> [-Location] <String> [-Kind <String>]
 [-Tag <Hashtable>] [-DefaultProfile <IAzureContextContainer>] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="example"></a>Exemplo

```powershell
New-AzApplicationInsights -Kind java -ResourceGroupName testgroup -Name test1027 -location eastus
```
#### <a name="results"></a>Resultados

```powershell
Id                 : /subscriptions/{subid}/resourceGroups/testgroup/providers/microsoft.insights/components/test1027
ResourceGroupName  : testgroup
Name               : test1027
Kind               : web
Location           : eastus
Type               : microsoft.insights/components
AppId              : 8323fb13-32aa-46af-b467-8355cf4f8f98
ApplicationType    : web
Tags               : {}
CreationDate       : 10/27/2017 4:56:40 PM
FlowType           :
HockeyAppId        :
HockeyAppToken     :
InstrumentationKey : 00000000-aaaa-bbbb-cccc-dddddddddddd
ProvisioningState  : Succeeded
RequestSource      : AzurePowerShell
SamplingPercentage :
TenantId           : {subid}
```

Para obter a documentação completa do PowerShell para esse cmdlet e saber como recuperar a chave de instrumentação, consulte a [documentação do Azure PowerShell](/powershell/module/az.applicationinsights/new-azapplicationinsights).

### <a name="azure-cli-preview"></a>CLI do Azure (versão prévia)

Para acessar a versão prévia dos comandos da CLI do Azure do Application Insights, primeiro você precisa executar:

```azurecli
 az extension add -n application-insights
```

Se você não executar o comando `az extension add`, verá a seguinte mensagem de erro: `az : ERROR: az monitor: 'app-insights' is not in the 'az monitor' command group. See 'az monitor --help'.`

Agora você pode executar o seguinte para criar o recurso do Application Insights:

```azurecli
az monitor app-insights component create --app
                                         --location
                                         --resource-group
                                         [--application-type]
                                         [--kind]
                                         [--tags]
```

#### <a name="example"></a>Exemplo

```azurecli
az monitor app-insights component create --app demoApp --location westus2 --kind web -g demoRg --application-type web
```

#### <a name="results"></a>Resultados

```azurecli
az monitor app-insights component create --app demoApp --location eastus --kind web -g demoApp  --application-type web
{
  "appId": "87ba512c-e8c9-48d7-b6eb-118d4aee2697",
  "applicationId": "demoApp",
  "applicationType": "web",
  "creationDate": "2019-08-16T18:15:59.740014+00:00",
  "etag": "\"0300edb9-0000-0100-0000-5d56f2e00000\"",
  "flowType": "Bluefield",
  "hockeyAppId": null,
  "hockeyAppToken": null,
  "id": "/subscriptions/{subid}/resourceGroups/demoApp/providers/microsoft.insights/components/demoApp",
  "instrumentationKey": "00000000-aaaa-bbbb-cccc-dddddddddddd",
  "kind": "web",
  "location": "eastus",
  "name": "demoApp",
  "provisioningState": "Succeeded",
  "requestSource": "rest",
  "resourceGroup": "demoApp",
  "samplingPercentage": null,
  "tags": {},
  "tenantId": {tenantID},
  "type": "microsoft.insights/components"
}
```

Para obter a documentação completa da CLI do Azure para esse comando e saber como recuperar a chave de instrumentação, consulte a [documentação da CLI do Azure](/cli/azure/ext/application-insights/monitor/app-insights/component#ext-application-insights-az-monitor-app-insights-component-create).

## <a name="next-steps"></a>Próximas etapas
* [Pesquisa de Diagnóstico](./diagnostic-search.md)
* [Explorar métricas](../platform/metrics-charts.md)
* [Escrever consultas do Analytics](../log-query/log-query-overview.md)

<!--Link references-->

[api]: ./api-custom-events-metrics.md
[diagnostic]: ./diagnostic-search.md
[metrics]: ../platform/metrics-charts.md
[start]: ./app-insights-overview.md

