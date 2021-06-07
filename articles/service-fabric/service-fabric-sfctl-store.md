---
title: CLI do Azure Service Fabric-sfctl Store
description: Saiba mais sobre o sfctl, a interface de linha de comando Service Fabric do Azure. Inclui uma lista de comandos para executar operações de nível de arquivo no repositório de imagens de cluster.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: af5399be44d2946775622598c5c1db3d8355f7cd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "86257063"
---
# <a name="sfctl-store"></a>repositório sfctl
Execute operações de arquivo de nível básico no repositório de imagens do cluster.

## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
| excluir | Exclui o conteúdo do repositório de imagens existente. |
| root-info | Obtém as informações de conteúdo na raiz do repositório de imagens. |
| stat | Obtém as informações de conteúdo do repositório de imagens. |

## <a name="sfctl-store-delete"></a>sfctl store delete
Exclui o conteúdo do repositório de imagens existente.

Exclui o conteúdo do repositório de imagens existente encontrado em determinado caminho relativo do repositório de imagens. Este comando pode ser usado para excluir pacotes de aplicativos carregados quando eles são provisionados.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --content-path [Obrigatório] | Caminho relativo para o arquivo ou pasta no repositório de imagens de sua raiz. |
| --timeout -t | O tempo limite do servidor para executar a operação em segundos. Esse tempo limite especifica a duração de tempo que o cliente está disposto a aguardar a conclusão da operação solicitada. O valor padrão para esse parâmetro é 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --debug | Aumente o detalhamento do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o detalhamento do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-store-root-info"></a>informações sobre o sfctl repositório raiz
Obtém as informações de conteúdo na raiz do repositório de imagens.

Retorna as informações sobre o repositório de imagens conteúdo na raiz do repositório de imagem.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --timeout -t | O tempo limite do servidor para executar a operação em segundos. Esse tempo limite especifica a duração de tempo que o cliente está disposto a aguardar a conclusão da operação solicitada. O valor padrão para esse parâmetro é 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --debug | Aumente o detalhamento do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o detalhamento do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-store-stat"></a>sfctl store stat
Obtém as informações de conteúdo do repositório de imagens.

Retorna as informações sobre o conteúdo do repositório de imagem no contentPath especificado. O contentPath é relativo à raiz do repositório de imagem.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --content-path [Obrigatório] | Caminho relativo para o arquivo ou pasta no repositório de imagens de sua raiz. |
| --timeout -t | O tempo limite do servidor para executar a operação em segundos. Esse tempo limite especifica a duração de tempo que o cliente está disposto a aguardar a conclusão da operação solicitada. O valor padrão para esse parâmetro é 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --debug | Aumente o detalhamento do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o detalhamento do log. Use --debug para logs de depuração completos. |


## <a name="next-steps"></a>Próximas etapas
- [Configurar](service-fabric-cli.md) a CLI do Service Fabric.
- Saiba como usar a CLI do Service Fabric usando os [scripts de exemplo](./scripts/sfctl-upgrade-application.md).
