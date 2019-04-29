---
title: CLI do Azure Service Fabric - sfctl chaos| Microsoft Docs
description: Descreve os comandos do sfctl chaos da CLI do Service Fabric.
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
ms.openlocfilehash: b584ec301f0f4841c8df8fbbafb410abf645c373
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60837344"
---
# <a name="sfctl-chaos"></a>sfctl chaos
Iniciar, parar e emitir relatórios sobre o serviço de teste de caos.

## <a name="subgroups"></a>Subgrupos
|Subgrupo|DESCRIÇÃO|
| --- | --- |
| [schedule](service-fabric-sfctl-chaos-schedule.md) | Obter e definir a agenda do caos. |
## <a name="commands"></a>Comandos

|Comando|DESCRIÇÃO|
| --- | --- |
| events | Obtém o próximo segmento dos eventos do caos com base no token de continuação ou no intervalo. |
| get | Obter o status do caos. |
| iniciar | Inicia o Chaos no cluster. |
| parar | Interrompe o Chaos se ele estiver em execução no cluster e coloca a Agenda do Chaos em um estado parado. |

## <a name="sfctl-chaos-events"></a>sfctl chaos events
Obtém o próximo segmento dos eventos do caos com base no token de continuação ou no intervalo.

Para obter o próximo segmento dos eventos do Chaos, especifique o ContinuationToken. Para obter o início de um novo segmento de eventos do Chaos, especifique o intervalo de tempo por meio de StartTimeUtc e EndTimeUtc. Não é possível especificar o ContinuationToken e o intervalo de tempo na mesma chamada. Quando houver mais de 100 eventos de Chaos, os eventos de Chaos retornarão em vários segmentos, sendo que um segmento contém no máximo 100 eventos de Chaos. E para obter o próximo segmento, faça uma chamada para essa API com o token de continuação.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --continuation-token | O parâmetro do token de continuação é usado para obter o próximo conjunto de resultados. Um token de continuação com um valor não vazio será incluso na resposta da API quando os resultados do sistema não couberem em uma única resposta. Quando esse valor for passado para a próxima chamada de API, a API retornará o próximo conjunto de resultados. Se não houver mais resultados, o token de continuação não conterá um valor. O valor desse parâmetro não deve ser codificado em URL. |
| --hora de término utc | O horário do arquivo do Windows que representa a hora de término do intervalo de tempo para o qual um relatório Chaos deve ser gerado. Veja o [Método DateTime.ToFileTimeUtc](https://msdn.microsoft.com/library/system.datetime.tofiletimeutc(v=vs.110).aspx) para obter detalhes. |
| --max-results | O número máximo de resultados a serem retornados como parte das consultas paginadas. Esse parâmetro define o limite superior no número de resultados retornados. Os resultados retornados podem ser inferiores aos resultados máximos especificados se não couberem na mensagem, de acordo com as restrições de tamanho máximo de mensagem definidas na configuração. Se esse parâmetro for zero, ou não for especificado, a consulta paginada incluirá o máximo de resultados possível na mensagem de retorno. |
| --start-time-utc | O horário do arquivo do Windows que representa a hora de início do intervalo de tempo para o qual um relatório Chaos deve ser gerado. Veja o [Método DateTime.ToFileTimeUtc](https://msdn.microsoft.com/library/system.datetime.tofiletimeutc(v=vs.110).aspx) para obter detalhes. |
| --timeout -t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, table, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-chaos-get"></a>sfctl chaos get
Obter o status do caos.

Obtenha o status do Chaos indicando se o Chaos está ou não em execução, os parâmetros do Chaos usados para execução e o status da Agenda do Chaos.

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

## <a name="sfctl-chaos-start"></a>início sfctl chaos
Inicia o Chaos no cluster.

Se Chaos já não estiver em execução no cluster, inicia o Chaos com os parâmetros passados no Chaos. Se o Chaos já estiver em execução quando esta chamada for feita, ela falhará com o código de erro FABRIC_E_CHAOS_ALREADY_RUNNING.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| -- mapa de política de integridade do tipo de aplicativo | Lista codificada JSON com percentual máximo de aplicativos prejudiciais para tipos específicos de aplicativos. Cada entrada especifica como uma chave o nome do tipo de aplicativo e como valor um número inteiro que represente a porcentagem MaxPercentUnhealthyApplications usada para avaliar os aplicativos do tipo de aplicativo especificado. <br><br> Define um mapa com percentual máximo de aplicativos prejudiciais para tipos específicos de aplicativos. Cada entrada especifica como uma chave o nome do tipo de aplicativo e como valor um número inteiro que represente a porcentagem MaxPercentUnhealthyApplications usada para avaliar os aplicativos do tipo de aplicativo especificado. O mapa da política de integridade do tipo de aplicativo pode ser usado durante a avaliação da integridade do cluster para descrever tipos especiais de aplicativo. Os tipos de aplicativos incluídos no mapa são avaliados em relação à porcentagem especificada no mapa e não com o MaxPercentUnhealthyApplications global definido na política de integridade do cluster. Os aplicativos de tipos de aplicativos especificados no mapa não são contados no pool global de aplicativos. Por exemplo, se alguns aplicativos de um tipo são críticos, o administrador do cluster pode adicionar uma entrada no mapa para esse tipo de aplicativo e atribuir a ela um valor de 0% (ou seja, não tolerar falhas). Todos os outros aplicativos podem ser avaliados com MaxPercentUnhealthyApplications definido para 20% para tolerar algumas falhas de milhares de instâncias de aplicativos. O mapa de política de integridade de tipo de aplicativo é usado somente se o manifesto do cluster permite a avaliação de integridade de tipo de aplicativo usando a entrada de configuração para HealthManager/EnableApplicationTypeHealthEvaluation. |
| --chaos-target-filter | Dicionário codificado para JSON com duas chaves de tipo de cadeia de caracteres. As duas chaves são NodeTypeInclusionList e ApplicationInclusionList. Os valores para ambas as chaves são lista de cadeia de caracteres. chaos_target_filter define todos os filtros de para falhas do Chaos intencionais, por exemplo, falha apenas em determinados tipos de nó ou falha apenas em determinados aplicativos. <br><br> Se chaos_target_filter não for usado, o Chaos falha em todas as entidades de cluster. Se chaos_target_filter for usado, o Chaos falha apenas nas entidades que atendem a especificação de chaos_target_filter. NodeTypeInclusionList e ApplicationInclusionList permitem apenas uma semântica de união. Não é possível especificar uma interseção de NodeTypeInclusionList e ApplicationInclusionList. Por exemplo, não é possível especificar "falha deste aplicativo somente quando ele estiver nesse tipo de nó." Depois que uma entidade é incluída em NodeTypeInclusionList ou ApplicationInclusionList, essa entidade não pode ser excluída usando ChaosTargetFilter. Mesmo se não aparecer aplicativoX no ApplicationInclusionList, em alguns aplicativosX de iteração Chaos podem apresentar falha porque isso acontece de estar em um nó de nodeTypeY que está incluído no NodeTypeInclusionList. Se NodeTypeInclusionList e ApplicationInclusionList são vazios, ArgumentException será lançada. Todos os tipos de falhas (reiniciar o nó, reiniciar o pacote de códigos, remover a réplica, reiniciar a réplica, mover primário e mover secundário) estão habilitados para os nós desses tipos de nó. Se um tipo de nó (digamos NodeTypeX) não aparece na NodeTypeInclusionList, então as falhas de nível de nó (como NodeRestart) nunca serão habilitadas para os nós de NodeTypeX, mas falhas de pacote e de réplica de código ainda podem ser habilitadas para NodeTypeX se um aplicativo no ApplicationInclusionList vir a residir em um nó de NodeTypeX. No máximo 100 nomes de tipo de nó podem ser incluídos nessa lista; para aumentar esse número, uma atualização de configuração é necessária para a configuração de MaxNumberOfNodeTypesInChaosEntityFilter. Todas as réplicas que pertencem aos serviços desses aplicativos são receptivos a falhas de réplica (reinicialização de réplica, remover réplica, mover primário e secundário de movimentação) por Chaos. Chaos pode reiniciar um pacote de código somente se o pacote de código hospeda réplicas somente desses aplicativos. Se um aplicativo não aparecer nessa lista, ele ainda pode falhar em alguma iteração do Chaos se o aplicativo terminar em um nó de um tipo de nó que está incluído em NodeTypeInclusionList. No entanto se aplicativoX for vinculado a nodeTypeY por meio de restrições de posicionamento e aplicativoX está ausente do ApplicationInclusionList e nodeTypeY está ausente do NodeTypeInclusionList, em seguida, aplicativoX será nunca interrompido. No máximo 1000 nomes de tipo de nó podem ser incluídos nessa lista; para aumentar esse número, uma atualização de configuração é necessária para a configuração de MaxNumberOfApplicationsInChaosEntityFilter. |
| --context | Um mapa com codificação para JSON de pares chave/valor de tipo (cadeia de caracteres, cadeia de caracteres). O mapa pode ser usado para registrar informações sobre a execução do Chaos. Não pode haver mais de 100 desses pares e cada cadeia de caracteres (chave ou valor) pode ter no máximo 4095 caracteres. Esse mapa é definido pelo iniciador da execução do Chaos para armazenar opcionalmente o contexto sobre a execução específica. |
| --desabilitar-mover-réplica-falhas | Desabilita as falhas de movimentação primárias e secundárias. |
| --max-cluster-estabilização | A quantidade máxima de tempo de espera para todas as entidades do cluster se tornam estáveis e íntegras.  Padrão\: 60. <br><br> O Chaos é executado em iterações e no início de cada iteração ele valida a integridade de entidades de cluster. Durante a validação, se uma entidade de cluster não está estável e íntegra no MaxClusterStabilizationTimeoutInSeconds, o Chaos gera um evento de falha de validação. |
| --max-falhas-simultâneas | O número máximo de falhas simultâneas induzidas por iteração. O Chaos é executado em iterações e duas iterações consecutivas são separadas por uma fase de validação. Quanto maior a simultaneidade, mais intensa é a injeção de falhas – induzindo série mais complexas de estados para revelar bugs. A recomendação é iniciar com um valor de 2 ou 3 e ter cuidado ao mover para cima.  Padrão\: 1. |
| -max-percentual-aplicativos-prejudiciais | Ao avaliar a integridade de cluster durante caos, a porcentagem máxima permitida de aplicativos problemáticos antes de relatar um erro. <br><br> O percentual máximo permitido de aplicativos não íntegros antes de relatar um erro. Por exemplo, para permitir que 10% dos aplicativos sejam problemáticos, esse valor seria 10. A porcentagem representa o máximo tolerado de aplicativos que podem estar não íntegros antes de o cluster ser considerado com erro. Se a porcentagem for respeitada, mas houver pelo menos um aplicativo não íntegro, a integridade será avaliada como ‘Aviso’. Isso é calculado dividindo o número de aplicativos problemáticos pelo número total de instâncias do aplicativo no cluster, exceto aplicativos de tipos de aplicativos que estão incluídos no ApplicationTypeHealthPolicyMap. O cálculo é arredondado para cima para tolerar uma falha em um número pequeno de aplicativos. A porcentagem padrão é zero. |
| -max-percentual-nós-prejudiciais | Ao avaliar a integridade do cluster durante o Chaos, a porcentagem máxima permitida de nós prejudiciais antes de relatar um erro. <br><br> O percentual máximo permitido de nós não íntegros antes de relatar um erro. Por exemplo, para permitir que 10% dos nós sejam problemáticos, esse valor seria 10. A porcentagem representa o máximo tolerado de nós que podem estar não íntegros antes de o cluster ser considerado com erro. Se a porcentagem for respeitada, mas houver pelo menos um nó não íntegro, a integridade será avaliada como ‘Aviso’. Este percentual é calculado pela divisão do número de nós não íntegros pelo número total de nós no cluster. O cálculo é arredondado para cima para tolerar uma falha em um número pequeno de nós. A porcentagem padrão é zero. Em clusters grandes, alguns nós sempre estarão inoperantes ou ausentes para reparos, de modo que esse percentual deve ser configurado para tolerá-los. |
| -tempo de execução | Tempo total (em segundos) para o qual o Chaos será executado antes de parar automaticamente. O valor máximo permitido é 4.294.967.295 (System.UInt32.MaxValue).  Padrão\: 4294967295. |
| --timeout -t | Tempo limite do servidor em segundos.  Padrão\: 60. |
| --tempo de espera entre falhas | Tempo de espera (em segundos) entre falhas consecutivas dentro de uma única iteração.  Padrão\: 20. <br><br> Quanto maior o valor, menor a sobreposição entre falhas e mais simples a sequência de transições de estado pelas quais o cluster passa. A recomendação é iniciar com um valor entre 1 e 5 e ter cuidado ao mover para cima. |
| --tempo de espera entre iterações | Tempo-separação (em segundos) entre as duas iterações consecutivas de Chaos. Quanto maior o valor, menor é a taxa de injeção de falhas.  Padrão\: 30. |
| --aviso-como-erro | Indica se os avisos são ou não tratados com a mesma severidade que os erros. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, table, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-chaos-stop"></a>parar sfctl chaos
Interrompe o Chaos se ele estiver em execução no cluster e coloca a Agenda do Chaos em um estado parado.

Impede que o Chaos execute novas falhas. Falhas em curso continuarão em execução até a conclusão. A Agenda do Chaos atual é colocada no estado parado. Após a interrupção da agenda, ela permanecerá no estado parado e não será usada para Agendar novas execuções do Chaos. Uma nova Agenda de Chaos deve ser definida para retomar o agendamento.

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


## <a name="next-steps"></a>Próximas etapas
- [Configurar](service-fabric-cli.md) a CLI do Service Fabric.
- Saiba como usar a CLI do Service Fabric usando os [scripts de exemplo](/azure/service-fabric/scripts/sfctl-upgrade-application).