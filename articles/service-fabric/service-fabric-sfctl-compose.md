---
title: CLI do Azure Service Fabric - composição sfctl | Microsoft Docs
description: Descreve os comandos de composição do sfctl da CLI do Service Fabric.
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
ms.openlocfilehash: 4b5cbb4a24b61de7e64a52ef950deedab3eec263
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60837259"
---
# <a name="sfctl-compose"></a>composição sfctl
Criar, excluir e gerenciar aplicativos do Docker Compose.

## <a name="commands"></a>Comandos

|Comando|DESCRIÇÃO|
| --- | --- |
| create | Cria uma implantação de composição do Service Fabric. |
| list | Obtém a lista de implantação de composição criada no cluster do Service Fabric. |
| remove | Exclui uma implantação de composição existente do Service Fabric do cluster. |
| status | Obtém informações sobre um aplicativo de implantação do Service Fabric. |
| atualizar | Começa a atualização de uma implantação de composição no cluster do Service Fabric. |
| upgrade-rollback | Começa a reversão de uma atualização de implantação de composição no cluster do Service Fabric. |
| upgrade-status | Obtém detalhes para a atualização mais recente realizada na implantação da composição do Service Fabric. |

## <a name="sfctl-compose-create"></a>criar composição sfctl
Cria uma implantação de composição do Service Fabric.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --nome de implantação [obrigatório] | O nome da implantação. |
| -caminho de arquivo [requerido] | Caminho para o arquivo de destino do Docker Compose. |
| -senha criptografada | Em vez de solicitar uma senha de registro do contêiner, use uma frase secreta já criptografada. |
| --has-pass | Solicitará uma senha para o registro de contêiner. |
| --timeout -t | Tempo limite do servidor em segundos.  Padrão\: 60. |
| --user | Nome de usuário para conectar o registro de contêiner. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, table, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-compose-list"></a>lista de composição sfctl
Obtém a lista de implantação de composição criada no cluster do Service Fabric.

Obtém o status sobre as implantações de redação que foram criadas ou no processo de criação do cluster do Service Fabric. A resposta inclui o nome, o status e outros detalhes sobre a implantação de composição. Se a lista de implantações não couber em uma página, uma página de resultados é retornada, bem como um token de continuação que pode ser usado para a próxima página.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --continuation-token | O parâmetro do token de continuação é usado para obter o próximo conjunto de resultados. Um token de continuação com um valor não vazio será incluso na resposta da API quando os resultados do sistema não couberem em uma única resposta. Quando esse valor for passado para a próxima chamada de API, a API retornará o próximo conjunto de resultados. Se não houver mais resultados, o token de continuação não conterá um valor. O valor desse parâmetro não deve ser codificado em URL. |
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

## <a name="sfctl-compose-remove"></a>remoção de composição sfctl
Exclui uma implantação de composição existente do Service Fabric do cluster.

Exclui uma implantação de composição existente do Service Fabric.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --nome de implantação [obrigatório] | A identidade da implantação. |
| --timeout -t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, table, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-compose-status"></a>status de composição sfctl
Obtém informações sobre um aplicativo de implantação do Service Fabric.

Retorna o status da implantação de composição que foi criada ou em processo de criação no cluster do Service Fabric e cujo nome corresponde ao especificado como o parâmetro. A resposta inclui o nome, status e outros detalhes sobre a implantação.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --nome de implantação [obrigatório] | A identidade da implantação. |
| --timeout -t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, table, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-compose-upgrade"></a>upgrade de composição sfctl
Começa a atualização de uma implantação de composição no cluster do Service Fabric.

Valida os parâmetros de atualização fornecidos e inicia a atualização da implantação, se os parâmetros são válidos.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --deployment-name [Obrigatório] | O nome da implantação. |
| --caminho do arquivo [obrigatório] | Caminho para o arquivo de destino do Docker Compose. |
| --falha-svc-tipo-integridade-mapa | Dicionário codificado JSON que descreve a política de integridade usada para avaliar a integridade dos serviços. |
| -senha criptografada | Em vez de solicitar uma senha de registro do contêiner, use uma frase secreta já criptografada. |
| --ação de falha | Os valores possíveis incluem\: 'Inválido', 'Reversão', 'Manual'. |
| -força-reinicialização | Os processos são reiniciados de modo forçado durante a atualização, mesmo quando a versão do código não foi alterada. <br><br> A atualização altera apenas a configuração ou os dados. |
| --has-pass | Solicitará uma senha para o registro de contêiner. |
| --nova tentativa de verificação da integridade | O período entre as tentativas de realizar verificações de integridade, se o aplicativo ou o cluster não estiver íntegro. |
| --estabilidade de verificação de integridade | A quantidade de tempo que o aplicativo ou o cluster deve permanecer íntegro antes que a atualização passe para o próximo domínio de atualização. <br><br> Primeiro, é interpretado como uma cadeia de caracteres representando uma duração ISO 8601. Se isso falhar, será interpretado como um número que representa o número total de milissegundos. |
| --espera de verificação de integridade | O período de espera após a conclusão de um domínio de atualização, antes de iniciar o processo de verificações de integridade. |
| --verificação do conjunto de réplicas | A quantidade máxima de tempo para bloqueio do processamento de um domínio de atualização e prevenção da perda de disponibilidade quando houver problemas inesperados. <br><br> Quando esse tempo limite expira, o processamento do domínio de atualização continua, independentemente de problemas de perda de disponibilidade. O tempo limite será redefinido no início de cada domínio de atualização. Os valores válidos estão entre 0 e 42949672925, inclusive. |
| --mapa de integridade do tipo de svc | Lista codificada JSON de objetos que descrevem as políticas de integridade usadas para avaliar a integridade de diferentes tipos de serviços. |
| --timeout -t | Tempo limite do servidor em segundos.  Padrão\: 60. |
| --aplicativo prejudicial | O percentual máximo permitido de aplicativos não íntegros antes de relatar um erro. <br><br> Por exemplo, para permitir que 10% dos aplicativos sejam problemáticos, esse valor seria 10. A porcentagem representa o máximo tolerado de aplicativos que podem estar não íntegros antes de o cluster ser considerado com erro. Se a porcentagem for respeitada, mas houver pelo menos um aplicativo não íntegro, a integridade será avaliada como ‘Aviso’. Isso é calculado dividindo o número de aplicativos problemáticos no número total de instâncias de aplicativos no cluster. |
| -tempo limite ao domínio de upgrade | A quantidade de tempo que cada domínio de atualização deve concluir antes de FailureAction ser executado. <br><br> Primeiro, é interpretado como uma cadeia de caracteres representando uma duração ISO 8601. Se isso falhar, será interpretado como um número que representa o número total de milissegundos. |
| --tipo de upgrade | Padrão\: sem interrupção. |
| --modo de upgrade | Os valores possíveis incluem\: 'Invalid', 'UnmonitoredAuto', 'UnmonitoredManual', 'Monitored'.  Padrão\: UnmonitoredAuto. |
| -tempo limite de upgrade | A quantidade de tempo que a atualização geral deve concluir antes de FailureAction ser executado. <br><br> Primeiro, é interpretado como uma cadeia de caracteres representando uma duração ISO 8601. Se isso falhar, será interpretado como um número que representa o número total de milissegundos. |
| --user | Nome de usuário para conectar o registro de contêiner. |
| --aviso-como-erro | Indica se os avisos são ou não tratados com a mesma severidade que os erros. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, table, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-compose-upgrade-rollback"></a>Reversão de atualização de composição de sfctl
Começa a reversão de uma atualização de implantação de composição no cluster do Service Fabric.

Reversão de uma atualização de implantação de composição do Service Fabric.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --nome de implantação [obrigatório] | A identidade da implantação. |
| --timeout -t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, table, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-compose-upgrade-status"></a>sfctl compor o status de atualização
Obtém detalhes para a atualização mais recente realizada na implantação da composição do Service Fabric.

Retorna as informações sobre o estado da atualização de implantação de redação juntamente com detalhes para auxiliar na depuração problemas de integridade do aplicativo.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --nome de implantação [obrigatório] | A identidade da implantação. |
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