---
title: CLI do Azure Service Fabric-segredo de malha sfctl
description: Saiba mais sobre o sfctl, a interface de linha de comando Service Fabric do Azure. Inclui uma lista de comandos para obter e excluir Service Fabric recursos secretos de malha.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 2c91a59e57613934c946e3cd56089b330893a51a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86260337"
---
# <a name="sfctl-mesh-secret"></a>sfctl mesh secret
Obter e excluir recursos de segredo da malha.

## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
| excluir | Exclui o recurso de segredo. |
| list | Lista todos os recursos de segredo. |
| show | Obtém o recurso de segredo com o nome fornecido. |

## <a name="sfctl-mesh-secret-delete"></a>sfctl mesh secret delete
Exclui o recurso de segredo.

Exclui o recurso de segredo especificado e todos os seus valores nomeados.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --name -n [Obrigatório] | O nome do recurso de segredo. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --debug | Aumente o detalhamento do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o detalhamento do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-mesh-secret-list"></a>sfctl mesh secret list
Lista todos os recursos de segredo.

Obtém as informações sobre todos os recursos de segredo em um determinado grupo de recursos. As informações incluem a descrição e outras propriedades do segredo.

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --debug | Aumente o detalhamento do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o detalhamento do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-mesh-secret-show"></a>sfctl mesh secret show
Obtém o recurso de segredo com o nome fornecido.

Obtém as informações sobre o recurso de segredo com o nome fornecido. As informações incluem a descrição e outras propriedades do segredo.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --name -n [Obrigatório] | O nome do recurso de segredo. |

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
