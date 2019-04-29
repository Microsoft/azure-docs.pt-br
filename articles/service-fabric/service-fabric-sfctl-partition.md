---
title: Partição da CLI- sfctl do Service Fabric do Azure| Microsoft Docs
description: Descreve os comandos de partição da CLI- sfctl do Service Fabric.
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
ms.openlocfilehash: f7c9bcc51757100cb1fc957dee12213bc8bf2eec
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60556584"
---
# <a name="sfctl-partition"></a>partição sfctl
Consultar e gerenciar partições para qualquer serviço.

## <a name="commands"></a>Comandos

|Comando|DESCRIÇÃO|
| --- | --- |
| data-loss | Essa API induzirá a perda de dados para a partição especificada. |
| data-loss-status | Obtém o progresso de uma operação de perda de dados de partição iniciada, utilizando a API StartDataLoss. |
| integridade | Obtém a integridade da partição do Service Fabric especificado. |
| informações | Obtém as informações sobre uma partição do Service Fabric. |
| list | Obtém a lista de partições de um serviço do Service Fabric. |
| load | Obtém as informações de carga da partição do Service Fabric especificado. |
| load-reset | Redefine a carga atual de uma partição do Service Fabric. |
| quorum-loss | Induz a perda de quorum de uma determinada partição de serviço com estado. |
| quorum-loss-status | Obtém o progresso de uma operação de perda de quorum em uma partição iniciada utilizando a API StartQuorumLoss. |
| recover | Indica ao cluster do Service Fabric que deve tentar recuperar uma partição específica que está atualmente paralisada na perda de quorum. |
| recover-all | Indica ao cluster do Service Fabric que ele deve tentar recuperar quaisquer serviços (incluindo serviços do sistema) que estão atualmente paralisados na perda de quorum. |
| report-health | Envia um relatório de integridade na partição do Service Fabric. |
| restart | Essa API reiniciará algumas ou todas as réplicas ou instâncias da partição especificada. |
| restart-status | Obtém o progresso de uma operação PartitionRestart iniciada utilizando StartPartitionRestart. |
| svc-name | Obtém o nome do serviço do Service Fabric para uma partição. |

## <a name="sfctl-partition-data-loss"></a>sfctl partition data-loss
Essa API induzirá a perda de dados para a partição especificada.

Ele acionará uma chamada à API OnDataLossAsync da partição.  Essa API induzirá a perda de dados para a partição especificada. Ele acionará uma chamada à API OnDataLoss da partição. A perda de dados real dependerá do DataLossMode especificado.  <br> - PartialDataLoss: somente um quorum de réplicas é removido e OnDataLoss é acionado para a partição, mas a perda de dados real depende da presença da replicação em andamento.  <br> - FullDataLoss: todas as réplicas são removidas, portanto, todos os dados são perdidos e OnDataLoss é acionado. Essa API só deve ser chamada com um serviço com estado como o destino. Não aconselhamos chamar uma API com um serviço de sistema como destino.

> [!NOTE] 
> Após chamar essa API, ela não poderá ser revertida. Chamar CancelOperation apenas interromperá a execução e limpará o estado do sistema interno. Não restaurará os dados se o comando tiver avançado o suficiente para causar perda de dados. Chame a API de GetDataLossProgress com a mesma OperationId para retornar informações sobre a operação iniciada com essa API.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --data-loss-mode [Obrigatório] | Essa enumeração é passada para a API StartDataLoss para indicar qual tipo de perda de dados induzir. |
| --operation-id   [Obrigatório] | Um GUID que identifica uma chamada dessa API.  Isso é passado para a API GetProgress correspondente. |
| --partition-id   [Obrigatório] | A identidade da partição. |
| --service-id          [Obrigatório] | A identidade do serviço. Essa ID normalmente é o nome completo do serviço sem o ' malha\:' esquema de URI. A partir da versão 6.0, nomes hierárquicos são delimitados pelo caractere "\~". Por exemplo, se o nome do serviço for "fabric\:/myapp/app1/svc1", a identidade do serviço será "myapp\~app1\~svc1" na versão 6.0 e superiores, e "myapp/app1/svc1" em versões anteriores. |
| --timeout -t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, table, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-partition-data-loss-status"></a>sfctl partition data-loss-status
Obtém o progresso de uma operação de perda de dados de partição iniciada, utilizando a API StartDataLoss.

Obtém o progresso de uma operação de perda de dados iniciada com StartDataLoss, usando a OperationId.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --id da operação [requerido] | Um GUID que identifica uma chamada dessa API.  Isso é passado para a API GetProgress correspondente. |
| --partition-id [Obrigatório] | A identidade da partição. |
| --service-id          [Obrigatório] | A identidade do serviço. Essa ID normalmente é o nome completo do serviço sem o ' malha\:' esquema de URI. A partir da versão 6.0, nomes hierárquicos são delimitados pelo caractere "\~". Por exemplo, se o nome do serviço for "fabric\:/myapp/app1/svc1", a identidade do serviço será "myapp\~app1\~svc1" na versão 6.0 e superiores, e "myapp/app1/svc1" em versões anteriores. |
| --timeout -t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, table, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-partition-health"></a>sfctl partition health
Obtém a integridade da partição do Service Fabric especificado.

Use EventsHealthStateFilter para filtrar o conjunto de eventos de integridade relatados no serviço de acordo com o estado de integridade. Utilize ReplicasHealthStateFilter para filtrar a coleção dos objetos ReplicaHealthState na partição. Se você especificar um serviço que não existe no repositório de integridade, essa solicitação retornará um erro.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --partition-id      [Obrigatório] | A identidade da partição. |
| --events-health-state-filter | Permite filtrar a coleção de objetos HealthEvent retornados com base no estado de integridade. Os valores possíveis para esse parâmetro incluem o valor de inteiro de um dos seguintes estados de integridade. Somente os eventos que correspondem ao filtro são retornados. Todos os eventos são usados para avaliar o estado de integridade agregado. Se não for especificado, retorna todas as entradas. Os valores de estado são enumerações baseadas no sinalizador. Assim, o valor pode ser uma combinação desses valores obtidos, usando o operador “OR” bit a bit. Por exemplo, se o valor fornecido for 6, serão retornados todos os eventos com o valor de HealthState de OK (2) e de Aviso (4).  <br> – Default – Valor padrão. Corresponde a qualquer HealthState. O valor é zero.  <br> -None- Filtro que não corresponde a qualquer valor de HealthState. Usado para não retornar qualquer resultado em um determinado conjunto de estados. O valor é 1.  <br> - Ok - Filtro que corresponde à entrada com o valor de HealthState Ok. O valor é 2.  <br> -Warning- Filtro que corresponde à entrada com o valor de HealthState Warning. O valor é 4.  <br> -Error- Filtro que corresponde a entrada com o valor de HealthState Error. O valor é 8.  <br> - All - Filtro que corresponde à entrada com qualquer valor de HealthState. O valor é 65535. |
| --exclude-health-statistics | Indica se as estatísticas de integridade devem ser retornadas como parte do resultado da consulta. Falso por padrão. As estatísticas mostram o número de entidades filhas nos estados de integridade Ok, Warning e Error. |
| --replicas-health-state-filter | Permite a filtragem da coleção de objetos ReplicaHealthState na partição. O valor pode ser obtido de membros ou operações bit a bit em membros de HealthStateFilter. Somente as réplicas que correspondem ao filtro retornam. Todas as réplicas serão usadas para avaliar o estado de integridade agregado. Se não especificado, todas as entradas serão retornadas. Os valores de estado são enumerações baseadas no sinalizador. Assim, o valor pode ser uma combinação desses valores obtidos, usando o operador 'OR' bit a bit. Por exemplo, se o valor fornecido for 6, serão retornados todos os eventos com o valor OK (2) e Warning (4) de HealthState. Os valores possíveis para esse parâmetro incluem o valor de inteiro de um dos seguintes estados de integridade.  <br> – Default – Valor padrão. Corresponde a qualquer HealthState. O valor é zero.  <br> -None- Filtro que não corresponde a qualquer valor de HealthState. Usado para não retornar qualquer resultado em um determinado conjunto de estados. O valor é 1.  <br> - Ok - Filtro que corresponde à entrada com o valor de HealthState Ok. O valor é 2.  <br> -Warning- Filtro que corresponde à entrada com o valor de HealthState Warning. O valor é 4.  <br> -Error- Filtro que corresponde a entrada com o valor de HealthState Error. O valor é 8.  <br> - All - Filtro que corresponde à entrada com qualquer valor de HealthState. O valor é 65535. |
| --timeout -t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, table, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-partition-info"></a>sfctl partition info
Obtém as informações sobre uma partição do Service Fabric.

Obtém as informações sobre a partição especificada. A resposta inclui a ID da partição, informações do esquema de particionamento, chaves suportadas pela partição, status, integridade e outros detalhes sobre a partição.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --partition-id [Obrigatório] | A identidade da partição. |
| --timeout -t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, table, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-partition-list"></a>sfctl partition list
Obtém a lista de partições de um serviço do Service Fabric.

A resposta inclui a ID da partição, informações do esquema de particionamento, chaves suportadas pela partição, status, integridade e outros detalhes sobre a partição.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --service-id [Obrigatório] | A identidade do serviço. Essa ID normalmente é o nome completo do serviço sem o ' malha\:' esquema de URI. A partir da versão 6.0, nomes hierárquicos são delimitados pelo caractere "\~". Por exemplo, se o nome do serviço for "fabric\:/myapp/app1/svc1", a identidade do serviço será "myapp\~app1\~svc1" na versão 6.0 e superiores, e "myapp/app1/svc1" em versões anteriores. |
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

## <a name="sfctl-partition-load"></a>sfctl partition load
Obtém as informações de carga da partição do Service Fabric especificado.

Retorna informações sobre carga de uma partição especificada. A resposta inclui uma lista de relatórios de carga para uma partição do Service Fabric. Cada relatório inclui o nome da métrica de carga, o valor e o último horário registrado em UTC.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --partition-id [Obrigatório] | A identidade da partição. |
| --timeout -t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, table, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-partition-load-reset"></a>sfctl partition load-reset
Redefine a carga atual de uma partição do Service Fabric.

Redefine a carga atual de uma partição do Service Fabric para a carga padrão do serviço.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --partition-id [Obrigatório] | A identidade da partição. |
| --timeout -t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, table, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-partition-quorum-loss"></a>sfctl partition quorum-loss
Induz a perda de quorum de uma determinada partição de serviço com estado.

Essa API é útil em uma situação de perda de quorum temporária em seu serviço. Chame a API de GetQuorumLossProgress com a mesma OperationId para retornar informações sobre a operação iniciada com essa API. Isso só pode ser chamado em serviços com estado persistente (HasPersistedState = = true).  Não use essa API em serviços sem estado ou em serviços com estado na memória apenas.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --operation-id         [Obrigatório] | Um GUID que identifica uma chamada dessa API.  Isso é passado para a API GetProgress correspondente. |
| --partition-id         [Obrigatório] | A identidade da partição. |
| --quorum-loss-duration [Obrigatório] | A quantidade de tempo durante a qual a partição será mantida com perda de quorum.  Isso deve ser especificado em segundos. |
| --quorum-loss-mode     [Obrigatório] | Essa enumeração é passada para a API StartQuorumLoss para indicar qual tipo de perda de quorum induzir. |
| --service-id           [Obrigatório] | A identidade do serviço. Essa ID normalmente é o nome completo do serviço sem o ' malha\:' esquema de URI. A partir da versão 6.0, nomes hierárquicos são delimitados pelo caractere "\~". Por exemplo, se o nome do serviço for "fabric\:/myapp/app1/svc1", a identidade do serviço será "myapp\~app1\~svc1" na versão 6.0 e superiores, e "myapp/app1/svc1" em versões anteriores. |
| --timeout -t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, table, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-partition-quorum-loss-status"></a>sfctl partition quorum-loss-status
Obtém o progresso de uma operação de perda de quorum em uma partição iniciada utilizando a API StartQuorumLoss.

Obtém o progresso de uma operação de perda de quorum iniciada com StartQuorumLoss, usando a OperationId fornecida.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --id da operação [requerido] | Um GUID que identifica uma chamada dessa API.  Isso é passado para a API GetProgress correspondente. |
| --partition-id [Obrigatório] | A identidade da partição. |
| --service-id          [Obrigatório] | A identidade do serviço. Essa ID normalmente é o nome completo do serviço sem o ' malha\:' esquema de URI. A partir da versão 6.0, nomes hierárquicos são delimitados pelo caractere "\~". Por exemplo, se o nome do serviço for "fabric\:/myapp/app1/svc1", a identidade do serviço será "myapp\~app1\~svc1" na versão 6.0 e superiores, e "myapp/app1/svc1" em versões anteriores. |
| --timeout -t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, table, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-partition-recover"></a>sfctl partition recover
Indica ao cluster do Service Fabric que deve tentar recuperar uma partição específica que está atualmente paralisada na perda de quorum.

Esta operação somente deve ser realizada se souber que as réplicas inativas não podem ser recuperadas. O uso incorreto dessa API pode causar possíveis perdas de dados.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --partition-id [Obrigatório] | A identidade da partição. |
| --timeout -t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, table, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-partition-recover-all"></a>sfctl partition recover-all
Indica ao cluster do Service Fabric que ele deve tentar recuperar quaisquer serviços (incluindo serviços do sistema) que estão atualmente paralisados na perda de quorum.

Esta operação somente deve ser realizada se souber que as réplicas inativas não podem ser recuperadas. O uso incorreto dessa API pode causar possíveis perdas de dados.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --timeout -t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, table, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-partition-report-health"></a>sfctl partition report-health
Envia um relatório de integridade na partição do Service Fabric.

Relata o estado de integridade da partição do Service Fabric especificada. O relatório deve conter as informações sobre a origem do relatório de integridade e a propriedade na qual ele será relatado. O relatório é enviado a uma Partição de gateway do Service Fabric, que encaminha para o repositório de integridade. O relatório pode ser aceito pelo gateway, mas rejeitado pelo repositório de integridade após validação adicional. Por exemplo, o repositório de integridade pode rejeitar o relatório devido a um parâmetro inválido, como um número de sequência obsoleto. Para ver se o relatório foi aplicado no repositório de integridade, verifique se o relatório é exibido na seção de eventos.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --health-property [Obrigatório] | A propriedade das informações de integridade. <br><br> Uma entidade pode ter relatórios de integridade para propriedades diferentes. A propriedade é uma cadeia de caracteres e não uma enumeração fixa para permitir ao relator flexibilidade para categorizar a condição de estado que dispara o relatório. Por exemplo, um relator com SourceId "LocalWatchdog" pode monitorar o estado do disco disponível em um nó, para poder relatar a propriedade "AvailableDisk" nesse nó. O mesmo relator pode monitorar a conectividade do nó, para que ele possa relatar a "Conectividade" de uma propriedade no mesmo nó. No repositório de integridade, esses relatórios são tratados como eventos de integridade distintos para o nó especificado. Junto com a SourceId, a propriedade identifica exclusivamente as informações de integridade. |
| --health-state    [Obrigatório] | Os valores possíveis são\: 'Invalid', 'Ok', 'Warning', 'Error', 'Unknown'. |
| --partition-id [Obrigatório] | A identidade da partição. |
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

## <a name="sfctl-partition-restart"></a>sfctl partition restart
Essa API reiniciará algumas ou todas as réplicas ou instâncias da partição especificada.

Essa API é útil para teste de failover. Se usada para segmentar uma partição de serviço sem estado, RestartPartitionMode deverá ser AllReplicasOrInstances. Chame a API de GetPartitionRestartProgress utilizando a mesma OperationId para obter o progresso.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --operation-id           [Obrigatório] | Um GUID que identifica uma chamada dessa API.  Isso é passado para a API GetProgress correspondente. |
| --partition-id           [Obrigatório] | A identidade da partição. |
| --restart-partition-mode [Obrigatório] | Descreva quais partições devem ser reiniciadas. |
| --service-id             [Obrigatório] | A identidade do serviço. Essa ID normalmente é o nome completo do serviço sem o ' malha\:' esquema de URI. A partir da versão 6.0, nomes hierárquicos são delimitados pelo caractere "\~". Por exemplo, se o nome do serviço for "fabric\:/myapp/app1/svc1", a identidade do serviço será "myapp\~app1\~svc1" na versão 6.0 e superiores, e "myapp/app1/svc1" em versões anteriores. |
| --timeout -t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, table, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-partition-restart-status"></a>sfctl partition restart-status
Obtém o progresso de uma operação PartitionRestart iniciada utilizando StartPartitionRestart.

Obtém o progresso de uma PartitionRestart iniciada utilizando StartPartitionRestart e usando a OperationId fornecida.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --id da operação [requerido] | Um GUID que identifica uma chamada dessa API.  Isso é passado para a API GetProgress correspondente. |
| --partition-id [Obrigatório] | A identidade da partição. |
| --service-id          [Obrigatório] | A identidade do serviço. Essa ID normalmente é o nome completo do serviço sem o ' malha\:' esquema de URI. A partir da versão 6.0, nomes hierárquicos são delimitados pelo caractere "\~". Por exemplo, se o nome do serviço for "fabric\:/myapp/app1/svc1", a identidade do serviço será "myapp\~app1\~svc1" na versão 6.0 e superiores, e "myapp/app1/svc1" em versões anteriores. |
| --timeout -t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, table, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-partition-svc-name"></a>sfctl partition svc-name
Obtém o nome do serviço do Service Fabric para uma partição.

Obtém o nome do serviço para a partição especificada. Um erro 404 retornará se a ID da partição não existir no cluster.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --partition-id [Obrigatório] | A identidade da partição. |
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
