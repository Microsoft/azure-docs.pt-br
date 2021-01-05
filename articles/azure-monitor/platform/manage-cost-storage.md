---
title: Gerenciar o uso e os custos dos logs do Azure Monitor
description: Saiba como alterar o plano de preços e gerenciar o volume de dados e a política de retenção para o workspace do Log Analytics no Azure Monitor.
services: azure-monitor
documentationcenter: azure-monitor
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/24/2020
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: 45f02850797582f97220e91d1582b04b3be711c0
ms.sourcegitcommit: 6d6030de2d776f3d5fb89f68aaead148c05837e2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/05/2021
ms.locfileid: "97882476"
---
# <a name="manage-usage-and-costs-with-azure-monitor-logs"></a>Gerenciar o uso e os custos com logs do Azure Monitor    

> [!NOTE]
> Este artigo descreve como entender e controlar seus custos com logs de Azure Monitor. Um artigo relacionado chamado [Monitorar o uso e os custos estimados](usage-estimated-costs.md) descreve como visualizar o uso e os custos estimados em vários recursos de monitoramento do Azure para diferentes modelos de preços. Todos os preços e custos mostrados neste artigo se destinam apenas para fins de exemplo. 

Os logs do Azure Monitor foram projetados para dimensionar e fornecer suporte à coleta, à indexação e ao armazenamento de grandes quantidades de dados por dia de qualquer fonte em sua empresa ou daqueles implantados no Azure.  Embora isso possa ser um driver primário para a organização, a eficiência de custo é, em última instância, o driver subjacente. Para esse fim, é importante entender que o custo de um workspace do Log Analytics não se baseia somente no volume de dados coletados, mas também depende do plano selecionado e de quanto tempo você escolheu armazenar os dados gerados por suas fontes conectadas.  

Neste artigo, analisamos como você pode monitorar proativamente o volume de dados ingeridos e o aumento do armazenamento, além de definir limites para controlar esses custos associados. 

## <a name="pricing-model"></a>Modelo de preços

O preço padrão do Log Analytics é um modelo de **Pagamento Conforme o Uso** baseado no volume de dados ingerido e, opcionalmente, para uma retenção de dados mais longa. O volume de dados é medido como o tamanho dos dados que serão armazenados em GB (10 ^ 9 bytes). Cada workspace do Log Analytics é cobrado como um serviço separado e contribui para a cobrança da sua assinatura do Azure. A quantidade de ingestão de dados pode ser considerável dependendo dos fatores a seguir: 

  - Número de soluções de gerenciamento habilitadas e sua configuração
  - Número de VMs monitoradas
  - Tipo de dados coletados de cada VM monitorada 
  
Além do modelo de Pagamento Conforme o Uso, o Log Analytics tem tipos de preço de **Reserva de Capacidade**, o que permite que você economize até 25% em comparação com o preço de Pagamento Conforme o Uso. O preço de reserva de capacidade habilita você a comprar uma reserva a partir de 100 GB/dia. Qualquer uso acima do nível de reserva será cobrado com a taxa de Pagamento Conforme o Uso. Os tipos de preço de Reserva de Capacidade têm um período de compromisso de 31 dias. Durante o período de compromisso, é possível alterar para um tipo de preço de Reserva de Capacidade de um nível maior (o qual reiniciará o período de compromisso de 31 dias), mas não poderá voltar para o modelo de Pagamento Conforme o Uso ou para um tipo de preço de Reserva de Capacidade menor até que o período de compromisso seja concluído. A cobrança dos tipos de preço de Reserva de Capacidade é feita diariamente. [Saiba mais](https://azure.microsoft.com/pricing/details/monitor/) sobre os modelos de preços de Pagamento Conforme o Uso e de Reserva de Capacidade do Log Analytics. 

Em todos os tipos de preço, o tamanho dos dados de um evento é calculado a partir de uma representação de cadeia de caracteres das propriedades que são armazenadas em Log Analytics para esse evento, quer os dados sejam enviados de um agente ou adicionados durante o processo de ingestão. Isso inclui todos os [campos personalizados](custom-fields.md) que são adicionados à medida que os dados são coletados e armazenados em log Analytics. Várias propriedades comuns a todos os tipos de dados, incluindo algumas [log Analytics propriedades padrão](./log-standard-columns.md), são excluídas no cálculo do tamanho do evento. Isso inclui `_ResourceId` , `_ItemId` , `_IsBillable` `_BilledSize` e `Type` . Todas as outras propriedades armazenadas em Log Analytics são incluídas no cálculo do tamanho do evento. Alguns tipos de dados são gratuitos dos encargos de ingestão de dados, por exemplo, os tipos de AzureActivity, pulsação e uso. Para determinar se um evento foi excluído da cobrança da ingestão de dados, você pode usar a `_IsBillable` propriedade, conforme mostrado [abaixo](#data-volume-for-specific-events). O uso é relatado em GB (1,0 E9 bytes). 

Além disso, observe que algumas soluções, como a [Central de Segurança do Azure](https://azure.microsoft.com/pricing/details/security-center/), o [Azure Sentinel](https://azure.microsoft.com/pricing/details/azure-sentinel/) e o [Gerenciamento de configurações](https://azure.microsoft.com/pricing/details/automation/) têm seus próprios modelos de preços. 

### <a name="log-analytics-dedicated-clusters"></a>Clusters dedicados do Log Analytics

Os clusters dedicados do Log Analytics são coleções de workspace em um único cluster gerenciado do Azure Data Explorer para dar suporte a cenários avançados, como [Chaves Gerenciadas pelo Cliente](customer-managed-keys.md).  Log Analytics clusters dedicados usam um modelo de preços de reserva de capacidade que deve ser configurado para pelo menos 1000 GB/dia. Esse nível de capacidade tem um desconto de 25% em comparação com o preço pago conforme o uso. Qualquer uso acima do nível de reserva será cobrado com a taxa de Pagamento Conforme o Uso. A Reserva de Capacidade do cluster tem um período de compromisso de 31 dias após o aumento do nível de reserva. Durante o período de compromisso, o nível de reserva de capacidade não pode ser reduzido, mas ele pode ser aumentado a qualquer momento. Quando os espaços de trabalho estão associados a um cluster, a cobrança de ingestão de dados para esses espaços de trabalho é feita no nível do cluster usando o nível de reserva de capacidade configurado. Saiba mais sobre [criação de clusters do Log Analytics](customer-managed-keys.md#create-cluster) e [associação de workspaces a ele](customer-managed-keys.md#link-workspace-to-cluster). As informações de preços de reserva de capacidade estão disponíveis na [página de preços Azure monitor]( https://azure.microsoft.com/pricing/details/monitor/).  

O nível de reserva de capacidade do cluster é configurado por meio de programação com Azure Resource Manager usando o `Capacity` parâmetro em `Sku` . A `Capacity` é especificada em unidades de GB e pode ter valores de 1.000 GB/dia ou mais em incrementos de 100 GB/dia. Isso é detalhado em [Azure monitor chave gerenciada pelo cliente](customer-managed-keys.md#create-cluster). Caso o cluster precise de uma reserva acima de 2.000 GB/dia, entre em contato conosco pelo email [LAIngestionRate@microsoft.com](mailto:LAIngestionRate@microsoft.com).

Há dois modos de cobrança para uso em um cluster. Eles podem ser especificados pelo `billingType` parâmetro ao [Configurar o cluster](customer-managed-keys.md#customer-managed-key-operations). Os dois modos são: 

1. **Cluster**: nesse caso (que é o padrão), a cobrança de dados ingeridos é feita no nível do cluster. As quantidades de dados ingeridos de cada workspace associado a um cluster são agregadas para calcular a fatura diária do cluster. Observe que as alocações por nó da [Central de Segurança do Azure](../../security-center/index.yml) são aplicadas no nível do workspace antes dessa agregação de dados em todos os workspaces presentes no cluster. 

2. **Espaços de trabalho**: os custos de reserva de capacidade para o cluster são atribuídos proporcionalmente aos espaços de trabalho no cluster (após a contabilização de alocações por nó da [central de segurança do Azure](../../security-center/index.yml) para cada espaço de trabalho). Se o volume de dados total ingerido em um espaço de trabalho para um dia for menor que a reserva de capacidade, cada espaço de trabalho será cobrado por seus dados ingeridos na taxa de reserva de capacidade por GB efetiva, cobrando-os por uma fração da reserva de capacidade e a parte não utilizada da reserva de capacidade será cobrada no recurso de cluster. Se o volume de dados total ingerido em um espaço de trabalho para um dia for maior do que a reserva de capacidade, cada espaço de trabalho será cobrado por uma fração da reserva de capacidade com base na fração dos dados ingeridos desse dia e em cada espaço de trabalho para uma fração dos dados ingeridos acima da reserva de capacidade. Não há nada cobrado no recurso de cluster se o volume de dados total ingerido em um espaço de trabalho por um dia está acima da reserva de capacidade.

Em opções de cobrança de cluster, a retenção de dados é cobrada por espaço de trabalho. Observe que a cobrança do cluster começa quando o cluster é criado, independentemente de os workspaces estarem associados a ele. Além disso, observe que os espaços de trabalho associados a um cluster não têm mais um tipo de preço.

## <a name="estimating-the-costs-to-manage-your-environment"></a>Estimativa dos custos para gerenciar seu ambiente 

Caso ainda não esteja usando logs do Azure Monitor, você poderá usar a [calculadora de preços do Azure Monitor](https://azure.microsoft.com/pricing/calculator/?service=monitor) para estimar o custo do uso do Log Analytics. Para começar, insira "Azure Monitor" na caixa de Pesquisa e clique no bloco do Azure Monitor resultante. Role a página para baixo até o Azure Monitor e selecione o Log Analytics na lista suspensa de Tipo.  Aqui é possível inserir o número de VMs e de GB de dados que você espera coletar de cada VM. Normalmente, são ingeridos de 1 a 3 GB de dados por mês de uma VM típica do Azure. Caso já esteja avaliando os logs do Azure Monitor, você poderá usar suas estatísticas de dados do seu próprio ambiente. Veja abaixo como determinar o [número de VMs monitoradas](#understanding-nodes-sending-data) e o [volume de dados que seu workspace está ingerindo](#understanding-ingested-data-volume). 

## <a name="understand-your-usage-and-estimate-costs"></a>Entender seu uso e estimar os custos

Caso esteja usando os logs do Azure Monitor atualmente, é fácil entender quais serão os prováveis custos com base nos padrões de uso recentes. Para isso, use o **Uso e custos estimados do Log Analytics** para revisar e analisar o uso de dados. Isso mostra quantos dados são coletados por cada solução, quantos dados estão sendo retidos e uma estimativa de seus custos com base na quantidade de dados ingeridos e em qualquer retenção adicional além do valor incluído.

![Uso e custos estimados](media/manage-cost-storage/usage-estimated-cost-dashboard-01.png)

Para explorar seus dados com mais detalhes, clique no ícone no canto superior direito de qualquer um dos gráficos na página **Uso e custos estimados**. Agora você pode trabalhar com essa consulta para explorar mais detalhes do seu uso.  

![Exibir logs](media/manage-cost-storage/logs.png)

Da página **uso e custos estimados**, pode-se examinar o seu volume de dados para o mês. Isso inclui todos os dados faturáveis recebidos e retidos em seu espaço de trabalho Log Analytics.  
 
Os encargos do Log Analytics são adicionadas à sua fatura do Azure. É possível ver os detalhes da fatura do Azure na seção Cobrança do Portal do Azure ou no [Portal de Cobrança do Azure](https://account.windowsazure.com/Subscriptions).  

## <a name="viewing-log-analytics-usage-on-your-azure-bill"></a>Exibição do uso de Log Analytics em sua fatura do Azure 

O Azure fornece um grande controle da funcionalidade útil no hub [Gerenciamento de Custos do Azure + Cobrança](../../cost-management-billing/costs/quick-acm-cost-analysis.md?toc=%2fazure%2fbilling%2fTOC.json). Por exemplo, a funcionalidade de "Análise de custo" permite que você exiba seus gastos dos recursos do Azure. Primeiro, adicione um filtro por "tipo de recurso" (para Microsoft. operationalinsights/espaço de trabalho para Log Analytics e Microsoft. operationalinsights/cluster para clusters Log Analytics) permitirá que você acompanhe seus Log Analytics gastos. Depois, em "Agrupar por", selecione "Categoria do medidor" ou "Medidor".  Observe que outros serviços, como a Central de Segurança do Azure e o Azure Sentinel, também cobram seu uso em relação aos recursos do workspace do Log Analytics. Para ver o mapeamento para o Nome do serviço, você pode selecionar a Exibição de tabela em vez de um gráfico. 

É possível obter uma compreensão maior do seu uso [baixando seu uso do portal do Azure](../../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md#download-usage-in-azure-portal). Na planilha baixada, você pode ver o uso por recurso do Azure (por exemplo, workspace do Log Analytics) por dia. Nessa planilha do Excel, o uso de seus workspaces do Log Analytics pode ser encontrado ao, primeiro, filtrar a coluna "Categoria do Medidor" para mostrar o "Log Analytics", "Insight e Análise" (usado por alguns dos tipos de preço herdados) e "Azure Monitor" (usado por tipos de preço de Reserva de Capacidade) e, em seguida, adicionar um filtro na coluna "ID da Instância", que será "contém workspace" ou "contém cluster" (este último inclui o uso de cluster do Log Analytics). O uso é mostrado na coluna "Quantidade Consumida", e a unidade de cada entrada é mostrada na coluna "Unidade de Medida".  Há mais detalhes disponíveis para ajudar você a [entender sua fatura do Microsoft Azure](../../cost-management-billing/understand/review-individual-bill.md). 

## <a name="changing-pricing-tier"></a>Alterando o tipo de preço

Para alterar o tipo de preço do Log Analytics do seu workspace: 

1. No portal do Azure, abra **Uso e custos estimados** de seu workspace, onde você verá uma lista de cada tipo de preço disponível para esse workspace.

2. Revise os custos estimados de cada tipo de preço. Essa estimativa de custo se baseia nos últimos 31 dias de uso, portanto, ela depende dos últimos 31 dias que representam o uso típico. No exemplo abaixo, você pode ver como, com base nos padrões de dados dos últimos 31 dias, esse workspace custaria menos no tipo de preço Pagamento Conforme o Uso (#1) em comparação ao tipo de preço de Reserva de Capacidade de 100 GB/dia (#2).  

    ![Tipos de preço](media/manage-cost-storage/pricing-tier-estimated-costs.png)

3. Depois de revisar os custos estimados com base nos últimos 31 dias de uso, caso você decida alterar o tipo de preço, clique em **Selecionar**.  

Você também pode [definir o tipo de preço por meio do Azure Resource Manager](../samples/resource-manager-workspace.md) usando o parâmetro `sku` (`pricingTier` no modelo do Azure Resource Manager). 

## <a name="legacy-pricing-tiers"></a>Tipos de preço legados

As assinaturas que tinham um workspace do Log Analytics ou um recurso do Application Insights presentes antes de 2 de abril de 2018, ou que estejam vinculadas a um Contrato Enterprise iniciado antes de 1º de fevereiro de 2019, continuarão a ter acesso ao uso dos tipos de preço herdados: **Gratuito**, **Autônomo (por GB)** e **Por Nó (OMS)** .  Os workspaces no tipo de preço Gratuito terão uma ingestão de dados diária limitada a 500 MB (exceto por tipos de dados de segurança coletados pela [Central de Segurança do Azure](../../security-center/index.yml)) e a retenção de dados será limitada a 7 dias. O tipo de preço Gratuito é destinado apenas para fins de avaliação. Os workspaces nos tipos de preço Autônomo ou Por Nó têm retenção configurável do usuário de 30 a 730 dias.

O uso no tipo de preço autônomo é cobrado pelo volume de dados ingeridos. Ele é relatado no serviço de **log Analytics** e o medidor é denominado "dados analisados". 

O tipo de preço Por Nó faz a cobrança por VM monitorada (nó) em uma granularidade de hora. Para cada nó monitorado, são alocados 500 MB de dados por dia ao workspace, os quais não são cobrados. Essa alocação é calculada com granularidade por hora e é agregada no nível do espaço de trabalho a cada dia. Os dados ingeridos em quantidade acima da alocação diária de dados agregados são cobrados por GB como excedentes de dados. Observe que, em sua fatura, o serviço será **Insight e Análise** para o uso do Log Analytics caso o workspace esteja no tipo de preço Por Nó. O uso é relatado em três medidores:

1. Node: é o uso do número de nós monitorados (VMs) em unidades de nó * meses.
2. Dados excedentes por nó: Este é o número de GB de dados ingeridos excedendo a alocação de dados agregada.
3. Dados incluídos por nó: esta é a quantidade de dados ingeridos que foram cobertos pela alocação de dados agregados. Esse medidor também é usado quando o espaço de trabalho está em todos os tipos de preço para mostrar a quantidade de dados cobertos pela central de segurança do Azure.

> [!TIP]
> Caso seu workspace tenha acesso ao tipo de preço **Por Nó**, mas você esteja se perguntando se o custo seria menor em um tipo de preço Pagamento Conforme o Uso, você pode [usar a consulta abaixo](#evaluating-the-legacy-per-node-pricing-tier) para obter uma recomendação facilmente. 

Os workspaces criados antes de abril de 2016 também podem acessar os tipos de preço originais **Standard** e  **Premium** que têm retenção de dados fixa de 30 a 365 dias respectivamente. Novos workspaces não podem ser criados nos tipos de preço **Standard** ou **Premium** e, se um workspace for movido para fora desses tipos, ele não poderá ser movido de volta. Os medidores de ingestão de dados para essas camadas herdadas são chamados de "dados analisados".

Também há alguns comportamentos entre o uso de tipos de preço herdados do Log Analytics e como o uso é cobrado para a [Central de Segurança do Azure](../../security-center/index.yml). 

1. Caso o workspace esteja no tipo de preço herdado Standard ou Premium, a Central de Segurança do Azure será cobrada somente pela ingestão de dados do Log Analytics, e não por nó.
2. Caso o workspace esteja no tipo de preço herdado Por Nó, a Central de Segurança do Azure será cobrada usando o [modelo atual de preço baseado em nó da Central de Segurança do Azure](https://azure.microsoft.com/pricing/details/security-center/). 
3. Em outros tipos de preço (incluindo Reservas de Capacidade), se a Central de Segurança do Azure tiver sido habilitada antes de 19 de junho de 2017, ela será cobrada somente pela ingestão de dados do Log Analytics. Caso contrário, a Central de Segurança do Azure será cobrada usando o modelo atual de preço baseado em nó da Central de Segurança do Azure.

Mais detalhes das limitações do tipo de preço estão disponíveis em [assinatura do Azure e limites de serviço, cotas e restrições](../../azure-resource-manager/management/azure-subscription-service-limits.md#log-analytics-workspaces).

Nenhum dos tipos de preço herdados tem preços baseados na região.  

> [!NOTE]
> Para usar os direitos provenientes da aquisição de OMS E1 Suite, OMS E2 Suite OMS ou Complemento do OMS para System Center, escolha o tipo de preço *Por Nó* do Log Analytics.

## <a name="log-analytics-and-security-center"></a>Central de Log Analytics e segurança

A cobrança da [central de segurança do Azure](../../security-center/index.yml) está diretamente ligada à cobrança log Analytics. A central de segurança fornece a alocação de 500 MB/nó/dia em relação a um conjunto de [tipos de dados de segurança](/azure/azure-monitor/reference/tables/tables-category#security) (WindowsEvent, SecurityAlert, SecurityBaseline, SecurityBaselineSummary, SecurityDetection, SecurityEvent, WindowsFirewall, MaliciousIPCommunication, LinuxAuditLog, SysmonEvent, ProtectionStatus) e os tipos de dados Update e UpdateSummary quando a solução gerenciamento de atualizações não está em execução no espaço de trabalho ou o direcionamento de solução está habilitado. Se o espaço de trabalho estiver no tipo de preço herdado por nó, a central de segurança e as alocações de Log Analytics serão combinadas e aplicadas em conjunto a todos os dados ingeridos faturáveis.  

## <a name="change-the-data-retention-period"></a>Alterar o período de retenção de dados

As etapas a seguir descrevem como configurar por quanto tempo os dados de log são mantidos no workspace. A retenção de dados no nível do espaço de trabalho pode ser configurada de 30 a 730 dias (2 anos) para todos os espaços de trabalho, a menos que eles estejam usando o tipo de preço gratuito herdado. [Saiba mais](https://azure.microsoft.com/pricing/details/monitor/) sobre os preços para a retenção de dados mais longa. A retenção de tipos de dados individuais pode ser definida como baixa de 4 dias. 

### <a name="workspace-level-default-retention"></a>Retenção padrão no nível do espaço de trabalho

Para definir a retenção padrão do seu workspace: 
 
1. No portal do Azure, a partir do seu workspace, selecione **Uso e custos estimados** no painel esquerdo.
2. Na página **Uso e estimativa de custos**, clique em **Retenção de dados** na parte superior da página.
3. No painel, mova o controle deslizante para aumentar ou diminuir o número de dias e, em seguida, clique em **Salvar**.  Se você usar a camada *Gratuita*, não será possível modificar o período de retenção de dados, sendo necessário atualizar para a camada paga para controlar essa configuração.

    ![Alterar a configuração de retenção de dados do workspace](media/manage-cost-storage/manage-cost-change-retention-01.png)

Quando a retenção é reduzida, há um período de carência de vários dias antes que os dados anteriores à nova configuração de retenção sejam removidos. 

A retenção também pode ser [definida por meio do Azure Resource Manager](../samples/resource-manager-workspace.md) usando o parâmetro `retentionInDays`. Ao definir a retenção de dados para 30 dias, você pode disparar uma limpeza imediata de dados mais antigos usando o `immediatePurgeDataOn30Days` parâmetro (eliminando o período de carência de vários dias). Isso pode ser útil para cenários relacionados à conformidade em que a remoção imediata de dados é imperativa. Essa funcionalidade de limpeza imediata só é exposta por meio de Azure Resource Manager. 

Os espaços de trabalho com retenção de 30 dias podem realmente reter dados por 31 dias. Se for imperativo que os dados sejam mantidos por apenas 30 dias, use a Azure Resource Manager para definir a retenção para 30 dias e com o `immediatePurgeDataOn30Days` parâmetro.  

Dois tipos de dados — `Usage` e `AzureActivity` — são retidos por um mínimo de 90 dias por padrão, e não há nenhum custo para esse período de retenção. Caso a retenção do workspace seja aumentada para mais de 90 dias, a retenção desses tipos de dados também será aumentada.  Também não são cobrados encargos de ingestão de dados para esses tipos de dados. 

Os tipos de dados dos recursos do Application Insights baseados em workspace (`AppAvailabilityResults`, `AppBrowserTimings`, `AppDependencies`, `AppExceptions`, `AppEvents`, `AppMetrics`, `AppPageViews`, `AppPerformanceCounters`, `AppRequests`, `AppSystemEvents` e `AppTraces`) também são retidos por 90 dias por padrão, e não há nenhum custo para esse período de retenção. Essa retenção pode ser ajustada usando a funcionalidade de retenção por tipo de dados. 

A [API de limpeza](/rest/api/loganalytics/workspacepurge/purge) do Log Analytics não afeta a cobrança de retenção e se destina a ser usada em casos muito limitados. Para reduzir sua fatura de retenção, o período de retenção deve ser reduzido para o espaço de trabalho ou para tipos de dados específicos. 

### <a name="retention-by-data-type"></a>Retenção por tipo de dados

Também é possível especificar diferentes configurações de retenção para tipos de dados individuais de 4 a 730 dias (exceto para espaços de trabalho no tipo de preço livre herdado) que substituem a retenção padrão em nível de espaço de trabalho. Cada tipo de dados é um sub-recurso do workspace. Por exemplo, a tabela SecurityEvent pode ser abordada no [Azure Resource Manager](../../azure-resource-manager/management/overview.md) como:

```
/subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent
```

Esteja ciente de que o tipo de dados (tabela) diferencia maiúsculas de minúsculas.  Para obter as configurações de retenção de tipo por dados atuais de um tipo de dados específico (neste exemplo SecurityEvent), use:

```JSON
    GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview
```

> [!NOTE]
> A retenção só será retornada para um tipo de dados se a retenção tiver sido definida explicitamente para ele.  Os tipos de dados que não tiveram a retenção definida explicitamente (e, portanto, herdam a retenção do espaço de trabalho) não retornarão nada dessa chamada. 

Para obter as configurações atuais de retenção de tipo de dados para todos os tipos de dados no espaço de trabalho que tiveram seu conjunto de retenção de tipo por dados definido, basta omitir o tipo de dados específico, por exemplo:

```JSON
    GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables?api-version=2017-04-26-preview
```

Para definir a retenção de um tipo de dados específico (nesse exemplo, SecurityEvent) em 730 dias, faça o seguinte:

```JSON
    PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview
    {
        "properties": 
        {
            "retentionInDays": 730
        }
    }
```

Os valores válidos para `retentionInDays` são a partir de 30 até 730.

Os tipos de dados `Usage` e `AzureActivity` não podem ser definidos com uma retenção personalizada. Eles serão definidos conforme o máximo de retenção de workspace padrão ou 90 dias. 

Uma ótima ferramenta para se conectar diretamente ao Azure Resource Manager para definir a retenção por tipo de dados é a ferramenta de OSS [ARMClient](https://github.com/projectkudu/ARMClient).  Saiba mais sobre o ARMClient nos artigos de [David Ebbo](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) e [Daniel Bowbyes](https://blog.bowbyes.co.nz/2016/11/02/using-armclient-to-directly-access-azure-arm-rest-apis-and-list-arm-policy-details/).  Aqui está um exemplo usando o ARMClient, configurando os dados de SecurityEvent para uma retenção de 730 dias:

```
armclient PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview "{properties: {retentionInDays: 730}}"
```

> [!TIP]
> A configuração da retenção em tipos de dados individuais pode ser usada para reduzir seus custos de retenção de dados.  Em relação aos dados coletados a partir de outubro de 2019 (quando esse recurso foi lançado), a redução da retenção de alguns tipos de dados pode reduzir o custo de retenção ao longo do tempo.  Em relação aos dados coletados anteriormente, a definição de uma retenção inferior de um tipo individual não afetará os custos de retenção.  

## <a name="manage-your-maximum-daily-data-volume"></a>Gerenciar o volume máximo de dados por dia

É possível configurar um limite diário e limitar a ingestão diária para o workspace, mas seja cuidadoso, pois sua meta não deve ser atingir o limite diário.  Caso contrário, você perderá os dados no restante do dia, o que pode afetar outros serviços e soluções do Azure cuja funcionalidade pode depender de dados atualizados no workspace.  Como resultado, sua capacidade de observar e receber alertas quando as condições de integridade dos recursos que dão suporte a serviços de TI forem afetadas.  O limite diário destina-se a ser usado como uma maneira de gerenciar um **aumento inesperado** no volume de dados de seus recursos gerenciados e permanecer dentro do limite ou quando você quiser limitar encargos não planejados para seu espaço de trabalho. Não é apropriado definir um limite diário para que ele seja atendido todos os dias em um workspace.

Cada espaço de trabalho tem seu limite diário aplicado em uma hora diferente do dia. A hora de redefinição é mostrada na página de **limite diário** (veja abaixo). Esta hora de redefinição não pode ser configurada. 

Tão logo o limite diário seja alcançado, a coleta de tipos de dados faturáveis é interrompida pelo restante do dia. A latência inerente à aplicação do limite diário significa que o limite não é aplicado exatamente ao nível de limite diário especificado. Uma faixa de aviso aparece na parte superior da página do espaço de trabalho do Log Analytics selecionado e um evento de operação é enviado para a tabela *Operação* na categoria **LogManagement**. A coleta de dados é retomada após o tempo de redefinição definido em *O limite diário será definido em*. É recomendável definir uma regra de alerta com base nesse evento de operação, configurada para notificar quando o limite diário de dados foi atingido (veja [abaixo](#alert-when-daily-cap-reached)). 

> [!NOTE]
> O limite diário não pode parar a coleta de dados exatamente com o nível de Cap especificado e alguns dados em excesso são esperados, especialmente se o espaço de trabalho estiver recebendo grandes volumes de dados. Veja [abaixo](#view-the-effect-of-the-daily-cap) uma consulta que é útil para estudar o comportamento de limite diário. 

> [!WARNING]
> O limite diário não interrompe a coleta de tipos de dados incluídos na [concessão diária por nó da central de segurança do Azure](#log-analytics-and-security-center) (WindowsEvent, SecurityAlert, SecurityBaseline, SecurityBaselineSummary, SecurityDetection, SecurityEvent, WindowsFirewall, MaliciousIPCommunication, LinuxAuditLog, SysmonEvent, ProtectionStatus, Update e UpdateSummary), exceto para espaços de trabalho nos quais a central de segurança do Azure foi instalada antes de 19 de junho de 2017. 

### <a name="identify-what-daily-data-limit-to-define"></a>Identificar o limite diário de dados a definir

Analise [Uso do Log Analytics e custos estimados](usage-estimated-costs.md) para reconhecer a tendência de ingestão de dados e qual é o limite diário de volume a ser definido. Considere-o com cuidado, pois não será possível monitorar os recursos depois que o limite for alcançado. 

### <a name="set-the-daily-cap"></a>Definir o limite diário

As etapas a seguir descrevem como configurar um limite para gerenciar o volume de dados que o workspace do Log Analytics ingerirá por dia.  

1. No workspace, selecione **Uso e custos estimados** no painel esquerdo.
2. Na página **uso e custos estimados** para o espaço de trabalho selecionado, clique em **limite de dados** na parte superior da página. 
3. O limite diário fica como **OFF** por padrão – clique em **ON** para habilitá-lo e defina o limite de volume de dados em GB dia.

    ![Configuração do limite de dados do Log Analytics](media/manage-cost-storage/set-daily-volume-cap-01.png)
    
O limite diário pode ser configurado por meio do ARM definindo o `dailyQuotaGb` parâmetro sob `WorkspaceCapping` conforme descrito em [espaços de trabalho – criar ou atualizar](/rest/api/loganalytics/workspaces/createorupdate#workspacecapping). 

### <a name="view-the-effect-of-the-daily-cap"></a>Exibir o efeito do limite diário

Para exibir o efeito do limite diário, é importante considerar os tipos de dados de segurança não incluídos no limite diário e a hora de redefinição do seu espaço de trabalho. A hora de redefinição de limite diário é visível na página de **limite diário** .  A consulta a seguir pode ser usada para acompanhar os volumes de dados sujeitos ao limite diário entre as redefinições de limite diário. Neste exemplo, a hora de redefinição do espaço de trabalho é 14:00.  Você precisará atualizá-lo para seu espaço de trabalho.

```kusto
let DailyCapResetHour=14;
Usage
| where Type !in ("SecurityAlert", "SecurityBaseline", "SecurityBaselineSummary", "SecurityDetection", "SecurityEvent", "WindowsFirewall", "MaliciousIPCommunication", "LinuxAuditLog", "SysmonEvent", "ProtectionStatus", "WindowsEvent")
| extend TimeGenerated=datetime_add("hour",-1*DailyCapResetHour,TimeGenerated)
| where TimeGenerated > startofday(ago(31d))
| where IsBillable
| summarize IngestedGbBetweenDailyCapResets=sum(_BilledSize)/1000. by day=bin(TimeGenerated, 1d) | render areachart  
```

### <a name="alert-when-daily-cap-reached"></a>Alerta de quando o limite diário for atingido

Embora uma indicação visual seja apresentada no Portal do Azure quando o limite de dados é alcançado, esse comportamento não alinha-se necessariamente à maneira como você gerencia problemas operacionais que exigem atenção imediata.  Para receber uma notificação de alerta, é possível criar uma nova regra de alerta no Azure Monitor.  Para saber mais, consulte [como criar, exibir e gerenciar alertas](alerts-metric.md).

Para começar, aqui estão as configurações recomendadas para o alerta consultar a `Operation` tabela usando a `_LogOperation` função. 

- Destino: Selecionar o recurso do Log Analytics
- Critérios: 
   - Nome do sinal: Pesquisa de logs personalizada
   - Consulta de pesquisa: `_LogOperation | where Category == "Ingestion" | where Operation == "Ingestion rate" | where Level == "Warning"`
   - Baseado em: Número de resultados
   - Condição: Maior que
   - Limite: 0
   - Período: 5 (minutos)
   - Frequência: 5 (minutos)
- Nome da regra de alerta: Limite diário de dados atingido
- Gravidade: Aviso (Sev 1)

Quando o alerta é definido e o limite é alcançado, um alerta é disparado e executa a resposta definida no Grupo de Ações. Ele pode notificar a equipe por email e mensagens de texto, ou automatizar ações usando webhooks, runbooks de Automação ou [integrando com uma solução ITSM externa](itsmc-definition.md#create-itsm-work-items-from-azure-alerts). 

## <a name="troubleshooting-why-usage-is-higher-than-expected"></a>Solução de problemas por uso acima do esperado

O uso aumenta devido a uma ou mais das seguintes causas:
- A quantidade de nós enviando dados para o workspace do Log Analytics está acima da esperada
- Há mais dados do que o esperado sendo enviados para o workspace do Log Analytics (talvez devido ao início do uso de uma nova solução ou devido a uma alteração de configuração de uma solução existente)

## <a name="understanding-nodes-sending-data"></a>Noções básicas sobre nós que enviam dados

Para entender o número de nós que relatam pulsações do agente todos os dias no último mês, use:

```kusto
Heartbeat 
| where TimeGenerated > startofday(ago(31d))
| summarize nodes = dcount(Computer) by bin(TimeGenerated, 1d)    
| render timechart
```
E para obter uma contagem de nós que enviaram dados nas últimas 24 horas use a consulta: 

```kusto
find where TimeGenerated > ago(24h) project Computer
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize nodes = dcount(computerName)
```

Para obter uma lista de nós que enviam quaisquer dados (e a quantidade de dados enviados por cada um deles), a seguinte consulta pode ser usada:

```kusto
find where TimeGenerated > ago(24h) project _BilledSize, Computer
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize TotalVolumeBytes=sum(_BilledSize) by computerName
```

### <a name="nodes-billed-by-the-legacy-per-node-pricing-tier"></a>Nós cobrados pelo tipo de preço herdado por nó

O [tipo de preço herdado por nó](#legacy-pricing-tiers) cobra por nós com granularidade por hora e também não conta os nós enviando apenas um conjunto de tipos de dados de segurança. Sua contagem diária de nós estaria próxima da seguinte consulta:

```kusto
find where TimeGenerated >= startofday(ago(7d)) and TimeGenerated < startofday(now()) project Computer, _IsBillable, Type, TimeGenerated
| where Type !in ("SecurityAlert", "SecurityBaseline", "SecurityBaselineSummary", "SecurityDetection", "SecurityEvent", "WindowsFirewall", "MaliciousIPCommunication", "LinuxAuditLog", "SysmonEvent", "ProtectionStatus", "WindowsEvent")
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| where _IsBillable == true
| summarize billableNodesPerHour=dcount(computerName) by bin(TimeGenerated, 1h)
| summarize billableNodesPerDay = sum(billableNodesPerHour)/24., billableNodeMonthsPerDay = sum(billableNodesPerHour)/24./31.  by day=bin(TimeGenerated, 1d)
| sort by day asc
```

O número de unidades em sua fatura está em unidades de nó * meses, que é representado por `billableNodeMonthsPerDay` na consulta. Se o espaço de trabalho tiver a solução Gerenciamento de Atualizações instalada, adicione os tipos de dados Update e UpdateSummary à lista na cláusula WHERE da consulta acima. Por fim, há uma complexidade adicional no algoritmo de cobrança real quando o direcionamento de solução é usado e não é representado na consulta acima. 


> [!TIP]
> Use essas consultas `find` com moderação como verificações entre tipos de dados, uma que a execução delas faz um uso [intensivo de recursos](../log-query/query-optimization.md#query-performance-pane). Caso não precise de resultados **por computador**, consulte o tipo de dados Uso (veja abaixo).

## <a name="understanding-ingested-data-volume"></a>Noções básicas sobre o volume de dados ingeridos

Na página **Uso e custos estimados**, o gráfico *Ingestão de dados por solução* mostra o volume total de dados enviados e quanto está sendo enviado por cada solução. Isso permite determinar tendências, como se o uso geral de dados (ou uso por uma solução específica) está crescendo, permanecendo estável ou diminuindo. 

### <a name="data-volume-for-specific-events"></a>Volume de dados de eventos específicos

Para analisar o tamanho dos dados ingeridos de um determinado conjunto de eventos, você pode consultar a tabela específica (nesse exemplo, `Event`) e depois restrinja a consulta aos eventos de interesse (nesse exemplo, ID do evento 5145 ou 5156):

```kusto
Event
| where TimeGenerated > startofday(ago(31d)) and TimeGenerated < startofday(now()) 
| where EventID == 5145 or EventID == 5156
| where _IsBillable == true
| summarize count(), Bytes=sum(_BilledSize) by EventID, bin(TimeGenerated, 1d)
``` 

Observe que a cláusula `where _IsBillable = true` filtra os tipos de dados de determinadas soluções para o qual não há nenhuma taxa de ingestão. [Saiba mais](./log-standard-columns.md#_isbillable) sobre o `_IsBillable` .

### <a name="data-volume-by-solution"></a>Volume de dados por solução

A consulta usada para exibir o volume de dados faturáveis por solução ao longo do último mês (exceto o último dia parcial) é:

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by bin(StartTime, 1d), Solution 
| render columnchart
```

A cláusula com `TimeGenerated` é usada apenas para garantir que a experiência de consulta no portal do Azure analise além do padrão de 24 horas. Ao usar o tipo de dados Uso, `StartTime` e `EndTime` representam os buckets de tempo para os quais os resultados são apresentados. 

### <a name="data-volume-by-type"></a>Volume de dados por tipo

Você pode fazer uma análise mais detalhada para ver as tendências de dados por tipo de dados:

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by bin(StartTime, 1d), DataType 
| render columnchart
```

Ou para ver uma tabela por solução e por tipo em relação ao último mês:

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) by Solution, DataType
| sort by Solution asc, DataType asc
```

### <a name="data-volume-by-computer"></a>Volume de dados por computador

O tipo de dados `Usage` não contém informações no nível do computador. Para ver o **tamanho** de dados ingeridos por computador, use a [propriedade](./log-standard-columns.md#_billedsize) `_BilledSize`, que fornece o tamanho em bytes:

```kusto
find where TimeGenerated > ago(24h) project _BilledSize, _IsBillable, Computer
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize BillableDataBytes = sum(_BilledSize) by  computerName 
| sort by BillableDataBytes nulls last
```

A [propriedade](./log-standard-columns.md#_isbillable) `_IsBillable` especifica se os dados ingeridos incorrerão em encargos. 

Para ver a **contagem** de eventos faturáveis ingeridos por computador, use 

```kusto
find where TimeGenerated > ago(24h) project _IsBillable, Computer
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize eventCount = count() by computerName  
| sort by eventCount nulls last
```

> [!TIP]
> Use essas consultas `find` com moderação como verificações entre tipos de dados, uma que a execução delas faz um uso [intensivo de recursos](../log-query/query-optimization.md#query-performance-pane). Caso não precise de resultados **por computador**, consulte o tipo de dados Uso.

### <a name="data-volume-by-azure-resource-resource-group-or-subscription"></a>Volume de dados por recurso do Azure, grupo de recursos ou assinatura

Em relação aos dados de nós hospedados no Azure, você pode obter o **tamanho** de dados ingeridos __por computador__; use a [propriedade](./log-standard-columns.md#_resourceid) _ResourceId, a qual fornece o caminho completo para o recurso:

```kusto
find where TimeGenerated > ago(24h) project _ResourceId, _BilledSize, _IsBillable
| where _IsBillable == true 
| summarize BillableDataBytes = sum(_BilledSize) by _ResourceId | sort by BillableDataBytes nulls last
```

Para dados de nós hospedados no Azure, você pode obter o **tamanho** dos dados ingeridos __por assinatura do Azure__, obter o uso da `_SubscriptionId` propriedade como:

```kusto
find where TimeGenerated > ago(24h) project _ResourceId, _BilledSize, _IsBillable
| where _IsBillable == true 
| summarize BillableDataBytes = sum(_BilledSize) by _ResourceId
| summarize BillableDataBytes = sum(BillableDataBytes) by _SubscriptionId | sort by BillableDataBytes nulls last
```

Para obter o volume de dados por grupo de recursos, você pode analisar `_ResourceId` :

```kusto
find where TimeGenerated > ago(24h) project _ResourceId, _BilledSize, _IsBillable
| where _IsBillable == true 
| summarize BillableDataBytes = sum(_BilledSize) by _ResourceId
| extend resourceGroup = tostring(split(_ResourceId, "/")[4] )
| summarize BillableDataBytes = sum(BillableDataBytes) by resourceGroup | sort by BillableDataBytes nulls last
```

Você também pode analisar o `_ResourceId` mais completamente, se necessário, usando

```Kusto
| parse tolower(_ResourceId) with "/subscriptions/" subscriptionId "/resourcegroups/" 
    resourceGroup "/providers/" provider "/" resourceType "/" resourceName   
```

> [!TIP]
> Use essas consultas `find` com moderação como verificações entre tipos de dados, uma que a execução delas faz um uso [intensivo de recursos](../log-query/query-optimization.md#query-performance-pane). Caso não precise de resultados por assinatura, por grupo de recursos ou por nome de recurso, consulte o tipo de dados Uso.

> [!WARNING]
> Alguns dos campos do tipo de dados Uso, ainda no esquema, foram reprovados e seus valores não serão mais preenchidos. Estes são **Computador**, bem como campos relacionados à ingestão (**TotalBatches**, **BatchesWithinSla**, **BatchesOutsideSla**, **BatchesCapped** e **AverageProcessingTimeMs**.


### <a name="querying-for-common-data-types"></a>Consulta de tipos de dados comuns

Para aprofundar a fonte de dados de um determinado tipo de dados, aqui estão algumas consultas de exemplo úteis:

+ Recursos **baseados em workspace do Application Insights**
  - Saiba mais em [gerenciar o uso e os custos para Application insights](../app/pricing.md#data-volume-for-workspace-based-application-insights-resources)
+ Solução de **Segurança**
  - `SecurityEvent | summarize AggregatedValue = count() by EventID`
+ Solução do **Gerenciamento de log**
  - `Usage | where Solution == "LogManagement" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | summarize AggregatedValue = count() by DataType`
+ Tipo de dados de **Desempenho**
  - `Perf | summarize AggregatedValue = count() by CounterPath`
  - `Perf | summarize AggregatedValue = count() by CounterName`
+ Tipo de dados de **Evento**
  - `Event | summarize AggregatedValue = count() by EventID`
  - `Event | summarize AggregatedValue = count() by EventLog, EventLevelName`
+ Tipo de dados **Syslog**
  - `Syslog | summarize AggregatedValue = count() by Facility, SeverityLevel`
  - `Syslog | summarize AggregatedValue = count() by ProcessName`
+ Tipo de dados **AzureDiagnostics**
  - `AzureDiagnostics | summarize AggregatedValue = count() by ResourceProvider, ResourceId`

## <a name="tips-for-reducing-data-volume"></a>Dicas para reduzir o volume de dados

Algumas sugestões para reduzir o volume de logs coletados incluem:

| Origem do alto volume de dados | Como reduzir o volume de dados |
| -------------------------- | ------------------------- |
| Insights do contêiner         | [Configure o insights de contêiner](../insights/container-insights-cost.md#controlling-ingestion-to-reduce-cost) para coletar somente os dados necessários. |
| Eventos de segurança            | Selecione [eventos de segurança mínima ou comuns](../../security-center/security-center-enable-data-collection.md#data-collection-tier) <br> Alterar a política de auditoria de segurança para coletar somente eventos necessários. Em particular, examine a necessidade para coletar eventos para <br> - [auditoria de plataforma de filtragem](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772749(v=ws.10)) <br> - [auditoria de registro](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941614(v%3dws.10))<br> - [auditoria de sistema de arquivos](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772661(v%3dws.10))<br> - [auditoria de objeto kernel](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941615(v%3dws.10))<br> - [auditoria de manipulação de identificador](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772626(v%3dws.10))<br> - auditoria de armazenamento removível |
| Contadores de desempenho       | Altere a [configuração do contador de desempenho](data-sources-performance-counters.md) para: <br> - Reduzir a frequência de coleta <br> - Reduzir o número de contadores de desempenho |
| Logs de eventos                 | Altere a [configuração de log de eventos](data-sources-windows-events.md) para: <br> - Reduzir o número de logs de eventos coletados <br> - Coletar somente níveis de eventos necessários. Por exemplo, não colete eventos de nível *informações* |
| syslog                     | Altere a [configuração do syslog](data-sources-syslog.md) para: <br> - Reduzir o número de instalações coletadas <br> - Coletar somente níveis de eventos necessários. Por exemplo, não coletar eventos de nível *Informações* e *Depurar* |
| AzureDiagnostics           | Altere a [coleção de logs de recursos](./diagnostic-settings.md#create-in-azure-portal) para: <br> - Reduzir o número de logs de envio de recursos para o Log Analytics <br> - Coletar somente os logs necessários |
| Dados da solução de computadores que não precisam da solução | Use [direcionamento de solução](../insights/solution-targeting.md) para coletar dados somente dos grupos de computadores necessários. |

### <a name="getting-nodes-as-billed-in-the-per-node-pricing-tier"></a>Obter nós conforme a cobrança no tipo de preço Por Nó

Para obter uma lista de computadores que serão cobrados como nós caso o workspace esteja no tipo de preço Por Nó herdado, procure os nós que estão enviando **tipos de dados cobrados** (alguns tipos de dados são gratuitos). Para isso, use a [propriedade](./log-standard-columns.md#_isbillable) `_IsBillable` e use o campo mais à esquerda do nome de domínio totalmente qualificado. Isso retorna a contagem de computadores com dados cobrados por hora (o que é a granularidade na qual os nós são contados e cobrados):

```kusto
find where TimeGenerated > ago(24h) project Computer, TimeGenerated
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize billableNodes=dcount(computerName) by bin(TimeGenerated, 1h) | sort by TimeGenerated asc
```

### <a name="getting-security-and-automation-node-counts"></a>Obter contagens de nós de segurança e automação

Se você estiver no nível de preço "Por nó (OMS)", será cobrado com base no número de nós e soluções usados, o número de nós do Insights e do Analytics para os quais você está sendo faturado será mostrado na tabela do **Uso e custo estimado**.  

Para ver o número de nós de segurança distintos, você pode usar a consulta:

```kusto
union
(
    Heartbeat
    | where (Solutions has 'security' or Solutions has 'antimalware' or Solutions has 'securitycenter')
    | project Computer
),
(
    ProtectionStatus
    | where Computer !in~
    (
        (
            Heartbeat
            | project Computer
        )
    )
    | project Computer
)
| distinct Computer
| project lowComputer = tolower(Computer)
| distinct lowComputer
| count
```

Para ver o número de nós de automação distintos, use a consulta:

```kusto
 ConfigurationData 
 | where (ConfigDataType == "WindowsServices" or ConfigDataType == "Software" or ConfigDataType =="Daemons") 
 | extend lowComputer = tolower(Computer) | summarize by lowComputer 
 | join (
     Heartbeat 
       | where SCAgentChannel == "Direct"
       | extend lowComputer = tolower(Computer) | summarize by lowComputer, ComputerEnvironment
 ) on lowComputer
 | summarize count() by ComputerEnvironment | sort by ComputerEnvironment asc
```

## <a name="evaluating-the-legacy-per-node-pricing-tier"></a>Avaliar o tipo de preço Por Nó herdado

Geralmente, é difícil para os clientes fazerem a avaliação entre ter ou não workspaces com acesso ao tipo de preço **Por Nó** herdado é melhor nesse tipo de preço ou nos atuais **Pagamento Conforme o Uso** ou **Reserva de Capacidade**.  Isso envolve a compreensão da compensação entre o custo fixo por nó monitorado no tipo de preço Por Nó e sua alocação de dados inclusa de 500 MB/nó/dia, além do custo de pagar apenas por dados ingeridos no tipo de preço Pagamento Conforme o Uso (por GB). 

Para facilitar essa avaliação, a consulta a seguir pode ser usada para se obter uma recomendação sobre o tipo de preço ideal com base nos padrões de uso de um workspace.  Essa consulta analisa os nós monitorados e os dados ingeridos de um workspace nos últimos 7 dias e avalia qual tipo de preço seria ideal para cada dia. Para usar essa consulta, você precisa:

1. especificar se o workspace está usando a Central de Segurança do Azure definindo `workspaceHasSecurityCenter` como `true` ou `false`; 
2. atualizar os preços, caso você tenha descontos específicos; e
3. especificar o número de dias a serem examinados e analisados configurando `daysToEvaluate`. Isso será útil caso a consulta esteja demorando muito tempo tentando analisar 7 dias de dados. 

Esta é a consulta de recomendação de tipo de preço:

```kusto
// Set these parameters before running query
let workspaceHasSecurityCenter = true;  // Specify if the workspace has Azure Security Center
let PerNodePrice = 15.; // Enter your montly price per monitored nodes
let PerNodeOveragePrice = 2.30; // Enter your price per GB for data overage in the Per Node pricing tier
let PerGBPrice = 2.30; // Enter your price per GB in the Pay-as-you-go pricing tier
let daysToEvaluate = 7; // Enter number of previous days look at (reduce if the query is taking too long)
// ---------------------------------------
let SecurityDataTypes=dynamic(["SecurityAlert", "SecurityBaseline", "SecurityBaselineSummary", "SecurityDetection", "SecurityEvent", "WindowsFirewall", "MaliciousIPCommunication", "LinuxAuditLog", "SysmonEvent", "ProtectionStatus", "WindowsEvent", "Update", "UpdateSummary"]);
let StartDate = startofday(datetime_add("Day",-1*daysToEvaluate,now()));
let EndDate = startofday(now());
union * 
| where TimeGenerated >= StartDate and TimeGenerated < EndDate
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize nodesPerHour = dcount(computerName) by bin(TimeGenerated, 1h)  
| summarize nodesPerDay = sum(nodesPerHour)/24.  by day=bin(TimeGenerated, 1d)  
| join kind=leftouter (
    Heartbeat 
    | where TimeGenerated >= StartDate and TimeGenerated < EndDate
    | where Computer != ""
    | summarize ASCnodesPerHour = dcount(Computer) by bin(TimeGenerated, 1h) 
    | extend ASCnodesPerHour = iff(workspaceHasSecurityCenter, ASCnodesPerHour, 0)
    | summarize ASCnodesPerDay = sum(ASCnodesPerHour)/24.  by day=bin(TimeGenerated, 1d)   
) on day
| join (
    Usage 
    | where TimeGenerated >= StartDate and TimeGenerated < EndDate
    | where IsBillable == true
    | extend NonSecurityData = iff(DataType !in (SecurityDataTypes), Quantity, 0.)
    | extend SecurityData = iff(DataType in (SecurityDataTypes), Quantity, 0.)
    | summarize DataGB=sum(Quantity)/1000., NonSecurityDataGB=sum(NonSecurityData)/1000., SecurityDataGB=sum(SecurityData)/1000. by day=bin(StartTime, 1d)  
) on day
| extend AvgGbPerNode =  NonSecurityDataGB / nodesPerDay
| extend PerGBDailyCost = iff(workspaceHasSecurityCenter,
             (NonSecurityDataGB + max_of(SecurityDataGB - 0.5*ASCnodesPerDay, 0.)) * PerGBPrice,
             DataGB * PerGBPrice)
| extend OverageGB = iff(workspaceHasSecurityCenter, 
             max_of(DataGB - 0.5*nodesPerDay - 0.5*ASCnodesPerDay, 0.), 
             max_of(DataGB - 0.5*nodesPerDay, 0.))
| extend PerNodeDailyCost = nodesPerDay * PerNodePrice / 31. + OverageGB * PerNodeOveragePrice
| extend Recommendation = iff(PerNodeDailyCost < PerGBDailyCost, "Per Node tier", 
             iff(NonSecurityDataGB > 85., "Capacity Reservation tier", "Pay-as-you-go (Per GB) tier"))
| project day, nodesPerDay, ASCnodesPerDay, NonSecurityDataGB, SecurityDataGB, OverageGB, AvgGbPerNode, PerGBDailyCost, PerNodeDailyCost, Recommendation | sort by day asc
//| project day, Recommendation // Comment this line to see details
| sort by day asc
```

Essa consulta não é uma replicação exata de como o uso é calculado, mas ajudará a fornecer recomendações de tipo de preço na maioria dos casos.  

> [!NOTE]
> Para usar os direitos provenientes da aquisição de OMS E1 Suite, OMS E2 Suite OMS ou Complemento do OMS para System Center, escolha o tipo de preço *Por Nó* do Log Analytics.

## <a name="create-an-alert-when-data-collection-is-high"></a>Criar um alerta para quando a coleta de dados for alta

Esta seção descreve como criar um alerta o volume de dados nas últimas 24 horas excedeu um valor especificado, usando Azure Monitor [alertas de log](alerts-unified-log.md). 

Para alertar se o volume de dados faturáveis ingerido nas últimas 24 horas foi maior que 50 GB, siga estas etapas: 

- **Definir condição de alerta** especifica seu espaço de trabalho do Log Analytics como o destino do recurso.
- **Critérios de alerta** especificam o seguinte:
   - **Nome do sinal** seleciona **Pesquisa de registro personalizada**
   - **Pesquisar consulta** para `Usage | where IsBillable | summarize DataGB = sum(Quantity / 1000.) | where DataGB > 50` . Se você quiser um diferente 
   - A **Lógica de alerta** é **Baseada em** *número de resultados* e em se a **Condição** é *maior* que um **Limite**  de *0*
   - **Período** de *1440* minutos e **frequência de alerta** a cada *1440* minutos para ser executado uma vez por dia.
- **Definir os detalhes do alerta** especifica o seguinte:
   - **Nome** para o *volume de dados faturáveis maior que 50 GB em 24 horas*
   - A **Gravidade** como *Aviso*

Especifique um já existente ou crie um novo [Grupo de ação](action-groups.md) para que, quando o alerta de log corresponder aos critérios, você seja notificado.

Ao receber um alerta, use as etapas nas seções acima sobre como solucionar problemas por que o uso é maior do que o esperado.

## <a name="data-transfer-charges-using-log-analytics"></a>Cobranças de transferência de dados usando o Log Analytics

O envio de dados para Log Analytics pode incorrer em encargos de largura de banda de dados, no entanto, que se limitam a máquinas virtuais em que um agente de Log Analytics está instalado e não se aplica ao usar configurações de diagnóstico ou com outros conectores internos do Azure Sentinel Conforme descrito na [página de preços de largura de banda do Azure](https://azure.microsoft.com/pricing/details/bandwidth/), a transferência de dados entre os serviços do Azure localizados em duas regiões cobradas como transferência de dados de saída com base na taxa normal. As transferências de dados de entrada são gratuitas. No entanto, esse encargo é muito pequeno (baixa %) em comparação aos custos de ingestão de dados do Log Analytics. Consequentemente, controlar os custos para Log Analytics precisa se concentrar no [volume de dados ingerido](#understanding-ingested-data-volume). 


## <a name="troubleshooting-why-log-analytics-is-no-longer-collecting-data"></a>Solucionar problemas se o Log Analytics não está mais coletando dados

Se você estiver usando o tipo de preço gratuito herdado e tiver enviado mais de 500 MB de dados em um dia, a coleta de dados será interrompida pelo restante do dia. Alcançar o limite diário é um motivo comum para o Log Analytics parar de coletar dados ou para dados parecerem estar ausentes.  O Log Analytics cria um evento de tipo Operação quando a coleta de dados inicia e para. Execute a seguinte consulta na pesquisa para verificar se você está atingindo o limite diário e perdendo dados: 

```kusto
Operation | where OperationCategory == 'Data Collection Status'
```

Quando a coleta de dados é interrompida, o OperationStatus torna-se **Aviso**. Quando a coleta de dados inicia, o OperationStatus é **Com êxito**. A tabela a seguir descreve os motivos pelos quais a coleta de dados é interrompida e uma ação é sugerida para retomar a coleta de dados:  

|Motivo para a interrupção da coleta| Solução| 
|-----------------------|---------|
|O limite diário do seu workspace foi atingido|Aguarde para a coleta ser reiniciada automaticamente ou aumente o limite diário de volume de dados descrito em Gerenciar o volume máximo de dados diário. A hora de redefinição de limite diário é mostrada na página de **limite diário** . |
| Seu espaço de trabalho atingiu a [taxa de volume de ingestão de dados](../service-limits.md#log-analytics-workspaces) | O limite de taxa de volume de ingestão padrão para os dados enviados dos recursos do Azure usando as configurações de diagnóstico é de aproximadamente 6 GB/min por workspace. Esse é um valor aproximado, pois o tamanho real pode variar entre os tipos de dados, dependendo do tamanho do log e de sua taxa de compactação. Esse limite não se aplica aos dados enviados de agentes ou da API do coletor de dados. Se você enviar dados a uma taxa mais alta para um único workspace, alguns dados serão descartados, e um evento será enviado para a tabela de operações no seu workspace a cada 6 horas, enquanto o limite continuará sendo excedido. Se o volume de ingestão continuar a exceder o limite de taxa ou você estiver esperando alcançá-lo em breve, poderá solicitar um aumento no workspace enviando um e-mail para LAIngestionRate@microsoft.com ou abrindo uma solicitação de suporte. O evento a ser procurado que indica que um limite de taxa de ingestão de dados pode ser encontrado pela consulta `Operation | where OperationCategory == "Ingestion" | where Detail startswith "The rate of data crossed the threshold"`. |
|Limite diário de tipo de preço gratuito herdado atingido |Aguarde até o dia seguinte para que a coleta seja reiniciada automaticamente ou altere para um tipo de preço pago.|
|Assinatura do Azure está em um estado suspenso devido a:<br> A avaliação gratuita terminou<br> O Azure Pass expirou<br> Limite de gastos mensal atingido (por exemplo, em uma assinatura do MSDN ou do Visual Studio)|Converter para uma assinatura paga<br> Remova o limite ou espere o limite ser redefinido|

Para receber uma notificação quando a coleta de dados for interrompida, use as etapas descritas no alerta *Criar um limite de dados diário*. Use as etapas descritas em [criar um grupo de ações](action-groups.md) para configurar uma ação de runbook, webhook ou email para a regra de alerta. 

## <a name="limits-summary"></a>Resumo de limites

Há alguns limites adicionais do Log Analytics, alguns dos quais dependem do tipo de preço Log Analytics. Eles estão documentados em [assinatura do Azure e limites de serviço, cotas e restrições](../../azure-resource-manager/management/azure-subscription-service-limits.md#log-analytics-workspaces).


## <a name="next-steps"></a>Próximas etapas

- Confira [Pesquisas de logs nos Logs do Azure Monitor](../log-query/log-query-overview.md) para aprender a usar a linguagem de pesquisa. Você pode usar consultas de pesquisa para executar outras análises nos dados de uso.
- Use as etapas descritas em [criar um alerta de log](alerts-metric.md) para ser notificado quando um critério de pesquisa for atendido.
- Use [direcionamento de solução](../insights/solution-targeting.md) para coletar dados somente dos grupos de computadores necessários.
- Para configurar uma política de coleta de eventos eficaz, confira a [Política de filtragem da Central de Segurança do Azure](../../security-center/security-center-enable-data-collection.md).
- Altere a [configuração do contador de desempenho](data-sources-performance-counters.md).
- Para modificar as configurações de coleta de eventos, analise [configuração de log de eventos](data-sources-windows-events.md).
- Para modificar as configurações de coleta de syslog, analise [configuração de syslog](data-sources-syslog.md).
- Para modificar as configurações de coleta de syslog, analise [configuração de syslog](data-sources-syslog.md).