---
title: CLI do Azure Service Fabric – valor secreto da malha do sfctl | Microsoft Docs
description: Descreve os comandos de valor secreto da malha do sfctl da CLI do Service Fabric.
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
ms.openlocfilehash: 3f8e46f063d3e725e2174fd907169f3e0167586a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60836936"
---
# <a name="sfctl-mesh-secretvalue"></a>sfctl mesh secretvalue
Obtém e exclui recursos de valor secreto da malha.

## <a name="commands"></a>Comandos

|Comando|DESCRIÇÃO|
| --- | --- |
| excluir | Exclui o valor especificado do recurso de segredo nomeado. |
| list | Lista os nomes de todos os valores do recurso de segredo especificado. |
| mostrar | Recupera o valor de uma versão especificada de um recurso de segredo. |

## <a name="sfctl-mesh-secretvalue-delete"></a>sfctl mesh secretvalue delete
Exclui o valor especificado do recurso de segredo nomeado.

Exclui o recurso de valor secreto identificado pelo nome. O nome do recurso normalmente é a versão associada a esse valor. A exclusão falhará se o valor especificado estiver em uso.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --secret-name -n [Obrigatório] | O nome do recurso de segredo. |
| --version -v     [Obrigatório] | O nome da versão do segredo. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, table, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-mesh-secretvalue-list"></a>sfctl mesh secretvalue list
Lista os nomes de todos os valores do recurso de segredo especificado.

Obtém informações sobre todos os recursos de valor secreto do recurso de segredo especificado. As informações incluem os nomes dos recursos de valor secreto, mas não os valores reais.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --secret-name -n [Obrigatório] | O nome do recurso de segredo. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, table, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-mesh-secretvalue-show"></a>sfctl mesh secretvalue show
Recupera o valor de uma versão especificada de um recurso de segredo.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --secret-name -n [Obrigatório] | O nome do recurso de segredo. |
| --version -v     [Obrigatório] | O nome da versão do segredo. |
| --show-value | Mostra o valor real da versão do segredo. |

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