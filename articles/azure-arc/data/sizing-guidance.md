---
title: Diretrizes de dimensionamento
description: Planeje o tamanho de uma implantação dos serviços de dados habilitados para Arc do Azure.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 6e7f2e445c3e4e8df7420c0587e156968f3a2c92
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94542670"
---
# <a name="sizing-guidance"></a>Diretrizes de dimensionamento

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="overview-of-sizing-guidance"></a>Visão geral das diretrizes de dimensionamento

Ao planejar a implantação de serviços de dados de arco do Azure, você deve planejar a quantidade correta de computação, memória e armazenamento que será necessária para executar o controlador de dados de arco do Azure e para o número de grupos de servidores de hiperescala do PostgreSQL e de instâncias gerenciadas pelo SQL que serão implantados.  Como os serviços de dados habilitados para Arc do Azure são implantados no kubernetes, você tem a flexibilidade de adicionar mais capacidade ao cluster kubernetes ao longo do tempo, adicionando mais nós de computação ou armazenamento.  Este guia fornecerá orientação sobre os requisitos mínimos e fornecerá orientação sobre os tamanhos recomendados para alguns requisitos comuns.

## <a name="general-sizing-requirements"></a>Requisitos gerais de dimensionamento

> [!NOTE]
> Se você não estiver familiarizado com os conceitos deste artigo, poderá ler mais sobre a [kubernetes de recursos](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/) e a [notação de tamanho de kubernetes](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/#resource-units-in-kubernetes).

Os números de núcleos devem ser um valor inteiro maior ou igual a um.

Ao usar azdata para implantação, os valores de memória devem ser especificados em uma potência de dois números, ou seja, usando os sufixos: Ki, mi ou GI.

Os valores de limite devem ser sempre maiores do que o valor da solicitação, se especificado.

Os valores de limite para os núcleos são a métrica Faturável na instância gerenciada do SQL e nos grupos de servidores de hiperescala PostgreSQL.

## <a name="minimum-deployment-requirements"></a>Requisitos mínimos de implantação

Um tamanho mínimo o Azure Arc habilitado para implantação de serviços de dados pode ser considerado o controlador de dados de arco do Azure, além de uma instância gerenciada do SQL, além de um grupo de servidores de hiperescala PostgreSQL com dois nós de trabalho.  Para essa configuração, você precisa de pelo menos 16 GB de RAM e 4 núcleos de capacidade _disponível_ no cluster kubernetes.  Você deve verificar se tem um tamanho mínimo de nó kubernetes de 8 GB de RAM e 4 núcleos e uma capacidade total de soma de 16 GB de RAM disponível em todos os nós kubernetes.  Por exemplo, você poderia ter 1 nó a 32 GB de RAM e 4 núcleos ou poderia ter 2 nós com 16 GB RAM e 4 núcleos cada.

Consulte o artigo [armazenamento-configuração](storage-configuration.md) para obter detalhes sobre o dimensionamento de armazenamento.

## <a name="data-controller-sizing-details"></a>Detalhes de dimensionamento do controlador de dados

O controlador de dados é uma coleção de pods que são implantados em seu cluster kubernetes para fornecer uma API, o serviço do controlador, o bootstrapper e os bancos de dados e painéis de monitoramento.  Esta tabela descreve os valores padrão para solicitações e limites de memória e CPU.

|Nome do pod|Solicitação de CPU|Solicitação de memória|Limite da CPU|Limite de Memória|Observações|
|---|---|---|---|---|---|
|**bootstrapper**|100|100 ms|200m|200Mi||
|**control**|400m|2Gi|1800m|2Gi||
|**controldb**|200m|3Gi|800m|6Gi||
|**controlwd**|10 milhões|100 ms|100|200Mi||
|**logsdb**|200m|1600Mi|2|1600Mi||
|**logsui**|100|500 milhões|2|2Gi||
|**metricsdb**|200m|800Mi|400m|2Gi||
|**metricsdc**|100|200Mi|200m|300Mi|Metricsdc é um daemonset que é criado em cada um dos nós kubernetes em seu cluster.  Os números na tabela aqui são _por nó_. Se você definir allowNodeMetricsCollection = false em seu arquivo de perfil de implantação antes de criar o controlador de dados, o daemonset metricsdc não será criado.|
|**metricsui**|20 milhões|200Mi|2.500 MB|200Mi||
|**mgmtproxy**|200m|250Mi|2.500 MB|500 milhões||

Você pode substituir as configurações padrão para o controldb e o pods de controle no arquivo de perfil de implantação ou no arquivo YAML do datacontroller.  Exemplo:

```yaml
  resources:
    controller:
      limits:
        cpu: "1000m"
        memory: "3Gi"
      requests:
        cpu: "800m"
        memory: "2Gi"
    controllerDb:
      limits:
        cpu: "800m"
        memory: "8Gi"
      requests:
        cpu: "200m"
        memory: "4Gi"
```

Consulte o artigo [armazenamento-configuração](storage-configuration.md) para obter detalhes sobre o dimensionamento de armazenamento.

## <a name="sql-managed-instance-sizing-details"></a>Detalhes de dimensionamento da instância gerenciada do SQL

Cada instância gerenciada do SQL deve ter as seguintes solicitações de recursos mínimos:
- Memória: 2Gi
- Núcleos: 1

Cada pod de instância gerenciada do SQL criado tem três contêineres:

|Nome do contêiner|Solicitação de CPU|Solicitação de memória|Limite da CPU|Limite de Memória|Observações|
|---|---|---|---|---|---|
|fluentbit|100|100 ms|Não especificado|Não especificado|As solicitações de recurso de contêiner fluentbit são _além_ das solicitações especificadas para a instância gerenciada do SQL.||
|arco-sqlmi|Especificado pelo usuário ou não especificado.|Especificado pelo usuário ou não especificado.|Especificado pelo usuário ou não especificado.|Especificado pelo usuário ou não especificado.||
|collectd|Não especificado|Não especificado|Não especificado|Não especificado||

O tamanho do volume padrão para todos os volumes persistentes é 5Gi.

## <a name="postgresql-hyperscale-server-group-sizing-details"></a>Detalhes de dimensionamento do grupo de servidores de hiperescala PostgreSQL

Cada nó do grupo de servidores de hiperescala PostgreSQL deve ter as seguintes solicitações de recursos mínimas:
- Memória: 256Mi
- Núcleos: 1

Cada coordenador de grupo de servidores de hiperescala do PostgreSQL ou pod de trabalho criado tem três contêineres:

|Nome do contêiner|Solicitação de CPU|Solicitação de memória|Limite da CPU|Limite de Memória|Observações|
|---|---|---|---|---|---|
|fluentbit|100|100 ms|Não especificado|Não especificado|As solicitações de recurso de contêiner fluentbit são _além_ das solicitações especificadas para os nós do grupo de servidores de hiperescala PostgreSQL.|
|postgres|Especificado pelo usuário ou não especificado.|Usuário especificado ou 256Mi (padrão).|Especificado pelo usuário ou não especificado.|Especificado pelo usuário ou não especificado.||
|Telegraf|Não especificado|Não especificado|Não especificado|Não especificado||

## <a name="cumulative-sizing"></a>Dimensionamento cumulativo

O tamanho geral de um ambiente necessário para os serviços de dados habilitados para Arc do Azure é principalmente uma função do número e do tamanho das instâncias de banco de dado que serão criadas.  O tamanho geral pode ser difícil de prever antecipadamente, sabendo que o número de instâncias aumentará e diminuirá e a quantidade de recursos necessários para cada instância do banco de dados será alterada.

O tamanho da linha de base para um determinado ambiente de serviços de dados habilitados para o Azure Arc é o tamanho do controlador de dados que requer 4 núcleos e 16 GB de RAM.  A partir daí, você pode adicionar o total cumulativo de núcleos e memória necessários para as instâncias de banco de dados.  Para a instância gerenciada do SQL, o número de pods é igual ao número de instâncias gerenciadas do SQL que são criadas.  Para grupos de servidores de hiperescala do PostgreSQL, o número de pods é equivalente ao número de nós de trabalho mais um para o nó de coordenador.  Por exemplo, se você tiver um grupo de servidores PostgreSQL com 3 nós de trabalho, o número total de pods será 4.

Além dos núcleos e da memória que você solicita para cada instância de banco de dados, você deve adicionar 250m de núcleos e 250Mi de RAM para os contêineres de agente.

Veja a seguir um exemplo de cálculo de dimensionamento.

Requisitos:

- **"SQL1"**: 1 instância gerenciada do SQL com 16 GB de RAM, 4 núcleos
- **"Sql2"**: 1 instância gerenciada do SQL com 256 GB de RAM, 16 núcleos
- **"Postgres1"**: 1 grupo de servidores de hiperescala PostgreSQL com 4 trabalhadores a 12 GB de RAM, 4 núcleos

Cálculos de dimensionamento:

- O tamanho de "SQL1" é: 1 Pod * ([16 GI RAM, 4 núcleos] + [250Mi RAM, 250m núcleos]) para os agentes por Pod = 16,25 GI RAM, 4,25 núcleos.
- O tamanho de "SQL2" é: 1 Pod * ([256 GI RAM, 16 núcleos] + + [250Mi RAM, 250m núcleos]) para os agentes por Pod = 256,25 GI RAM, 16,25 núcleos.
- O tamanho total do SQL 1 e do SQL 2 é: (16,25 GB + 256,25 GI) = 272,5 GB de RAM, (4,25 núcleos + 16,25 núcleos) = 20,5 núcleos.

- O tamanho de "Postgres1" é: (4 os pods de operador + 1 pod de coordenador) * ([12 GB de RAM, 4 núcleos] + [250Mi de RAM, 250m núcleos]) para os agentes por Pod = 61,25 GB de RAM, 21,25 núcleos.

- A capacidade total necessária para as instâncias de banco de dados é: 272,5 GB de RAM, 20,5 núcleos para SQL + 61,25 GB de RAM, 21,25 núcleos para PostgreSQL hiperscale = 333,75 GB de RAM, 42,5 núcleos.

- A capacidade total necessária para as instâncias de banco de dados mais o controlador de dado é: 333,75 GB de RAM, 42,5 núcleos para as instâncias de banco de dados + 16 GB de RAM, 4 núcleos para o controlador de dados = 349,75 GB de RAM, 46,5 núcleos.

Consulte o artigo [armazenamento-configuração](storage-configuration.md) para obter detalhes sobre o dimensionamento de armazenamento.

## <a name="other-considerations"></a>Outras considerações

Tenha em mente que uma determinada solicitação de tamanho de instância de banco de dados para núcleos ou RAM não pode exceder a capacidade disponível dos nós kubernetes no cluster.  Por exemplo, se o maior nó kubernetes que você tem em seu cluster kubernetes for de 256 GB de RAM e 24 núcleos, você não poderá criar uma instância de banco de dados com uma solicitação de 512 GB de RAM e 48 núcleos.  

É uma boa ideia manter pelo menos 25% da capacidade disponível nos nós do kubernetes para permitir que kubernetes Agende com eficiência os pods a serem criados e permita o dimensionamento elástico e o crescimento de prazo mais longo sob demanda.  

Em seus cálculos de dimensionamento, não se esqueça de adicionar os requisitos de recursos do pods do sistema kubernetes e quaisquer outras cargas de trabalho que possam estar compartilhando capacidade com os serviços de dados habilitados para Arc do Azure no mesmo cluster kubernetes.

Para manter a alta disponibilidade durante a manutenção planejada e a continuidade de desastres, você deve planejar pelo menos um dos nós kubernetes no cluster para ficar indisponível em qualquer momento determinado.  O kubernetes tentará reagendar os pods que estavam em execução em um determinado nó que foi desativado para manutenção ou devido a uma falha.  Se não houver capacidade disponível nos nós restantes, esses pods não serão reagendados para criação até que haja a capacidade disponível novamente.  Tenha cuidado extra com instâncias de banco de dados grandes.  Por exemplo, se houver apenas um nó kubernetes grande o suficiente para atender aos requisitos de recursos de uma instância de banco de dados grande e esse nó falhar, o kubernetes não será capaz de agendar o Pod da instância do banco de dados em outro nó do kubernetes.

Mantenha os [limites máximos para um tamanho de cluster kubernetes](https://kubernetes.io/docs/setup/best-practices/cluster-large/) em mente.

O administrador do kubernetes pode ter configurado [cotas de recursos](https://kubernetes.io/docs/concepts/policy/resource-quotas/) em seu namespace/projeto.  Tenha isso em mente ao planejar seus tamanhos de instância de banco de dados.
