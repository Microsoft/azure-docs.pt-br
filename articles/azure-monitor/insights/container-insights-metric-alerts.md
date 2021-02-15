---
title: Alertas de métrica de Azure Monitor para contêineres
description: Este artigo revisa os alertas de métrica recomendados disponíveis em Azure Monitor para contêineres em visualização pública.
ms.topic: conceptual
ms.date: 10/28/2020
ms.openlocfilehash: a81dfb3fab57b378a56bfa8ac8102d723a50dbbc
ms.sourcegitcommit: b6267bc931ef1a4bd33d67ba76895e14b9d0c661
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/19/2020
ms.locfileid: "97695966"
---
# <a name="recommended-metric-alerts-preview-from-azure-monitor-for-containers"></a>Alertas de métrica recomendados (versão prévia) de Azure Monitor para contêineres

Para alertar sobre problemas de recursos do sistema quando eles estão passando por uma demanda de pico e executando quase capacidade, com Azure Monitor para contêineres, você criaria um alerta de log com base nos dados de desempenho armazenados em logs de Azure Monitor. Azure Monitor para contêineres agora inclui regras de alerta de métrica pré-configuradas para seu AKS e o cluster kubernetes habilitado para Arc do Azure, que está em visualização pública.

Este artigo revisa a experiência e fornece orientação sobre como configurar e gerenciar essas regras de alerta.

Se você não estiver familiarizado com alertas de Azure Monitor, consulte [visão geral de alertas no Microsoft Azure](../platform/alerts-overview.md) antes de iniciar. Para saber mais sobre os alertas de métrica, consulte [alertas de métrica em Azure monitor](../platform/alerts-metric-overview.md).

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, confirme o seguinte:

* As métricas personalizadas só estão disponíveis em um subconjunto de regiões do Azure. Uma lista de regiões com suporte é documentada em [regiões com suporte](../platform/metrics-custom-overview.md#supported-regions).

* Para dar suporte a alertas de métricas e à introdução de métricas adicionais, a versão mínima do agente necessária é **MCR.Microsoft.com/azuremonitor/containerinsights/ciprod:ciprod05262020** para AKS e **MCR.Microsoft.com/azuremonitor/containerinsights/ciprod:ciprod09252020** para o cluster kubernetes do Azure com Arc habilitado.

    Para verificar se o cluster está executando a versão mais recente do agente, você pode:

    * Execute o comando: `kubectl describe <omsagent-pod-name> --namespace=kube-system` . No status retornado, observe o valor em **imagem** para omsagent na seção *contêineres* da saída. 
    * Na guia **nós** , selecione o nó de cluster e, no painel **Propriedades** à direita, observe o valor em **marca de imagem do agente**.

    O valor mostrado para AKS deve ser a versão **ciprod05262020** ou posterior. O valor mostrado para o cluster kubernetes habilitado para Arc do Azure deve ser a versão **ciprod09252020** ou posterior. Se o cluster tiver uma versão mais antiga, consulte [como atualizar o Azure monitor para agente de contêineres](container-insights-manage-agent.md#upgrade-agent-on-aks-cluster) para obter as etapas para obter a versão mais recente.

    Para obter mais informações relacionadas à versão do agente, consulte [histórico de versão do agente](https://github.com/microsoft/docker-provider/tree/ci_feature_prod). Para verificar se as métricas estão sendo coletadas, você pode usar Azure Monitor métricas Explorer e verificar no **namespace de métrica** que o **insights** está listado. Se estiver, vá em frente e comece a configurar os alertas. Se você não vir nenhuma métrica coletada, a entidade de serviço de cluster ou a MSI não tem as permissões necessárias. Para verificar se o SPN ou o MSI é um membro da função de **Editor de métricas de monitoramento** , siga as etapas descritas na seção [atualizar por cluster usando CLI do Azure](container-insights-update-metrics.md#upgrade-per-cluster-using-azure-cli) para confirmar e definir a atribuição de função.

## <a name="alert-rules-overview"></a>Visão geral das regras de alerta

Para alertar sobre o que importa, Azure Monitor para contêineres inclui os seguintes alertas de métrica para seus clusters kubernetes habilitados para AKS e Azure Arc:

|Nome| Descrição |Limite padrão |
|----|-------------|------------------|
|% De CPU de contêiner média |Calcula a média de CPU usada por contêiner.|Quando o uso médio da CPU por contêiner for maior que 95%.| 
|% De memória do conjunto de trabalho de contêiner médio |Calcula a média de memória do conjunto de trabalho usada por contêiner.|Quando o uso médio de memória do conjunto de trabalho por contêiner é maior que 95%. |
|% de CPU Média |Calcula a média de CPU usada por nó. |Quando a utilização média da CPU do nó for maior que 80% |
|% De uso médio do disco |Calcula a média de uso do disco para um nó.|Quando o uso do disco para um nó for maior que 80%. |
|% De uso de volume persistente médio |Calcula o uso médio de VP por Pod. |Quando o uso médio de VP por pod é maior que 80%.|
|% De memória de conjunto de trabalho média |Calcula a média de memória do conjunto de trabalho para um nó. |Quando a memória do conjunto de trabalho médio para um nó é maior que 80%. |
|Reiniciando a contagem de contêineres |Calcula o número de contêineres de reinicialização. | Quando as reinicializações de contêiner forem maiores que 0. |
|Contagens de Pod com falha |Calcula se algum pod no estado de falha.|Quando um número de pods no estado de falha for maior que 0. |
|Status de não leitura de nó |Calcula se algum nó está em estado não legível.|Quando um número de nós no estado de não-leitura for maior que 0. |
|Contêineres mortos de OOM eliminados |Calcula o número de contêineres de OOM eliminados. |Quando um número de contêineres mortos de OOM eliminados for maior que 0. |
|% De pronto para pods |Calcula o estado de preparação médio de pods. |Quando o estado pronto de pods é inferior a 80%.|
|Contagem de trabalhos concluída |Calcula o número de trabalhos concluídos há mais de seis horas. |Quando o número de trabalhos obsoletos com mais de seis horas for maior que 0.|

Há propriedades comuns em todas essas regras de alerta:

* Todas as regras de alerta são baseadas em métricas.

* Todas as regras de alerta são desabilitadas por padrão.

* Todas as regras de alerta são avaliadas uma vez por minuto e retornam nos últimos 5 minutos de dados.

* As regras de alertas não têm um grupo de ação atribuído a eles por padrão. Você pode adicionar um [grupo de ações](../platform/action-groups.md) ao alerta selecionando um grupo de ações existente ou criando um novo grupo de ação ao editar a regra de alerta.

* Você pode modificar o limite de regras de alerta editando-as diretamente. No entanto, consulte as diretrizes fornecidas em cada regra de alerta antes de modificar seu limite.

As seguintes métricas baseadas em alerta têm características de comportamento exclusivas em comparação com as outras métricas:

* a métrica *completedJobsCount* é enviada somente quando há trabalhos que são concluídos há mais de seis horas.

* a métrica *containerRestartCount* é enviada somente quando há contêineres reiniciando.

* a métrica *oomKilledContainerCount* é enviada somente quando há contêineres mortos de OOM eliminados.

* as métricas *cpuExceededPercentage*, *memoryRssExceededPercentage* e *memoryWorkingSetExceededPercentage* são enviadas quando os valores de conjunto de trabalho CPU, memória RSS e memória excedem o limite configurado (o limite padrão é 95%). Esses limites são exclusivos do limite de condição de alerta especificado para a regra de alerta correspondente. Ou seja, se você quiser coletar essas métricas e analisá-las do [Metrics Explorer](../platform/metrics-getting-started.md), recomendamos que configure o limite para um valor inferior ao limite de alertas. A configuração relacionada às configurações de coleção para seus limites de utilização de recursos de contêiner pode ser substituída no arquivo ConfigMaps na seção `[alertable_metrics_configuration_settings.container_resource_utilization_thresholds]` . Consulte a seção [Configurar métricas realertáveis ConfigMaps](#configure-alertable-metrics-in-configmaps) para obter detalhes relacionados à configuração do arquivo de configuração do ConfigMap.

* a métrica *pvUsageExceededPercentage* é enviada quando a porcentagem de uso de volume persistente excede o limite configurado (o limite padrão é 60%). Esse limite é exclusivo do limite de condição de alerta especificado para a regra de alerta correspondente. Ou seja, se você quiser coletar essas métricas e analisá-las do [Metrics Explorer](../platform/metrics-getting-started.md), recomendamos que configure o limite para um valor inferior ao limite de alertas. A configuração relacionada às configurações de coleta para limites de utilização de volume persistente pode ser substituída no arquivo ConfigMaps na seção `[alertable_metrics_configuration_settings.pv_utilization_thresholds]` . Consulte a seção [Configurar métricas realertáveis ConfigMaps](#configure-alertable-metrics-in-configmaps) para obter detalhes relacionados à configuração do arquivo de configuração do ConfigMap. A coleta de métricas de volume persistente com declarações no namespace *Kube-System* são excluídas por padrão. Para habilitar a coleta nesse namespace, use a seção `[metric_collection_settings.collect_kube_system_pv_metrics]` no arquivo ConfigMap. Consulte [configurações de coleta de métrica](./container-insights-agent-config.md#metric-collection-settings) para obter detalhes.

## <a name="metrics-collected"></a>Métricas coletadas

As métricas a seguir são habilitadas e coletadas, a menos que especificado de outra forma, como parte desse recurso:

|Namespace da métrica |Metric |Descrição |
|---------|----|------------|
|Informações. contêiner/nós |cpuUsageMillicores |Utilização da CPU em milicores por host.|
|Informações. contêiner/nós |cpuUsagePercentage |Percentual de uso da CPU por nó.|
|Informações. contêiner/nós |memoryRssBytes |Utilização de RSS de memória em bytes por host.|
|Informações. contêiner/nós |memoryRssPercentage |Percentual de uso de RSS de memória por host.|
|Informações. contêiner/nós |memoryWorkingSetBytes |Utilização de conjunto de trabalho de memória em bytes pelo host.|
|Informações. contêiner/nós |memoryWorkingSetPercentage |Porcentagem de uso do conjunto de trabalho de memória por host.|
|Informações. contêiner/nós |nodesCount |Contagem de nós por status.|
|Informações. contêiner/nós |diskUsedPercentage |Percentual do disco usado no nó pelo dispositivo.|
|Percepções. Container/pods |podCount |Contagem de pods por controlador, namespace, nó e fase.|
|Percepções. Container/pods |completedJobsCount |Contagem de trabalhos concluídos-limite configurável do usuário mais antigo (o padrão é seis horas) por controlador, namespace kubernetes. |
|Percepções. Container/pods |restartingContainerCount |Contagem de reinicializações de contêiner por controlador, namespace kubernetes.|
|Percepções. Container/pods |oomKilledContainerCount |Contagem de contêineres OOMkilled por controlador, namespace kubernetes.|
|Percepções. Container/pods |podReadyPercentage |Porcentagem de pods no estado pronto por controlador, namespace kubernetes.|
|Informações. contêiner/contêineres |cpuExceededPercentage |Porcentagem de utilização da CPU para contêineres que excedem o limite configurável pelo usuário (o padrão é 95,0) pelo nome do contêiner, nome do controlador, namespace kubernetes, nome do pod.<br> Coleta  |
|Informações. contêiner/contêineres |memoryRssExceededPercentage |Porcentagem de RSS de memória para contêineres excedendo o limite configurável pelo usuário (o padrão é 95,0) pelo nome do contêiner, nome do controlador, namespace kubernetes, nome do pod.|
|Informações. contêiner/contêineres |memoryWorkingSetExceededPercentage |Porcentagem do conjunto de trabalho de memória para contêineres que excedem o limite configurável pelo usuário (o padrão é 95,0) pelo nome do contêiner, nome do controlador, namespace kubernetes, nome do pod.|
|Percepções. Container/persistentvolumes |pvUsageExceededPercentage |Porcentagem de utilização de PV para volumes persistentes que excedem o limite configurável pelo usuário (o padrão é 60,0) pelo nome da declaração, namespace kubernetes, nome do volume, nome do pod e nome do nó.

## <a name="enable-alert-rules"></a>Habilitar regras de alerta

Siga estas etapas para habilitar os alertas de métrica no Azure Monitor do portal do Azure. Para habilitar o uso de um modelo do Resource Manager, consulte [habilitar com um modelo do Resource Manager](#enable-with-a-resource-manager-template).

### <a name="from-the-azure-portal"></a>No portal do Azure

Esta seção orienta a habilitação do alerta de métrica de Azure Monitor para contêineres (versão prévia) do portal do Azure.

1. Entre no [portal do Azure](https://portal.azure.com/).

2. O acesso à Azure Monitor para o recurso alerta de métricas de contêineres (versão prévia) está disponível diretamente de um cluster AKS selecionando **insights** no painel esquerdo na portal do Azure.

3. Na barra de comandos, selecione **alertas recomendados**.

    ![Opção de alertas recomendados no Azure Monitor para contêineres](./media/container-insights-metric-alerts/command-bar-recommended-alerts.png)

4. O painel de propriedades **alertas recomendados** é exibido automaticamente no lado direito da página. Por padrão, todas as regras de alerta na lista estão desabilitadas. Depois de selecionar **habilitar**, a regra de alerta é criada e o nome da regra é atualizado para incluir um link para o recurso de alerta.

    ![Painel de propriedades de alertas recomendados](./media/container-insights-metric-alerts/recommended-alerts-pane.png)

    Depois de selecionar **habilitar/desabilitar ativar/desativar** para habilitar o alerta, uma regra de alerta é criada e o nome da regra é atualizado para incluir um link para o recurso de alerta real.

    ![Habilitar a regra de alerta](./media/container-insights-metric-alerts/recommended-alerts-pane-enable.png)

5. As regras de alerta não são associadas a um [grupo de ações](../platform/action-groups.md) para notificar os usuários de que um alerta foi disparado. Selecione **nenhum grupo de ação atribuído** e, na página **grupos de ações** , especifique um existente ou crie um grupo de ação selecionando **Adicionar** ou **criar**.

    ![Selecionar um grupo de ação](./media/container-insights-metric-alerts/select-action-group.png)

### <a name="enable-with-a-resource-manager-template"></a>Habilitar com um modelo do Resource Manager

Você pode usar um modelo de Azure Resource Manager e um arquivo de parâmetros para criar os alertas de métrica incluídos no Azure Monitor.

As etapas básicas são as seguintes:

1. Baixe um ou todos os modelos disponíveis que descrevem como criar o alerta do [GitHub](https://github.com/microsoft/Docker-Provider/tree/ci_dev/alerts/recommended_alerts_ARM).

2. Crie e use um [arquivo de parâmetros](../../azure-resource-manager/templates/parameter-files.md) como um JSON para definir os valores necessários para criar a regra de alerta.

3. Implante o modelo do portal do Azure, do PowerShell ou do CLI do Azure.

#### <a name="deploy-through-azure-portal"></a>Implantar por meio do portal do Azure

1. Baixe e salve em uma pasta local, o modelo Azure Resource Manager e o arquivo de parâmetro, para criar a regra de alerta usando os seguintes comandos:

2. Para implantar um modelo personalizado por meio do portal, selecione **criar um recurso** no [portal do Azure](https://portal.azure.com).

3. Pesquise por **modelo** e, em seguida, selecione **implantação de modelo**.

4. Selecione **Criar**.

5. Você verá várias opções para criar um modelo, selecionar **criar seu próprio modelo no editor**.

6. Na **página Editar modelo**, selecione **carregar arquivo** e, em seguida, selecione o arquivo de modelo.

7. Na página **Editar modelo** , selecione **salvar**.

8. Na página **implantação personalizada** , especifique o seguinte e, quando concluir, selecione **comprar** para implantar o modelo e criar a regra de alerta.

    * Resource group
    * Location
    * Nome do alerta
    * ID de recurso de cluster

#### <a name="deploy-with-azure-powershell-or-cli"></a>Implantar com o Azure PowerShell ou a CLI

1. Baixe e salve em uma pasta local, o modelo Azure Resource Manager e o arquivo de parâmetro, para criar a regra de alerta usando os seguintes comandos:

2. Você pode criar o alerta de métrica usando o arquivo de modelo e parâmetros usando o PowerShell ou a CLI do Azure.

    Usando o PowerShell do Azure

    ```powershell
    Connect-AzAccount

    Select-AzSubscription -SubscriptionName <yourSubscriptionName>
    New-AzResourceGroupDeployment -Name CIMetricAlertDeployment -ResourceGroupName ResourceGroupofTargetResource `
    -TemplateFile templateFilename.json -TemplateParameterFile templateParameterFilename.parameters.json
    ```

    Usando a CLI do Azure

    ```azurecli
    az login

    az deployment group create \
    --name AlertDeployment \
    --resource-group ResourceGroupofTargetResource \
    --template-file templateFileName.json \
    --parameters @templateParameterFilename.parameters.json
    ```

    >[!NOTE]
    >Embora o alerta de métrica possa ser criado em um grupo de recursos diferente do recurso de destino, é recomendável usar o mesmo grupo de recursos como recurso de destino.

## <a name="edit-alert-rules"></a>Editar regras de alerta

Você pode exibir e gerenciar regras de alerta de Azure Monitor para contêineres, para editar seu limite ou configurar um [grupo de ação](../platform/action-groups.md) para o cluster AKs. Embora você possa executar essas ações no portal do Azure e CLI do Azure, ele também pode ser feito diretamente do cluster AKS no Azure Monitor para contêineres.

1. Na barra de comandos, selecione **alertas recomendados**.

2. Para modificar o limite, no painel **alertas recomendados** , selecione o alerta habilitado. Na **regra editar**, selecione os **critérios de alerta** que você deseja editar.

    * Para modificar o limite da regra de alerta, selecione a **condição**.
    * Para especificar um grupo de ação existente ou criar um, selecione **Adicionar** ou **criar** em **grupo de ação**

Para exibir alertas criados para as regras habilitadas, no painel **alertas recomendados** , selecione **Exibir em alertas**. Você será redirecionado para o menu de alerta do cluster AKS, onde poderá ver todos os alertas criados no momento para o cluster.

## <a name="configure-alertable-metrics-in-configmaps"></a>Configurar métricas de alerta no ConfigMaps

Execute as etapas a seguir para configurar o arquivo de configuração do ConfigMap para substituir os limites de utilização padrão. Essas etapas são aplicáveis somente para as seguintes métricas de alerta:

* *cpuExceededPercentage*
* *memoryRssExceededPercentage*
* *memoryWorkingSetExceededPercentage*
* *pvUsageExceededPercentage*

1. Edite o arquivo ConfigMap YAML na seção `[alertable_metrics_configuration_settings.container_resource_utilization_thresholds]` ou `[alertable_metrics_configuration_settings.pv_utilization_thresholds]` .

   - Para modificar o limite de *cpuExceededPercentage* para 90% e iniciar a coleta dessa métrica quando esse limite for atingido e excedido, configure o arquivo ConfigMap usando o exemplo a seguir:

     ```
     [alertable_metrics_configuration_settings.container_resource_utilization_thresholds]
         # Threshold for container cpu, metric will be sent only when cpu utilization exceeds or becomes equal to the following percentage
         container_cpu_threshold_percentage = 90.0
         # Threshold for container memoryRss, metric will be sent only when memory rss exceeds or becomes equal to the following percentage
         container_memory_rss_threshold_percentage = 95.0
         # Threshold for container memoryWorkingSet, metric will be sent only when memory working set exceeds or becomes equal to the following percentage
         container_memory_working_set_threshold_percentage = 95.0
     ```

   - Para modificar o limite de *pvUsageExceededPercentage* para 80% e iniciar a coleta dessa métrica quando esse limite for atingido e excedido, configure o arquivo ConfigMap usando o exemplo a seguir:

     ```
     [alertable_metrics_configuration_settings.pv_utilization_thresholds]
         # Threshold for persistent volume usage bytes, metric will be sent only when persistent volume utilization exceeds or becomes equal to the following percentage
         pv_usage_threshold_percentage = 80.0
     ```

2. Execute o seguinte comando kubectl: `kubectl apply -f <configmap_yaml_file.yaml>` .

    Exemplo: `kubectl apply -f container-azm-ms-agentconfig.yaml`.

A alteração de configuração pode levar alguns minutos para ser concluída antes de entrar em vigor, e todos os pods de omsagent no cluster serão reiniciados. A reinicialização é uma reinicialização sem interrupção para todos os pods omsagents; Eles nem todos reiniciam ao mesmo tempo. Quando as reinicializações forem concluídas, será exibida uma mensagem semelhante ao exemplo a seguir e inclui o resultado: `configmap "container-azm-ms-agentconfig" created` .

## <a name="next-steps"></a>Próximas etapas

- Exiba [exemplos de consulta de log](container-insights-log-search.md#search-logs-to-analyze-data) para ver consultas predefinidas e exemplos para avaliar ou personalizar para alertar, Visualizar ou analisar seus clusters.

- Para saber mais sobre Azure Monitor e como monitorar outros aspectos do cluster do kubernetes, consulte [Exibir o desempenho do cluster kubernetes](container-insights-analyze.md).