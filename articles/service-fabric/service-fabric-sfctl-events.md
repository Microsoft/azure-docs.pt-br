---
title: CLI do Azure Service Fabric-eventos sfctl
description: Descreve os comandos Service Fabric CLI sfctl Events.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 635bddef1a98d6ed2b112662d8c9c44fe65e9e32
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "76906096"
---
# <a name="sfctl-events"></a>eventos de sfctl
Recupere eventos do repositório de eventos (se o serviço EventStore já estiver instalado).

O serviço do sistema EventStore pode ser adicionado por meio de uma atualização de configuração para qualquer cluster SFRP em execução >= 6,4. Verifique a URL \: https \: //docs.Microsoft.com/Azure/Service-Fabric/Service-Fabric-Diagnostics-eventstore a seguir.

## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
| todos os aplicativos – lista | Obtém todos os eventos relacionados a aplicativos. |
| todos os nós-lista | Obtém todos os eventos relacionados a nós. |
| todas as partições-lista | Obtém todos os eventos relacionados a partições. |
| todos os serviços-lista | Obtém todos os eventos relacionados a serviços. |
| lista de aplicativos | Obtém eventos relacionados ao aplicativo. |
| lista de clusters | Obtém todos os eventos relacionados ao cluster. |
| lista de nós | Obtém eventos relacionados a nós. |
| partição-todas as réplicas-lista | Obtém todos os eventos relacionados a réplicas para uma partição. |
| lista de partições | Obtém eventos relacionados à partição. |
| partição-réplica-List | Obtém eventos relacionados à réplica de partição. |
| lista de serviços | Obtém eventos relacionados ao serviço. |

## <a name="sfctl-events-all-applications-list"></a>sfctl eventos All-Applications-List
Obtém todos os eventos relacionados a aplicativos.

A resposta é uma lista de objetos ApplicationEvent.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --hora de término – UTC [obrigatório] | A hora de término de uma consulta de pesquisa no ISO UTC AAAA-MM-ddTHH \: mm \: ssZ. |
| --hora de início UTC [obrigatório] | A hora de início de uma consulta de pesquisa no ISO UTC AAAA-MM-ddTHH \: mm \: ssZ. |
| --eventos-tipos-filtro | Esta é uma cadeia de caracteres separada por vírgula que especifica os tipos de FabricEvents que só devem ser incluídos na resposta. |
| --Exclude-Analysis-Events | Esse parâmetro desabilita a recuperação de AnalysisEvents se true for passado. |
| --Skip-correlationion-Lookup | Esse parâmetro desabilita a pesquisa de informações de CorrelatedEvents se true for passado. caso contrário, o campo CorrelationEvents Get processado e HasCorrelatedEvents em cada FabricEvent será populado. |
| --timeout -t | O tempo limite do servidor para executar a operação em segundos. Esse tempo limite especifica a duração de tempo que o cliente está disposto a aguardar a conclusão da operação solicitada. O valor padrão para esse parâmetro é 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --debug | Aumente o detalhamento do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o detalhamento do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-events-all-nodes-list"></a>sfctl eventos todos-nós-lista
Obtém todos os eventos relacionados a nós.

A resposta é uma lista de objetos NodeEvent.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --hora de término – UTC [obrigatório] | A hora de término de uma consulta de pesquisa no ISO UTC AAAA-MM-ddTHH \: mm \: ssZ. |
| --hora de início UTC [obrigatório] | A hora de início de uma consulta de pesquisa no ISO UTC AAAA-MM-ddTHH \: mm \: ssZ. |
| --eventos-tipos-filtro | Esta é uma cadeia de caracteres separada por vírgula que especifica os tipos de FabricEvents que só devem ser incluídos na resposta. |
| --Exclude-Analysis-Events | Esse parâmetro desabilita a recuperação de AnalysisEvents se true for passado. |
| --Skip-correlationion-Lookup | Esse parâmetro desabilita a pesquisa de informações de CorrelatedEvents se true for passado. caso contrário, o campo CorrelationEvents Get processado e HasCorrelatedEvents em cada FabricEvent será populado. |
| --timeout -t | O tempo limite do servidor para executar a operação em segundos. Esse tempo limite especifica a duração de tempo que o cliente está disposto a aguardar a conclusão da operação solicitada. O valor padrão para esse parâmetro é 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --debug | Aumente o detalhamento do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o detalhamento do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-events-all-partitions-list"></a>sfctl eventos All-Partitions-List
Obtém todos os eventos relacionados a partições.

A resposta é uma lista de objetos PartitionEvent.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --hora de término – UTC [obrigatório] | A hora de término de uma consulta de pesquisa no ISO UTC AAAA-MM-ddTHH \: mm \: ssZ. |
| --hora de início UTC [obrigatório] | A hora de início de uma consulta de pesquisa no ISO UTC AAAA-MM-ddTHH \: mm \: ssZ. |
| --eventos-tipos-filtro | Esta é uma cadeia de caracteres separada por vírgula que especifica os tipos de FabricEvents que só devem ser incluídos na resposta. |
| --Exclude-Analysis-Events | Esse parâmetro desabilita a recuperação de AnalysisEvents se true for passado. |
| --Skip-correlationion-Lookup | Esse parâmetro desabilita a pesquisa de informações de CorrelatedEvents se true for passado. caso contrário, o campo CorrelationEvents Get processado e HasCorrelatedEvents em cada FabricEvent será populado. |
| --timeout -t | O tempo limite do servidor para executar a operação em segundos. Esse tempo limite especifica a duração de tempo que o cliente está disposto a aguardar a conclusão da operação solicitada. O valor padrão para esse parâmetro é 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --debug | Aumente o detalhamento do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o detalhamento do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-events-all-services-list"></a>sfctl eventos All-lista de serviços
Obtém todos os eventos relacionados a serviços.

A resposta é uma lista de objetos irregulares.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --hora de término – UTC [obrigatório] | A hora de término de uma consulta de pesquisa no ISO UTC AAAA-MM-ddTHH \: mm \: ssZ. |
| --hora de início UTC [obrigatório] | A hora de início de uma consulta de pesquisa no ISO UTC AAAA-MM-ddTHH \: mm \: ssZ. |
| --eventos-tipos-filtro | Esta é uma cadeia de caracteres separada por vírgula que especifica os tipos de FabricEvents que só devem ser incluídos na resposta. |
| --Exclude-Analysis-Events | Esse parâmetro desabilita a recuperação de AnalysisEvents se true for passado. |
| --Skip-correlationion-Lookup | Esse parâmetro desabilita a pesquisa de informações de CorrelatedEvents se true for passado. caso contrário, o campo CorrelationEvents Get processado e HasCorrelatedEvents em cada FabricEvent será populado. |
| --timeout -t | O tempo limite do servidor para executar a operação em segundos. Esse tempo limite especifica a duração de tempo que o cliente está disposto a aguardar a conclusão da operação solicitada. O valor padrão para esse parâmetro é 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --debug | Aumente o detalhamento do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o detalhamento do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-events-application-list"></a>aplicativo de eventos sfctl – lista
Obtém eventos relacionados ao aplicativo.

A resposta é uma lista de objetos ApplicationEvent.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --application-id [Obrigatório] | A identidade do aplicativo. Normalmente, este é o nome completo do aplicativo sem o esquema de URI "fabric\:". A partir da versão 6.0, nomes hierárquicos são delimitados pelo caractere "\~". Por exemplo, se o nome do aplicativo for "fabric\:/meuaplicativo/aplicativo1", a identidade do aplicativo será "meuaplicativo\~aplicativo1" na versão 6.0 e superiores, e "meuaplicativo/aplicativo1" nas versões anteriores. |
| --hora de término – UTC [obrigatório] | A hora de término de uma consulta de pesquisa no ISO UTC AAAA-MM-ddTHH \: mm \: ssZ. |
| --hora de início UTC [obrigatório] | A hora de início de uma consulta de pesquisa no ISO UTC AAAA-MM-ddTHH \: mm \: ssZ. |
| --eventos-tipos-filtro | Esta é uma cadeia de caracteres separada por vírgula que especifica os tipos de FabricEvents que só devem ser incluídos na resposta. |
| --Exclude-Analysis-Events | Esse parâmetro desabilita a recuperação de AnalysisEvents se true for passado. |
| --Skip-correlationion-Lookup | Esse parâmetro desabilita a pesquisa de informações de CorrelatedEvents se true for passado. caso contrário, o campo CorrelationEvents Get processado e HasCorrelatedEvents em cada FabricEvent será populado. |
| --timeout -t | O tempo limite do servidor para executar a operação em segundos. Esse tempo limite especifica a duração de tempo que o cliente está disposto a aguardar a conclusão da operação solicitada. O valor padrão para esse parâmetro é 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --debug | Aumente o detalhamento do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o detalhamento do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-events-cluster-list"></a>lista de clusters de eventos sfctl
Obtém todos os eventos relacionados ao cluster.

A resposta é uma lista de objetos ClusterEvent.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --hora de término – UTC [obrigatório] | A hora de término de uma consulta de pesquisa no ISO UTC AAAA-MM-ddTHH \: mm \: ssZ. |
| --hora de início UTC [obrigatório] | A hora de início de uma consulta de pesquisa no ISO UTC AAAA-MM-ddTHH \: mm \: ssZ. |
| --eventos-tipos-filtro | Esta é uma cadeia de caracteres separada por vírgula que especifica os tipos de FabricEvents que só devem ser incluídos na resposta. |
| --Exclude-Analysis-Events | Esse parâmetro desabilita a recuperação de AnalysisEvents se true for passado. |
| --Skip-correlationion-Lookup | Esse parâmetro desabilita a pesquisa de informações de CorrelatedEvents se true for passado. caso contrário, o campo CorrelationEvents Get processado e HasCorrelatedEvents em cada FabricEvent será populado. |
| --timeout -t | O tempo limite do servidor para executar a operação em segundos. Esse tempo limite especifica a duração de tempo que o cliente está disposto a aguardar a conclusão da operação solicitada. O valor padrão para esse parâmetro é 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --debug | Aumente o detalhamento do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o detalhamento do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-events-node-list"></a>lista de nós de eventos do sfctl
Obtém eventos relacionados a nós.

A resposta é uma lista de objetos NodeEvent.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --hora de término – UTC [obrigatório] | A hora de término de uma consulta de pesquisa no ISO UTC AAAA-MM-ddTHH \: mm \: ssZ. |
| --node-name      [Obrigatório] | O nome do nó. |
| --hora de início UTC [obrigatório] | A hora de início de uma consulta de pesquisa no ISO UTC AAAA-MM-ddTHH \: mm \: ssZ. |
| --eventos-tipos-filtro | Esta é uma cadeia de caracteres separada por vírgula que especifica os tipos de FabricEvents que só devem ser incluídos na resposta. |
| --Exclude-Analysis-Events | Esse parâmetro desabilita a recuperação de AnalysisEvents se true for passado. |
| --Skip-correlationion-Lookup | Esse parâmetro desabilita a pesquisa de informações de CorrelatedEvents se true for passado. caso contrário, o campo CorrelationEvents Get processado e HasCorrelatedEvents em cada FabricEvent será populado. |
| --timeout -t | O tempo limite do servidor para executar a operação em segundos. Esse tempo limite especifica a duração de tempo que o cliente está disposto a aguardar a conclusão da operação solicitada. O valor padrão para esse parâmetro é 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --debug | Aumente o detalhamento do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o detalhamento do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-events-partition-all-replicas-list"></a>sfctl de eventos partição-todas as réplicas-lista
Obtém todos os eventos relacionados a réplicas para uma partição.

A resposta é uma lista de objetos ReplicaEvent.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --hora de término – UTC [obrigatório] | A hora de término de uma consulta de pesquisa no ISO UTC AAAA-MM-ddTHH \: mm \: ssZ. |
| --partition-id   [Obrigatório] | A identidade da partição. |
| --hora de início UTC [obrigatório] | A hora de início de uma consulta de pesquisa no ISO UTC AAAA-MM-ddTHH \: mm \: ssZ. |
| --eventos-tipos-filtro | Esta é uma cadeia de caracteres separada por vírgula que especifica os tipos de FabricEvents que só devem ser incluídos na resposta. |
| --Exclude-Analysis-Events | Esse parâmetro desabilita a recuperação de AnalysisEvents se true for passado. |
| --Skip-correlationion-Lookup | Esse parâmetro desabilita a pesquisa de informações de CorrelatedEvents se true for passado. caso contrário, o campo CorrelationEvents Get processado e HasCorrelatedEvents em cada FabricEvent será populado. |
| --timeout -t | O tempo limite do servidor para executar a operação em segundos. Esse tempo limite especifica a duração de tempo que o cliente está disposto a aguardar a conclusão da operação solicitada. O valor padrão para esse parâmetro é 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --debug | Aumente o detalhamento do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o detalhamento do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-events-partition-list"></a>partição de eventos sfctl – lista
Obtém eventos relacionados à partição.

A resposta é uma lista de objetos PartitionEvent.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --hora de término – UTC [obrigatório] | A hora de término de uma consulta de pesquisa no ISO UTC AAAA-MM-ddTHH \: mm \: ssZ. |
| --partition-id   [Obrigatório] | A identidade da partição. |
| --hora de início UTC [obrigatório] | A hora de início de uma consulta de pesquisa no ISO UTC AAAA-MM-ddTHH \: mm \: ssZ. |
| --eventos-tipos-filtro | Esta é uma cadeia de caracteres separada por vírgula que especifica os tipos de FabricEvents que só devem ser incluídos na resposta. |
| --Exclude-Analysis-Events | Esse parâmetro desabilita a recuperação de AnalysisEvents se true for passado. |
| --Skip-correlationion-Lookup | Esse parâmetro desabilita a pesquisa de informações de CorrelatedEvents se true for passado. caso contrário, o campo CorrelationEvents Get processado e HasCorrelatedEvents em cada FabricEvent será populado. |
| --timeout -t | O tempo limite do servidor para executar a operação em segundos. Esse tempo limite especifica a duração de tempo que o cliente está disposto a aguardar a conclusão da operação solicitada. O valor padrão para esse parâmetro é 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --debug | Aumente o detalhamento do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o detalhamento do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-events-partition-replica-list"></a>sfctl de eventos de partição – lista de réplicas
Obtém eventos relacionados à réplica de partição.

A resposta é uma lista de objetos ReplicaEvent.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --hora de término – UTC [obrigatório] | A hora de término de uma consulta de pesquisa no ISO UTC AAAA-MM-ddTHH \: mm \: ssZ. |
| --partition-id   [Obrigatório] | A identidade da partição. |
| --ID da réplica [obrigatório] | O identificador da réplica. |
| --hora de início UTC [obrigatório] | A hora de início de uma consulta de pesquisa no ISO UTC AAAA-MM-ddTHH \: mm \: ssZ. |
| --eventos-tipos-filtro | Esta é uma cadeia de caracteres separada por vírgula que especifica os tipos de FabricEvents que só devem ser incluídos na resposta. |
| --Exclude-Analysis-Events | Esse parâmetro desabilita a recuperação de AnalysisEvents se true for passado. |
| --Skip-correlationion-Lookup | Esse parâmetro desabilita a pesquisa de informações de CorrelatedEvents se true for passado. caso contrário, o campo CorrelationEvents Get processado e HasCorrelatedEvents em cada FabricEvent será populado. |
| --timeout -t | O tempo limite do servidor para executar a operação em segundos. Esse tempo limite especifica a duração de tempo que o cliente está disposto a aguardar a conclusão da operação solicitada. O valor padrão para esse parâmetro é 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --debug | Aumente o detalhamento do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o detalhamento do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-events-service-list"></a>serviço de eventos sfctl – lista
Obtém eventos relacionados ao serviço.

A resposta é uma lista de objetos irregulares.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --hora de término – UTC [obrigatório] | A hora de término de uma consulta de pesquisa no ISO UTC AAAA-MM-ddTHH \: mm \: ssZ. |
| --service-id          [Obrigatório] | A identidade do serviço. Essa ID normalmente é o nome completo do serviço sem o ' malha\:' esquema de URI. A partir da versão 6.0, nomes hierárquicos são delimitados pelo caractere "\~". Por exemplo, se o nome do serviço for "fabric\:/myapp/app1/svc1", a identidade do serviço será "myapp\~app1\~svc1" na versão 6.0 e superiores, e "myapp/app1/svc1" em versões anteriores. |
| --hora de início UTC [obrigatório] | A hora de início de uma consulta de pesquisa no ISO UTC AAAA-MM-ddTHH \: mm \: ssZ. |
| --eventos-tipos-filtro | Esta é uma cadeia de caracteres separada por vírgula que especifica os tipos de FabricEvents que só devem ser incluídos na resposta. |
| --Exclude-Analysis-Events | Esse parâmetro desabilita a recuperação de AnalysisEvents se true for passado. |
| --Skip-correlationion-Lookup | Esse parâmetro desabilita a pesquisa de informações de CorrelatedEvents se true for passado. caso contrário, o campo CorrelationEvents Get processado e HasCorrelatedEvents em cada FabricEvent será populado. |
| --timeout -t | O tempo limite do servidor para executar a operação em segundos. Esse tempo limite especifica a duração de tempo que o cliente está disposto a aguardar a conclusão da operação solicitada. O valor padrão para esse parâmetro é 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --debug | Aumente o detalhamento do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o detalhamento do log. Use --debug para logs de depuração completos. |

