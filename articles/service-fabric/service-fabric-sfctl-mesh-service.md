---
title: CLI do Azure Service Fabric – sfctl mesh service | Microsoft Docs
description: Descreve os comandos sfctl mesh service da CLI do Service Fabric.
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
ms.openlocfilehash: e8b735780f4ed3402845d9d401f8e37701b9a1a6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60836919"
---
# <a name="sfctl-mesh-service"></a>sfctl mesh service
Obter detalhes do serviço e listar os serviços de um recurso de aplicativo.

## <a name="commands"></a>Comandos

|Comando|DESCRIÇÃO|
| --- | --- |
| list | Lista todos os recursos de serviço. |
| mostrar | Obtém o recurso de serviço com o nome fornecido. |

## <a name="sfctl-mesh-service-list"></a>sfctl mesh service list
Lista todos os recursos de serviço.

Obtém as informações sobre todos os serviços de um recurso de aplicativo. As informações incluem a descrição e outras propriedades do serviço.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --app-name --application-name [Obrigatório] | O nome do aplicativo. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, table, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-mesh-service-show"></a>sfctl mesh service show
Obtém o recurso de serviço com o nome fornecido.

Obtém as informações sobre o recurso de serviço com o nome fornecido. As informações incluem a descrição e outras propriedades do serviço.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --app-name --application-name [Obrigatório] | O nome do aplicativo. |
| --name -n                     [Obrigatório] | O nome do serviço. |

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