---
title: "Partição da CLI- sfctl do Service Fabric do Azure| Microsoft Docs"
description: "Descreve os comandos de partição da CLI- sfctl do Service Fabric."
services: service-fabric
documentationcenter: na
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: cli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 09/22/2017
ms.author: ryanwi
ms.openlocfilehash: a5d0ff59803212403281063f47e706433cee64b4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="sfctl-partition"></a>partição sfctl
Consultar e gerenciar partições para qualquer serviço.

## <a name="commands"></a>Comandos

|Command|Descrição|
| --- | --- |
|    data-loss      | Essa API induz a perda de dados para a partição especificada.|
|    data-loss-status  | Obtém o progresso de uma operação de perda de dados de partição iniciada, utilizando a API StartDataLoss.|
|    health         | Obtém a integridade da partição do Service Fabric especificado.|
|    informações           | Obtém as informações sobre uma partição do Service Fabric.|
|    list           | Obtém a lista de partições de um serviço do Service Fabric.|
|    load           | Obtém a carga da partição do Service Fabric especificado.|
|    load-reset     | Redefine a carga atual de uma partição do Service Fabric.|
|    quorum-loss    | Induz a perda de quorum de uma determinada partição de serviço com estado.|
|    quorum-loss-status| Obtém o progresso de uma operação de perda de quorum em uma partição iniciada utilizando a API StartQuorumLoss.|
|    recover        | Indica ao cluster do Service Fabric que ele deve tentar recuperar uma partição específica, que atualmente está paralisada na perda de quorum.|
|    recover-all    | Indica ao cluster do Service Fabric que ele deve tentar recuperar quaisquer serviços (incluindo serviços do sistema) que estão atualmente paralisados na perda de quorum.|
|    report-health  | Envia um relatório de integridade na partição do Service Fabric.|
|    restart        | Essa API reinicia algumas ou todas as réplicas ou as instâncias da partição especificada.|
|    restart-status | Obtém o progresso de uma operação PartitionRestart iniciada utilizando StartPartitionRestart.|
|    svc-name       | Obtém o nome do serviço do Service Fabric para uma partição.|


## <a name="sfctl-partition-health"></a>sfctl partition health
Obtém a integridade da partição do Service Fabric especificado.

Obtém as informações de integridade da partição especificada. Utilize EventsHealthStateFilter para filtrar a coleção de eventos de integridade relatados no serviço com base no estado de integridade.
Utilize ReplicasHealthStateFilter para filtrar a coleção dos objetos ReplicaHealthState na partição. Se você especificar um serviço que não existe no repositório de integridade, esse cmdlet retornará um erro. .

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --partition-id      [Obrigatório]| A identidade da partição.|
| --events-health-state-filter  | Permite filtrar a coleção de objetos HealthEvent retornados com base no estado de integridade. Os valores possíveis para esse parâmetro incluem o valor de inteiro de um dos seguintes estados de integridade.                Somente os eventos que correspondem ao filtro são retornados. Todos os eventos são usados para avaliar o estado de integridade agregado. Se não for especificado, todas as entradas retornarão. Os valores de estado são uma enumeração baseada em sinalizador, então o valor pode ser uma combinação desses valores obtidos usando o operador 'OR' bit a bit. Por exemplo, se o valor fornecido for 6, todos os eventos com o valor de HealthState de OK (2) e de Aviso (4) retornarão. - Default -                Valor padrão. Corresponde a qualquer HealthState. O valor é zero. -                None - Filtro que não corresponde a nenhum valor de HealthState. Usado para não retornar qualquer resultado em uma determinada coleção de estados. O valor é 1. - Ok - Filtro que corresponde à entrada com o valor de HealthState Ok. O valor é 2. - Warning - Filtro que corresponde à entrada com o valor de HealthState Warning. O valor é 4. - Error - Filtro que corresponde à entrada com o valor Error de HealthState. O valor é 8.                - All - Filtro que corresponde à entrada com qualquer valor de HealthState.                O valor é 65535.|
|--exclude-health-statistics   | Indica se as estatísticas de integridade devem ser retornadas como parte do resultado da consulta. Falso por padrão. As estatísticas mostram o número de entidades filhas no estado de integridade OK, Warning e Error.|
| --replicas-health-state-filter| Utilize ReplicasHealthStateFilter para filtrar a coleção dos objetos ReplicaHealthState na partição. O valor pode ser obtido de membros ou operações bit a bit em membros de HealthStateFilter. Somente as réplicas que correspondem ao filtro são retornadas. Todas as réplicas são usadas para avaliar o estado de integridade agregado. Se não for especificado, todas as entradas retornarão. Os valores de estado são uma enumeração baseada em sinalizador, então o valor pode ser uma combinação desses valores obtidos usando o operador 'OR' bit a bit. Por exemplo, se o valor fornecido for 6, todos os eventos com o valor de HealthState de OK (2) e de Aviso (4) retornarão. Os valores possíveis para esse parâmetro incluem o valor de inteiro de um dos seguintes estados de integridade. - Default - Valor padrão. Corresponde a qualquer HealthState. O valor é zero. - None - Filtro que não corresponde a nenhum valor de HealthState. Usado para não retornar qualquer resultado em uma determinada coleção de estados. O valor é 1. - Ok - Filtro que corresponde à entrada com o valor de HealthState Ok. O valor é 2. - Warning - Filtro que corresponde à entrada com o valor Warning de HealthState. O valor é 4. - Error - Filtro que corresponde à entrada com o valor Error de HealthState. O valor é 8. - All - Filtro que corresponde à entrada com qualquer valor de HealthState. O valor é 65535.|
| --timeout -t               | Tempo limite do servidor em segundos.  Padrão: 60.|

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --debug                    | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração.|
| --help -h                  | Mostrar esta mensagem de ajuda e sair.|
| --output -o                | O formato da saída.  Valores permitidos: json, jsonc, table, tsv.                Padrão: json.|
| --query                    | Cadeia de caracteres de consulta JMESPath. Para saber mais e obter exemplos, consulte http://jmespath.org/. |
| --verbose                  | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos.|

## <a name="sfctl-partition-info"></a>sfctl partition info
Obtém as informações sobre uma partição do Service Fabric.

O ponto de extremidade das Partições retorna informações sobre a partição especificada. A resposta inclui a ID da partição, informações do esquema de particionamento, chaves suportadas pela partição, status, integridade e outros detalhes sobre a partição.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --partition-id [Obrigatório]| A identidade da partição.|
| --timeout -t          | Tempo limite do servidor em segundos.  Padrão: 60.|

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --debug               | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração.|
| --help -h             | Mostrar esta mensagem de ajuda e sair.|
| --output -o           | O formato da saída.  Valores permitidos: json, jsonc, table, tsv.  Padrão:           json.|
| --query               | Cadeia de caracteres de consulta JMESPath. Para saber mais e obter exemplos, consulte http://jmespath.org/.|
| --verbose             | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos.|

## <a name="sfctl-partition-list"></a>sfctl partition list
Obtém a lista de partições de um serviço do Service Fabric.

Obtém a lista de partições de um serviço do Service Fabric. As ID da partição, informações do esquema de particionamento, chaves suportadas pela partição, status, integridade e outros detalhes sobre a partição.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --service-id [Obrigatório]| A identidade do serviço. Normalmente é o nome completo do serviço sem o esquema de URI "fabric:". A partir da versão 6.0, nomes hierárquicos são delimitados pelo caractere "~". Por exemplo, se o nome do serviço for fabric://myapp/app1/svc1, a identidade do serviço deverá ser "myapp~app1~svc1" em 6.0+ e "myapp/app1/svc1" em versões anteriores.|
| --continuation-token| O parâmetro de token de continuação é usado para obter o próximo conjunto de resultados.         Um token de continuação com um valor não vazio será incluído na resposta da API quando os resultados do sistema não se ajustarem em uma única resposta. Quando esse valor for passado para a próxima chamada de API, a API retornará o próximo conjunto de resultados. Se não houver mais resultados, então, o token de continuação não conterá um valor. O valor desse parâmetro não deve ser codificados em URL.|
| --timeout -t        | Tempo limite do servidor em segundos.  Padrão: 60.|

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --debug             | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração.|
| --help -h           | Mostrar esta mensagem de ajuda e sair.|
| --output -o         | O formato da saída.  Valores permitidos: json, jsonc, table, tsv.  Padrão:         json.|
| --query             | Cadeia de caracteres de consulta JMESPath. Para saber mais e obter exemplos, consulte http://jmespath.org/.|
| --verbose           | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos.|

## <a name="sfctl-partition-load"></a>sfctl partition load
Obtém a carga da partição do Service Fabric especificado.

Retorna informações sobre a partição especificada. A resposta inclui uma lista de informações de carga. Cada informação inclui o nome da métrica de carga, o valor e o último tempo registrado em UTC. .

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --partition-id [Obrigatório]| A identidade da partição.|
| --timeout -t          | Tempo limite do servidor em segundos.  Padrão: 60.|

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --debug               | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração.|
| --help -h             | Mostrar esta mensagem de ajuda e sair.|
| --output -o           | O formato da saída.  Valores permitidos: json, jsonc, table, tsv.  Padrão:           json.|
| --query               | Cadeia de caracteres de consulta JMESPath. Para saber mais e obter exemplos, consulte http://jmespath.org/.|
| --verbose             | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos.|

## <a name="sfctl-partition-recover"></a>sfctl partition recover
Indica ao cluster do Service Fabric que deve tentar recuperar uma partição específica que está atualmente paralisada na perda de quorum.

Indica ao cluster do Service Fabric que deve tentar recuperar uma partição específica que está atualmente paralisada na perda de quorum. Esta operação somente deve ser realizada se souber que as réplicas inativas não podem ser recuperadas. O uso incorreto dessa API pode causar possíveis perdas de dados.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --partition-id [Obrigatório]| A identidade da partição.|
| --timeout -t          | Tempo limite do servidor em segundos.  Padrão: 60.|

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --debug               | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração.|
| --help -h             | Mostrar esta mensagem de ajuda e sair.|
| --output -o           | O formato da saída.  Valores permitidos: json, jsonc, table, tsv.  Padrão:           json.|
| --query               | Cadeia de caracteres de consulta JMESPath. Para saber mais e obter exemplos, consulte http://jmespath.org/.|
| --verbose             | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos.|

## <a name="sfctl-partition-restart"></a>sfctl partition restart
Essa API reinicia algumas ou todas as réplicas ou as instâncias da partição especificada.

Essa API é útil para teste de failover. Se usada para segmentar uma partição de serviço sem estado, RestartPartitionMode deverá ser AllReplicasOrInstances. Chame a API de GetPartitionRestartProgress utilizando a mesma OperationId para obter o progresso. .

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --operation-id           [Obrigatório]| Um GUID que identifica uma chamada dessa API.  Isso é passado para a API GetProgress correspondente.|
| --partition-id           [Obrigatório]| A identidade da partição.|
| --restart-partition-mode [Obrigatório]| - Invalid - Reserved.  Não passe para a API. - AllReplicasOrInstance - todas as réplicas ou instâncias na partição são reiniciadas uma vez. - OnlyActiveSecondaries - Apenas as réplicas secundárias são reiniciadas. .|
| --service-id             [Obrigatório]| A identidade do serviço. Normalmente é o nome completo do serviço sem o esquema de URI 'fabric:'. A partir da versão 6.0, nomes hierárquicos são delimitados pelo caractere "~". Por exemplo, se o nome do serviço for "fabric://myapp/app1/svc1", a identidade do serviço deverá ser "myapp~app1~svc1" em 6.0+ e "myapp/app1/svc1" em versões anteriores.|
| --timeout -t                    | Tempo limite do servidor em segundos.  Padrão: 60.|

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --debug                         | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração.|
| --help -h                       | Mostrar esta mensagem de ajuda e sair.|
| --output -o                     | O formato da saída.  Valores permitidos: json, jsonc, table, tsv.                     Padrão: json.|
| --query                         | Cadeia de caracteres de consulta JMESPath. Para saber mais e obter exemplos, consulte http://jmespath.org/.|
| --verbose                       | Aumentar o nível de detalhes do log. Use --debug para todos os logs de depuração.|

## <a name="next-steps"></a>Próximas etapas
- [Configurar](service-fabric-cli.md) a CLI do Service Fabric.
- Saiba como usar a CLI do Service Fabric usando os [scripts de exemplo](/azure/service-fabric/scripts/sfctl-upgrade-application).