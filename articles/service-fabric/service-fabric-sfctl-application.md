---
title: "CLI do Azure Service Fabric – aplicativo sfctl | Microsoft Docs"
description: Descreve os comandos do aplicativo sfctl da CLI do Service Fabric.
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
ms.openlocfilehash: dc57c813a6aecabc21ac3931b7294bce909778d6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="sfctl-application"></a>aplicativo sfctl
Criar, excluir e gerenciar aplicativos e tipos de aplicativo.

## <a name="commands"></a>Comandos

|Command|Descrição|
| --- | --- |
| create       | Crie um aplicativo do Service Fabric usando a descrição especificada.|
| excluir       | Exclui um aplicativo existente do Service Fabric.|
| deployed     | Obtém as informações sobre um aplicativo implantado em um nó do Service Fabric.|
| deployed-health | Obtém as informações sobre a integridade de um aplicativo implantado em um nó do
                      Service Fabric.|
| deployed-list| Obtém a lista de aplicativos implantados em um nó do Service Fabric.|
| health       | Obtém a integridade do aplicativo do service fabric.|
| informações         | Obtém informações sobre um aplicativo do Service Fabric.|
| list         | Obtém a lista de aplicativos criados no cluster do Service Fabric que correspondem
                      a filtros especificados como o parâmetro.|
| load | Obtém informações de carregamento sobre um aplicativo do Service Fabric. |
| manifest     | Obtém o manifesto que descreve um tipo de aplicativo.|
| provision    | Provisiona ou registra um tipo de aplicativo do Service Fabric com o cluster.|
| report-health| Envia um relatório de integridade sobre o aplicativo do Service Fabric.|
| type         | Obtém a lista de tipos de aplicativo criados no cluster do Service Fabric que correspondem
                      exatamente ao nome especificado.|
| type-list    | Obtém a lista de tipos de aplicativo criados no cluster do Service Fabric.|
| unprovision  | Remove ou cancela o registro de um tipo de aplicativo do Service Fabric do cluster.|
| atualizar      | Começa a atualização de um aplicativo no cluster do Service Fabric.|
| upgrade-resume  | Retoma a atualização de um aplicativo no cluster do Service Fabric.|
| upgrade-rollback| Começa a reversão da atualização em andamento no momento de um aplicativo no
                      cluster do Service Fabric.|
| upgrade-status  | Obtém detalhes para a atualização mais recente realizada neste aplicativo.|
| upload       | Copia um pacote de aplicativos do Service Fabric para o repositório de imagens.|

## <a name="sfctl-application-create"></a>sfctl application create
Crie um aplicativo do Service Fabric usando a descrição especificada.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --app-name    [Obrigatório]| O nome do aplicativo, incluindo o esquema de URI "fabric:".|
| --app-type    [Obrigatório]| O nome do tipo de aplicativo encontrado no manifesto do aplicativo.|
| --app-version [Obrigatório]| A versão do tipo de aplicativo, conforme definido no manifesto do aplicativo.|
| --max-node-count     | O número máximo de nós que o Service Fabric reserva como capacidade para este aplicativo. Isso não significa que os serviços desse aplicativo são colocados em todos os nós.|
| --metrics            | Uma lista codificada em JSON das descrições de métricas de capacidade do aplicativo. Uma métrica é definida como um nome, associado a um conjunto de recursos para cada nó no qual o aplicativo existe.|
| --min-node-count     | O número mínimo de nós que o Service Fabric reserva como capacidade para este aplicativo. Isso não significa que os serviços desse aplicativo são colocados em todos os nós.|
| --parameters         | Uma lista codificada em JSON de substituições de parâmetro de aplicativo a serem aplicadas durante a criação do aplicativo.|
| --timeout -t         | Tempo limite do servidor em segundos.  Padrão: 60.|

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --debug              | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração.|
| --help -h            | Mostrar esta mensagem de ajuda e sair.|
| --output -o          | O formato da saída.  Valores permitidos: json, jsonc, table, tsv.  Padrão: json.|
| --query              | Cadeia de consulta JMESPath. Consulte http://jmespath.org/ para saber mais e obter exemplos.|
| --verbose            | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos.|

## <a name="sfctl-application-delete"></a>sfctl application delete
Exclui um aplicativo existente do Service Fabric.

Exclui um aplicativo existente do Service Fabric. Um aplicativo deve ser criado antes que possa ser excluído. A exclusão de um aplicativo exclui todos os serviços que fazem parte desse aplicativo. Por padrão, o Service Fabric tenta fechar réplicas do serviço de uma forma normal e, depois, exclui o serviço. No entanto, se o serviço estiver com problemas para fechar a réplica normalmente, a operação de exclusão poderá demorar muito tempo ou travar. Use o sinalizador opcional ForceRemove para ignorar a sequência de fechamento normal e forçar a exclusão do aplicativo e de todos os seus serviços.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --application-id [Obrigatório]| A identidade do aplicativo. Normalmente, é o nome completo do aplicativo sem o esquema de URI "fabric:". Começando em
                                 version 6.0, hierarchical names are delimited with the "~"
                                 character. For example, if the application name is
                                 "fabric://myapp/app1", the application identity would be
                                 "myapp~app1" in 6.0+ and "myapp/app1" in previous versions.|
| --force-remove          | Força a remoção de um aplicativo ou serviço do Service Fabric sem passar pela sequência de desligamento normal. Esse parâmetro pode ser usado para forçar a exclusão de um aplicativo ou serviço para o qual a exclusão está atingindo o tempo limite devido a problemas no código do serviço que impedem o fechamento normal das réplicas. | | --timeout -t            | Tempo limite do servidor em segundos.  Padrão: 60.|

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --debug                 | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração.|
| --help -h               | Mostrar esta mensagem de ajuda e sair.|
| --output -o             | O formato da saída.  Valores permitidos: json, jsonc, table, tsv.  Padrão: json.|
| --query                 | Cadeia de consulta JMESPath. Consulte http://jmespath.org/ para saber mais e obter exemplos.|
| --verbose               | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos.|

## <a name="sfctl-application-deployed"></a>sfctl application deployed
Obtém as informações sobre um aplicativo implantado em um nó do Service Fabric.|
|     
### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --application-id [Obrigatório]| A identidade do aplicativo. Normalmente, é o nome completo do aplicativo sem o esquema de URI "fabric:". Começando em
                                 version 6.0, hierarchical names are delimited with the "~"
                                 character. For example, if the application name is
                                 "fabric://myapp/app1", the application identity would be
                                 "myapp~app1" in 6.0+ and "myapp/app1" in previous versions.|
| --node-name      [Obrigatório]| O nome do nó.| | --timeout -t            | Tempo limite do servidor em segundos.  Padrão: 60.|

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --debug                 | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração.|
| --help -h               | Mostrar esta mensagem de ajuda e sair.|
| --output -o             | O formato da saída.  Valores permitidos: json, jsonc, table, tsv.  Padrão: json.|
| --query                 | Cadeia de consulta JMESPath. Consulte http://jmespath.org/ para saber mais e obter exemplos.|
| --verbose               | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos.|

## <a name="sfctl-application-health"></a>sfctl application health
Obtém a integridade do aplicativo do service fabric.

Retorna o estado de integridade do aplicativo do service fabric. A resposta mostra o estado de integridade Ok, Erro ou Aviso. Se a entidade não for encontrada no repositório de integridade, retornará um erro.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --application-id                 [Obrigatório]| A identidade do aplicativo. Normalmente, é o nome completo do aplicativo sem o esquema de URI "fabric:". A partir da versão 6.0,
                                                 hierarchical names are delimited with the "~"
                                                 character. For example, if the application name is
                                                 "fabric://myapp/app1", the application identity
                                                 would be "myapp~app1" in 6.0+ and "myapp/app1" in
                                                 previous versions.|
| --deployed-applications-health-state-filter| Possibilita filtrar os objetos de estado de integridade dos aplicativos implantados retornados no resultado da consulta de integridade do aplicativo com base no estado de integridade. Os valores possíveis para esse parâmetro incluem o valor de inteiro de um dos seguintes estados de integridade. Retorna somente os aplicativos implantados que correspondem ao filtro. Todos os aplicativos implantados são usados para avaliar o estado de integridade agregado. Se não for especificado, retorna todas as entradas. Os valores de estado são uma enumeração baseada em sinalizador, então o valor pode ser uma combinação desses valores obtidos usando o operador 'OR' bit a bit.                        Por exemplo, se o valor fornecido for 6, o estado de integridade dos aplicativos implantados com um valor de HealthState de OK (2) e de Aviso (4) retornarão. -Default- Valor padrão. Corresponde a qualquer HealthState.                        O valor é zero. -None- Filtro que não corresponde a qualquer valor de HealthState. Usado para não retornar qualquer resultado em uma determinado conjunto de estados.                        O valor é 1. -Ok- Filtro que corresponde à entrada com o valor de HealthState Ok. O valor é 2. -Warning- Filtro que corresponde à entrada com o valor de HealthState Warning. O valor é 4. -Error- Filtro que corresponde à entrada com o valor de HealthState Error. O valor é 8. -All- Filtro que corresponde à entrada com qualquer valor de HealthState. O valor é 65535. | | --events-health-state-filter | Permite filtrar o conjunto de objetos HealthEvent retornado com base no estado de integridade. Os valores possíveis para esse parâmetro incluem o valor de inteiro de um dos seguintes estados de integridade. Somente os eventos que correspondem ao filtro são retornados. Todos os eventos são usados para avaliar o estado de integridade agregado. Se não for especificado, retorna todas as entradas.                        Os valores de estado são uma enumeração baseada em sinalizador, então o valor pode ser uma combinação desses valores obtidos usando o operador 'OR' bit a bit. Por exemplo, se o valor fornecido for 6, todos os eventos com o valor de HealthState de OK (2) e de Aviso (4) retornarão. -Default- Valor padrão. Corresponde a qualquer HealthState. O valor é zero. -None- Filtro que não corresponde a qualquer valor de HealthState. Usado para não retornar qualquer resultado em uma determinado conjunto de estados. O valor é 1. - Ok - Filtro que corresponde à entrada com o valor de HealthState Ok. O valor é 2. -Warning- Filtro que corresponde à entrada com o valor de HealthState Warning. O valor é 4. -Error- Filtro que corresponde à entrada com o valor de HealthState Error. O valor é 8. -All- Filtro que corresponde à entrada com qualquer valor de HealthState. O valor é 65535. | | --exclude-health-statistics | Indica se as estatísticas de integridade devem ser retornadas como parte do resultado da consulta. Falso por padrão. As estatísticas mostram o número de entidades filhas no estado de integridade OK, Aviso e Erro. | | --services-health-state-filter          | Permite a filtragem dos objetos de estado de integridade dos serviços retornados no resultado da consulta de integridade dos serviços com base em seu estado de integridade. Os valores possíveis para esse parâmetro incluem o valor de inteiro de um dos seguintes estados de integridade. Somente os serviços que correspondem ao filtro são retornados. Todos os serviços são usados para avaliar o estado de integridade agregado.                        Se não for especificado, retorna todas as entradas. Os valores de estado são uma enumeração baseada em sinalizador, então o valor pode ser uma combinação desses valores obtidos usando o operador 'OR' bit a bit. Por exemplo, se o valor fornecido for 6, o estado de integridade dos serviços com um valor de HealthState de OK (2) e de Aviso (4) retornarão. - Default - Valor padrão. Corresponde a qualquer HealthState. O valor é zero.                        -None- Filtro que não corresponde a qualquer valor de HealthState. Usado para não retornar qualquer resultado em uma determinado conjunto de estados. O valor é 1. - Ok - Filtro que corresponde à entrada com o valor de HealthState Ok. O valor é 2. -Warning- Filtro que corresponde à entrada com o valor de HealthState Warning. O valor é 4. -Error- Filtro que corresponde a entrada com o valor de HealthState Error. O valor é 8. -All- Filtro que corresponde a entrada com qualquer valor de HealthState. O valor é 65535. | | timeout -t | Tempo limite do servidor em segundos.  Padrão: 60.|

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --debug                                 | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração.|
| --help -h                               | Mostrar esta mensagem de ajuda e sair.|
| --output -o                             | O formato da saída.  Valores permitidos: json, jsonc, table, tsv.  Padrão: json.|
| --query                                 | Cadeia de consulta JMESPath. Consulte http://jmespath.org/ para saber mais e obter exemplos.|
| --verbose                               | Aumentar o nível de detalhes do log. Use --debug para obter logs de depuração completos.|

## <a name="sfctl-application-info"></a>sfctl application info
Obtém informações sobre um aplicativo do Service Fabric.

Retorna as informações sobre o aplicativo que foi criado, ou que está sendo criado no cluster do Service Fabric, e cujo nome corresponde àquele especificado como o parâmetro. A resposta inclui o nome, o tipo, o status, os parâmetros e outros detalhes sobre o aplicativo.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --application-id      [Obrigatório]| A identidade do aplicativo. Normalmente, é o nome completo do aplicativo sem o esquema de URI "fabric:". A partir da versão 6.0, nomes hierárquicos são delimitados
                                      with the "~" character. For example, if the application name
                                      is "fabric://myapp/app1", the application identity would be
                                      "myapp~app1" in 6.0+ and "myapp/app1" in previous versions.|
| --exclude-application-parameters| O sinalizador que especifica se os parâmetros do aplicativo serão excluídos do resultado.| | --timeout -t                 | Tempo limite do servidor em segundos.  Padrão: 60.|

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --debug                      | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração.|
| --help -h                    | Mostrar esta mensagem de ajuda e sair.|
| --output -o                  | O formato da saída.  Valores permitidos: json, jsonc, table, tsv.             Padrão: json.|
| --query                      | Cadeia de consulta JMESPath. Consulte http://jmespath.org/ para saber mais e obter exemplos.|
| --verbose                    | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos.|

## <a name="sfctl-application-list"></a>sfctl application list
Obtém a lista de aplicativos criados no cluster do Service Fabric que correspondem aos filtros especificados como o parâmetro.

Obtém as informações sobre os aplicativos que foram criados, ou que estão sendo criados no cluster do Service Fabric, e que correspondem aos filtros especificados como o parâmetro. A resposta inclui o nome, o tipo, o status, os parâmetros e outros detalhes sobre o aplicativo. Se os aplicativos não couberem em uma página, uma página de resultados retornará junto com um token de continuação, que poderá ser usado para acessar a próxima página.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
|--application-definition-kind-filter| Usado para filtrar ApplicationDefinitionKind para
                                          application query operations. - Default - Default value.
                                          Filter that matches input with any
                                          ApplicationDefinitionKind value. The value is 0. - All -
                                          Filter that matches input with any
                                          ApplicationDefinitionKind value. The value is 65535. -
                                          ServiceFabricApplicationDescription - Filter that matches
                                          input with ApplicationDefinitionKind value
                                          ServiceFabricApplicationDescription. The value is 1. -
                                          Compose - Filter that matches input with
                                          ApplicationDefinitionKind value Compose. The value is 2.
                                          Default: 65535.|
| --application-type-name      | O nome do tipo de aplicativo usado para filtrar os aplicativos para consulta. Esse valor não deve conter a versão do tipo de aplicativo. | | --continuation-token | O parâmetro do token de continuação é usado para obter o próximo conjunto de resultados. Um token de continuação com um valor não vazio será incluído na resposta da API, quando os resultados do sistema não couberem em uma única resposta. Quando esse valor for passado para a próxima chamada de API, a API retornará o próximo conjunto de resultados. Se não houver mais resultados, o token de continuação não conterá um valor. O valor desse parâmetro não deve ser codificado em URL. | --exclude-application-parameters| O sinalizador que especifica se os parâmetros do aplicativo são excluídos do resultado.| | --timeout -t                 | Tempo limite do servidor em segundos.  Padrão: 60.|

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --debug                      | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração.|
| --help -h                    | Mostrar esta mensagem de ajuda e sair.|
| --output -o                  | O formato da saída.  Valores permitidos: json, jsonc, table, tsv.             Padrão: json.|
| --query                      | Cadeia de consulta JMESPath. Consulte http://jmespath.org/ para saber mais e obter exemplos.|
| --verbose                    | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos.|

## <a name="sfctl-application-load"></a>sfctl application load
Obtém informações de carregamento sobre um aplicativo do Service Fabric.

        Returns the load information about the application that was created or in the process of
        being created in the Service Fabric cluster and whose name matches the one specified as the
        parameter. The response includes the name, minimum nodes, maximum nodes, the number of nodes
        the app is occupying currently, and application load metric information about the
        application.

### <a name="arguments"></a>Argumentos
|Argumento|Descrição|
| --- | --- |
|--application-id [Obrigatório]| A identidade do aplicativo. Isso normalmente é o nome completo do
                                 the application without the 'fabric:' URI scheme. Starting from
                                 version 6.0, hierarchical names are delimited with the "~"
                                 character. For example, if the application name is
                                 "fabric://myapp/app1", the application identity would be
                                 "myapp~app1" in 6.0+ and "myapp/app1" in previous versions. |
| timeout -t | Tempo limite do servidor em segundos.  Padrão: 60.|

### <a name="global-arguments"></a>Argumentos globais
|Argumento|Descrição|
| --- | --- |
|--debug                    | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração.|
    --help -h                  | Mostrar esta mensagem de ajuda e sair.|
    --output -o                | O formato da saída.  Valores permitidos: json, jsonc, table, tsv.  Padrão:
                                 json.|
    --query                    | Cadeia de caracteres de consulta JMESPath. Consulte http://jmespath.org/ para obter mais
                                 informações e exemplos.|
    --verbose                  | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos.|

## <a name="sfctl-application-manifest"></a>sfctl application manifest
Obtém o manifesto que descreve um tipo de aplicativo.
        
Obtém o manifesto que descreve um tipo de aplicativo. A resposta contém o XML do manifesto do aplicativo como uma cadeia de caracteres.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --application-type-name    [Obrigatório]| O nome do tipo de aplicativo.|
| --application-type-version [Obrigatório]| A versão do tipo de aplicativo.|
| --timeout -t                      | Tempo limite do servidor em segundos.  Padrão: 60.|

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --debug                           | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração.|
| --help -h                         | Mostrar esta mensagem de ajuda e sair.|
| --output -o                       | O formato da saída.  Valores permitidos: json, jsonc, table, tsv.                  Padrão: json.|
| --query                           | Cadeia de consulta JMESPath. Consulte http://jmespath.org/ para saber mais e obter exemplos.|
| --verbose                         | Aumentar o nível de detalhes do log. Use --debug para todos os logs de depuração.|

## <a name="sfctl-application-provision"></a>sfctl application provision
Provisiona ou registra um tipo de aplicativo do Service Fabric com o cluster.
        
Provisiona ou registra um tipo de aplicativo do Service Fabric com o cluster. Isso é necessário antes que qualquer aplicativo novo possa ser instanciado.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --application-type-build-path [Obrigatório]| O caminho do repositório de imagens relativo até o pacote de aplicativos.|
| --timeout -t                         | Tempo limite do servidor em segundos.  Padrão: 60.|

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --debug                              | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração.|
| --help -h                            | Mostrar esta mensagem de ajuda e sair.|
| --output -o                          | O formato da saída.  Valores permitidos: json, jsonc, table,                     tsv.  Padrão: json.|
| --query                              | Cadeia de consulta JMESPath. Consulte http://jmespath.org/ para saber mais e obter exemplos.|
| --verbose                            | Aumentar o nível de detalhes do log. Use --debug para todos os logs de depuração.|

## <a name="sfctl-application-type"></a>sfctl application type

Obtém a lista de tipos de aplicativo criados no cluster do Service Fabric que correspondem exatamente ao nome especificado.

Retorna as informações sobre os tipos de aplicativos provisionados, ou que estão sendo provisionados no cluster do Service Fabric. Esses resultados são de tipos de aplicativos cujo nome corresponde exatamente ao especificado como o parâmetro, e que estão em conformidade com os parâmetros de consulta fornecidos. Todas as versões do tipo de aplicativo correspondentes ao nome do tipo de aplicativo retornam, sendo que cada versão retorna como um tipo de aplicativo. A resposta inclui o nome, versão, status e outros detalhes sobre o tipo de aplicativo. Esta é uma consulta paginada, ou seja, se nem todos os aplicativos couberem em uma página, uma página de resultados retornará junto com um token de continuação, que poderá ser usado para acessar a próxima página. Por exemplo, se houver 10 tipos de aplicativo, mas uma página tiver espaço apenas para os três primeiros tipos de aplicativos, ou se o resultado máximo for definido como três, três tipos retornarão. Para acessar o restante dos resultados, recupere as páginas subsequentes usando o token de continuação retornado na próxima consulta. Um token de continuação vazio será retornado se não houver páginas subsequentes.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --application-type-name [Obrigatório]| O nome do tipo de aplicativo.|
| --continuation-token           | O parâmetro de token de continuação é usado para obter o próximo conjunto de resultados. Um token de continuação com um valor não vazio será incluído na resposta da API, quando os resultados do sistema não couberem em uma única resposta. Quando esse valor for passado para a próxima chamada de API, a API retornará o próximo conjunto de resultados. Se não houver mais resultados, o token de continuação não conterá um valor. O valor desse parâmetro não deve ser codificados em URL.|
| --exclude-application-parameters  | O sinalizador que especifica se os parâmetros do aplicativo serão excluídos do resultado.|
| --max-results                  | O número máximo de resultados a serem retornados como parte das consultas paginadas. Esse parâmetro define o limite superior no número de resultados retornados. Os resultados retornados podem ser inferiores aos resultados máximos especificados se não couberem na mensagem, de acordo com as restrições de tamanho máximo de mensagem definidas na configuração. Se esse parâmetro for zero, ou não for especificado, a consulta paginada incluirá o máximo de resultados possível na mensagem de retorno.|
| --timeout -t                   | Tempo limite do servidor em segundos.  Padrão: 60.|

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --debug                        | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração.|
| --help -h                      | Mostrar esta mensagem de ajuda e sair.|
| --output -o                    | O formato da saída.  Valores permitidos: json, jsonc, table, tsv.               Padrão: json.|
| --query                        | Cadeia de consulta JMESPath. Consulte http://jmespath.org/ para saber mais e obter exemplos.|
| --verbose                      | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos.|

## <a name="sfctl-application-unprovision"></a>sfctl application unprovision
Remove ou cancela o registro de um tipo de aplicativo do Service Fabric do cluster.

Remove ou cancela o registro de um tipo de aplicativo do Service Fabric do cluster. Esta operação só pode ser executada se todas as instâncias de aplicativo do tipo de aplicativo tiverem sido excluídas. Após o cancelamento do registro do tipo de aplicativo, nenhuma instância nova do aplicativo poderá ser criada para este tipo específico de aplicativo.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --application-type-name    [Obrigatório]| O nome do tipo de aplicativo.|
| --application-type-version [Obrigatório]| A versão do tipo de aplicativo.|
| --timeout -t                      | Tempo limite do servidor em segundos.  Padrão: 60.|

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --debug                           | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração.|
| --help -h                         | Mostrar esta mensagem de ajuda e sair.|
| --output -o                       | O formato da saída.  Valores permitidos: json, jsonc, table, tsv.                  Padrão: json.|
| --query                           | Cadeia de consulta JMESPath. Consulte http://jmespath.org/ para saber mais e obter exemplos.|
| --verbose                         | Aumentar o nível de detalhes do log. Use --debug para todos os logs de depuração.|

## <a name="sfctl-application-upgrade"></a>sfctl application upgrade
Começa a atualização de um aplicativo no cluster do Service Fabric.

Valida os parâmetros de atualização de aplicativo fornecidos e começa a atualizar o aplicativo se os parâmetros forem válidos. Observe que a descrição de atualização substitui a descrição do aplicativo existente. Isso significa que, se o parâmetro não for especificado, os parâmetros existentes nos aplicativos serão substituídos pela lista de parâmetros vazia. Isso resulta em um aplicativo que usa o valor padrão dos parâmetros do manifesto de aplicativo.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --app-id             [Obrigatório]| A identidade do aplicativo. Normalmente, é o nome completo do aplicativo sem o esquema de URI "fabric:". A partir da versão 6.0, nomes hierárquicos são delimitados pelo caractere "~". Por
        example, if the application name is 'fabric://myapp/app1', the application identity would be
        'myapp~app1' in 6.0+ and 'myapp/app1' in previous versions.|
| --app-version        [Obrigatório]| Versão de destino do aplicativo.| | --parameters         [Obrigatório]| Uma lista codificada em JSON de substituições de parâmetro de aplicativo que serão aplicados durante a atualização do aplicativo.| | --default-service-health-policy| Especificação codificada em JSON da política de integridade usada por padrão para avaliar a integridade de um tipo de serviço.| | --failure-action            | A ação a ser executada quando uma atualização monitorada encontra violações da política de monitoramento ou da política de integridade.| | --force-restart             | Força a reinicialização dos processos durante uma atualização, mesmo quando a versão do código não tiver sido alterada.| | --health-check-retry-timeout| O tempo para repetir avaliações de integridade quando o aplicativo ou cluster não estiver íntegro antes da ação de falha ser executada. Medida em milissegundos.  Padrão: PT0H10M0S.| | --health-check-stable-duration | A quantidade de tempo que o aplicativo ou o cluster deve permanecer íntegro antes que a atualização passe para o próximo domínio de atualização.            Medida em milissegundos.  Padrão: PT0H2M0S.| | --health-check-wait-duration| A quantidade de tempo de espera após a conclusão de um domínio de atualização, antes de aplicar as políticas de integridade. Medida em milissegundos.            Padrão: 0.| | --max-unhealthy-apps        | A porcentagem máxima permitida de aplicativos não íntegros implantados. Representado como um número entre 0 e 100. | | --mode | O modo usado para monitorar a integridade durante uma atualização sem interrupção.            Padrão: UnmonitoredAuto.| | --replica-set-check-timeout | O máximo de tempo para bloqueio do processamento de um domínio de atualização e prevenção da perda de disponibilidade quando houver problemas inesperados. Medido em segundos.| | --service-health-policy     | Mapa codificado em JSON com a política de integridade de tipo de serviço por nome de tipo de serviço. O mapa é vazio por padrão.| | --timeout -t                | Tempo limite do servidor em segundos.  Padrão: 60. | | --upgrade-domain-timeout    | A quantidade de tempo que cada domínio de atualização deve concluir antes de FailureAction ser executado. Medida em milissegundos.  Padrão:            P10675199DT02H48M05.4775807S.| | --upgrade-timeout           | A quantidade de tempo que a atualização geral deve concluir antes de FailureAction ser executado. Medida em milissegundos.  Padrão:            P10675199DT02H48M05.4775807S.| | --warning-as-error          | Trata avisos de avaliação de integridade com a mesma gravidade que os erros.|

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --debug                     | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração.|
| --help -h                   | Mostrar esta mensagem de ajuda e sair.|
| --output -o                 | O formato da saída.  Valores permitidos: json, jsonc, table, tsv.            Padrão: json.|
| --query                     | Cadeia de consulta JMESPath. Consulte http://jmespath.org/ para saber mais e obter exemplos.|
| --verbose                   | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos.|

## <a name="sfctl-application-upload"></a>sfctl application upload
Copia um pacote de aplicativos do Service Fabric para o repositório de imagens.

Opcionalmente, exiba o progresso do carregamento para cada arquivo no pacote. O progresso do carregamento é enviado para `stderr`.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --path [Required]| Caminho até o pacote de aplicativo local.|
|--imagestore-string| Repositório de imagens de destino no qual carregar o pacote de aplicativos.  Padrão:
                         fabric:ImageStore.|
| --show-progress  | Mostra progresso do carregamento de arquivo para pacotes grandes.|

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --debug       | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração.|
| --help -h     | Mostrar esta mensagem de ajuda e sair.|
| --output -o   | O formato da saída.  Valores permitidos: json, jsonc, table, tsv.  Padrão: json.|
| --query       | Cadeia de caracteres de consulta JMESPath. Consulte http://jmespath.org/ para obter mais informações e
                       exemplos.|
| --verbose     | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos.|

## <a name="next-steps"></a>Próximas etapas
- [Configurar](service-fabric-cli.md) a CLI do Service Fabric.
- Saiba como usar a CLI do Service Fabric usando os [scripts de exemplo](/azure/service-fabric/scripts/sfctl-upgrade-application).