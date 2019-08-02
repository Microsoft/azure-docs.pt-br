---
title: Log de instância de contêiner com os logs do Azure Monitor
description: Saiba como enviar logs de Instâncias de Contêiner do Azure para logs do Azure Monitor.
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: overview
ms.date: 07/09/2019
ms.author: danlep
ms.openlocfilehash: 4099bc0b15f02faade02f47aeb00fb7c4b4a3332
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325878"
---
# <a name="container-instance-logging-with-azure-monitor-logs"></a>Log de instância de contêiner com os logs do Azure Monitor

Os workspaces do Log Analytics fornecem uma localização centralizada para armazenar e consultar dados de log não apenas dos recursos do Azure, mas também dos recursos locais e recursos em outras nuvens. As Instâncias de Contêiner do Azure incluem suporte interno para enviar dados aos logs do Azure Monitor.

Para enviar dados de instância de contêiner para logs do Azure Monitor, você deve especificar uma chave de workspace e uma ID de workspace do Log Analytics ao criar um grupo de contêineres. As seções a seguir descrevem a criação de um grupo de contêineres com registro em log habilitado e consulta de logs.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para habilitar o registro em log nas instâncias de contêiner, serão necessários o seguinte:

* [Espaço de Trabalho do Log Analytics](../azure-monitor/learn/quick-create-workspace.md)
* [CLI do Azure](/cli/azure/install-azure-cli) (ou [Cloud Shell](/azure/cloud-shell/overview))

## <a name="get-log-analytics-credentials"></a>Obter credenciais do Log Analytics

As Instâncias de Contêiner do Azure precisam de permissão para enviar dados ao espaço de trabalho do Log Analytics. Para conceder essa permissão e habilitar o registro em log, será necessário fornecer a ID do espaço de trabalho do Log Analytics e uma de suas chaves (primária ou secundária) ao criar o grupo de contêineres.

Para obter a ID do espaço de trabalho do Log Analytics e a chave primária:

1. Navegue até o espaço de trabalho do Log Analytics no portal do Azure
1. Em **Configurações**, selecione **Configurações avançadas**
1. Selecione **Fontes Conectadas** > **Servidores Windows** (ou **Servidores Linux**--a ID e as chaves são as mesmas para ambos)
1. Anote:
   * **ID DO WORKSPACE**
   * **CHAVE PRIMÁRIA**

## <a name="create-container-group"></a>Criar grupo de contêineres

Agora que você tem a ID e do espaço de trabalho do Log Analytics e a chave primária, você está pronto para criar um grupo de contêineres habilitado para log.

Os exemplos a seguir demonstram duas maneiras de criar um grupo de contêineres com um único contêiner [fluentd][fluentd]: CLI do Azure e CLI do Azure com um modelo YAML. O contêiner Fluentd produz várias linhas de saída na configuração padrão. Como essa saída é enviada para o espaço de trabalho do Log Analytics, ela funciona bem para demonstrar a visualização e a consulta de logs.

### <a name="deploy-with-azure-cli"></a>Implantar com a CLI do Azure

Para implantar com a CLI do Azure, especifique os parâmetros `--log-analytics-workspace` e `--log-analytics-workspace-key` no comando [az container create][az-container-create]. Substitua os dois valores de workspace pelos valores que você obteve na etapa anterior (e atualize o nome do grupo de recursos) antes de executar o comando a seguir.

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainergroup001 \
    --image fluent/fluentd \
    --log-analytics-workspace <WORKSPACE_ID> \
    --log-analytics-workspace-key <WORKSPACE_KEY>
```

### <a name="deploy-with-yaml"></a>Implantar com YAML

Use esse método se você preferir implantar grupos de contêineres com YAML. O YAML a seguir define um grupo de contêineres com um único contêiner. Copie o YAML para em um novo arquivo, então substitua `LOG_ANALYTICS_WORKSPACE_ID` e `LOG_ANALYTICS_WORKSPACE_KEY` pelos valores obtidos na etapa anterior. Salve o arquivo como **deploy-aci.yaml**.

```yaml
apiVersion: 2018-10-01
location: eastus
name: mycontainergroup001
properties:
  containers:
  - name: mycontainer001
    properties:
      environmentVariables: []
      image: fluent/fluentd
      ports: []
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
  osType: Linux
  restartPolicy: Always
  diagnostics:
    logAnalytics:
      workspaceId: LOG_ANALYTICS_WORKSPACE_ID
      workspaceKey: LOG_ANALYTICS_WORKSPACE_KEY
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

Em seguida, execute o comando a seguir para implantar o grupo de contêineres. Substitua `myResourceGroup` por um grupo de recursos em sua assinatura (ou primeiro crie um grupo de recursos chamado "myResourceGroup"):

```azurecli-interactive
az container create --resource-group myResourceGroup --name mycontainergroup001 --file deploy-aci.yaml
```

Você deverá receber uma resposta do Azure contendo detalhes da implantação, logo após a emissão do comando.

## <a name="view-logs-in-azure-monitor-logs"></a>Exibir logs nos logs do Azure Monitor

Após implantar o grupo de contêineres, poderá demorar vários minutos (até 10) para que as primeiras entradas de log apareçam no portal do Azure. Para exibir logs do grupo de contêiner:

1. Navegue até o espaço de trabalho do Log Analytics no portal do Azure
1. Em **Geral**, selecione **Logs**  
1. Digite a seguinte consulta: `search *`
1. Selecionar **Executar**

Você deverá ver vários resultados exibidos pela consulta `search *`. Caso não veja nenhum resultado, aguarde alguns minutos e, em seguida, selecione o botão **Executar** para executar a consulta novamente. Por padrão, as entradas de log são exibidas no formato de **Tabela**. Em seguida, você poderá expandir uma linha para ver o conteúdo de uma entrada de log individual.

![Pesquisa de Logs no portal do Azure][log-search-01]

## <a name="query-container-logs"></a>Consulta de logs do contêiner

Os logs do Azure Monitor incluem uma [linguagem de consulta][query_lang] extensa para efetuar pull das informações de potencialmente milhares de linhas de saída de log.

O agente de log das Instâncias de Contêiner do Azure envia entradas para a tabela `ContainerInstanceLog_CL` no espaço de trabalho do Log Analytics. A estrutura básica de uma consulta é a tabela de origem (`ContainerInstanceLog_CL`) seguida por uma série de operadores separados pelo caractere de pipe (`|`). É possível encadear vários operadores para refinar os resultados e executar funções avançadas.

Para ver os resultados da consulta de exemplo, cole a consulta a seguir na caixa de texto da consulta (em "Mostrar conversor da linguagem herdada") e selecione o botão **EXECUTAR** para executar a consulta. Esta consulta exibe todas as entradas de log cujo campo de "Mensagem" contém a palavra "warn":

```query
ContainerInstanceLog_CL
| where Message contains("warn")
```

Consultas mais complexas também têm suporte. Por exemplo, essa consulta exibe apenas as entradas de log do grupo de contêineres "mycontainergroup001" gerado na última hora:

```query
ContainerInstanceLog_CL
| where (ContainerGroup_s == "mycontainergroup001")
| where (TimeGenerated > ago(1h))
```

## <a name="next-steps"></a>Próximas etapas

### <a name="azure-monitor-logs"></a>Logs do Azure Monitor

Para obter mais informações sobre como consultar logs e configurar alertas nos logs do Azure Monitor, confira:

* [Noções básicas sobre pesquisas de logs nos logs do Azure Monitor](../log-analytics/log-analytics-log-search.md)
* [Alertas unificados no Azure Monitor](../azure-monitor/platform/alerts-overview.md)


### <a name="monitor-container-cpu-and-memory"></a>Monitorar memória e CPU do contêiner

Para obter informações sobre monitoramento de recursos de memória e CPU da instância de contêiner, consulte:

* [Monitorar os recursos de contêiner em Instâncias de Contêiner do Azure](container-instances-monitor.md).

<!-- IMAGES -->
[log-search-01]: ./media/container-instances-log-analytics/portal-query-01.png

<!-- LINKS - External -->
[fluentd]: https://hub.docker.com/r/fluent/fluentd/
[query_lang]: https://aka.ms/LogAnalyticsLanguage

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create