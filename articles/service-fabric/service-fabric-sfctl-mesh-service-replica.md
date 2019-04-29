---
title: CLI do Azure Service Fabric – sfctl mesh service-replica | Microsoft Docs
description: Descreve os comandos sfctl mesh service-replica da CLI do Service Fabric.
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
ms.openlocfilehash: 1e0955ef2a52a6313f0449b956229e03f0a5b5e8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61038441"
---
# <a name="sfctl-mesh-service-replica"></a>sfctl mesh service-replica
Obtenha detalhes da réplica e listar as réplicas de um determinado serviço em um recurso de aplicativo.

## <a name="commands"></a>Comandos

|Comando|DESCRIÇÃO|
| --- | --- |
| list | Lista todas as réplicas de um serviço. |
| mostrar | Obtém a réplica fornecida do serviço de um aplicativo. |

## <a name="sfctl-mesh-service-replica-list"></a>sfctl mesh service-replica list
Lista todas as réplicas de um serviço.

Obtém as informações sobre todas as replicas de um serviço. As informações incluem a descrição e outras propriedades da réplica do serviço.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --app-name --application-name [Obrigatório] | O nome do aplicativo. |
| --service-name                [Obrigatório] | O nome do serviço. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, table, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-mesh-service-replica-show"></a>sfctl mesh service-replica show
Obtém a réplica fornecida do serviço de um aplicativo.

Obtém as informações sobre a réplica do serviço com o nome fornecido. As informações incluem a descrição e outras propriedades da réplica do serviço.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --app-name --application-name [Obrigatório] | O nome do aplicativo. |
| --name -n                     [Obrigatório] | O nome da réplica do serviço. |
| --service-name                [Obrigatório] | O nome do serviço. |

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