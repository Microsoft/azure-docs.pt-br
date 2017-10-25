---
title: "CLI do Azure Service Fabric - nó sfctl | Microsoft Docs"
description: "Descreve os comandos do nó sfctl da CLI do Service Fabric."
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
ms.openlocfilehash: 76037c7b4a2f7ada314a9360e3990245e6fbc06c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="sfctl-node"></a>Nó sfctl
Gerenciar os nós que formam um cluster.

## <a name="commands"></a>Comandos

|Command|Descrição|
| --- | --- |
|    disable       | Desativa um nó de cluster do Service Fabric com a intenção de desativação especificada.|
|    enable        | Ativa um nó de cluster do Service Fabric desativado no momento.|
|    health        | Obtém a integridade de um nó do Service Fabric.|
|    informações          | Obtém a lista de nós no cluster do Service Fabric.|
|    list          | Obtém a lista de nós no cluster do Service Fabric.|
|    load          | Obtém as informações de carregamento de um nó do Service Fabric.|
|    remove-state  | Notifica o Service Fabric que o estado persistente em um nó foi permanentemente removido ou perdido.|
|    report-health | Envia um relatório de integridade sobre o nó do Service Fabric.|
|    restart       | Reinicia um nó de cluster do Service Fabric.|
|    transition    | Inicia ou interrompe um nó de cluster.|
|    transition-status| Obtém o progresso de uma operação iniciada usando StartNodeTransition.|


## <a name="sfctl-node-disable"></a>sfctl node disable
Desativa um nó de cluster do Service Fabric com a intenção de desativação especificada.

Desativa um nó de cluster do Service Fabric com a intenção de desativação especificada. Quando a desativação estiver em progresso, a intenção de desativação poderá ser aumentada, mas não reduzida (por exemplo, um nó que está desativado com a intenção Pause pode ser desativado com Restart, mas não o contrário). Os nós podem ser reativados usando a operação Ativar um nó a qualquer momento após a desativação. Se a desativação não estiver concluída, isso cancela a desativação. Um nó desativado e que volta a funcionar enquanto está desativado ainda precisa ser reativado antes que os serviços possam ser colocados nesse nó.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --node-name [Obrigatório]| O nome do nó.|
| --deactivation-intent | Descreve a intenção ou o motivo da desativação do nó. Os valores possíveis são os seguintes. -Pause- Indica se o nó deve ser pausado. O valor é 1. -Restart- Indica que a intenção é a reinicialização do nó após um curto período de tempo. O valor é 2. -RemoveData- Indica que a intenção para o nó é a remoção dos dados. O valor é 3. .|
| --timeout -t       | Tempo limite do servidor em segundos.  Padrão: 60.|

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --debug            | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração.|
| --help -h          | Mostrar esta mensagem de ajuda e sair.|
| --output -o        | O formato da saída.  Valores permitidos: json, jsonc, table, tsv.  Padrão: json.|
| --query            | Cadeia de caracteres de consulta JMESPath. Para saber mais e obter exemplos, consulte http://jmespath.org/.|
| --verbose          | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos.|

## <a name="sfctl-node-enable"></a>sfctl node enable
Ativar um nó de cluster do Service Fabric desativado no momento.

Ativa um nó de cluster do Service Fabric desativado no momento. Quando ativado, o nó se torna novamente um destino viável para o posicionamento de novas réplicas, e todas as réplicas desativadas restantes no nó serão reativadas.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --node-name [Obrigatório]| O nome do nó.|
| --timeout -t       | Tempo limite do servidor em segundos.  Padrão: 60.|

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --debug            | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração.|
| --help -h          | Mostrar esta mensagem de ajuda e sair.|
| --output -o        | O formato da saída.  Valores permitidos: json, jsonc, table, tsv.  Padrão: json.|
| --query            | Cadeia de caracteres de consulta JMESPath. Para saber mais e obter exemplos, consulte http://jmespath.org/.|
| --verbose          | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos.|

## <a name="sfctl-node-health"></a>sfctl node health
Obtém a integridade de um nó do Service Fabric.

Obtém a integridade de um nó do Service Fabric. Use EventsHealthStateFilter para filtrar o conjunto de eventos de integridade relatados no nó de acordo com o estado de integridade. Se o nó especificado pelo nome não existir no repositório de integridade, isso retornará um erro.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --node-name [Obrigatório]| O nome do nó.|
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

## <a name="sfctl-node-info"></a>sfctl node info
Obtém a lista de nós no cluster do Service Fabric.

Obtém as informações sobre um nó específico no cluster do Service Fabric. A resposta inclui o nome, o status, a ID, a integridade, o tempo de atividade e outros detalhes sobre o nó.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --node-name [Obrigatório]| O nome do nó.|
| --timeout -t       | Tempo limite do servidor em segundos.  Padrão: 60.|

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --debug            | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração.|
| --help -h          | Mostrar esta mensagem de ajuda e sair.|
| --output -o        | O formato da saída.  Valores permitidos: json, jsonc, table, tsv.  Padrão: json.|
| --query            | Cadeia de caracteres de consulta JMESPath. Para saber mais e obter exemplos, consulte http://jmespath.org/.|
| --verbose          | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos.|

## <a name="sfctl-node-list"></a>sfctl node list
Obtém a lista de nós no cluster do Service Fabric.

O ponto de extremidade Nodes retorna informações sobre os nós de cluster do Service Fabric. A resposta inclui o nome, o status, a ID, a integridade, o tempo de atividade e outros detalhes sobre o nó.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --continuation-token| O parâmetro do token de continuação é usado para obter o próximo conjunto de resultados. Um token de continuação com um valor não vazio será incluso na resposta da API quando os resultados do sistema não couberem em uma única resposta.      Quando esse valor for passado para a próxima chamada de API, a API retornará o próximo conjunto de resultados. Se não houver mais resultados, o token de continuação não conterá um valor. O valor desse parâmetro não deve ser codificado em URL.|
| --node-status-filter| Permite a filtragem de nós de acordo com o NodeStatus. Somente os nós que correspondem ao valor do filtro especificado retornam. O valor do filtro pode ser um dos seguintes. -default- Esse valor de filtro corresponde a todos os nós, exceto aqueles com status Unknown ou Removed. -all- Esse valor de filtro corresponde a todos os nós. -up - Esse valor de filtro corresponde aos nós ativos. -down- Esse valor de filtro corresponde aos nós desativados. -enabling- Esse valor de filtro corresponde aos nós que estão sendo habilitados com o status Enabling. -disabling- Esse valor de filtro corresponde aos nós que estão sendo desabilitados com o status de Disabling. -disabled- Esse valor de filtro corresponde aos nós desabilitados. -unknown- Esse valor de filtro corresponde aos nós cujo status é desconhecido. Um nó fica em estado desconhecido se o Service Fabric não tem informações de autoridade sobre esse nó. Isso pode acontecer se o sistema aprender sobre um nó no tempo de execução. -removed- Esse valor de filtro corresponde aos nós cujo status é removido. Esses são os nós que serão removidos do cluster usando a API RemoveNodeState. .      Padrão: default.|
| --timeout -t     | Tempo limite do servidor em segundos.  Padrão: 60.|

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --debug          | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração.|
| --help -h        | Mostrar esta mensagem de ajuda e sair.|
| --output -o      | O formato da saída.  Valores permitidos: json, jsonc, table, tsv.  Padrão: json.|
| --query          | Cadeia de caracteres de consulta JMESPath. Para saber mais e obter exemplos, consulte http://jmespath.org/.|
| --verbose        | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos.|

## <a name="sfctl-node-load"></a>sfctl node load
Obtém as informações de carregamento de um nó do Service Fabric.

Obtém as informações de carregamento de um nó do Service Fabric.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --node-name [Obrigatório]| O nome do nó.|
| --timeout -t       | Tempo limite do servidor em segundos.  Padrão: 60.|

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --debug            | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração.|
| --help -h          | Mostrar esta mensagem de ajuda e sair.|
| --output -o        | O formato da saída.  Valores permitidos: json, jsonc, table, tsv.  Padrão: json.|
| --query            | Cadeia de caracteres de consulta JMESPath. Para saber mais e obter exemplos, consulte http://jmespath.org/.|
| --verbose          | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos.|

## <a name="sfctl-node-restart"></a>sfctl node restart
Reinicia um nó de cluster do Service Fabric.

Reinicia um nó de cluster do Service Fabric que já foi iniciado.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --node-name [Obrigatório]| O nome do nó.|
| --create-fabric-dump  | Especifique True para criar um despejo do processo do nó de malha. Diferencia maiúsculas de minúsculas.  Padrão: false.|
| --node-instance-id | A ID da instância do nó de destino. Se a ID da instância for especificada, o nó será reiniciado somente se corresponder à instância atual do nó. Um valor padrão de "0" corresponderia à ID de qualquer instância. A ID da instância pode ser obtida usando a consulta get node.  Padrão: 0.|
| --timeout -t       | Tempo limite do servidor em segundos.  Padrão: 60.|

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --debug            | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração.|
| --help -h          | Mostrar esta mensagem de ajuda e sair.|
| --output -o        | O formato da saída.  Valores permitidos: json, jsonc, table, tsv.  Padrão: json.|
| --query            | Cadeia de caracteres de consulta JMESPath. Para saber mais e obter exemplos, consulte http://jmespath.org/.|
| --verbose          | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos.|

## <a name="sfctl-node-transition"></a>sfctl node transition
Inicia ou interrompe um nó de cluster.

Inicia ou interrompe um nó de cluster.  Um nó de cluster é um processo, não a própria instância do sistema operacional.
Para iniciar um nó, passe "Start" para o parâmetro NodeTransitionType. Para interromper um nó, passe "Stop" para o parâmetro NodeTransitionType. A API começa a operação; quando a API retornar, talvez o nó ainda não tenha terminado a transição. Chame GetNodeTransitionProgress com a mesma OperationId para obter o progresso da operação. .

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --node-instance-id         [Obrigatório]| A ID da instância do nó de destino. Isso pode ser determinado por meio da API GetNodeInfo.|
| --node-name                [Obrigatório]| O nome do nó.|
| --node-transition-type     [Obrigatório]| Indica o tipo de transição a ser executado.                       NodeTransitionType.Start inicia um nó parado.                       NodeTransitionType.Stop interrompe um nó ativo. -                       Invalid - Reserved.  Não passe para a API. -Start- Faz a transição de um nó interrompido para ativo. -Stop- Faz a transição de um nó ativo para interrompido. .|
| --operation-id             [Obrigatório]| Um GUID que identifica uma chamada dessa API.  Isso é passado para a API GetProgress correspondente.|
| --stop-duration-in-seconds [Obrigatório]| A duração, em segundos, para manter o nó interrompido.  O valor mínimo é 600, o máximo é 14400. Depois que esse tempo expirar, o nó automaticamente volta a funcionar.|
| --timeout -t                      | Tempo limite do servidor em segundos.  Padrão: 60.|

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --debug                           | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração.|
| --help -h                         | Mostrar esta mensagem de ajuda e sair.|
| --output -o                       | O formato da saída.  Valores permitidos: json, jsonc, table, tsv.                       Padrão: json.|
| --query                           | Cadeia de consulta JMESPath. Consulte http://jmespath.org/ para saber mais e obter exemplos.|
| --verbose                         | Aumentar o nível de detalhes do log. Use --debug para todos os logs de depuração.|

## <a name="next-steps"></a>Próximas etapas
- [Configurar](service-fabric-cli.md) a CLI do Service Fabric.
- Saiba como usar a CLI do Service Fabric usando os [scripts de exemplo](/azure/service-fabric/scripts/sfctl-upgrade-application).