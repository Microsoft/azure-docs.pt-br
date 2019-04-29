---
title: Contêiner do Azure Service Fabric CLI - sfctl | Microsoft Docs
description: Descreve os comandos de contêiner sfctl CLI de malha do serviço.
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
ms.openlocfilehash: a5037c535737946a50d8af6fa60d0815120276d9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60837293"
---
# <a name="sfctl-container"></a>sfctl container
Execute comandos relacionados ao contêiner em um nó de cluster.

## <a name="commands"></a>Comandos

|Comando|DESCRIÇÃO|
| --- | --- |
| invocar api | Invoca a API de contêiner em um contêiner implantado em um nó do Service Fabric para o pacote de código fornecido. |
| logs | Obtém os logs do contêiner para contêiner implantado em um nó de malha do serviço para o pacote de código fornecido. |

## <a name="sfctl-container-invoke-api"></a>contêiner de sfctl invoke-api
Invoca a API de contêiner em um contêiner implantado em um nó do Service Fabric para o pacote de código fornecido.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --id do aplicativo [requerido] | A identidade do aplicativo. <br><br> Normalmente, este é o nome completo do aplicativo sem o esquema de URI "fabric\:". A partir da versão 6.0, nomes hierárquicos são delimitados pelo caractere "\~". Por exemplo, se o nome do aplicativo for "fabric\:/meuaplicativo/aplicativo1", a identidade do aplicativo será "meuaplicativo\~aplicativo1" na versão 6.0 e superiores, e "meuaplicativo/aplicativo1" nas versões anteriores. |
| -código de pacote-id-instância-[requerido] | ID que identifica exclusivamente uma instância de pacote de código implantada em um nó do Service Fabric. <br><br> Pode ser recuperada usando "service code-package-list". |
| -código de-nome do pacote [requerido] | O nome do pacote de código especificado no manifesto de serviço registrado como parte de um tipo de aplicativo em um cluster do Service Fabric. |
| – contêiner-api-uri-path [requerido] | Caminho de URI de API REST do contêiner, use '{ID}' no lugar de nome/id do contêiner. |
| --node-name                [Obrigatório] | O nome do nó. |
| --service-manifest-name    [Obrigatório] | O nome de um manifesto de serviço registrado como parte de um tipo de aplicativo em um cluster do Service Fabric. |
| – corpo da api de contêiner | Corpo da solicitação HTTP para API REST do contêiner. |
| – contêiner-api-content-type | Tipo de conteúdo para a API REST, o padrão é 'application/json' de contêiner. |
| --container-api-http-verb | Verbo HTTP para o contêiner de API REST, o padrão é GET. |
| --timeout -t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, table, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-container-logs"></a>logs de contêiner sfctl
Obtém os logs do contêiner para contêiner implantado em um nó de malha do serviço para o pacote de código fornecido.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --id do aplicativo [requerido] | A identidade do aplicativo. <br><br> Normalmente, este é o nome completo do aplicativo sem o esquema de URI "fabric\:". A partir da versão 6.0, nomes hierárquicos são delimitados pelo caractere "\~". Por exemplo, se o nome do aplicativo for "fabric\:/meuaplicativo/aplicativo1", a identidade do aplicativo será "meuaplicativo\~aplicativo1" na versão 6.0 e superiores, e "meuaplicativo/aplicativo1" nas versões anteriores. |
| -código de pacote-id-instância-[requerido] | ID de instância do pacote de código, que pode ser recuperada por 'serviço código--lista de pacotes'. |
| -código de-nome do pacote [requerido] | O nome do pacote de código especificado no manifesto de serviço registrado como parte de um tipo de aplicativo em um cluster do Service Fabric. |
| --node-name                [Obrigatório] | O nome do nó. |
| --service-manifest-name    [Obrigatório] | O nome de um manifesto de serviço registrado como parte de um tipo de aplicativo em um cluster do Service Fabric. |
| -final | Número de linhas a serem mostradas do final dos logs. O padrão é 100. 'todos' para mostrar os logs de conclusão. |
| --timeout -t | Tempo limite do servidor em segundos.  Padrão\: 60. |

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