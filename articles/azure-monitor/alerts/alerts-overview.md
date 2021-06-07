---
title: Visão geral de alertas e monitoramento de notificações do Azure
description: Visão geral de alertas no Azure Monitor
ms.topic: conceptual
ms.date: 02/14/2021
ms.openlocfilehash: 6785cfdf673e4c2da03ff26649c9336d57b699c8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102038043"
---
# <a name="overview-of-alerts-in-microsoft-azure"></a>Visão geral dos alertas no Microsoft Azure 

Este artigo descreve o que são alertas, seus benefícios e como começar a usá-los.  

## <a name="what-are-alerts-in-microsoft-azure"></a>O que são os alertas no Microsoft Azure?

Os alertas o notificam proativamente quando são encontrados problemas com sua infraestrutura ou aplicativo usando seus dados de monitoramento em Azure Monitor. Eles permitem que você identifique e resolva problemas antes que os usuários do seu sistema os percebam. 

## <a name="overview"></a>Visão geral

O diagrama a seguir representa o fluxo de alertas. 

![Diagrama do fluxo de alerta](media/alerts-overview/Azure-Monitor-Alerts.svg)

As regras de alerta são separadas dos alertas e das ações tomadas quando um alerta é disparado. A regra de alerta captura o destino e os critérios para alertas. A regra de alerta pode estar em estado habilitado ou desabilitado. Os alertas disparam apenas quando estão habilitados. 

Veja a seguir os principais atributos de uma regra de alerta:

**Recurso de Destino** - Define o escopo e os sinais disponíveis para o alerta. Um destino pode ser um recurso do Azure. Destinos de exemplo:

- Máquinas virtuais.
- Contas de armazenamento.
- Workspace do Log Analytics.
- Application Insights. 

Para determinados recursos (como máquinas virtuais), você pode especificar vários recursos como o destino da regra de alerta.

**Sinal** emitido pelo recurso de destino. Os sinais podem ser dos seguintes tipos: métrica, log de atividades, Application Insights e log.

**Critérios** – uma combinação de sinal e lógica aplicada em um recurso de destino. Exemplos: 

- Porcentagem de CPU > 70%
- Tempo de resposta do servidor > 4 ms 
- Contagem de resultados de uma consulta de log > 100

**Nome do alerta** – um nome específico para a regra de alerta configurada pelo usuário.

**Descrição do alerta** -uma descrição para a regra de alerta configurada pelo usuário.

**Severidade** – a severidade do alerta depois que os critérios especificados na regra de alerta são atendidos. A gravidade pode variar de 0 a 4.

- Sev 0 = crítico
- Sev 1 = erro
- Sev 2 = aviso
- Sev 3 = informativo
- Sev 4 = detalhado 

**Ação** - Uma ação específica executada quando o alerta é disparado. Para obter mais informações, consulte [grupos de ações](../alerts/action-groups.md).

## <a name="what-you-can-alert-on"></a>Sobre o que você pode alertar

Você pode alertar sobre métricas e logs, conforme descrito em [monitorando fontes de dados](./../agents/data-sources.md). Os sinais incluem, mas não estão limitados a:

- Valores métricos
- Consultas da pesquisa de logs
- Eventos de log de atividades
- Integridade da plataforma subjacente do Azure
- Testes de disponibilidade do site

## <a name="manage-alerts"></a>Gerenciar alertas

Você pode definir o estado de um alerta para especificar onde ele está no processo de resolução. Quando os critérios especificados na regra de alerta são atendidos, um alerta é criado ou acionado e tem um status *novo*. É possível alterar o status ao reconhecer um alerta e ao fechá-lo. Todas as alterações de estado são armazenadas no histórico do alerta.

Os seguintes estados de alerta são compatíveis.

| Estado | Descrição |
|:---|:---|
| Novo | O problema foi detectado e ainda não foi revisado. |
| Confirmado | Um administrador examinou o alerta e começou a trabalhar nele. |
| Fechadas | O problema foi resolvido. Depois que um alerta for fechado, será possível reabri-lo, alterando-o para outro estado. |

O *estado de alerta* é diferente e independente da *condição do monitor*. O estado de alerta é definido pelo usuário. A condição do monitor é definida pelo sistema. Quando um alerta é disparado, a condição de monitor do alerta é definida como ' disparado *'* e, quando a condição subjacente que fez com que o alerta fosse desativada, a condição do monitor é definida como *' resolvida '*. 

O estado de alerta não é alterado até que o usuário o altere. Saiba [como alterar o estado dos seus alertas e grupos inteligentes](./alerts-managing-alert-states.md?toc=%2fazure%2fazure-monitor%2ftoc.json).

## <a name="alerts-experience"></a>Experiência de alertas 
A página alertas padrão fornece um resumo dos alertas que são criados dentro de um intervalo de tempo específico. Ele exibe o total de alertas para cada severidade, com colunas que identificam o número total de alertas em cada Estado para cada severidade. Selecione qualquer uma das gravidades para abrir a página [Todos os Alertas](#all-alerts-page) filtrada por tal gravidade.

Em vez disso, você pode [enumerar programaticamente as instâncias de alerta geradas em suas assinaturas usando APIs REST](#manage-your-alert-instances-programmatically).

> [!NOTE]
   >  Você só pode acessar alertas gerados nos últimos 30 dias.

Você pode alterar as assinaturas ou parâmetros para atualizar a página.

![Captura de tela da página de alertas](media/alerts-overview/alerts-page.png)

Você pode filtrar essa exibição selecionando valores nos menus suspensos na parte superior da página.

| Coluna | Descrição |
|:---|:---|
| Subscription | Selecione as assinaturas do Azure para as quais você deseja exibir os alertas. Opcionalmente, você pode optar por selecionar todas as suas assinaturas. Somente os alertas aos quais você tem acesso nas assinaturas selecionadas são incluídos na exibição. |
| Resource group | Selecione um único grupo de recursos. Somente alertas com destinos no grupo de recursos selecionado são incluídos na exibição. |
| Intervalo de horas | Somente os alertas acionados no intervalo de tempo selecionado são incluídos na exibição. Os valores com suporte são a última hora, as últimas 24 horas, os últimos 7 dias e os últimos 30 dias. |

Selecione os seguintes valores na parte superior da página alertas para abrir outra página:

| Valor | Descrição |
|:---|:---|
| Total de alertas | O número total de alertas que correspondem aos critérios selecionados. Selecione esse valor para abrir a exibição Todos os Alertas sem filtro. |
| Grupos inteligentes | O número total de grupos inteligentes que foram criados a partir dos alertas que correspondem aos critérios selecionados. Selecione esse valor para abrir a lista de grupos inteligentes na exibição Todos os Alertas.
| Total de regras de alerta | O número total de regras de alerta na assinatura e no grupo de recursos selecionados. Selecione esse valor para abrir a exibição Regras filtrada na assinatura e no grupo de recursos selecionados.


## <a name="manage-alert-rules"></a>Gerenciar regras de alerta
Para mostrar a página **regras** , selecione **gerenciar regras de alerta**. A página de regras é um único local para gerenciar todas as regras de alerta em suas assinaturas do Azure. Ela lista todas as regras de alerta e estas podem ser classificadas com base em grupos de recursos, recursos de destino, nome da regra ou status. Você também pode editar, habilitar ou desabilitar regras de alerta nesta página.  

 ![Captura de tela da página regras](./media/alerts-overview/alerts-preview-rules.png)


## <a name="create-an-alert-rule"></a>Criar uma regra de alerta
Você pode criar regras de alerta de maneira consistente, seja qual for o serviço de monitoramento ou tipo de sinal.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4tflw]

 
Veja como criar uma nova regra de alerta:
1. Escolha o _destino_ para o alerta.
1. Selecione o _sinal_ entre os sinais disponíveis para o destino.
1. Especifique a _lógica_ a ser aplicada aos dados do sinal.

Esse processo de criação simplificado não exige mais que você conheça a origem de monitoramento ou sinais que tenham suporte antes de selecionar um recurso do Azure. A lista de sinais disponíveis é filtrada automaticamente com base no recurso de destino selecionado. Também com base nesse destino, você é guiado pela definição da lógica da regra de alerta automaticamente.  

Saiba mais sobre como criar regras de alerta em [Criar, exibir e gerenciar alertas usando o Azure Monitor](../alerts/alerts-metric.md).

Os alertas estão disponíveis em vários serviços de monitoramento do Azure. Para obter informações sobre como e quando usar cada um desses serviços, consulte [Monitorar aplicativos e recursos do Azure](../overview.md). 


## <a name="all-alerts-page"></a>Página Todos os Alertas 
Para ver a página **todos os alertas** , selecione **total de alertas**. Aqui você pode exibir uma lista de alertas criados dentro da hora selecionada. É possível exibir uma lista dos alertas individuais ou uma lista dos grupos inteligentes que contêm os alertas. Selecione a faixa na parte superior da página para alternar entre as exibições.

![Captura de tela de todas as páginas de alertas](media/alerts-overview/all-alerts-page.png)

Você pode filtrar a exibição selecionando os seguintes valores nos menus suspensos na parte superior da página:

| Coluna | Descrição |
|:---|:---|
| Subscription | Selecione as assinaturas do Azure para as quais você deseja exibir os alertas. Opcionalmente, você pode optar por selecionar todas as suas assinaturas. Somente os alertas aos quais você tem acesso nas assinaturas selecionadas são incluídos na exibição. |
| Resource group | Selecione um único grupo de recursos. Somente alertas com destinos no grupo de recursos selecionado são incluídos na exibição. |
| Tipo de recurso | Selecione um ou mais tipos de recurso. Somente alertas com destinos do tipo selecionado são incluídos na exibição. Essa coluna somente estará disponível depois que um grupo de recursos for especificado. |
| Recurso | Selecione um recurso. Apenas alertas com esse recurso como um destino são incluídos na exibição. Essa coluna somente estará disponível depois que um tipo de recurso for especificado. |
| Severidade | Selecione uma severidade de alerta ou selecione **tudo** para incluir alertas de todas as severidades. |
| Monitorar condição | Selecione uma condição de monitor ou selecione **tudo** para incluir alertas de todas as condições. |
| Estado de alerta | Selecione um estado de alerta ou selecione **todos** para incluir alertas de todos os Estados. |
| Monitorar serviço | Selecione um serviço ou selecione **todos** para incluir todos os serviços. Apenas alertas criados por regras que usam o serviço como um destino são incluídos. |
| Intervalo de horas | Somente os alertas acionados no intervalo de tempo selecionado são incluídos na exibição. Os valores com suporte são a última hora, as últimas 24 horas, os últimos 7 dias e os últimos 30 dias. |

Selecione **colunas** na parte superior da página para selecionar quais colunas mostrar. 

## <a name="alert-details-page"></a>Página detalhes do alerta
Quando você seleciona um alerta, essa página fornece detalhes do alerta e permite que você altere seu estado.

![Captura de tela da página de detalhes do alerta](media/alerts-overview/alert-detail2.png)

A página detalhes do alerta inclui as seguintes seções:

| Seção | DESCRIÇÃO |
|:---|:---|
| Resumo | Exibe as propriedades e outras informações significativas sobre o alerta. |
| Histórico | Lista cada ação realizada pelo alerta e todas as alterações feitas no alerta. Atualmente limitado a alterações de estado. |
| Diagnósticos | Informações sobre o grupo inteligente no qual o alerta está incluído. A *contagem de alerta* refere-se ao número de alertas incluídos no grupo inteligente. Inclui outros alertas no mesmo grupo inteligente que foram criados nos últimos 30 dias, seja qual for o filtro de tempo na página de lista de alertas. Selecione um alerta para exibir os detalhes. |

## <a name="azure-role-based-access-control-azure-rbac-for-your-alert-instances"></a>Controle de acesso baseado em função do Azure (RBAC do Azure) para suas instâncias de alerta

O consumo e o gerenciamento das instâncias de alerta exigem que o usuário tenha as funções internas do Azure de [colaborador de monitoramento](../../role-based-access-control/built-in-roles.md#monitoring-contributor) ou [leitor de monitoramento](../../role-based-access-control/built-in-roles.md#monitoring-reader). Essas funções têm suporte em qualquer escopo do Azure Resource Manager, do nível de assinatura até atribuições granulares no nível de recurso. Por exemplo, se um usuário só tiver acesso de colaborador de monitoramento para a máquina virtual `ContosoVM1` , esse usuário poderá consumir e gerenciar somente alertas gerados no `ContosoVM1` .

## <a name="manage-your-alert-instances-programmatically"></a>Gerencie suas instâncias de alerta programaticamente

Talvez você queira consultar programaticamente os alertas gerados em sua assinatura. As consultas podem ser para criar exibições personalizadas fora do portal do Azure ou para analisar seus alertas para identificar padrões e tendências.

Você pode consultar alertas gerados em suas assinaturas usando a [API rest gerenciamento de alertas](/rest/api/monitor/alertsmanagement/alerts) ou usando o [grafo de recursos do Azure](../../governance/resource-graph/overview.md) e a [API REST para recursos](/rest/api/azureresourcegraph/resourcegraph(2019-04-01)/resources/resources).

A API REST do grafo de recursos para recursos permite consultar instâncias de alerta em escala. O grafo de recursos é recomendado quando você precisa gerenciar alertas gerados em várias assinaturas. 

A seguinte solicitação de exemplo para a API REST do grafo de recursos retorna a contagem de alertas em uma assinatura:

```json
{
  "subscriptions": [
    <subscriptionId>
  ],
  "query": "AlertsManagementResources | where type =~ 'Microsoft.AlertsManagement/alerts' | summarize count()"
}
```

Você também pode ver o resultado dessa consulta de grafo de recursos no portal com o Gerenciador de gráficos de recursos do Azure: [Portal.Azure.com](https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/AlertsManagementResources%20%7C%20where%20type%20%3D~%20%27Microsoft.AlertsManagement%2Falerts%27%20%7C%20summarize%20count())

Você pode consultar os alertas para seus campos [essenciais](../alerts/alerts-common-schema-definitions.md#essentials) .

Use a [API REST do gerenciamento de alertas](/rest/api/monitor/alertsmanagement/alerts) para obter mais informações sobre alertas específicos, incluindo seus campos de [contexto de alerta](../alerts/alerts-common-schema-definitions.md#alert-context) .

## <a name="smart-groups"></a>Grupos inteligentes

Os grupos inteligentes são agregações de alertas com base em algoritmos de aprendizado de máquina, o que pode ajudar a reduzir o ruído de alerta e auxiliar na solução de problemas. [Saiba mais sobre Grupos inteligentes](./alerts-smartgroups-overview.md?toc=%2fazure%2fazure-monitor%2ftoc.json) e [como gerenciar seus grupos inteligentes](./alerts-managing-smart-groups.md?toc=%2fazure%2fazure-monitor%2ftoc.json).

## <a name="next-steps"></a>Próximas etapas

- [Saiba mais sobre os grupos inteligentes](./alerts-smartgroups-overview.md?toc=%2fazure%2fazure-monitor%2ftoc.json)
- [Saiba mais sobre grupos de ação](../alerts/action-groups.md)
- [Gerenciar suas instâncias de alertas no Azure](./alerts-managing-alert-instances.md?toc=%2fazure%2fazure-monitor%2ftoc.json)
- [Gerenciando grupos inteligentes](./alerts-managing-smart-groups.md?toc=%2fazure%2fazure-monitor%2ftoc.json)
- [Saiba mais sobre os preços dos alertas do Azure](https://azure.microsoft.com/pricing/details/monitor/)