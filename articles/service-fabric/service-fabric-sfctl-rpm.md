---
title: CLI do Azure Service Fabric - sfctl rpm | Microsoft Docs
description: Descreve os comandos do sfctl rpm da CLI do Service Fabric.
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
ms.openlocfilehash: 04080d75042bfa8a07533336936165e0abef051b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60556348"
---
# <a name="sfctl-rpm"></a>sfctl rpm
Consultar e enviar comandos para o serviço de gerenciador de reparo.

## <a name="commands"></a>Comandos

|Comando|DESCRIÇÃO|
| --- | --- |
| Aprovar-força | Força a aprovação de determinada tarefa de reparo. |
| excluir | Exclui uma tarefa de reparo concluída. |
| list | Obtém uma lista de tarefas de reparo, correspondendo os filtros fornecidos. |

## <a name="sfctl-rpm-approve-force"></a>sfctl rpm aprovar força
Força a aprovação de determinada tarefa de reparo.

Essa API dá suporte à plataforma do Service Fabric; não é destinada a ser usada diretamente a partir do seu código.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --id da tarefa [obrigatório] | A ID da tarefa de reparo. |
| --versão | O número da versão atual da tarefa de reparo. Se diferente de zero, a solicitação terá êxito apenas se esse valor corresponde à versão de atual real da tarefa de reparo. Se for zero, nenhuma verificação de versão será executada. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, table, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-rpm-delete"></a>excluir sfctl rpm
Exclui uma tarefa de reparo concluída.

Essa API dá suporte à plataforma do Service Fabric; não é destinada a ser usada diretamente a partir do seu código.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --id da tarefa [obrigatório] | A ID da tarefa de reparo concluída a ser excluída. |
| --versão | O número da versão atual da tarefa de reparo. Se diferente de zero, a solicitação terá êxito apenas se esse valor corresponde à versão de atual real da tarefa de reparo. Se for zero, nenhuma verificação de versão será executada. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, table, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-rpm-list"></a>lista sfctl rpm
Obtém uma lista de tarefas de reparo, correspondendo os filtros fornecidos.

Essa API dá suporte à plataforma do Service Fabric; não é destinada a ser usada diretamente a partir do seu código.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --filtro do executor | O nome do executor de reparos cujas tarefas declaradas devem ser incluídas na lista. |
| -filtro de estado | Um bitwise OR dos valores a seguir, especificando qual estado de tarefa deve ser incluído na lista de resultados. <br> 1 - criada <br>2 - declarado  <br>4 - preparação  <br>8 - aprovado  <br>16 - em execução  <br>32 - restaurar  <br>64 - concluída |
| -filtro de id de tarefa | O prefixo de ID da tarefa de reparo a ser correspondido. |

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