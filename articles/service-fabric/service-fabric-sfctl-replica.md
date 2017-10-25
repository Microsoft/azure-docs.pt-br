---
title: "CLI do Azure Service Fabric - réplica sfctl | Microsoft Docs"
description: "Descreve os comandos da réplica sfctl da CLI do Service Fabric."
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
ms.openlocfilehash: 50d185294131e42aaf8b541ab17495fcb40c89dd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="sfctl-replica"></a>réplica sfctl
Gerenciar as réplicas que pertencem a partições de serviço.

## <a name="commands"></a>Comandos

|Command|Descrição|
| --- | --- |
|    deployed  | Obtém os detalhes da réplica implantada em um nó do Service Fabric.|
|    deployed-list| Obtém a lista de réplicas implantadas em um nó do Service Fabric.|
|    health    | Obtém a integridade de uma réplica de instância de serviço com estado ou de serviço sem estado do Service Fabric
                   do Service Fabric.|
|    informações      | Obtém as informações sobre uma réplica de uma partição do Service Fabric.|
|    list      | Obtém as informações sobre réplicas de uma partição de serviço do Service Fabric.|
|    remove    | Remove uma réplica de serviço em execução em um nó.|
|    report-health| Envia um relatório de integridade sobre a réplica do Service Fabric.|
|    restart   | Reinicia uma réplica de serviço de um serviço persistente em execução em um nó.|


## <a name="sfctl-replica-deployed"></a>sfctl replica deployed
Obtém os detalhes da réplica implantada em um nó do Service Fabric.

Obtém os detalhes da réplica implantada em um nó do Service Fabric. As informações incluem o tipo de serviço, o nome do serviço, a operação do serviço atual, a data e a hora de início da operação de serviço atual, a ID da partição, a ID da instância/réplica, a carga relatada e outras informações.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --node-name                [Obrigatório]| O nome do nó.|
| --partition-id [Obrigatório]| A identidade da partição.|
| --replica-id   [Obrigatório]| O identificador da réplica.|
| --timeout -t          | Tempo limite do servidor em segundos.  Padrão: 60.|

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --debug               | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração.|
| --help -h             | Mostrar esta mensagem de ajuda e sair.|
| --output -o           | O formato da saída.  Valores permitidos: json, jsonc, table, tsv.  Padrão:           json.|
| --query               | Cadeia de consulta JMESPath. Para saber mais e obter exemplos, consulte http://jmespath.org/.|
| --verbose             | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos.|

## <a name="sfctl-replica-health"></a>sfctl replica health
Obtém a integridade de uma réplica de instância de serviço com estado ou de serviço sem estado do Service Fabric.

Obtém a integridade de uma réplica do Service Fabric. Use EventsHealthStateFilter para filtrar o conjunto de eventos de integridade relatados na réplica de acordo com o estado de integridade. .

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --partition-id [Obrigatório]| A identidade da partição.|
| --replica-id   [Obrigatório]| O identificador da réplica.|
| --events-health-state-filter| Permite filtrar o conjunto de objetos HealthEvent retornado com base no estado de integridade. Os valores possíveis para esse parâmetro incluem o valor de inteiro de um dos seguintes estados de integridade. Só retornam os eventos que correspondem ao filtro. Todos os eventos são usados para avaliar o estado de integridade agregado. Se não for especificado, retorna todas as entradas. Os valores de estado são uma enumeração baseada em sinalizador, então o valor pode ser uma combinação desses valores obtidos usando o operador 'OR' bit a bit. Por exemplo, se o valor fornecido for 6, serão retornados todos os eventos com o valor de HealthState de OK (2) e de Aviso (4). -Default- Valor padrão. Corresponde a qualquer HealthState. O valor é zero. -None- Filtro que não corresponde a qualquer valor de HealthState. Usado para não retornar qualquer resultado em um determinado conjunto de estados. O valor é 1. -Ok- Filtro que corresponde à entrada com o valor de HealthState Ok. O valor é 2. -Warning- Filtro que corresponde à entrada com o valor de HealthState Warning. O valor é 4. -Error- Filtro que corresponde à entrada com o valor de HealthState Error. O valor é 8. -All- Filtro que corresponde à entrada com qualquer valor de HealthState. O valor é 65535.|
| --timeout -t             | Tempo limite do servidor em segundos.  Padrão: 60.|

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --debug                  | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração.|
| --help -h                | Mostrar esta mensagem de ajuda e sair.|
| --output -o              | O formato da saída.  Valores permitidos: json, jsonc, table, tsv.  Padrão: json.|
| --query                  | Cadeia de consulta JMESPath. Consulte http://jmespath.org/ para saber mais e obter exemplos.|
| --verbose                | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos.|

## <a name="sfctl-replica-info"></a>sfctl replica info
Obtém as informações sobre uma réplica de uma partição do Service Fabric.

A resposta inclui a ID, a função, o status, a integridade, o nome do nó, o tempo de atividade e outros detalhes sobre a réplica.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --partition-id [Obrigatório]| A identidade da partição.|
| --replica-id   [Obrigatório]| O identificador da réplica.|
| --continuation-token  | O parâmetro de token de continuação é usado para obter o próximo conjunto de resultados. Um token de continuação com um valor não vazio será incluso na resposta da API quando os resultados do sistema não couberem em uma única resposta. Quando esse valor for passado para a próxima chamada de API, a API retornará o próximo conjunto de resultados. Se não houver mais resultados, o token de continuação não conterá um valor. O valor desse parâmetro não deve ser codificado em URL.|
| --timeout -t          | Tempo limite do servidor em segundos.  Padrão: 60.|

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --debug               | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração.|
| --help -h             | Mostrar esta mensagem de ajuda e sair.|
| --output -o           | O formato da saída.  Valores permitidos: json, jsonc, table, tsv.  Padrão:           json.|
| --query               | Cadeia de consulta JMESPath. Consulte http://jmespath.org/ para saber mais e obter exemplos.|
| --verbose             | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos.|

## <a name="sfctl-replica-list"></a>sfctl replica list
Obtém as informações sobre réplicas de uma partição de serviço do Service Fabric.

O ponto de extremidade GetReplicas retorna informações sobre as réplicas da partição especificada.
A resposta inclui a ID, a função, o status, a integridade, o nome do nó, o tempo de atividade e outros detalhes sobre a réplica.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --partition-id [Obrigatório]| A identidade da partição.|
| --continuation-token  | O parâmetro de token de continuação é usado para obter o próximo conjunto de resultados. Um token de continuação com um valor não vazio será incluso na resposta da API quando os resultados do sistema não couberem em uma única resposta. Quando esse valor for passado para a próxima chamada de API, a API retornará o próximo conjunto de resultados. Se não houver mais resultados, o token de continuação não conterá um valor. O valor desse parâmetro não deve ser codificado em URL.|
| --timeout -t          | Tempo limite do servidor em segundos.  Padrão: 60.|

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --debug               | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração.|
| --help -h             | Mostrar esta mensagem de ajuda e sair.|
| --output -o           | O formato da saída.  Valores permitidos: json, jsonc, table, tsv.  Padrão:           json.|
| --query               | Cadeia de consulta JMESPath. Consulte http://jmespath.org/ para saber mais e obter exemplos.|
| --verbose             | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos.|

## <a name="sfctl-replica-remove"></a>sfctl replica remove
Remove uma réplica de serviço em execução em um nó.

Essa API simula uma falha de réplica do Service Fabric removendo uma réplica de um cluster do Service Fabric. A remoção fecha a réplica, faz a transição da réplica para a função None e, em seguida, remove todas as informações de estado da réplica do cluster. Essa API testa o caminho de remoção de estado da réplica e simula o caminho permanente de falha de relatório por meio de APIs de cliente. Aviso: nenhuma verificação de segurança é executada ao usar essa API. O uso incorreto dessa API pode levar à perda de dados para serviços com estado. Além disso, o sinalizador forceRemove afeta todas as outras réplicas hospedadas no mesmo processo.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --node-name                [Obrigatório]| O nome do nó.|
| --partition-id [Obrigatório]| A identidade da partição.|
| --replica-id   [Obrigatório]| O identificador da réplica.|
| --force-remove        | Força a remoção de um aplicativo ou serviço do Service Fabric sem passar pela sequência de desligamento normal. Esse parâmetro pode ser usado para forçar a exclusão de um aplicativo ou serviço para o qual a exclusão está atingindo o tempo limite devido a problemas no código do serviço que impedem o fechamento normal das réplicas.|
| --timeout -t          | Tempo limite do servidor em segundos.  Padrão: 60.|

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --debug               | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração.|
| --help -h             | Mostrar esta mensagem de ajuda e sair.|
| --output -o           | O formato da saída.  Valores permitidos: json, jsonc, table, tsv.  Padrão:           json.|
| --query               | Cadeia de consulta JMESPath. Consulte http://jmespath.org/ para saber mais e obter exemplos.|
| --verbose             | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos.|

## <a name="sfctl-replica-restart"></a>sfctl replica restart
Reinicia uma réplica de serviço de um serviço persistente em execução em um nó.

Reinicia uma réplica de serviço de um serviço persistente em execução em um nó. Aviso: nenhuma verificação de segurança é executada ao usar essa API. O uso incorreto dessa API pode levar à perda de disponibilidade para serviços com estado.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --node-name                [Obrigatório]| O nome do nó.|
| --partition-id [Obrigatório]| A identidade da partição.|
| --replica-id   [Obrigatório]| O identificador da réplica.|
| --timeout -t          | Tempo limite do servidor em segundos.  Padrão: 60.|

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --debug               | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração.|
| --help -h             | Mostrar esta mensagem de ajuda e sair.|
| --output -o           | O formato da saída.  Valores permitidos: json, jsonc, table, tsv.  Padrão:           json.|
| --query               | Cadeia de consulta JMESPath. Consulte http://jmespath.org/ para saber mais e obter exemplos.|
| --verbose             | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos.|

## <a name="next-steps"></a>Próximas etapas
- [Configurar](service-fabric-cli.md) a CLI do Service Fabric.
- Saiba como usar a CLI do Service Fabric usando os [scripts de exemplo](/azure/service-fabric/scripts/sfctl-upgrade-application).