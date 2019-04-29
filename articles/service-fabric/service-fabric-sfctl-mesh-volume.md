---
title: CLI do Azure Service Fabric – sfctl mesh volume | Microsoft Docs
description: Descreve os comandos do volume da malha do sfctl da CLI do Service Fabric.
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
ms.openlocfilehash: 740aec6ccb9b20cbcb8f55b2518c2b2539ef82ac
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60836902"
---
# <a name="sfctl-mesh-volume"></a>sfctl mesh volume
Obter e excluir recursos do volume.

## <a name="commands"></a>Comandos

|Comando|DESCRIÇÃO|
| --- | --- |
| excluir | Exclui o recurso do volume. |
| list | Lista todos os recursos do volume. |
| mostrar | Obtém o recurso de volume com o nome fornecido. |

## <a name="sfctl-mesh-volume-delete"></a>sfctl mesh volume delete
Exclui o recurso do volume.

Exclui o recurso de volume identificado pelo nome.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --name -n [Obrigatório] | O nome do volume. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, table, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-mesh-volume-list"></a>sfctl mesh volume list
Lista todos os recursos do volume.

Obtém as informações sobre todos os recursos de volume em um determinado grupo de recursos. As informações incluem a descrição e outras propriedades do volume.

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, table, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-mesh-volume-show"></a>sfctl mesh volume show
Obtém o recurso de volume com o nome fornecido.

Obtém as informações sobre o recurso de volume com o nome fornecido. As informações incluem a descrição e outras propriedades do volume.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --name -n [Obrigatório] | O nome do volume. |

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