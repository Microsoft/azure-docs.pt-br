---
title: CLI do Azure Service Fabric – aplicativo sfctl | Microsoft Docs
description: Descreve os comandos do aplicativo sfctl da CLI do Service Fabric.
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
ms.openlocfilehash: d4fec5d8131d269d3df229360066452c37a92430
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60837480"
---
# <a name="sfctl-application"></a>aplicativo sfctl
Criar, excluir e gerenciar aplicativos e tipos de aplicativo.

## <a name="commands"></a>Comandos

|Comando|DESCRIÇÃO|
| --- | --- |
| create | Crie um aplicativo do Service Fabric usando a descrição especificada. |
| excluir | Exclui um aplicativo existente do Service Fabric. |
| deployed | Obtém as informações sobre um aplicativo implantado em um nó do Service Fabric. |
| deployed-health | Obtém as informações sobre a integridade de um aplicativo implantado em um nó do Service Fabric. |
| deployed-list | Obtém a lista de aplicativos implantados em um nó do Service Fabric. |
| integridade | Obtém a integridade do aplicativo do service fabric. |
| informações | Obtém informações sobre um aplicativo do Service Fabric. |
| list | Obtém a lista de aplicativos criados no cluster do Service Fabric que correspondem aos filtros especificados. |
| load | Obtém informações de carregamento sobre um aplicativo do Service Fabric. |
| manifest | Obtém o manifesto que descreve um tipo de aplicativo. |
| provision | Provisiona ou registra um tipo de aplicativo do Service Fabric com o cluster usando o pacote .sfpkg no armazenamento externo ou o pacote de aplicativos no repositório de imagens. |
| report-health | Envia um relatório de integridade sobre o aplicativo do Service Fabric. |
| Tipo | Obtém a lista de tipos de aplicativo criados no cluster do Service Fabric que correspondem exatamente ao nome especificado. |
| type-list | Obtém a lista de tipos de aplicativo criados no cluster do Service Fabric. |
| unprovision | Remove ou cancela o registro de um tipo de aplicativo do Service Fabric do cluster. |
| atualizar | Começa a atualização de um aplicativo no cluster do Service Fabric. |
| upgrade-resume | Retoma a atualização de um aplicativo no cluster do Service Fabric. |
| upgrade-rollback | Começa a reversão da atualização em andamento no momento de um aplicativo no cluster do Service Fabric. |
| upgrade-status | Obtém detalhes para a atualização mais recente realizada neste aplicativo. |
| upload | Copia um pacote de aplicativos do Service Fabric para o repositório de imagens. |

## <a name="sfctl-application-create"></a>sfctl application create
Crie um aplicativo do Service Fabric usando a descrição especificada.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --app-name    [Obrigatório] | O nome do aplicativo, incluindo o esquema de URI 'fabric\:'. |
| --app-type    [Obrigatório] | O nome do tipo de aplicativo encontrado no manifesto do aplicativo. |
| --app-version [Obrigatório] | A versão do tipo de aplicativo, conforme definido no manifesto do aplicativo. |
| --max-node-count | O número máximo de nós que o Service Fabric reservará como capacidade para este aplicativo. Observe que isso não significa que os serviços desse aplicativo serão colocados em todos os nós. |
| --metrics | Uma lista codificada em JSON das descrições de métricas de capacidade do aplicativo. Uma métrica é definida como um nome associado a um conjunto de recursos para cada nó no qual o aplicativo existe. |
| --min-node-count | O número mínimo de nós que o Service Fabric reservará como capacidade para este aplicativo. Observe que isso não significa que os serviços desse aplicativo serão colocados em todos os nós. |
| --parameters | Uma lista codificada em JSON de substituições de parâmetro de aplicativo a serem aplicadas durante a criação do aplicativo. |
| --timeout -t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, table, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-application-delete"></a>sfctl application delete
Exclui um aplicativo existente do Service Fabric.

Um aplicativo deve ser criado antes que possa ser excluído. A exclusão de um aplicativo excluirá todos os serviços que fazem parte desse aplicativo. Por padrão, o Service Fabric irá fechar réplicas do serviço normalmente e, depois, excluir o serviço. No entanto, se um serviço estiver com problemas para fechar a réplica normalmente, a operação de exclusão poderá demorar muito tempo ou travar. Use o sinalizador opcional ForceRemove para ignorar a sequência de fechamento normal e forçar a exclusão do aplicativo e de todos os seus serviços.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --application-id [Obrigatório] | A identidade do aplicativo. Normalmente, este é o nome completo do aplicativo sem o esquema de URI "fabric\:". A partir da versão 6.0, nomes hierárquicos são delimitados pelo caractere "\~". Por exemplo, se o nome do aplicativo for "fabric\:/meuaplicativo/aplicativo1", a identidade do aplicativo será "meuaplicativo\~aplicativo1" na versão 6.0 e superiores, e "meuaplicativo/aplicativo1" nas versões anteriores. |
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

## <a name="sfctl-application-deployed"></a>sfctl application deployed
Obtém as informações sobre um aplicativo implantado em um nó do Service Fabric.

Essa consulta retorna informações do aplicativo do sistema se a ID do aplicativo fornecida é para o aplicativo do sistema. Os resultados abrangem aplicativos implantados nos estados ativo, ativando e baixando. Essa consulta requer que o nome do nó corresponda a um nó no cluster. A consulta falhará se o nome do nó fornecido não apontar para qualquer nó do Service Fabric ativo no cluster.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --application-id [Obrigatório] | A identidade do aplicativo. Normalmente, este é o nome completo do aplicativo sem o esquema de URI "fabric\:". A partir da versão 6.0, nomes hierárquicos são delimitados pelo caractere "\~". Por exemplo, se o nome do aplicativo for "fabric\:/meuaplicativo/aplicativo1", a identidade do aplicativo será "meuaplicativo\~aplicativo1" na versão 6.0 e superiores, e "meuaplicativo/aplicativo1" nas versões anteriores. |
| --node-name      [Obrigatório] | O nome do nó. |
| --include-health-state | Inclua o estado de integridade de uma entidade. Se esse parâmetro for definido como false ou não especificado, o estado de integridade retornado será "Desconhecido". Quando definido como true, a consulta será paralela ao nó e ao serviço do sistema de integridade, antes que os resultados sejam mesclados. Consequentemente, a consulta é mais onerosa e pode levar mais tempo. |
| --timeout -t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, table, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-application-deployed-health"></a>sfctl application deployed-health
Obtém as informações sobre a integridade de um aplicativo implantado em um nó do Service Fabric.

Obtém as informações sobre a integridade de um aplicativo implantado em um nó do Service Fabric. Use EventsHealthStateFilter para filtrar opcionalmente a coleção de objetos HealthEvent relatada em um aplicativo implantado com base no estado de integridade. Use DeployedServicePackagesHealthStateFilter para filtrar opcionalmente para filhos de DeployedServicePackageHealth com base no estado de integridade.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --application-id                     [Obrigatório] | A identidade do aplicativo. Normalmente, este é o nome completo do aplicativo sem o esquema de URI "fabric\:". A partir da versão 6.0, nomes hierárquicos são delimitados pelo caractere "\~". Por exemplo, se o nome do aplicativo for "fabric\:/meuaplicativo/aplicativo1", a identidade do aplicativo será "meuaplicativo\~aplicativo1" na versão 6.0 e superiores, e "meuaplicativo/aplicativo1" nas versões anteriores. |
| --node-name                          [Obrigatório] | O nome do nó. |
| --deployed-service-packages-health-state-filter | Permite filtrar os objetos de estado de integridade do pacote de serviços implantado retornados no resultado da consulta de integridade do aplicativo implantado com base no estado de integridade. Os valores possíveis para esse parâmetro incluem o valor de inteiro de um dos seguintes estados de integridade. Serão retornados somente os pacotes de serviços implantados que corresponderem ao filtro. Todos os pacotes de serviços implantados são usados para avaliar o estado de integridade agregado do aplicativo implantado. Se não for especificado, retorna todas as entradas. Os valores de estado são enumerações baseadas no sinalizador. Assim, o valor pode ser uma combinação desses valores obtidos, usando o operador “OR” bit a bit. Por exemplo, se o valor fornecido for "6", será retornado o estado de integridade dos pacotes de serviços com um valor OK (2) e Warning (4) de HealthState.  <br> – Default – Valor padrão. Corresponde a qualquer HealthState. O valor é zero.  <br> -None- Filtro que não corresponde a qualquer valor de HealthState. Usado para não retornar qualquer resultado em um determinado conjunto de estados. O valor é 1.  <br> - Ok - Filtro que corresponde à entrada com o valor de HealthState Ok. O valor é 2.  <br> -Warning- Filtro que corresponde à entrada com o valor de HealthState Warning. O valor é 4.  <br> -Error- Filtro que corresponde a entrada com o valor de HealthState Error. O valor é 8.  <br> - All - Filtro que corresponde à entrada com qualquer valor de HealthState. O valor é 65535. |
| --events-health-state-filter | Permite filtrar a coleção de objetos HealthEvent retornados com base no estado de integridade. Os valores possíveis para esse parâmetro incluem o valor de inteiro de um dos seguintes estados de integridade. Somente os eventos que correspondem ao filtro são retornados. Todos os eventos são usados para avaliar o estado de integridade agregado. Se não for especificado, retorna todas as entradas. Os valores de estado são enumerações baseadas no sinalizador. Assim, o valor pode ser uma combinação desses valores obtidos, usando o operador “OR” bit a bit. Por exemplo, se o valor fornecido for 6, serão retornados todos os eventos com o valor de HealthState de OK (2) e de Aviso (4).  <br> – Default – Valor padrão. Corresponde a qualquer HealthState. O valor é zero.  <br> -None- Filtro que não corresponde a qualquer valor de HealthState. Usado para não retornar qualquer resultado em um determinado conjunto de estados. O valor é 1.  <br> - Ok - Filtro que corresponde à entrada com o valor de HealthState Ok. O valor é 2.  <br> -Warning- Filtro que corresponde à entrada com o valor de HealthState Warning. O valor é 4.  <br> -Error- Filtro que corresponde a entrada com o valor de HealthState Error. O valor é 8.  <br> - All - Filtro que corresponde à entrada com qualquer valor de HealthState. O valor é 65535. |
| --exclude-health-statistics | Indica se as estatísticas de integridade devem ser retornadas como parte do resultado da consulta. Falso por padrão. As estatísticas mostram o número de entidades filhas nos estados de integridade Ok, Warning e Error. |
| --timeout -t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, table, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-application-deployed-list"></a>sfctl application deployed-list
Obtém a lista de aplicativos implantados em um nó do Service Fabric.

Obtém a lista de aplicativos implantados em um nó do Service Fabric. Os resultados não incluem informações sobre aplicativos de sistema implantados, a menos que explicitamente consultados por ID. Os resultados abrangem aplicativos implantados nos estados ativo, ativando e baixando. Essa consulta requer que o nome do nó corresponda a um nó no cluster. A consulta falhará se o nome do nó fornecido não apontar para qualquer nó do Service Fabric ativo no cluster.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --node-name [Obrigatório] | O nome do nó. |
| --continuation-token | O parâmetro do token de continuação é usado para obter o próximo conjunto de resultados. Um token de continuação com um valor não vazio será incluso na resposta da API quando os resultados do sistema não couberem em uma única resposta. Quando esse valor for passado para a próxima chamada de API, a API retornará o próximo conjunto de resultados. Se não houver mais resultados, o token de continuação não conterá um valor. O valor desse parâmetro não deve ser codificado em URL. |
| --include-health-state | Inclua o estado de integridade de uma entidade. Se esse parâmetro for definido como false ou não especificado, o estado de integridade retornado será "Desconhecido". Quando definido como true, a consulta será paralela ao nó e ao serviço do sistema de integridade, antes que os resultados sejam mesclados. Consequentemente, a consulta é mais onerosa e pode levar mais tempo. |
| --max-results | O número máximo de resultados a serem retornados como parte das consultas paginadas. Esse parâmetro define o limite superior no número de resultados retornados. Os resultados retornados podem ser inferiores aos resultados máximos especificados se não couberem na mensagem, de acordo com as restrições de tamanho máximo de mensagem definidas na configuração. Se esse parâmetro for zero, ou não for especificado, a consulta paginada incluirá o máximo de resultados possível na mensagem de retorno. |
| --timeout -t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, table, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-application-health"></a>sfctl application health
Obtém a integridade do aplicativo do service fabric.

Retorna o estado de integridade do aplicativo do service fabric. A resposta mostra o estado de integridade Ok, Erro ou Aviso. Se a entidade não for encontrada no repositório de integridade, a consulta retornará como Erro.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --application-id                 [Obrigatório] | A identidade do aplicativo. Normalmente, este é o nome completo do aplicativo sem o esquema de URI "fabric\:". A partir da versão 6.0, nomes hierárquicos são delimitados pelo caractere "\~". Por exemplo, se o nome do aplicativo for "fabric\:/meuaplicativo/aplicativo1", a identidade do aplicativo será "meuaplicativo\~aplicativo1" na versão 6.0 e superiores, e "meuaplicativo/aplicativo1" nas versões anteriores. |
| --deployed-applications-health-state-filter | Permite filtrar os objetos de estado de integridade dos aplicativos implantados retornados no resultado da consulta de integridade do aplicativo com base no estado de integridade. Os valores possíveis para esse parâmetro incluem o valor de inteiro de um dos seguintes estados de integridade. Serão retornados apenas os aplicativos implantados que corresponderem ao filtro. Todos os aplicativos implantados são usados para avaliar o estado de integridade agregado. Se não for especificado, retorna todas as entradas. Os valores de estado são enumerações baseadas no sinalizador. Assim, o valor pode ser uma combinação desses valores obtidos, usando o operador 'OR' bit a bit. Por exemplo, se o valor fornecido for 6, será retornado o estado de integridade dos aplicativos implantados com um valor OK (2) e Warning (4) de HealthState.  <br> – Default – Valor padrão. Corresponde a qualquer HealthState. O valor é zero.  <br> -None- Filtro que não corresponde a qualquer valor de HealthState. Usado para não retornar qualquer resultado em um determinado conjunto de estados. O valor é 1.  <br> - Ok - Filtro que corresponde à entrada com o valor de HealthState Ok. O valor é 2.  <br> -Warning- Filtro que corresponde à entrada com o valor de HealthState Warning. O valor é 4.  <br> -Error- Filtro que corresponde a entrada com o valor de HealthState Error. O valor é 8.  <br> - All - Filtro que corresponde à entrada com qualquer valor de HealthState. O valor é 65535. |
| --events-health-state-filter | Permite filtrar a coleção de objetos HealthEvent retornados com base no estado de integridade. Os valores possíveis para esse parâmetro incluem o valor de inteiro de um dos seguintes estados de integridade. Somente os eventos que correspondem ao filtro são retornados. Todos os eventos são usados para avaliar o estado de integridade agregado. Se não for especificado, retorna todas as entradas. Os valores de estado são enumerações baseadas no sinalizador. Assim, o valor pode ser uma combinação desses valores obtidos, usando o operador “OR” bit a bit. Por exemplo, se o valor fornecido for 6, serão retornados todos os eventos com o valor de HealthState de OK (2) e de Aviso (4).  <br> – Default – Valor padrão. Corresponde a qualquer HealthState. O valor é zero.  <br> -None- Filtro que não corresponde a qualquer valor de HealthState. Usado para não retornar qualquer resultado em um determinado conjunto de estados. O valor é 1.  <br> - Ok - Filtro que corresponde à entrada com o valor de HealthState Ok. O valor é 2.  <br> -Warning- Filtro que corresponde à entrada com o valor de HealthState Warning. O valor é 4.  <br> -Error- Filtro que corresponde a entrada com o valor de HealthState Error. O valor é 8.  <br> - All - Filtro que corresponde à entrada com qualquer valor de HealthState. O valor é 65535. |
| --exclude-health-statistics | Indica se as estatísticas de integridade devem ser retornadas como parte do resultado da consulta. Falso por padrão. As estatísticas mostram o número de entidades filhas nos estados de integridade Ok, Warning e Error. |
| --services-health-state-filter | Permite filtrar os objetos de estado de integridade de serviços retornados no resultado da consulta de integridade de serviços com base no estado de integridade. Os valores possíveis para esse parâmetro incluem o valor de inteiro de um dos seguintes estados de integridade. Serão retornados somente os serviços que corresponderem ao filtro. Todos os serviços são usados para avaliar o estado de integridade agregado. Se não for especificado, retorna todas as entradas. Os valores de estado são enumerações baseadas no sinalizador. Assim, o valor pode ser uma combinação desses valores obtidos, usando o operador 'OR' bit a bit. Por exemplo, se o valor fornecido for 6, será retornado o estado de integridade dos serviços com um valor OK (2) e Warning (4) de HealthState.  <br> – Default – Valor padrão. Corresponde a qualquer HealthState. O valor é zero.  <br> -None- Filtro que não corresponde a qualquer valor de HealthState. Usado para não retornar qualquer resultado em um determinado conjunto de estados. O valor é 1.  <br> - Ok - Filtro que corresponde à entrada com o valor de HealthState Ok. O valor é 2.  <br> -Warning- Filtro que corresponde à entrada com o valor de HealthState Warning. O valor é 4.  <br> -Error- Filtro que corresponde a entrada com o valor de HealthState Error. O valor é 8.  <br> - All - Filtro que corresponde à entrada com qualquer valor de HealthState. O valor é 65535. |
| --timeout -t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, table, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-application-info"></a>sfctl application info
Obtém informações sobre um aplicativo do Service Fabric.

Retorna as informações sobre o aplicativo que foi criado, ou que está sendo criado no cluster do Service Fabric, e cujo nome corresponde àquele especificado como o parâmetro. A resposta inclui o nome, o tipo, o status, os parâmetros e outros detalhes sobre o aplicativo.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --application-id      [Obrigatório] | A identidade do aplicativo. Normalmente, este é o nome completo do aplicativo sem o esquema de URI "fabric\:". A partir da versão 6.0, nomes hierárquicos são delimitados pelo caractere "\~". Por exemplo, se o nome do aplicativo for "fabric\:/meuaplicativo/aplicativo1", a identidade do aplicativo será "meuaplicativo\~aplicativo1" na versão 6.0 e superiores, e "meuaplicativo/aplicativo1" nas versões anteriores. |
| --exclude-application-parameters | O sinalizador que especifica se os parâmetros do aplicativo serão excluídos do resultado. |
| --timeout -t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, table, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-application-list"></a>sfctl application list
Obtém a lista de aplicativos criados no cluster do Service Fabric que correspondem aos filtros especificados.

Obtém as informações sobre os aplicativos que foram criados, ou que estão sendo criados no cluster do Service Fabric, e que correspondem aos filtros especificados. A resposta inclui o nome, o tipo, o status, os parâmetros e outros detalhes sobre o aplicativo. Se os aplicativos não couberem em uma página, uma página de resultados retornará junto com um token de continuação, que poderá ser usado para acessar a próxima página. Os filtros ApplicationTypeName e ApplicationDefinitionKindFilter não podem ser especificados ao mesmo tempo.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --application-definition-kind-filter | Usado para filtrar ApplicationDefinitionKind, que é o mecanismo usado para definir um aplicativo do Service Fabric.  <br> – Default – Valor padrão, que executa a mesma função que selecionar "All". O valor é 0.  <br> – All – Filtro que corresponde à entrada com qualquer valor ApplicationDefinitionKind. O valor é 65535.  <br> – ServiceFabricApplicationDescription – Filtro que corresponde à entrada com o valor ApplicationDefinitionKind de ServiceFabricApplicationDescription. O valor é 1.  <br> – Compose – Filtro que corresponde à entrada com o valor ApplicationDefinitionKind de Compose. O valor é 2. |
| --application-type-name | O nome do tipo de aplicativo usado para filtrar os aplicativos para consulta. Esse valor não deve conter a versão do tipo de aplicativo. |
| --continuation-token | O parâmetro do token de continuação é usado para obter o próximo conjunto de resultados. Um token de continuação com um valor não vazio será incluso na resposta da API quando os resultados do sistema não couberem em uma única resposta. Quando esse valor for passado para a próxima chamada de API, a API retornará o próximo conjunto de resultados. Se não houver mais resultados, o token de continuação não conterá um valor. O valor desse parâmetro não deve ser codificado em URL. |
| --exclude-application-parameters | O sinalizador que especifica se os parâmetros do aplicativo serão excluídos do resultado. |
| --max-results | O número máximo de resultados a serem retornados como parte das consultas paginadas. Esse parâmetro define o limite superior no número de resultados retornados. Os resultados retornados podem ser inferiores aos resultados máximos especificados se não couberem na mensagem, de acordo com as restrições de tamanho máximo de mensagem definidas na configuração. Se esse parâmetro for zero, ou não for especificado, a consulta paginada incluirá o máximo de resultados possível na mensagem de retorno. |
| --timeout -t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, table, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-application-load"></a>sfctl application load
Obtém informações de carregamento sobre um aplicativo do Service Fabric.

Retorna as informações de carga sobre o aplicativo que foi criado ou que estava sendo criado no cluster do Service Fabric e cujo nome corresponde àquele especificado como o parâmetro. A resposta inclui o nome, nós mínimos, nós máximos, o número de nós que o aplicativo está ocupando no momento e as informações de métrica de carga do aplicativo sobre o aplicativo.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --application-id [Obrigatório] | A identidade do aplicativo. Normalmente, este é o nome completo do aplicativo sem o esquema de URI "fabric\:". A partir da versão 6.0, nomes hierárquicos são delimitados pelo caractere "\~". Por exemplo, se o nome do aplicativo for "fabric\:/meuaplicativo/aplicativo1", a identidade do aplicativo será "meuaplicativo\~aplicativo1" na versão 6.0 e superiores, e "meuaplicativo/aplicativo1" nas versões anteriores. |
| --timeout -t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, table, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-application-manifest"></a>sfctl application manifest
Obtém o manifesto que descreve um tipo de aplicativo.

A resposta contém o XML do manifesto do aplicativo como uma cadeia de caracteres.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --application-type-name    [Obrigatório] | O nome do tipo de aplicativo. |
| --application-type-version [Obrigatório] | A versão do tipo de aplicativo. |
| --timeout -t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, table, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-application-provision"></a>sfctl application provision
Provisiona ou registra um tipo de aplicativo do Service Fabric com o cluster usando o pacote .sfpkg no armazenamento externo ou o pacote de aplicativos no repositório de imagens.

Provisiona um tipo de aplicativo do Service Fabric com o cluster. Isso é necessário antes que qualquer aplicativo novo possa ser instanciado. A operação de provisionamento pode ser executada no pacote do aplicativo especificado pelo relativePathInImageStore, ou usando o URI do .sfpkg externo. A menos que --external-provision esteja definido, esse comando irá esperar o provisionamento do repositório de imagens.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --application-package-download-uri | O caminho para o pacote de aplicativos '.sfpkg' de onde o pacote de aplicativos pode ser baixado usando protocolos HTTP ou HTTPS. <br><br> Somente para repositório externo de tipo de provisionamento. O pacote de aplicativos pode ser armazenado em um repositório externo que fornece a operação GET para baixar o arquivo. Os protocolos com suporte são HTTP e HTTPS, e o caminho deve permitir o acesso de LEITURA. |
| --application-type-build-path | Somente para repositório de imagens de tipo de provisionamento. O caminho relativo para o pacote de aplicativo no repositório de imagem especificado durante a operação de carregamento anteriores. |
| --application-type-name | Somente para repositório externo de tipo de provisionamento. O nome do tipo de aplicativo representa o nome do tipo de aplicativo encontrado no manifesto do aplicativo. |
| --application-type-version | Somente para repositório externo de tipo de provisionamento. A versão do tipo de aplicativo representa a versão do tipo de aplicativo encontrado no manifesto do aplicativo. |
| --external-provision | O local de onde o pacote de aplicativos pode ser registrado ou provisionado. Indica que o provisionamento de um pacote de aplicativos que foi carregado anteriormente em um armazenamento externo. O pacote de aplicativos termina com a extensão *.sfpkg. |
| --no-wait | Indica se o provisionamento deve ocorrer assincronamente. <br><br> Quando definido como true, a operação de provisionamento retorna quando a solicitação é aceita pelo sistema e a operação de provisionamento continua sem nenhum tempo limite. O valor padrão é falso. Para pacotes de aplicativos grandes, é recomendável definir o valor como true. |
| --timeout -t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, table, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-application-report-health"></a>sfctl application report-health
Envia um relatório de integridade sobre o aplicativo do Service Fabric.

Relata o estado de integridade do aplicativo Service Fabric especificado. O relatório deve conter as informações sobre a origem do relatório de integridade e a propriedade na qual ele será relatado. O relatório é enviado a um Aplicativo de gateway do Service Fabric, que encaminha para o repositório de integridade. O relatório pode ser aceito pelo gateway, mas rejeitado pelo repositório de integridade após validação adicional. Por exemplo, o repositório de integridade pode rejeitar o relatório devido a um parâmetro inválido, como um número de sequência obsoleto. Para ver se o relatório foi aplicado no repositório de integridade, obtenha a integridade do aplicativo e verifique se o relatório é exibido.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --application-id  [Obrigatório] | A identidade do aplicativo. <br><br> Normalmente, este é o nome completo do aplicativo sem o esquema de URI "fabric\:". A partir da versão 6.0, nomes hierárquicos são delimitados pelo caractere "\~". Por exemplo, se o nome do aplicativo for "fabric\:/meuaplicativo/aplicativo1", a identidade do aplicativo será "meuaplicativo\~aplicativo1" na versão 6.0 e superiores, e "meuaplicativo/aplicativo1" nas versões anteriores. |
| --health-property [Obrigatório] | A propriedade das informações de integridade. <br><br> Uma entidade pode ter relatórios de integridade para propriedades diferentes. A propriedade é uma cadeia de caracteres e não uma enumeração fixa para permitir ao relator flexibilidade para categorizar a condição de estado que dispara o relatório. Por exemplo, um relator com SourceId "LocalWatchdog" pode monitorar o estado do disco disponível em um nó, para poder relatar a propriedade "AvailableDisk" nesse nó. O mesmo relator pode monitorar a conectividade do nó, para que ele possa relatar a "Conectividade" de uma propriedade no mesmo nó. No repositório de integridade, esses relatórios são tratados como eventos de integridade distintos para o nó especificado. Junto com a SourceId, a propriedade identifica exclusivamente as informações de integridade. |
| --health-state    [Obrigatório] | Os valores possíveis são\: 'Invalid', 'Ok', 'Warning', 'Error', 'Unknown'. |
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

## <a name="sfctl-application-type"></a>sfctl application type
Obtém a lista de tipos de aplicativo criados no cluster do Service Fabric que correspondem exatamente ao nome especificado.

Retorna as informações sobre os tipos de aplicativos provisionados, ou que estão sendo provisionados no cluster do Service Fabric. Esses resultados são de tipos de aplicativos cujo nome corresponde exatamente ao especificado como o parâmetro, e que estão em conformidade com os parâmetros de consulta fornecidos. Todas as versões do tipo de aplicativo correspondentes ao nome do tipo de aplicativo retornam, sendo que cada versão retorna como um tipo de aplicativo. A resposta inclui o nome, versão, status e outros detalhes sobre o tipo de aplicativo. Esta é uma consulta paginada, ou seja, se nem todos os aplicativos couberem em uma página, uma página de resultados retornará junto com um token de continuação, que poderá ser usado para acessar a próxima página. Por exemplo, se houver 10 tipos de aplicativo, mas uma página tiver espaço apenas para os três primeiros tipos de aplicativos, ou se o resultado máximo for definido como 3, três tipos retornarão. Para acessar o restante dos resultados, recupere as páginas subsequentes usando o token de continuação retornado na próxima consulta. Um token de continuação vazio será retornado se não houver páginas subsequentes.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --application-type-name [Obrigatório] | O nome do tipo de aplicativo. |
| --application-type-version | A versão do tipo de aplicativo. |
| --continuation-token | O parâmetro do token de continuação é usado para obter o próximo conjunto de resultados. Um token de continuação com um valor não vazio será incluso na resposta da API quando os resultados do sistema não couberem em uma única resposta. Quando esse valor for passado para a próxima chamada de API, a API retornará o próximo conjunto de resultados. Se não houver mais resultados, o token de continuação não conterá um valor. O valor desse parâmetro não deve ser codificado em URL. |
| --exclude-application-parameters | O sinalizador que especifica se os parâmetros do aplicativo serão excluídos do resultado. |
| --max-results | O número máximo de resultados a serem retornados como parte das consultas paginadas. Esse parâmetro define o limite superior no número de resultados retornados. Os resultados retornados podem ser inferiores aos resultados máximos especificados se não couberem na mensagem, de acordo com as restrições de tamanho máximo de mensagem definidas na configuração. Se esse parâmetro for zero, ou não for especificado, a consulta paginada incluirá o máximo de resultados possível na mensagem de retorno. |
| --timeout -t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, table, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-application-type-list"></a>sfctl application type-list
Obtém a lista de tipos de aplicativo criados no cluster do Service Fabric.

Retorna as informações sobre os tipos de aplicativos provisionados, ou que estão sendo provisionados no cluster do Service Fabric. Cada versão de um tipo de aplicativo é retornada como um tipo de aplicativo. A resposta inclui o nome, versão, status e outros detalhes sobre o tipo de aplicativo. Esta é uma consulta paginada, ou seja, se nem todos os aplicativos couberem em uma página, uma página de resultados retornará junto com um token de continuação, que poderá ser usado para acessar a próxima página. Por exemplo, se houver 10 tipos de aplicativo, mas uma página tiver espaço apenas para os três primeiros tipos de aplicativos, ou se o resultado máximo for definido como 3, três tipos retornarão. Para acessar o restante dos resultados, recupere as páginas subsequentes usando o token de continuação retornado na próxima consulta. Um token de continuação vazio será retornado se não houver páginas subsequentes.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --application-type-definition-kind-filter | Usado para filtrar ApplicationTypeDefinitionKind, que é o mecanismo usado para definir um tipo de aplicativo do Service Fabric.  <br> – Default – Valor padrão, que executa a mesma função que selecionar "All". O valor é 0.  <br> – All – Filtro que corresponde à entrada com qualquer valor ApplicationTypeDefinitionKind. O valor é 65535.  <br> – ServiceFabricApplicationPackage – Filtro que corresponde à entrada com ServiceFabricApplicationPackage de valor ApplicationTypeDefinitionKind. O valor é 1.  <br> – Compose – Filtro que corresponde à entrada com Compose de valor ApplicationTypeDefinitionKind. O valor é 2. |
| --continuation-token | O parâmetro do token de continuação é usado para obter o próximo conjunto de resultados. Um token de continuação com um valor não vazio será incluso na resposta da API quando os resultados do sistema não couberem em uma única resposta. Quando esse valor for passado para a próxima chamada de API, a API retornará o próximo conjunto de resultados. Se não houver mais resultados, o token de continuação não conterá um valor. O valor desse parâmetro não deve ser codificado em URL. |
| --exclude-application-parameters | O sinalizador que especifica se os parâmetros do aplicativo serão excluídos do resultado. |
| --max-results | O número máximo de resultados a serem retornados como parte das consultas paginadas. Esse parâmetro define o limite superior no número de resultados retornados. Os resultados retornados podem ser inferiores aos resultados máximos especificados se não couberem na mensagem, de acordo com as restrições de tamanho máximo de mensagem definidas na configuração. Se esse parâmetro for zero, ou não for especificado, a consulta paginada incluirá o máximo de resultados possível na mensagem de retorno. |
| --timeout -t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, table, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-application-unprovision"></a>sfctl application unprovision
Remove ou cancela o registro de um tipo de aplicativo do Service Fabric do cluster.

Esta operação só pode ser executada se todas as instâncias de aplicativo do tipo de aplicativo tiverem sido excluídas. Após o cancelamento do registro do tipo de aplicativo, nenhuma instância nova do aplicativo poderá ser criada para este tipo específico de aplicativo.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --application-type-name    [Obrigatório] | O nome do tipo de aplicativo. |
| --application-type-version [Obrigatório] | A versão do tipo de aplicativo, conforme definido no manifesto do aplicativo. |
| --async-parameter | O sinalizador que indica se o desprovisionamento deve ocorrer de modo assíncrono ou não. Quando definido como true, a operação de desprovisionamento retorna quando a solicitação é aceita pelo sistema e a operação de desprovisionamento continua sem nenhum tempo limite. O valor padrão é falso. No entanto, recomendamos que você defina-o como true para pacotes de aplicativos grandes que foram provisionados. |
| --timeout -t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, table, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-application-upgrade"></a>sfctl application upgrade
Começa a atualização de um aplicativo no cluster do Service Fabric.

Valida os parâmetros de atualização de aplicativo fornecidos e começa a atualizar o aplicativo se os parâmetros forem válidos. Observe que a descrição de atualização substitui a descrição do aplicativo existente. Isso significa que, se o parâmetro não for especificado, os parâmetros existentes nos aplicativos serão substituídos pela lista de parâmetros vazia. Isso faria com que o aplicativo usasse o valor padrão dos parâmetros do manifesto de aplicativo.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --application-id      [Obrigatório] | A identidade do aplicativo. <br><br> Normalmente, este é o nome completo do aplicativo sem o esquema de URI "fabric\:". A partir da versão 6.0, nomes hierárquicos são delimitados pelo caractere "\~". Por exemplo, se o nome do aplicativo for "fabric\:/meuaplicativo/aplicativo1", a identidade do aplicativo será "meuaplicativo\~aplicativo1" na versão 6.0 e superiores, e "meuaplicativo/aplicativo1" nas versões anteriores. |
| --application-version [Obrigatório] | A versão do tipo de aplicativo de destino (encontrada no manifesto do aplicativo) para a atualização do aplicativo. |
| --parameters          [Obrigatório] | Uma lista codificada em JSON de substituições de parâmetro de aplicativo a serem aplicadas ao atualizar o aplicativo. |
| --default-service-health-policy | Especificação codificada em JSON da política de integridade usada por padrão para avaliar a integridade de um tipo de serviço. |
| --ação de falha | A ação a ser executada quando uma atualização monitorada encontra uma política de monitoramento ou violações da política de integridade. |
| -força-reinicialização | Reinicia processos de modo forçado durante a atualização mesmo quando a versão do código não foi alterada. |
| --health-check-retry-timeout | O período entre as tentativas de realizar verificações de integridade se o aplicativo ou o cluster não estiver íntegro.  Padrão\: PT0H10M0S. |
| --health-check-stable-duration | A quantidade de tempo que o aplicativo ou o cluster deve permanecer íntegro antes que a atualização passe para o próximo domínio de atualização.  Padrão\: PT0H2M0S. <br><br> Primeiro, é interpretado como uma cadeia de caracteres representando uma duração ISO 8601. Se isso falhar, será interpretado como um número que representa o número total de milissegundos. |
| --health-check-wait-duration | O período de espera após a conclusão de um domínio de atualização, antes de iniciar o processo de verificações de integridade.  Padrão\: 0. |
| --max-unhealthy-apps | O percentual máximo permitido de aplicativos não íntegros implantados. Representado como um número entre 0 e 100. |
| --mode | O modo usado para monitorar a integridade durante uma atualização sem interrupção.  Padrão\: UnmonitoredAuto. |
| --replica-set-check-timeout | A quantidade máxima de tempo para bloqueio do processamento de um domínio de atualização e prevenção da perda de disponibilidade quando houver problemas inesperados. Medido em segundos. |
| --service-health-policy | Mapa codificado em JSON com a política de integridade de tipo de serviço por nome de tipo de serviço. O mapa está vazio por padrão. |
| --timeout -t | Tempo limite do servidor em segundos.  Padrão\: 60. |
| -tempo limite ao domínio de upgrade | A quantidade de tempo que cada domínio de atualização deve concluir antes de FailureAction ser executado.  Padrão\: P10675199DT02H48M05.4775807S. <br><br> Primeiro, é interpretado como uma cadeia de caracteres representando uma duração ISO 8601. Se isso falhar, será interpretado como um número que representa o número total de milissegundos. |
| -tempo limite de upgrade | A quantidade de tempo que a atualização geral deve concluir antes de FailureAction ser executado.  Padrão\: P10675199DT02H48M05.4775807S. <br><br> Primeiro, é interpretado como uma cadeia de caracteres representando uma duração ISO 8601. Se isso falhar, será interpretado como um número que representa o número total de milissegundos. |
| --aviso-como-erro | Indica se os avisos são ou não tratados com a mesma severidade que os erros. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, table, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-application-upgrade-resume"></a>sfctl application upgrade-resume
Retoma a atualização de um aplicativo no cluster do Service Fabric.

Retoma a atualização manual não monitorada de um aplicativo do Service Fabric. O Service Fabric atualiza um domínio de atualização de cada vez. Para atualizações manuais não monitoradas, depois de concluir um domínio de atualização, o Service Fabric espera você chamar esta API antes de prosseguir para o próximo domínio de atualização.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --application-id      [Obrigatório] | A identidade do aplicativo. Normalmente, este é o nome completo do aplicativo sem o esquema de URI "fabric\:". A partir da versão 6.0, nomes hierárquicos são delimitados pelo caractere "\~". Por exemplo, se o nome do aplicativo for "fabric\:/meuaplicativo/aplicativo1", a identidade do aplicativo será "meuaplicativo\~aplicativo1" na versão 6.0 e superiores, e "meuaplicativo/aplicativo1" nas versões anteriores. |
| --upgrade-domain-name [Obrigatório] | O nome do domínio de atualização no qual continuar a atualização. |
| --timeout -t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, table, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-application-upgrade-rollback"></a>sfctl application upgrade-rollback
Começa a reversão da atualização em andamento no momento de um aplicativo no cluster do Service Fabric.

Inicia a reversão da atualização do aplicativo atual para a versão anterior. Esta API só pode ser usada para reverter a atualização em andamento atual que está efetuando roll forward para a nova versão. Se o aplicativo não está sendo atualizado no momento use a API StartApplicationUpgrade para atualizá-lo para a versão desejada, incluindo a reversão para uma versão anterior.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --application-id [Obrigatório] | A identidade do aplicativo. Normalmente, este é o nome completo do aplicativo sem o esquema de URI "fabric\:". A partir da versão 6.0, nomes hierárquicos são delimitados pelo caractere "\~". Por exemplo, se o nome do aplicativo for "fabric\:/meuaplicativo/aplicativo1", a identidade do aplicativo será "meuaplicativo\~aplicativo1" na versão 6.0 e superiores, e "meuaplicativo/aplicativo1" nas versões anteriores. |
| --timeout -t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, table, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-application-upgrade-status"></a>sfctl application upgrade-status
Obtém detalhes para a atualização mais recente realizada neste aplicativo.

Retorna informações sobre o estado da atualização mais recente do aplicativo juntamente com detalhes para auxiliar na depuração de problemas de integridade do aplicativo.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --application-id [Obrigatório] | A identidade do aplicativo. Normalmente, este é o nome completo do aplicativo sem o esquema de URI "fabric\:". A partir da versão 6.0, nomes hierárquicos são delimitados pelo caractere "\~". Por exemplo, se o nome do aplicativo for "fabric\:/meuaplicativo/aplicativo1", a identidade do aplicativo será "meuaplicativo\~aplicativo1" na versão 6.0 e superiores, e "meuaplicativo/aplicativo1" nas versões anteriores. |
| --timeout -t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-application-upload"></a>sfctl application upload
Copia um pacote de aplicativos do Service Fabric para o repositório de imagens.

Opcionalmente, exiba o progresso do carregamento para cada arquivo no pacote. O progresso do carregamento é enviado para `stderr`.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --path   [Obrigatório] | Caminho até o pacote de aplicativo local. |
| --imagestore-string | Repositório de imagens de destino no qual carregar o pacote de aplicativos.  Padrão\: fabric\:ImageStore. |
| --show-progress | Mostra progresso do carregamento de arquivo para pacotes grandes. |

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
