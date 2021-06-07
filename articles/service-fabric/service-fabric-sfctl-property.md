---
title: CLI do Azure Service Fabric-Propriedade sfctl
description: Saiba mais sobre o sfctl, a interface de linha de comando Service Fabric do Azure. Inclui uma lista de comandos para armazenar e consultar Propriedades.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 0a5ebd4822c5f0ff1735464bb4d5b42c436ee529
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "86260322"
---
# <a name="sfctl-property"></a>sfctl property
Propriedades de armazenamento e a consulta em nomes do Service Fabric.

## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
| excluir | Exclui a propriedade especificada do Service Fabric. |
| get | Obtém a propriedade especificada do Service Fabric. |
| list | Obtém informações sobre todas as propriedades de malha do serviço em um determinado nome. |
| put | Cria ou atualiza uma propriedade de malha do serviço. |

## <a name="sfctl-property-delete"></a>exclusão de propriedade sfctl
Exclui a propriedade especificada do Service Fabric.

Exclui a propriedade do Service Fabric especificada em um determinado nome. Uma propriedade deve ser criada antes que possa ser excluído.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| – id de nome [requerido] | Nome da malha do serviço, sem o ' malha\:' esquema de URI. |
| -nome de propriedade [requerido] | Especifica o nome da propriedade a ser obtida. |
| --timeout -t | O tempo limite do servidor para executar a operação em segundos. Esse tempo limite especifica a duração de tempo que o cliente está disposto a aguardar a conclusão da operação solicitada. O valor padrão para esse parâmetro é 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --debug | Aumente o detalhamento do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o detalhamento do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-property-get"></a>get de propriedade sfctl
Obtém a propriedade especificada do Service Fabric.

Obtém a propriedade do Service Fabric especificada em um determinado nome. Isso sempre retornará valor e metadados.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| – id de nome [requerido] | Nome da malha do serviço, sem o ' malha\:' esquema de URI. |
| -nome de propriedade [requerido] | Especifica o nome da propriedade a ser obtida. |
| --timeout -t | O tempo limite do servidor para executar a operação em segundos. Esse tempo limite especifica a duração de tempo que o cliente está disposto a aguardar a conclusão da operação solicitada. O valor padrão para esse parâmetro é 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --debug | Aumente o detalhamento do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o detalhamento do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-property-list"></a>lista de propriedades sfctl
Obtém informações sobre todas as propriedades de malha do serviço em um determinado nome.

Um nome de malha do serviço pode ter um ou mais propriedades nomeadas que armazenam informações personalizadas. Esta operação obtém as informações sobre essas propriedades em uma lista de página. As informações incluem o nome, valor e metadados sobre cada uma das propriedades.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| – id de nome [requerido] | Nome da malha do serviço, sem o ' malha\:' esquema de URI. |
| --continuation-token | O parâmetro do token de continuação é usado para obter o próximo conjunto de resultados. Um token de continuação com um valor não vazio será incluso na resposta da API quando os resultados do sistema não couberem em uma única resposta. Quando esse valor for passado para a próxima chamada de API, a API retornará o próximo conjunto de resultados. Se não houver mais resultados, o token de continuação não conterá um valor. O valor desse parâmetro não deve ser codificado em URL. |
| – incluir valores | Permite especificar se deseja incluir os valores das propriedades retornadas. Verdadeiro se os valores devem ser retornados com os metadados; Falso para retornar somente metadados de propriedade. |
| --timeout -t | O tempo limite do servidor para executar a operação em segundos. Esse tempo limite especifica a duração de tempo que o cliente está disposto a aguardar a conclusão da operação solicitada. O valor padrão para esse parâmetro é 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --debug | Aumente o detalhamento do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o detalhamento do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-property-put"></a>sfctl propriedade put
Cria ou atualiza uma propriedade de malha do serviço.

Cria ou atualiza a propriedade de malha do serviço especificada em um determinado nome.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| – id de nome [requerido] | Nome da malha do serviço, sem o ' malha\:' esquema de URI. |
| -nome de propriedade [requerido] | O nome da propriedade do Service Fabric. |
| – valor [requerido] | Descreve um valor de propriedade do Service Fabric. Isso é uma cadeia de caracteres JSON. <br><br> A cadeia de caracteres JSON tem dois campos, o ' tipo ' dos dados e o valor, inseridos como ' dados ' dos dados. O valor de 'Kind' deve ser o primeiro item deve aparecer na cadeia de caracteres JSON e pode ser valores 'Binário', 'Int64', 'Double', 'String' ou 'Guid'. O valor deve ser serializar-capaz de tipos de dados. Valores 'Tipo' e 'Data' devem ser fornecidos como cadeias de caracteres. |
| --custom-id-type | Um tipo personalizado da propriedade ID. Usando essa propriedade, o usuário é capaz de marcar o tipo do valor da propriedade. |
| --timeout -t | Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --debug | Aumente o detalhamento do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o detalhamento do log. Use --debug para logs de depuração completos. |


## <a name="next-steps"></a>Próximas etapas
- [Configure](service-fabric-cli.md) a CLI do Service Fabric.
- Saiba como usar a CLI do Service Fabric usando os [scripts de exemplo](./scripts/sfctl-upgrade-application.md).
