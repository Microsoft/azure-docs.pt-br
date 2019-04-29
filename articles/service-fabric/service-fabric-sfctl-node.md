---
title: CLI do Azure Service Fabric - nó sfctl | Microsoft Docs
description: Descreve os comandos do nó sfctl da CLI do Service Fabric.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: 08ea0081c84ea31b2b71d03679b1b527cf94c075
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60556773"
---
# <a name="sfctl-node"></a>Nó sfctl
Gerenciar os nós que formam um cluster.

## <a name="commands"></a>Comandos

|Comando|DESCRIÇÃO|
| --- | --- |
| disable | Desativa um nó de cluster do Service Fabric com a intenção de desativação especificada. |
| enable | Ativar um nó de cluster do Service Fabric que está desativado no momento. |
| integridade | Obtém a integridade de um nó do Service Fabric. |
| informações | Obtém as informações sobre um nó específico no cluster do Service Fabric. |
| list | Obtém a lista de nós no cluster do Service Fabric. |
| load | Obtém as informações de carregamento de um nó do Service Fabric. |
| remove-state | Notifica o Service Fabric que o estado persistente em um nó foi permanentemente removido ou perdido. |
| report-health | Envia um relatório de integridade sobre o nó do Service Fabric. |
| restart | Reinicia um nó de cluster do Service Fabric. |
| transition | Inicia ou interrompe um nó de cluster. |
| transition-status | Obtém o progresso de uma operação iniciada usando StartNodeTransition. |

## <a name="sfctl-node-disable"></a>sfctl node disable
Desativa um nó de cluster do Service Fabric com a intenção de desativação especificada.

Desativa um nó de cluster do Service Fabric com a intenção de desativação especificada. Quando a desativação estiver em progresso, a intenção de desativação poderá ser aumentada, mas não reduzida (por exemplo, um nó que está desativado com a intenção Pause pode ser desativado com Restart, mas não o contrário. Os nós podem ser reativados usando a operação Ativar um nó a qualquer momento após a desativação. Se a desativação não estiver concluída, isso cancelará a desativação. Um nó desativado e que volta a funcionar enquanto está desativado ainda precisará ser reativado antes que os serviços possam ser colocados nesse nó.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --node-name [Obrigatório] | O nome do nó. |
| --deactivation-intent | Descreve a intenção ou o motivo da desativação do nó. Os valores possíveis são os seguintes. |
| --timeout -t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, table, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-node-enable"></a>sfctl node enable
Ativar um nó de cluster do Service Fabric que está desativado no momento.

Ativa um nó de cluster do Service Fabric que está desativado no momento. Quando ativado, o nó se tornará novamente um destino viável para o posicionamento de novas réplicas e todas as réplicas desativadas restantes no nó serão reativadas.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --node-name [Obrigatório] | O nome do nó. |
| --timeout -t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, table, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-node-health"></a>sfctl node health
Obtém a integridade de um nó do Service Fabric.

Obtém a integridade de um nó do Service Fabric. Use EventsHealthStateFilter para filtrar o conjunto de eventos de integridade relatados no nó de acordo com o estado de integridade. Se o nó especificado pelo nome não existir no repositório de integridade, isso retornará um erro.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --node-name [Obrigatório] | O nome do nó. |
| --events-health-state-filter | Permite filtrar a coleção de objetos HealthEvent retornados com base no estado de integridade. Os valores possíveis para esse parâmetro incluem o valor de inteiro de um dos seguintes estados de integridade. Somente os eventos que correspondem ao filtro são retornados. Todos os eventos são usados para avaliar o estado de integridade agregado. Se não for especificado, retorna todas as entradas. Os valores de estado são enumerações baseadas no sinalizador. Assim, o valor pode ser uma combinação desses valores obtidos, usando o operador “OR” bit a bit. Por exemplo, se o valor fornecido for 6, serão retornados todos os eventos com o valor de HealthState de OK (2) e de Aviso (4).  <br> – Default – Valor padrão. Corresponde a qualquer HealthState. O valor é zero.  <br> -None- Filtro que não corresponde a qualquer valor de HealthState. Usado para não retornar qualquer resultado em um determinado conjunto de estados. O valor é 1.  <br> - Ok - Filtro que corresponde à entrada com o valor de HealthState Ok. O valor é 2.  <br> -Warning- Filtro que corresponde à entrada com o valor de HealthState Warning. O valor é 4.  <br> -Error- Filtro que corresponde a entrada com o valor de HealthState Error. O valor é 8.  <br> - All - Filtro que corresponde à entrada com qualquer valor de HealthState. O valor é 65535. |
| --timeout -t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, table, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-node-info"></a>sfctl node info
Obtém as informações sobre um nó específico no cluster do Service Fabric.

A resposta inclui o nome, o status, a ID, a integridade, o tempo de atividade e outros detalhes sobre o nó.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --node-name [Obrigatório] | O nome do nó. |
| --timeout -t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, table, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-node-list"></a>sfctl node list
Obtém a lista de nós no cluster do Service Fabric.

A resposta inclui o nome, o status, a ID, a integridade, o tempo de atividade e outros detalhes sobre o nó.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --continuation-token | O parâmetro do token de continuação é usado para obter o próximo conjunto de resultados. Um token de continuação com um valor não vazio será incluso na resposta da API quando os resultados do sistema não couberem em uma única resposta. Quando esse valor for passado para a próxima chamada de API, a API retornará o próximo conjunto de resultados. Se não houver mais resultados, o token de continuação não conterá um valor. O valor desse parâmetro não deve ser codificado em URL. |
| --max-results | O número máximo de resultados a serem retornados como parte das consultas paginadas. Esse parâmetro define o limite superior no número de resultados retornados. Os resultados retornados podem ser inferiores aos resultados máximos especificados se não couberem na mensagem, de acordo com as restrições de tamanho máximo de mensagem definidas na configuração. Se esse parâmetro for zero, ou não for especificado, a consulta paginada incluirá o máximo de resultados possível na mensagem de retorno. |
| --node-status-filter | Permite a filtragem de nós de acordo com o NodeStatus. Somente os nós que correspondem ao valor do filtro especificado serão retornados. O valor do filtro pode ser um dos seguintes.  Padrão\: padrão. |
| --timeout -t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, table, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-node-load"></a>sfctl node load
Obtém as informações de carregamento de um nó do Service Fabric.

Recupera as informações de carga de um nó do Service Fabric para todas as métricas que têm carga ou capacidade definida.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --node-name [Obrigatório] | O nome do nó. |
| --timeout -t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, table, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-node-remove-state"></a>remover nó sfctl - estado
Notifica o Service Fabric que o estado persistente em um nó foi permanentemente removido ou perdido.

Isso significa que não é possível recuperar o estado persistente desse nó. Isso geralmente acontece se um disco rígido tiver sido limpo ou se um disco rígido falhar. O nó deve ser para baixo para que essa operação seja bem-sucedida. Esta operação permite que o Service Fabric saiba que as réplicas no nó deixarão de existir e esse Service Fabric deve parar de esperar que essas réplicas retornem. Não execute esse cmdlet se o estado no nó foi removido e se o nó pode retornar com seu estado intacto.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --node-name [Obrigatório] | O nome do nó. |
| --timeout -t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, table, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-node-report-health"></a>relatório de nó - integridade
Envia um relatório de integridade sobre o nó do Service Fabric.

Relata o estado de integridade do nó do Service Fabric especificado. O relatório deve conter as informações sobre a origem do relatório de integridade e a propriedade na qual ele será relatado. O relatório é enviado a um nó de gateway do Service Fabric, que encaminha para o repositório de integridade. O relatório pode ser aceito pelo gateway, mas rejeitado pelo repositório de integridade após validação adicional. Por exemplo, o repositório de integridade pode rejeitar o relatório devido a um parâmetro inválido, como um número de sequência obsoleto. Para ver se o relatório foi aplicado no repositório de integridade, verifique se o relatório é exibido na seção HealthEvents.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --health-property [Obrigatório] | A propriedade das informações de integridade. <br><br> Uma entidade pode ter relatórios de integridade para propriedades diferentes. A propriedade é uma cadeia de caracteres e não uma enumeração fixa para permitir ao relator flexibilidade para categorizar a condição de estado que dispara o relatório. Por exemplo, um relator com SourceId "LocalWatchdog" pode monitorar o estado do disco disponível em um nó, para poder relatar a propriedade "AvailableDisk" nesse nó. O mesmo relator pode monitorar a conectividade do nó, para que ele possa relatar a "Conectividade" de uma propriedade no mesmo nó. No repositório de integridade, esses relatórios são tratados como eventos de integridade distintos para o nó especificado. Junto com a SourceId, a propriedade identifica exclusivamente as informações de integridade. |
| --health-state    [Obrigatório] | Os valores possíveis são\: 'Invalid', 'Ok', 'Warning', 'Error', 'Unknown'. |
| --node-name [Obrigatório] | O nome do nó. |
| --source-id       [Obrigatório] | O nome de origem que identifica o componente do cliente/watchdog/sistema que gerou as informações de integridade. |
| --description | A descrição de informações de integridade. <br><br> Ele representa texto livre usado para adicionar informações legíveis humanas sobre o relatório. O tamanho máximo da cadeia de caracteres da descrição é de 4.096 caracteres. Se a cadeia de caracteres fornecida for maior, será truncada automaticamente. Quando truncada, os últimos caracteres da descrição contêm um marcador "[Truncated]", e o tamanho total da cadeia de caracteres é de 4.096 caracteres. A presença do marcador indica aos usuários que o truncamento ocorreu. Observe que, quando truncada, a descrição tem menos de 4.096 caracteres da cadeia de caracteres original. |
| --immediate | Um sinalizador que indica se o relatório deve ser enviado imediatamente. <br><br> Um relatório de integridade é enviado a um Aplicativo de gateway do Service Fabric, que encaminha para o repositório de integridade. Se "Immediate" for definido como "true", o relatório será enviado imediatamente do Gateway de HTTP para o repositório de integridade, independentemente das configurações de cliente que o Aplicativo de Gateway de HTTP estiver usando. Isso é útil para relatórios importantes que precisam ser enviados assim que possível. Dependendo do tempo e de outras condições, enviar o relatório ainda pode falhar se, por exemplo, o Gateway de HTTP estiver fechado ou a mensagem não alcançar o Gateway. Se "Immediate" for definido como "false", o relatório será enviado com base nas configurações do cliente de integridade do Gateway de HTTP. Portanto, ele será agrupado de acordo com a configuração de HealthReportSendInterval. Esta é a configuração recomendada, pois ela permite que o cliente de integridade otimize a integridade relatando mensagens para o repositório de integridade, bem como para o processamento de relatório de integridade. Por padrão, relatórios não são enviados imediatamente. |
| – Remover quando expirado | Valor que indica se o relatório é removido do repositório de integridade quando expirar. <br><br> Se definido como "true", o relatório é removido do repositório de integridade depois de expirar. Se definido como "false", o relatório é tratado como um erro quando expirado. O valor dessa propriedade é "false" por padrão. Quando os clientes enviam relatórios periodicamente, devem definir RemoveWhenExpired como "false" (padrão). Dessa forma, se o gerador de relatórios tiver problemas (por exemplo, um deadlock) e não puder fazer o relatório, a entidade será avaliada como erro quando o relatório de integridade expirar. Isso sinaliza a entidade como em um estado de integridade de Erro. |
| --sequence-number | O número da sequência para este relatório de integridade como uma cadeia de caracteres numérica. <br><br> O número de sequência de relatório é usado pelo repositório de integridade para detectar relatórios obsoletos. Se não for especificado, um número de sequência será gerado automaticamente pelo cliente de integridade quando um relatório for adicionado. |
| --timeout -t | Tempo limite do servidor em segundos.  Padrão\: 60. |
| --ttl | A duração da validade deste relatório de integridade. Este campo usa o formato ISO8601 para especificar a duração. <br><br> Quando clientes geram relatórios periodicamente, eles devem enviar relatórios com uma frequência maior do que o tempo de vida. Se os clientes gerarem relatórios sobre a transição, poderão definir o tempo de vida como infinito. Quando o tempo de vida expira, o evento de integridade que contém as informações de integridade é removido do repositório de integridade, se RemoveWhenExpired for true, ou recebe uma avaliação de erro, se RemoveWhenExpired for false. Se não for especificado, o tempo de vida terá como padrão o valor infinito. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, table, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-node-restart"></a>sfctl node restart
Reinicia um nó de cluster do Service Fabric.

Reinicia um nó de cluster do Service Fabric que já foi iniciado.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --node-name [Obrigatório] | O nome do nó. |
| --create-fabric-dump | Especifique True para criar um despejo do processo do nó de malha. Diferencia maiúsculas de minúsculas.  Padrão\: falso. |
| --node-instance-id | A ID da instância do nó de destino. Se a ID da instância for especificada, o nó será reiniciado somente se corresponder à instância atual do nó. Um valor padrão de "0" corresponderia à ID de qualquer instância. A ID da instância pode ser obtida usando a consulta get node.  Padrão\: 0. |
| --timeout -t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, table, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-node-transition"></a>sfctl node transition
Inicia ou interrompe um nó de cluster.

Inicia ou interrompe um nó de cluster.  Um nó de cluster é um processo, não a própria instância do sistema operacional.  Para iniciar um nó, passe "Start" para o parâmetro NodeTransitionType. Para interromper um nó, passe "Stop" para o parâmetro NodeTransitionType. A API começa a operação; quando a API retornar, talvez o nó ainda não tenha terminado a transição. Chame GetNodeTransitionProgress com a mesma OperationId para obter o progresso da operação.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --node-instance-id         [Obrigatório] | A ID da instância do nó de destino. Isso pode ser determinado por meio da API GetNodeInfo. |
| --node-name                [Obrigatório] | O nome do nó. |
| --node-transition-type     [Obrigatório] | Indica o tipo de transição a ser executado.  NodeTransitionType.Start iniciará um nó parado. NodeTransitionType.Stop interromperá um nó ativo. |
| --operation-id             [Obrigatório] | Um GUID que identifica uma chamada dessa API.  Isso é passado para a API GetProgress correspondente. |
| --stop-duration-in-seconds [Obrigatório] | A duração, em segundos, para manter o nó interrompido.  O valor mínimo é 600, o máximo é 14400.  Depois que esse tempo expirar, o nó automaticamente voltará a funcionar. |
| --timeout -t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, table, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-node-transition-status"></a>transição de nó sfctl-status
Obtém o progresso de uma operação iniciada usando StartNodeTransition.

Obtém o andamento de uma operação iniciada com StartNodeTransition usando a ID da operação fornecida.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --node-name                [Obrigatório] | O nome do nó. |
| --id da operação [requerido] | Um GUID que identifica uma chamada dessa API.  Isso é passado para a API GetProgress correspondente. |
| --timeout -t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, table, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |


## <a name="next-steps"></a>Próximas etapas
- [Configurar](service-fabric-cli.md) a CLI do Service Fabric.
- Saiba como usar a CLI do Service Fabric usando os [scripts de exemplo](/azure/service-fabric/scripts/sfctl-upgrade-application).