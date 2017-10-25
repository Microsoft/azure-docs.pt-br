---
title: CLI do Azure Service Fabric - cluster sfctl | Microsoft Docs
description: Descreve os comandos do cluster sfctl da CLI do Service Fabric.
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
ms.openlocfilehash: dc2ed59d6adaca97b23dddcb7ec968d90171b483
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="sfctl-cluster"></a>cluster sfctl
Selecionar, gerenciar e operar clusters do Service Fabric.

## <a name="commands"></a>Comandos

|Command|Descrição|
| --- | --- |
|    versões de código| Obtém uma lista de versões de códigos de malha que são provisionadas em um cluster do Service Fabric.|
|    versões de configurações | Obtém uma lista de versões de configurações de malha que são provisionadas em um cluster do Service Fabric.|
|    integridade       | Obtém a integridade de um cluster do Service Fabric.|
|    manifest     | Obter o manifesto do cluster do Service Fabric.|
|    cancelamento de operação| Cancela uma operação com falha induzida pelo usuário.|
|    operationgit | Obtém uma lista de operações com falha induzidas pelo usuário e filtradas por entrada fornecida.|
|    provision     | Provisão dos pacotes de código ou a configuração de um cluster do Service Fabric.|
|    sistema de recuperação  | Indica ao cluster do Service Fabric que ele deve tentar recuperar os serviços do sistema que estão atualmente paralisados na perda de quorum.|
|report-health   | Envia um relatório de integridade sobre o cluster do Service Fabric.|
|    selecionar       | Se conecta a um ponto de extremidade do cluster do Service Fabric.|
| unprovision     | Desconfigurar os pacotes de código ou a configuração de um cluster do Service Fabric.|
|    atualizar         | Começar a realizar os upgrades da versão do código ou configuração de um cluster do Service Fabric.|
|    upgrade-resume  | Verifica o upgrade do cluster para passar para o próximo domínio de upgrade.|
|    upgrade-rollback| Reverter o upgrade de um cluster de Service Fabric.|
|    upgrade-status  | Obtém o andamento do upgrade do cluster atual.|
|atualização do upgrade  | Atualize os parâmetros de upgrade de um upgrade de cluster do Service Fabric.|


## <a name="sfctl-cluster-health"></a>Integridade do cluster sfctl
Obtém a integridade de um cluster do Service Fabric.

Obtém a integridade de um cluster do Service Fabric. Use EventsHealthStateFilter para filtrar o conjunto de eventos de integridade relatados no cluster de acordo com o estado de integridade. Da mesma forma, use NodesHealthStateFilter e ApplicationsHealthStateFilter para filtrar o conjunto de nós e aplicativos retornados com base no seu estado de integridade agregada. 

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --filtro do estado de integridade dos aplicativos| Permite a filtragem do estado de integridade do aplicativo
                                                    objects returned in the result of cluster health
                                                    query based on their health state. The possible
                                                    values for this parameter include integer value
                                                    obtained from members or bitwise operations on
                                                    members of HealthStateFilter enumeration. Only
                                                    applications that match the filter are returned.
                                                    All applications are used to evaluate the
                                                    aggregated health state. If not specified, all
                                                    entries are returned. The state values are flag
                                                    based enumeration, so the value could be a
                                                    combination of these values obtained using
                                                    bitwise 'OR' operator. For example, if the
                                                    provided value is 6 then health state of
                                                    applications with HealthState value of OK (2)
                                                    and Warning (4) are returned. - Default -
                                                    Default value. Matches any HealthState. The
                                                    value is zero. - None - Filter that doesn't
                                                    match any HealthState value. Used in order to
                                                    return no results on a given collection of
                                                    states. The value is 1. - Ok - Filter that
                                                    matches input with HealthState value Ok. The
                                                    value is 2. - Warning - Filter that matches
                                                    input with HealthState value Warning. The value
                                                    is 4. - Error - Filter that matches input with
                                                    HealthState value Error. The value is 8. - All -
                                                    Filter that matches input with any HealthState value. The value is 65535.|
| --events-health-state-filter | Permite filtrar o conjunto de objetos HealthEvent retornado com base no estado de integridade. Os valores possíveis para esse parâmetro incluem o valor de inteiro de um dos seguintes estados de integridade. Somente os eventos que correspondem ao filtro são retornados. Todos os eventos são usados para avaliar o estado de integridade agregado. Se não for especificado, retorna todas as entradas. Os valores de estado são uma enumeração baseada em sinalizador, então o valor pode ser uma combinação desses valores obtidos usando o operador 'OR' bit a bit. Por exemplo, se o valor fornecido for 6, serão retornados todos os eventos com o valor de HealthState de OK (2) e de Aviso (4). -Default- Valor padrão. Corresponde a qualquer HealthState. O valor é zero. -None- Filtro que não corresponde a qualquer valor de HealthState. Usado para não retornar qualquer resultado em um determinado conjunto de estados. O valor é 1. - Ok - Filtro que corresponde à entrada com o valor de HealthState Ok. O valor é 2. -Warning- Filtro que corresponde à entrada com o valor de HealthState Warning.
O valor é 4. -Error- Filtro que corresponde a entrada com o valor de HealthState Error. O valor é 8. - All - Filtro que corresponde à entrada com qualquer valor de HealthState. O valor é 65535. | | --exclude-health-statistics | Indica se as estatísticas de integridade devem ser retornadas como parte do resultado da consulta. Falso por padrão. As estatísticas mostram o número de entidades filhas no estado de integridade Ok, aviso e erro. | |   --include-system-application-health-statistics| Indica se as estatísticas de integridade devem incluir a malha: / estatísticas de integridade do aplicativo de sistema. Falso por padrão. Se IncludeSystemApplicationHealthStatistics for definido como true, a integridade estatística inclui as entidades que pertencem à malha: / aplicativo do sistema. Caso contrário, o resultado da consulta inclui estatísticas de integridade somente para aplicativos de usuário. As estatísticas de integridade devem ser incluídas no resultado da consulta para esse parâmetro a ser aplicado. | --nodes-health-state-filter    | Permite filtrar os objetos de estado de integridade do nó retornados no resultado da consulta de integridade de cluster com base em seu estado de integridade. Os valores possíveis para esse parâmetro incluem o valor de inteiro de um dos seguintes estados de integridade. Só retornam os nós que correspondem ao filtro. Todos os nós são usados para avaliar o estado de integridade agregado. Se não for especificado, retorna todas as entradas. Os valores de estado são uma enumeração baseada em sinalizador, então o valor pode ser uma combinação desses valores obtidos usando o operador 'OR' bit a bit. Por exemplo, se o valor fornecido for "6", o estado de integridade dos nós com um valor de HealthState OK (2) e Warning (4) retornará. -Default- Valor padrão. Corresponde a qualquer HealthState. O valor é zero. -None- Filtro que não corresponde a qualquer valor de HealthState. Usado para não retornar qualquer resultado em um determinado conjunto de estados. O valor é 1. - Ok - Filtro que corresponde à entrada com o valor de HealthState Ok. O valor é 2. -Warning- Filtro que corresponde à entrada com o valor de HealthState Warning.
O valor é 4. -Error- Filtro que corresponde a entrada com o valor de HealthState Error. O valor é 8. - All - Filtro que corresponde à entrada com qualquer valor de HealthState. O valor é 65535. | | timeout -t | Tempo limite do servidor em segundos.  Padrão: 60.|

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --debug                        | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração.|
| --help -h                      | Mostrar esta mensagem de ajuda e sair.|
| --output -o                    | O formato da saída.  Valores permitidos: json, jsonc, table, tsv.                    Padrão: json.|
| --query                        | Cadeia de caracteres de consulta JMESPath. Consulte http://jmespath.org/ para saber mais e obter exemplos.|
| --verbose                      | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos.|

## <a name="sfctl-cluster-manifest"></a>Manifesto do cluster sfctl
Obter o manifesto do cluster do Service Fabric.

Obter o manifesto do cluster do Service Fabric. O manifesto do cluster contém propriedades do cluster que incluem diferentes tipos de nó no cluster, as configurações de segurança, falhas e topologias de domínio de atualização etc. Essas propriedades são especificadas como parte do arquivo Clusterconfig.JSON ao implantar um cluster autônomo. No entanto, a maioria das informações no manifesto do cluster é gerada internamente pela malha do serviço durante a implantação do cluster em outros cenários de implantação (por exemplo, ao usar o [portal do Azure](https://portal.azure.com)). O conteúdo do manifesto do cluster é apenas para fins informativos e os usuários não devem depender do formato do conteúdo do arquivo ou sua interpretação. 

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --timeout -t| Tempo limite do servidor em segundos.  Padrão: 60.|

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --debug  | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração.|
| --help -h| Mostrar esta mensagem de ajuda e sair.|
| --output -o | O formato da saída.  Valores permitidos: json, jsonc, table, tsv.  Padrão: json.|
| --query  | Cadeia de caracteres de consulta JMESPath. Consulte http://jmespath.org/ para saber mais e obter exemplos.|
| --verbose| Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos.|

## <a name="sfctl-cluster-provision"></a>provisionamento do cluster sfctl
Provisão dos pacotes de código ou a configuração de um cluster do Service Fabric.

        Validate and provision the code or configuration packages of a Service Fabric cluster.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
|--caminho do arquivo do manifesto do cluster| O caminho do arquivo do manifesto do cluster.|
|    --caminho do arquivo do código            | O caminho do pacote de código do cluster.|
|    --timeout -t                | Tempo limite do servidor em segundos.  Padrão: 60.|

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração.|
| --help -h  | Mostrar esta mensagem de ajuda e sair.|
| --output -o| O formato da saída.  Valores permitidos: json, jsonc, table, tsv.  Padrão: json.|
| --query | Cadeia de caracteres de consulta JMESPath. Consulte http://jmespath.org/ para saber mais e obter exemplos.|
| --verbose  | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos.|

## <a name="sfctl-cluster-select"></a>seleção do cluster sfctl
Se conecta a um ponto de extremidade do cluster do Service Fabric.

Se estiver se conectando ao cluster seguro, especifique um certificado (.crt) e o arquivo chave (.key) ou um único arquivo com ambos (.pem). Não especifique ambos. Opcionalmente, se estiver se conectando a um cluster seguro, também especifique um caminho para um arquivo de pacote de autoridade de certificação ou o diretório de certificados de autoridade de certificação confiáveis.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --ponto de extremidade [obrigatório]| URL do ponto de extremidade, incluindo o prefixo HTTPS ou HTTPS e porta do cluster.|
| --aad             | Usar o Azure Active Directory para autenticação.|
| --ca              | Caminho para o diretório de certificados de autoridade de certificação para tratar como válido ou o arquivo de pacote de autoridade de certificação.|
| --cert            | Caminho para um arquivo de certificado do cliente.|
| --chave             | Caminho para um arquivo chave de certificado do cliente.|
| --sem verificação       | Desativar a verificação de certificados ao usar HTTPS, observe: essa é uma opção insegura e não deve ser usada para ambientes de produção.|
| --pem             | Caminho para um certificado de cliente, como um arquivo .pem.|

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --debug           | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração.|
| --help -h         | Mostrar esta mensagem de ajuda e sair.|
| --output -o       | O formato da saída.  Valores permitidos: json, jsonc, table, tsv.  Padrão: json.|
| --query           | Cadeia de caracteres de consulta JMESPath. Consulte http://jmespath.org/ para saber mais e obter exemplos.|
| --verbose         | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos.|

## <a name="sfctl-cluster-unprovision"></a>desprovisionamento do cluster sfctl
Desconfigurar os pacotes de código ou a configuração de um cluster do Service Fabric.

        Unprovision the code or configuration packages of a Service Fabric cluster.

### <a name="arguments"></a>Argumentos
|Argumento|Descrição|
| --- | --- |
|--versão do código  | A versão do pacote de código do cluster.|
|    --versão de configuração| A versão do manifesto de cluster.|
|    --timeout -t    | Tempo limite do servidor em segundos.  Padrão: 60.|

### <a name="global-arguments"></a>Argumentos globais
|Argumento|Descrição|
| --- | --- |
|--debug         | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração.|
 |   --help -h       | Mostrar esta mensagem de ajuda e sair.|
 |   --output -o     | O formato da saída.  Valores permitidos: json, jsonc, table, tsv.  Padrão: json.|
 |   --query         | Cadeia de caracteres de consulta JMESPath. Consulte http://jmespath.org/ para obter mais informações e
                      exemplos.|
 |   --verbose       | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos.|


## <a name="sfctl-cluster-upgrade"></a>upgrade do cluster sfctl
Começar a realizar os upgrades da versão do código ou configuração de um cluster do Service Fabric.

        Validate the supplied upgrade parameters and start upgrading the code or configuration
        version of a Service Fabric cluster if the parameters are valid.

### <a name="arguments"></a>Argumentos
|Argumento|Descrição|
| --- | --- |
|    --mapa de integridade do aplicativo                      | Dicionário codificado em JSON dos pares de nome do aplicativo e
                                            percentual máximo de problemas antes de gerar o erro.|
 |   --mapa de integridade do tipo do aplicativo                 | Dicionário codificado em JSON dos pares de tipo de aplicativo
                                            name and maximum percentage unhealthy before raising
                                            error.|
 |   – versão de código | A versão do código de cluster. | |   -- versão de config | A versão de configuração do cluster. | |   – avaliação de integridade de delta | Permite que a avaliação de integridade de delta, em vez de avaliação de integridade absoluta após a conclusão de cada domínio de atualização. | |   – nós de delta não íntegro | A porcentagem máxima permitida de nós de degradação de integridade permitida durante as atualizações de cluster.  Padrão: 10.
O delta é medido entre o estado de nós no início da atualização e o estado de nós no momento da avaliação de integridade. A verificação é executada após a conclusão do upgrade de cada domínio de upgrade para verificar se o estado global do cluster está dentro dos limites tolerados. | |   -Falha na ação | Os valores possíveis incluem: 'Inválido', 'Reverter', 'Manual'. | |   -forçar-reinicialização | Forçar a reinicialização. | |   -verificação de integridade de repetição | Tempo limite de novas tentativas de verificação de integridade medido em milissegundos. | |   – estabilização da verificação da integridade | Estabilização da verificação da integridade com duração medida em milissegundos. | |  – espera de verificação de integridade | Duração medida em milissegundos da espera de verificação de integridade. | |  – tempo limite de verificação do conjunto de réplica | Tempo limite de verificação medido em segundos do conjunto de réplicas de atualização. | |   – modo de upgrade contínuo | Os valores possíveis incluem: 'Inválido', 'UnmonitoredAuto', 'UnmonitoredManual', 'Monitorado'.  Padrão: UnmonitoredAuto.| | --timeout -t                | Tempo limite do servidor em segundos.  Padrão: 60. || --aplicativos prejudiciais A porcentagem máxima permitida de aplicativos prejudiciais antes de relatar um erro.
Por exemplo, para permitir que 10% dos aplicativos sejam problemáticos, esse valor seria 10. A porcentagem representa o máximo tolerado de aplicativos que podem estar não íntegros antes de o cluster ser considerado com erro. Se a porcentagem for respeitada, mas houver pelo menos um aplicativo não íntegro, a integridade será avaliada como ‘Aviso’. Isso é calculado dividindo o número de aplicativos problemáticos pelo número total de instâncias do aplicativo no cluster, exceto aplicativos de tipos de aplicativos que estão incluídos no ApplicationTypeHealthPolicyMap. A computação arredonda para tolerar uma falha em um número pequeno de aplicativos. | |   – nós problemáticos | A porcentagem máxima permitida de nós problemáticos antes de relatar um erro.
Por exemplo, para permitir que 10% dos nós sejam problemáticos, esse valor seria 10. A porcentagem representa o máximo tolerado de nós que podem estar não íntegros antes de o cluster ser considerado com erro. Se a porcentagem for respeitada, mas houver pelo menos um nó não íntegro, a integridade será avaliada como ‘Aviso’. Este percentual é calculado pela divisão do número de nós não íntegros pelo número total de nós no cluster. O cálculo é arredondado para cima para tolerar uma falha em um número pequeno de nós. Em grandes clusters, alguns nós sempre estarão para baixo ou sem reparos, portanto essa porcentagem deve ser configurada para tolerar que. | |   --nós prejudiciais de delta do domínio do upgrade | A porcentagem máxima permitida da degradação de integridade dos nós de domínio de upgrade permitidos durante os upgrades do cluster.
Padrão: 15.
O delta é medido entre o estado de nós do domínio do upgrade no início do upgrade e o estado dos nós no momento da avaliação de integridade. A verificação é executada após a conclusão do upgrade do domínio do upgrade para todos os domínios de upgrade para garantir que o estado dos domínios de upgrade estejam dentro dos limites tolerados. | |   -tempo limite do domínio do upgrade | Tempo limite do domínio do upgrade medido em milissegundos. | |   -tempo limite de upgrade | Tempo limite de upgrade medido em milissegundos. | |   -Aviso como erro | Os avisos são tratados com a mesma gravidade dos erros. |

### <a name="global-arguments"></a>Argumentos globais
    |Argumento|Descrição|
| --- | --- |
|--debug                               | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração.|
|    --help -h                             | Mostrar esta mensagem de ajuda e sair.|
|    --output -o                           | O formato da saída.  Valores permitidos: json, jsonc, table, tsv.
                                            Padrão: json.|
|    --query                               | Cadeia de caracteres de consulta JMESPath. Consulte http://jmespath.org/ para obter mais
                                            informações e exemplos.|
|    --verbose                             | Aumentar o nível de detalhes do log. Use --debug para depuração completa
                                            logs.|

## <a name="next-steps"></a>Próximas etapas
- [Configurar](service-fabric-cli.md) a CLI do Service Fabric.
- Saiba como usar a CLI do Service Fabric usando os [scripts de exemplo](/azure/service-fabric/scripts/sfctl-upgrade-application).