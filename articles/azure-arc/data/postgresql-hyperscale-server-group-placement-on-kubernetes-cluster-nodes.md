---
title: Posicionamento de um grupo de servidores de hiperescala PostgreSQL nos nós de cluster kubernetes
description: Explica como as instâncias do PostgreSQL que formam um grupo de servidores de hiperescala PostgreSQL são colocadas nos nós de cluster kubernetes
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 02/11/2021
ms.topic: how-to
ms.openlocfilehash: b88b36ba8ec1d2d612adbbf19a6cf1e91fbb2cfd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100377747"
---
# <a name="azure-arc-enabled-postgresql-hyperscale-server-group-placement"></a>Posicionamento do grupo de servidores de hiperescala do PostgreSQL habilitado para o Azure Arc

Neste artigo, vamos usar um exemplo para ilustrar como as instâncias do PostgreSQL do grupo de servidores de hiperescala PostgreSQL habilitados para o Azure ARC são colocadas nos nós físicos do cluster kubernetes que os hospeda. 

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="configuration"></a>Configuração

Neste exemplo, estamos usando um cluster AKS (serviço kubernetes do Azure) que tem quatro nós físicos. 

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/1_cluster_portal.png" alt-text="cluster AKS de 4 nós no portal do Azure":::

Liste os nós físicos do cluster kubernetes. Execute o comando:

```console
kubectl get nodes
```

`kubectl` retorna quatro nós físicos dentro do cluster kubernetes:

```output
NAME                                STATUS   ROLES   AGE   VERSION
aks-agentpool-42715708-vmss000000   Ready    agent   11h   v1.17.9
aks-agentpool-42715708-vmss000001   Ready    agent   11h   v1.17.9
aks-agentpool-42715708-vmss000002   Ready    agent   11h   v1.17.9
aks-agentpool-42715708-vmss000003   Ready    agent   11h   v1.17.9
```

A arquitetura pode ser representada como:

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/2_logical_cluster.png" alt-text="Representação lógica de 4 nós agrupados em um cluster kubernetes":::

O cluster kubernetes hospeda um controlador de dados de arco do Azure e um grupo de servidores de hiperescala PostgreSQL habilitados para o Azure Arc. Esse grupo de servidores é constituído de três instâncias do PostgreSQL: um coordenador e dois trabalhadores.

Liste o pods com o comando:

```console
kubectl get pods -n arc3
```
`kubectl` apresenta

```output
NAME                 READY   STATUS    RESTARTS   AGE
…
postgres01c-0         3/3     Running   0          9h
postgres01w-0         3/3     Running   0          9h
postgres01w-1         3/3     Running   0          9h
```
Cada um desses pods hospeda uma instância do PostgreSQL. Juntos, o pods formam o grupo de servidores de hiperescala do PostgreSQL habilitado para o Azure Arc:

```output
Pod name        Role in the server group
postgres01c-0 Coordinator
postgres01w-0   Worker
postgres01w-1   Worker
```

## <a name="placement"></a>Posicionamento
Vejamos como o kubernetes coloca o pods do grupo de servidores. Descreva cada pod e identifique em qual nó físico do cluster kubernetes eles são colocados. Por exemplo, para o coordenador, execute o seguinte comando:

```console
kubectl describe pod postgres01c-0 -n arc3
```

`kubectl` apresenta

```output
Name:         postgres01c-0
Namespace:    arc3
Priority:     0
Node:         aks-agentpool-42715708-vmss000000
Start Time:   Thu, 17 Sep 2020 00:40:33 -0700
…
```

À medida que executamos esse comando para cada um dos pods, resumimos o posicionamento atual como:

| Função de grupo de servidores|Pod do grupo de servidores|Nó físico kubernetes que hospeda o Pod |
|--|--|--|
| Trabalho|postgres01-1|AKs-agentpool-42715708-vmss000002 |
| Trabalho|postgres01-2|AKs-agentpool-42715708-vmss000003 |

Além disso, observe também, na descrição do pods, os nomes dos contêineres que cada pod hospeda. Por exemplo, para o segundo trabalho, execute o seguinte comando:

```console
kubectl describe pod postgres01w-1 -n arc3
```

`kubectl` apresenta

```output
…
Node:         aks-agentpool-42715708-vmss000003/10.240.0.7
..
Containers:
  Fluentbit:
…
  Postgres:
…
  Telegraf:
…
```

Cada pod que faz parte do grupo de servidores de hiperescala PostgreSQL habilitado para Arc do Azure hospeda os três contêineres a seguir:

|Contêineres|Descrição
|----|----|
|`Fluentbit` |Data * coletor de logs: https://fluentbit.io/
|`Postgres`|Parte da instância PostgreSQL do grupo de servidores de hiperescala PostgreSQL habilitados para o Azure Arc
|`Telegraf` |Coletor de métricas: https://www.influxdata.com/time-series-platform/telegraf/

A arquitetura é semelhante ao:

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/3_pod_placement.png" alt-text="3 pods, cada um colocado em nós separados":::

Isso significa que, neste ponto, cada instância PostgreSQL que constituem o grupo de servidores de hiperescala PostgreSQL habilitado para o Azure Arc é hospedado em um host físico específico dentro do contêiner kubernetes. Essa configuração fornece o máximo de desempenho do grupo de servidores de hiperescala PostgreSQL habilitado para Arc do Azure, pois cada função (coordenador e trabalhadores) usa os recursos de cada nó físico. Esses recursos não são compartilhados entre várias funções do PostgreSQL.

## <a name="scale-out-azure-arc-enabled-postgresql-hyperscale"></a>Escalar horizontalmente a hiperescala do PostgreSQL habilitada para o Arc do Azure

Agora, vamos expandir para adicionar um terceiro nó de trabalho ao grupo de servidores e observar o que acontece. Ele criará uma quarta instância PostgreSQL que será hospedada em um quarto Pod.
Para escalar horizontalmente, execute o comando:

```console
azdata arc postgres server edit --name postgres01 --workers 3
```

Que produz a seguinte saída:

```output
Updating postgres01 in namespace `arc3`
postgres01 is Ready
```

Liste os grupos de servidores implantados no controlador de dados Arc do Azure e verifique se o grupo de servidores agora é executado com três trabalhadores. Execute o comando:

```console
azdata arc postgres server list
```

Observe que ele foi dimensionado de dois trabalhadores para três trabalhadores:

```output
Name        State    Workers
----------  -------  ---------
postgres01  Ready    3
```

Como fizemos anteriormente, observe que o grupo de servidores agora usa um total de quatro pods:

```console
kubectl get pods -n arc3
```

```output
NAME                 READY   STATUS    RESTARTS   AGE
…
postgres01c-0         3/3     Running   0          11h
postgres01w-0         3/3     Running   0          11h
postgres01w-1         3/3     Running   0          11h
postgres01w-2         3/3     Running   0          5m2s
```

E descreva o novo pod para identificar em quais dos nós físicos do cluster kubernetes ele está hospedado.
Execute o comando:

```console
kubectl describe pod postgres01w-2 -n arc3
```

Para identificar o nome do nó de hospedagem:

```output
Name:         postgres01w-2
Namespace:    arc3
Priority:     0
Node:         aks-agentpool-42715708-vmss000000
```

O posicionamento das instâncias do PostgreSQL nos nós físicos do cluster agora é:

|Função de grupo de servidores|Pod do grupo de servidores|Nó físico kubernetes que hospeda o Pod
|-----|-----|-----
|Pelos|postgres01-0|AKs-agentpool-42715708-vmss000000
|Trabalho|postgres01-1|AKs-agentpool-42715708-vmss000002
|Trabalho|postgres01-2|AKs-agentpool-42715708-vmss000003
|Trabalho|postgres01-3|AKs-agentpool-42715708-vmss000000

E observe que o Pod do novo trabalhador (postgres01w-2) foi colocado no mesmo nó que o coordenador. 

A arquitetura é semelhante ao:

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/4_pod_placement_.png" alt-text="Quarto pod no mesmo nó que o coordenador":::

Por que o novo trabalhador/Pod não foi colocado no nó físico restante do cluster kubernetes AKs-agentpool-42715708-vmss000003?

O motivo é que o último nó físico do cluster kubernetes está na verdade hospedando vários pods que hospedam componentes adicionais que são necessários para executar os serviços de dados habilitados para o Azure Arc. Kubernetes avaliou que o melhor candidato – no momento do agendamento – para hospedar o operador adicional é o nó físico AKs-agentpool-42715708-vmss000000. 

Usando os mesmos comandos acima; vemos o que cada nó físico está hospedando:

|Outros nomes de pods\* |Uso|Nó físico kubernetes hospedando o pods
|----|----|----
|bootstrapper-jh48b|Um serviço que manipula solicitações de entrada para criar, editar e excluir recursos personalizados, como instâncias gerenciadas do SQL, grupos de servidores de hiperescala PostgreSQL e controladores de dados|AKs-agentpool-42715708-vmss000003
|controle-gwmbs||AKs-agentpool-42715708-vmss000002
|controldb-0|O armazenamento de dados do controlador que é usado para armazenar a configuração e o estado do controlador de dados.|AKs-agentpool-42715708-vmss000001
|controlwd-zzjp7|O serviço "Watch Dog" do controlador que fica atento à disponibilidade do controlador de dados.|AKs-agentpool-42715708-vmss000000
|logsdb-0|Uma instância de pesquisa elástica que é usada para armazenar todos os logs coletados em todos os pods dos serviços de dados de arco. Elasticsearch, recebe dados do `Fluentbit` contêiner de cada pod|AKs-agentpool-42715708-vmss000003
|logsui-5fzv5|Uma instância de Kibana que fica na parte superior do banco de dados de pesquisa elástica para apresentar uma GUI do log Analytics.|AKs-agentpool-42715708-vmss000003
|metricsdb-0|Uma instância de InfluxDB que é usada para armazenar todas as métricas coletadas em todos os pods de serviços de dados de arco. InfluxDB, recebe dados do `Telegraf` contêiner de cada pod|AKs-agentpool-42715708-vmss000000
|metricsdc-47d47|Um conjunto de daemon implantado em todos os nós kubernetes no cluster para coletar métricas em nível de nó sobre os nós.|AKs-agentpool-42715708-vmss000002
|metricsdc-864kj|Um conjunto de daemon implantado em todos os nós kubernetes no cluster para coletar métricas em nível de nó sobre os nós.|AKs-agentpool-42715708-vmss000001
|metricsdc-l8jkf|Um conjunto de daemon implantado em todos os nós kubernetes no cluster para coletar métricas em nível de nó sobre os nós.|AKs-agentpool-42715708-vmss000003
|metricsdc-nxm4l|Um conjunto de daemon implantado em todos os nós kubernetes no cluster para coletar métricas em nível de nó sobre os nós.|AKs-agentpool-42715708-vmss000000
|metricsui-4fb7l|Uma instância Grafana que fica na parte superior do banco de dados InfluxDB para apresentar uma GUI do painel de monitoramento.|AKs-agentpool-42715708-vmss000003
|mgmtproxy-4qppp|Uma camada de proxy de aplicativo Web que fica na frente das instâncias Grafana e Kibana.|AKs-agentpool-42715708-vmss000002

> \* O sufixo nos nomes de Pod variará em outras implantações. Além disso, estamos listando apenas os pods hospedados no namespace kubernetes do controlador de dados de arco do Azure.

A arquitetura é semelhante ao:

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/5_full_list_of_pods.png" alt-text="Todos os pods no namespace em vários nós":::

Conforme descrito acima, os nós de coordenador (Pod 1) do grupo de servidores postgres de hiperescala habilitados para o Azure Arc compartilham os mesmos recursos físicos que o terceiro nó de trabalho (Pod 4) do grupo de servidores. Isso é aceitável porque o nó de coordenador geralmente usa muito poucos recursos em comparação com o que um nó de trabalho pode estar usando. Por esse motivo, escolha cuidadosamente:
- o tamanho do cluster kubernetes e as características de cada um de seus nós físicos (memória, vCore)
- o número de nós físicos dentro do cluster kubernetes
- os aplicativos ou as cargas de trabalho que você hospeda no cluster kubernetes.

A implicação de hospedar muitas cargas de trabalho no cluster kubernetes é a limitação pode ocorrer para o grupo de servidores de hiperescala PostgreSQL habilitados para o Azure Arc. Se isso acontecer, você não se beneficiará muito com sua capacidade de dimensionar horizontalmente. O desempenho que você sai do sistema não se trata apenas do posicionamento ou das características físicas dos nós físicos ou do sistema de armazenamento. O desempenho que você obtém também é sobre como configurar cada um dos recursos em execução dentro do cluster kubernetes (incluindo a hiperescala do PostgreSQL habilitada para Arc do Azure), por exemplo, as solicitações e os limites definidos para a memória e vCore. A quantidade de carga de trabalho que você pode hospedar em um determinado cluster kubernetes é relativa às características do cluster kubernetes, a natureza das cargas de trabalho, o número de usuários, como as operações do cluster kubernetes são feitas...

## <a name="scale-out-aks"></a>Expandir AKS

Vamos demonstrar que dimensionar horizontalmente o cluster AKS e o servidor de hiperescala PostgreSQL habilitado para Arc do Azure é uma maneira de aproveitar ao máximo o alto desempenho da hiperescala do PostgreSQL habilitada para Arc do Azure.
Vamos adicionar um quinto nó ao cluster AKS:

:::row:::
    :::column:::
        Antes
    :::column-end:::
    :::column:::
        After (após)
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        :::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/6_layout_before.png" alt-text="Layout de portal do Azure antes de":::
    :::column-end:::
    :::column:::
        :::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/7_layout_after.png" alt-text="portal do Azure layout após":::
    :::column-end:::
:::row-end:::

A arquitetura é semelhante ao:

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/8_logical_layout_after.png" alt-text="Layout lógico no cluster kubernetes após a atualização":::

Vejamos o que o pods do namespace do controlador de dados Arc está hospedado no novo nó físico AKS executando o comando:

```console
kubectl describe node aks-agentpool-42715708-vmss000004
```

E vamos atualizar a representação da arquitetura do nosso sistema:

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/9_updated_list_of_pods.png" alt-text="Todos os pods no diagrama lógico do cluster":::

Podemos observar que o novo nó físico do cluster kubernetes está hospedando apenas o pod de métricas necessário para os serviços de dados de arco do Azure. Observe que, neste exemplo, estamos nos concentrando apenas no namespace do controlador de dados Arc, não estamos representando os outros pods.

## <a name="scale-out-azure-arc-enabled-postgresql-hyperscale-again"></a>Escalar horizontalmente habilitada para o Arc do Azure com o hiperescala do PostgreSQL

O quinto nó físico ainda não está hospedando nenhuma carga de trabalho. À medida que expandirmos a hiperescala do PostgreSQL habilitada para o Azure Arc, o kubernetes otimizará o posicionamento do novo pod PostgreSQL e não deverá colocar o local em nós físicos que já hospedam mais cargas de trabalho. Execute o comando a seguir para dimensionar a hiperescala do PostgreSQL habilitada para o Arc do Azure de 3 a 4 trabalhadores. No final da operação, o grupo de servidores será constituído e distribuído entre cinco instâncias do PostgreSQL, um coordenador e quatro trabalhadores.

```console
azdata arc postgres server edit --name postgres01 --workers 4
```

Que produz a seguinte saída:

```output
Updating postgres01 in namespace `arc3`
postgres01 is Ready
```

Liste os grupos de servidores implantados no controlador de dados e verifique se o grupo de servidores agora é executado com quatro trabalhadores:

```console
azdata arc postgres server list
```

Observe que ele foi dimensionado de três para quatro trabalhadores. 

```console
Name        State    Workers
----------  -------  ---------
postgres01  Ready    4
```

Como fizemos anteriormente, observe que o grupo de servidores agora usa quatro pods:

```output
kubectl get pods -n arc3

NAME                 READY   STATUS    RESTARTS   AGE
…
postgres01c-0         3/3     Running   0          13h
postgres01w-0         3/3     Running   0          13h
postgres01w-1         3/3     Running   0          13h
postgres01w-2         3/3     Running   0          179m
postgres01w-3         3/3     Running   0          3m13s
```

A forma do grupo de servidores agora é:

|Função de grupo de servidores|Pod do grupo de servidores
|----|-----
|Pelos|postgres01c-0
|Trabalho|postgres01w-0
|Trabalho|postgres01w-1
|Trabalho|postgres01w-2
|Trabalho|postgres01w-3

Vamos descrever o Pod postgres01w-3 para identificar em qual nó físico ele está hospedado:

```console
kubectl describe pod postgres01w-3 -n arc3
```

E observe em que pods ele é executado:

|Função de grupo de servidores|Pod do grupo de servidores| Pod
|----|-----|------
|Pelos|postgres01c-0|AKs-agentpool-42715708-vmss000000
|Trabalho|postgres01w-0|AKs-agentpool-42715708-vmss000002
|Trabalho|postgres01w-1|AKs-agentpool-42715708-vmss000003
|Trabalho|postgres01w-2|AKs-agentpool-42715708-vmss000000
|Trabalho|postgres01w-3|AKs-agentpool-42715708-vmss000004

E a arquitetura é semelhante a:

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/10_kubernetes_schedules_newest_pod.png" alt-text="Kubernetes agenda o Pod mais recente no nó com o menor uso":::

O kubernetes agendou o novo pod PostgreSQL no nó físico menos carregado do cluster kubernetes.

## <a name="summary"></a>Resumo

Para aproveitar ao máximo a escalabilidade e o desempenho do dimensionamento horizontal do grupo de servidores habilitados para Arc do Azure, você deve evitar a contenção de recursos dentro do cluster kubernetes:
- entre o grupo de servidores de hiperescala do PostgreSQL habilitado para o Azure Arc e outras cargas de trabalho hospedadas no mesmo cluster kubernetes
- entre todas as instâncias do PostgreSQL que constituem o grupo de servidores de hiperescala PostgreSQL habilitado para Arc do Azure

Você pode conseguir isso de várias maneiras:
- Dimensione horizontalmente o kubernetes e o Arc do Azure habilitados para postgres hiperescala: considere a possibilidade de dimensionar verticalmente o cluster kubernetes da mesma maneira que está dimensionando o grupo de servidores de hiperescala PostgreSQL habilitados para o Azure Arc. Adicione um nó físico ao cluster para cada trabalho que você adicionar ao grupo de servidores.
- Escalar horizontalmente o postgres de expansão habilitado para Arc do Azure sem escalar horizontalmente o kubernetes: definindo as restrições de recursos certas (solicitação e limites de memória e vCore) nas cargas de trabalho hospedadas em kubernetes (o Azure Arc habilitou o hiperescala do PostgreSQL incluído), você habilitará a colocação de cargas de trabalho no kubernetes e reduzirá o risco de contenção de recursos. Você precisa certificar-se de que as características físicas dos nós físicos do cluster kubernetes possam respeitar as restrições de recursos que você definir. Você também deve garantir que o equilíbrio permaneça à medida que as cargas de trabalho evoluem ao longo do tempo ou à medida que mais cargas de trabalho são adicionadas ao cluster kubernetes.
- Use os mecanismos de kubernetes (seletor de Pod, afinidade, antiafinidade) para influenciar o posicionamento do pods.

## <a name="next-steps"></a>Próximas etapas

[Escalar horizontalmente seu grupo de servidores de hiperescala PostgreSQL habilitado para o Azure Arc adicionando mais nós de trabalho](scale-out-postgresql-hyperscale-server-group.md)
