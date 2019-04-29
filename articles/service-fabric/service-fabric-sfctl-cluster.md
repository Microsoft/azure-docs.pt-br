---
title: CLI do Azure Service Fabric - cluster sfctl | Microsoft Docs
description: Descreve os comandos do cluster sfctl da CLI do Service Fabric.
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
ms.openlocfilehash: 7bb399472d7e0ab14e6399fc8652d2eb132a866a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60837295"
---
# <a name="sfctl-cluster"></a>cluster sfctl
Selecionar, gerenciar e operar clusters do Service Fabric.

## <a name="commands"></a>Comandos

|Comando|DESCRIÇÃO|
| --- | --- |
| versões de código | Obtém uma lista de versões de códigos de malha que são provisionadas em um cluster do Service Fabric. |
| versões de configurações | Obtém uma lista de versões de configurações de malha que são provisionadas em um cluster do Service Fabric. |
| integridade | Obtém a integridade de um cluster do Service Fabric. |
| manifest | Obter o manifesto do cluster do Service Fabric. |
| cancelamento de operação | Cancela uma operação com falha induzida pelo usuário. |
| operation-list | Obtém uma lista de operações com falha induzidas pelo usuário e filtradas por entrada fornecida. |
| provision | Provisão dos pacotes de código ou a configuração de um cluster do Service Fabric. |
| sistema de recuperação | Indica ao cluster do Service Fabric que ele deve tentar recuperar os serviços do sistema que estão atualmente paralisados na perda de quorum. |
| report-health | Envia um relatório de integridade sobre o cluster do Service Fabric. |
| selecionar | Se conecta a um ponto de extremidade do cluster do Service Fabric. |
| show-connection | Mostre qual cluster do Service Fabric essa instância sfctl está conectada. |
| unprovision | Desconfigurar os pacotes de código ou a configuração de um cluster do Service Fabric. |
| atualizar | Começar a realizar os upgrades da versão do código ou configuração de um cluster do Service Fabric. |
| upgrade-resume | Verifica o upgrade do cluster para passar para o próximo domínio de upgrade. |
| upgrade-rollback | Reverter o upgrade de um cluster de Service Fabric. |
| upgrade-status | Obtém o andamento do upgrade do cluster atual. |
| atualização do upgrade | Atualize os parâmetros de upgrade de um upgrade de cluster do Service Fabric. |

## <a name="sfctl-cluster-code-versions"></a>versões de código sfctl cluster
Obtém uma lista de versões de códigos de malha que são provisionadas em um cluster do Service Fabric.

Obtém uma lista de informações sobre a malha versões de código que são provisionadas no cluster. O parâmetro CodeVersion pode ser usado para filtrar, opcionalmente, a saída para somente aquela versão específica.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --versão do código | A versão de produto do Service Fabric. |
| --timeout -t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, table, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-cluster-config-versions"></a>configuração de cluster sfctl versões
Obtém uma lista de versões de configurações de malha que são provisionadas em um cluster do Service Fabric.

Obtém uma lista de informações sobre a malha versões de configuração que são provisionadas no cluster. O parâmetro ConfigVersion pode ser usado para filtrar, opcionalmente, a saída para somente aquela versão específica.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --versão de configuração | A versão de configuração do Service Fabric. |
| --timeout -t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, table, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-cluster-health"></a>Integridade do cluster sfctl
Obtém a integridade de um cluster do Service Fabric.

Use EventsHealthStateFilter para filtrar o conjunto de eventos de integridade relatados no cluster de acordo com o estado de integridade. Da mesma forma, use NodesHealthStateFilter e ApplicationsHealthStateFilter para filtrar o conjunto de nós e aplicativos retornados com base no seu estado de integridade agregada.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --filtro do estado de integridade dos aplicativos | Permite filtrar os objetos de estado de integridade do aplicativo retornados no resultado da consulta de integridade do cluster com base em seu estado de integridade. Os valores possíveis para este parâmetro incluem valor inteiro obtido de membros ou operações bit a bit em membros da enumeração HealthStateFilter. São retornados apenas aplicativos que correspondem ao filtro. Todos os aplicativos são usados para avaliar o estado de integridade agregada. Se não for especificado, retorna todas as entradas. Os valores de estado são enumerações baseadas no sinalizador. Assim, o valor pode ser uma combinação desses valores obtidos, usando o operador 'OR' bit a bit. Por exemplo, se o valor fornecido for 6, o estado de integridade dos aplicativos com valor HealthState de OK (2) e Aviso (4) será retornado.  <br> – Default – Valor padrão. Corresponde a qualquer HealthState. O valor é zero.  <br> -None- Filtro que não corresponde a qualquer valor de HealthState. Usado para não retornar qualquer resultado em um determinado conjunto de estados. O valor é 1.  <br> - Ok - Filtro que corresponde à entrada com o valor de HealthState Ok. O valor é 2.  <br> -Warning- Filtro que corresponde à entrada com o valor de HealthState Warning. O valor é 4.  <br> -Error- Filtro que corresponde a entrada com o valor de HealthState Error. O valor é 8.  <br> - All - Filtro que corresponde à entrada com qualquer valor de HealthState. O valor é 65535. |
| --events-health-state-filter | Permite filtrar a coleção de objetos HealthEvent retornados com base no estado de integridade. Os valores possíveis para esse parâmetro incluem o valor de inteiro de um dos seguintes estados de integridade. Somente os eventos que correspondem ao filtro são retornados. Todos os eventos são usados para avaliar o estado de integridade agregado. Se não for especificado, retorna todas as entradas. Os valores de estado são enumerações baseadas no sinalizador. Assim, o valor pode ser uma combinação desses valores obtidos, usando o operador “OR” bit a bit. Por exemplo, se o valor fornecido for 6, serão retornados todos os eventos com o valor de HealthState de OK (2) e de Aviso (4).  <br> – Default – Valor padrão. Corresponde a qualquer HealthState. O valor é zero.  <br> -None- Filtro que não corresponde a qualquer valor de HealthState. Usado para não retornar qualquer resultado em um determinado conjunto de estados. O valor é 1.  <br> - Ok - Filtro que corresponde à entrada com o valor de HealthState Ok. O valor é 2.  <br> -Warning- Filtro que corresponde à entrada com o valor de HealthState Warning. O valor é 4.  <br> -Error- Filtro que corresponde a entrada com o valor de HealthState Error. O valor é 8.  <br> - All - Filtro que corresponde à entrada com qualquer valor de HealthState. O valor é 65535. |
| --exclude-health-statistics | Indica se as estatísticas de integridade devem ser retornadas como parte do resultado da consulta. Falso por padrão. As estatísticas mostram o número de entidades filhas nos estados de integridade Ok, Warning e Error. |
| --include-system-application-health-statistics | Indica se as estatísticas de integridade devem incluir as estatísticas de integridade do aplicativo fabric\:/System. Falso por padrão. Se IncludeSystemApplicationHealthStatistics for definido como true, as estatísticas de integridade incluirão as entidades que pertencem ao aplicativo fabric \:/System. Caso contrário, o resultado da consulta inclui estatísticas de integridade somente para aplicativos de usuário. As estatísticas de integridade devem ser incluídas no resultado da consulta para que este parâmetro seja aplicado. |
| --nodes-health-state-filter | Permite filtrar os objetos de estado de integridade de nó retornados no resultado da consulta de integridade do cluster com base em seu estado de integridade. Os valores possíveis para esse parâmetro incluem o valor de inteiro de um dos seguintes estados de integridade. Só retornam os nós que correspondem ao filtro. Todos os nós são usados para avaliar o estado de integridade agregado. Se não for especificado, retorna todas as entradas. Os valores de estado são enumerações baseadas no sinalizador. Assim, o valor pode ser uma combinação desses valores obtidos, usando o operador 'OR' bit a bit. Por exemplo, se o valor fornecido for 6, o estado de integridade dos nós com o valor HealthState de OK (2) e Aviso (4) será retornado.  <br> – Default – Valor padrão. Corresponde a qualquer HealthState. O valor é zero.  <br> -None- Filtro que não corresponde a qualquer valor de HealthState. Usado para não retornar qualquer resultado em um determinado conjunto de estados. O valor é 1.  <br> - Ok - Filtro que corresponde à entrada com o valor de HealthState Ok. O valor é 2.  <br> -Warning- Filtro que corresponde à entrada com o valor de HealthState Warning. O valor é 4.  <br> -Error- Filtro que corresponde a entrada com o valor de HealthState Error. O valor é 8.  <br> - All - Filtro que corresponde à entrada com qualquer valor de HealthState. O valor é 65535. |
| --timeout -t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, table, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-cluster-manifest"></a>Manifesto do cluster sfctl
Obter o manifesto do cluster do Service Fabric.

Obter o manifesto do cluster do Service Fabric. O manifesto do cluster contém propriedades do cluster que incluem diferentes tipos de nó no cluster, as configurações de segurança, falhas e topologias de domínio de atualização etc. Essas propriedades são especificadas como parte do arquivo Clusterconfig ao implantar um cluster autônomo. No entanto, a maioria das informações no manifesto do cluster é gerada internamente pela malha do serviço durante a implantação de cluster em outros cenários de implantação (por exemplo, ao usar o portal do Azure). O conteúdo do manifesto do cluster é apenas para fins informativos e os usuários não devem depender do formato do conteúdo do arquivo ou sua interpretação.

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

## <a name="sfctl-cluster-operation-cancel"></a>operação de cluster sfctl cancelamento
Cancela uma operação com falha induzida pelo usuário.

As seguintes APIs iniciar operações com falha que podem ser canceladas usando CancelOperation\: StartDataLoss, StartQuorumLoss, StartPartitionRestart, StartNodeTransition. Se forçar for false, em seguida, a operação especificada induzido pelo usuário será normalmente interrompida e limpos.  Se forçar for true, o comando será anulado e algum estado interno pode ser deixado para trás.  Especificar force como verdadeiro deve ser usada com cuidado. Não é permitido chamar essa API com force definido como verdadeiro, até que essa API já foi chamada no mesmo comando de teste com force definido como false, ou a menos que o comando de teste já tem um OperationState de OperationState.RollingBack. 

Esclarecimento\: significa OperationState.RollingBack que o sistema será/está limpando interno do sistema de estado causado pela execução do comando.  Ele não irá restaurar os dados se o comando de teste foi causar perda de dados.  Por exemplo, se você chamar StartDataLoss chamar esta API, o sistema só limpará o estado interno da execução do comando. Ele não irá restaurar os dados da partição de destino, se o comando progrediu distante o suficiente para causar perda de dados. 

> [!NOTE]
> Se essa API é invocada com a equipe = = true, interno de estado pode ser deixado para trás.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --id da operação [requerido] | Um GUID que identifica uma chamada dessa API.  Isso é passado para a API GetProgress correspondente. |
| -Force | Indica se deve normalmente revertê-lo e limpar o estado do sistema interno modificado executando a operação induzido pelo usuário. |
| --timeout -t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, table, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-cluster-operation-list"></a>lista de operações de cluster de sfctl
Obtém uma lista de operações com falha induzidas pelo usuário e filtradas por entrada fornecida.

Obtém a lista de operações com falha induzido pelo usuário filtrados por entrada fornecida.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| -filtro de estado | Usado para filtrar do OperationState para operações induzido pelo usuário. <br> 65535 - Selecionar tudo <br> 1 - selecionar em execução <br> 2 - selecione RollingBack <br>8 - Selecione concluído <br>16 - selecionar com falha <br>32 - selecione cancelado <br>64 - selecione ForceCancelled.  <br>Padrão\: 65535. |
| --timeout -t | Tempo limite do servidor em segundos.  Padrão\: 60. |
| --type-filter | Usado para filtrar OperationType para operações induzido pelo usuário. <br> 65535 - Selecionar tudo <br> 1 - Selecione PartitionDataLoss. <br> 2 - Selecione PartitionQuorumLoss. <br> 4 - Selecione PartitionRestart. <br> 8 - Selecione NodeTransition.  <br> Padrão\: 65535. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, table, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-cluster-provision"></a>provisionamento do cluster sfctl
Provisão dos pacotes de código ou a configuração de um cluster do Service Fabric.

Valide e provisione o código ou os pacotes de configuração de um cluster do Service Fabric.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --caminho do arquivo do manifesto do cluster | O caminho do arquivo do manifesto do cluster. |
| --caminho do arquivo do código | O caminho do pacote de código do cluster. |
| --timeout -t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, table, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-cluster-recover-system"></a>sistema de recuperação de cluster de sfctl
Indica ao cluster do Service Fabric que ele deve tentar recuperar os serviços do sistema que estão atualmente paralisados na perda de quorum.

Indica ao cluster do Service Fabric que ele deve tentar recuperar os serviços do sistema que estão atualmente paralisados na perda de quorum. Esta operação somente deve ser realizada se souber que as réplicas inativas não podem ser recuperadas. O uso incorreto dessa API pode causar possíveis perdas de dados.

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

## <a name="sfctl-cluster-report-health"></a>relatório de cluster sfctl integridade
Envia um relatório de integridade sobre o cluster do Service Fabric.

O relatório deve conter as informações sobre a origem do relatório de integridade e propriedade na qual ele será relatado. O relatório é enviado a um nó de gateway do Service Fabric, que encaminha para o repositório de integridade. O relatório pode ser aceito pelo gateway, mas rejeitado pelo repositório de integridade após validação adicional. Por exemplo, o repositório de integridade pode rejeitar o relatório devido a um parâmetro inválido, como um número de sequência obsoleto. Para ver se o relatório foi aplicado no health store, verifique se o relatório é exibido na HealthEvents do cluster.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
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

## <a name="sfctl-cluster-select"></a>seleção do cluster sfctl
Se conecta a um ponto de extremidade do cluster do Service Fabric.

Se estiver se conectando ao cluster seguro, especifique um caminho absoluto para um certificado (. crt) e o arquivo de chave (chave) ou um único arquivo com ambos (. PEM). Não especifique ambos. Opcionalmente, se estiver se conectando a um cluster seguro, especifique também um caminho absoluto para um arquivo de pacote de autoridade de certificação ou o diretório de certificados de autoridade de certificação confiáveis. Se um diretório de Certificados de Autoridade de Certificação estiver sendo usado, o `c_rehash <directory>` fornecido pelo OpenSSL precisará ser executado primeiro para computar os hashes de certificado e criar os links simbólicos apropriados.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --ponto de extremidade [obrigatório] | URL do ponto de extremidade, incluindo o prefixo HTTPS ou HTTPS e porta do cluster. |
| --aad | Usar o Azure Active Directory para autenticação. |
| --ca | Caminho absoluto para o diretório de certificados de autoridade de certificação para tratar como válido ou o arquivo de pacote de autoridade de certificação. |
| --cert | Caminho absoluto para um arquivo de certificado de cliente. |
| --chave | Caminho absoluto para o arquivo de chave de certificado de cliente. |
| --sem verificação | Desativar a verificação de certificados ao usar HTTPS, observe\:: essa é uma opção insegura e não deve ser usada para ambientes de produção. |
| --pem | Caminho absoluto para o certificado de cliente, como um arquivo. PEM. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, table, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-cluster-show-connection"></a>sfctl cluster show-connection
Mostre qual cluster do Service Fabric essa instância sfctl está conectada.

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, table, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-cluster-unprovision"></a>desprovisionamento do cluster sfctl
Desconfigurar os pacotes de código ou a configuração de um cluster do Service Fabric.

Há suporte para desprovisionar o código e a configuração separadamente.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --versão do código | A versão do pacote de código do cluster. |
| --versão de configuração | A versão do manifesto de cluster. |
| --timeout -t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, table, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-cluster-upgrade"></a>upgrade do cluster sfctl
Começar a realizar os upgrades da versão do código ou configuração de um cluster do Service Fabric.

Valide os parâmetros de upgrade fornecidos e comece a atualizar o código ou a versão de configuração de um cluster do Service Fabric se os parâmetros forem válidos.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --mapa de integridade do aplicativo | O JSON codificava o dicionário de pares de nome de aplicativo e percentual máximo não íntegro antes de gerar o erro. |
| --mapa de integridade do tipo do aplicativo | O JSON codificava o dicionário de pares de nome de tipo de aplicativo e percentual máximo não íntegro antes de gerar o erro. |
| --versão do código | A versão do código do cluster. |
| --versão de configuração | A versão da configuração do cluster. |
| --delta-health-evaluation | Habilita a avaliação de integridade delta em vez da avaliação de integridade absoluta após a conclusão de cada domínio de atualização. |
| --delta-unhealthy-nodes | O percentual máximo permitido de degradação de integridade de nós permitida durante os upgrades do cluster.  Padrão\: 10. <br><br> O delta é medido entre o estado de nós no início da atualização e o estado de nós no momento da avaliação de integridade. A verificação é realizada após a conclusão de cada atualização de domínio de atualização para garantir que o estado global do cluster esteja dentro dos limites tolerados. |
| --ação de falha | Os valores possíveis incluem\: 'Inválido', 'Reversão', 'Manual'. |
| -força-reinicialização | Os processos são reiniciados de modo forçado durante a atualização, mesmo quando a versão do código não foi alterada. <br><br> A atualização altera apenas a configuração ou os dados. |
| --nova tentativa de verificação da integridade | O período entre as tentativas de realizar verificações de integridade, se o aplicativo ou o cluster não estiver íntegro. |
| --estabilidade de verificação de integridade | A quantidade de tempo que o aplicativo ou o cluster deve permanecer íntegro antes que a atualização passe para o próximo domínio de atualização. <br><br> Primeiro, é interpretado como uma cadeia de caracteres representando uma duração ISO 8601. Se isso falhar, será interpretado como um número que representa o número total de milissegundos. |
| --espera de verificação de integridade | O período de espera após a conclusão de um domínio de atualização, antes de iniciar o processo de verificações de integridade. |
| --replica-set-check-timeout | A quantidade máxima de tempo para bloqueio do processamento de um domínio de atualização e prevenção da perda de disponibilidade quando houver problemas inesperados. <br><br> Quando esse tempo limite expira, o processamento do domínio de atualização continua, independentemente de problemas de perda de disponibilidade. O tempo limite será redefinido no início de cada domínio de atualização. Os valores válidos estão entre 0 e 42949672925, inclusive. |
| --rolling-upgrade-mode | Os valores possíveis incluem\: 'Invalid', 'UnmonitoredAuto', 'UnmonitoredManual', 'Monitored'.  Padrão\: UnmonitoredAuto. |
| --timeout -t | Tempo limite do servidor em segundos.  Padrão\: 60. |
| --unhealthy-applications | O percentual máximo permitido de aplicativos não íntegros antes de relatar um erro. <br><br> Por exemplo, para permitir que 10% dos aplicativos sejam problemáticos, esse valor seria 10. A porcentagem representa o máximo tolerado de aplicativos que podem estar não íntegros antes de o cluster ser considerado com erro. Se a porcentagem for respeitada, mas houver pelo menos um aplicativo não íntegro, a integridade será avaliada como ‘Aviso’. Isso é calculado dividindo o número de aplicativos problemáticos pelo número total de instâncias do aplicativo no cluster, exceto aplicativos de tipos de aplicativos que estão incluídos no ApplicationTypeHealthPolicyMap. O cálculo é arredondado para cima para tolerar uma falha em um número pequeno de aplicativos. |
| --unhealthy-nodes | O percentual máximo permitido de nós não íntegros antes de relatar um erro. <br><br> Por exemplo, para permitir que 10% dos nós sejam problemáticos, esse valor seria 10. A porcentagem representa o máximo tolerado de nós que podem estar não íntegros antes de o cluster ser considerado com erro. Se a porcentagem for respeitada, mas houver pelo menos um nó não íntegro, a integridade será avaliada como ‘Aviso’. Este percentual é calculado pela divisão do número de nós não íntegros pelo número total de nós no cluster. O cálculo é arredondado para cima para tolerar uma falha em um número pequeno de nós. Em clusters grandes, alguns nós sempre estarão inoperantes ou ausentes para reparos, de modo que esse percentual deve ser configurado para tolerá-los. |
| --upgrade-domain-delta-unhealthy-nodes | O percentual máximo permitido de degradação de integridade de nós de domínio de atualização permitida durante os upgrades do cluster.  Padrão\: 15. <br><br> O delta é medido entre o estado de nós do domínio do upgrade no início do upgrade e o estado dos nós no momento da avaliação de integridade. A verificação é realizada após a conclusão de cada atualização de domínio de atualização para todos os domínios de atualização concluídos para garantir que o estado dos domínios de atualização esteja dentro dos limites tolerados. |
| -tempo limite ao domínio de upgrade | A quantidade de tempo que cada domínio de atualização deve concluir antes de FailureAction ser executado. <br><br> Primeiro, é interpretado como uma cadeia de caracteres representando uma duração ISO 8601. Se isso falhar, será interpretado como um número que representa o número total de milissegundos. |
| -tempo limite de upgrade | A quantidade de tempo que a atualização geral deve concluir antes de FailureAction ser executado. <br><br> Primeiro, é interpretado como uma cadeia de caracteres representando uma duração ISO 8601. Se isso falhar, será interpretado como um número que representa o número total de milissegundos. |
| --aviso-como-erro | Indica se os avisos são ou não tratados com a mesma severidade que os erros. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, table, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-cluster-upgrade-resume"></a>atualização de cluster sfctl continuar
Verifica o upgrade do cluster para passar para o próximo domínio de upgrade.

Mude cluster configuração ou código de atualização para o próximo domínio de atualização se apropriado.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| – domínio de atualização [requerido] | O próximo domínio de atualização para essa atualização de cluster. |
| --timeout -t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, table, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-cluster-upgrade-rollback"></a>atualização de cluster sfctl reversão
Reverter o upgrade de um cluster de Service Fabric.

Reverta a atualização de configuração ou código de um cluster do Service Fabric.

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

## <a name="sfctl-cluster-upgrade-status"></a>status de atualização de cluster de sfctl
Obtém o andamento do upgrade do cluster atual.

Obtém o progresso atual da atualização do cluster em andamento. Se nenhuma atualização estiver em andamento, obtenha o último estado da atualização do cluster anterior.

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

## <a name="sfctl-cluster-upgrade-update"></a>sfctl atualização-atualização de cluster
Atualize os parâmetros de upgrade de um upgrade de cluster do Service Fabric.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --mapa de integridade do aplicativo | O JSON codificava o dicionário de pares de nome de aplicativo e percentual máximo não íntegro antes de gerar o erro. |
| --mapa de integridade do tipo do aplicativo | O JSON codificava o dicionário de pares de nome de tipo de aplicativo e percentual máximo não íntegro antes de gerar o erro. |
| --delta-health-evaluation | Habilita a avaliação de integridade delta em vez da avaliação de integridade absoluta após a conclusão de cada domínio de atualização. |
| --delta-unhealthy-nodes | O percentual máximo permitido de degradação de integridade de nós permitida durante os upgrades do cluster.  Padrão\: 10. <br><br> O delta é medido entre o estado de nós no início da atualização e o estado de nós no momento da avaliação de integridade. A verificação é realizada após a conclusão de cada atualização de domínio de atualização para garantir que o estado global do cluster esteja dentro dos limites tolerados. |
| --ação de falha | Os valores possíveis incluem\: 'Inválido', 'Reversão', 'Manual'. |
| -força-reinicialização | Os processos são reiniciados de modo forçado durante a atualização, mesmo quando a versão do código não foi alterada. <br><br> A atualização altera apenas a configuração ou os dados. |
| --nova tentativa de verificação da integridade | O período entre as tentativas de realizar verificações de integridade, se o aplicativo ou o cluster não estiver íntegro. |
| --estabilidade de verificação de integridade | A quantidade de tempo que o aplicativo ou o cluster deve permanecer íntegro antes que a atualização passe para o próximo domínio de atualização. <br><br> Primeiro, é interpretado como uma cadeia de caracteres representando uma duração ISO 8601. Se isso falhar, será interpretado como um número que representa o número total de milissegundos. |
| --espera de verificação de integridade | O período de espera após a conclusão de um domínio de atualização, antes de iniciar o processo de verificações de integridade. |
| --replica-set-check-timeout | A quantidade máxima de tempo para bloqueio do processamento de um domínio de atualização e prevenção da perda de disponibilidade quando houver problemas inesperados. <br><br> Quando esse tempo limite expira, o processamento do domínio de atualização continua, independentemente de problemas de perda de disponibilidade. O tempo limite será redefinido no início de cada domínio de atualização. Os valores válidos estão entre 0 e 42949672925, inclusive. |
| --rolling-upgrade-mode | Os valores possíveis incluem\: 'Invalid', 'UnmonitoredAuto', 'UnmonitoredManual', 'Monitored'.  Padrão\: UnmonitoredAuto. |
| --timeout -t | Tempo limite do servidor em segundos.  Padrão\: 60. |
| --unhealthy-applications | O percentual máximo permitido de aplicativos não íntegros antes de relatar um erro. <br><br> Por exemplo, para permitir que 10% dos aplicativos sejam problemáticos, esse valor seria 10. A porcentagem representa o máximo tolerado de aplicativos que podem estar não íntegros antes de o cluster ser considerado com erro. Se a porcentagem for respeitada, mas houver pelo menos um aplicativo não íntegro, a integridade será avaliada como ‘Aviso’. Isso é calculado dividindo o número de aplicativos problemáticos pelo número total de instâncias do aplicativo no cluster, exceto aplicativos de tipos de aplicativos que estão incluídos no ApplicationTypeHealthPolicyMap. O cálculo é arredondado para cima para tolerar uma falha em um número pequeno de aplicativos. |
| --unhealthy-nodes | O percentual máximo permitido de nós não íntegros antes de relatar um erro. <br><br> Por exemplo, para permitir que 10% dos nós sejam problemáticos, esse valor seria 10. A porcentagem representa o máximo tolerado de nós que podem estar não íntegros antes de o cluster ser considerado com erro. Se a porcentagem for respeitada, mas houver pelo menos um nó não íntegro, a integridade será avaliada como ‘Aviso’. Este percentual é calculado pela divisão do número de nós não íntegros pelo número total de nós no cluster. O cálculo é arredondado para cima para tolerar uma falha em um número pequeno de nós. Em clusters grandes, alguns nós sempre estarão inoperantes ou ausentes para reparos, de modo que esse percentual deve ser configurado para tolerá-los. |
| --upgrade-domain-delta-unhealthy-nodes | O percentual máximo permitido de degradação de integridade de nós de domínio de atualização permitida durante os upgrades do cluster.  Padrão\: 15. <br><br> O delta é medido entre o estado de nós do domínio do upgrade no início do upgrade e o estado dos nós no momento da avaliação de integridade. A verificação é realizada após a conclusão de cada atualização de domínio de atualização para todos os domínios de atualização concluídos para garantir que o estado dos domínios de atualização esteja dentro dos limites tolerados. |
| -tempo limite ao domínio de upgrade | A quantidade de tempo que cada domínio de atualização deve concluir antes de FailureAction ser executado. <br><br> Primeiro, é interpretado como uma cadeia de caracteres representando uma duração ISO 8601. Se isso falhar, será interpretado como um número que representa o número total de milissegundos. |
| --tipo de upgrade | Os valores possíveis incluem\: 'Inválido', 'Reverter', 'Rolling_ForceRestart'.  Padrão\: sem interrupção. |
| -tempo limite de upgrade | A quantidade de tempo que a atualização geral deve concluir antes de FailureAction ser executado. <br><br> Primeiro, é interpretado como uma cadeia de caracteres representando uma duração ISO 8601. Se isso falhar, será interpretado como um número que representa o número total de milissegundos. |
| --aviso-como-erro | Indica se os avisos são ou não tratados com a mesma severidade que os erros. |

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