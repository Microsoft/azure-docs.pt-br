---
title: CLI do Azure Service Fabric – sfctl mesh gateway | Microsoft Docs
description: Descreve os comandos do sfctl mesh gateway da CLI do Service Fabric.
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
ms.openlocfilehash: 27cfe380e5988f17ca79c430cde7defc7f7bc440
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60839072"
---
# <a name="sfctl-mesh-gateway"></a>sfctl mesh gateway
Obter e excluir recursos de gateway da malha.

## <a name="commands"></a>Comandos

|Comando|DESCRIÇÃO|
| --- | --- |
| excluir | Exclui o recurso de gateway. |
| list | Lista todos os recursos de gateway. |
| mostrar | Obtém o recurso de gateway com o nome fornecido. |

## <a name="sfctl-mesh-gateway-delete"></a>sfctl mesh gateway delete
Exclui o recurso de gateway.

Exclui o recurso de gateway identificado pelo nome.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --name -n [Obrigatório] | O nome do recurso de gateway. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, table, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-mesh-gateway-list"></a>sfctl mesh gateway list
Lista todos os recursos de gateway.

Obtém as informações sobre todos os recursos de gateway em um determinado grupo de recursos. As informações incluem a descrição e outras propriedades do gateway.

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, table, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-mesh-gateway-show"></a>sfctl mesh gateway show
Obtém o recurso de gateway com o nome fornecido.

Obtém as informações sobre o recurso de gateway com o nome fornecido. As informações incluem a descrição e outras propriedades do gateway.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --name -n [Obrigatório] | O nome do recurso de gateway. |

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