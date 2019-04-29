---
title: CLI do Azure Service Fabric – réplica sfctl | Microsoft Docs
description: Descreve os comandos da réplica sfctl da CLI do Service Fabric.
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
ms.openlocfilehash: d0a7199ff0e9cb17c3fbc179a9b37a6620f521f9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60544663"
---
# <a name="sfctl-replica"></a>réplica sfctl
Gerenciar as réplicas que pertencem a partições de serviço.

## <a name="commands"></a>Comandos

|Comando|DESCRIÇÃO|
| --- | --- |
| deployed | Obtém os detalhes da réplica implantada em um nó do Service Fabric. |
| deployed-list | Obtém a lista de réplicas implantadas em um nó do Service Fabric. |
| integridade | Obtém a integridade de uma réplica de instância de serviço com estado ou de serviço sem estado do Service Fabric. |
| informações | Obtém as informações sobre uma réplica de uma partição do Service Fabric. |
| list | Obtém as informações sobre réplicas de uma partição de serviço do Service Fabric. |
| remove | Remove uma réplica de serviço em execução em um nó. |
| report-health | Envia um relatório de integridade sobre a réplica do Service Fabric. |
| restart | Reinicia uma réplica de serviço de um serviço persistente em execução em um nó. |

## <a name="sfctl-replica-deployed"></a>sfctl replica deployed
Obtém os detalhes da réplica implantada em um nó do Service Fabric.

Obtém os detalhes da réplica implantada em um nó do Service Fabric. As informações incluem o tipo de serviço, o nome do serviço, a operação do serviço atual, a data e a hora de início da operação do serviço atual, a ID da partição, a ID da instância/réplica, a carga relatada e outras informações.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --node-name                [Obrigatório] | O nome do nó. |
| --partition-id [Obrigatório] | A identidade da partição. |
| --replica-id   [Obrigatório] | O identificador da réplica. |
| --timeout -t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, table, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-replica-deployed-list"></a>sfctl implantado-lista de réplica
Obtém a lista de réplicas implantadas em um nó do Service Fabric.

Obtém a lista que contém as informações sobre réplicas implantado em um nó de malha do serviço. As informações incluem a ID de partição, o ID da réplica, o status da réplica, o nome do serviço, nome do tipo de serviço e outras informações. Use PartitionId ou ServiceManifestName parâmetros de consulta para retornar informações sobre as réplicas implantadas os valores especificados para os parâmetros de correspondência.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --application-id [Obrigatório] | A identidade do aplicativo. Normalmente, este é o nome completo do aplicativo sem o esquema de URI "fabric\:". A partir da versão 6.0, nomes hierárquicos são delimitados pelo caractere "\~". Por exemplo, se o nome do aplicativo for "fabric\:/meuaplicativo/aplicativo1", a identidade do aplicativo será "meuaplicativo\~aplicativo1" na versão 6.0 e superiores, e "meuaplicativo/aplicativo1" nas versões anteriores. |
| --node-name      [Obrigatório] | O nome do nó. |
| partition-id | A identidade da partição. |
| --service-manifest-name | O nome de um manifesto de serviço registrado como parte de um tipo de aplicativo em um cluster do Service Fabric. |
| --timeout -t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, table, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-replica-health"></a>sfctl replica health
Obtém a integridade de uma réplica de instância de serviço com estado ou de serviço sem estado do Service Fabric.

Obtém a integridade de uma réplica do Service Fabric. Use EventsHealthStateFilter para filtrar o conjunto de eventos de integridade relatados na réplica de acordo com o estado de integridade.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --partition-id [Obrigatório] | A identidade da partição. |
| --replica-id   [Obrigatório] | O identificador da réplica. |
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

## <a name="sfctl-replica-info"></a>sfctl replica info
Obtém as informações sobre uma réplica de uma partição do Service Fabric.

A resposta inclui o ID, a função, o status, a integridade, o nome do nó, o tempo de atividade e outros detalhes sobre a réplica.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --partition-id [Obrigatório] | A identidade da partição. |
| --replica-id   [Obrigatório] | O identificador da réplica. |
| --timeout -t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, table, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-replica-list"></a>sfctl replica list
Obtém as informações sobre réplicas de uma partição de serviço do Service Fabric.

O ponto de extremidade GetReplicas retorna informações sobre as réplicas da partição especificada. A resposta inclui o ID, a função, o status, a integridade, o nome do nó, o tempo de atividade e outros detalhes sobre a réplica.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --partition-id [Obrigatório] | A identidade da partição. |
| --continuation-token | O parâmetro do token de continuação é usado para obter o próximo conjunto de resultados. Um token de continuação com um valor não vazio será incluso na resposta da API quando os resultados do sistema não couberem em uma única resposta. Quando esse valor for passado para a próxima chamada de API, a API retornará o próximo conjunto de resultados. Se não houver mais resultados, o token de continuação não conterá um valor. O valor desse parâmetro não deve ser codificado em URL. |
| --timeout -t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, table, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-replica-remove"></a>sfctl replica remove
Remove uma réplica de serviço em execução em um nó.

Essa API simula uma falha de réplica do Service Fabric removendo uma réplica de um cluster do Service Fabric. A remoção fecha a réplica, faz a transição da réplica para a função None e, em seguida, remove todas as informações de estado da réplica do cluster. Essa API testa o caminho de remoção de estado da réplica e simula o caminho permanente de falha de relatório por meio de APIs de cliente. Aviso: nenhuma verificação de segurança é executada ao usar essa API. Uso incorreto dessa API pode levar a perda de dados para serviços com monitoração de estado. Além disso, o sinalizador forceRemove afeta todas as outras réplicas hospedadas no mesmo processo.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --node-name                [Obrigatório] | O nome do nó. |
| --partition-id [Obrigatório] | A identidade da partição. |
| --replica-id   [Obrigatório] | O identificador da réplica. |
| --force-remove | Force a remoção de um aplicativo ou serviço do Service Fabric sem passar pela sequência de desligamento normal. Esse parâmetro pode ser usado para forçar a exclusão de um aplicativo ou serviço para o qual a exclusão estiver ultrapassando o tempo limite, devido a problemas no código do serviço que impedem o fechamento normal das réplicas. |
| --timeout -t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, table, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-replica-report-health"></a>relatório de réplica sfctl integridade
Envia um relatório de integridade sobre a réplica do Service Fabric.

Relata o estado de integridade da réplica de malha do serviço especificado. O relatório deve conter as informações sobre a origem do relatório de integridade e propriedade na qual ele será relatado. O relatório é enviado a um gateway do Service Fabric réplica, que encaminha para o repositório de integridade. O relatório pode ser aceito pelo gateway, mas rejeitado pelo repositório de integridade após validação adicional. Por exemplo, o repositório de integridade pode rejeitar o relatório devido a um parâmetro inválido, como um número de sequência obsoleto. Para ver se o relatório foi aplicado no repositório de integridade, execute para obter a integridade da réplica e verifique se o relatório é exibido na seção HealthEvents.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --health-property [Obrigatório] | A propriedade das informações de integridade. <br><br> Uma entidade pode ter relatórios de integridade para propriedades diferentes. A propriedade é uma cadeia de caracteres e não uma enumeração fixa para permitir ao relator flexibilidade para categorizar a condição de estado que dispara o relatório. Por exemplo, um relator com SourceId "LocalWatchdog" pode monitorar o estado do disco disponível em um nó, para poder relatar a propriedade "AvailableDisk" nesse nó. O mesmo relator pode monitorar a conectividade do nó, para que ele possa relatar a "Conectividade" de uma propriedade no mesmo nó. No repositório de integridade, esses relatórios são tratados como eventos de integridade distintos para o nó especificado. Junto com a SourceId, a propriedade identifica exclusivamente as informações de integridade. |
| --health-state    [Obrigatório] | Os valores possíveis são\: 'Invalid', 'Ok', 'Warning', 'Error', 'Unknown'. |
| --partition-id [Obrigatório] | A identidade da partição. |
| --replica-id   [Obrigatório] | A identidade da partição. |
| --source-id       [Obrigatório] | O nome de origem que identifica o componente do cliente/watchdog/sistema que gerou as informações de integridade. |
| --description | A descrição de informações de integridade. <br><br> Ele representa texto livre usado para adicionar informações legíveis humanas sobre o relatório. O tamanho máximo da cadeia de caracteres da descrição é de 4.096 caracteres. Se a cadeia de caracteres fornecida for maior, será truncada automaticamente. Quando truncada, os últimos caracteres da descrição contêm um marcador "[Truncated]", e o tamanho total da cadeia de caracteres é de 4.096 caracteres. A presença do marcador indica aos usuários que o truncamento ocorreu. Observe que, quando truncada, a descrição tem menos de 4.096 caracteres da cadeia de caracteres original. |
| --immediate | Um sinalizador que indica se o relatório deve ser enviado imediatamente. <br><br> Um relatório de integridade é enviado a um Aplicativo de gateway do Service Fabric, que encaminha para o repositório de integridade. Se "Immediate" for definido como "true", o relatório será enviado imediatamente do Gateway de HTTP para o repositório de integridade, independentemente das configurações de cliente que o Aplicativo de Gateway de HTTP estiver usando. Isso é útil para relatórios importantes que precisam ser enviados assim que possível. Dependendo do tempo e de outras condições, enviar o relatório ainda pode falhar se, por exemplo, o Gateway de HTTP estiver fechado ou a mensagem não alcançar o Gateway. Se "Immediate" for definido como "false", o relatório será enviado com base nas configurações do cliente de integridade do Gateway de HTTP. Portanto, ele será agrupado de acordo com a configuração de HealthReportSendInterval. Esta é a configuração recomendada, pois ela permite que o cliente de integridade otimize a integridade relatando mensagens para o repositório de integridade, bem como para o processamento de relatório de integridade. Por padrão, relatórios não são enviados imediatamente. |
| – Remover quando expirado | Valor que indica se o relatório é removido do repositório de integridade quando expirar. <br><br> Se definido como "true", o relatório é removido do repositório de integridade depois de expirar. Se definido como "false", o relatório é tratado como um erro quando expirado. O valor dessa propriedade é "false" por padrão. Quando os clientes enviam relatórios periodicamente, devem definir RemoveWhenExpired como "false" (padrão). Dessa forma, se o gerador de relatórios tiver problemas (por exemplo, um deadlock) e não puder fazer o relatório, a entidade será avaliada como erro quando o relatório de integridade expirar. Isso sinaliza a entidade como em um estado de integridade de Erro. |
| --sequence-number | O número da sequência para este relatório de integridade como uma cadeia de caracteres numérica. <br><br> O número de sequência de relatório é usado pelo repositório de integridade para detectar relatórios obsoletos. Se não for especificado, um número de sequência será gerado automaticamente pelo cliente de integridade quando um relatório for adicionado. |
| --service-kind | O tipo de réplica de serviço (com ou sem estado) para o qual a integridade é informada. Estes são os valores possíveis\: 'Sem monitoração de estado', 'Com estado'.  Padrão\: com monitoração de estado. |
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

## <a name="sfctl-replica-restart"></a>sfctl replica restart
Reinicia uma réplica de serviço de um serviço persistente em execução em um nó.

Reinicia uma réplica de serviço de um serviço persistente em execução em um nó. Aviso: nenhuma verificação de segurança é executada ao usar essa API. O uso incorreto dessa API pode levar à perda de disponibilidade para serviços com estado.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --node-name                [Obrigatório] | O nome do nó. |
| --partition-id [Obrigatório] | A identidade da partição. |
| --replica-id   [Obrigatório] | O identificador da réplica. |
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
