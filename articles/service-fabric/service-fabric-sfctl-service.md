---
title: CLI do Azure Service Fabric - serviço sfctl | Microsoft Docs
description: Descreve os comandos do serviço sfctl da CLI do Service Fabric.
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
ms.openlocfilehash: e0454d0124efba04434884fbac9056c5e324710d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60556365"
---
# <a name="sfctl-service"></a>Serviço sfctl
Crie, exclua e gerencie o serviço, os tipos de serviço e os pacotes de serviço.

## <a name="commands"></a>Comandos

|Comando|DESCRIÇÃO|
| --- | --- |
| app-name | Obtém o nome do aplicativo do Service Fabric para um serviço. |
| code-package-list | Obtém a lista de pacotes de código implantados em um nó do Service Fabric. |
| create | Cria o serviço de malha do serviço especificado. |
| excluir | Exclui um serviço existente do Service Fabric. |
| deployed-type | Obtém as informações sobre um tipo de serviço especificado do aplicativo implantado em um nó em um cluster do Service Fabric. |
| deployed-type-list | Obtém a lista que contém as informações sobre os tipos de serviço dos aplicativos implantados em um nó em um cluster do Service Fabric. |
| description | Obtém a descrição de um serviço do Service Fabric existente. |
| get-container-logs | Obtém os logs de contêiner para um contêiner implantado em um nó do Service Fabric. |
| integridade | Obtém a integridade do serviço do Service Fabric especificado. |
| informações | Obtém as informações sobre o serviço específico pertencentes ao aplicativo do Service Fabric. |
| list | Obtém as informações sobre todos os serviços pertencentes ao aplicativo especificado pela ID de aplicativo. |
| manifest | Obtém o manifesto que descreve um tipo de serviço. |
| package-deploy | Baixa os pacotes associados ao manifesto de serviço especificado para o cache de imagem no nó especificado. |
| package-health | Obtém as informações sobre a integridade de um pacote de serviço para um aplicativo específico implantado em um nó e em um aplicativo do Service Fabric. |
| package-info | Obtém a lista de pacotes de serviço implantados em um nó do Service Fabric que correspondem exatamente ao nome especificado. |
| package-list | Obtém a lista de pacotes de serviço implantados em um nó do Service Fabric. |
| recover | Indica para o cluster do Service Fabric que ele deve tentar recuperar o serviço especificado no momento está preso em perda de quorum. |
| report-health | Envia um relatório de integridade sobre o serviço do Service Fabric. |
| resolve | Resolve uma partição do Service Fabric. |
| type-list | Obtém a lista que contém as informações sobre os tipos de serviço compatíveis com um tipo de aplicativo provisionado em um cluster do Service Fabric. |
| atualizar | Atualiza o serviço especificado usando a descrição de atualização fornecida. |

## <a name="sfctl-service-app-name"></a>nome de aplicativo do serviço sfctl
Obtém o nome do aplicativo do Service Fabric para um serviço.

Obtém o nome do aplicativo para o serviço especificado. Um erro FABRIC_E_SERVICE_DOES_NOT_EXIST 404 será retornado se um serviço com a ID de serviço fornecido não existe.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --service-id [Obrigatório] | A identidade do serviço. Essa ID normalmente é o nome completo do serviço sem o ' malha\:' esquema de URI. A partir da versão 6.0, nomes hierárquicos são delimitados pelo caractere "\~". Por exemplo, se o nome do serviço for "fabric\:/myapp/app1/svc1", a identidade do serviço será "myapp\~app1\~svc1" na versão 6.0 e superiores, e "myapp/app1/svc1" em versões anteriores. |
| --timeout -t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, table, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-service-code-package-list"></a>sfctl serviço código--lista de pacotes
Obtém a lista de pacotes de código implantados em um nó do Service Fabric.

Obtém a lista de pacotes de código implantado em um nó de malha do serviço para determinado aplicativo.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --application-id [Obrigatório] | A identidade do aplicativo. Normalmente, este é o nome completo do aplicativo sem o esquema de URI "fabric\:". A partir da versão 6.0, nomes hierárquicos são delimitados pelo caractere "\~". Por exemplo, se o nome do aplicativo for "fabric\:/meuaplicativo/aplicativo1", a identidade do aplicativo será "meuaplicativo\~aplicativo1" na versão 6.0 e superiores, e "meuaplicativo/aplicativo1" nas versões anteriores. |
| --node-name      [Obrigatório] | O nome do nó. |
| --code-package-name | O nome do pacote de código especificado no manifesto de serviço registrado como parte de um tipo de aplicativo em um cluster do Service Fabric. |
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

## <a name="sfctl-service-create"></a>sfctl service create
Cria o serviço de malha do serviço especificado.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --app-id       [Obrigatório] | A identidade do aplicativo. Normalmente, este é o nome completo do aplicativo sem o esquema de URI "fabric\:". A partir da versão 6.0, nomes hierárquicos são delimitados pelo caractere "\~". Por exemplo, se o nome do aplicativo é ' malha\:/myapp/app1 ', a identidade do aplicativo deve ser ' myapp\~app1' 6.0 + e ' myapp/app1' nas versões anteriores. |
| --name         [Obrigatório] | Nome do serviço. Deve ser um filho da ID do aplicativo. Este é o nome completo, incluindo o URI `fabric\:`. Por exemplo, o serviço `fabric\:/A/B` é um filho do aplicativo `fabric\:/A`. |
| --service-type [Obrigatório] | O nome do tipo de serviço. |
| --activation-mode | O modo de ativação para o pacote de serviço. |
| --constraints | As restrições de posicionamento como uma cadeia de caracteres. As restrições de posicionamento são expressões boolianas nas propriedades de nó, e permitem restringir um serviço a nós específicos com base nos requisitos do serviço. Por exemplo, para posicionar um serviço em nós nos quais NodeType é azul, especifique o seguinte\: "NodeColor == blue". |
| --correlated-service | Nome do serviço de destino com o qual correlacionar. |
| --correlation | Correlaciona o serviço com um serviço existente usando uma afinidade de alinhamento. |
| --dns-name | O nome DNS do serviço a ser criado. O serviço do sistema de DNS do Service Fabric deve estar habilitado para essa configuração. |
| --instance-count | A contagem de instâncias. Aplica-se apenas a serviços sem estado. |
| --int-scheme | Indica que o serviço deve ser particionado uniformemente em um intervalo de inteiros sem sinal. |
| --int-scheme-count | O número de partições dentro do intervalo de chaves inteiro para criar, se usando um esquema de partição inteiro uniforme. |
| --int-scheme-high | O final do intervalo de chaves de inteiros, se estiver usando um esquema de partição de inteiros uniforme. |
| --int-scheme-low | O final do intervalo de chaves de inteiros, se estiver usando um esquema de partição de inteiros uniforme. |
| --load-metrics | Lista de métricas codificada em JSON, usada ao balancear as cargas de serviços entre nós. |
| --min-replica-set-size | O tamanho mínimo do conjunto de réplicas como um número. Aplica-se apenas a serviços com estado. |
| --move-cost | Especifica o custo da mudança para o serviço. Os valores possíveis são\:: "Zero", "Low", "Medium", "High". |
| --named-scheme | Indica que o serviço deve ter várias partições nomeadas. |
| --named-scheme-list | Lista de nomes codificada em JSON para particionar o serviço, se estiver usando o esquema de partição nomeada. |
| --no-persisted-state | Se verdadeiro, indica que o serviço não tem um estado persistente armazenado no disco local, ou só armazena o estado na memória. |
| --placement-policy-list | Lista codificada por JSON de políticas de posicionamento para o serviço e quaisquer nomes de domínio associados. As políticas podem ser uma ou mais dentre estas: \:, `NonPartiallyPlaceService`, `PreferPrimaryDomain`, `RequireDomain`, `RequireDomainDistribution`. |
| --quorum-loss-wait | A duração máxima, em segundos, durante a qual uma partição é permitida em um estado de perda de quorum. Aplica-se apenas a serviços com estado. |
| --replica-restart-wait | A duração, em segundos, entre a inatividade de uma réplica e a criação de uma nova réplica. Aplica-se apenas a serviços com estado. |
| -políticas de dimensionamento | Lista de políticas para esse serviço de dimensionamento codificados para JSON. |
| --singleton-scheme | Indica que o serviço deve ter uma única partição ou ser um serviço não particionado. |
| --stand-by-replica-keep | A duração máxima, em segundos, para qual espera réplicas serão mantidas antes de serem removidos. Aplica-se apenas a serviços com estado. |
| --stateful | Indica que o serviço tem estado. |
| --stateless | Indica que o serviço não tem estado. |
| --target-replica-set-size | O tamanho do conjunto de réplicas de destino como um número. Aplica-se apenas a serviços com estado. |
| --timeout -t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, table, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-service-delete"></a>sfctl service delete
Exclui um serviço existente do Service Fabric.

Um serviço deve ser criado antes que possa ser excluído. Por padrão, do Service Fabric tentará fechar réplicas do serviço de maneira normal e, em seguida, excluir o serviço. No entanto, se o serviço está tendo problemas para fechar a réplica normalmente, a operação de exclusão pode levar muito tempo ou preso. Use o sinalizador opcional ForceRemove para ignorar a sequência de fechamento normal e forçar a exclusão do serviço.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --service-id [Obrigatório] | A identidade do serviço. Essa ID normalmente é o nome completo do serviço sem o ' malha\:' esquema de URI. A partir da versão 6.0, nomes hierárquicos são delimitados pelo caractere "\~". Por exemplo, se o nome do serviço for "fabric\:/myapp/app1/svc1", a identidade do serviço será "myapp\~app1\~svc1" na versão 6.0 e superiores, e "myapp/app1/svc1" em versões anteriores. |
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

## <a name="sfctl-service-deployed-type"></a>serviço sfctl tipo implantado
Obtém as informações sobre um tipo de serviço especificado do aplicativo implantado em um nó em um cluster do Service Fabric.

Obtém a lista que contém as informações sobre um tipo de serviço específico de aplicativos implantados em um nó em um cluster do Service Fabric. A resposta inclui o nome do tipo de serviço, seu status de registro, o pacote de código que registrou a ele e a ativação de ID do pacote de serviço. Cada entrada representa uma ativação de um tipo de serviço, diferenciada pela ID de ativação.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --application-id      [Obrigatório] | A identidade do aplicativo. Normalmente, este é o nome completo do aplicativo sem o esquema de URI "fabric\:". A partir da versão 6.0, nomes hierárquicos são delimitados pelo caractere "\~". Por exemplo, se o nome do aplicativo for "fabric\:/meuaplicativo/aplicativo1", a identidade do aplicativo será "meuaplicativo\~aplicativo1" na versão 6.0 e superiores, e "meuaplicativo/aplicativo1" nas versões anteriores. |
| -nome de nó [requerido] | O nome do nó. |
| ---nome do tipo serviço [requerido] | Especifica o nome de um tipo de serviço do Service Fabric. |
| --service-manifest-name | O nome do manifesto do serviço para filtrar a lista de informações de tipo de serviço implantado. Se especificado, a resposta conterá apenas as informações sobre os tipos de serviço que são definidos neste manifesto de serviço. |
| --timeout -t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, table, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-service-deployed-type-list"></a>sfctl serviço implantado-lista de tipo
Obtém a lista que contém as informações sobre os tipos de serviço dos aplicativos implantados em um nó em um cluster do Service Fabric.

Obtém a lista que contém as informações sobre os tipos de serviço dos aplicativos implantados em um nó em um cluster do Service Fabric. A resposta inclui o nome do tipo de serviço, seu status de registro, o pacote de código que registrou a ele e a ativação de ID do pacote de serviço.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --application-id [Obrigatório] | A identidade do aplicativo. Normalmente, este é o nome completo do aplicativo sem o esquema de URI "fabric\:". A partir da versão 6.0, nomes hierárquicos são delimitados pelo caractere "\~". Por exemplo, se o nome do aplicativo for "fabric\:/meuaplicativo/aplicativo1", a identidade do aplicativo será "meuaplicativo\~aplicativo1" na versão 6.0 e superiores, e "meuaplicativo/aplicativo1" nas versões anteriores. |
| --node-name      [Obrigatório] | O nome do nó. |
| --service-manifest-name | O nome do manifesto do serviço para filtrar a lista de informações de tipo de serviço implantado. Se especificado, a resposta conterá apenas as informações sobre os tipos de serviço que são definidos neste manifesto de serviço. |
| --timeout -t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, table, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-service-description"></a>sfctl service description
Obtém a descrição de um serviço do Service Fabric existente.

Obtém a descrição de um serviço do Service Fabric existente. É necessário criar um serviço para poder obter sua descrição.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --service-id [Obrigatório] | A identidade do serviço. Essa ID normalmente é o nome completo do serviço sem o ' malha\:' esquema de URI. A partir da versão 6.0, nomes hierárquicos são delimitados pelo caractere "\~". Por exemplo, se o nome do serviço for "fabric\:/myapp/app1/svc1", a identidade do serviço será "myapp\~app1\~svc1" na versão 6.0 e superiores, e "myapp/app1/svc1" em versões anteriores. |
| --timeout -t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, table, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-service-get-container-logs"></a>sfctl get-container-logs de serviço
Obtém os logs de contêiner para um contêiner implantado em um nó do Service Fabric.

Obtém os logs do contêiner para contêiner implantado em um nó de malha do serviço para o pacote de código fornecido.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --application-id      [Obrigatório] | A identidade do aplicativo. Normalmente, este é o nome completo do aplicativo sem o esquema de URI "fabric\:". A partir da versão 6.0, nomes hierárquicos são delimitados pelo caractere "\~". Por exemplo, se o nome do aplicativo for "fabric\:/meuaplicativo/aplicativo1", a identidade do aplicativo será "meuaplicativo\~aplicativo1" na versão 6.0 e superiores, e "meuaplicativo/aplicativo1" nas versões anteriores. |
| -código de-nome do pacote [requerido] | O nome do pacote de código especificado no manifesto de serviço registrado como parte de um tipo de aplicativo em um cluster do Service Fabric. |
| --node-name      [Obrigatório] | O nome do nó. |
| --service-manifest-name    [Obrigatório] | O nome de um manifesto de serviço registrado como parte de um tipo de aplicativo em um cluster do Service Fabric. |
| -anterior | Especifica se deve obter logs de contêiner contêineres/mortas saiu da instância do pacote de código. |
| -final | Número de linhas a serem mostradas do final dos logs. O padrão é 100. 'todos' para mostrar os logs de conclusão. |
| --timeout -t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, table, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-service-health"></a>sfctl service health
Obtém a integridade do serviço do Service Fabric especificado.

Obtém as informações de integridade do serviço especificado. Use EventsHealthStateFilter para filtrar o conjunto de eventos de integridade relatados no serviço de acordo com o estado de integridade. Use PartitionsHealthStateFilter para filtrar o conjunto de partições retornado. Se você especificar um serviço que não existe no repositório de integridade, essa solicitação retornará um erro.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --service-id          [Obrigatório] | A identidade do serviço. Essa ID normalmente é o nome completo do serviço sem o ' malha\:' esquema de URI. A partir da versão 6.0, nomes hierárquicos são delimitados pelo caractere "\~". Por exemplo, se o nome do serviço for "fabric\:/myapp/app1/svc1", a identidade do serviço será "myapp\~app1\~svc1" na versão 6.0 e superiores, e "myapp/app1/svc1" em versões anteriores. |
| --events-health-state-filter | Permite filtrar a coleção de objetos HealthEvent retornados com base no estado de integridade. Os valores possíveis para esse parâmetro incluem o valor de inteiro de um dos seguintes estados de integridade. Somente os eventos que correspondem ao filtro são retornados. Todos os eventos são usados para avaliar o estado de integridade agregado. Se não for especificado, retorna todas as entradas. Os valores de estado são enumerações baseadas no sinalizador. Assim, o valor pode ser uma combinação desses valores obtidos, usando o operador “OR” bit a bit. Por exemplo, se o valor fornecido for 6, serão retornados todos os eventos com o valor de HealthState de OK (2) e de Aviso (4).  <br> – Default – Valor padrão. Corresponde a qualquer HealthState. O valor é zero.  <br> -None- Filtro que não corresponde a qualquer valor de HealthState. Usado para não retornar qualquer resultado em um determinado conjunto de estados. O valor é 1.  <br> - Ok - Filtro que corresponde à entrada com o valor de HealthState Ok. O valor é 2.  <br> -Warning- Filtro que corresponde à entrada com o valor de HealthState Warning. O valor é 4.  <br> -Error- Filtro que corresponde a entrada com o valor de HealthState Error. O valor é 8.  <br> - All - Filtro que corresponde à entrada com qualquer valor de HealthState. O valor é 65535. |
| --exclude-health-statistics | Indica se as estatísticas de integridade devem ser retornadas como parte do resultado da consulta. Falso por padrão. As estatísticas mostram o número de entidades filhas nos estados de integridade Ok, Warning e Error. |
| --partitions-health-state-filter | Permite a filtragem dos objetos de estado de integridade das partições retornados no resultado da consulta de integridade de serviço, com base em seu estado de integridade. Os valores possíveis para esse parâmetro incluem o valor de inteiro de um dos seguintes estados de integridade. Somente as partições que correspondem ao filtro são retornadas. Todas as partições são usadas para avaliar o estado de integridade agregado. Se não for especificado, retorna todas as entradas. Os valores de estado são uma enumeração baseada em sinalizador, então o valor pode ser uma combinação desses valores obtidos usando o operador 'OR' bit a bit. Por exemplo, se o valor fornecido for "6", o estado de integridade das partições com um valor de HealthState OK (2) e Warning (4) retornará.  <br> – Default – Valor padrão. Corresponde a qualquer HealthState. O valor é zero.  <br> -None- Filtro que não corresponde a qualquer valor de HealthState. Usado para não retornar qualquer resultado em um determinado conjunto de estados. O valor é 1.  <br> - Ok - Filtro que corresponde à entrada com o valor de HealthState Ok. O valor é 2.  <br> -Warning- Filtro que corresponde à entrada com o valor de HealthState Warning. O valor é 4.  <br> -Error- Filtro que corresponde a entrada com o valor de HealthState Error. O valor é 8.  <br> - All - Filtro que corresponde à entrada com qualquer valor de HealthState. O valor é 65535. |
| --timeout -t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, table, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-service-info"></a>sfctl service info
Obtém as informações sobre o serviço específico pertencentes ao aplicativo do Service Fabric.

Retorna as informações sobre o serviço especificado pertencentes ao aplicativo de malha do serviço especificado.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --application-id [Obrigatório] | A identidade do aplicativo. Normalmente, este é o nome completo do aplicativo sem o esquema de URI "fabric\:". A partir da versão 6.0, nomes hierárquicos são delimitados pelo caractere "\~". Por exemplo, se o nome do aplicativo for "fabric\:/meuaplicativo/aplicativo1", a identidade do aplicativo será "meuaplicativo\~aplicativo1" na versão 6.0 e superiores, e "meuaplicativo/aplicativo1" nas versões anteriores. |
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

## <a name="sfctl-service-list"></a>sfctl service list
Obtém as informações sobre todos os serviços pertencentes ao aplicativo especificado pela ID de aplicativo.

Retorna as informações sobre todos os serviços pertencentes ao aplicativo especificado pela ID de aplicativo.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --application-id [Obrigatório] | A identidade do aplicativo. Normalmente, este é o nome completo do aplicativo sem o esquema de URI "fabric\:". A partir da versão 6.0, nomes hierárquicos são delimitados pelo caractere "\~". Por exemplo, se o nome do aplicativo for "fabric\:/meuaplicativo/aplicativo1", a identidade do aplicativo será "meuaplicativo\~aplicativo1" na versão 6.0 e superiores, e "meuaplicativo/aplicativo1" nas versões anteriores. |
| --continuation-token | O parâmetro do token de continuação é usado para obter o próximo conjunto de resultados. Um token de continuação com um valor não vazio será incluso na resposta da API quando os resultados do sistema não couberem em uma única resposta. Quando esse valor for passado para a próxima chamada de API, a API retornará o próximo conjunto de resultados. Se não houver mais resultados, o token de continuação não conterá um valor. O valor desse parâmetro não deve ser codificado em URL. |
| --service-type-name | O nome de tipo de serviço usado para filtrar os serviços para consulta. |
| --timeout -t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, table, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-service-manifest"></a>sfctl service manifest
Obtém o manifesto que descreve um tipo de serviço.

Obtém o manifesto que descreve um tipo de serviço. A resposta contém o XML do manifesto do serviço como uma cadeia de caracteres.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --application-type-name    [Obrigatório] | O nome do tipo de aplicativo. |
| --application-type-version [Obrigatório] | A versão do tipo de aplicativo. |
| --service-manifest-name    [Obrigatório] | O nome de um manifesto de serviço registrado como parte de um tipo de aplicativo em um cluster do Service Fabric. |
| --timeout -t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, table, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-service-package-deploy"></a>implantar pacotes de serviço sfctl
Baixa os pacotes associados ao manifesto de serviço especificado para o cache de imagem no nó especificado.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| -aplicativo-nome do tipo [requerido] | O nome do manifesto do aplicativo para o manifesto de serviço solicitado correspondente. |
| --tipo-versão do aplicativo [requerido] | A versão do manifesto do aplicativo para o manifesto de serviço solicitado correspondente. |
| --node-name      [Obrigatório] | O nome do nó. |
| --service-manifest-name    [Obrigatório] | O nome do manifesto de serviço associado aos pacotes que serão baixados. |
| -compartilhamento de política | Lista de políticas de compartilhamento codificados para JSON. Cada elemento de diretiva de compartilhamento é composto de um 'name' e 'escopo'. O nome corresponde ao nome do pacote de código, configuração ou dados que deve ser compartilhado. O escopo pode ser 'None', 'Todos', 'Code', 'Config' ou 'Dados'. |
| --timeout -t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, table, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-service-package-health"></a>pacote de serviço sfctl integridade
Obtém as informações sobre a integridade de um pacote de serviço para um aplicativo específico implantado em um nó e em um aplicativo do Service Fabric.

Obtém as informações sobre a integridade do pacote de serviço para um aplicativo específico implantado em um nó do Microsoft Azure Service Fabric. Use EventsHealthStateFilter para filtrar, opcionalmente, a coleção de objetos HealthEvent relatado no pacote de serviço implantado com base no estado de integridade.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --id do aplicativo [requerido] | A identidade do aplicativo. Normalmente, este é o nome completo do aplicativo sem o esquema de URI "fabric\:". A partir da versão 6.0, nomes hierárquicos são delimitados pelo caractere "\~". Por exemplo, se o nome do aplicativo for "fabric\:/meuaplicativo/aplicativo1", a identidade do aplicativo será "meuaplicativo\~aplicativo1" na versão 6.0 e superiores, e "meuaplicativo/aplicativo1" nas versões anteriores. |
| -nome de nó [requerido] | O nome do nó. |
| -serviço-nome do pacote [requerido] | O nome do pacote de serviço. |
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

## <a name="sfctl-service-package-info"></a>informações de pacote do serviço sfctl
Obtém a lista de pacotes de serviço implantados em um nó do Service Fabric que correspondem exatamente ao nome especificado.

Retorna as informações sobre os pacotes de serviço implantado em um nó de malha do serviço para determinado aplicativo. Esses resultados são de pacotes de serviço cujo nome corresponde exatamente ao nome do pacote de serviço especificados como o parâmetro.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --id do aplicativo [requerido] | A identidade do aplicativo. Normalmente, este é o nome completo do aplicativo sem o esquema de URI "fabric\:". A partir da versão 6.0, nomes hierárquicos são delimitados pelo caractere "\~". Por exemplo, se o nome do aplicativo for "fabric\:/meuaplicativo/aplicativo1", a identidade do aplicativo será "meuaplicativo\~aplicativo1" na versão 6.0 e superiores, e "meuaplicativo/aplicativo1" nas versões anteriores. |
| -nome de nó [requerido] | O nome do nó. |
| -serviço-nome do pacote [requerido] | O nome do pacote de serviço. |
| --timeout -t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, table, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-service-package-list"></a>lista de pacotes de serviço de sfctl
Obtém a lista de pacotes de serviço implantados em um nó do Service Fabric.

Retorna as informações sobre os pacotes de serviço implantado em um nó de malha do serviço para determinado aplicativo.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --application-id [Obrigatório] | A identidade do aplicativo. Normalmente, este é o nome completo do aplicativo sem o esquema de URI "fabric\:". A partir da versão 6.0, nomes hierárquicos são delimitados pelo caractere "\~". Por exemplo, se o nome do aplicativo for "fabric\:/meuaplicativo/aplicativo1", a identidade do aplicativo será "meuaplicativo\~aplicativo1" na versão 6.0 e superiores, e "meuaplicativo/aplicativo1" nas versões anteriores. |
| --node-name      [Obrigatório] | O nome do nó. |
| --timeout -t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, table, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-service-recover"></a>sfctl service recover
Indica para o cluster do Service Fabric que ele deve tentar recuperar o serviço especificado no momento está preso em perda de quorum.

Indica para o cluster do Service Fabric que ele deve tentar recuperar o serviço especificado no momento está preso em perda de quorum. Esta operação somente deve ser realizada se souber que as réplicas inativas não podem ser recuperadas. O uso incorreto dessa API pode causar possíveis perdas de dados.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --service-id [Obrigatório] | A identidade do serviço. Essa ID normalmente é o nome completo do serviço sem o ' malha\:' esquema de URI. A partir da versão 6.0, nomes hierárquicos são delimitados pelo caractere "\~". Por exemplo, se o nome do serviço for "fabric\:/myapp/app1/svc1", a identidade do serviço será "myapp\~app1\~svc1" na versão 6.0 e superiores, e "myapp/app1/svc1" em versões anteriores. |
| --timeout -t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, table, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-service-report-health"></a>relatório de serviço sfctl integridade
Envia um relatório de integridade sobre o serviço do Service Fabric.

Relata o estado de integridade do serviço de malha do serviço especificado. O relatório deve conter as informações sobre a origem do relatório de integridade e propriedade na qual ele será relatado. O relatório é enviado a um serviço, que encaminha para o repositório de integridade de gateway do Service Fabric. O relatório pode ser aceito pelo gateway, mas rejeitado pelo repositório de integridade após validação adicional. Por exemplo, o repositório de integridade pode rejeitar o relatório devido a um parâmetro inválido, como um número de sequência obsoleto. Para ver se o relatório foi aplicado no health store, verifique se o relatório é exibido nos eventos de integridade do serviço.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --health-property [Obrigatório] | A propriedade das informações de integridade. <br><br> Uma entidade pode ter relatórios de integridade para propriedades diferentes. A propriedade é uma cadeia de caracteres e não uma enumeração fixa para permitir ao relator flexibilidade para categorizar a condição de estado que dispara o relatório. Por exemplo, um relator com SourceId "LocalWatchdog" pode monitorar o estado do disco disponível em um nó, para poder relatar a propriedade "AvailableDisk" nesse nó. O mesmo relator pode monitorar a conectividade do nó, para que ele possa relatar a "Conectividade" de uma propriedade no mesmo nó. No repositório de integridade, esses relatórios são tratados como eventos de integridade distintos para o nó especificado. Junto com a SourceId, a propriedade identifica exclusivamente as informações de integridade. |
| --health-state    [Obrigatório] | Os valores possíveis são\: 'Invalid', 'Ok', 'Warning', 'Error', 'Unknown'. |
| – id de serviço [requerido] | A identidade do serviço. <br><br> Normalmente, é o nome completo do serviço sem o esquema de URI "fabric\:". A partir da versão 6.0, nomes hierárquicos são delimitados pelo caractere "\~". Por exemplo, se o nome do serviço é ' malha\:/myapp/app1/svc1', a identidade de serviço deve ser ' myapp\~app1\~svc1' 6.0 + e ' myapp/app1/svc1' nas versões anteriores. |
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

## <a name="sfctl-service-resolve"></a>sfctl service resolve
Resolve uma partição do Service Fabric.

Resolve uma partição de serviço do Service Fabric, para obter os pontos de extremidade das réplicas do serviço.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --service-id [Obrigatório] | A identidade do serviço. Essa ID normalmente é o nome completo do serviço sem o ' malha\:' esquema de URI. A partir da versão 6.0, nomes hierárquicos são delimitados pelo caractere "\~". Por exemplo, se o nome do serviço for "fabric\:/myapp/app1/svc1", a identidade do serviço será "myapp\~app1\~svc1" na versão 6.0 e superiores, e "myapp/app1/svc1" em versões anteriores. |
| --partition-key-type | Tipo de chave para a partição. Esse parâmetro é necessário se o esquema de partição para o serviço for Int64Range ou Nomeado. Os valores possíveis são os seguintes. -None (1) - Indica que o parâmetro PartitionKeyValue não foi especificado. Isso é válido para as partições com o esquema de particionamento como Singleton. Esse é o valor padrão. O valor é 1. -Int64Range (2)- Indica que o parâmetro PartitionKeyValue é uma chave de partição int64. Isso é válido para as partições com o esquema de particionamento como Int64Range. O valor é 2. -Named (3) - Indica que o parâmetro PartitionKeyValue é um nome da partição. Isso é válido para as partições com o esquema de particionamento como Named. O valor é 3. |
| --partition-key-value | Chave de partição. Isso é necessário se o esquema de partição para o serviço for Int64Range ou Named. Essa não é a ID da partição, mas em vez disso, o valor inteiro da chave ou o nome da ID da partição. Por exemplo, se o serviço estiver usando partições de intervalos de 0 a 10, o PartitionKeyValue dele seria um número inteiro nesse intervalo. Consulte a descrição do serviço para ver o intervalo ou o nome. |
| --previous-rsp-version | O valor no campo Versão da resposta recebida anteriormente. Isso será necessário se o usuário souber que o resultado obtido anteriormente está obsoleto. |
| --timeout -t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, table, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-service-type-list"></a>lista de tipo de serviço de sfctl
Obtém a lista que contém as informações sobre os tipos de serviço compatíveis com um tipo de aplicativo provisionado em um cluster do Service Fabric.

Obtém a lista que contém as informações sobre os tipos de serviço compatíveis com um tipo de aplicativo provisionado em um cluster do Service Fabric. O tipo de aplicativo fornecido deve existir. Caso contrário, será retornado um status 404.

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

## <a name="sfctl-service-update"></a>sfctl service update
Atualiza o serviço especificado usando a descrição de atualização fornecida.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --service-id          [Obrigatório] | A identidade do serviço. Normalmente, é o nome completo do serviço sem o esquema de URI "fabric\:". A partir da versão 6.0, nomes hierárquicos são delimitados pelo caractere "\~". Por exemplo, se o nome do serviço é ' malha\:/myapp/app1/svc1', a identidade de serviço deve ser ' myapp\~app1\~svc1' 6.0 + e ' myapp/app1/svc1' nas versões anteriores. |
| --constraints | As restrições de posicionamento como uma cadeia de caracteres. As restrições de posicionamento são expressões boolianas nas propriedades de nó, e permitem restringir um serviço a nós específicos com base nos requisitos do serviço. Por exemplo, para posicionar um serviço em nós nos quais NodeType é azul, especifique o seguinte\:: "NodeColor == blue". |
| --correlated-service | Nome do serviço de destino com o qual correlacionar. |
| --correlation | Correlaciona o serviço com um serviço existente usando uma afinidade de alinhamento. |
| --instance-count | A contagem de instâncias. Aplica-se apenas a serviços sem estado. |
| --load-metrics | Lista de métricas codificadas em JSON usadas ao balancear as cargas entre os nós. |
| --min-replica-set-size | O tamanho mínimo do conjunto de réplicas como um número. Aplica-se apenas a serviços com estado. |
| --move-cost | Especifica o custo da mudança para o serviço. Os valores possíveis são\:: "Zero", "Low", "Medium", "High". |
| --placement-policy-list | Lista codificada por JSON de políticas de posicionamento para o serviço e quaisquer nomes de domínio associados. As políticas podem ser uma ou mais dentre estas: \:, `NonPartiallyPlaceService`, `PreferPrimaryDomain`, `RequireDomain`, `RequireDomainDistribution`. |
| --quorum-loss-wait | A duração máxima, em segundos, durante a qual uma partição é permitida em um estado de perda de quorum. Aplica-se apenas a serviços com estado. |
| --replica-restart-wait | A duração, em segundos, entre a inatividade de uma réplica e a criação de uma nova réplica. Aplica-se apenas a serviços com estado. |
| -políticas de dimensionamento | Lista de políticas para esse serviço de dimensionamento codificados para JSON. |
| --stand-by-replica-keep | A duração máxima, em segundos, para qual espera réplicas serão mantidas antes de serem removidos. Aplica-se apenas a serviços com estado. |
| --stateful | Indica que o serviço de destino tem estado. |
| --stateless | Indica que o serviço de destino não tem estado. |
| --target-replica-set-size | O tamanho do conjunto de réplicas de destino como um número. Aplica-se apenas a serviços com estado. |
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
