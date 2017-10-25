---
title: "CLI do Azure Service Fabric - serviço sfctl | Microsoft Docs"
description: "Descreve os comandos do serviço sfctl da CLI do Service Fabric."
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
ms.openlocfilehash: 66649bb6ae317eb227dcdf45aa084905967c117f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="sfctl-service"></a>Serviço sfctl
Crie, exclua e gerencie o serviço, os tipos de serviço e os pacotes de serviço.

## <a name="commands"></a>Comandos

|Command|Descrição|
| --- | --- |
|    app-name       | Obtém o nome do aplicativo do Service Fabric para um serviço.|
|    code-package-list | Obtém a lista de pacotes de código implantados em um nó do Service Fabric.|
|    create         | Cria o serviço do Service Fabric especificado a partir da descrição.|
|    excluir         | Exclui um serviço existente do Service Fabric.|
|    deployed-type  | Obtém as informações sobre um tipo de serviço especificado do aplicativo implantado em um nó em um cluster do Service Fabric.|
|    deployed-type-list| Obtém a lista que contém as informações sobre os tipos de serviço dos aplicativos implantados em um nó em um cluster do Service Fabric.|
|    description    | Obtém a descrição de um serviço do Service Fabric existente.|
|    health         | Obtém a integridade do serviço do Service Fabric especificado.|
|    informações           | Obtém as informações sobre o serviço específico que pertence a um aplicativo do Service Fabric.|
|    list           | Obtém as informações sobre todos os serviços pertencentes ao aplicativo especificado pela ID do aplicativo.|
|    manifest       | Obtém o manifesto que descreve um tipo de serviço.|
|    package-deploy | Baixa os pacotes associados ao manifesto de serviço especificado para o cache de imagem no nó especificado.|
|    package-health | Obtém as informações sobre a integridade de um pacote de serviço para um aplicativo específico implantado em um nó e em um aplicativo do Service Fabric.|
|    package-info   | Obtém a lista de pacotes de serviço implantados em um nó do Service Fabric que correspondem exatamente ao nome especificado.|
|    package-list   | Obtém a lista de pacotes de serviço implantados em um nó do Service Fabric.|
|    recover        | Indica ao cluster do Service Fabric que ele deve tentar recuperar o serviço especificado, que atualmente está preso em perda de quorum.|
|    report-health  | Envia um relatório de integridade sobre o serviço do Service Fabric.|
|    resolve        | Resolve uma partição do Service Fabric.|
|    type-list      | Obtém a lista que contém as informações sobre os tipos de serviço compatíveis com um tipo de aplicativo provisionado em um cluster do Service Fabric.|
|    atualizar         | Atualiza o serviço especificado usando a descrição de atualização fornecida.|


## <a name="sfctl-service-create"></a>sfctl service create
Cria o serviço do Service Fabric especificado a partir da descrição.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --app-id       [Obrigatório]| A identidade do aplicativo pai. Normalmente, é a ID completa do aplicativo sem o esquema de URI "fabric:". A partir da versão 6.0, nomes hierárquicos são delimitados pelo caractere "~". Por exemplo, se o nome do aplicativo for "fabric://meuaplicativo/aplicativo1", a identidade do aplicativo será "meuaplicativo~aplicativo1" na versão 6.0 e superiores, e "meuaplicativo/aplicativo1" nas versões anteriores.|
| --name         [Obrigatório]| Nome do serviço. Deve ser um filho da ID do aplicativo.           Este é o nome completo, incluindo o URI `fabric:`. Por exemplo, o serviço `fabric:/A/B` é um filho do aplicativo `fabric:/A`.|
| --service-type [Obrigatório]| O nome do tipo de serviço.|
| --activation-mode     | O modo de ativação para o pacote de serviço.|
| --constraints         | As restrições de posicionamento como uma cadeia de caracteres. As restrições de posicionamento são expressões boolianas nas propriedades de nó, e permitem restringir um serviço a nós específicos com base nos requisitos do serviço. Por exemplo, para posicionar um serviço em nós nos quais NodeType é azul, especifique o seguinte: "NodeColor == blue".|
| --correlated-service  | Nome do serviço de destino com o qual correlacionar.|
| --correlation         | Correlaciona o serviço com um serviço existente usando uma afinidade de alinhamento.|
| --dns-name            | O nome DNS do serviço a ser criado. O serviço do sistema de DNS do Service Fabric deve estar habilitado para essa configuração.|
| --instance-count      | A contagem de instâncias. Aplica-se apenas a serviços sem estado.|
| --int-scheme          | Indica que o serviço deve ser particionado uniformemente em um intervalo de inteiros sem sinal.|
| --int-scheme-count    | O número de partições dentro do intervalo de chaves de inteiros (para um esquema de partição de inteiros uniforme) a ser criado.|
| --int-scheme-high     | O final do intervalo de chaves de inteiros, se estiver usando um esquema de partição de inteiros uniforme.|
| --int-scheme-low      | O início do intervalo de chaves de inteiros, se estiver usando um esquema de partição de inteiros uniforme.|
| --load-metrics        | Lista de métricas codificada em JSON, usada ao balancear as cargas de serviços entre nós.|
| --min-replica-set-size| O tamanho mínimo do conjunto de réplicas como um número. Aplica-se apenas a serviços com estado.|
| --move-cost           | Especifica o custo da mudança para o serviço. Os valores possíveis são: "Zero", "Low", "Medium", "High".|
| --named-scheme        | Indica que o serviço deve ter várias partições nomeadas.|
| --named-scheme-list   | Lista de nomes codificada em JSON  para particionar o serviço, se estiver usando o esquema de partição nomeada.|
| --no-persisted-state  | Se verdadeiro, indica que o serviço não tem um estado persistente armazenado no disco local, ou só armazena o estado na memória.|
| --placement-policy-list  | Lista de políticas de posicionamento codificada em JSON para o serviço, e qualquer nome de domínio associado. As políticas podem ser uma ou mais dentre estas: `NonPartiallyPlaceService`, `PreferPrimaryDomain`, `RequireDomain`, `RequireDomainDistribution`.|
| --quorum-loss-wait    | A duração máxima, em segundos, durante a qual uma partição é permitida em um estado de perda de quorum. Aplica-se apenas a serviços com estado.|
| --replica-restart-wait| A duração, em segundos, entre a inatividade de uma réplica e a criação de uma nova réplica. Aplica-se apenas a serviços com estado.|
| --singleton-scheme    | Indica que o serviço deve ter uma única partição ou ser um serviço não particionado.|
| --stand-by-replica-keep  | A duração máxima, em segundos, durante a qual as réplicas StandBy são mantidas antes de serem removidas. Aplica-se apenas a serviços com estado.|
| --stateful            | Indica que o serviço tem estado.|
| --stateless           | Indica que o serviço não tem estado.|
| --target-replica-set-size| O tamanho do conjunto de réplicas de destino como um número. Aplica-se apenas a serviços com estado.|
| --timeout -t          | Tempo limite do servidor em segundos.  Padrão: 60.|

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --debug               | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração.|
| --help -h             | Mostrar esta mensagem de ajuda e sair.|
| --output -o           | O formato da saída.  Valores permitidos: json, jsonc, table, tsv.  Padrão:           json.|
| --query               | Cadeia de consulta JMESPath. Para saber mais e obter exemplos, consulte http://jmespath.org/.|
| --verbose             | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos.|

## <a name="sfctl-service-delete"></a>sfctl service delete
Exclui um serviço existente do Service Fabric.

Exclui um serviço existente do Service Fabric. Um serviço deve ser criado antes que possa ser excluído. Por padrão, o Service Fabric tenta fechar réplicas do serviço de uma forma normal e, depois, exclui o serviço. No entanto, se o serviço estiver com problemas para fechar a réplica normalmente, a operação de exclusão poderá demorar muito tempo ou travar. Use o sinalizador opcional ForceRemove para ignorar a sequência de fechamento normal e forçar a exclusão do serviço.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --service-id [Obrigatório]| A identidade do serviço. Normalmente é o nome completo do serviço sem o esquema de URI "fabric:". A partir da versão 6.0, nomes hierárquicos são delimitados pelo caractere "~". Por exemplo, se o nome do serviço for "fabric://meuaplicativo/aplicativo1/svc1, a identidade do serviço será "meuaplicativo~aplicativo1~svc1" na versão 6.0 e superiores, e "meuaplicativo/aplicativo1/svc1" em versões anteriores.|
| --force-remove      | Força a remoção de um aplicativo ou serviço do Service Fabric sem passar pela sequência de desligamento normal. Esse parâmetro pode ser usado para forçar a exclusão de um aplicativo ou serviço para o qual a exclusão está atingindo o tempo limite devido a problemas no código do serviço que impedem o fechamento normal das réplicas.|
| --timeout -t        | Tempo limite do servidor em segundos.  Padrão: 60.|

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --debug             | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração.|
| --help -h           | Mostrar esta mensagem de ajuda e sair.|
| --output -o         | O formato da saída.  Valores permitidos: json, jsonc, table, tsv.  Padrão:         json.|
| --query             | Cadeia de caracteres de consulta JMESPath. Para saber mais e obter exemplos, consulte http://jmespath.org/.|
| --verbose           | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos.|

## <a name="sfctl-service-description"></a>sfctl service description
Obtém a descrição de um serviço do Service Fabric existente.

Obtém a descrição de um serviço do Service Fabric existente. É necessário criar um serviço para poder obter sua descrição.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --service-id [Obrigatório]| A identidade do serviço. Normalmente é o nome completo do serviço sem o esquema de URI "fabric:". A partir da versão 6.0, nomes hierárquicos são delimitados pelo caractere "~". Por exemplo, se o nome do serviço for "fabric://meuaplicativo/aplicativo1/svc1", a identidade do serviço será "meuaplicativo~aplicativo1~svc1" na versão 6.0 e superiores, e "meuaplicativo/aplicativo1/svc1" em versões anteriores.|
| --timeout -t        | Tempo limite do servidor em segundos.  Padrão: 60.|

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --debug             | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração.|
| --help -h           | Mostrar esta mensagem de ajuda e sair.|
| --output -o         | O formato da saída.  Valores permitidos: json, jsonc, table, tsv.  Padrão:         json.|
| --query             | Cadeia de caracteres de consulta JMESPath. Para saber mais e obter exemplos, consulte http://jmespath.org/.|
| --verbose           | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos.|

## <a name="sfctl-service-health"></a>sfctl service health
Obtém a integridade do serviço do Service Fabric especificado.

Obtém as informações de integridade do serviço especificado. Use EventsHealthStateFilter para filtrar o conjunto de eventos de integridade relatados no serviço de acordo com o estado de integridade. Use PartitionsHealthStateFilter para filtrar o conjunto de partições retornado. Se você especificar um serviço que não existe no repositório de integridade, esse cmdlet retornará um erro. .

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --service-id          [Obrigatório]| A identidade do serviço. Normalmente, é o nome completo do serviço sem o esquema de URI "fabric:". A partir da versão 6.0, nomes hierárquicos são delimitados pelo caractere "~". Por exemplo, se o nome do serviço for "fabric://meuaplicativo/aplicativo1/svc1", a identidade do serviço será "meuaplicativo~aplicativo1~svc1" na versão 6.0 e superiores, e "meuaplicativo/aplicativo1/svc1" em versões anteriores.|
| --events-health-state-filter | Permite filtrar o conjunto de objetos HealthEvent retornado com base no estado de integridade. Os valores possíveis para esse parâmetro incluem o valor de inteiro de um dos seguintes estados de integridade. Somente os eventos que correspondem ao filtro são retornados. Todos os eventos são usados para avaliar o estado de integridade agregado. Se não for especificado, retorna todas as entradas. Os valores de estado são uma enumeração baseada em sinalizador, então o valor pode ser uma combinação desses valores obtidos usando o operador 'OR' bit a bit. Por exemplo, se o valor fornecido for 6, todos os eventos com o valor de HealthState de OK (2) e de Aviso (4) retornarão. -Default- Valor padrão. Corresponde a qualquer HealthState. O valor é zero. -None- Filtro que não corresponde a qualquer valor de HealthState. Usado para não retornar qualquer resultado em um determinado conjunto de estados. O valor é 1. -Ok- Filtro que corresponde à entrada com o valor de HealthState Ok. O valor é 2. -Warning- Filtro que corresponde à entrada com o valor de HealthState Warning. O valor é 4. -Error- Filtro que corresponde à entrada com o valor de HealthState Error. O valor é 8. -All- Filtro que corresponde à entrada com qualquer valor de HealthState. O valor é 65535.|
|--exclude-health-statistics     | Indica se as estatísticas de integridade devem ser retornadas como parte do resultado da consulta. Falso por padrão. As estatísticas mostram o número de entidades filhas nos estados de integridade OK, Warning e Error.|
| --partitions-health-state-filter| Permite a filtragem dos objetos de estado de integridade das partições retornados no resultado da consulta de integridade de serviço, com base em seu estado de integridade. Os valores possíveis para esse parâmetro incluem o valor de inteiro de um dos seguintes estados de integridade. Somente as partições que correspondem ao filtro são retornadas. Todas as partições são usadas para avaliar o estado de integridade agregado. Se não for especificado, retorna todas as entradas. Os valores de estado são uma enumeração baseada em sinalizador, então o valor pode ser uma combinação desses valores obtidos usando o operador 'OR' bit a bit. Por exemplo, se o valor fornecido for "6", o estado de integridade das partições com um valor de HealthState OK (2) e Warning (4) retornará. -Default- Valor padrão. Corresponde a qualquer HealthState.                  O valor é zero. -None- Filtro que não corresponde a qualquer valor de HealthState. Usado para não retornar qualquer resultado em uma determinado conjunto de estados. O valor é 1. -Ok- Filtro que corresponde à entrada com o valor de HealthState Ok. O valor é 2. -Warning- Filtro que corresponde à entrada com o valor de HealthState Warning. O valor é 4. -Error- Filtro que corresponde à entrada com o valor de HealthState Error. O valor é 8. -All- Filtro que corresponde à entrada com qualquer valor de HealthState. O valor é 65535.|
| --timeout -t                 | Tempo limite do servidor em segundos.  Padrão: 60.|

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --debug                      | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração.|
| --help -h                    | Mostrar esta mensagem de ajuda e sair.|
| --output -o                  | O formato da saída.  Valores permitidos: json, jsonc, table, tsv.                  Padrão: json.|
| --query                      | Cadeia de consulta JMESPath. Consulte http://jmespath.org/ para saber mais e obter exemplos.|
| --verbose                    | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos.|

## <a name="sfctl-service-info"></a>sfctl service info
Obtém as informações sobre o serviço específico que pertencem a um aplicativo do Service Fabric.

Retorna as informações sobre o serviço especificado que pertencem a um aplicativo especificado do Service Fabric especificado.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --application-id [Obrigatório]| A identidade do aplicativo. Normalmente, é o nome completo do aplicativo sem o esquema de URI "fabric:". A partir da versão 6.0, nomes hierárquicos são delimitados pelo caractere "~". Por exemplo, se o nome do aplicativo for "fabric://meuaplicativo/aplicativo1", a identidade do aplicativo será "meuaplicativo~aplicativo1" na versão 6.0 e superiores, e "meuaplicativo/aplicativo1" nas versões anteriores.|
| --service-id          [Obrigatório]| A identidade do serviço. Normalmente, é o nome completo do serviço sem o esquema de URI "fabric:". A partir da versão 6.0, nomes hierárquicos são delimitados pelo caractere "~". Por exemplo, se o nome do serviço for fabric://meuaplicativo/aplicativo1/svc1, a identidade do serviço será "meuaplicativo~aplicativo1~svc1" na versão 6.0 e superiores, e "meuaplicativo/aplicativo1/svc1" em versões anteriores.|
| --timeout -t            | Tempo limite do servidor em segundos.  Padrão: 60.|

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --debug                 | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração.|
| --help -h               | Mostrar esta mensagem de ajuda e sair.|
| --output -o             | O formato da saída.  Valores permitidos: json, jsonc, table, tsv.  Padrão: json.|
| --query                 | Cadeia de consulta JMESPath. Para saber mais e obter exemplos, consulte http://jmespath.org/.|
| --verbose               | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos.|

## <a name="sfctl-service-list"></a>sfctl service list
Obtém as informações sobre todos os serviços pertencentes ao aplicativo especificado pela ID de aplicativo.

Retorna as informações sobre todos os serviços pertencentes ao aplicativo especificado pela ID de aplicativo.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --application-id [Obrigatório]| A identidade do aplicativo. Normalmente, é o nome completo do aplicativo sem o esquema de URI "fabric:". A partir da versão 6.0, nomes hierárquicos são delimitados pelo caractere "~". Por exemplo, se o nome do aplicativo for "fabric://meuaplicativo/aplicativo1", a identidade do aplicativo será "meuaplicativo~aplicativo1" na versão 6.0 e superiores, e "meuaplicativo/aplicativo1" nas versões anteriores.|
| --continuation-token    | O parâmetro de token de continuação é usado para obter o próximo conjunto de resultados. Um token de continuação com um valor não vazio será incluído na resposta da API, quando os resultados do sistema não couberem em uma única resposta. Quando esse valor for passado para a próxima chamada de API, a API retornará o próximo conjunto de resultados. Se não houver mais resultados, o token de continuação não conterá um valor. O valor desse parâmetro não deve ser codificados em URL.|
| --service-type-name     | O nome de tipo de serviço usado para filtrar os serviços para consulta.|
| --timeout -t            | Tempo limite do servidor em segundos.  Padrão: 60.|

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --debug                 | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração.|
| --help -h               | Mostrar esta mensagem de ajuda e sair.|
| --output -o             | O formato da saída.  Valores permitidos: json, jsonc, table, tsv.  Padrão: json.|
| --query                 | Cadeia de caracteres de consulta JMESPath. Para saber mais e obter exemplos, consulte http://jmespath.org/.|
| --verbose               | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos.|

## <a name="sfctl-service-manifest"></a>sfctl service manifest
Obtém o manifesto que descreve um tipo de serviço.

Obtém o manifesto que descreve um tipo de serviço. A resposta contém o XML do manifesto do serviço como uma cadeia de caracteres.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --application-type-name    [Obrigatório]| O nome do tipo de aplicativo.|
| --application-type-version [Obrigatório]| A versão do tipo de aplicativo.|
| --service-manifest-name    [Obrigatório]| O nome de um manifesto de serviço registrado como parte de um tipo de aplicativo em um cluster do Service Fabric.|
| --timeout -t                      | Tempo limite do servidor em segundos.  Padrão: 60.|

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --debug                           | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração.|
| --help -h                         | Mostrar esta mensagem de ajuda e sair.|
| --output -o                       | O formato da saída.  Valores permitidos: json, jsonc, table, tsv.                       Padrão: json.|
| --query                           | Cadeia de consulta JMESPath. Consulte http://jmespath.org/ para saber mais e obter exemplos.|
| --verbose                         | Aumentar o nível de detalhes do log. Use --debug para todos os logs de depuração.|

## <a name="sfctl-service-recover"></a>sfctl service recover
Indica ao cluster do Service Fabric que ele deve tentar recuperar o serviço especificado, que atualmente está preso em perda de quorum.

Indica ao cluster do Service Fabric que ele deve tentar recuperar o serviço especificado, que atualmente está preso em perda de quorum. Esta operação deve ser executada apenas se as réplicas inativas não puderem ser recuperadas. O uso incorreto dessa API pode causar possíveis perdas de dados.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --service-id [Obrigatório]| A identidade do serviço. Normalmente é o nome completo do serviço sem o esquema de URI "fabric:". A partir da versão 6.0, nomes hierárquicos são delimitados pelo caractere "~". Por exemplo, se o nome do serviço for fabric://meuaplicativo/aplicativo1/svc1, a identidade do serviço será "meuaplicativo~aplicativo1~svc1" na versão 6.0 e superiores, e "meuaplicativo/aplicativo1/svc1" em versões anteriores.|
| --timeout -t        | Tempo limite do servidor em segundos.  Padrão: 60.|

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --debug             | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração.|
| --help -h           | Mostrar esta mensagem de ajuda e sair.|
| --output -o         | O formato da saída.  Valores permitidos: json, jsonc, table, tsv.  Padrão:         json.|
| --query             | Cadeia de caracteres de consulta JMESPath. Para saber mais e obter exemplos, consulte http://jmespath.org/.|
| --verbose           | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos.|

## <a name="sfctl-service-resolve"></a>sfctl service resolve
Resolve uma partição do Service Fabric.

Resolve uma partição de serviço do Service Fabric, para obter os pontos de extremidade das réplicas do serviço.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --service-id [Obrigatório]| A identidade do serviço. Normalmente é o nome completo do serviço sem o esquema de URI "fabric:". A partir da versão 6.0, nomes hierárquicos são delimitados pelo caractere "~". Por exemplo, se o nome do serviço for "fabric://meuaplicativo/aplicativo1/svc1", a identidade do serviço será "meuaplicativo~aplicativo1~svc1" na versão 6.0 e superiores, e "meuaplicativo/aplicativo1/svc1" em versões anteriores.|
| --partition-key-type| Tipo de chave para a partição. Esse parâmetro é necessário se o esquema de partição para o serviço for Int64Range ou Nomeado. Os valores possíveis são os seguintes. -None (1) - Indica que o parâmetro PartitionKeyValue não foi especificado. Isso é válido para as partições com o esquema de particionamento como Singleton. Esse é o valor padrão. O valor é 1. -Int64Range (2)- Indica que o parâmetro PartitionKeyValue é uma chave de partição int64. Isso é válido para as partições com o esquema de particionamento como Int64Range. O valor é 2. -Named (3) - Indica que o parâmetro PartitionKeyValue é um nome da partição. Isso é válido para as partições com o esquema de particionamento como Named. O valor é 3.|
| --partition-key-value  | Chave de partição. Isso é necessário se o esquema de partição para o serviço for Int64Range ou Named.|
| --previous-rsp-version | O valor no campo Versão da resposta recebida anteriormente. Isso será necessário se o usuário souber que o resultado obtido anteriormente está obsoleto.|
| --timeout -t        | Tempo limite do servidor em segundos.  Padrão: 60.|

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --debug             | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração.|
| --help -h           | Mostrar esta mensagem de ajuda e sair.|
| --output -o         | O formato da saída.  Valores permitidos: json, jsonc, table, tsv.  Padrão:         json.|
| --query             | Cadeia de caracteres de consulta JMESPath. Para saber mais e obter exemplos, consulte http://jmespath.org/.|
| --verbose           | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos.|

## <a name="sfctl-service-update"></a>sfctl service update
Atualiza o serviço especificado usando a descrição de atualização fornecida.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --service-id          [Obrigatório]| Serviço de destino para a atualização. Normalmente, é a ID completa do serviço sem o esquema de URI "fabric:". A partir da versão 6.0, nomes hierárquicos são delimitados pelo caractere "~". Por exemplo, se o nome do serviço for fabric://meuaplicativo/aplicativo1/svc1, a identidade do serviço será "meuaplicativo~aplicativo1~svc1" na versão 6.0 e superiores, e "meuaplicativo/aplicativo1/svc1" em versões anteriores.|
| --constraints         | As restrições de posicionamento como uma cadeia de caracteres. As restrições de posicionamento são expressões boolianas nas propriedades de nó, e permitem restringir um serviço a nós específicos com base nos requisitos do serviço. Por exemplo, para posicionar um serviço em nós nos quais NodeType é azul, especifique o seguinte: "NodeColor == blue".|
| --correlated-service  | Nome do serviço de destino com o qual correlacionar.|
| --correlation         | Correlaciona o serviço com um serviço existente usando uma afinidade de alinhamento.|
| --instance-count      | A contagem de instâncias. Aplica-se apenas a serviços sem estado.|
| --load-metrics        | Lista de métricas codificadas em JSON usadas ao balancear as cargas entre os nós.|
| --min-replica-set-size| O tamanho mínimo do conjunto de réplicas como um número. Aplica-se apenas a serviços com estado.|
| --move-cost           | Especifica o custo da mudança para o serviço. Os valores possíveis são: "Zero", "Low", "Medium", "High".|
| --placement-policy-list  | Lista de políticas de posicionamento codificada em JSON para o serviço, e qualquer nome de domínio associado. As políticas podem ser uma ou mais dentre estas: `NonPartiallyPlaceService`, `PreferPrimaryDomain`, `RequireDomain`, `RequireDomainDistribution`.|
| --quorum-loss-wait    | A duração máxima, em segundos, durante a qual uma partição é permitida em um estado de perda de quorum. Aplica-se apenas a serviços com estado.|
| --replica-restart-wait| A duração, em segundos, entre a inatividade de uma réplica e a criação de uma nova réplica. Aplica-se apenas a serviços com estado.|
| --stand-by-replica-keep  | A duração máxima, em segundos, durante a qual as réplicas StandBy são mantidas antes de serem removidas. Aplica-se apenas a serviços com estado.|
| --stateful            | Indica que o serviço de destino tem estado.|
| --stateless           | Indica que o serviço de destino não tem estado.|
| --target-replica-set-size| O tamanho do conjunto de réplicas de destino como um número. Aplica-se apenas a serviços com estado.|
| --timeout -t          | Tempo limite do servidor em segundos.  Padrão: 60.|

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --debug               | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração.|
| --help -h             | Mostrar esta mensagem de ajuda e sair.|
| --output -o           | O formato da saída.  Valores permitidos: json, jsonc, table, tsv.  Padrão:           json.|
| --query               | Cadeia de consulta JMESPath. Para saber mais e obter exemplos, consulte http://jmespath.org/.|
| --verbose             | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos.|

## <a name="next-steps"></a>Próximas etapas
- [Configurar](service-fabric-cli.md) a CLI do Service Fabric.
- Saiba como usar a CLI do Service Fabric usando os [scripts de exemplo](/azure/service-fabric/scripts/sfctl-upgrade-application).