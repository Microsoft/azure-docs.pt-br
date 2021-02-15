---
title: CLI do Azure Service Fabric-serviço de malha sfctl-réplica
description: Saiba mais sobre o sfctl, a interface de linha de comando Service Fabric do Azure. Inclui uma lista de comandos para obter detalhes da réplica para os recursos do aplicativo.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: cbfdba30663e2aa531ab1db955b0e035a0588709
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86245714"
---
# <a name="sfctl-mesh-service-replica"></a>sfctl mesh service-replica
Obtenha detalhes da réplica e listar as réplicas de um determinado serviço em um recurso de aplicativo.

## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
| list | Lista todas as réplicas de um serviço. |
| show | Obtém a réplica fornecida do serviço de um aplicativo. |

## <a name="sfctl-mesh-service-replica-list"></a>sfctl mesh service-replica list
Lista todas as réplicas de um serviço.

Obtém as informações sobre todas as replicas de um serviço. As informações incluem a descrição e outras propriedades da réplica do serviço.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --app-name --application-name [Obrigatório] | O nome do aplicativo. |
| --service-name                [Obrigatório] | O nome do serviço. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --debug | Aumente o detalhamento do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o detalhamento do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-mesh-service-replica-show"></a>sfctl mesh service-replica show
Obtém a réplica fornecida do serviço de um aplicativo.

Obtém as informações sobre a réplica do serviço com o nome fornecido. As informações incluem a descrição e outras propriedades da réplica do serviço.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --app-name --application-name [Obrigatório] | O nome do aplicativo. |
| --name -n                     [Obrigatório] | O nome da réplica do serviço. |
| --service-name                [Obrigatório] | O nome do serviço. |

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
