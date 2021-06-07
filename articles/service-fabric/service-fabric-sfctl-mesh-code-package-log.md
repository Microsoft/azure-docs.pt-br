---
title: CLI do Azure Service Fabric-pacote de código de malha sfctl-log
description: Saiba mais sobre o sfctl, a interface de linha de comando Service Fabric do Azure. Inclui uma lista de comandos para obter logs para um pacote de códigos especificado.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 9ac1d85a1a498f9f6fcd0a03f8f819d1cdfcac33
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "86257305"
---
# <a name="sfctl-mesh-code-package-log"></a>sfctl mesh code-package-log
Obter os logs para o contêiner do pacote de código especificado para a réplica de um determinado serviço.

## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
| get | Obtém os logs do contêiner. |

## <a name="sfctl-mesh-code-package-log-get"></a>sfctl mesh code-package-log get
Obtém os logs do contêiner.

Obtém os logs para o contêiner do pacote de código especificado para a réplica do serviço.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --app-name --application-name [Obrigatório] | O nome do aplicativo. |
| --code-package-name           [Obrigatório] | O nome do pacote de código do serviço. |
| --replica-name                [Obrigatório] | Nome da réplica do Service Fabric. |
| --service-name                [Obrigatório] | O nome do serviço. |
| -final | Número de linhas a serem mostradas do final dos logs. O padrão é 100. 'todos' para mostrar os logs de conclusão. |

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
